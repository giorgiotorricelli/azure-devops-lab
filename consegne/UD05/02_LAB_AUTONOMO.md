# UD05 — Report Laboratorio Autonomo: Diagnosi di una Regola Incoerente

## 1. Inventario e Topologia della Rete

| Risorsa | Nome | CIDR / IP | Associazione / Note |
|---|---|---|---|
| **Resource Group** | `rg-cea-lab-ud05` | N/A | Contenitore risorse di laboratorio |
| **VNet** | `vnet-cea-lab` | `10.50.0.0/16` | Spazio di indirizzamento principale |
| **Subnet Web** | `snet-web` | `10.50.10.0/24` | Origine traffico web |
| **Subnet Data** | `snet-data` | `10.50.20.0/24` | Destinazione PostgreSQL (NSG Associato) |
| **NSG Data** | `nsg-data` | N/A | Applicato a livello di Subnet `snet-data` |
| **NIC Data** | `nic-data-01` | `10.50.20.4` | Scheda di rete su `snet-data` |
| **NIC Web** | `nic-web-01` | `10.50.10.4` | Scheda di rete su `snet-web` |

---

## 2. Iniettamento del Guasto e Valutazione Priorità

Per simulare l'incoerenza è stata creata la regola di blocco temporanea con la CLI:

```bash
az network nsg rule create \
  --resource-group "$LAB_RG" \
  --nsg-name "$LAB_NSG" \
  --name Deny-Web-Postgres-Auto \
  --priority 250 \
  --direction Inbound \
  --access Deny \
  --protocol Tcp \
  --source-address-prefixes 10.50.10.0/24 \
  --destination-address-prefixes '*' \
  --destination-port-ranges 5432