# UD06 — Laboratorio Autonomo: Troubleshooting, Monitoring e Continuità Operativa

**Candidato/Partecipante:** azureuser  
**Data esecuzione:** 14 Settembre 2026  
**Resource Group:** rg-ud06-compute  
**VM:** vm-ud06-linux  

---

## Attività 1 — Baseline

### Verifica dello stato iniziale
Command:
az vm show --resource-group "$LAB_RG" --name "$LAB_VM" --show-details --query "{PowerState:powerState, PublicIP:publicIps}" --output table
curl -I "http://$LAB_VM_IP"

Output:
PowerState    PublicIP
------------  -------------
VM running    20.228.142.85

HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Mon, 14 Sep 2026 15:00:10 GMT
Content-Type: text/html
Content-Length: 612
Connection: keep-alive

### Dettagli della Baseline:
* Stato VM: Running (Attiva e in esecuzione)
* Nginx: Active / Running (in ascolto sulla porta 80)
* Esito HTTP: 200 OK
* Regola NSG abilitante: Allow-HTTP-MyIP (Priority 310, TCP/80 da My IP)

---

## Attività 2 — Guasto controllato

Creazione della regola di blocco con priorità maggiore (numero di priorità più basso):

Command:
az network nsg rule create \
  --resource-group "$LAB_RG" \
  --nsg-name "vm-ud06-linux-nsg" \
  --name "Deny-HTTP-Auto" \
  --priority 200 \
  --direction Inbound \
  --access Deny \
  --protocol Tcp \
  --source-address-prefixes "109.118.x.x/32" \
  --destination-port-ranges 80 \
  --output table

Output:
Access    Direction    Name            Priority    Protocol    SourceAddressPrefix    DestinationPortRange
--------  -----------  --------------  ----------  ----------  ---------------------  --------------------
Deny      Inbound      Deny-HTTP-Auto  200         Tcp         109.118.x.x/32         80

---

## Attività 3 — Diagnosi

### Esecuzione test di diagnosi
Command (da client locale WSL):
curl -I --connect-timeout 5 "http://$LAB_VM_IP"

Output:
curl: (28) Failed to connect to 20.228.142.85 port 80 after 5001 ms: Connection timed out

Command (da SSH interno alla VM):
ssh -i ~/.ssh/ud06_azure azureuser@"$LAB_VM_IP" "systemctl status nginx --no-pager && curl -I http://localhost"

Output:
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2026-09-14 13:42:10 UTC; 1h 20m ago

HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)

### Report Diagnostico Structured:

sintomo
→ Connection timed out eseguendo 'curl' verso l'IP pubblico della VM sulla porta 80 dal client locale.

→ ipotesi
→ Blocco a livello di filtro di rete (NSG) oppure arresto improvviso del servizio Nginx all'interno del sistema operativo.

→ controllo
→ 1. Collegamento tramite SSH alla VM ed esecuzione di 'curl http://localhost' e 'systemctl status nginx' (Esito: OK, il servizio risponde localmente).
   2. Esecuzione di IP Flow Verify da Network Watcher per la porta 80.

→ causa
→ Presenza della regola NSG 'Deny-HTTP-Auto' con priorità 200 che scarta il traffico HTTP in ingresso dal client prima che la regola 'Allow-HTTP-MyIP' (priorità 310) possa essere valutata.

→ correzione minima
→ RIMOZIONE o DISABILITAZIONE della sola regola NSG 'Deny-HTTP-Auto' (Priority 200).

---

## Attività 4 — Ripristino

Command:
az network nsg rule delete \
  --resource-group "$LAB_RG" \
  --nsg-name "vm-ud06-linux-nsg" \
  --name "Deny-HTTP-Auto"

curl -I "http://$LAB_VM_IP"

Output:
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Mon, 14 Sep 2026 15:15:22 GMT
Content-Type: text/html
Content-Length: 612
Connection: keep-alive

Verifica: Il servizio web è immediatamente tornato raggiungibile da remoto.

---

## Attività 5 — Monitoring

Configurazione e analisi della metrica estratta da Azure Monitor:

metrica: Percentage CPU
intervallo: Ultimi 60 minuti (Time grain: 1 minuto)
aggregazione: Average (Media)

ciò che puoi dedurre:
- Il consumo di risorse CPU si mantiene costantemente al di sotto del 2%, con un unico picco isolato dell'8.2% in corrispondenza delle attività di provisioning iniziale (apt install).
- Il carico di lavoro attuale è estremamente ridotto e la dimensione dell'istanza (Standard_B1s) è sovradimensionata rispetto all'uso reale in idle.

ciò che NON puoi dedurre:
- Il quantitativo di memoria RAM occupata dal sistema operativo o dai processi Nginx (la RAM richiede l'agente Azure Monitor/Log Analytics).
- Il numero esatto di richieste HTTP fallite (codici 4xx/5xx) gestite dall'applicazione web.
- La saturazione dello spazio disco su filesystem.

---

## Attività 6 — Progetta autoscaling VMSS

### Parametri di configurazione:

min: 1
default: 1
max: 4
metrica: Percentage CPU
condizione: Media > 70% per una durata continuativa di 5 minuti
azione: Scale Out (Aumenta il conteggio di 1 istanza)

### Motivazione del limite massimo (max = 4):
Impostare max = 4 è un controllo di sicurezza fondamentale per:
1. Budget & Cost Control: Previene costi imprevisti evitando che un numero indefinito di macchine virtuali venga istanziato in automatico.
2. Contenimento anomalie software: Evita che loop infiniti o bug applicativi che saturano la CPU al 100% causino lo scaling continuo dell'infrastruttura fino all'esaurimento del budget.
3. Quota Limits: Impedisce il raggiungimento improvviso dei limiti di quota vCPU assegnati alla Subscription Azure, garantendo stabilità agli altri servizi tenant.

---

## Attività 7 — App Service scaling

### A — Serve una singola istanza più potente.
Scelta: Scale up  
Motivazione: Lo Scale Up modifica la dimensione (SKU/Tier) dell'App Service Plan aumentando vCPU e RAM della singola istanza.

### B — Il numero di istanze deve aumentare quando CPU supera una soglia definita.
Scelta: Azure Monitor Autoscale  
Motivazione: Consente di definire regole metriche personalizzate basate su soglie di risorse e finestre temporali specifiche.

### C — La piattaforma deve reagire automaticamente al traffico HTTP senza definire regole metriche esplicite, su un tier compatibile.
Scelta: Automatic Scaling  
Motivazione: È la funzionalità nativa PaaS di Azure che scala le istanze in risposta automatica alle fluttuazioni del traffico HTTP, lasciando la gestione dell'algoritmo alla piattaforma.

### D — Per una demo voglio semplicemente passare da 1 a 2 istanze manualmente.
Scelta: Scale out manuale  
Motivazione: Permette di impostare direttamente da portale o CLI il numero esatto di istanze desiderate senza attendere il trigger delle metriche.

---

## Attività 8 — Backup policy

Progettazione della politica di backup (VM aziendale):

frequenza: Giornaliera (Daily)
orario: 01:00 UTC (finestra notturna fuori dagli orari operativi standard)
retention: 30 giorni per i punti di ripristino giornalieri; 12 mesi per gli snapshot di fine mese
motivazione: Questa configurazione bilancia la conformità aziendale per il Disaster Recovery a lungo termine con la necessità di minimizzare l'impatto sulle prestazioni I/O del disco durante le ore lavorative.

---

## Attività 9 — HA / Backup / DR

### Scenario A — Guasto di una singola istanza, servizio deve continuare.
Classificazione: HA (High Availability)  
Motivazione: La Ridondanza e la High Availability (es. Availabilty Zones, Load Balancer, VMSS) garantiscono la tolleranza ai guasti fisici dei singoli nodi senza interruzione di servizio per l'utente finale.

### Scenario B — Cancellazione accidentale di dati: serve recuperare uno stato precedente.
Classificazione: Backup  
Motivazione: Il Backup gestisce la protezione dei dati point-in-time, consentendo il ripristino chirurgico a una data specifica a seguito di corruzione o cancellazione di file.

### Scenario C — Regione primaria indisponibile: il workload deve essere riattivato altrove.
Classificazione: DR (Disaster Recovery)  
Motivazione: Il Disaster Recovery permette di far fronte alla perdita totale di un'intera regione geografica Azure replicando l'infrastruttura e i dati su un'area secondaria (es. tramite Azure Site Recovery).

---

## Attività 10 — RPO / RTO

Analisi del requisito:
* perdita massima dati: 15 minuti
* servizio nuovamente operativo entro: 60 minuti

Risultato:
RPO: 15 minuti (Recovery Point Objective - massima perdita di dati tollerabile)
RTO: 60 minuti (Recovery Time Objective - massimo tempo di disservizio tollerabile per il ripristino)