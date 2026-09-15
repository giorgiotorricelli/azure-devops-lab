# Configurazione variabili

```bash
RESOURCE_GROUP="rg-ud07-auto"
LOCATION="westeurope"
TAGS="ManagedBy=Autonomo UD=07"

echo "=== Verifica esistenza Resource Group: $RESOURCE_GROUP ==="
EXISTS=$(az group exists --name "$RESOURCE_GROUP")

if [ "$EXISTS" = "false" ]; then
    echo "Il Resource Group non esiste. Creazione in corso..."
    az group create \
        --name "$RESOURCE_GROUP" \
        --location "$LOCATION" \
        --tags $TAGS
else
    echo "Il Resource Group esiste già. Aggiornamento tag in corso..."
    az group update \
        --name "$RESOURCE_GROUP" \
        --set tags.ManagedBy=Autonomo tags.UD=07
fi

echo -e "\n=== Stato e proprietà del Resource Group ==="
az group show \
    --name "$RESOURCE_GROUP" \
    --query "{Name:name, Location:location, ProvisioningState:properties.provisioningState, Tags:tags}" \
    --output table

```

=== Verifica esistenza Resource Group: rg-ud07-auto ===
Il Resource Group non esiste. Creazione in corso...
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-ud07-auto",
  "location": "westeurope",
  "name": "rg-ud07-auto",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": {
    "ManagedBy": "Autonomo",
    "UD": "07"
  }
}

=== Stato e proprietà del Resource Group ===
Name          Location    ProvisioningState
------------  ----------  ------------------
rg-ud07-auto  westeurope  Succeeded

```shell

$ResourceGroupName = "rg-ud07-auto-ps"
$Location = "westeurope"
$Tags = @{
    "ManagedBy" = "Autonomo"
    "UD"        = "07"
}

Write-Host "=== Verifica esistenza Resource Group: $ResourceGroupName ===" -ForegroundColor Cyan
$rg = Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue

if ($null -eq $rg) {
    Write-Host "Resource Group non trovato. Creazione in corso..." -ForegroundColor Yellow
    $rg = New-AzResourceGroup -Name $ResourceGroupName -Location $Location -Tag $Tags
} else {
    Write-Host "Resource Group esistente. Aggiornamento tag..." -ForegroundColor Yellow
    $rg = Update-AzResourceGroup -Name $ResourceGroupName -Tag $Tags
}

Write-Host "`n=== Dettagli Resource Group ===" -ForegroundColor Green
$rg | Select-Object ResourceGroupName, Location, ProvisioningState, @{Name="Tags"; Expression={($_.Tags.GetEnumerator() | ForEach-Object { "$($_.Key)=$($_.Value)" }) -join "; "}} | Format-Table -AutoSize

```

=== Verifica esistenza Resource Group: rg-ud07-auto-ps ===
Resource Group non trovato. Creazione in corso...

=== Dettagli Resource Group ===

ResourceGroupName  Location   ProvisioningState Tags
-----------------  --------   ----------------- ----
rg-ud07-auto-ps    westeurope Succeeded         ManagedBy=Autonomo; UD=07

```bash

az monitor activity-log list \
  --resource-group rg-ud07-auto \
  --query "[0].{Operation:operationName.localizedValue, Status:status.localizedValue, Timestamp:eventTimestamp}" \
  --output table

```


Operazione -> Create or Update Resource Group
Status -> Succeeded
Timestamp -> 2026-09-15T15:10:45.120Z

=== KQL ===
Risposte ai quesiti:
1. Quante righe aggregate produce?
    - Produce 2 righe (una riga per lo stato "OK" e una per lo stato "WARN").

2. Quale stato ha latenza media più alta?
    - Lo stato WARN ha la latenza media più alta (470 ms, derivata dalla media tra 430 ms e 510 ms, contro i 150 ms di OK).

3. Perché summarize cambia la granularità dei dati?
    - L'operatore summarize raggruppa le righe di input in base alle chiavi specificate nella clausola by (in questo caso Status). Aggrega i record individuali applicando funzioni di aggregazione (count(), avg()), trasformando i dati da un livello di granularità puntuale (singole richieste) a un livello sintetico/macro (metriche aggregate per stato).


=== Metrics ===

```bash

az monitor metrics list-definitions \
  --resource /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-ud07-main/providers/Microsoft.Storage/storageAccounts/saud07main \
  --query "[].{Name:name.value, Unit:unit, PrimaryAggregation:primaryAggregationType}" \
  --output table

```

=== Metrica Selezionata ===

Metrica: UsedCapacity

Unità: Bytes

Aggregazione primaria: Average


=== Lettura della Metrica ===

```bash

az monitor metrics list \
  --resource /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-ud07-main/providers/Microsoft.Storage/storageAccounts/saud07main \
  --metric "UsedCapacity" \
  --interval PT1H

```

**esito**: Non sono presenti dati registrati nell'intervallo temporale selezionato (Timespan privo di metric data points). Come da direttiva, la metrica viene documentata nel suo stato attuale senza effettuare variazioni o tentativi alternativi.


=== Analisi Alert ===

Configurazione Regola Alert

Scope: Subscription /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-ud07-main

Condition: Percentage CPU > 80% per un periodo di 5 minuti

Severity: 2 (Warning)

Evaluation Frequency: 1 minuto

Action Group: Assente (Nessuna notifica automatica configurata)

Spiegazione Differenza: alert rule configurata vs alert fired
Una Alert Rule configurata rappresenta unicamente la definizione logica di un monitoraggio (definizione di scope, metriche, soglie e frequenza di valutazione).

Un Alert fired rappresenta l'evento dinamico in cui la condizione definita nella regola viene effettivamente soddisfatta dalle metriche/log raccolti durante l'intervallo di valutazione. Pertanto, la sola presenza di una regola non implica l'esistenza di uno stato di anomalia attivo.

=== Guasto Amministrativo Controllato ===

```bash

az group show \
  --name rg-ud07-NON-ESISTE \
  --output table

  ```
Output / Errore

(ResourceGroupNotFound) Resource group 'rg-ud07-NON-ESISTE' could not be found.
Code: ResourceGroupNotFound
Message: Resource group 'rg-ud07-NON-ESISTE' could not be found.


Scheda di Troubleshooting
Sintomo: Il comando CLI restituisce codice di errore di uscita non-zero e un messaggio di errore esplicito.

Tipo di errore: Client/API Error (ResourceGroupNotFound, HTTP Status 404).

Ipotesi: Il nome del Resource Group fornito non è corretto, è stato digitato errato o la risorsa si trova in un'altra Subscription / non è mai stata creata.

Controllo: Esecuzione di az group list --query "[].name" per elencare i Resource Group effettivamente esistenti nella sottoscrizione corrente.

Correzione: Rettificare il parametro --name nello script o nel comando CLI inserendo il nome corretto di un Resource Group esistente (es. rg-ud07-auto).

Verifica: Rieseguire az group show --name rg-ud07-auto --output table e confermare l'esito positivo.


RUNBOOK — Risorsa Azure non trovata o contesto errato
1. Sintomo
    - L'utente o la pipeline di deployment riceve l'errore ResourceGroupNotFound o ResourceNotFound durante l'esecuzione di comandi Azure CLI / PowerShell o deployment ARM/Bicep.

=== Controllo account/subscription ===

```bash

az account show --query "{Account:user.name, SubscriptionId:id, SubscriptionName:name}" --output table

```

Se necessario, impostare la sottoscrizione corretta:

```bash
az account set --subscription <SUBSCRIPTION_ID>

```


Controllo nome RG
Elencare tutti i Resource Group disponibili per escludere errori di sintassi (typo):

```bash
az group list --query "[].name" --output table

```
Controllo resource ID/name
Verificare l'ID completo della risorsa target per accertarsi che rispetti la struttura standard Azure:
/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/<provider>/<resource-type>/<resource-name>

Activity Log
Interrogare l'Activity Log per verificare se il Resource Group o la risorsa sono stati eliminati di recente:

```bash
az monitor activity-log list \
  --offset 1d \
  --query "[?contains(operationName.localizedValue, 'Delete')].{Resource:resourceGroupName, Operation:operationName.localizedValue, Status:status.localizedValue, Time:eventTimestamp}" \
  --output table

```
Interpretazione
Se la risorsa è visibile in az group list, il problema è legato ad un errore sintattico nel comando CLI originale.

Se la risorsa appare cancellata negli Activity Log, si tratta di una cancellazione intenzionale o accidentale.

Se la sottoscrizione attiva era errata, il problema era esclusivamente di contesto d'esecuzione.

Correzione minima
Ripristinare la variabile d'ambiente o il parametro di input con il nome corretto.

Oppure correggere il contesto della subscription attiva con az account set.

Verifica finale
Eseguire una query di test per confermare la risoluzione:

```bash
az group show --name <NOME_RG_CORRETTO> --query "properties.provisioningState" --output tsv

```
Cleanup
Rimuovere eventuali file di log temporanei o script di test creati durante la fase di diagnosi.

Attività 9 — Cleanup Autonomo
Comandi di Eliminazione
```bash
# Eliminazione del Resource Group Bash
az group delete --name rg-ud07-auto --yes --no-wait

# Eliminazione del Resource Group PowerShell
az group delete --name rg-ud07-auto-ps --yes --no-wait
```
Verifica dell'Avvenuta Eliminazione
```bash
az group exists --name rg-ud07-auto
az group exists --name rg-ud07-auto-ps
```

Output di Risposta:

false
false