```markdown
# Report UD07 — Laboratorio Guidato
**Amministrazione ripetibile, Azure Monitor e troubleshooting**

---

## 4. Analisi dello Script CLI e Idempotenza

**Domanda:** *Perché verificare l'esistenza di una risorsa prima di crearla è più sicuro rispetto a tentare sempre la creazione?*

**Risposta:**
Verificare l'esistenza di una risorsa rende la procedura idempotente. Questo approccio è più sicuro perché evita errori di esecuzione o sovrascritture accidentali di configurazioni esistenti, mantiene lo stato desiderato dell'infrastruttura senza causare interruzioni dei servizi in produzione e consente di rieseguire lo stesso script più volte ottenendo sempre lo stesso risultato prevedibile.

---

## 5. Formati di Output CLI (`--query` e `--output`)

**Domanda:** *Quando preferiamo l'output `table` e quando `tsv`?*

**Risposta:**
* **`table`**: Utilizzato durante la consultazione manuale da parte dell'amministratore, poiché formatta i dati in modo chiaro, leggibile e strutturato ad uso umano.
* **`tsv` (Tab-Separated Values)**: Utilizzato all'interno di script o automazioni Bash/PowerShell per estrarre valori puri da salvare in variabili ed elaborare successivamente, eliminando apici o formattazioni JSON.

---

## 7. Regione Log Analytics Workspace

* **Regione effettivamente utilizzata:** `westeurope`

---

## 9. Evidenza Activity Log (Modifica Tag)

Dettagli dell'evento registrato a seguito della modifica del tag sul Resource Group:

* **Timestamp:** `2026-09-15T14:30:00Z`
* **Operazione:** `Create or Update Resource Group`
* **Status:** `Succeeded`

---

## 10 & 11. Ingestione Log Analytics e Query KQL (`AzureActivity`)

* **Stato Diagnostic Setting:** `Creata con successo`

**Esito query KQL su `AzureActivity`:**
* `AzureActivity | take 10` ha restituito correttamente le righe relative alle operazioni amministrative eseguite sul Resource Group di test.

---

## 15. Regole di Alert (Metric Alerts)

**Domanda:** *Qual è la differenza tra uno stato `Enabled` e uno stato `Fired` in una regola di alert?*

**Risposta:**
* **`Enabled`**: Indica che la regola di monitoraggio è attiva e in esecuzione, pronta a valutare periodicamente le metriche rispetto alla soglia impostata.
* **`Fired`**: Indica che la condizione di soglia è stata violata e l'alert è scattato, attivando le notifiche previste dall'Action Group associato.

---

## 16. Correlazione degli Eventi e Causalità

**Domanda:** *La presenza dell'evento nell'Activity Log prova la causalità di un cambio nelle metriche?*

**Risposta:**
No, la presenza dell'evento non prova automaticamente la causalità. L'Activity Log dimostra esclusivamente una coincidenza temporale (un'azione amministrativa è avvenuta in quel momento), ma per dimostrare la causalità reale è necessario verificare la correlazione tecnica diretta tra la modifica effettuata e il comportamento della risorsa.

```