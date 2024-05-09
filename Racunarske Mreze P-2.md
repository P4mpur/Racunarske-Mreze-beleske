### Primeri Protokola za PTP kanale
- [ ] HDLC - High level data link control (ISO standard)
- [ ] PPP - Point To Point Protocol (Internet standard)

#### HDLC
- [ ] Bit orijentisani Protokol za PTP i viseprikljucne linije
- [ ] Koristi Kontinualni ARQ
	- Numeracija poruka  po modulu 8 ili 12
- [ ] Podrzava dva rezima rada
	- Rezim normalnih odgovora
	- asinhroni balansirani rezim

- [ ] Rezimi rada
	- Response mode
		- sekundarna stanica 'terminal' moze da salje poruke samo kada je prozvana
	- Asinhroni balansirani rezim
		- PTP veze, svaka stanica moze da radi i kao prima i kao sekundar

##### Format Okvira
![[Pasted image 20240509231212.png]]

- [ ] Tri vrste okvira
	- informacioni (I)
	- supervizorski (S)
	- nenumerisani (U)
			-Adresa - "kod" nebalansiranog rezima za adresiranje sekundarnih stanica na multidrop linijama
			-- Kada sekundarna stanica vraca odgovor u polju, adresa je uvek adresa sekundara
	- [ ]  Checksum 
	- $$ CRC = x^.16 + x^.12 + x^5 +1$$
	- [ ] CRC se generise modifikovanom metodom
		- na kraj manje  tezine poruke se dodaje 16 jedinica (umesto 16 nula)
		- vrsi se deljenje generatorskim polinomom
		- dobijeni ostatak se invertuje pa se oduzima od poruke
		- ako nema greske u toku prenosa ostatak nije 0 vec 1

##### Informacioni Ram
- [ ] Numerisani Ram![[Pasted image 20240509232201.png]]
	-  P/F : poll/final - bit prozivke u komandama, kranji bit u odgovorima
	- N(S): redni broj rama
	- N(R): redni broj rama koji  se ocekuje **Sluzi za potvrdjivanje** 
	- tehnika potvrdjivanja zajedno sa slanje informacionoih ramova -- **piggybacking**

- [ ] Ne-Numerisani Ram![[Pasted image 20240509232631.png]]
	- Koriste se za uspostavljanje i raskidanje veze
	- Ne numerisu se i ne sadrze informacije o potvrdjivanju
		- SNRM (E) - Set Normal Response Mode
		- SABM( E ) - Set Asynchronous Balance Mode
		- DISC - Raskidanje veze
		- UA - ( un-numerated acknowledgment ) - nenumerisana potvrda. Koristi se kao ram potvrde u ovoj klasi ramova
		- DM - Raskidanje veze

- [ ] Supervizorski![[Pasted image 20240509232842.png]]
	- Koriste se za kontrolu toka i kontrolu gresaka kada druga strana nema poruka za slanje
		- 00 - RR ( Receave Ready )
		- 01 - RNR ( Receave not Ready )
		- 10 - REJ ( Reject ) - nastupila greska u toku prenosa, zahteva se retransmisija po prinicpu GO-BACK-N ([[Racunarske Mreze P-1#^7c03dc]])
		- 11 - SREJ - selektivna retransmisija ([[Racunarske Mreze P-1#^7041c6]] )
		-  N ( R ) - redni broj poruke koa se ocekuje da se primi
- [ ] Primer : ![[Pasted image 20240509233536.png]]
##### PPP Protokol 
- [ ] Za povezivanje LAN na kicmu
	- veza LAN prema spoljnjem svetu ide preko jednog ili dva rutera koji imaju PTP vezu sa udaljenim ruterima.
		-Takva veza izmedju ruter tehnicki cini internet
- [ ] Za povezivanje kucnih racunara na internet
	- Mode uspostavlja fizicku vezu sa modemom u ISP.
- [ ]  Data Link Layer u Internetu : ![[Pasted image 20240509233845.png]]
- [ ] Projektantski zahtevi
	- [ ] Uramljivanje paketa
		- datagram sa mreznog nivoa prihvata i stavlja u okvir tako da prijemnik moze da zna kad je kraj kad je pocetak rama
		- mora biti u stanju da prenosi poruke preko bilo kog mreznog protokola
		- mora biti u stanju da prosledi poruku odgovarajucem protokolu mreznog nivoa
	- [ ] Transparentnost
		- ne sme postojati ogranicenje u pogledu sadrzaja poruke sa mreznog nivoa
			- - byte staffing se primenjuje - stream podataka se deli u mrezne frame-ove/nivoe, mozda je najbolje da se kaze u ramove
	- [ ] Odrzavanje veze
		- PPP mora biti u stanju da detektuje otkaz na nivou veze i da signalizira gresku mreznom nivou
	- [ ] mora biti u stanju da radi sa razlicitim protokolima fizickog nivoa

- [ ] Ne Ocekuje se
	- [ ] Da PPP ispravlja greske
	- [ ] Kontrola toka 
		- ocekuje se da je PPP prijemnik u stanju da prihvata ramove punom brzinom
		- ako to nije obezbedjeno, ostavljeno je visim nivoima da regulisu briznu
	- [ ] Sekvenciranje ( numerisanje ) poruka
		- od PPP se ne zahteva samo da u prijemnku stignu ramovi po redosledu
	- [ ] Multipoint veze 
		- PPP moze da radi samo na PTP linijama za razliku od [[#HDLC]]

- [ ] Format Okvira : ![[Pasted image 20240509234617.png]]
	- flag : delimiter ( framing ) 
	- ==Address: sadrzaj polja je uvek 11111111==
	- ==Control : sadrzaj polja je uvek 00000011, ostavljeno za moguce buduce primene== -- s obzirom da ove dve primaju fiksirane vrednosti, moguce je da PPP sender jednostvano izostavi  address i control sektore.
	- Protocol: Oznacava kom protokolu je namenjem ram 
		- - PPP-LCP
		- -IP
		- -IPCP
- [ ] Transparentnost 
	- PPP definise specijalni escape bajt 01111101
		- - ako se flag pojavljuje u polju podataka, PPP ubacuje esc bajt ispred.
		- - Prijemnik, kada detektuje esc bajt ispred, odbacuje
		- - Ako se esc bajt pojavljuje u poljuu podataka ubacuje se jos jedan
- [ ] Uspostavljanje i raskidanje : 
	![[Pasted image 20240510000220.png]]
	- Uspostavljanje, Odrzavanje i raskidanje veze ostvaruje se uz pomoc PPP-ovo LCAP ( Link Control Protocol ).
		-  PPP veza pocinje i okoncava se u stanju dead
		-  Kada se detektuje nosilac signala na fiz. nivou, PPP prelazi u stanje uspostavljanje veze
			- - strana koja zeli uspostavljanje veze salje ram za konfigurisanje veze koriscenjem configure-request rama 
			- - druga strana odgovara sa configure-ack ramom(opcije prihvatljive), ili configure-nak(opcije shvacene ali nisu prihvatljive) ili configure-reject(ne valja uopste, ali prihvatljivo za razgovore)
			- -LCP opcije ukljucuju maksimalnu velicinu rama, specifikaciju za autentifikaciju, opcije da se preskoci koriscenje address i control polja u PPP ramu.
	- Konfigurisanje Mreznog Nivoa (IP, IPX, OSI , CLANP, ...)
		- Davanje IP adrese, primer sa prezentacije, ako je kucni racunar onda mu ISP daje privremenu adresu.
			- Veza ostaje otvorena sve dok 1 ne posalje terminate-request a 2 ne posalje terminate-ack

- [ ] LCP ( Link Control Protocol ) paketi : ![[Pasted image 20240510000410.png]]
	- Polje 'kod' definisu vrstu LCP paketa
	- Polje 'identifikacija' sluzi za uparivanje zahteva ( requests ) i odgovora ( replies )
	- Polje 'length' govori o ukupnoj velicini LCP paketa u byteovima.
- [ ] Postoje tri klase LCP paketa :
	- Paketi za uspostavljanje i konfigurisannje veze
		- - Configure-request, Configure-ack, Configure-nak, Configure-reject
	- LCP paketi za raskidanje veze
		- - Termination-request, Termination-ack
	- LCP paketi za upravljanje i debagiranje 
		- - Code-reject, Protocol-reject, Echo-request, Echo-reply, Discard-request
- ![[Pasted image 20240510000711.png]]
- [ ] Configuration - request paket
	- Format Polja Opcija : ![[Pasted image 20240510000804.png]]
		- Tip ( 1 byte ) : 
			- - Max vel.paketa, protokol za autentifikaciju, kompresija rama.
		- Duzina (1 byte ) 
			- - definise duzinu konfiguracionie opcije ukljucujuci tip, duzinu i polje podataka
		- Podaci ( 0 ili vise byte-ova)
			- - sadrzi informacije specificne za datu opciju
	- [ ] Autentifikacija : ![[Pasted image 20240510000953.png]]