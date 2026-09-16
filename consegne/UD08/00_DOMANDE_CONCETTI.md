**1. Perché DevOps non coincide con Azure DevOps?**
DevOps è una cultura e una metodologia di lavoro basata sulla collaborazione tra sviluppo e operations. Azure DevOps è semplicemente un insieme di strumenti fornito da Microsoft per supportare questo flusso.

**2. Distingui Continuous Integration, Continuous Delivery e Continuous Deployment.**
* **Continuous Integration:** Integrare e verificare frequentemente piccole modifiche nella branch comune per individuare subito gli errori.
* **Continuous Delivery:** Mantenere il software in uno stato costantemente pronto per il rilascio, richiedendo un'approvazione manuale prima di andare in produzione.
* **Continuous Deployment:** Automatizzare completamente il flusso, dove ogni modifica che supera i controlli va direttamente e automaticamente in produzione.

**3. Che differenza c'è tra working tree, staging area e commit?**
* **Working tree:** I file fisici nella directory di lavoro su cui si sta lavorando.
* **Staging area:** L'area di preparazione (`git add`) in cui si seleziona cosa includere nella modifica successiva.
* **Commit:** Lo snapshot registrato nella cronologia locale di Git (`git commit`).

**4. Perché conviene creare un feature branch da `main` aggiornata?**
Per lavorare partendo dall'ultimo stato stabile del codice, riducendo le divergenze e minimizzando il rischio di conflitti al momento del merge.

**5. Che cosa rappresentano base branch e head branch in una Pull Request?**
* **Head branch:** La branch sorgente che contiene le modifiche proposte.
* **Base branch:** La branch di destinazione (es. `main`) che riceverà le modifiche.

**6. Perché una review non dovrebbe limitarsi a controllare che il codice "funzioni"?**
Perché deve verificare anche la qualità del lavoro: pulizia del diff, assenza di file spuri o segreti/password, aderenza ai requisiti, leggibilità e assenza di regressioni.

**7. Che cosa succede a una Pull Request quando il contributor aggiunge un nuovo commit allo stesso branch?**
La Pull Request si aggiorna automaticamente con i nuovi commit inviati con il `push`, senza bisogno di aprire una nuova proposta.

**8. Che cosa provoca tipicamente un merge conflict?**
Modifiche incompatibili effettuate da branch diverse sulle stesse righe di un file, che Git non può risolvere in modo automatico senza un intervento umano.

**9. Perché l'accesso del collaboratore deve essere rimosso al termine?**
Per applicare il principio del **least privilege**: concedere l'accesso solo a chi serve, per il tempo strettamente necessario, riducendo la superficie di rischio.

**10. Distingui frontend, backend/API, configurazione e dati nel Catalogo prodotti.**
* **Frontend:** L'interfaccia utente visibile nel browser (`static/index.html`).
* **Backend/API:** La logica del server che gestisce le richieste HTTP e le risposte (`server.py`).
* **Configurazione:** I parametri del server (`config.json`).
* **Dati:** Le informazioni sui prodotti memorizzate separatamente (`data/products.json`).

**11. Quali endpoint principali espone l'applicazione?**
* `GET /health`
* `GET /api/products`
* `GET /api/products/<id>`
* `GET /`

**12. Perché è utile eseguire `git diff` prima del commit?**
Per controllare esattamente quali righe sono state modificate ed evitare di inserire nel commit modifiche involontarie o file estranei.