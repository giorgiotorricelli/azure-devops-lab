# Verifica — Modelli cloud e struttura iniziale di Azure

## Parte A — Scelte operative

1. **B** (IaaS — offre il controllo totale del sistema operativo)
2. **C** (Configurazione dell'applicazione, identità, accessi e dati)
3. **C** (È un contenitore logico per risorse che condividono ciclo di vita)
4. **B** (Indica dove si conservano i metadati; le risorse possono avere altre località)
5. **B** (`az account show` — per verificare il contesto prima di creare)
6. **C** (`stcea02a7f9` — minuscole e numeri senza caratteri speciali)
7. **B** (Documenta l'intenzione, serve un'azione/policy per eliminare)
8. **C** (`az group exists --name <NOME>` restituisce `false`)

---

## Parte B — Risposte brevi

### 9. Differenza tra cloud pubblico, privato e ibrido
Pubblico: risorse condivise gestite da Azure. Privato: data center dedicato all'azienda. Ibrido: connette pubblico e privato per bilanciare sicurezza e scalabilità.

### 10. Relazione fra Tenant, Sottoscrizione, Resource Group e Risorsa
Il Tenant è l'organizzazione (identità Entra ID). La Sottoscrizione è l'account di fatturazione. Il Resource Group è il contenitore logico. La Risorsa è il singolo servizio (es. VNet o Storage).

### 11. Perché una Availability Zone non è sinonimo di Region
Una Region è un'area geografica globale. Una Availability Zone è un data center fisico isolato (con rete ed energia indipendenti) all'interno di quella Region per l'alta affidabilità.

### 12. Differenza operativa tra Azure Portal e Azure CLI
Il Portale serve per l'esplorazione visiva manuale; la CLI serve per automazione, scripting e ripetibilità senza dipendere dall'interfaccia grafica.

### 13. Perché i tag non si ereditano automaticamente dal Resource Group
Non si ereditano per consentire una gestione flessibile e un tracciamento dei costi mirato risorsa per risorsa.

---

## Parte C — Interpretazione tecnica

1. **Analisi ID:** Sottoscrizione anonimizzata, Resource Group `rg-cea-test`, Provider `Microsoft.Storage`, Tipo `storageAccounts`, Nome `stceatest01`.
2. **Località e Tag:** Località `italynorth`. Tag: `environment=lab`, `unit=UD02`.
3. **Comando di verifica appartenenza:** `az resource list --resource-group "rg-cea-test" --output table`
4. **Operazione di rimozione:** `az group delete --name "rg-cea-test" --yes --no-wait`
5. **Verifica post-eliminazione:** `az group exists --name "rg-cea-test"` (esito atteso: `false`).