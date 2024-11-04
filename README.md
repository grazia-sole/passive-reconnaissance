# Passive-Reconnaissance

Tre strumenti importanti per il riconoscimento passivo:

1. whois per interrogare i server WHOIS
```
whois

```

2. nslookup per interrogare i server DNS
```
nslookup

```
3. dig per interrogare i server DNS
```
dig

```
Usiamo **whois** per interrogare i record WHOIS, mentre usiamo **nslookup** e **dig** per interrogare i record del database DNS. 
Si tratta di record pubblicamente accessibili e, di conseguenza, non attivano alcun allarme sul bersaglio.


Il riconoscimento è una fase preliminare per raccogliere informazioni su un obiettivo ed è il primo passo nella Unified Kill Chain per ottenere un accesso iniziale a un sistema. Si suddivide in:

- **Riconoscimento Passivo**: Consiste nell’acquisire informazioni da risorse pubbliche senza interagire direttamente con l’obiettivo, ad esempio consultando i record DNS di un dominio o leggendo articoli di notizie sull'azienda.
- **Riconoscimento Attivo**: Richiede un’interazione diretta con l'obiettivo, come connettersi ai server dell'azienda (es. HTTP, FTP) o fare domande per ottenere informazioni (ingegneria sociale). Poiché è più invasivo, il riconoscimento attivo può comportare rischi legali senza autorizzazione appropriata.

  
**WHOIS** è un protocollo di richiesta e risposta che segue la specifica RFC 3912 e funziona sulla porta TCP 43. I registri dei domini sono responsabili della gestione dei record WHOIS per i domini che affittano. Il server WHOIS risponde con diverse informazioni sul dominio richiesto, tra cui:

- **Registrar**: Qual è il registrar che ha registrato il dominio?
- **Informazioni di contatto**: Nome, organizzazione, indirizzo, telefono (salvo protezione da servizi di privacy).
- **Date di creazione, aggiornamento e scadenza**: Quando è stato registrato, aggiornato e quando scade il dominio.
- **Name Server**: Quale server risolve il nome di dominio?

Per ottenere queste informazioni, si può usare un client WHOIS o un servizio online, ma un client locale (ad esempio su Linux) è spesso più veloce. Il comando è `whois NOME_DOMINIO`.

Eseguendo `whois tryhackme.com`, vediamo informazioni dettagliate: chi è il registrar (ad esempio namecheap.com), le date rilevanti, il registrante e i name server da interrogare per i record DNS.

Le informazioni raccolte possono rivelare nuove superfici di attacco per il social engineering o attacchi tecnici, come l’email dell’admin o i server DNS, se inclusi nel test di penetrazione.

Molti WHOIS oscurano le email per proteggerle dagli spammer, e i registranti possono attivare servizi di privacy per mantenere i dati riservati.

Per ottenere l’indirizzo IP di un dominio, possiamo usare il comando nslookup, che consente di eseguire ricerche sui server DNS. La sintassi del comando è:

```
nslookup [OPZIONI] NOME_DOMINIO [SERVER]
```
Dove:
- OPZIONI specifica il tipo di query, come "A" per indirizzi IPv4 e "AAAA" per indirizzi IPv6.
- NOME_DOMINIO è il dominio da cercare.
- SERVER è il server DNS da interrogare, come quelli pubblici di Cloudflare (1.1.1.1) o Google (8.8.8.8).

Esempi:
```
nslookup -type=A tryhackme.com 1.1.1.1

```
Il comando sopra restituisce gli indirizzi IPv4 di "tryhackme.com". 
```
nslookup -type=MX tryhackme.com
```
Il comando sopra restituisce i server email di "tryhackme.com".

Questo comando mostra che l'email per @tryhackme.com utilizza server di Google, come aspmx.l.google.com. Per mail server proprietari, questa informazione potrebbe essere rilevante per il penetration testing.

Per query DNS avanzate, si può usare dig (Domain Information Groper), che mostra anche informazioni aggiuntive come il TTL (Time To Live). La sintassi è:

```
dig @SERVER NOME_DOMINIO TIPO
```

Esempio:
```
dig @1.1.1.1 tryhackme.com MX
```



Strumenti di DNS lookup come **nslookup** e **dig** non riescono a individuare autonomamente i sottodomini. Tuttavia, i sottodomini di un dominio principale possono contenere molte informazioni rilevanti e a volte non sono aggiornati, rendendoli potenzialmente vulnerabili. Per esempio, un dominio come "tryhackme.com" potrebbe avere sottodomini come "wiki.tryhackme.com" e "webmail.tryhackme.com" che possono fornire accesso a informazioni critiche.

#### Tecniche per scoprire i sottodomini:
1. **Utilizzo dei motori di ricerca**: Consultare più motori di ricerca per ottenere una lista di sottodomini noti pubblicamente, anche se richiede tempo e analisi di numerosi risultati.
2. **Brute-forcing**: Generare e testare combinazioni di sottodomini per verificare la presenza di record DNS associati.

#### Utilizzo di servizi online:
Un metodo più rapido è l’utilizzo di servizi online come **DNSDumpster**. DNSDumpster raccoglie e mostra informazioni dettagliate sui DNS in tabelle e grafici, rivelando sottodomini e i relativi indirizzi IP, geolocalizzazione, server di posta (MX) e record TXT.

Esempio: effettuando una ricerca su DNSDumpster per "tryhackme.com", si possono individuare sottodomini come "blog.tryhackme.com" non visibili con query DNS standard. DNSDumpster organizza le informazioni graficamente e offre una funzione di esportazione beta per salvare il grafico.

Usando DNSDumpster o un servizio simile nel browser, è possibile ottenere e rappresentare visivamente tutte queste informazioni con una singola query.4


Link: [DNSDumpster](https://dnsdumpster.com)


**Shodan.io** è un motore di ricerca per dispositivi connessi a Internet, distinto dai motori di ricerca tradizionali che indicizzano pagine web. Si propone di fornire informazioni sui "dispositivi connessi", come server, router e altri dispositivi IoT, identificando i loro indirizzi IP e raccogliendo informazioni sui servizi in esecuzione. Shodan esegue la scansione di Internet per identificare i dispositivi disponibili pubblicamente, registrando dettagli come:

- **Indirizzo IP**
- **Azienda di hosting**
- **Posizione geografica**
- **Tipo e versione del server**

Queste informazioni possono essere utilizzate sia per la ricognizione passiva in un contesto di penetrazione, sia per la gestione della sicurezza in un contesto difensivo, consentendo agli utenti di monitorare i propri dispositivi esposti online.

Utilizzando Shodan, puoi cercare un dominio specifico, come *tryhackme.com*, per raccogliere dati sui server associati e sui dispositivi connessi. Shodan è particolarmente utile per scoprire vulnerabilità e configurazioni errate nei servizi esposti.

Puoi trovare ulteriori informazioni su Shodan e le sue funzionalità sul loro sito ufficiale: [Shodan.io](https://shodan.io).

