# Risposte ai Quesiti - UD06: Concetti di Architettura e Servizi Azure

## 1. Risorse e Componenti Principali di una VM Azure
Una Virtual Machine (VM) in Azure è una risorsa composita costituita da diversi componenti indipendenti:

| Componente | Ruolo Principale |
| :--- | :--- |
| **Compute (VM Core)** | Rappresenta la capacità di calcolo (vCPU e RAM) definita dalla dimensione (*Size* o *SKU*). |
| **OS Disk & Data Disks** | Dischi gestiti (*Managed Disks*) basati su archiviazione SSD/HDD. L'OS Disk ospita il sistema operativo, mentre i Data Disks salvano dati applicativi persistenti. |
| **Network Interface (NIC)** | La scheda di rete virtuale che collega la VM a una Virtual Network (VNet) e gestisce l'indirizzamento IP privato. |
| **Virtual Network (VNet) & Subnet** | L'infrastruttura di rete privata in cui risiede la VM per comunicare in modo sicuro con altre risorse. |
| **Network Security Group (NSG)** | Un firewall di rete a livello di subnet o NIC che definisce regole di traffico (*Inbound/Outbound*). |
| **Public IP (Opzionale)** | Indirizzo IP pubblico associato alla NIC per rendere la VM direttamente accessibile da Internet. |

---

## 2. Perché un Public IP non garantisce la Raggiungibilità da Internet
Avere un IP pubblico associato a una VM **non è sufficiente** per garantire che un servizio sia raggiungibile, poiché il traffico deve superare diversi livelli di controllo e configurazione:

1. **Regole del Network Security Group (NSG):** L'NSG potrebbe bloccare il traffico in ingresso (*Inbound*) sulla porta specifica usata dal servizio (es. porta 80/443 per HTTP/HTTPS o 3389/22 per RDP/SSH).
2. **Firewall del Sistema Operativo:** Il firewall interno alla VM (Windows Firewall o `iptables`/`ufw` su Linux) potrebbe scartare i pacchetti in arrivo.
3. **Stato del Servizio Applicativo:** Il servizio o web server (es. IIS, Nginx, Apache) potrebbe non essere in esecuzione o non essere in ascolto (*listening*) sull'interfaccia di rete corretta.
4. **Routing di Rete e User Defined Routes (UDR):** Eventuali tabelle di instradamento personalizzate o Azure Firewall intermedio potrebbero reindirizzare o bloccare il traffico.

---

## 3. Availability Zone vs Availability Set vs VM Scale Set

| Soluzione | Problema che affronta | Architettura |
| :--- | :--- | :--- |
| **Availability Set** | Protegge da guasti hardware locali nello stesso data center (es. guasto al rack o allo switch). | Distribuisce le VM su diversi **Fault Domain** (rack fisici separati) e **Update Domain** (gruppi di aggiornamento). |
| **Availability Zone** | Protegge da guasti all'intero data center all'interno di una stessa regione Azure. | Distribuisce le VM in data center **fisicamente separati** all'interno della stessa regione, con alimentazione e rete indipendenti. |
| **VM Scale Set (VMSS)** | Gestisce l'alta scalabilità e il bilanciamento del carico di applicazioni uniformi. | Crea e gestisce automaticamente un gruppo di VM identiche con supporto per l'autoscale basato sulla domanda. |

---

## 4. Differenza tra Scale Up e Scale Out

* **Scale Up (Scalabilità Verticale):**
  * **Concetto:** Aumentare le risorse della singola macchina (es. passare da una VM con 2 vCPU e 8GB di RAM a una con 8 vCPU e 32GB di RAM).
  * **Impatto:** Spesso richiede il riavvio della macchina (*downtime*) e ha limiti fisici legati alla dimensione massima supportata dall'hardware.
* **Scale Out (Scalabilità Orizzontale):**
  * **Concetto:** Aumentare il numero di istanze/VM che lavorano in parallelo dietro un bilanciatore di carico (es. passare da 2 a 10 istanze identiche).
  * **Impatto:** Garantisce **Zero Downtime**, alta disponibilità e scalabilità praticamente illimitata.

---

## 5. Azure Monitor Autoscale nei VM Scale Set (Soglie e Limiti)
Azure Monitor Autoscale modifica automaticamente il numero di istanze in un VMSS aggiungendo (*scale-out*) o rimuovendo (*scale-in*) VM in base a metriche prestazionali (es. utilizzo medio della CPU > 80%).

### Importanza delle Soglie e dei Limiti:
* **Soglia di Attivazione (*Trigger Thresholds*):** Evita reazioni impulsive al traffico temporaneo, richiedendo che una metrica superi un valore per un certo periodo (es. CPU > 75% per 10 minuti).
* **Limite Minimo (*Minimum Count*):** Garantisce che ci sia sempre un numero minimo di risorse pronte a servire il traffico base ed evita di spegnere tutte le istanze.
* **Limite Massimo (*Maximum Count*):** Protegge il budget aziendale evitando una crescita incontrollata delle risorse e dei costi in caso di picchi o attacchi DDoS.

---

## 6. Differenza tra App Service Plan e Web App

* **App Service Plan:**
  * Rappresenta la **risorsa computazionale sottostante** (l'infrastruttura di server/VM gestiti da Microsoft).
  * Definisce la regione, la dimensione (vCPU/RAM), il tier di prezzo (Free, Shared, Basic, Standard, Premium) e la capacità di scalabilità.
* **Web App:**
  * È la **singola applicazione web / API** distribuita sopra l'App Service Plan.
  * Contiene il codice applicativo, le configurazioni dell'ambiente di runtime, le variabili di ambiente e i certificati SSL. Più Web App possono condividere lo stesso App Service Plan.

---

## 7. Azure Monitor Autoscale vs App Service Automatic Scaling

* **Azure Monitor Autoscale (Rule-Based / Reactive):**
  * Basato su **regole esplicite definite dall'utente** (es. *"Se la CPU media > 70%, aggiungi 1 istanza"*).
  * Richiede la configurazione manuale di soglie, intervalli di valutazione e tempi di raffreddamento (*cooldown*).
* **App Service Automatic Scaling (Native / Predictive):**
  * Gestito in modo **completamente automatico dalla piattaforma Azure**.
  * Azure analizza autonomamente le metriche di traffico e carico dell'applicazione e scala le istanze in modo dinamico ed elastico, senza richiedere la definizione manuale di regole complesse per ogni metrica.

---

## 8. Metrics vs Logs in Log Analytics

* **Metrics (Metriche):**
  * **Cosa sono:** Dati numerici leggeri, strutturati e memorizzati a intervalli regolari (time-series).
  * **Informazione fornita:** Forniscono lo **stato di salute e prestazioni in tempo reale** (es. % CPU, utilizzo memoria, throughput di rete). Ideali per dashboard veloci e allarmi tempestivi.
* **Logs / Log Analytics:**
  * **Cosa sono:** Record dettagliati di eventi, traccia e log applicativi memorizzati in una struttura a tabelle ed interrogabili tramite linguaggio KQL (*Kusto Query Language*).
  * **Informazione fornita:** Forniscono **contesto e dettagli diagnostici approfonditi** (es. log di errore di un'applicazione, tentativi di accesso falliti, modifiche di configurazione).

---

## 9. Recovery Services Vault, Backup Policy e Recovery Point

* **Recovery Services Vault:** L'entità di archiviazione in Azure che conserva in modo sicuro dati di backup, snapshot e punti di ripristino per VM, SQL Database, ecc.
* **Backup Policy:** La regola di configurazione che pianifica **quando** eseguire il backup (es. ogni giorno alle 02:00) e **per quanto tempo** conservarlo (*Retention Rule*, es. 30 giorni, 12 mesi).
* **Recovery Point (Point-in-Time):** La copia dei dati salvata in uno specifico momento storico all'interno del Vault, utilizzabile per effettuare un ripristino.

### Come sono collegati:
La **Backup Policy** viene associata alla VM e definisce la frequenza di creazione dei **Recovery Point**, i quali vengono salvati e protetti all'interno del **Recovery Services Vault**.

---

## 10. High Availability vs Backup vs Disaster Recovery

| Concetto | Definizione | Esempio di Esigenza |
| :--- | :--- | :--- |
| **High Availability (HA)** | Mantenere il servizio attivo e funzionante a fronte di guasti hardware o di rete locali. | Un sito e-commerce deve rimanere attivo al 99.99% anche se un rack di server nel data center subisce un guasto elettrico. |
| **Backup** | Salvare copie dei dati nel tempo per recuperare informazioni perse, corrotte o cancellate. | Un dipendente cancella per errore una cartella critica o un database viene danneggiato da un ransomware; è necessario ripristinare la versione di ieri. |
| **Disaster Recovery (DR)** | Ripristinare l'intera infrastruttura in una regione secondaria a fronte di un evento catastrofico. | Una regione intera di Azure subisce un'interruzione di servizio prolungata (es. alluvione o disastro naturale); le operazioni devono ripartire da un'altra regione. |

---

## 11. RPO (Recovery Point Objective) vs RTO (Recovery Time Objective)

* **RPO (Recovery Point Objective):**
  * **Cosa indica:** La **massima quantità di dati che un'azienda può permettersi di perdere**, misurata in tempo.
  * **Focus:** Frequenza dei backup. (Es. RPO = 1 ora significa che nel peggiore dei casi si perdono i dati dell'ultima ora).
* **RTO (Recovery Time Objective):**
  * **Cosa indica:** Il **tempo massimo ammissibile per ripristinare il servizio** dopo un guasto.
  * **Focus:** Velocità di ripristino. (Es. RTO = 4 ore significa che il servizio deve essere di nuovo operativo entro 4 ore dal disastro).

### Perché sono due requisiti diversi:
RPO riguarda la **tolleranza alla perdita di dati**, mentre RTO riguarda la **tolleranza al tempo di inattività (*downtime*)**. Un sistema può avere un RPO di poche secondi (nessuna perdita di dati grazie a repliche sincrone), ma un RTO di diverse ore (il processo di failover o riavvio richiede tempo).

---

## 12. Perché una VM Deallocated Continua a Generare Costi
Quando una VM Azure si trova nello stato **Stopped (Deallocated)**, le risorse di calcolo (vCPU e RAM) vengono rilasciate e **non vengono addebitate**. 

Tuttavia, la VM continua a generare costi perché **le risorse correlate continuano ad essere riservate e mantenute attive** in Azure:

1. **Dischi Gestiti (OS Disk e Data Disks):** Lo spazio di archiviazione SSD/HDD è ancora occupato e riservato nel data center.
2. **Indirizzi IP Pubblici Statici:** Se la VM ha un IP pubblico statico assegnato, questo viene addebitato finché rimane allocato.
3. **Altre risorse collegate:** Eventuali altre risorse ancillari come snapshot, chiavi in Key Vault o servizi di monitoraggio dedicati.