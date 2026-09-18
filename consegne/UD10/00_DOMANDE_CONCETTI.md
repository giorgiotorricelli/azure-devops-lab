# UD10 — Domande e ConcettiTeorici

**1. Qual è la differenza fra codice sorgente, image e container?**
* **Codice sorgente:** Il testo/script scritto dallo sviluppatore (es. `.py`, `.js`). È statico ed è la base del progetto.
* **Image (Immagine):** Un pacchetto eseguibile statico e immutabile (read-only) che contiene il codice, le dipendenze, le librerie e le configurazioni necessarie per eseguire l'applicazione.
* **Container:** Un'istanza di processo isolata e in esecuzione (*runtime*) basata su un'immagine. È l'elemento dinamico con uno strato di scrittura temporaneo.

**2. Che cosa fa `docker build` e che cosa non fa?**
* **Cosa fa:** Legge le istruzioni del `Dockerfile`, impacchetta il codice e le dipendenze definite nel *build context* e crea un'immagine Docker immutabile.
* **Cosa NON fa:** Non avvia un container permanente, non esegue l'applicazione in modalità operativa e non pubblica porte sull'host.

**3. Che ruolo hanno Dockerfile, build context e `.dockerignore` nella creazione di un'image?**
* **Dockerfile:** Il file di configurazione con le istruzioni passo-passo per costruire l'immagine.
* **Build Context:** La directory di file e cartelle inviata al daemon Docker per essere utilizzata durante il build.
* **`.dockerignore`:** Un file che specifica quali file/cartelle escludere dal build context (es. `.git`, file temporanei, `.venv`), velocizzando la build e riducendo la dimensione dell'immagine.

**4. Che cosa succede se l'image indicata da `FROM` non è disponibile localmente?**
Docker contatta automaticamente il registro remoto (di default Docker Hub), scarica l'immagine indicata con i relativi layer e la salva nella cache locale prima di proseguire con l'esecuzione delle istruzioni del Dockerfile.

**5. Distingui `RUN` e `CMD`.**
* **`RUN`:** Viene eseguito **durante la fase di build** dell'immagine per installare pacchetti o preparare l'ambiente (crea un nuovo layer nell'immagine).
* **`CMD`:** Specifica il comando di default che verrà eseguito **quando il container viene avviato** (in *runtime*).

**6. Perché una modifica a `server.py` non modifica automaticamente un'image già costruita?**
Perché le immagini Docker sono **immutabili**. Il file `server.py` è stato copiato dentro l'immagine al momento della build. Qualsiasi modifica al codice sorgente sull'host richiede un nuovo `docker build` per essere inclusa in una nuova versione dell'immagine.

**7. A che cosa serve il tag `catalog-backend:ud10`?**
Serve a identificare in modo univoco l'immagine compilata. `catalog-backend` rappresenta il nome del repository/immagine, mentre `:ud10` è l'etichetta (*tag*) di versione, utile per versionare e fare riferimento all'immagine esatta nelle configurazioni o nei container.

**8. Che cosa succede, in ordine, quando eseguiamo `docker run catalog-backend:ud10`?**
1. Docker verifica la presenza locale dell'immagine `catalog-backend:ud10` (se non c'è, prova a scaricarla).
2. Crea un container isolato con il proprio filesystem sovrapposto.
3. Assegna le risorse di rete e di sistema al container.
4. Esegue il comando definito nell'istruzione `CMD` (o `ENTRYPOINT`) dell'immagine.

**9. Distingui `EXPOSE 8000` e `--publish 127.0.0.1:8000:8000`.**
* **`EXPOSE 8000`:** Ha valore puramente documentale/informativo all'interno del `Dockerfile`; dichiara che il container ascolta su quella porta, ma **non la rende accessibile** dall'host.
* **`--publish 127.0.0.1:8000:8000` (`-p`):** Crea una regola di port-mapping reale sul firewall/network dell'host, reindirizzando il traffico della porta 8000 dell'host locale direttamente alla porta 8000 del container.

**10. Che problema risolve Docker Compose rispetto a molti comandi `docker run` manuali?**
Risolve la complessità di gestione: permette di definire, avviare e orchestrare un'applicazione multi-container tramite un unico file di configurazione dichiarativo (`compose.yaml`), evitando di dover digitare a mano lunghi comandi `docker run` con reti, volumi e variabili per ciascun servizio.

**11. Distingui il ruolo di un Dockerfile dal ruolo di `compose.yaml`.**
* **Dockerfile:** Definisce come **costruire una singola immagine** (ambiente, dipendenze, comando d'avvio di un singolo componente).
* **`compose.yaml`:** Definisce come **orchestrare ed eseguire più container insieme** (reti condivise, dipendenze, volumi, mapping delle porte, politiche di riavvio).

**12. Che cosa crea/gestisce Compose nella nostra UD10?**
Crea e gestisce:
* I servizi (container per Backend e Frontend/Nginx).
* La rete virtuale isolata del cluster/applicazione (`catalog-net`).
* Le politiche di avvio, le verifiche di salute (*healthcheck*) e il mapping delle porte verso l'host.

**13. Perché `docker compose up -d --build` può costruire sia backend sia frontend?**
Perché nel file `compose.yaml`, sotto la definizione dei singoli servizi (es. `backend` e `frontend`), è presente la direttiva `build:` con il percorso ai rispettivi contesti/Dockerfile. Il flag `--build` forza la ricompilazione di tutte le immagini che hanno una sezione `build` definita.

**14. Perché il backend non viene pubblicato direttamente sull'host nello stack Compose?**
Per motivi di sicurezza e incapsulamento (*Zero Trust* interno). Il backend è accessibile solo dai container presenti nella rete interna `catalog-net`. L'unico punto d'accesso esposto verso l'esterno/host è il Frontend (Nginx), che agisce da Reverse Proxy.

**15. Perché Nginx usa `http://backend:8000` e non `http://localhost:8000`?**
Perché all'interno di una rete Docker ogni container ha la propria interfaccia `localhost` isolata. Nginx sfrutta il server DNS interno di Docker, che risolve il nome del servizio `backend` (definito in Compose) nell'indirizzo IP interno corretto del container backend.

**16. Che funzione ha `catalog-net`?**
È la rete bridge virtuale isolata creata da Docker Compose che consente la comunicazione diretta, sicura e con risoluzione DNS per nome-servizio tra i container appartenenti allo stack (Backend e Frontend).

**17. Che funzione ha `catalog-runtime`?**
È il nome identificativo o l'etichetta associata allo stack/ambiente di esecuzione in runtime gestito da Docker Compose per raggruppare le risorse correlate.

**18. Perché `running` e `healthy` non significano la stessa cosa?**
* **`running`:** Indica semplicemente che il processo principale del container è attivo a livello di sistema operativo.
* **`healthy`:** Indica che l'applicazione dentro il container sta rispondendo ed è **realmente operativa**, come confermato dal superamento dei test definiti nell'`healthcheck` (es. una richiesta HTTP /health che restituisce 200 OK).

**19. Che cosa fa `depends_on: condition: service_healthy` nel nostro stack?**
Garantisce l'ordine corretto di avvio: ordina a Docker Compose di attendere non solo che il container backend sia avviato (`running`), ma che abbia superato con successo la verifica di salute (`healthy`) prima di avviare il servizio dipendente (Frontend).

**20. Distingui rebuild e recreate.**
* **Rebuild:** La ricompilazione dell'immagine Docker a partire dai Dockerfile e dal codice sorgente (`docker compose build`).
* **Recreate:** La distruzione e ricreazione del container da un'immagine già esistente (ad esempio in caso di modifica alle configurazioni del file `compose.yaml`).

**21. Quale ordine useresti per diagnosticare uno stack Compose che non risponde?**
1. **`docker compose ps`:** Per verificare lo stato dei container (`running`, `healthy` o `exited`).
2. **`docker compose logs`:** Per ispezionare gli errori nei log dei singoli servizi (es. `docker compose logs backend`).
3. **`docker exec -it <container> ...` / `curl` interno:** Per testare le risposte di rete direttamente all'interno della rete Docker (`catalog-net`).
4. **Verifica mapping porte/host:** Controllare che le porte sull'host siano aperte e non occupate da altri processi.

**22. In che modo ciò che facciamo manualmente in UD10 verrà riutilizzato nelle pipeline successive?**
I Dockerfile e il file `compose.yaml` scritti e testati localmente in UD10 diventeranno gli artefatti centrali automatizzati nelle pipeline CI/CD: la pipeline eseguirà i comandi di `build`, `test` ed `healthcheck` in modo automatico ad ogni commit, rilasciando l'applicazione in modo coerente negli ambienti di staging e produzione.