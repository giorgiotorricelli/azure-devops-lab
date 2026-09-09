**2**
## Percorso A

Ho verificato dall'interfaccia Azure che i tasti **New User** e **New Group** sono disponibili e abilitati

**3A**

Creato nuovo Gruppo **grp-cea-readers-fb14b9** e nuovo User con alias **cea-lab-fb14b9** che poi ho aggiunto nel gruppo

**4**

Creata assegnazione RBAC e verificato il role assignment tramite CLI, l'**output** generato è:

| Role | PrincipalType | Scope |
| :--- | :--- | :--- |
| Owner | User | `/subscriptions/9570c4c3-b6ad-44e0-93f6-516eb76bcd2e` |
| Reader | User | `/subscriptions/9570c4c3-b6ad-44e0-93f6-516eb76bcd2e/resourceGroups/rg-cea-identity-fb14b9` |
| Reader | Group | `/subscriptions/9570c4c3-b6ad-44e0-93f6-516eb76bcd2e/resourceGroups/rg-cea-identity-fb14b9` |

