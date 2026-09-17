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


21. Che cosa aggiunge DevSecOps al lifecycle?
22. Che cos'è una DevOps toolchain?
23. Quali sono i cinque principali servizi Azure DevOps?
24. A che cosa serve Azure Boards?
25. Perché nel corso usiamo GitHub invece di Azure Repos?
26. Distingui Azure Test Plans e test automatici in pipeline.
27. Distingui Azure Artifacts e Azure Container Registry.
28. Distingui Organization e Project.
29. Distingui Agent, Agent Pool e Parallel Job.
30. Distingui Microsoft-hosted e self-hosted Agent.
31. Che cos'è una Service Connection?
32. Perché il PAT di registrazione può essere revocato dopo che l'agent è Online?
33. Perché DevOps non coincide con Azure DevOps?
34. Qual è la differenza tra Azure Pipelines e Azure Pipelines Agent?
35. Qual è la relazione concettuale tra Azure Pipelines Agent, Jenkins Agent, GitHub Runner e GitLab Runner?
36. Quali attività svolgerà concretamente l'Agent nelle UD13–UD15?
37. Perché un Job Microsoft-hosted non dovrebbe dipendere da file lasciati dal Job precedente?
38. Perché il WSL2 personale del corso non rappresenta la topologia self-hosted tipica di un team?
39. Come può essere organizzato un Agent Pool aziendale?
40. Perché più Agent non implicano automaticamente più Job eseguibili in parallelo?