#### Data link layer
- [ ] zaduzen je za pouzdani prenos podataka u kom se definise format poruke preko komunikacionong kanala izmedju dva cvora umrezi 
		- Cvor moze da bude host ili IMP (ruter)

- [ ] Potrebne stvari da bi se ostvario pouzdani prenos

1. Ram sinhronizacija - Prepoznavanje granice svakog okvira
2. Kontrola Gresaka - 
3. Kontrola toka
4. adresiranje - odredjivanje identiteta ucesnika
5. upravljanje vezon ( link management )

- [ ] Kako se prenos odvija 
	- Mrezni nivo (host ili ruter ) predaje datagram adapteru koji upravlja izvornom stranom komunikacionog kanala
	- adapter stavlja datagram u  ram  i predaje ram na komunikacioni kanal
	- prijemni adapter prima ram, izvlaci datagram i predaje ga mreznom nivou
	- ako protokol nivoa veze obezbedjuje detekciju gresaka, adapter u izvoru odredjuje bitove za detekciju gresaka, a adapter u odredistu vrsi prover

##### Analogija - Put iz Nisa za Dortmund
- [ ] Organizaciju puta izgleda ovako 
	- Nis - Beograd, autobus
	- Beograd - Dizeldorf, avion
	- Dizeldorf - Dortmund, voz
- [ ] Odgovarajuce agencije za prevoz preuzimaju odgovornost za prevoz putnika
	- Nis - Beograd, autoprevoznik
	- Beograd - Dizeldorf, aviokompanija 
	- Dizeldorf - Dortmund, zeleznica
- [ ] Poenta je da kroz tri segmenta puta upravljaju razlicite kompanije i koriste razlicita prevozna sredstva ali je usluga ista === PREVOZ PUTNIKA
	- putnik - datagram
	- svaki transportni segment - komunikacioni kanal
	- nacin transporta - protokol dll
	- turisticka agencija - mrezni protokol za rutiranje

#### Funkcije DLL -a 
##### Uramljivanje 
- [ ] Uramljivanje Karakterima
	- Pocetak svakog rama oznacen je specijalni parom DLE STX, kraj je oznacen sa specijalnim DLE ETX, izmedju su podaci.
	- Problem nastaje kada se zadesi specijalni par DLE STX/ETX u podatku
	- Resenje : 
		- Nivo vezze na izvornoj strani posle nailaska DLE ubacuje jos jedan DLE
		- Na odredistu kada vidi 2 DLE DLE , drugi DLE se odbacuje

- [ ] Uramljivanje u par rezervisanih bajtova
	- primer ; 01111110 . STA AKO SE NADJE 01111110 kod podaci
	- RESENJE :
		- nivo veze u izvoru posle 5 uzastopnih 1-ca dodaje 0
		- nivo veze u odredistu brise 0 posle 5 uzastopnih 1-ca

- [ ] Kontrola Gresaka
	- [ ] Tehnike za detekciju
		- Parity Checking (cifra parnosti)
			- jedan bit se dodaje svakom paru.
			- Omogucava otkrivanje jednostrukih gresaka
			- ako nastanu 2 ili paran broj uopste nije bas od pomoci
		- Kontrola suma bloka
			- Bloku koji se prenosi dodaje se jos jedan karakter
			- ![[Pasted image 20240505215058.png]]
		- Kontrolna suma (checksum)
			- formiraju se sabiranjem 16- bitnih polureci koriscenjem jedinicnog komplementa i na kraju kompletiranjem sume
		- Polinomski kodovi
			- Bloku podataka koji se prenose dodati na kraj 1 ili vise karaktera u zavisnosti od algoritma i sadrzaja poruke
			- Dva tipa
				- Checksum
					- Izracunava se sabiranjem decimalnih vrednosti svakog karaktera u poruci, podeli se sa 255 i na kraj se doda ostatak, prijemnik isto to uradi
				- Cyclic Redundancy Code
					- m-bitna poruka se gleda kao koeficijent polinoma stepena m-1 (dizes na stepen gde je 1)
					- Generisanje CRC
						- ako je r stepen polinoma G(X)
							- 1 dodati r nula na kraj, sad je XrM(X)
							- 2 podeliti X^rM(X) sa G(X)
								- sva izračunavanja se obavljaju u mod2 aritmetici
								- nema prenosa kod sabiranja
								- nema pozajmice kod oduzimanja
								- obe operacije su identične i odgovaraju EXOR operaciji
								- deljenje se obavlja kao binarno deljenje, ali se oduzimanje obavlja po mod2
								- za delilac se kaže da se sadrži u deljeniku ako deljenik ima jednak broj bitova kao i delilac 
							- 3 Oduzeti ostatak deljenja (koji je uvek r ili manje bitova) od X^rM(X) koriscenjem sumiranja po MOd2
						- Kada poruka sa CRC stigne u odrediste vrsise deljenje T(X) sa G(X), ako == 0 super, != 0 lose.

- [ ] Primer CRC
	- [ ] ![[Pasted image 20240505223523.png]]
	- [ ] ![[Pasted image 20240505223538.png]]
	- [ ] ![[Pasted image 20240505223545.png]]
	- [ ] ![[Pasted image 20240505223616.png]]




 Ima definicija navalne greske, sto je ukratko, mora da se desi dva put a da nisu jedno pored drugo. mora da ima nekoliko tacnih izmedju ta dva pogresna, da bi se nazivala navalnom

##### Kako izvor utvrdjuje da li je doslo do greske ? 
- [ ] Provera odjekom
	- primenjuje sa kada se prenosi mali broj bitova
	- primenjuje se kod komunikacije izmedju terminala i racunara
		- svaki karakter se prenosi od T do R, zatim racunar vrati primljeni karakter i tek tada se karakter pojavljuje na displeju terminala.

- [ ] Zahtev za automatskom retransmisijom
	- Slobodni (idle ) 
		- izvor moze poslati samo jedan informacioni ram (i ram) pre nego sto se zaustavi da saceka prijem potvrde
		- svaki put kada se inicira prenos i-rama u izvoru se startuje casovnik
			- ako odrediste primi i-ram bez greske salje ACK
			- ako odrediste primi naruseni odbacuje ga
		- kada izvor primi ACK moze da salje drugi
		- Ako izvor primi naruseni ACK ili posle timeout-a ne pristigne nista, vrsi se retransmisija i-rama
	- Neprekidni (kontinualni) ARQ
			- Salje se vise poruka pre nego sto se saceka potvrda, broj poruka zavisi od "prozora", kada se popuni prozor, on ceka dok ne dodje potvrda za prvi, pa se prozor pomera
	- U Slucaju Greske (pogotovo za neku u sredini) : 
		- selektivna retransmisija
			- vrsi se retransmisija samo poruke za koju nije pristigla potvrda
		- vrati se nazad na N
			- Odrediste nakom prijema naruseon i-rama odbacuje sve naredne ramove tako da izvor mora vrsiti retransmisiju svih poruka pocev od narusene

Zadaci za selektivno i go back N imas resene. Neces to da hvatas beleske, jer je sablon.