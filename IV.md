# MAC protokoli i lokalne mreze

- Point-to-Point
- Broadcast ( Deljivi medijumi )
	- Ethernet
	- 802.11 wireless LAN

### Protokoli za pristup emisionom

- Jedan emisioni deljivi kanal 
- Protokoli koji definisu pristup emisionom kanalu pripadaju podnivou nivou veze za podatke koji se zove Medium access sublayer ( podnivo pristupa medijumu ) i zovu se multiple access protocol ( protokoli za visestruki pristup )
	- distribuirani algoritam koji definise kako cvorovi dele kanal, tj. definisu kada cvor moze da salje poruke
### Idealni Muitiple Access Protokol

- Emisioni kanal brzine R bps
	1. Kada jedan cvor zeli da koristi kanal, moze da koristi ceo kapacitet R
	2. Kada M broj cvorova zeli da vrsi prenos, svaki to cini brzinom R/M
	3. potpuno decentralizovan
		- nema posebnog cvora koji koordinira prenosom
		- nema sinhronizacije
	4. SIMPLE
### MAC protokoli : taksonomija 
taksonomija Meaning : nacin organizovanja predmeta ( robe ) i usluga u hijerarhijsku strukturu.

- TRI VELIKE  KLASE
1. Protokoli sa podelom kanala ( Channel Partitioning )
	- podeliti kanal na manje 'delove' ( vremenske slotove, frekvencije ,kodove)
	- podeliti 'parce ' cvoru na ekskluzivno koriscenje
2. Protokoli sa slucajnim pristupon ( Random Access )
	- kanal nije podeljen, dozvoliti koliziju
	- "oporavak" od kolizije
3. "Taking Turns" protokoli ( sa definisanim redosledom pristupa ) - nema kolizije
	- cvrsto koordinisati pristup deljivom medijumu da bi se izbeglo kolizije

#### MAC protokoli sa podelom kanala u vremenskom domenu: TDMA ( time division multiple access )

- Deljivi pristup sa vremenskom podelom
- svaki cvor dobija vremenski interval fiksne velicine (slot) u svakom ciklusu)
- ako nema podataka za slanje, slot je prazan
#### MAC protokoli sa podelom kanal u frekventnom domenu: FDMA ( frequency division multiple access )

-  kanalni spektar podeljen na frekventne bandove
- svakoj stanici dodeljen fiksni frekventni band ( kao kod radio stanica)

#### Protokoli sa podelom kanala na osnovu kodova CDMA ( code division multiple Access )

- jedinstveni "kod " dodeljen svakom korisniku
- uglavnom se koristi kod wi-fi
- svi korisnici koriste istu frekvenciju ali se njihovi kodovi ( kodne sekvence ) razlikuju
	- svaki bitski interval je podeljen na M kratkih intervala
		- obicno se koristi 64 ili 128 cipova po bitu
		- da bi se prenela 1 stanica salje se 'S', u suprotnom komplement od S
- Kodirani signal = (originalni podatak) X (kodna sekvenca)
- dekodiranje= srednja vrednost unutrasnjeg proizvoda kodiranog signala i kodne sekvence
- Analogija sa ljudima koji govore razlicitim jezicima u isto vreme

#### Protokoli sa slucajnim pristupom ( Random Access Protocols )

- Pri prenosu paketa  
	- <mark style="background: #BBFABBA6;">ne postoji Koordinisanje paketa, ali se zato prenosi punom brzinom</mark>
	- <mark style="background: #FF5582A6;">Losa cinjenica je sto ako ima vise cvorova koji prenosi -> "kolizija"</mark>
- Prokol sa proizvoljnim pristupom prenosnom medijumu definise:
	- kako se detektuje kolizije
	- kako se vrsi oporavak od kolizije
- Primeri random access MAC protokola: 
	- ALOHA
	- slotted ALOHA
	- CSMA,CSMA/CD


##### ALOHA
- Jednostavan, nema sinhronizacije
- kada stanica ima ram za slanje, odmah ga salje
- velika verovatnoca nastanka kolizije

- Nacin rada
	- svi hostovi koriste istu frekvenciju
	- nema nikakve provere da li je kanal zauzet
	- ako je paket stigao korektno do centralnog cvora, centralni cvor salje potvrdu na drugoj frekvenciji
	- AKO VISE HOSTOVA PRENOSI U ISTO VREME -> KOLIZIJA
		- Kolizija se ne resava, vec host ceka neko vreme na potvrdu dok ne posalje ponovno poruku

#### Poboljsanje SLotted ALOHA 

- Pretpostavke ( po kome protokol radi ) 
	- svi ramovi su iste velicine
	- vreme je podeljeno na jednake velicine, vreme koje je potrebno da se prenese jedan ram
	- cvorovi pocinju sa slanjem ramova samo na pocetku slota
	- cvorovi su sinhronizovani
	- ako 2 ili vise cvorova prenose u istom cvoru, dolazi do kolizije

- rad protokola
	- kada cvor dobije novi ram, prenosi ga u sledecem slotu
	- ako nema kolizije cvor moze poslati novi  ram u sledecem slotu
	- ako postoji kolizija, cvor moze poslati novi ram u sledecem slotu sa verovatnocom `p`, sve dok ne uspe da ga posalje
	- neophodna je sinhronizacija da bi se znalo kada pocinje novi slot
		- Poseban stanica emituje bip signal na pocetku svakog intervala

### Osluskivanje nosioca signala - CSMA ( carrier sense multiple access )

- Ideja :
	- osluskuj kanal pre pocketka slanje: 
		- ako je kanala slobodan, prenosi se ceo ram
		- u suprotnom odlozi prenos
	- ANALOGIJA -  ne prekidaj drugo g dok govori

- Kolizija ipak moze da se desi, situacija kad cvor 1.  proveri u isto vreme kad i cvor 2. i onda dodje do kolizije
### CSMA/CD (Collision Detection)
- Osluskivanje nosioca, odlaganje u slucaju zauzetosti kao kod CSMA
	- kolizije se detektuju u kratkom vremenu 
	- u slucaju detekcije kolizije prenos se obustavlja, redukuje se izgubljeno vreme i kapacitet kanala
- detekcija kolizije:
	- laka u scujaju zicanih lanova
	- otezano kod bezicnih

#### CSMA/CD : restrikcije velicine mreze
- da bi osigurali da se paket prenese bez kolizije, host mora biti ustanju  da detekuje koliziju pre nego sto okonca sa slanje paketa
- Da bi se osigurali da se paket prenese bez kolizije, host mora biti u stanju da detektuje kolizijju pre nego sto se okonca sa slanje mpaketa

- Taking turns - protokoli
	- sa prozivkom
		- master cvor kruzno proziva sluge da obave prenos
		- nedostaci:
			- vreme utroseno na prozivku
			- latentnost
			- otkaz master cvora je katastrofalan za sistem
	- sa zetonima
		- kontrolni zetoni se prenose od cvora do c vora
		- vlasnik zetona ima pravo prenosa
		- nedostaci:
			- vreme potrebno za prenos zetona
			- latentnost
			- kriticno: gubitak zivota

