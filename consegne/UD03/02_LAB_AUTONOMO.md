**check dei role assignments**

| Role | PrincipalType | Scope |
| :--- | :--- | :--- |
| Owner | User | `/subscriptions/<omitted>` |
| Reader | User | `/subscriptions/<omitted>/resourceGroups/rg-cea-identity-fb14b9` |
| Reader | Group | `/subscriptions/<omitted>/resourceGroups/rg-cea-identity-fb14b9` |
| Cost Management Reader | Group | `/subscriptions/<omitted>/resourceGroups/rg-cea-identity-fb14b9` |

Come principal del team scelgo Group perchè un team è di solito composto da più membri e grazie all'ereditarietà tutte le risorse figlie otterranno il ruolo; io ho scelto il ruolo di Cost Management Reader perchè è il ruolo con minimo privilegio disponibile per la lettura dei costi della risorsa


Analisi dei permessi per il gruppo `grp-cea-finops-fb14b9` sul Resource Group `rg-cea-identity-fb14b9`:

| Role | Description | Scope | Group Assignment | Condition |
| :--- | :--- | :--- | :--- | :--- |
| Cost Management Reader
 | Can view cost data and configuration | This resource | -- | None |

 L'assegnazione è diretta per tutte le risorse nella tabella precedente tranne che per Owner che la eredita direttamente dalla subscription.

 L'assegnazione Reader temporanea non riduce eventuali privilegi più ampi già posseduti perchè si trova al disotto della gerarchia dei ruoli.


 Il budget è configurato con nome **budget-cea-fb14b9**, lo scope è quello del rg-cea-identity-fb14b9 (Resource group) con un importo di 60 euro e una soglia dell'80% del costo effettivo.

 Il comando **az group delete --name "$LAB_RG" --yes** ha dato errore come previsto perchè il lock blocca l'eliminazione e **az group show --name "$LAB_RG" --output table** restituisce correttamente l'output:


 | Location | Name |
| :--- | :--- |
| italynorth| rg-cea-identity-fb14b9 |


La distinzione tra autenticazione, autorizzazione e lock:

L'autenticazione definisce l'identità, l'autorizzazione definisce la capacità di eseguire un operazione e il lock invece impedisce un operazione (come l'eliminazione) su una risorsa in un determinato scope.

Il cleanup è stato effettuato e **az group exists --name "$LAB_RG"** restituisce false.