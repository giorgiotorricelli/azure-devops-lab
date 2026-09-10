## Domande Concetti

1. Perché Azure Blob e Azure Files non sono intercambiabili?
    - Azure Blob è un sito web per i tuoi file.
    I file non stanno in una cartella del computer, ma hanno un link internet (URL). Il codice delle applicazioni o i siti li scaricano e li caricano tramite web. È l'opzione più economica per salvare foto, backup, video e log.

    - Azure Files è un disco di rete condiviso.
    Funziona esattamente come una cartella condivisa in ufficio (es. il disco Z:\). Lo puoi montare direttamente su una Macchina Virtuale o un PC, aprendo le cartelle e i file proprio come se fossero sul tuo computer.
2. Qual è la differenza tra management plane e data plane?
    - Il management plane è dove si definisce cosa le risorse devono fare e chi ha i permessi per farlo, gestite tramite Azure Resource Manager.
    - Il data plane è dove vengono eseguite le operazioni sui dati contenuti all'interno delle risorse.
3. Perché Contributor sullo storage account non implica accesso Blob con Entra ID?
    - Il ruolo di Contributor definisce la possibilità di lettura e modifica della risorsa, per l'accesso ai Blob servono ruoli specifici, per esempio Storage Blob Data Reader o Storage Blob Data Contributor.
4. Perché geo-ridondanza e backup risolvono problemi diversi?
    - La geo-ridondanza protegge contro guasti ambientali o malfunzionamenti in modo tale da poter accedere a una copia dei dati, ma non evita errori accidentali o manomissioni a differenza del backup che salva un versionamento preciso dei dati.
5. Quali fattori valuteresti prima di scegliere Archive?
    - Valuterei il tipo di dato, la data di creazione e update del dato ,la sua importanza e il costo di riattivazione e accesso al dato.
6. Perché una account key ha un impatto maggiore di una SAS limitata?
    - Perchè le SAS (Shared Access Signature) con account key concedono un accesso molto ampio, chi possiede una chiave può operare sui dati secondo le capacità dell'account. Una user delegation SAS è firmata usando credenziali Microsoft Entra quindi aggiunge un layer di protezione. 
7. Quali proprietà rendono una SAS coerente con il minimo privilegio?
    - permessi minimi
    - scope più ristretto possibile
    - scadenza breve
    - trasporto HTTPS
    - nessuna pubblicazione in repository o screenshot
8. Perché non possiamo verificare una policy lifecycle aspettando pochi minuti?
    - Perchè l'elaborazione può richiedere ore.