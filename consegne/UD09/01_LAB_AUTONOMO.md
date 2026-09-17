# UD09 — Laboratorio Autonomo: Audit di Sicurezza e Readiness Azure DevOps

## Attività 1 — Organization e Project


Organization: azdo-student-lab-01
Project: az900-az104-devops
Visibility: Private
Source repository: GitHub (mio-utente/azure-devops-lab)

Risposte

Perché il Project è privato? 
Per rispettare il principio del minimo privilegio e prevenire l'esposizione indesiderata verso l'esterno di codice sorgente, pipeline di build, log di esecuzione ed eventuali variabili d'ambiente.

Perché il source repository rimane GitHub? 
Per evitare la duplicazione del codice e sfruttare GitHub come unica fonte della verità (Single Source of Truth), separando l'archiviazione del codice (GitHub) dalla gestione ed esecuzione delle pipeline CI/CD (Azure DevOps).

Attività 2 — Matrice Gruppi

| Gruppo | Scopo essenziale | Lo assegneresti a tutti? Perché? |
|---|---|---|
| **Project Administrators** | Gestione completa del progetto, inclusi permessi, policy, connessioni e servizi. | **No.** Viola il principio del minimo privilegio. L'accesso amministrativo va limitato solo a chi gestisce l'infrastruttura di progetto per evitare configurazioni errate o modifiche critiche non autorizzate. |
| **Contributors** | Permette di lavorare sul codice, creare e gestire work item ed eseguire/modificare le pipeline. | **No.** Va assegnato solo agli sviluppatori attivi del team. Dare i permessi di modifica a tutti (inclusi utenti esterni o audit) comporterebbe rischi per la stabilità del codice e delle pipeline. |
| **Readers** | Sola visualizzazione di codice, pipeline e stato dei lavori senza possibilità di modifica. | **No.** Anche se è un ruolo sicuro in sola lettura, l'assegnazione automatica a tutti allargherebbe la superficie di visibilità di progetti privati senza reale necessità operativa. |
| **Build Administrators** | Amministrazione specifica di agent pool, service connection e configurazioni di build. | **No.** Deve essere limitato ai ruoli DevOps/Engineers preposti alla manutenzione degli agenti e delle risorse di esecuzione, evitando che utenti generici alterino l'infrastruttura di CI/CD. |

Attività 3 — GitHub Integration ReadinessEsito Comandi CLI (WSL2):
repository: mio-utente/azure-devops-lab
private: true
gh auth: Logged in to github.com as mio-utente
git fetch: Success (nessun errore di connessione/autenticazione)

Risposte:
Perché in UD09 non abbiamo creato una service connection GitHub OAuth/PAT? 

Perché in questa fase non stiamo ancora eseguendo pipeline attive che necessitano di collegare programmaticamente i due sistemi; l'integrazione con GitHub verrà configurata nel momento in cui collegheremo la prima pipeline reale tramite OAuth o GitHub App.

Quale metodo useremo quando creeremo la prima pipeline GitHub? 

L'integrazione raccomandata Azure Pipelines GitHub App (oppure la Service Connection gestita via OAuth/GitHub Personal Access Token).

Perché evitare di duplicare il repository in Azure Repos? 

Per evitare il disallineamento della cronologia dei commit, la gestione di doppie sincronizzazioni e la dispersione dei processi di Code Review/Pull Request tra due piattaforme diverse.

Attività 4 — Parallel Jobs

Microsoft-hosted: Ready (1 Job con limite 1.800 min/mese per progetti privati)


Self-hosted: 1 Free Concurrent Job (Progetti privati)

Scenario: Microsoft-hosted = 0, self-hosted = 1

Risposte:

Il corso è bloccato? 

No. AVENDO a disposizione 1 parallel job self-hosted, le pipeline possono essere eseguite regolarmente appoggiandosi all'agent registrato su WSL2.

Quanti job self-hosted possono essere eseguiti contemporaneamente con un solo parallel job? 

1 solo job alla volta. Se ci sono più esecuzioni, verranno accodate.

Registrare 3 agent aumenta automaticamente a 3 i job concorrenti? 

No. Il numero di job concorrenti è stabilito dalle licenze/quote acquistate a livello di Organization, non dal numero di agent installati. Con 1 parallel job, anche se sono registrati 3 agent, solo 1 eseguirà mentre gli altri rimarranno inattivi.

Attività 5 — Agent Audit

pool: 

pool-ud09-wsl
agent: wsl-ud09-student
status: Online
version: 3.240.1
Capability Rilevate:PlaintextAgent.OS: Linux
Agent.Version: 3.240.1
git: /usr/bin/git
python: /usr/bin/python3


Attività 6 — Audit dell'Autenticazione di Registrazione

PAT: Revoked
Registration: Personal Access Token (PAT)


Risposte:

Perché l'agent resta Online anche dopo la revoca del PAT? 

Il PAT serve esclusivamente per l'autenticazione e la registrazione iniziale dell'agent presso il pool. Una volta completata la configurazione, l'agent scambia le chiavi e riceve credenziali OAuth dedicate salvate in locale (file .agent), che usa per la connessione continua in polling via HTTPS.

Perché non serve un PAT Full access? 

È sufficiente lo scope ristretto Agent Pools (Read & manage). Un PAT Full Access espone inutilmente l'intera organizzazione a rischi di sicurezza in caso di compromissione del token.

Perché Device Code Flow è un fallback migliore rispetto ad allargare una policy PAT? 

Perché limita la finestra temporale dell'autenticazione all'istante dell'interazione dell'utente, evita di generare token persistenti e rispetta le policy aziendali che bloccano la creazione di PAT a lunga durata.

Attività 7 — Test Offline/Online

Premuto Ctrl+C nel terminale dell'agent: 

lo stato nel portale Azure DevOps è passato a Offline.

Eseguito ./run.sh nel terminale dell'agent: 

lo stato nel portale è tornato a Online senza richiedere ri-configurazione o nuovi token.

Attività 8 — Troubleshooting Scenario

Scenario: Agent configurato ma OfflineChecklist di risoluzione ordinata dal controllo meno invasivo a quello diagnostico avanzato:

1.1. 

Verifica Processo Locale:Verifica esecuzione.

Controllare se il processo ./run.sh è in esecuzione nel terminale WSL2 o se è stato terminato accidentalmente.

Verifica: Se non è in esecuzione, riavviarlo con ./run.sh.

2.2. 

Connettività di Rete e HTTPS:Test connettività.

Verificare se la macchina locale o WSL2 riesce a raggiungere i servizi cloud di Azure DevOps.

Comando: curl -v https://dev.azure.comVerifica: 

Confermare che la risposta sia HTTP 200/302 e non ci siano blocchi DNS o di firewall.

3.3. 

Correttezza URL Organization:File .agent.

Controllare che l'URL dell'organizzazione non sia cambiato e che sia raggiungibile.

Verifica: Ispezionare il file .agent nella directory dell'agent per verificare la corrispondenza dell'URL (https://dev.azure.com/azdo-student-lab-01).

4.4. Stato dell'Agent Pool su Portale:

Verifica impostazioni.

Verificare sul portale Azure DevOps che l'Agent Pool pool-ud09-wsl sia attivo, esistente e non disabilitato da un amministratore.Verifica: Lo stato del pool risulta Enabled nel portale.

5.5. 

Integrità Directory e Configurazione:File locali.Verificare che i file di runtime locali nella directory dell'agent (.agent, .credentials, ecc.) non siano stati corrotti o cancellati.

Verifica: Presenza dei file nascosti con permessi di lettura/scrittura corretti per l'utente.

6.6. 

Diagnostica Avanzata Log:Output diagnostico.

Avviare l'agent in modalità diagnostica per identificare errori nei log di connessione o autenticazione.

Comando: cd ~/azdo-agent && ./run.sh --diagnostics

Verifica: Analisi del log generato nella cartella _diag per risalire alla causa esatta prima di effettuare qualsiasi tentativo di re-registrazione.

Attività 9 — Readiness Finale

| Controllo | Stato |
|---|---|
| **Organization** | PASS |
| **Private Project** | PASS |
| **GitHub integration readiness** | PASS |
| **Hosted status documentato** | PASS |
| **Self-hosted pool** | PASS |
| **Agent Online** | PASS |
| **PAT revocato / Device Code Flow** | PASS |
| **Capability controllate** | PASS |
| **Restart Online/Offline testato** | PASS |
| **Nessun segreto nel repository** | PASS |