# UD05 — Risposte alle domande di ripasso (Fondamenti di Rete)

## 1. Qual è la differenza tra switch e router?
Lo **switch** lavora a Livello 2 (Data Link) e usa gli indirizzi **MAC** per smistare il traffico all'interno della *stessa* rete locale (LAN). Il **router** lavora a Livello 3 (Network) e usa gli indirizzi **IP** e le tabelle di routing per collegare e far comunicare *reti diverse*.

## 2. Qual è la differenza tra MAC e IP?
L'**indirizzo MAC** è un identificatore fisico e univoco assegnato alla scheda di rete (es. `00-1A-2B-...`) usato per la comunicazione locale nella LAN. L'**indirizzo IP** è un indirizzo logico (es. `192.168.1.25`) assegnato alla macchina per essere identificata e instradata all'interno di una rete IP o su Internet.

## 3. Che cosa significa `/24`?
Indica che i primi **24 bit** dell'indirizzo IP identificano la **rete**, lasciando i restanti 8 bit per gli host. Rappresenta un blocco di **256 indirizzi IPv4 totali** (con subnet mask `255.255.255.0`).

## 4. Che cos'è il default gateway?
È l'indirizzo IP del router di riferimento a cui un host invia tutto il traffico destinato a reti esterne alla propria subnet locale (ad esempio, il traffico diretto verso Internet).

## 5. A cosa serve DHCP?
Il **DHCP** è un servizio che assegna automaticamente ai dispositivi che si collegano alla rete i parametri di configurazione necessari: Indirizzo IP, Subnet Mask, Default Gateway e Server DNS.

## 6. A cosa serve DNS?
Il **DNS** è il sistema che traduce i nomi di dominio leggibili dall'uomo (es. `www.microsoft.com`) nei corrispondenti indirizzi IP numerici (es. `20.x.x.x`) utilizzati dai computer per comunicare.

## 7. Qual è la differenza tra TCP e UDP?
* **TCP:** È orientato alla connessione, garantisce l'ordine, l'affidabilità e la consegna dei dati (con ritrasmissione in caso di errore).
* **UDP:** È non orientato alla connessione, non garantisce la consegna ma è molto più veloce e leggero (usato per streaming, VoIP, DNS).

## 8. Che cos'è una porta?
La **porta** è un numero (da 0 a 65535) che identifica uno specifico processo o servizio applicativo in ascolto su un host (es. porta 80/443 per il traffico Web, porta 22 per SSH).

## 9. Che cosa fa una route?
Una **route** è una regola all'interno di una tabella di routing che indica al router verso quale "prossimo balzo" (*next hop*) o interfaccia inviare i pacchetti destinati a uno specifico intervallo di IP.

## 10. Che cosa fa un firewall?
Un **firewall** analizza il traffico di rete e applica regole di sicurezza (Allow/Deny) per consentire o bloccare i flussi di dati in base a IP, porte, protocolli o stato della connessione.

## 11. Che cos'è una VLAN?
Una **VLAN** (Virtual LAN) è una suddivisione logica di una rete fisica a Livello 2 (Ethernet) gestita dagli switch. Permette di separare il traffico di diversi gruppi di utenti sullo stesso hardware.

## 12. Perché VLAN e VNet non sono la stessa cosa?
* La **VLAN** è una tecnologia fisica/tradizionale di Livello 2 basata su switch e protocollo 802.1Q.
* La **VNet** di Azure è una rete virtuale software-defined (SDN) di Livello 3 completa, che include supporto nativo a subnet IP, tabelle di routing, DNS e firewall (NSG).

## 13. Che cosa fa NAT?
Il **NAT** (Network Address Translation) modifica gli indirizzi IP nei pacchetti in transito. Tipicamente traduce gli IP privati di una LAN aziendale o domestica in un unico IP pubblico per permettere l'accesso a Internet.

## 14. Che cosa rappresenta una DMZ?
La **DMZ** (Zona Demilitarizzata) è una sottorete isolata e controllata da firewall, destinata a ospitare i servizi che devono essere esposti all'esterno (es. server Web), separandoli e proteggendo la rete interna privata.

## 15. Qual è lo scopo di una VPN?
La **VPN** (Virtual Private Network) crea un tunnel cifrato e sicuro attraverso una rete pubblica non fidata (Internet) per collegare in modo sicuro due sedi (Site-to-Site) o un utente remoto alla rete aziendale (Point-to-Site).

## 16. Che informazioni fornisce `ipconfig /all`?
Fornisce il dettaglio completo della configurazione di rete di tutte le interfacce sul PC: Indirizzo IP, Subnet Mask, Default Gateway, indirizzo MAC (Physical Address), stato e server DHCP, e server DNS configurati.

## 17. Che cosa verifica `nslookup`?
Verifica il funzionamento del servizio **DNS**: invia una query a un server DNS per verificare se un determinato nome host viene risolto nel suo indirizzo IP (e viceversa).

## 18. Perché un DNS funzionante non garantisce la connettività applicativa?
Perché il DNS si occupa esclusivamente di **tradurre il nome in IP**. Se la risoluzione ha successo ma ci sono blocchi a livello di routing, regole del firewall (NSG) che chiudono le porte, o se il servizio sulla macchina di destinazione è spento, la connessione applicativa fallirà comunque.