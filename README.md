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

## WHOIS
**WHOIS** è un protocollo di richiesta e risposta che segue la specifica RFC 3912 e funziona sulla porta TCP 43. I registri dei domini sono responsabili della gestione dei record WHOIS per i domini che affittano. Il server WHOIS risponde con diverse informazioni sul dominio richiesto, tra cui:

- **Registrar**: Qual è il registrar che ha registrato il dominio?
- **Informazioni di contatto**: Nome, organizzazione, indirizzo, telefono (salvo protezione da servizi di privacy).
- **Date di creazione, aggiornamento e scadenza**: Quando è stato registrato, aggiornato e quando scade il dominio.
- **Name Server**: Quale server risolve il nome di dominio?

Per ottenere queste informazioni, si può usare un client WHOIS o un servizio online, ma un client locale (ad esempio su Linux) è spesso più veloce. Il comando è `whois NOME_DOMINIO`.
```
whois NOME_DOMINIO
```

Eseguendo `whois tryhackme.com`, vediamo informazioni dettagliate: chi è il registrar (ad esempio namecheap.com), le date rilevanti, il registrante e i name server da interrogare per i record DNS.

Le informazioni raccolte possono rivelare nuove superfici di attacco per il social engineering o attacchi tecnici, come l’email dell’admin o i server DNS, se inclusi nel test di penetrazione.

Molti WHOIS oscurano le email per proteggerle dagli spammer, e i registranti possono attivare servizi di privacy per mantenere i dati riservati.

## nslookup & dig

Per ottenere l’indirizzo IP di un dominio, possiamo usare il comando nslookup, che consente di eseguire ricerche sui server DNS. La sintassi del comando è:

```
nslookup [OPZIONI] NOME_DOMINIO [SERVER]
```
Dove:
- OPZIONI specifica il tipo di query, come "A" per indirizzi IPv4 e "AAAA" per indirizzi IPv6.
- NOME_DOMINIO è il dominio da cercare.
- SERVER è il server DNS da interrogare, come quelli pubblici di Cloudflare (1.1.1.1) o Google (8.8.8.8).

  ![image](https://github.com/user-attachments/assets/d3ab4f76-37ef-4f95-8500-e0638e2e1b2c)


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
## Altre tecniche di ricognizione passiva

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


## Nginx vs. Apache
Nginx e Apache sono due dei server web più popolari e utilizzati al mondo.

### 1. Architettura
- **Nginx**: Asincrono e basato su eventi, gestisce molte connessioni con basso consumo di risorse (più veloce e leggero).
- **Apache**: Basato su processi e thread, può consumare più risorse con carichi elevati.

### 2. Performance
- **Nginx**: Eccellente per contenuti statici e carichi pesanti, ottimo per applicazioni ad alto traffico.
- **Apache**: Buono, ma meno efficiente di Nginx con connessioni simultanee.

### 3. Configurazione e Flessibilità
- **Nginx**: Configurazione semplice e chiara, ideale per reverse proxy.
- **Apache**: Estremamente flessibile con molti moduli, ma può risultare complesso.

### 4. Sicurezza
- **Nginx**: Misure di sicurezza solide e supporto per SSL/TLS.
- **Apache**: Buone funzionalità di sicurezza, ma la configurazione può portare a vulnerabilità.

### 5. Utilizzo
- **Nginx**: Ideale come server front-end, reverse proxy e per contenuti statici.
- **Apache**: Adatto a una vasta gamma di applicazioni web e configurazioni complesse.

### Conclusione
- **Nginx** è preferito per efficienza e prestazioni in ambienti ad alto traffico (open source).
- **Apache** è scelto per progetti con ampie esigenze di scripting e configurazione. Spesso usati insieme.

##Esercizio 05/11
#Domande finali

### 1. Confronto tra `nslookup`, `dig`, e `whois`

#### **nslookup**
- **Descrizione**: È uno strumento di diagnostica DNS che consente di interrogare i server DNS per ottenere informazioni sui nomi di dominio, come indirizzi IP, record MX, record NS, ecc.
- **Utilizzo**: Semplice e utile per la risoluzione di nomi di dominio e per la verifica delle configurazioni DNS.
- **Quando usarlo**: Quando hai bisogno di un'interfaccia user-friendly per interrogare i record DNS, soprattutto in ambienti Windows.

#### **dig**
- **Descrizione**: "Domain Information Groper" è uno strumento più potente e flessibile rispetto a `nslookup`. È specificamente progettato per interrogare il DNS e fornisce output dettagliato.
- **Utilizzo**: Consente di fare query DNS più complesse e offre informazioni più dettagliate rispetto a `nslookup`.
- **Quando usarlo**: Quando hai bisogno di maggiori dettagli o vuoi eseguire query avanzate, come specificare il tipo di record (A, MX, NS, etc.) o interrogare server DNS specifici.

#### **whois**
- **Descrizione**: È un protocollo di rete utilizzato per ottenere informazioni sui registranti di nomi di dominio e indirizzi IP, come il proprietario del dominio, i dati di registrazione e le date di scadenza.
- **Utilizzo**: Utile per raccogliere informazioni su chi possiede un dominio, per verificare la disponibilità di un dominio e per capire la gestione dei domini.
- **Quando usarlo**: Quando hai bisogno di informazioni sui registranti di un dominio, come contattare il proprietario di un dominio o verificare la legittimità di un dominio.

### 2. Privacy e Sicurezza

#### **Rischi di Sicurezza**
- **Esposto a attacchi**: La disponibilità delle informazioni sui domini può rendere più facili gli attacchi mirati, come il phishing o gli attacchi DDoS, poiché gli aggressori possono identificare i punti deboli dell'infrastruttura.
- **Compromissione dei dati**: Le informazioni sugli indirizzi IP e sui server DNS possono essere utilizzate per compromettere la rete di un'organizzazione o per attaccare direttamente i servizi esposti.

#### **Rischi di Privacy**
- **Informazioni personali**: Il comando `whois` può rivelare informazioni personali sui registranti, come nome, indirizzo e numero di telefono, che possono essere utilizzate per scopi malevoli o per stalking.
- **Profilazione**: Le informazioni disponibili sui domini possono essere utilizzate per profilare le aziende e gli individui, consentendo attacchi mirati basati sulle loro attività online e sulla loro infrastruttura.

### Mitigazione dei Rischi
- **Privacy DNS**: Utilizzare servizi di registrazione di domini che offrono la protezione della privacy, oscurando le informazioni personali del registrante.
- **Sicurezza informatica**: Implementare misure di sicurezza appropriate, come firewall, protezione DDoS e sistemi di rilevamento delle intrusioni, per proteggere le infrastrutture di rete.
- **Limitazione delle informazioni pubbliche**: Limitare le informazioni visibili attraverso le query `whois` e considerare di non rendere pubbliche le informazioni critiche sui domini.

In sintesi, mentre `nslookup`, `dig` e `whois` hanno scopi diversi, è importante utilizzare lo strumento giusto in base alle tue esigenze specifiche e essere consapevoli dei rischi di sicurezza e privacy associati alla visibilità delle informazioni sui domini.
