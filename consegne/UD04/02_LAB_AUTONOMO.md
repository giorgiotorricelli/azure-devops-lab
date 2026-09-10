## Laboratorio autonomo

**Scenario**: Un'applicazione deve conservare documenti consultati frequentemente per 30 giorni e file temporanei eliminabili dopo un giorno. Gli utenti applicativi devono leggere i documenti senza ricevere account key. L'accesso pubblico anonimo non è consentito.

1. La scelta di blob deriva dal fatto che ci serve immagazzinare file non strutturati, come testi, imamgini e video facilmente reperibili tramite URL.
2. In laboratori si usa la ridondanza LRS perchè replica a livello locale ed è economico quindi funziona bene per i test; per quanto riguarda lo scenario utilizzo ZRS perchè il requisito comprende resilienza a un guasto zonale, quindi è perfetto per questo tipo di ridondanza che duplica in zone diverse della stessa regione.
3. Per creare da CLI un altro container con auth-mode login e nome archive ho usato il comando:
```bash
    az storage container create \
  --account-name "$LAB_STORAGE" \
  --name archive \
  --auth-mode login \
> --output table
```

4. Ho caricato la copia del file `01_DOCUMENTO_LAB.txt` con il nome di `documento.txt` nella cartella `current` tramite:
```bash
    az storage blob upload \
    --account-name "$LAB_STORAGE" \
    --container-name "archive" \
    --name current/documento.txt \
    --file consegne/UD04/01_DOCUMENTO_LAB.txt \
    --auth-mode login \
    --overwrite \
    --output table
```

5. Ho creato una SAS tramite l'interfaccia di azure, mantenendo le impostazioni Read e HTTPS only, ovvero il protocollo sicuro, e le ho dato una scadenza di 15 minuti. Poi ho testato la visualizzazione del file incollando l'url generato nel browser, che effettivamente mi ha restituito la visualizzazione del contenuto del file text nel container. Non ho dovuto quindi cancellare nessuna variabile.

6. Controllo errori:
    - AuthorizationPermissionMismatch. L'identità che effettua la richiesta ha superato l'autenticazione (es. az login), ma non dispone del ruolo RBAC sul Data Plane (es. manca Storage Blob Data Contributor) per la risorsa specifica.
    - ResourceNotFound: The specified container does not exist. Chiamata API indirizzata a un container inesistente o parametro nome sbagliato.
    - curl: (22) The requested URL returned error: 403. Le cause principali sono un SAS token scaduto o permessi insufficienti, tipo un tentativo di scrittura con una SAS con soli diritti di lettura.

7. Perchè sono due container diversi e inoltre la regola funziona con il prefisso **documents/temporary/**, quindi anche se fosse applicata a uno scope globale **archive/current/documento.txt** non verrebbe influenzato.

Driver di Costo Principali:
Spazio Archiviato (GB/Mese): Quantità di dati salvati, legata all'Access Tier scelto (Hot costa più di Cool/Archive).

Ridondanza: ZRS ha un costo unitario superiore a LRS per via della replica distribuita su 3 zone.

Operazioni REST / Transazioni: Numero di chiamate API effettuate (lettura, scrittura, listing).

Traffico in Uscita (Egress): Dati scaricati o trasferiti al di fuori della regione Azure.

Sequenza Corretta di Cleanup:
File locali e variabili: Eliminare i file scaricati in /tmp/ ed eseguire l'unset delle variabili d'ambiente contenenti SAS o credenziali.

Eliminazione Risorse Azure: Cancellare l'intero Resource Group (az group delete --name "$LAB_RG" --yes --no-wait) per rimuovere in un solo passaggio Storage Account, container e file.

Verifica: Verificare la completa rimozione con az group wait e az group exists.


