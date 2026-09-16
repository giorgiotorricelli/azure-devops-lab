# UD08 — Laboratorio Guidato: Report di Consegna

---

## 1. Dati della Collaborazione e Repository

* **Repository Personale (A):** `https://github.com/mio-utente/azure-devops-lab`
* **Collaboratore Coinvolto (B):** `@collega-devops`
* **Repository Collaboratore (B):** `https://github.com/collega-devops/azure-devops-lab`
* **Directory di Lavoro usata per la PR:** `~/workspace/ud08-collab/repo-a`

---

## 2. Flusso Pull Request Collaborativa

### Dettagli della PR Creata
* **Titolo PR:** `UD08: collaboration evidence`
* **URL PR:** `https://github.com/mio-utente/azure-devops-lab/pull/1`
* **Head Branch:** `feature/ud08-collab-collega-devops`
* **Base Branch:** `main`

### Ciclo di Review e Correzione
1. **Richiesta di Modifica (Review - Request Changes):**
   * **Commento del Reviewer:** *"Aggiungi una sezione '## Esito' con la frase: Review completata e modifica corretta."*
2. **Commit di Correzione:**
   * **Messaggio Commit:** `docs: address UD08 review`
   * **Integrazione:** Modifica effettuata sullo stesso branch ed inviata tramite `git push`. La PR si è aggiornata automaticamente senza aprire nuove richieste.
3. **Approvazione e Merge:**
   * **Esito Review:** `Approve`
   * **Strategia di Merge:** `Squash and merge`
   * **Pulizia:** Rimosso il collaboratore `@collega-devops` dalle impostazioni del repository (`Settings -> Collaborators`) in rispetto del principio di **Least Privilege**.

---

## 3. Risoluzione del Conflitto Git

### Procedura Eseguita
1. Creato il file temporaneo `ud08-conflict.txt` su `main` con contenuto `PORT=8000`.
2. Creato il branch `lab/conflict-a` impostando `PORT=9000`.
3. Creato il branch `lab/conflict-b` partendo da `main` e impostando `PORT=7000`.
4. Eseguito `git merge lab/conflict-a` da `lab/conflict-b`, provocando un merge conflict.

### Risoluzione del Conflitto
I marker generati da Git nel file erano:

```text
<<<<<<< HEAD
PORT=7000
=======
PORT=9000
>>>>>>> lab/conflict-a

```

Azione intrapresa: Eliminati manualmente i marker di conflitto e impostato il valore corretto richiesto dal requisito (PORT=8000).

```bash
git add ud08-conflict.txt
git commit -m "lab: resolve port conflict"

```
Infine eliminato il file e rimossi i branch di test (lab/conflict-a e lab/conflict-b).

4. Baseline e Verifica del Catalogo Prodotti
Struttura Applicativa Verificata
server.py: Backend / API HTTP sviluppato con Python Standard Library.

config.json: File di configurazione del server (porta, host, impostazioni).

data/products.json: Archivio dati dei prodotti in formato JSON.

static/index.html: Frontend dell'applicazione.

Esiti dei Test Eseguiti tramite curl
1. Verifica Healthcheck (GET /health)

```text
HTTP/1.0 200 OK
Server: BaseHTTP/0.6 Python/3.10.12
Date: Wed, 16 Sep 2026 10:00:00 GMT
Content-type: application/json
{"status": "ok"}

```

2. Elenco Prodotti (GET /api/products)
```json
{
  "count": 4,
  "products": [
    {
      "id": "P001",
      "name": "Notebook Pro 14",
      "category": "Notebook",
      "price": 1299.0,
      "stock": 8
    },
    {
      "id": "P002",
      "name": "Monitor 27 UHD",
      "category": "Monitor",
      "price": 349.0,
      "stock": 4
    },
    {
      "id": "P003",
      "name": "Dock USB-C",
      "category": "Accessori",
      "price": 119.0,
      "stock": 15
    },
    {
      "id": "P004",
      "name": "Keyboard Business",
      "category": "Accessori",
      "price": 59.0,
      "stock": 2
    }
  ]
}

```

3. Prodotto Esistente (GET /api/products/P001)
Status Code: 200 OK

Risposta:

```json
{
  "id": "P001",
  "name": "Notebook Pro 14",
  "category": "Notebook",
  "price": 1299.0,
  "stock": 8
}
```

4. Prodotto Inesistente (GET /api/products/XXX)
Status Code: 404 Not Found

Risposta: {"error": "Product not found"}

Nota: Il codice 404 è l'esito previsto e corretto poiché attesta che il server risponde correttamente all'assenza della risorsa richiesta.

5. Stato Finale del Repository
```bash
git status
```
```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```
Baseline inserita tramite commit feat: add local product catalog e sincronizzata sul remote.