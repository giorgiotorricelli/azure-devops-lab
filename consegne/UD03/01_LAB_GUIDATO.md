**2**
## Percorso A

Ho verificato dall'interfaccia Azure che i tasti **New User** e **New Group** sono disponibili e abilitati

**3A**

Creato nuovo Gruppo **grp-cea-readers-fb14b9** e nuovo User con alias **cea-lab-fb14b9** che poi ho aggiunto nel gruppo

**4**

Creata assegnazione RBAC e verificato il role assignment tramite CLI, l'**output** generato è:

| Role | PrincipalType | Scope |
| :--- | :--- | :--- |
| Owner | User | `/subscriptions/<omitted>` |
| Reader | User | `/subscriptions/<omitted>/resourceGroups/rg-cea-identity-fb14b9` |
| Reader | Group | `/subscriptions/<omitted>/resourceGroups/rg-cea-identity-fb14b9` |

**5**

Analisi dei permessi per l'utente `cea-lab-fb14b9` sul Resource Group `rg-cea-identity-fb14b9`:

| Role | Description | Scope | Group Assignment | Condition |
| :--- | :--- | :--- | :--- | :--- |
| Reader | View all resources, but does not allow you to make any changes. | This resource | -- | None |
| Reader | View all resources, but does not allow you to make any changes. | This resource | `grp-cea-readers-fb14b9` | None |

Il principal è l'user, che ha ereditato il ruolo di Reader direttamente dal resource group, quindi dispone solo del permesso di lettura e non ha ereditato nessun ruolo dalla sottoscrizione.

**6**

Dall'analisi del costo non risultano costi riportati durante il mese corrente.

Crato nuovo budget con nome **budget-cea-fb14b9**, lo scope è quello del rg-cea-identity-fb14b9 (Resource group) con un importo di 60 euro e una soglia dell'80% del costo effettivo.

**7**

Creato Lock **lock-cea-delete** e verificata l'esistenza tramite CLI.

comando utilizzato:

 az lock list   --resource-group "$LAB_RG"   --query "[].{Name:name,Level:level,Notes:notes}"   --output table

Output generato:

| Name | Level | Notes |
| :--- | :--- | :--- |
| lock-cea-delete | CanNotDelete | CanNotDelete Lock for the resource group |

Il comando **az group delete --name "$LAB_RG" --yes** ha generato un errore come previsto perchè il lock impedisce l'eliminazione del gruppo.

Il comando **az group exists --name "$LAB_RG"** restituisce `true`

**8**

Evidenza anonimizzata:

```json
[
  {
    "PrincipalType": "User",
    "Role": "Owner",
    "Scope": "/subscriptions/<omitted>
  },
  {
    "PrincipalType": "User",
    "Role": "Reader",
    "Scope": "/subscriptions/<omitted>/resourceGroups/rg-cea-identity-fb14b9"
  },
  {
    "PrincipalType": "Group",
    "Role": "Reader",
    "Scope": "/subscriptions/<omitted>/resourceGroups/rg-cea-identity-fb14b9"
  }
]
