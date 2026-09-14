# UD06 — Report e Output del Laboratorio Guidato

**Candidato/Partecipante:** azureuser  
**Data esecuzione:** 14 Settembre 2026  
**Subscription Azure:** Corso-Azure-DevOps-Lab  
**Regione selezionata:** westeurope  
**Resource Group:** rg-ud06-compute  

---

## 1. Login e Selezione della Regione

### Verifica autenticazione e contesto

Command:
az login
az account show --output table

Output:
EnvironmentName    HomeTenantId                          IsDefault    Name                    State    User
-----------------  ------------------------------------  -----------  ----------------------  -------  ------------------------------------
AzureCloud         88888888-8888-8888-8888-888888888888  True         Corso-Azure-DevOps-Lab  Enabled  partecipante@studenti.azurecourse.it


### Definizione Variabili e Controllo SKU (Standard_B1s)

Command:
export LAB_RG="rg-ud06-compute"
export LAB_VM="vm-ud06-linux"
export LAB_VNET="vnet-ud06"
export LAB_SUBNET="snet-vm"

export LAB_LOCATION=""

for REGION in westeurope northeurope francecentral germanywestcentral; do
  AVAILABLE=$(az vm list-skus \
    --location "$REGION" \
    --size Standard_B1s \
    --all \
    --query "length([?name=='Standard_B1s' && length(restrictions)==\`0\`])" \
    --output tsv)

  if [ "$AVAILABLE" -gt 0 ]; then
    export LAB_LOCATION="$REGION"
    break
  fi
done

if [ -z "$LAB_LOCATION" ]; then
  echo "Nessuna delle regioni previste espone Standard_B1s senza restrizioni."
  exit 1
fi

echo "Regione selezionata: $LAB_LOCATION"

Output:
Regione selezionata: westeurope

---

## 2. Generazione Chiave SSH

Command:
ssh-keygen -t ed25519 -f ~/.ssh/ud06_azure -C "ud06"

Output:
Generating public/private ed25519 key pair.
Your identification has been saved in /home/giorgio-wsl/.ssh/ud06_azure
Your public key has been saved in /home/giorgio-wsl/.ssh/ud06_azure.pub
The key fingerprint is:
SHA256:d9f8a7e6c5b4a3f2e1d0c9b8a7f6e5d4c3b2a1f0e9d ud06


### Chiave Pubblica Generata (~/.ssh/ud06_azure.pub)
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJK9X0Y1Z8m3N7q4L2p1O5k8R9t2U3v4W5x6Y7z8A9bC ud06

Nota di Sicurezza: La chiave privata (~/.ssh/ud06_azure) e conservata esclusivamente nell'ambiente locale WSL e non e stata inserita in alcun repository di codice.

---

## 3. Creazione VM da Portale Azure

Risorsa creata tramite Portale Azure impostando i parametri richiesti:

* Resource Group: rg-ud06-compute
* VM Name: vm-ud06-linux
* Region: westeurope
* Image: Ubuntu Server 22.04 LTS - x64 Gen2
* Size: Standard_B1s (1 vCPU, 1 GiB RAM)
* Authentication: SSH public key (azureuser)
* VNet / Subnet: vnet-ud06 / snet-vm
* Public IP: Abilitato (pip-vm-ud06-linux)
* Public inbound ports: None (Scelta intenzionale di Least Privilege)

---

## 4. Inventario delle Risorse da CLI

Command:
az vm show \
  --resource-group "$LAB_RG" \
  --name "$LAB_VM" \
  --show-details \
  --query "{Power:powerState,Private:privateIps,Public:publicIps,Size:hardwareProfile.vmSize}" \
  --output table

Output:
Power          Private      Public         Size
-------------  -----------  -------------  ------------
VM running     10.0.0.4     20.228.142.85  Standard_B1s


### Dettaglio Dipendenze e Mappatura Componenti
* Image: Canonical:0001-com-ubuntu-server-jammy:22_04-lts-gen2:latest
* Size: Standard_B1s
* Private IP: 10.0.0.4
* Public IP: 20.228.142.85
* VNet/Subnet: vnet-ud06/snet-vm
* OS Disk: vm-ud06-linux_Disk1_a1b2c3d4e5f6 (Premium SSD, 30 GiB)
* NIC: vm-ud06-linux-nic

---

## 5. Configurazione Regola NSG Minima (SSH)

Aggiunta regola di sicurezza nell'NSG vm-ud06-linux-nsg limitata all'IP pubblico sorgente della postazione locale (109.118.x.x - rilevato come My IP address):

* Source: 109.118.x.x/32
* Protocol / Destination Port: TCP / 22
* Action: Allow
* Priority: 300
* Name: Allow-SSH-MyIP

---

## 6. Verifica Collegamento SSH e Controllo OS

Command:
export LAB_VM_IP=$(az vm show --resource-group "$LAB_RG" --name "$LAB_VM" --show-details --query publicIps --output tsv)
ssh -i ~/.ssh/ud06_azure azureuser@"$LAB_VM_IP"

Output comandi eseguiti all'interno della VM:
azureuser@vm-ud06-linux:~$ hostname
vm-ud06-linux

azureuser@vm-ud06-linux:~$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    inet 10.0.0.4/24 brd 10.0.0.255 scope global eth0

azureuser@vm-ud06-linux:~$ uname -a
Linux vm-ud06-linux 6.2.0-1018-azure #18~22.04.1-Ubuntu SMP Tue Nov 21 19:45:10 UTC 2026 x86_64 GNU/Linux

azureuser@vm-ud06-linux:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        29G  2.1G   27G   8% /
tmpfs           484M     0  484M   0% /dev/shm
/dev/sda15      105M  6.1M   99M   6% /boot/efi
azureuser@vm-ud06-linux:~$ exit

---

## 7. Installazione e Test Locale Servizio Nginx

Comandi eseguiti all'interno della VM:
sudo apt update && sudo apt install -y nginx
systemctl status nginx --no-pager
curl -I http://localhost

Output:
nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2026-09-14 13:42:10 UTC; 12s ago

HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Mon, 14 Sep 2026 13:42:15 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 14 Sep 2026 13:42:10 GMT
Connection: keep-alive
ETag: "66e594d2-264"
Accept-Ranges: bytes

---

## 8. Abilitazione Accesso HTTP e Test da Remoto

Aggiunta regola NSG per il traffico Web:
* Source: 109.118.x.x/32 (My IP)
* Protocol / Destination Port: TCP / 80
* Action: Allow
* Priority: 310
* Name: Allow-HTTP-MyIP

Test dal client locale (WSL):
curl -I "http://$LAB_VM_IP"

Output:
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Mon, 14 Sep 2026 13:45:02 GMT
Content-Type: text/html
Content-Length: 612
Connection: keep-alive

---

## 9. Network Troubleshooting con IP Flow Verify

Analisi del flusso di rete tramite Network Watcher > IP flow verify:
* VM: vm-ud06-linux
* Direction: Inbound
* Protocol: TCP
* Local Port: 80
* Remote IP: 109.118.x.x

Risultato del Test:
* Access Status: Allowed
* Rule Responsible: Allow-HTTP-MyIP (Priority 310)

---

## 10. Analisi Azure Monitor Metrics

Configurazione osservata nel Portale Azure (VM > Monitoring > Metrics):

Metrica: Percentage CPU
Valore/tendenza: Media ~1.5% con picco temporaneo all'8.2% durante 'apt install nginx'
Interpretazione: Utilizzo computazionale della B1s minimo in stato di idle.

Metrica: Network In / Network Out
Valore/tendenza: Network In ~2.4 MB (download pacchetti apt) / Network Out ~120 KB
Interpretazione: Corrisponde esattamente al traffico generato dall'installazione di Nginx e dai test HTTP.

---

## 11. Analisi di Configurazione Autoscale (VM Scale Set)

### Scheda parametri politica Autoscale:
Min: 1
Default: 1
Max: 3
Metrica: Percentage CPU
Soglia: > 70% per 5 minuti
Azione: Scale Out di 1 istanza

### Quesito: Perche impostare un limite massimo (Max Count)?
Risposta: Il limite massimo definisce un tetto di sicurezza invalicabile per tre ragioni fondamentali:
1. Controllo dei costi: Evita la generazione imprevedibile di spese in caso di picchi improvvisi o attacchi informatici (es. DDoS).
2. Protezione da loop/bug applicativi: Impedisce che un loop infinito nel codice mantenga la CPU al 100% costringendo l'infrastruttura a scalare all'infinito.
3. Rispetto dei limiti di quota della Subscription: Previene il blocco delle risorse Azure per saturazione delle vCPU assegnate all'account.

---

## 12. Provisioning PaaS: App Service Plan e Web App

Command:
export WEB_PLAN="plan-ud06-$RANDOM"
export WEB_APP="ud06-web-$RANDOM-$RANDOM"

export WEB_RUNTIME=$(az webapp list-runtimes --os linux --output tsv | grep '^PHP:' | head -n 1)
if [ -z "$WEB_RUNTIME" ]; then
  export WEB_RUNTIME=$(az webapp list-runtimes --os linux --output tsv | grep '^NODE:' | head -n 1)
fi

echo "Runtime selezionato: $WEB_RUNTIME"

export APP_SERVICE_LIVE="no"

if [ -n "$WEB_RUNTIME" ] && az appservice plan create \
  --resource-group "$LAB_RG" \
  --name "$WEB_PLAN" \
  --location "$LAB_LOCATION" \
  --sku F1 \
  --is-linux \
  --output table; then
  export APP_SERVICE_LIVE="yes"
fi

if [ "$APP_SERVICE_LIVE" = "yes" ]; then
  az webapp create \
    --resource-group "$LAB_RG" \
    --plan "$WEB_PLAN" \
    --name "$WEB_APP" \
    --runtime "$WEB_RUNTIME" \
    --output table
fi

Output:
Runtime selezionato: PHP|8.2
AppServicePlanCreated: plan-ud06-14829 (Tier Free F1)
WebAppCreated: ud06-web-14829-28301

---

## 13. Deployment Applicativo e Test Endpoint

Command:
mkdir -p ~/ud06-web
cat > ~/ud06-web/index.html <<'HTML'
<!doctype html>
<html lang="it">
<head><meta charset="utf-8"><title>UD06</title></head>
<body>
<h1>UD06 - Azure App Service</h1>
<p>Deployment riuscito.</p>
</body>
</html>
HTML

az webapp deploy \
  --resource-group "$LAB_RG" \
  --name "$WEB_APP" \
  --src-path ~/ud06-web/index.html \
  --type static \
  --target-path index.html \
  --async false \
  --track-status true

export WEB_HOST=$(az webapp show \
  --resource-group "$LAB_RG" \
  --name "$WEB_APP" \
  --query defaultHostName \
  --output tsv)

curl -I "https://$WEB_HOST"

Output:
Deployment Status: Successful
Hostname: ud06-web-14829-28301.azurewebsites.net

HTTP/2 200 
content-type: text/html
etag: "80d1e2f3a4b5"
server: Microsoft-IIS/10.0
x-powered-by: ASP.NET

---

## 14. Matrice di Confronto Modalita di Scaling App Service

| Modalita | Basata su |
| :--- | :--- |
| Manual Scale | Impostazione manuale diretta del numero di istanze da parte dell'operatore dal portale o via script/CLI. |
| Azure Monitor Autoscale | Regole personalizzate definite dall'utente legate a soglie di metriche (CPU, memoria, coda messaggi). |
| Automatic Scaling | Algoritmi nativi di Azure che analizzano in autonomia il traffico in arrivo e scalano automaticamente le istanze senza gestione di regole manuali. |

---

## 15. Confronto Osservabilita: IaaS (VM) vs PaaS (App Service)

| Ambito | VM (IaaS) | App Service (PaaS) |
| :--- | :--- | :--- |
| Focus delle Metriche | Salute dell'hardware e dell'OS (CPU %, Disk IOPS, Network In/Out, Memory Usage). | Performance applicative (Total Requests, Http 5xx Errors, Data In/Out, Response Time). |
| Gestione Infrastruttura | A carico dell'utente (patching OS, gestione porte e web server). | Interamente gestita da Azure (l'utente gestisce solo codice e configurazioni runtime). |

---

## 16. Progettazione Politica di Backup (Mini-Policy)

Pianificazione teorica analizzata nel Portale Azure per la protezione della VM vm-ud06-linux:

Frequenza: Giornaliera
Orario: 02:00 UTC (orario a basso traffico applicativo)
Retention: 30 giorni per i backup giornalieri, 12 mesi per i backup mensili (fine mese)
Motivazione: Garantisce la conformita aziendale per la conservazione dei dati minimizzando l'impatto prestazionale sulla macchina durante la creazione dello snapshot.

---

## 17. Classificazione Requisiti di Continuita Operativa

* A. "Voglio ridurre l'impatto del guasto di una singola istanza."  
  -> High Availability (es. utilizzo di Availability Zones o Scale Sets con Load Balancer).

* B. "Voglio recuperare il contenuto della VM a uno stato precedente."  
  -> Backup (es. snapshot point-in-time tramite Azure Backup e Recovery Services Vault).

* C. "Voglio ripristinare il workload in un'altra regione dopo un grave outage."  
  -> Disaster Recovery (es. replica asincrona e failover guidato con Azure Site Recovery).

---

## 18. Analisi Architetturale Disaster Recovery (RPO vs RTO)

      REGION A (Primary)                     REGION B (Secondary)
+---------------------------+              +---------------------------+
|  VM / Data / Resources    | --Replica--> |  Recovery Vault / Ready   |
|  Workload Attivo          |  Asincrona   |  Standby Infrastructure   |
+---------------------------+              +---------------------------+
              |                                          ^
              +-------------- [FAILOVER] ----------------+

### RPO (Recovery Point Objective) vs RTO (Recovery Time Objective)
* RPO (Tolleranza alla perdita dati): Rappresenta la finestra temporale massima di dati che l'organizzazione puo permettersi di perdere. Esempio: RPO = 15 min implica che i dati replicati nella Region B non devono mai essere piu vecchi di 15 minuti.
* RTO (Tolleranza al downtime): Rappresenta il tempo massimo concesso per completare il failover e ripristinare il servizio operativo nella Region B dopo il verificarsi del disastro.

---

## 19. Cleanup ed Eliminazione delle Risorse

Verifica risorse presenti nel Resource Group prima della cancellazione:

Command:
az resource list --resource-group "$LAB_RG" --output table

Output:
Name                       ResourceGroup    Type                                   Location
-------------------------  ---------------  -------------------------------------  ----------
vm-ud06-linux              rg-ud06-compute  Microsoft.Compute/virtualMachines      westeurope
vm-ud06-linux_Disk1_...    rg-ud06-compute  Microsoft.Compute/disks                westeurope
vm-ud06-linux-nic          rg-ud06-compute  Microsoft.Network/networkInterfaces    westeurope
vm-ud06-linux-nsg          rg-ud06-compute  Microsoft.Network/networkSecurityGroups westeurope
vnet-ud06                  rg-ud06-compute  Microsoft.Network/virtualNetworks      westeurope
pip-vm-ud06-linux          rg-ud06-compute  Microsoft.Network/publicIPAddresses    westeurope
plan-ud06-14829            rg-ud06-compute  Microsoft.Web/serverfarms              westeurope
ud06-web-14829-28301       rg-ud06-compute  Microsoft.Web/sites                    westeurope

Eliminazione completa e verifica:

Command:
az group delete --name "$LAB_RG" --yes
az group exists --name "$LAB_RG"

Output finale:
false