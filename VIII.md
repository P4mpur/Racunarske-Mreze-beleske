# Transportni nivo
- Razlika izmedju transportnog i mreznog nivoa
	- **Mrezni** : razmena se vrsi izmedju dva hosta
	- **Transportni** : razmena se vrsi izmedju aplikacije procesa
- Izvrsava multipleksiranje i demultipleksiranje, prima poruku i predaje odgovarajucem aplikativnom procesu
- Transportni protokoli se izvrsavaju na hostovima
	- izvorna strana: deli aplikativne poruke na segmente 
	- prijemna strana: reasemblira segmente u poruke i predaje ih aplikativnom nivou
- Zadatak transportnog nivoa je da poboljsa kvalitet usluga, tj. da premosti procep izmedju usluga koje mrezni nivo nudi i onoga sto korisnik zeli
- Transportni nivo omogucava korsiniku da specificira zeljene prihvatljive i minimalne prihvatljive vrednosti razlicitih parametara u trenutku uspostavljanja veze
	- propusnost, prenosno kasnjenje, nivo zastite, prioriteti poruka

### Tabela

**TPDU** - Transport protocol data unit

Funkcija
	TPDU koja se salje 1
		znacenje
**Listen**
- nista : blokira proces dok ga neki drugi ne kontaktira
**Connect**
- Connection : Aktivno pokusava da uspostavi konekciju
**Send**
- REQUEST DATA: Aktivno pokusava da uspostavi konekcioju
**Receive**
- nista : blokira se dok proces ne primi poruku
**Disconnect**
- DISCONNECTION REQUEST : Ova strana zeli dda zatvori konekciju
	- Ima dve varijante
		- **Asimetricno** : Bilo koji korisnik moze da izvrsi DISCONNECT funkciju, sto uzrokuje da se DISCONNECTION REQUIRED TPDU salje udaljenom transportnom entitetu
		- **Simetricno** : Svaki smer se zatvara posebno, kada jedna strana izvrsi DISCONNECT, to znaci da ona nema vise podataka za slanje, ali je jos uvek voljna da prihvati podatke sa druge strane
			-  Konekcija je okoncana kad obe strane izvrse DISCONNECT

## **Multipleksiranje i demultipleksiranje**
- Demultipleksiranje 
	- Prosledjivanje primljenih segmenata korektnom aplikativnom procesu
- Multipleksiranje
	- prikuplanje podataka iz vise aplikativnih procesa. Omogucava da vise procesa na jednom hostu komunicira kroz mrezu ( dodavanjem zaglavlja koje se kasnije koristi za demultipleksiranje)

- Transportni protokoli u internetu, TCP i UDP obavljaju zadatke MUX/DEMUX tako sto koriste dva polja u zaglavlju polja (segmenta) : 
	- broj izvornog porta ( SP - source port number ) i 
	- DP (destination port number)

- U slucaju da dva razlicicta klijenta uspostave ( telnet primera radi ) sesiju do servera i svaki odabere isti broj izvornog porta ( sp - 23), onda se gleda i ip adresa, i time se stvara **Sveto Trojstvo**

## Internet transport protokoli

### TCP - transmission control protocol
**Pruza konekcioni servis aplikaciji** : Pri konektovanju vrsi se Three way handshake
- <mark style="background: #FFB86CA6;">TCP</mark> obezbedjuje pouzdan komunikacioni kanal aplikativnom procesu, multipleksiranje, demultipleksiranje, kontrolu gresaka, kontrolu toka, kontrolu zagusenja, full-duplex komunikaciju, point-to-point tipa.
- <mark style="background: #FFB86CA6;">Uspostavljanje veze</mark> u klijentu obavestava TCP softwer da zeli da uspostavi konekciju sa serverom
- <mark style="background: #FFB86CA6;">Socket</mark> Vrata procesa preko kojih proces prenosi podatke TCP softweru ( programska apstrakcija za komunikacioni kanal ) : Prestavlja kapiju izmedju aplikativnog procesa i transportnog protokola ( TCP ili UDP )

###### **Kako funkcionise** - treba mi ovo za ovako
- Da bi se komunikacija obavila izmedju klijent procesa, klijent **MORA** da kontaktira server
	- Da bi server mogao da reaguje na klijentov kontakt, server mora da ima kreiran socket koji klijent moze da kontaktira
- Klijent proces konaktira server tako sto kreira svoj socket
	- Kada kreira svoj socket, klijent navodi ime servera ( ip adresu ) i broj porta aplikacije
- Nakon kreiranja socketa, TCP softver na strani klijenta inicira three way handshake proceduru i uspostavlja TCP konekciju sa serverom

#### TCP Zaglavlje
- <mark style="background: #BBFABBA6;">Polja source port i destination port</mark> - identifikuju krajnje tacke
- <mark style="background: #BBFABBA6;">Sequence number i Acknowledge number</mark> - igraju kljucnu ulogu u postizanju pouzdanog TCP servisa. TCP vidi podatke kao niz bajtova
- <mark style="background: #BBFABBA6;">Lenght </mark>- Definise velicinu zaglavlja u 32-bitnim recima
- sledecih 6 bitova se ne koristi
- <mark style="background: #BBFABBA6;">URG ( urgent ) bit</mark> - postavljen na 1 ako se koristi urgent pointer
	- Pokazuje gde se nalazi urgentni podaci ( ako postoje ) u segmentu
	- Redni broj poslednjeg bajta urgentnog podatka
	- Svi podaci do poslednjeg bajta urgentnog polja se u odredistu odma prosledjuju aplikaciji, pa se zato urgentno i prenose svi podaci ispod urgentnog i urgentni
- <mark style="background: #BBFABBA6;">PUSH bit</mark> - ukaziju na podatke koji moraju odma biti preneti nakon prijema iz aplikacije
- <mark style="background: #BBFABBA6;">RST bit</mark> - koristi se za odbacivanje nevazeceg segmenta ili kada se odbije konekcija
- <mark style="background: #BBFABBA6;">SYN bit</mark> - koristi se za uspostavljanje konekcije. Konekcioni zahtev ima <mark style="background: #BBFABBA6;">SYN = 1</mark> i <mark style="background: #FF5582A6;">ACK = 0</mark>, odgovor na konekcioni zahtev nosi <mark style="background: #BBFABBA6;">SYN = 1 i ACK = 1</mark>
- <mark style="background: #BBFABBA6;">FIN bit</mark> - za okoncanje konekcije
- <mark style="background: #BBFABBA6;">window size ( velicina prozora ) </mark> - koristi se za kontrolu toka. Kazuje koliko bajtova moze biti poslato, pocev od potvrdjenog 
	- win = 0 kaze izvoru da privremeno prestane sa slanjem
- <mark style="background: #BBFABBA6;">Checksum</mark> - polje za kontrolu celo TCP segmenta.
	- Kao i UDP koristi 12-byte "pseudo-header" kod izracunavanja checksum-a
	- Pseudo header se ne prenosi kroz mrezu vec samo se koristi za potvrdjivanje da li je poruka stigla na dobru destinaciju.
- <mark style="background: #BBFABBA6;">Polje opcija</mark> - omogucava definisanje opcije koje standardnim zaglavljem nisu definisane ( 40 byte max) 

Veza se uspostavlja sa 3 way handshake 
**SYN** -  initiate a connection
**ACK** - acknowledge  a connection
**RST** - reject a connection
**FIN** - terminate/finish a connection

- Raskidanje veze : Svaki smer se zatvara posebno
	- Jedna strana salje **FIN=1** i kada se potvrdi taj smer je zatvoran
	- Ali podaci mogu i dalje na drugu stranu da se salju
	- Tek kada se oba smera ugase tada je konekcija ugasena

- Borba sa kasnjenjem 
	- Kada se posalje segment 
	- izvor startuje casovnik
	- Kada primi ACK za dati segment, zaustavlja casovnik i belezi trenutni RTT.
	- Kada se dobije nova vrednost za RTT, preracuvana se srednja vrednost RTT ( RTT - Round Trip time / SRTT) kao 
	$$SRTT = a * SRTT + (1-a) * RTT $$
	- gde je `a` faktor 'peglanja'
		- Kada je a= 0 , koristi se nova RTT vrednost
		- Kada je 1 = 0, ignorise se nova vrednost
		- tipicne vrednosti su za a izmedju 0.8 i 0.9
	- Takodje mora da se pamti srednja devijacija, razlika izmedju izmerenog i ocekivanog RTT i predstavlja dobru aproksimacijju standardne devijacije. Racuna se na sledeci nacin: 
	 $$D = aD + (1-a) | SRTT - RTT$$
	 Vrednost Timeouta za segment se postavlja na
	 $$RTO = SRTT + 4*D $$

**Kasnjenje detalji**
- S obzirom da postoji samo jedan timer, vreme se meri samo za poslednji paket koji je poslat, postoji vise slucajeva kada neki paket ne stigne i sad izvor mora da reaguje adekvatno
	- Ako primi ACK za svaki do sad sto je poslao, super, nastavi dalje
	- ako su poslata n paketa , a poslednji ack je primljen za n-y = x , onda se opet salju podaci od x
	- ako se samo za poslednji paket ne primi ack , ponovo se salje i sada se ceka duplo vise u odnosu na prethodno, tako se ogranicava zagusenje u mrezi
- Jos jedan nacin za ispravku pri kasnjenju ili pri gubitku nekih paketa je da destination posalje vise puta ack za stigli paket... ako je source poslao y paketa ,a primi ack vise puta za paket x = y -n , onda ce da uporedi koji je to paket i opet ce da posalje

REZIME : DVA NAJVAZNIJE METODE ZAGUSENJA
- Primljena 3 duplikata ACK
- istekao timeout
Faktori koji uticu na brzinu slanja TCP segmenta
- Trenutna velicina prozora izvora definise kolicinu podataka koja u datom trenutku moze biti poslata.

#### Kontrola zagusenja
- Ako nema simptoma zagusenja, postepeno povecavaj velicinu prozor
- Trenutno i veliko smanjenje velicine prozora izvora u  slucaju da se detektuje zagusenje

**Dve verzije  kontrole zagusenja**
- Tahoe
	- Kada se detektuju simptomi zagusenja tj. primi se trostruki ACK ili istekne timeout
		- sstresh = 1/2 cwnd
		- cwnd = 1
	- krece se sa sporim startom ponovo
- Reno
	- Ak ose prime tri duplikata jedno ACK, cwnd i novi sstresh se postavljaju na 1/2 trenutog cwnd
	- Ako je istekao timeout, sstresh se postavlja na 1/2 trenutnog cwnd, a novi cwnd-1
		- 3 duplirana ACK ukazuje daa je mreza u stanju da prenese neke segmente
		- Isticanje timeout-a pre 3 dup ACK je mnogo alarmantnije
### UDP - User Datagram Protocol
**Pruza bezkonekcioni servis aplikaciji**

- Nema inicijalnog uspostavljanja veze
- Ne garantuje isporuku ne koristi potvrdjivanje i numeraciju poruka
- Programi koji korsite UDP sami moraju voditi racuna o greskama
- UDP je brz, moze podrzati point-to-point i point-to-multipoint komunikaciju