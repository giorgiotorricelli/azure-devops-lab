# UD07 — Verifica individuale: Soluzioni e Risposte

## Parte A — Scelta singola

1. **A. `--query`**
2. **B. tsv**
3. **B. oggetti**
4. **A. eventi del control plane della subscription**
5. **B. instradare segnali diagnostici verso destinazioni**
6. **B. KQL**
7. **B. notifiche e azioni associate agli alert**
8. **B. viene valutata, ma può non essere Fired**

---

## Parte B — Risposte brevi

### 9. Distingui Activity Log, Metrics e Logs.
* **Activity Log:** Traccia le operazioni del *control plane* (chi ha fatto cosa, quando e su quale risorsa) a livello di sottoscrizione.
* **Metrics:** Dati numerici legati al *data plane*, raccolti a intervalli regolari (es. utilizzo CPU, traffico di rete), ottimali per il monitoraggio in tempo reale.
* **Logs:** Dati di dettaglio strutturati o semi-strutturati (es. log applicativi, eventi di sistema) inviati a un workspace Log Analytics ed interrogabili tramite KQL per analisi approfondite.

### 10. Spiega l'idempotenza con un esempio amministrativo.
L'idempotenza è la proprietà di un'operazione che produce lo stesso risultato finale indipendentemente da quante volte viene eseguita. 

*Esempio:* Un script che verifica l'esistenza di un Resource Group: se non esiste lo crea, se esiste aggiorna solo i tag. Eseguire lo script 1 volta o 100 volte lascia il sistema sempre nello stesso stato desiderato senza generare errori o risorse duplicate.

### 11. Distingui `table` e `tsv`.
* **`table`:** Formato di output per Azure CLI orientato alla lettura humana, organizzato con righe, colonne e intestazioni visive.
* **`tsv` (Tab-Separated Values):** Formato di output privo di intestazioni e formattazione grafica, ideale per estrarre valori singoli ed assegnarli direttamente a variabili nei comandi o script Bash.

### 12. Spiega perché Log Analytics workspace e diagnostic setting non sono la stessa cosa.
* **Log Analytics Workspace:** È la *destinazione* e il contenitore centrale (con relativo motore di storage ed esecuzione KQL) dove i dati vengono memorizzati e analizzati.
* **Diagnostic Setting:** È la *regola di instradamento* (la "tubatura") configurata sulla singola risorsa per definire *quali* log e metriche inviare e *dove* (es. verso un Log Analytics Workspace, uno Storage Account o un Event Hub).

### 13. Distingui Alert Rule e Action Group.
* **Alert Rule:** Rileva l'anomalia. Definisce la condizione logica, lo scope, la metrica/query da valutare e la soglia di attivazione per determinare quando far scattare un allarme.
* **Action Group:** Gestisce la reazione. Definisce le azioni da intraprendere quando la regola di alert scatta (es. invio email/SMS, esecuzione di una Webhook, attivazione di un Azure Function o di un Runbook).

### 14. Perché correlazione temporale non implica causalità?
La semplice successione di due eventi nel tempo (evento A seguito dall'evento B) non garantisce che A sia la causa diretta di B. Potrebbero essere intervenuti fattori esterni concomitanti, processi schedulati indipendenti, ritardi nell'ingestione dei dati o semplici coincidenze. La causalità richiede una verifica tecnica basata su evidenze nei dati (es. log applicativi, tracciamento delle dipendenze).

---

## Parte C — Scenario

### 15. Quali fatti puoi affermare con certezza?
1. Alle **10:15** è stata inviata una richiesta di modifica a una risorsa Azure.
2. Alle **10:16** l'Activity Log ha registrato con successo l'operazione di `write` sul control plane.
3. Alle **10:20** il valore di una metrica specifica ha registrato un incremento.
4. Alle **10:25** una regola di alert configurata sulla risorsa ha riscontrato il superamento della soglia e si è attivata (stato *Fired*).

### 16. Quale ulteriore analisi è necessaria prima di affermare che la modifica delle 10:15 ha causato l'alert?
Prima di confermare il legame causale occorre:
* **Analizzare i parametri della modifica:** Verificare tramite l'Activity Log (payload JSON della `write`) quale proprietà specifica sia stata alterata alle 10:15.
* **Esaminare i log applicativi/di sistema (Log Analytics):** Interrogare i log dettagliati nell'intervallo 10:15–10:25 tramite KQL per identificare eventuali errori interni o cambi di comportamento correlati a quel cambio di configurazione.
* **Escludere carichi esterni ed eventi simultanei:** Verificare che l'aumento della metrica alle 10:20 non coincida con un picco improvviso di traffico utenti, un job batch programmato o un evento di sistema di terze parti del tutto indipendente dalla modifica manuale.