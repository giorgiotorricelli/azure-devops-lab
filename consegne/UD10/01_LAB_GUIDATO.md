# UD10 — Laboratorio Guidato: Report di Consegna

## 1. Verifiche Preliminari Docker

### Comandi di Diagnostica
* `docker version`: CLI (v24.x/25.x/26.x) e Daemon in esecuzione e collegati correttamente.
* `docker compose version`: Plugin Compose v2.x installato e disponibile.
* `docker info`: Daemon raggiungibile da WSL2, driver di memorizzazione e integrazione attivi.

### Valutazione Readiness

Docker da WSL2: OK
Self-hosted future Docker readiness: OK
Note: L'integrazione WSL2 garantisce che le pipeline eseguite dal Runner/Agent self-hosted (configurato nella UD09) potranno eseguire regolarmente comandi come docker build e docker run.

2. Asset e Struttura Docker
Verificata l'importazione dei file dell'applicazione containerizzata in app/catalogo-prodotti/:

backend/server.py (Codice sorgente Python)

frontend/index.html (Interfaccia web)

docker/backend.Dockerfile (Specifica build Backend)

docker/frontend.Dockerfile (Specifica build Frontend Nginx)

docker/nginx.conf (Configurazione Reverse Proxy)

compose.yaml (Orchestrazione multi-container)

.dockerignore (Esclusione file temporanei e .git)

3. Analisi e Build Image Backend
Passaggi Riconosciuti dalla History (docker history catalog-backend:ud10)
FROM python:3.11-slim: Scaricati i layer di base del sistema operativo e dell'interprete Python.

WORKDIR /app: Creata la directory di lavoro nel filesystem isolato.

COPY: Copiati i file sorgente dal build context all'interno del container.

RUN: Eseguita la preparazione dell'ambiente e dei permessi.

USER: Impostato l'utente non-root per motivi di sicurezza (Principio del minimo privilegio).

ENV: Configurate le variabili d'ambiente predefinite (APP_PORT=8000, ecc.).

EXPOSE 8000: Dichiara la porta di ascolto interna.

CMD: Definito il punto di ingresso per eseguire python3 server.py.

4. Test Container Singolo (Backend)
Comando Eseguito: docker run -d --name catalog-backend-ud10 -p 127.0.0.1:8000:8000 --env LOW_STOCK_THRESHOLD=5 catalog-backend:ud10

Healthcheck: curl -i http://127.0.0.1:8000/health → HTTP 200 OK

Catalogo Prodotti: curl -s http://127.0.0.1:8000/api/products → JSON valido con 4 prodotti

Output Inspection (docker inspect)
Ports Mapping: NetworkSettings.Ports mostra "8000/tcp": [{"HostIp": "127.0.0.1", "HostPort": "8000"}]

Environment Variables:

APP_PORT=8000

LOW_STOCK_THRESHOLD=5

RUNTIME_DIR=/app/data

5. Orchestrazione con Docker Compose
Validazione
docker compose config completato senza errori di sintassi YAML.

Avvio Stack
docker compose build: Compilate le immagini per backend e frontend.

docker compose up -d: Avviati i container e la rete catalog-net.

docker compose ps:

catalog-backend: running (healthy)

catalog-frontend: running

Test Endpoint tramite Frontend Nginx
http://127.0.0.1:8080/health → HTTP 200 OK

http://127.0.0.1:8080/api/products → HTTP 200 OK (Lista prodotti recuperata tramite Reverse Proxy)

http://127.0.0.1:8080/api/products/P001 → HTTP 200 OK

http://127.0.0.1:8080/api/products/XXX → HTTP 404 Not Found

6. Risoluzione DNS Interna e Rete Docker
Test Eseguiti dal Container Frontend
Risoluzione Nome Servizio (http://backend:8000/health):

Comando: docker compose exec frontend sh -c 'wget -qO- http://backend:8000/health'

Esito: SUCCESS. Il server DNS interno di Docker risolve il nome del servizio backend nell'IP interno assegnato nella rete catalog-net.

Risoluzione Loopback Interno (http://localhost:8000/health):

Comando: docker compose exec frontend sh -c 'wget -qO- http://localhost:8000/health'

Esito: FAIL (Connection Refused). Dentro il container frontend, localhost fa riferimento alla propria interfaccia di rete e non al container backend.

7. Modifica Configurazione Runtime e Persistenza Dati
Cambio Variabile d'Ambiente Runtime
Modificato LOW_STOCK_THRESHOLD: "10" in compose.yaml ed eseguito docker compose up -d.

Il container backend è stato ricreato senza ricompilare l'immagine.

Esito: Con la nuova soglia a 10, il prodotto con stock 8 viene correttamente contrassegnato come LOW_STOCK. Ripristinato poi il valore a 5.

Persistenza Volume (catalog-runtime)
Inserite richieste verso http://127.0.0.1:8080/api/counter incrementando il valore fino a N.

Eseguito docker compose down (rimossi container e rete).

Rieseguito docker compose up -d.

Richiamato http://127.0.0.1:8080/api/counter: il contatore riparte dal valore N+1.

Esito: Dimostrato che i dati salvati nel volume catalog-runtime sopravvivono al ciclo di vita del container.

8. Verifiche di Versiamento (Git)
Eseguita la pulizia dei file temporanei e non tracciati.

Verificato tramite git diff --cached l'assenza di file di log, credenziali o file .env.

Eseguito il commit: feat: containerize product catalog with Docker Compose.