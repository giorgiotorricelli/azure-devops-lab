Perché una macchina virtuale lascia al cliente più responsabilità operative rispetto ad App Service?
Qual è la differenza tra tenant, sottoscrizione e resource group?
Perché la località del resource group non obbliga tutte le risorse a usare la stessa region?
Quale differenza esiste tra una region e un'availability zone?
Perché az account show deve precedere la creazione di una risorsa?
Perché i tag non devono essere usati come meccanismo di sicurezza?
Quale vantaggio offre Azure CLI rispetto alla sola operazione nel portale?
Perché l'esecuzione del comando di eliminazione non dimostra da sola che il cleanup sia concluso?

1. Perché una macchina virtuale lascia al cliente più responsabilità operative rispetto ad App Service?
In una Macchina Virtuale (IaaS) il cliente gestisce il sistema operativo, le chiazze di sicurezza, l'installazione del software e la configurazione. In App Service (PaaS) Microsoft gestisce l'infrastruttura e l'OS, e il cliente deve preoccuparsi solo del codice dell'applicazione.

2. Qual è la differenza tra tenant, sottoscrizione e resource group?
- Tenant: È l'organizzazione (il contesto di identità Microsoft Entra/Active Directory).
- Sottoscrizione: È il confine amministrativo e di fatturazione (chi paga il conto).
- Resource Group: È il contenitore logico per raggruppare e gestire le risorse con lo stesso ciclo di vita.

3. Perché la località del resource group non obbliga tutte le risorse a usare la stessa region?
La località del Resource Group indica solo dove vengono salvati i suoi metadati (informazioni di gestione). Le risorse al suo interno possono risiedere in Region geografiche diverse in base alle esigenze del progetto.

4. Quale differenza esiste tra una region e un'availability zone?
- Region: Un'area geografica che contiene uno o più data center collegati tra loro.
- Availability Zone: Data center fisicamente separati e isolati (con alimentazione, rete e raffreddamento indipendenti) all'interno della stessa Region per garantire alta affidabilità.

5. Perché az account show deve precedere la creazione di una risorsa?
Serve a verificare su quale sottoscrizione attiva si è autenticati, evitando di creare risorse per errore nella sottoscrizione sbagliata o con l'account errato.

6. Perché i tag non devono essere usati come meccanismo di sicurezza?
I tag sono semplici etichette di testo usate per organizzare o tracciare i costi. Non applicano restrizioni di accesso o permessi; per la sicurezza e le autorizzazioni si usa RBAC (Role-Based Access Control).

7. Quale vantaggio offre Azure CLI rispetto alla sola operazione nel portale?
Permette di automatizzare, ripetere ed eseguire comandi in modo preciso e deterministico via script, riducendo gli errori manuali dell'interfaccia grafica.

8. Perché l'esecuzione del comando di eliminazione non dimostra da sola che il cleanup sia concluso?
Il comando di eliminazione invia solo la richiesta ad Azure. L'eliminazione reale avviene in background e richiede tempo; occorre verificare lo stato effettivo con comandi come `az group exists` fino a quando le risorse non sono state eliminate del tutto.