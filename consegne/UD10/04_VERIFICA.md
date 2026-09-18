# UD10 — Verifica individuale

## Parte A — Scelta singola

### 1. Una Docker image è:

- B. un artefatto usato per creare container

### 2. Nel comando `docker build -f docker/backend.Dockerfile .`, il punto finale rappresenta:

- B. il build context

### 3. `EXPOSE 8000`:

- B. documenta la porta prevista nell'immagine ma non sostituisce `-p`

### 4. `127.0.0.1:8080:80` significa:

- B. host 127.0.0.1:8080 → container 80

### 5. In Compose, due servizi sulla stessa rete possono normalmente raggiungersi tramite:

- B. nome del servizio

### 6. Dentro il container frontend, `localhost` indica:

- B. il container frontend stesso

### 7. `docker compose down -v`:

- B. rimuove anche i volumi del progetto

### 8. Un container `running`:

- B. può essere running ma unhealthy

---

## Parte B — Risposte brevi

### 9. Distingui image, container e registry.
    - Image: È un modello statico contenente il codice, il runtime e le dipendenze.
    - Container: È l'istanza dinamica e isolata in esecuzione di un'immagine.
    - Registry: È il repository remoto o locale (es. Docker Hub, Azure Container Registry) usato per conservare e distribuire le immagini.

### 10. Spiega perché il build context dovrebbe essere limitato.
    - Perché durante il docker build l'intero contenuto della cartella del contesto viene impacchettato e inviato al daemon Docker. Se la cartella contiene file grandi o inutili (es. file temporanei, .git, node_modules), la build diventa estremamente lenta e consuma memoria inutile. (Si usa il file .dockerignore per limitarlo).

### 11. Distingui named volume e bind mount.
    - Named Volume: È una porzione di disco gestita interamente da Docker. È isolato, portabile e ideale per dati persistenti del container.
    - Bind Mount: Collega direttamente una cartella specifica dell'host (es. /home/user/app) al container. Utile in fase di sviluppo per vedere subito le modifiche al codice senza rifare la build.

### 12. Perché le variabili d'ambiente permettono di riusare la stessa immagine?
    - Perché disaccoppiano la configurazione dal codice della build. L'immagine contiene solo il software ed è immutabile; cambiando le variabili d'ambiente a runtime (es. credenziali DB, porte, log level) si può usare la stessa identica immagine in sviluppo, staging e produzione.

### 13. Perché il backend Compose non deve necessariamente pubblicare una porta sull'host?
    - Perché se il backend deve essere raggiunto solo dal frontend all'interno della stessa rete Docker, essi comunicano privatamente tramite la rete virtuale creata da Compose. Esporre la porta sull'host con ports: serve solo se si vuole accedere al backend direttamente dalla macchina host o dal browser esterno.

### 14. Quali comandi useresti per iniziare il troubleshooting di uno stack Compose che non risponde?
    - docker compose ps -a (per verificare lo stato dei container: running, exited o unhealthy).

    - docker compose logs --tail 50 <nome-servizio> (per analizzare gli errori applicativi a schermo).

    - docker compose config (per verificare che le variabili d'ambiente e la sintassi del YAML siano corrette).

---

## Parte C — Scenario

> `docker compose ps` mostra frontend Running e backend Restarting. Il browser restituisce 502. Nei log backend compare `LOW_STOCK_THRESHOLD deve essere un intero, ricevuto: 'abc'`.

### 15. Qual è la causa più probabile e qual è la modifica minima?
    - Il backend è bloccato a causa del valore errato della variabile d'ambiente `LOW_STOCK_THRESHOLD`, che richiede un numero intero, quindi la modifica minima è ripristinarla nel file `compose.yaml`

### 16. È necessario ricostruire l'immagine? Quali verifiche eseguiresti dopo la correzione?
    - No, non è necessario. L'errore è in una variabile di configurazione runtime passata da Compose, non nell'immagine o nel codice sorgente inserito durante la build.

    - Verifiche:

        - Lancio docker compose up -d per ricreare il container con la configurazione valida.

        - Eseguo docker compose ps e attendo che il backend diventi healthy.

        - Faccio un test HTTP: curl -i [http://127.0.0.1:8080/health](http://127.0.0.1:8080/health) ed esamino i log con docker compose logs backend.

### 17. Una futura pipeline gira su `pool-ud09-wsl` e lo step `docker build` fallisce con errore di connessione al Docker daemon. Quale componente dell'ambiente controlleresti per primo e perché?
    - Controllerei lo stato del servizio/demone Docker sull'agente WSL (es. eseguendo sudo service docker status o sudo service docker start). Poiché in ambienti WSL2 il servizio Docker non si avvia automaticamente all'avvio del sistema, se il demone è spento la CLI di Docker non può comunicare con la socket locale e la build fallisce.

### 18. Distingui, rispetto alla disponibilità dei tool, un self-hosted Agent da un Microsoft-hosted Agent.
    - Self-hosted Agent (es. la tua macchina WSL): L'ambiente è persistente e gestito da te. Devi installare e aggiornare manualmente tutti i tool necessari (Docker, Git, gh, Python). Lo stato si conserva tra un job e l'altro (es. la cache delle immagini Docker rimane).

    - Microsoft-hosted Agent: È una macchina virtuale temporanea e pulita fornita da Microsoft. Viene distrutta dopo ogni esecuzione, ha un set preinstallato di software standard, ma ogni build parte da zero (senza cache locale dei file/immagini delle esecuzioni precedenti).
---

