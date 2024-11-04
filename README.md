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




