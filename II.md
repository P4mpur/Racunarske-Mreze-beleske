# Nivo veze za podatke (Data link layer)

### Funkcije nivoa veze za podatke

- ==Duznost==
	- Prenos podataka preko komunikacionog kanala u vidu ramova ( frames, okviri ) izmedju dva uredjaja u mrezi, uredjaji mogu biti host/ruter ( IMP ) i u njemu se definisu pravila ( protokol ) tj. format poruke kojim ce da se salju te iste poruke
	
- Kako bi se ostvario pouzdan prenos podatka, protokoli dll-a moraju da obezbede
	- Ram sinhronizaciju - Mora da se zna kada se zavrsava ram
	- mehanizmi za detekciju gresaka u prenosu
	- kontrolu toka - regulisanje brzine prenosa izmedju cvora i odredista 
	- adresiranje - odredjivanje identiteta ucesnika u komunikaciji 
	- upravaljanje vezom - mehanizmi za uspostavljanje, odrzavanje i raskidanje veze

## Uramljivanje 

Postoje dve vrste uramljivanja poruka : 
- Rezervisani niz karaktera ^ca9e56
	- Pocetak je oznacen parom <mark style="background: #BBFABBA6;">DLE STX</mark> 
		- <mark style="background: #BBFABBA6;">DLE STX</mark> podaci <mark style="background: #BBFABBA6;">DLE ETX</mark>
		- to su rezervisani karakteri sa specijalnim znacenjem
		- u slucaju da postoji ta sekvenca ali u poruci a nije kraj poruke ubaci se dva uzastopna <mark style="background: #BBFABBA6;">DLE</mark> , i na taj nacin je sekvenca jedinstvena
	- Pri uramljivanju poruka karakterima, velicina rama mora biti deljiva sa velicinom karaktera (8 bitova npr.)
- Rezervisani par bajtova
	- Rezervisana sekvenca je <mark style="background: #FFB86CA6;">01111110</mark> 
		- S obzirom da  je moguce da postoji u poruci sekvenca  <mark style="background: #FFB86CA6;">01111110</mark>  koja ne obelezava kraj, osmisljeno je pravilo da nakon nailaska 5 uzastopnih jedinica u polju dodaje se jedna 0
		- DLL nivo veze u destination-u ce da obrise tu nulu koja sledi posle 5 uzastopnih jedinica
	- Duzina poruke ne mora biti umnozak od 8 

## Kontrola Gresaka

- Kontrola cifra parnosti ( Parity checking )
	- Jedan bit se dodaje svakom karakteru
		- <mark style="background: #BBFABBA6;">parna vrednost </mark> : broj jedinica je paran u kodnoj reci
		- <mark style="background: #FF5582A6;">neparna vrednost</mark> : broj jedinica je paran u kodnoj reci
	- Jednostavan nacin ali ne pomaze u slucaju kada postoje greske na dva bita
- Kontrolna suma bloka
	 - Bloku koji se prenosi dodaje se jos jedan karakter -  <mark style="background: #FFF3A3A6;">BCC</mark> ( Block Check Character )
		 - <mark style="background: #FFF3A3A6;">BCC</mark> BCC se odredjuje kao parity bit ali za svaki bit u celokupnom bloku karaktera, kao i za svaki karakter posebno
		 1010 1100 1 <- paritet redova
		 0110 0011 1 <- paritet redova
		 1001 1010 0 <- paritet redova
		 0001 0111 1 <- paritet redova
		 1111 0110 i 1 <- paritet kolone PLUS 1 koji se i dalje gleda kao pariter redova
- Checksum 
	- Sabiranje 16 bitnih polureci koriscenjem jedinicnog komplementa i na kraju komplementiranjem dobijene sume
		- primenjuju  se na mreznom i transportnom nivou
	- U ovom formatu kontrole gresaka se desavaju navalne greske, to su greske koje pocinju i zavrsavaju se sa istim bitom.
	- Da bi duzina navalne greske bila B, potrebno je da izmedju dve navalne greske postoji najmanje B korektnih bitova
		- primer
			- 0010 1001
			- 0<mark style="background: #FF5582A6;">110 11</mark>00 <- crveno je navalna greska 
			- <mark style="background: #FFF3A3A6;">0100 010</mark>0 <- ovo ne bi bila navalna greska iako se zavrsavaju istim bitom 
- Polinomski kodovi ( CRC - cyclic redundancy Cide) kao mehanizam detektovanja navalnih gresaka ^d79b34
	- Elementi m-bitne poruke se posmatraju kao koeficijenti polinoma stepena m-1
	-  Koriste se generatorski polinomi koji se dodaju poruci 
		- bit najmanje i najvece tezine generatorskog polinoma mora biti 1
		- Stepen generatorskog polinom mora biti manji od stepena polinoma poruke
	- Generisanje CRC
		- Polinom (generatorski) je definisan kao G(x) i ima odredjeni stepen n
		- Podaci ( koji se prenose ) se predstavljajju kao binarni broj i oznacavaju kao M(x). Na kraju se dodaju n broj nula gde je n stepen generatorskog polinoma
		- Binarni broj M(x) se deli  sa G(x ) i  tako se dobija CRC vrednost. Ovo deljenje je rezultat operacije modx2 (XOR) i izvodi se na binarne brojeve
		- Ostatak nakon  deljenja je u stvari nasa CRC vrednost
		- Originalni podatak se prenosi zajedno sa CRC vrednoscu (originalni podaci + CRC) sa istim generatorskim polinomom. Ako je ostatak nula, dobro je , ako nije ostatak nula YIIKES

## Potvrda Gresaka  kod izvora

Postoje dve strategije: 
###  Provera odjekom

-  Primenjuje se kada se ne prenosi veliki broj bitova
	- svaki karakter se prenosi od terminala do racunara, zatim racunar vrati isti karatker kao odjek ili eho i time potvrdi da je doslo 
### Zahtev za automatskom retransmisijom ( Automatic repeat request - ARQ)
-  Destination vraca ACK ili NAK kao potvrdu od destination-a 
- ARQ tehnike , razlikuju se po tome da li se daje prioritet zahtevanom baferskom prostoru u izvoru i odredistu ili iskoriscenosti komunikacionog kanala : 
-  Slobodni (idle) ARQ : Protokoli 
	 - SEND-AND-WAIT OR STOP-AND-WAIT ^42c3fd
	 1. Izvor moze poslati samo Informacioni ram ( I frame ) pre nego sto se zaustavi da saceka ACK
	 2. Svaki put kada se posalje I-frame, startuje se timer
	 3. ako odrediste primi I-frame, salje ACK
	 4. Kada izvor primi ACK, moze da salje dalje
	 5. Ako destination primi naruseni I-frame, baca ga
	 6. Ako izvor primi naruseni ACK-ram ili nakon timeout-u stigne poruka, ista poruka se salje opet
- Neprekidni ( kontinualni ) ARQ
		Zbog neiskoriscenosti komunikacionog kanal, osmisljeni su bolje metode 
		Koristi princip slanja veci broj poruka, tj `w` broj poruka pre nego sto saceka ACK
		Destination prima poruke u baferu koji je ogranicen
	-Medote resavanja problema narusavanja poruka ^e7cba1
	- selektivna retransmisija 
		- Vrsi se retransmisija samo poruke za koju nije pristigla potvrda
		- Velicina prozora ( bafera ) > 1
	- vrati se nazad na N ( GO BACK N) ^0e2ce9
		- Odrediste nakon prijema narusenog I-frame-a odbacuje sve naredne ramove, tako da izvor moze vrsiti retransmisiju svih poruke pocev od narucene
		- Velicina prozora ( bafera ) > 1
