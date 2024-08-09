# Aplikativni nivo

**Protokol aplikativnog nivao definise** - cisto da si znas
- Tipove poruka koje se razmenjuju npr. zahtevi  i odgovori
- Sintaksu tj. format poruka ( koja polja postoje u formatu )
- Semantiku ( znacenje ) polja u poruci
**Public-domain protokoli**
- Definisani u RFC _Request  for comments_
- omogucavaju interoperabilnost ( sta ti to sad znaci jebo mater )
	- Interoperabilnost - Karakteristika proizvoda/sistema da radi sa drugim sistemima.
- Primer protokola : HTTP , SMTP 
### Client Server _paradigma_

**Klijent**
- inicira kontakt sa serverom 
- zahteva neku uslugu od servera 
- Web: klijent implementiran u browser-u

**Server**
- pruza zahtevanu uslugu klijentu
- npr. Web server salje zahtevanu Web stranicu, mail server isporucuje mail

**Ovo fino saaad**

| Application               | Application layer protocol | Underlying transport protocol |
| ------------------------- | -------------------------- | ----------------------------- |
| E-mail                    | SMTP                       | TCP                           |
| Remote terminal<br>access | Telnet/ mozda SSH          | TCP                           |
| WEB                       | HTTP                       | TCP                           |
| File Transfer             | FTP                        | TCP or UDP                    |
|                           |                            |                               |
## DNS
- Distribuirana baza podataka implementirana hijerarhijski u name serverima
- Na transportnom nivou DNS koristi UDP ( moze i TCP ) i port 53 
**Funkcija**
- Daje ime Ip adresi, umesto da kucas 192.168.0.13, ti postavis zivko-pc
- Mozes da simuliras rad DNS-a menjajuci /etc/hosts.
**Kako radi**
- Aplikativni program radi preslikavanje imena host-a u IP adresu koristeci funkciju RESOLVER
- RESOLVER salje UDP paket lokalnom DNS serveru koji pretrazuje svoju bazu, pronalazi preslikavanje i vraca IP adresu RESOLVER-u, koji je vraca aplikativnom programu
	- Kada se dobije IP adresa, moze se otvoriti TCP ( UDP ) konekcija sa server procesom lociranim na serveru cija je ip adresa dobijena

### Zone 
- DNS prostor je podeljen na nepreklapajuce zone, svaka zona sadrzi neki deo stabla i name server koji sadrzi autorizovane informacije o toj zoni na svom disku
- ni jedan server ne sadrzi sva preslikavanja imena u IP-adrese

## Name serveri
**Tipovi name servera**
- Lokalni name serveri
- Root
- Serveri vrsnih domena ( Top level Domain - TLD)
- Autorizovani name serveri
_Ovi serveri saradjuju medjusobno da bi hostu koji salje upit obezbedili trazeno preslikavanje_


### Lokalni Name Server
- Svaki ISP (npr. univerzitet ,akademski department ili cak neka velika firma) ima svoj lokalni name server
- Kada host izda DNS upit, poruka se prvo prenosi do lokalnog name server-a
- Upiti idu hijerarhijski, prvo se pita Lokalni name server , pa u slucaju da on ne znam tj. nema potrebnu informaciju ide se na sledeci nivo 
### Root Name Server
- Koristi se kada lokalni name server ne moze da nadje trazeno preslikavanje
- U slucaju da Root Name Server ima potrebnu informaciju, on prosledi to lokalnom, u slucaju da nema on zna ip adresu TLD-a koji zna adresu autorizovanog name server-a koji ima preslikavanje za dato ime

### TLD Server
- Server vrsnih domena su odgovrni za domene poput `.com , .org , .gov` i sve geografske domene `[.uk,.fr,.rs,.ba]`
- Ako root name server ne zna preslikavanje datog imena host-a on kontaktira jedan od TLD servera
	- Root serveri i TLD server obicno ne sadrze preslikavanje imena hosta u ip adrese, vec preslikavanja koja omogucavaju lociranje autorizovanih servera imena.
### Autorizovani Name Server

- Najveci nivo name server-a, Kada autorizovani name server  dobije upit od root name servera ili TLD servera, on odgovara trazenom ip adresom
**Upiti**
- Iterativni 
	- Kontaktirani name server odgovara IP adresom sledeceg name servera : _ne prepoznajem ovo ime, ali  pitaj ovaj server_
1. <mark style="background: #FF5582A6;">Host</mark> kontaktira svoj <mark style="background: #FFF3A3A6;">lokalni</mark> DNS server
2. <mark style="background: #FFF3A3A6;">Lokalni</mark> DNS server kontaktira <mark style="background: #BBFABBA6;">root</mark> DNS server
3. <mark style="background: #BBFABBA6;">root</mark> DNS server odgovara adresom <mark style="background: #ADCCFFA6;">TLD</mark> DNS servera
4. <mark style="background: #FFF3A3A6;">lokalni</mark> DNS server kontaktira <mark style="background: #ADCCFFA6;">TLD</mark> DNS server
5. <mark style="background: #ADCCFFA6;">TLD</mark> DNS server odgovara adresom <mark style="background: #D2B3FFA6;">autorizovanog</mark> DNS servera
6. <mark style="background: #FFF3A3A6;">Lokalni</mark> DNS server kontaktira <mark style="background: #D2B3FFA6;">autorizovani</mark> DNS server koji vraca IP adresu hosta 
---
- Rekurzivni 
	- Teziste razresenja imena je na kontaktiranom name server-u
1. Host kontaktira <mark style="background: #FFF3A3A6;">lokalni</mark> DNS
2. <mark style="background: #FFF3A3A6;">Lokalni</mark> DNS server kontaktira <mark style="background: #BBFABBA6;">root</mark> DNS server
3. <mark style="background: #BBFABBA6;">Root</mark> DNS server kontaktira <mark style="background: #ADCCFFA6;">TLD</mark> DNS server 
4. <mark style="background: #ADCCFFA6;">TLD</mark> kontaktira <mark style="background: #D2B3FFA6;">autorizovani</mark> DNS server
5. <mark style="background: #D2B3FFA6;">Autorizovani</mark> DNS server odgovara <mark style="background: #ADCCFFA6;">TLD</mark> serveru IP adresom host-a
6. <mark style="background: #ADCCFFA6;">TLD</mark> prosledjuje odgovor <mark style="background: #BBFABBA6;">root</mark> DNS serveru
7. <mark style="background: #BBFABBA6;">Root</mark> DNS server Prosledjuje odgovor <mark style="background: #FFF3A3A6;">lokalnom</mark> DNS serveru
8. <mark style="background: #FFF3A3A6;">Lokalni</mark> DNS server prosledjuje odgovor hostu 

_Kada bilo koji name server nauci mapiranje on ga kesira_

### Dodatni servisi DNS-a
**Host aliasing**
- host sa komplikovanim imenom moze imati jednu ili vise skracenica
- Mail server aliasing
- Distribucija opterecenja. DNS se koristi za distribuciju opterecenja repliciranih servera, kao npr. replicirani Web Serveri.
	-  Najbolji primer, jedno ime se daje Dvema web servera koji dalje rasporedjuju zahteve, time se izbegava da sve ode na jedan web server i time se optereti.
	- ili u slucaju da prvi 'umre' ovaj drugi moze da preuzme
**DNS zapisi**

**Type=A**
- name : ime hosta
- value : IP adresa hosta
- Type : NS
	- name je domen
	- value je ime autorizovanog DNS servera za ovaj domen
- koristi se da prosledi DNS upit kroz lanac upita

Type=CNAME
- name : alias za neko 'realno'   ime
- value : realno ime
- TYPE : MX
	- value : ime mail servera za domen name

### DNS format poruka
**DNS PROTOKOL** : Upiti i odgovori imaju isti format
**Header**
- Identification : 16 bit broj za identifikaciju upita
- flags 
	-  1bit : upit _0_ ili odgovor _1_
	- rekurzivni upit se trazi
	- rekurzija podrzana
	- 1 bit: odgovor je od autorizovanog name-servera
	- Number of - broj pojavljivanja 4 tipa podataka koji  slede iza zaglavlja
- questions : sadrzi informacije o upitu koji se trazi
	- ime
	- tip polja za upti ( npr. tip A-ime hosta)
- Answers : RR zapisi u odgovoru na pitanje
- authority : records za authorized servere
- additional information : dodatne korisne informacije , npr ako je u polju answers bio mx zapis kanonicno ime mail servera, tada u ovom polju moze biti njegova ip adresa
