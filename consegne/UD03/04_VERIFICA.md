## Parte A — Scelta singola

1. Un utente accede al portale ma non può leggere un resource group. Quale affermazione è corretta?
   - B. L'autenticazione è riuscita, ma manca un'autorizzazione applicabile

2. Quale elemento non appartiene alla role assignment Azure?
   - D. Password

3. Un team deve soltanto consultare un resource group. Qual è la scelta minima?
   - C. Reader sul resource group

4. Un ruolo assegnato alla sottoscrizione rispetto a un resource group figlio è normalmente:
   - B. ereditato

5. Quale ruolo può gestire risorse ma normalmente non creare role assignment?
   - B. Contributor

6. Un lock `CanNotDelete` applicato a un resource group:
   - C. impedisce l'eliminazione finché applicabile

7. Un budget Azure al 100%:
   - B. genera una condizione di notifica, ma non costituisce un tetto automatico

8. Dove si gestisce normalmente la creazione di un utente cloud?
   - B. Microsoft Entra ID con ruolo appropriato


## Parte B — Risposte brevi

9. Spiega perché assegnare ruoli a un gruppo è spesso preferibile alle assegnazioni individuali.
    - Perchè in questa maniera le risorse all'interno del gruppo erediteranno i ruoli e in caso si voglia revocare quel ruolo a una risorsa precisa basta toglierla dal gruppo
10. Distingui ruolo Microsoft Entra e ruolo Azure con un esempio per ciascuno.
    - Un esempio di ruolo Microsoft Entra potrebbe essere la creazione di un gruppo nel tenant, invece un esempio di ruolo Azure può essere il Contributor.
11. Un utente ha Reader sul resource group e Contributor ereditato dalla sottoscrizione. Qual è l'accesso effettivo e perché?
    - L'accesso effettivo dell'utente a questo punto diventa Contributor perchè viene ereditato da uno scope più ampio, che è quello della sottoscrizione, e gerarchicamente il ruolo di Contributor supera quello di Reader.
12. Elenca in ordine almeno quattro controlli per diagnosticare `AuthorizationFailed`.
    - Controllare l'identità
    - Controllare lo scope
    - Controllare il Role 
    - Controllare se è presente un Lock sul Resource Group
13. Spiega la differenza tra tag `deleteAfter`, lock `CanNotDelete` e budget.
    - Il tag `deleteAfter` ha uno scopo descrittivo che indica dopo quanto andrebbe eliminata una risorsa
    - Il lock `CanNotDelete` serve ad impedire l'eliminazione di risorse all'interno di un determinato scope
    - Il budget serve ad impostare un limite di costo per una risorsa, che una volta superato invierà una notifica, non blocca automaticamente la risorsa o la spesa.


## Parte C — Caso situazionale

Un tecnico deve consultare una VNet in `rg-network-prod`. Riceve Contributor sull'intera sottoscrizione. Successivamente non riesce ad assegnare Reader a un collega e, tentando il cleanup, riceve `ScopeLocked`.

14. Individua almeno due scelte o interpretazioni errate.
    - La prima scelta errata è stata l'assegnazione del role tramite sottoscrizione, che ha uno scope troppo ampio per l'incarico del tecnico.
    - Secondo, il Role di Contributor non fornisce l'autorizzazione per assegnare o modificare i ruoli
15. Proponi il ruolo e lo scope iniziali più appropriati.
    - Il ruolo e lo scope più appropriati sono Reader e il Resource Group `rg-network-prod`
16. Spiega separatamente perché non riesce ad assegnare il ruolo e perché non riesce a eliminare lo scope.
    - L'assegnazione dei ruoli è permessa solo ad Owner o User Access Administrator
    - Sicuramente non riesce ad eliminare lo scope perchè è presente un Lock `CanNotDelete` sul Resource Group o sulla Subscription