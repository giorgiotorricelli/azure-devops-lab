## 1. Configurazione Variabili e Risorse

Le risorse per il laboratorio sono state create in ambiente di test con suffisso dinamico per garantire l'univocità:

- **Resource Group:** `rg-cea-storage-lab`
- **Storage Account:** `$LAB_STORAGE` (`Standard_LRS`, `StorageV2`, HTTPS-only, TLS 1.2, no public blob access)
- **Container:** `$LAB_CONTAINER` (`documents`, accesso privato)
- **Location:** `italynorth`

---

## 2. Configurazione Accessi e Ruoli IAM (Data Plane vs Management Plane)

Per consentire l'accesso al Data Plane senza l'uso di Shared Keys, è stato assegnato il ruolo RBAC nativo di Microsoft Entra ID a livello di Storage Account:

- **Ruolo Assegnato:** `Storage Blob Data Contributor`
- **Scope:** Storage Account (`/subscriptions/.../resourceGroups/.../providers/Microsoft.Storage/storageAccounts/...`)
- **Autenticazione CLI:** `--auth-mode login`

---

## 3. Operazioni di Upload, Download e Integrità Dati

### 3.1 Upload del Documento del Laboratorio
Il file locale `consegne/UD04/01_DOCUMENTO_LAB.txt` è stato caricato su Azure Blob Storage imponendo esplicitamente il nome `--name documento-lab.txt` per garantire la coerenza con le verifiche successive.

```bash
az storage blob upload \
  --account-name "$LAB_STORAGE" \
  --container-name "$LAB_CONTAINER" \
  --name documento-lab.txt \
  --file consegne/UD04/01_DOCUMENTO_LAB.txt \
  --auth-mode login \
  --overwrite \
  --output table