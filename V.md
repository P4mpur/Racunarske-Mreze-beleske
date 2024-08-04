# Struktura Ethernet okvira ( frame )
- Preamble ( 7 bytes ) 
	- koriste se da se sinhornizuju brzine prenosa izvora i odredista
- SFD ( 1 byte ) 
	- Start of Frame delimiter 10101011 ( pocetak rama )
- DA i SA, Destination and Source address ( 6 bytes each ) : 
	- mac adresa
	- ako adapter primi okvir sa svojom adresom, ili sa broadcoast adresom ( npr. ARP paket), prihvata ram i prosledjuje ga mreznom nivou
	- u suprotnom se odbacuje
	- Type (2 bytes)
		- ukazuje na protokol viseg nivoa, uglavnom IP, ali mogu biti podrzani i drugi protokoli
		- vrednosti iznad 0600 hex su rezervisane za kodove protokola, max velicina polja je 05DC
	- Data ( 0 - 1500 ) 
		- polje podataka minimalne velicine 46 a max 1500, ne znam zasto pise da je 0
	- Pad ( 0 - 46 byte-a)
		- nule koje se dodaju da bi se postigla minimalna velicina okvira ( od 0 - 46 byte-a), kako bi se destektovala kolizija
	- CRC ( 4 bytes ) 
		- za detekciju greske

- Ethernet CSMA/CD protokol : ALGORITAM
- Jam signal: 
	- obezbedjuje da svi ostali adapteri detektuju koliziju ( 48 bytes )
- Bit time 
	- 0.1 microsec za 10 Mbps Ethernet : za K=1023, vreme cekanja iznosi oko 50msec
- Exponential Backoff:
	1. prva kolizija: bira se `K `iz opsea { 0,1 }: kasnjenje je Kx512 bitskih brzina prenosa
	2. posle druge kolizije: bira se ` K ` iz opsega {0,1,2,3}
	3. posle 10 kolizija, bira se `K`  iz opsega {0,1,2,3,4,5,...,1023}
	4. Maksimalna vrednost za k je 1023 (2^10 - 1), a max broj retransmisija 16, nakon cega se obavestava visi nivo o gresci

1. Adaptor prihvata datagram sa mreznog nivora i kreira ram ( frame )
2. adapter osluskuje kanal i ako ustanovi da je kanal slobodan, pocinje sa prenosom rama. u suprotnom ceka da se oslobodi
3. Akoadapter prenese ceo ram bez kolizije, uspesno je okoncao prenos
4. Ako adapter ddetektuje koliziju prestaje sa slanje rama i salje "jam" sekvencu da bi i drugi adapteri videli da ce doci do kolizije
5. Posle detektovanje kolizije adapter ulazi u "exponential backoff": posle i-te kolizije adapter ceka Kx512 (x - mnozenje) bitskih intervala, pri cemu K bira slucajno iz intervala $${0,1,2,3..., 2^(i-1) }  }$$ nakon prelazi na korak 2
	- Nakon 16 uzastopnih kolizija dostize se attempt limit i MAC odustaje i obavestava sve nivoe o gresci

## Povezivanje LAN segmenta
### HUB-ovi

- Izolovane LAN segmente je moguce povezati pomocu backbone hub-a
- povecava maksimalno moguce rastojanje izmedju cvorova
- Problem:
	- KOlizija u jednom segmentu se detektuje u celoj mrezi
	- Ako cvor u CS segmentu i  cvor u EE segmentu optocnu prenst u isto vredme dolazi do kolizije
	- Ne moze povezati dva segmenta sa razlicitim brzinama prenosta

### Mostovi 
Razlozi Uvodjenja
- GEOGRAFSKA razudjenost ? 
- vestacko cepanje LAN-a radi smanjenja opterecenja
- rastojanje izmedju najudaljenih tacaka je vece nego sto to dopusta odgovaraju tehnologija
- neispravna stanica ometa samo lokalni saobracaj 
- veca sigurnost ( moze se zabraniti neki protok )

Explanations: 
- Uredjaji data link nivoa
	- pamti i prosledjuje Ethernet ramove
	- analizira zaglavlje rama i selektivno prosledjuje ram na osnovu odredisne MAC adrese
	- da bi prosledio ram koristi CSMA/CD da bi pristupio segmentu
- transparentni
	- hostovi nisu 'svesni' prisustva mostova
- plug-and-play, 
	- ne treba konfiguracija, radi out-of-the-box

Filtriranje Saobracaja
- Most dovodi do deljenja LAN mreze na jedinstvene segmente
- Filtriraju pakete
	- ramovi koji treba da se proslede lokalno se ne prosledjuju drugim lan-segmentima

Kreiranje tabela za prosledjivanje
- Svaki most poseduje tabelu ( bridge table ) koju stalno refresh-a
- vrsta tabele sadrzi
	- adresu LAN cvora, oznaku interface mosta, vreme kreiranja
	- stari zapisi u tabeli se brisu ( posle 60 min )
- mostovi 'uce' preko kog interface-a se moze pristupiti odredjenom hostu
- kada ram stigne u most, most 'uci' (saznaje) lokaciju posiljaoca ( izvora ), tj. detektuje sa kog LAN segmenta pristize ram
	- pravi zapis u tabeli : adresa i broj interface-a i vreme

Filtriranje i prosledjivanje
- Indeksira tabelu koriscenjem odredisne Mac adrese
```if: pronadjen zapis za dato odrediste
	then: { 
		if: odrediste u segmentu sa koga je ram dosao
			then: odbaciti ram
			else: proslediti ram na oznaceni interface 
			}
		else: proslediti primljeni ram na sve interface osim na onaj na koji je                dosao
			
		}
```

### Ethernet Switch-evi
- Mostovi sa vvecim brojem interface-a
- obavljaju prosledjivanje i filtriranje ramova na osnovu LAN adresa
- Switching: A-ka-A i B-ka-B simultano, bez kolizije
- Cesto se koristi za povezivanje individualnih hostova u topologiju tipa zvezda
- Ethernet, ali bez kolizije

#### Spanning tree

- Svaki most dobija jedinstveni identifikator ( ID ) 
- Jedinstveni ID za svaki port mosta
	- MAC adresa, ili
	- Bridge ID + port Number
- Koristi STP protokol za odredjivanje spreznog stabla
	- Mostovi medjusobno razmenjuju konfiguracione poruke - BPDU ( Bridge Protocol data units )

ALGORITAM
1. Od svih mostova odabrati koren - root bridge
2. Svaki most odredjuje svoj root port
	- to je port koji se nalazi na najkracem putu ka root bridge
3. Odrediti ovlasceni ( designated bridge ) za svaki LAN
	- ovaj most obezbedjuje LAN-u najkraci put do root bridge-a
	- prihvata ramove sa LAN i prosledjuje ih ka root bridge
	- ramovi se prosledjuju preko root porta ovlascenog mosta

Odredjivanje korena stabla
- Na pocetku svaki most misli da je on koren
	- svaki most salje svim svojim susedima konfiguracionu poruku ( BPDU - bridge protocol data unit )
	- Konfiguraciona poruka sadrzi
		- ID mosta za koji on smatra da je root
		- cenu puta od mosta do root-a
		- ID mosta koji salje poruku
		- ID porta sa koga se salje poruka
- Pobednik je most sa najmanji id-om

Poredjenje konfiguracionih poruka C1 i C2 
- Najveci prioritet za odlucivanje root-a je cena puta.
- U slucaju da je cena ista, C1 > C2 ako je oznaka Root ID C1 manja od C2
- Ako je Root ID i cena rastojanja isti, onda se gleda ID mosta mosta koji salje poruku
- Ako su root ID , cena i ID mosta koji prenosi poruku, onda se gleda port ID
