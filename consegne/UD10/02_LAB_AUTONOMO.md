Il container backend è running? Dipende dal riavvio automatico, ma andrà in crash/restart loop oppure rimarrà attivo ma non funzionante.

È healthy? No, risulterà unhealthy perché il controllo di salute fallisce non rispondendo la porta 8000.

Quale eccezione compare nei log? Un errore di conversione di tipo in Python: ValueError: invalid literal for int() with base 10: 'not-a-number'.

Quale variabile viene citata? LOW_STOCK_THRESHOLD


Sintomo: Il backend non risponde alle chiamate HTTP e lo stato del container risulta unhealthy.
Risultato atteso: Il container backend deve avviarsi in stato Healthy e la rotta /health deve rispondere HTTP 200.
Evidenza: Nei log del container backend compare ValueError su int() durante la lettura di LOW_STOCK_THRESHOLD.
Ipotesi: Il file compose.yaml contiene un valore stringa non convertibile in numero intero per la soglia di stock.
Causa: La variabile LOW_STOCK_THRESHOLD è impostata a "not-a-number" invece di un numero valido (es. "5").

Ripristinato LOW_STOCK_THRESHOLD: "5" nel file compose.yaml

eseguo:

```bash
docker compose up -d
```

Verifico:

```bash
docker compose ps
```

il backend è healthy

```bash
curl -s http://127.0.0.1:8080/api/products \
  | python3 -m json.tool
```

Il comando restituisce correttamente la lista dei post

Era necessario docker compose build?
No.
Motivazione: L'errore risiedeva esclusivamente in una variabile d'ambiente definita nel file compose.yaml, che agisce a livello di runtime (durante la creazione/avvio del container). Non è stato modificato il codice sorgente Python né le istruzioni del Dockerfile, di conseguenza le immagini Docker non andavano ricreate

Verificato tramite:

```bash
docker compose config
docker compose up -d
curl -i http://127.0.0.1:8080/health
```

status 200 OK

Controllare che la modifica sia limitata al Compose:

```bash
 cd "$(git rev-parse --show-toplevel)""
git diff
```

l'output identifica effettivamente solo la modifica fatta nel compose.yaml

Creo pull request tramite:

```bash
gh pr create \
  --base main \
  --head fix/ud10-invalid-threshold \
  --title "UD10: validate Docker runtime configuration" \
  --body "Diagnosi di variabile runtime non valida, ripristino del threshold e verifica completa dello stack."
```

Eseguo il merge:

```bash
gh pr merge --squash --delete-branch
```

Andato a buon fine:
```bash
✓ Deleted local branch fix/ud10-invalid-threshold and switched to branch main
✓ Deleted remote branch fix/ud10-invalid-threshold
```

l'output indica che sia il branch locale che remoto sono stati eliminati correttamente

