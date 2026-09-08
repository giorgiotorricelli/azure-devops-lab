## Parte A — Scelte operative

1. **B** (IaaS — offre pieno controllo su sistema operativo e componenti personalizzati)
2. **C** (Configurazione dell'applicazione, identità, accessi e dati — il resto è gestito da Azure)
3. **C** (È un contenitore logico per risorse che possono condividere ciclo di vita e governance)
4. **B** (La località indica dove Azure conserva i metadati del resource group; le risorse possono avere località proprie)
5. **B** (`az account show` — per verificare l'account e la sottoscrizione attualmente attiva)
6. **C** (`stcea02a7f9` — lo storage account richiede 3-24 caratteri alfanumerici esclusivamente minuscoli e senza trattini)
7. **B** (Il tag documenta l'intenzione, ma serve ancora una procedura o una policy che esegua l'eliminazione)
8. **C** (`az group exists --name <NOME>` restituisce `false`)

---

## Parte B — Risposte brevi

### 9. Differenza tra cloud pubblico, privato e ibrido
In un'azienda finanziaria: i dati pubblici/sito web girano su **Cloud Pubblico** (scalabilità); i database con dati bancari sensibili risiedono nel **Cloud Privato** (data center proprietario per conformità); l'infrastruttura **Ibrida** connette in modo sicuro i due mondi per elaborare report di analisi.

### 10. Relazione fra Tenant, Sottoscrizione, Resource Group e Risorsa
- **Tenant:** L'organizzazione principale (l'identità aziendale su Microsoft Entra ID).
- **Sottoscrizione:** Il contenitore amministrativo e di fatturazione associato al Tenant.
- **Resource Group:** Il contenitore logico all'interno della Sottoscrizione per gestire il ciclo di vita di risorse correlate.
- **Risorsa:** L'elemento individuale d'infrastruttura (es. VNet, Storage Account, VM) posizionato dentro un Resource Group.

### 11. Perché una Availability Zone non è sinonimo di Region
Una **Region** è un'area geografica contenente uno o più data center. Una **Availability Zone** è un singolo data center (o gruppo di data center) fisicamente isolato e indipendente all'interno di quella Region (con alimentazione, rete e raffreddamento separati) per garantire tolleranza ai guasti.

### 12. Differenza operativa tra Azure Portal e Azure CLI
- **Azure Portal (GUI):** Ideale per un'esplorazione visiva, monitoraggio manuale e comprensione dell'architettura.
- **Azure CLI:** Strumento deterministico e veloce per automazione, scripting e ripetibilità senza dipendere dai cambiamenti visivi dell'interfaccia.

### 13. Perché i tag non si ereditano automaticamente dal Resource Group
Azure non applica l'ereditarietà automatica dei tag dal Resource Group alle risorse figlie. Ciascuna risorsa deve avere i propri tag assegnati esplicitamente per permettere filtri di ricerca individuali e report granulari di costo/gestione.

---

## Parte C — Interpretazione tecnica

1. **Analisi dell'ID:**
   - **Sottoscrizione:** `<omitted>` (anonimizzata)
   - **Resource Group:** `rg-cea-test`
   - **Provider:** `Microsoft.Storage`
   - **Tipo risorsa:** `storageAccounts`
   - **Nome risorsa:** `stceatest01`
2. **Località e Tag:**
   - **Località:** `italynorth`
   - **Tag:** `environment: lab`, `unit: UD02`
3. **Comando di verifica appartenenza:**
   ```bash
   az resource list --resource-group "rg-cea-test" --query "[?name=='stceatest01']" --output jsonc