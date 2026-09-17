# UD09 — Domande di Controllo e Concetti

### 1. Perché DevOps non può essere ridotto a un prodotto?
Perché DevOps è una **cultura lavorativa**, un insieme di pratiche e principi organizzativi. Gli strumenti e i software aiutano ad applicare DevOps, ma senza una collaborazione reale tra chi sviluppa (Dev) e chi gestisce le infrastrutture (Ops), nessun prodotto da solo può fare "DevOps".

---

### 2. Quali sono le tre dimensioni principali che abbiamo associato a DevOps?
Le tre dimensioni fondamentali sono:
* **Persone (Cultura):** Collaborazione, condivisione delle responsabilità e comunicazione tra i team.
* **Processi:** Metodologie di lavoro fluide, automazione delle attività e miglioramento continuo.
* **Tecnologie (Strumenti):** Software e piattaforme che automatizzano build, test e rilasci.

---

### 3. Perché il lifecycle DevOps è rappresentato come un ciclo?
È rappresentato come un **fiscio infinito (a forma di 8)** perché il software non si considera mai "finito" per sempre. Dopo il rilascio in produzione, si raccolgono dati e feedback tramite il monitoraggio, che diventano nuovi requisiti per la pianificazione successiva. È un ciclo continuo di miglioramento.

---

### 4. Distingui Agile e DevOps.
* **Agile:** Si concentra sulla collaborazione con l'utente e sulla gestione del cambiamento per creare software funzionante in brevi iterazioni (sviluppo rapido).
* **DevOps:** Estende la filosofia Agile a tutto il ciclo di vita del software, includendo il deployment, la gestione dell'infrastruttura, la sicurezza e le operazioni in produzione.

---

### 5. Che cos'è un backlog?
Il backlog è una **lista ordinata di priorità** che contiene tutto ciò che serve per il progetto: nuove funzionalità da sviluppare, requisiti tecnici, miglioramenti e bug da correggere.

---

### 6. Che cos'è uno sprint?
Uno sprint è un **periodo di tempo fisso e breve** (solitamente da 1 a 4 settimane) in cui il team si impegna a completare un blocco specifico di lavoro selezionato dal backlog per consegnare una versione funzionante del software.

---

### 7. Distingui Scrum e Kanban.
* **Scrum:** È basato su intervalli di tempo fissi (sprint), ruoli ben definiti (Product Owner, Scrum Master, Team) e cerimonie regolari (Daily Meeting, Sprint Planning).
* **Kanban:** È un metodo basato sul flusso continuo, incentrato sulla visualizzazione del lavoro (tramite lavagne/board) e sul limite al lavoro in corso (WIP - Work in Progress), senza sprint a durata fissa.

---

### 8. Distingui Epic, Feature, User Story, Task e Bug.
* **Epic:** Un grande obiettivo o insieme di funzionalità che richiede molto tempo per essere completato (es. *Area Riservata Clienti*).
* **Feature:** Una funzionalità specifica che compone una Epic (es. *Login con SPID*).
* **User Story:** Il requisito visto dal punto di vista dell'utente finale (*"Come utente desidero X per ottenere Y"*).
* **Task:** Una singola attività tecnica necessaria per completare una Story (es. *Creare tabella utenti nel DB*).
* **Bug:** La segnalazione di un malfunzionamento o difetto nel codice esistente che va corretto.

---

### 9. A che cosa servono gli Acceptance Criteria?
Servono a definire le **condizioni oggettive** che una User Story deve soddisfare per essere considerata completata con successo e pronta per essere consegnata al cliente o in produzione.

---

### 10. Perché il Version Control è importante anche per IaC e pipeline YAML?
Perché trattare l'infrastruttura (IaC) e i processi di automazione (pipeline YAML) come codice sorgente consente di **tracciare tutte le modifiche**, fare **rollback** in caso di errori, automatizzare i test e applicare le stesse regole di qualità e review usate per il codice applicativo.

---

### 11. Distingui build e artifact.
* **Build:** È il processo di compilazione, assemblaggio e test del codice sorgente.
* **Artifact (Artefatto):** È il file o pacchetto finale generato dal processo di build (es. un file `.jar`, un file `.zip` o un'immagine Docker) pronto per essere distribuito o installato.

---

### 12. Distingui unit test, integration test e smoke test.
* **Unit Test:** Verifica piccoli blocchi isolati di codice (come singole funzioni o classi).
* **Integration Test:** Verifica che diversi moduli o componenti dell'applicazione interagiscano correttamente tra loro (es. app + database).
* **Smoke Test:** Un test rapido ed essenziale eseguito dopo il deployment per verificare che le funzionalità critiche di base rispondano (es. *il server risponde alla homepage?*).

---

### 13. Che cosa significa shift-left?
Significa **anticipare le attività di controllo** (come test di qualità, verifiche di sicurezza e controlli d'infrastruttura) nelle fasi iniziali del ciclo di sviluppo, invece di eseguirle alla fine prima della produzione, riducendo così i costi e i rischi di errore.

---

### 14. Definisci Continuous Integration.
La Continuous Integration (CI) è la pratica automatizzata che consiste nell'integrare frequentemente le modifiche al codice di più sviluppatori in un repository condiviso, eseguendo automaticamente build e test a ogni push per rilevare subito eventuali errori.

---

### 15. Distingui Stage, Job e Step.
All'interno di una pipeline di automazione (es. YAML):
* **Stage:** Una macro-fase logica del processo (es. *Build*, *Test*, *Deploy*).
* **Job:** Un insieme di passaggi che vengono eseguiti in sequenza su un singolo agente/runner isolato.
* **Step:** La singola istruzione o comando da eseguire all'interno di un Job (es. un comando bash o uno script).

---

### 16. Distingui Continuous Delivery e Continuous Deployment.
* **Continuous Delivery:** Il codice passa automaticamente tutti i test ed è **pronto per la produzione** in qualsiasi momento, ma il rilascio finale richiede un'approvazione o un'azione manuale.
* **Continuous Deployment:** Ogni modifica che supera con successo la pipeline viene **rilasciata automaticamente in produzione** senza alcun intervento umano.

---

### 17. Distingui Dockerfile, image e container.
* **Dockerfile:** Il file di testo contenente le istruzioni per costruire un'immagine.
* **Image (Immagine):** Il pacchetto statico, immutabile ed eseguibile che contiene il codice, le librerie e le dipendenze dell'applicazione.
* **Container:** L'istanza in esecuzione (dinamica) di un'immagine Docker.

---

### 18. Che cos'è un registry?
È un **archivio centralizzato** (come Docker Hub, Azure Container Registry o GitHub Packages) in cui vengono salvate, gestite e distribuite le immagini dei container.

---

### 19. Che problema risolve un orchestrator?
Risolve la complessità di gestire **molti container distribuiti su più server**. Un orchestratore (come Kubernetes) gestisce automaticamente la scalabilità, la disponibilità, la rete, il riavvio in caso di guasto e i bilanciamenti di carico.

---

### 20. Che cos'è Infrastructure as Code?
Infrastructure as Code (IaC) è la pratica di **gestire, configurare ed erogare risorse di infrastruttura** (server, reti, database) tramite file di configurazione o codice leggibile (es. Terraform, Bicep, Ansible), anziché configurarle manualmente tramite interfacce grafiche o terminali.

---

### 21. Che cosa aggiunge DevSecOps al lifecycle?
Aggiunge la **sicurezza integrata fin dalle prime fasi** dello sviluppo (*Shift-Left Security*). Invece di verificare la sicurezza solo alla fine, DevSecOps automatizza controlli di vulnerabilità nel codice, nelle dipendenze e nelle infrastrutture durante tutto il ciclo di vita.

---

### 22. Che cos'è una DevOps toolchain?
È l'insieme di **strumenti e tecnologie interconnesse** che coprono le varie fasi del ciclo di vita DevOps (es. Jira/Azure Boards per il planning, Git per la gestione codice, Jenkins/Azure Pipelines per la CI/CD, Docker/Terraform per la deployment).

---

### 23. Quali sono i cinque principali servizi Azure DevOps?
I cinque servizi principali sono:
1. **Azure Boards** (gestione progetti ed elementi di lavoro)
2. **Azure Repos** (repository di codice sorgente Git)
3. **Azure Pipelines** (automazione CI/CD)
4. **Azure Test Plans** (gestione test manuali ed esplorativi)
5. **Azure Artifacts** (gestione pacchetti di codice/dipendenze)

---

### 24. A che cosa serve Azure Boards?
Serve a **pianificare, tracciare e gestire il lavoro** dei team di sviluppo tramite strumenti visuali come Kanban board, backlog, sprint ed elementi di lavoro (Epics, Features, User Stories, Bugs).

---

### 25. Perché nel corso usiamo GitHub invece di Azure Repos?
Perché **GitHub** è lo standard di fatto del mercato ed è integrato nativamente con Azure DevOps. Usarlo consente di sperimentare un ambiente reale con integrazione *cross-platform* tra piattaforme differenti.

---

### 26. Distingui Azure Test Plans e test automatici in pipeline.
* **Azure Test Plans:** Serve a gestire e tracciare l'esecuzione di **test manuali**, test di accettazione utente (UAT) e test esplorativi.
* **Test automatici in pipeline:** Sono script di test (es. unitari o di integrazione) eseguiti **automaticamente ed in modo non presidiato** dall'Agent durante l'esecuzione della pipeline.

---

### 27. Distingui Azure Artifacts e Azure Container Registry.
* **Azure Artifacts:** Gestisce pacchetti e dipendenze di codice sorgente (es. pacchetti NuGet, npm, Maven, PyPI).
* **Azure Container Registry (ACR):** È un registry dedicato unicamente al salvataggio e alla gestione delle **immagini Docker/container**.

---

### 28. Distingui Organization e Project.
* **Organization:** È il contenitore di livello più alto in Azure DevOps che raggruppa tutti i progetti, gli utenti e le risorse aziendali.
* **Project:** È una sotto-divisione all'interno dell'Organization utilizzata per isolare il codice, le pipeline e il lavoro di un singolo team o progetto applicativo.

---

### 29. Distingui Agent, Agent Pool e Parallel Job.
* **Agent:** La singola macchina/processo che esegue concretamente i comandi della pipeline.
* **Agent Pool:** Un raggruppamento logico di uno o più Agent disponibili per l'organizzazione.
* **Parallel Job:** Il numero massimo di Job che l'organizzazione ha la licenza di eseguire **contemporaneamente** (in parallelo).

---

### 30. Distingui Microsoft-hosted e self-hosted Agent.
* **Microsoft-hosted Agent:** Macchine virtuali gestite, aggiornate e fornite direttamente da Microsoft, ricreate da zero ad ogni singola esecuzione (usa e getta).
* **Self-hosted Agent:** Macchine o container gestiti direttamente dall'utente o dall'azienda, con controllo completo su software, rete ed ambiente di esecuzione.

---

### 31. Che cos'è una Service Connection?
È una **connessione sicura e autenticata** in Azure DevOps che consente alle pipeline di interagire con servizi esterni (es. un abbonamento Azure, GitHub, Docker Hub) senza dover esporre credenziali o token nel codice YAML.

---

### 32. Perché il PAT di registrazione può essere revocato dopo che l'agent è Online?
Perché il **PAT (Personal Access Token)** serve **solo durante la fase iniziale di registrazione** dell'Agent per autenticarsi ed inserirlo nell'Agent Pool. Una volta registrato, l'Agent riceve credenziali dedicate proprie per le successive comunicazioni.

---

### 33. Perché DevOps non coincide con Azure DevOps?
Perché **DevOps** è una filosofia, cultura e metodologia di lavoro universale, mentre **Azure DevOps** è semplicemente una delle tante suite di strumenti commerciali (prodotta da Microsoft) per implementarla.

---

### 34. Qual è la differenza tra Azure Pipelines e Azure Pipelines Agent?
* **Azure Pipelines:** È il servizio cloud orchestratore che legge i file YAML, gestisce le code dei Job e ne pianifica l'esecuzione.
* **Azure Pipelines Agent:** È il software installato su un server/macchina che scarica ed **esegue fisicamente i comandi** definiti nei Job della pipeline.

---

### 35. Qual è la relazione concettuale tra Azure Pipelines Agent, Jenkins Agent, GitHub Runner e GitLab Runner?
Svolgono tutti **esattamente lo stesso ruolo concettuale**: sono gli esecutori fisici (worker/runner) che ricevono istruzioni dal rispettivo orchestratore cloud/CI-CD ed eseguono i comandi nei vari sistemi.

---

### 36. Quali attività svolgerà concretamente l'Agent nelle UD13–UD15?
L'Agent scaricherà il codice dal repository, eseguirà il build dell'applicazione, lancerà i test automatici, costruirà le immagini Docker e distribuirà (deploy) l'applicazione su Azure.

---

### 37. Perché un Job Microsoft-hosted non dovrebbe dipendere da file lasciati dal Job precedente?
Perché ogni Job eseguito su Agent Microsoft-hosted viene lanciato su una **macchina virtuale pulita e diversa**, distrutta subito dopo il termine. I dati non persistono automaticamente tra un Job e l'altro.

---

### 38. Perché il WSL2 personale del corso non rappresenta la topologia self-hosted tipica di un team?
Perché un ambiente WSL2 personale è **locale, monoutente e temporaneo**. In un ambiente aziendale reale, gli Agent self-hosted risiedono su server dedicati (VM o cluster K8s), sempre attivi, condivisi da tutto il team e gestiti centralmente.

---

### 39. Come può essere organizzato un Agent Pool aziendale?
Può essere organizzato dividendo gli Agent per **ambiente** (es. Pool-Dev, Pool-Prod), per **capacità hardware/software** (es. Agent Windows per codice .NET, Agent Linux per Docker/Kubernetes) o per **livello di sicurezza e rete** (es. rete interna isolata).

---

### 40. Perché più Agent non implicano automaticamente più Job eseguibili in parallelo?
Perché il numero di esecuzioni contemporanee è limitato dalla **quota dei Parallel Jobs** acquistati o concessi nell'organizzazione Azure DevOps. Se si hanno 5 Agent ma solo 1 Parallel Job, verrà eseguito un solo Job alla volta e gli altri rimarranno in coda.