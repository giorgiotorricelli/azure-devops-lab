Ecco il codice Markdown completo contenente tutte le risposte alle domande di controllo dell'Unità Didattica 07, pronto per essere inserito nella tua repository GitHub.

```markdown
# Risposte alle Domande di Controllo — UD07

Questo documento contiene le risposte concettuali e tecniche alle domande di verifica per l'Unità Didattica 07: *Amministrare Azure in modo ripetibile, osservare ciò che accade e diagnosticare i problemi*.

---

### 1. Perché `--query` è preferibile a cercare manualmente una stringa nell'output JSON?
Il parametro `--query` utilizza il linguaggio **JMESPath** per filtrare e interrogare la **struttura ad oggetti** nativa del JSON restituito da Azure CLI. 

A differenza di una ricerca testuale generica (come `grep` o una ricerca visiva), `--query`:
- Lavora sui campi e sulle relazioni gerarchiche specifiche dell'oggetto, evitando falsi positivi (ad esempio, evita di catturare la stessa parola chiave presente in una descrizione o in un tag non rilevante).
- È indipendente dalla formattazione visiva o dall'ordinamento delle chiavi nel JSON.
- Consente l'automazione affidabile e deterministica negli script, estraendo esattamente il nodo o la proprietà richiesta.

---

### 2. Qual è la differenza tra `table` e `tsv` in Azure CLI?
I due formati di output rispondono a esigenze operative differenti:

- **`table` (Human-Readable):** Formatta i dati in una tabella concisa con intestazioni. È ideata per la lettura immediata da parte dell'operatore umano durante le sessioni interattive.
- **`tsv` (Tab-Separated Values):** Restituisce unicamente i valori grezzi senza intestazioni, virgolette, parentesi o formattazione JSON. È il formato ideale da assegnare a variabili all'interno di script (es. `RG_ID=$(az group show ... --output tsv)`) o da passare tramite pipe ad altri comandi.

---

### 3. Che cosa significa lavorare con oggetti in PowerShell?
PowerShell non gestisce il flusso di dati come semplice testo non strutturato (stringhe), ma come **istanze di classi/oggetti .NET**.

Significa che quando si esegue un cmdlet come `Get-AzResourceGroup`, il risultato salvato in una variabile è un oggetto dotato di:
- **Proprietà accessibili direttamente** (es. `$rg.Location`, `$rg.ResourceGroupName`).
- **Tipi di dati nativi** (stringhe, date, booleani, liste) senza bisogno di fare il parsing del testo.
- **Pipeline basata su oggetti**, che permette di passare l'intero oggetto strutturato direttamente al cmdlet successivo (es. `$rg | Remove-AzResourceGroup`).

---

### 4. Che cosa significa che una procedura amministrativa è idempotente?
Un'operazione o uno script è **idempotente** se può essere eseguito più volte di seguito ottenendo sempre lo stesso risultato finale, senza produrre errori, duplicazioni o effetti collaterali indesiderati dopo la prima esecuzione.

In ambito DevOps e amministrazione Azure:
- Una procedura non idempotente fallisce alla seconda esecuzione (es. `az group create` senza verificare se la risorsa esiste già potrebbe causare comportamenti imprevisti o errori).
- Una procedura idempotente controlla prima lo stato corrente della risorsa (`az group exists`), creando la risorsa se manca, oppure aggiornandola/riutilizzandola se è già presente.

---

### 5. Distingui Activity Log, Metrics e Logs.
Azure Monitor suddivide la telemetria in tre macro-categorie distinte:

1. **Activity Log (Control Plane):** Registra *chi* ha fatto *cosa*, *quando* e con quale *esito* sulle risorse dell'infrastruttura Azure (es. creazione VM, modifica tag, eliminazione RG).
2. **Metrics (Data/Performance Plane - Numeri nel tempo):** Serie temporali di dati numerici legati alle prestazioni e allo stato di salute delle risorse (es. `% CPU`, `Network In`, `Transactions`). Sono ideali per gli alert in tempo reale.
3. **Logs (Data/Operational Plane - Record strutturati):** Record dettagliati con timestamp, categorie ed eventi specifici (es. log applicativi, log di sistema operativo, log di diagnostica). Richiedono un motore di query (come Log Analytics) per essere analizzati.

---

### 6. A che cosa serve un Log Analytics workspace?
Un **Log Analytics workspace** è un ambiente di aggregazione e archiviazione centrale per i dati di log provenienti da differenti sorgenti Azure (e non). 

Fornisce:
- Un archivio strutturato per raccogliere log da più risorse, subscription e regioni.
- Il motore di ricerca ed analisi basato sul linguaggio **KQL (Kusto Query Language)**.
- La base di dati necessaria per creare dashboard, report complessi e regole di alert basate su query di log.

---

### 7. A che cosa serve una diagnostic setting?
Una **diagnostic setting** è una regola di instradamento (routing) associata a una risorsa Azure.

Non raccoglie i dati direttamente, ma definisce:
1. **Quali** categorie di log e/o metriche della risorsa inviare.
2. **Dove** inviarli (destinazione: Log Analytics Workspace, Storage Account per archiviazione a lungo termine, o Event Hub per lo streaming verso strumenti terzi).

---

### 8. Perché Activity Log e AzureActivity non sono esattamente la stessa cosa?
- **Activity Log:** È il servizio/registro nativo di Azure (accessibile dal portale o da CLI) che traccia gli eventi di gestione del Control Plane a livello di subscription.
- **AzureActivity:** È la **tabella specifica** all'interno di un *Log Analytics workspace* in cui i dati dell'Activity Log vengono memorizzati dopo essere stati inoltrati tramite una *diagnostic setting*.

*Nota operativa:* L'Activity Log nativo è disponibile subito, mentre la tabella `AzureActivity` richiede il flusso di instradamento (diagnostic setting) ed è soggetta a un piccolo tempo di latenza (ingestion time).

---

### 9. Distingui Alert Rule e Action Group.
In Azure Monitor, le notifiche sono disaccoppiate in due componenti distinte:

- **Alert Rule (La Condizione):** Definisce lo *scope* (quale risorsa monitorare), il *segnale* (metrica o log KQL) e la *condizione/soglia* di attivazione (es. "se la CPU media > 85% per 5 minuti").
- **Action Group ( La Reazione):** Definisce *cosa fare* quando una regola di alert viene attivata. Contiene la lista dei destinatari e le azioni automatiche da eseguire (es. invio email, SMS, chiamata ad un Webhook, avvio di una Azure Function o Logic App).

---

### 10. Perché un alert Fired non equivale automaticamente a un incidente?
Un alert che passa allo stato **`Fired`** indica semplicemente che una condizione matematica o logica predefinita è stata soddisfatta.

Non equivale automaticamente a un incidente perché:
- La soglia potrebbe essere stata impostata male o essere troppo sensibile (*alert noise*).
- Potrebbe trattarsi di un picco di carico previsto o temporaneo che non impatta la disponibilità del servizio.
- Un **incidente** richiede una reale interruzione o degradazione del servizio con un impatto sul business o sugli utenti. Un alert è solo un segnale di preavviso per l'operatore.

---

### 11. Qual è la differenza tra correlazione e causalità?
- **Correlazione:** Osserva che due o più eventi si verificano nello stesso arco temporale (es. "Alle 10:00 è stata modificata una configurazione e alle 10:05 la CPU è arrivata al 100%").
- **Causalità:** Dimostra con evidenze tecniche che il primo evento è stato la **causa diretta** del secondo (es. "La modifica delle 10:00 ha introdotto un ciclo infinito che ha portato la CPU al 100%").

Nel troubleshooting non bisogna mai assumere la causalità basandosi solo sulla vicinanza temporale degli eventi (correlazione).

---

### 12. Quali sono i passaggi essenziali di un troubleshooting ripetibile?
Un processo di troubleshooting professionale e strutturato si sviluppa nei seguenti step:

1. **Descrizione del sintomo:** Definire chiaramente cosa non funziona.
2. **Definizione del risultato atteso:** Chiarire qual è il comportamento corretto.
3. **Raccolta delle evidenze:** Raccogliere log, metriche, stati CLI e impostazioni attuali prima di applicare modifiche.
4. **Formulazione dell'ipotesi:** Ipotizzare la causa radice basandosi sulle evidenze.
5. **Verifica dell'ipotesi:** Testare se l'ipotesi è fondata.
6. **Applicazione della modifica minima:** Effettuare un solo cambiamento mirato alla volta per isolare il problema.
7. **Ripetizione del test:** Verificare se la modifica minima ha risolto il problema.
8. **Documentazione:** Registrare la soluzione adottata (es. aggiornare un runbook) per rendere l'esperienza riutilizzabile dal team.

```