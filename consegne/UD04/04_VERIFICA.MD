# Verifica — Azure Storage

## Parte A — Scelta singola

1. **A. Blob**
2. **B. Files**
3. **B. No, serve un ruolo del piano dati**
4. **B. ZRS**
5. **B. SAS**
6. **C. segnala una soglia ma non blocca i consumi**
7. **B. soltanto Blob che corrispondono al filtro**
8. **A. rende esplicito l'uso dell'identità Entra**

## Parte B — Risposte brevi

9. **Distingui ridondanza e backup.**
   - **Ridondanza:** Replica i dati su più dischi/zone per la continuità operativa in caso di guasti hardware. Non protegge da cancellazioni accidentali.
   - **Backup:** Copia storica dei dati usata per il ripristino (*recovery*) a seguito di eliminazioni, corruzioni o attacchi.

10. **Distingui management plane e data plane con un comando per ciascuno.**
    - **Management Plane:** Gestisce l'infrastruttura dell'account.
      `az storage account create --name mystorage --resource-group myRG`
    - **Data Plane:** Gestisce i dati ed i contenuti effettivi.
      `az storage blob upload --container-name docs --file doc.pdf --name doc.pdf --auth-mode login`

11. **Elenca quattro proprietà di una SAS a minimo privilegio.**
    - Permessi ristretti alle sole azioni necessarie (es. solo lettura `r`).
    - Scadenza temporale breve.
    - Ambito limitato al singolo blob o container.
    - Restrizione per protocollo HTTPS ed eventualmente IP del client.

12. **Spiega perché Archive non è appropriato per dati da recuperare immediatamente.**
    - È un livello *offline*. Per accedere ai dati serve un processo di ripristino (*rehydration*) verso i tier Hot o Cool che richiede fino a 15 ore.

13. **Perché non bisogna salvare account key o SAS nel repository?**
    - Perché le chiavi verrebbero esposte a scansioni e persone non autorizzate, permettendo l'accesso ai dati o la creazione di costi indebiti.

## Parte C — Caso situazionale

14. **Individua almeno tre problemi.**
    - `Contributor` gestisce l'infrastruttura, non i dati.
    - L'assenza di `--auth-mode login` forza l'uso delle chiavi di gestione invece di Entra ID.
    - La condivisione di Account Key o SAS senza scadenza breve e con permessi completi crea una grave vulnerabilità.

15. **Proponi autorizzazione e scope più appropriati.**
    - Autenticazione con **Microsoft Entra ID** (es. Managed Identity).
    - Ruolo **Storage Blob Data Reader** assegnato esclusivamente allo scope del container privato.

16. **Indica come verificheresti accesso e cleanup senza pubblicare segreti.**
    - **Accesso:** Test con login Entra senza usare chiavi:
      `az storage blob download --account-name <account> --container-name <container> --name <file> --file <path> --auth-mode login`
    - **Cleanup:** Cancellazione dei file locali usati per i test e verifica con `git status` per assicurarsi di non committare file di configurazione con segreti.