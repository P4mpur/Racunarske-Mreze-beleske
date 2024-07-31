# Primeri protokola za point-to-point kanale
- **HDLC** ( High Level Data Link Control ) - ISO standard
- **PPP** ( Point-to-Point Protocol ) -Internet standard 

### HDLC
- Bit orijentisani protokol 
- Koristi Kontinualni ARQ [[II#^e7cba1]] 
- Podrzava dva rezima rada
	1. rezim normalnih odgovora ( nebalansirani reezim komunikacije racunar - terminal )
	2. asinhroni balansirani rezim ( komunikacija racunar-racunar)
	-Razlika izmedju njih je sto 1. funkcionise tako sto terminal moze da daje odgovor samo kada racunar prvi posalje poruku, pa onda ceka ACK
	-Kod 2. i jedan i drugi racunar mogu da salju poruke slobodno, bez da cekaju da ovaj prvi posalje.

#### HLDC - Format okvira 
TRI VRSTE OKVIRA
- Informacioni ( `I` )
	- P/F: poll/finall - bit prozivke u komandama, krajnji bit u odgovorima
	- N ( S ) : redni broj rama
	- N ( R ) : redni broj rama koji se ocekuje 
		- tehnika potvrdjivanja zajedno sa slanjem informacionih ramova
- Supervizorski ( `S` )
	- Koriste se za kontrolu toka i kontrolu gresaka kada druga strana nema poruku za slanje
	
	- 00 - RR ( Receive Ready)
	- 01 - RNR ( Receive not Ready)
	- 10 - REJ ( Reject ) - Nastupila greska u toku prenosa, zahteva se retransmisija po principu GO-BACK-N ( [[GO-BACK-N#^0e2ce9]] )
	- N ( R ) - redni broj poruke koja se ocekuje da se primi
- Nenumerisani ( `U` )
	- Koriste se za uspostavljanje i raskidanje veze, ne numerisu se i kada se posalje ceka se na potvrdu da je destination dobio ram ( stani i cekaj [[STANI I CEKAJ#^42c3fd]])
	
	-  SNRM ( E ) - set normal response mode
	- SABM ( E ) - set asynchronous balance mode
	- DISC - raskidanje veze
	- UA - nenumerisana potvrsa. 'ram potvde'
	- DM - raskidanje veze 
	
- adresa - koristi se kod nebalansiranog rezima za adresiranje sekundarnih stanica na viselinijskim linijama.
	- kada sekundarna stanica vraca odgovor u polju adesa je uvek adresa sekundara
	
- Checksum - CRC [[II#^d79b34]] = x^16 + x^12 + x^5 + 1
	- Generisanje ide malo drugacije
	- Na kraj  se dodaje 16 jedinica
	- vrsi se deljenje generatorskim polinomom
	- dobijeni ostatak se invertuje pa se oduzima od poruke
	- u slucauj da je dobra poruka dobija se 1D0F

#### PPP Protokol
- veza LAN prema spoljnjem svetu ide preko jednog ili dva rutera koji imaju point-to-point vezu sa udaljenim ruterim apreko iznajmljenih linija
	- basically tako je stvoren internet
	- Kuci imas ruter koji se povezuje direktno sa ISP
- Projektantski zahtevi
	- Uramljivanje paketa
		- kako da se ram uklopi da se zna gde je kraj a gde pocetak
		- slanje preko razlicitih protokola ( ne samo preko IP)
		- mora biti u stanju da demultipleksira poruku odgovarajucem protokolu mreznog nivoa
	- Transparentnost 
		- ne sme postojati ogranicenje u pogledu sadrzaja poruke sa mreznog nivoa 
			- byte staffing - tehnika koja dovodi da kada se karakter koji oznacava kraj poruke nadje na sredini poruke ignorise, tj da se ne protumaci pogresno [[II#^ca9e56]]
	- Detekcija gresaka
	- Odrzavanje veze
		- PPP mora biti u stanju da vidi kad dodje do otkaza u vezi i da javi to na adekvatan nacin
	- MORA BITI U STANJU DA RADI SA RAZLICITIM PROTOKOLIMA
	
- Ne Ocekuje se
	- Korekcija greske
	- Kontrola toka
		- OCekuje se od prijemnika da prihvata ramove sto je brze moguce i kako je moguce, ali ako fizicki nije moguce brze da se ide, onda se taj mehanizam ostavlja visim nivoima da rese taj problem
	- Sekvenciranje poruka
		- ramovi kada stignu, ne moraju da budu u 'ispravnom' redosledu, vazno je samo da stignu
	- Multi-point veze
		- SAMO Point to Point
#### Format okvira
- Flag: delimiter ( framing )
- Address: sadrzaj polja je uvek 11111111
- Control: sadrzaj polja je uvek 00000011 ( ostavljeno je za moguce buduce primene )
- Protocol: oznacava na kom protokolu je namenjen ram ( npr, PPP-LCP, IP , IPCP, ...)

#### Uspostavljanje i raskidanje veze
- Koristi se LCP ( Link Control Protocol )
	- PPP veza pocinje i okoncava se u stanju <mark style="background: #FFB86CA6;">mrtva veza</mark>
	- Kada se detektuje primalac signala, PPP prelazi u stanje <mark style="background: #FFB86CA6;">Uspostavljena veza</mark> 
		- strana koja zeli povezivanje salje configure-request ram
		- druga strana ako zeli salje configue-ack ( moze ) ili configure-nak (opcije prepoznatljive ali nisu prihvatljive ) ili configure-reject ( opcije neprepoznatljive ili neprihvatljive za pregovore )
		- LCP opcije ukljucu maksimalnu velicinu rama, specifikaciju protokola za autentifikaciju, opcije da se preskoci koriscenje adress polja i control polja u PPP ramu
- ( OPCIONO ) kada se povezu dve strane, mogu  da se provere identiteti
- Veza ostaje otvorena dok se ne posalje LCP paket terminate-request
	- druga strana odgovara sa terminate-ack i veza ponovo dolazi u stanje dead ( mrtva linija )
- TOK 
	- <mark style="background: #FF5582A6;">DEAD</mark> -> <mark style="background: #BBFABBA6;">LINK ESTABLISHMENT</mark> 
	-<mark style="background: #BBFABBA6;"> LINK ESTABLISHMENT</mark> -> <mark style="background: #FFF3A3A6;">AUTHENTICATE </mark>or <mark style="background: #CACFD9A6;">NETWORK LAYER CONFIG</mark>
	- <mark style="background: #FFF3A3A6;">AUTHENTICATE</mark> -> <mark style="background: #CACFD9A6;">NETWORK LAYER CONFIG</mark>
	- <mark style="background: #CACFD9A6;">NETWORK LAYER CONFIG</mark> -> <mark style="background: #ADCCFFA6;">OPEN</mark>
	- <mark style="background: #ADCCFFA6;">OPEN</mark> -> <mark style="background: #D2B3FFA6;">TERMINATING</mark>
	- <mark style="background: #D2B3FFA6;">TERMINATING</mark> -> <mark style="background: #FF5582A6;">DEAD</mark>

#### LCP paketi
- KOD : definise vrstu LCP paketa ( 1 byte)
- ID : sluzi za uparivanje zahteva (requests) i odgovora  (replies) ( 1 byte )
- LENGTH : ukupna velicina LCP paketa u byte-ovima ( ukljucujuci CODE, ID , LENTH i DATA)
- TRI KLASE LCP PAKETA:
	- Uspostavljanje i konfigurisanje veze
		- configure-request, configure-ack, configue-nak, configure-reject
	- LCP paketi za raskidanje
		- termination-request, termination-ack
	- LCP paketi za upravljanje i debagiranje
		- code-reject, protocol-reject, echo-reject, echo-reply,discard-request
-  Format polja opcija sve u byte-ovima : 
	- TIP - 1
		- max velicina paketa, protokol za autentifikaciju, kompresija rama
	- Duzina - 1
		- duzina konfiguracione opcije ukljucujuci tip, duzinu i polje podataka
	- Podaci ( 0 ili vise byte )
		- sadrzi informacije specificne za datu opciju