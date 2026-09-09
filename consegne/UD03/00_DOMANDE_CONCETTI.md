## Risposte motivate

1. Perché autenticazione riuscita e autorizzazione sufficiente non sono equivalenti?

    Perchè l'autenticazione verifica l'identità mentre l'autorizzazione valuta la possibilità di agire su una risorsa in un determinato scope.

2. Quale differenza operativa esiste tra ruolo Microsoft Entra e ruolo Azure?

    Il ruolo Microsoft Entra determina la gestione la gestione degli oggetti della directory come utenti, gruppi, domini e configurazioni del tenant. 
    Il ruolo Azure (o RBAC) autorizza la gestione delle risorse Azure, esempi comuni sono **Reader**, **Contributor** e **Owner**

3. Quali tre elementi formano una role assignment?
    Gli elementi che formano un role assignment sono: **principal**, **scope** e **definizione**.
    Principal è l'identità, lo scope stabilisce dove si applica l'assegnazione e la definizione invece il tipo di operazioni autorizzate.

4.  Perché Reader su un resource group è preferibile a       Contributor sulla sottoscrizione quando serve soltanto consultare quel progetto?

    Per il principio della `Zero Trust` e privilegio minimo, in modo tale da minimizzare gli errori e la potenziale superficie di attacco.


5. Perché un ruolo ereditato non si rimuove dalla risorsa figlia?

    Perchè per il principio dell'ereditarietà il ruolo va rimosso dalla risorsa padre,dato che tutte le risorse figlie ereditano i ruoli dalle risorse sovrastanti.

6. Un tag deleteAfter impedisce l'eliminazione? Motiva.
    No. I tag non determina le autorizzazioni ma hanno valore descrittivo

7. Che cosa cambia tra lock CanNotDelete e ruolo Reader?
    Il lock **CanNotDelete** protegge dall'eliminazione di un determinato scope mentre il ruolo Reader e un ruolo Azure che autorizza alla sola lettura delle risorse.

8. Perché un budget non è sufficiente a garantire che la spesa non superi una cifra?
    Il budget notifica il superamento di una spesa ma non può limitarla o bloccarla.

