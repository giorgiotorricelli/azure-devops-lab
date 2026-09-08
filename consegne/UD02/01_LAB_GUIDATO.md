Durante la creazione dello Storage Account sul Portale si sono verificati dei bug con i tag. Tramite Azure CLI è stato invece possibile impostare i parametri di sicurezza e i tag richiesti con un comando:

az storage account create \
  --resource-group "$LAB_RG" \
  --name "$LAB_STORAGE" \
  --location italynorth \
  --sku Standard_LRS \
  --https-only true \
  --min-tls-version TLS1_2 \
  --allow-blob-public-access false \
  --tags course=cloud-engineer-academy unit=UD02 environment=lab deleteAfter=2026-09-10