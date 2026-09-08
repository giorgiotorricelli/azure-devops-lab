L'architettura creata per questo laboratorio risponde all'esigenza di isolare un ambiente di sviluppo temporaneo. 
- **Resource Group:** Utilizzato come confine logico del ciclo di vita. Tutte le risorse al suo interno condividono gli stessi tempi di vita e verranno eliminate contemporaneamente tramite una singola operazione.
- **Località:** Utilizzata la region `italynorth` per garantire la vicinanza geografica e il rispetto dei requisiti di latenza e conformità.
- **Differenza VNet vs Storage Account:** La VNet definisce lo spazio di rete privato e isolato (`10.30.0.0/16`) in cui posizionare le risorse di calcolo. Lo Storage Account fornisce uno spazio di archiviazione sicuro accessibile tramite endpoint web con protocolli cifrati.
- **Tag:** Applicati per garantire la tracciabilità, l'attribuzione dei costi e la gestione del ciclo di vita (`environment=dev`, `scenario=autonomous`, `deleteAfter`).
- **Dimostrazione Cleanup:** L'eliminazione completa dell'ambiente sarà confermata tramite il comando `az group exists` con esito `false`.


## Verifiche di Sicurezza e Rete

- **VNet e Subnet:** Indirizzo \`10.30.0.0/16\` e subnet \`snet-workload\` con prefisso \`10.30.10.0/24\` confermati.
- **Storage Account:** HTTPS obbligatorio (\`HttpsOnly=true\`), TLS 1.2 minimo (\`MinimumTls=TLS1_2\`) e accesso pubblico disabilitato (\`PublicBlobAccess=false\`).
- **Tag:** Presenti tutti e 5 i tag richiesti (\`course\`, \`unit\`, \`environment\`, \`scenario\`, \`deleteAfter\`).

## Autovalutazione

| Capacità | Valutazione |
|---|---|
| Traduco requisiti in nomi, tag e risorse | Completato |
| Verifico account e località prima della creazione | Completato |
| Interpreto le proprietà di VNet e storage | Completato |
| Confronto portale e CLI | Completato |
| Anonimizzo gli identificativi | Completato |
| Diagnostico una variabile o un nome errato | Completato |
| Verifico la conclusione del cleanup | Completato |