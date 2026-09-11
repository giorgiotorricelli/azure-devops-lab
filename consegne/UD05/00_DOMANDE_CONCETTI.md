# UD05 — Risposte alle domande di controllo

## 1. Perché due VNet da collegare non devono avere CIDR sovrapposti?
Due VNet da collegare tramite peering o VPN non devono avere CIDR sovrapposti perché gli indirizzi IP privati devono essere unici all'interno dell'intera rete interconnessa. In caso di sovrapposizione, i sistemi di routing non sarebbero in grado di determinare la corretta destinazione del traffico, rendendo impossibile l'instradamento dei pacchetti tra i due spazi IP.

## 2. Quale rete è più grande, `/24` o `/26`, e perché?
La rete `/24` è più grande della `/26`. Nel sistema di notazione CIDR IPv4, un numero più alto indica un maggior numero di bit riservati alla rete, lasciando meno bit disponibili per identificare gli host. 
* Una subnet `/24` lascia 8 bit per gli host, garantendo **256 indirizzi complessivi**.
* Una subnet `/26` lascia solo 6 bit per gli host, garantendo **64 indirizzi complessivi**.

## 3. Perché un public IP non garantisce raggiungibilità?
Un IP pubblico rende possibile l'esposizione di un endpoint verso l'esterno, ma la reale raggiungibilità dipende da una catena di controlli. La risorsa deve essere effettivamente raggiungibile tramite opportune regole nei Network Security Group (NSG) e tabelle di routing, il firewall o il sistema operativo interno devono consentire il flusso e, infine, ci deve essere un servizio attivo in ascolto sulla porta specificata.

## 4. Come viene scelta una regola NSG tra più corrispondenti?
Le regole di un NSG vengono valutate in ordine di **priorità**, da 100 a 4096. Il numero con il valore più basso indica la priorità più alta e viene valutato per primo. L'elaborazione del traffico termina immediatamente alla **prima corrispondenza** trovata; le regole con numero più alto o predefinite successive vengono ignorate.

## 5. Che cosa significa che un NSG è stateful?
Significa che quando un flusso di connessione viene autorizzato in una direzione (es. inbound), il traffico di risposta viene automaticamente consentito nella direzione opposta (outbound) senza la necessità di creare una regola speculare. Questo meccanismo di tracciamento dello stato non autorizza comunque una nuova connessione indipendente avviata nella direzione opposta.

## 6. Perché un `Allow` sulla NIC non supera un `Deny` applicabile sulla subnet?
Quando sia la subnet sia la NIC possiedono un NSG associato, il traffico deve essere esplicitamente consentito da **entrambi** i livelli per poter passare. Se l'NSG sulla subnet applica una regola di `Deny`, il traffico viene bloccato alla baseline di rete prima ancora di poter beneficiare dell'autorizzazione `Allow` definita a livello di singola interfaccia di rete (NIC).

## 7. Qual è la differenza tra DNS, routing e NSG?
Ognuno di questi componenti svolge un ruolo distinto nella catena di connettività:
* **DNS:** Si occupa esclusivamente della **risoluzione dei nomi**, traducendo un nome host (FQDN) nel relativo indirizzo IP. Non autorizza né instrada il traffico.
* **Routing:** Definisce la **strada (percorso)** che i pacchetti di dati devono seguire per raggiungere la destinazione specificata (tramite next hop).
* **NSG (Network Security Group):** Agisce da **firewall (filtro)**, consentendo o negando il traffico in base a regole di sicurezza (porte, origini, destinazioni e protocolli).

## 8. Perché IP Flow Verify verrà completato dopo la creazione della VM?
Strumenti di diagnostica come IP Flow Verify di Network Watcher verificano i flussi di traffico a partire da un endpoint di rete attivo (come la NIC di una Macchina Virtuale). Senza una VM creata ed esecutiva a cui agganciare il test di verifica reale del flusso, in questa fase è possibile consultare unicamente le regole ed i percorsi di routing effettivi in via preventiva.