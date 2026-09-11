# UD05 — Report Laboratorio Guidato: Segmentazione, NSG e Verifica Preventiva

## 1. Piano di Indirizzamento

| Elemento | Nome Risorsa | CIDR | Indirizzi Totali | Indirizzi Usabili in Azure (-5) |
|---|---|---|---|---|
| **VNet** | `vnet-cea-<suffix>` | `10.50.0.0/16` | 65.536 | N/A |
| **Subnet Web** | `snet-web` | `10.50.10.0/24` | 256 | 251 |
| **Subnet Data** | `snet-data` | `10.50.20.0/24` | 256 | 251 |

---

## 2. Risorse Create ed Esecuzione CLI

### Creazione Resource Group e VNet
```bash
# Creazione Resource Group
az group create --name "$LAB_RG" --location "$LAB_LOCATION" \
  --tags course=cloud-engineer-academy unit=UD05 environment=lab --output table

# Creazione VNet e Subnet Web
az network vnet create \
  --resource-group "$LAB_RG" \
  --name "$LAB_VNET" \
  --location "$LAB_LOCATION" \
  --address-prefixes 10.50.0.0/16 \
  --subnet-name snet-web \
  --subnet-prefixes 10.50.10.0/24 \
  --output table

# Creazione Subnet Data
az network vnet subnet create \
  --resource-group "$LAB_RG" \
  --vnet-name "$LAB_VNET" \
  --name snet-data \
  --address-prefixes 10.50.20.0/24 \
  --output table