# Uvod 
## Osi Model Sistema
	# Mrezno zavisni
	1. Fizicki 
	2. Nivo veze ( data link )  
	3. Mrezni 
	 # Filter
	4. Transportni ( filter )
	 # Aplikativno zavisni
	5. Nivo sesije ( 'sastanka' )
	6. prezentacioni
	 7.aplikativni 

### F-je fizickog nivoa
- koji fizicki nivo predstavlja jedan koji nula
- koliko je trajanje jednog bitskog intervala
- koliko pinova ima na konektorui cemu koji sluzi
- Koliko zica (linija ) ima prenosni medijum
- da li su signali koji se prenose elektricni ili opticki

### F-je nivoa veze ( Data link nivoa )
- Bavi se pouzdanim prenosom podataka izmedju dva cvora u mrezi 
- bavi se problemom narusavanja gubljenja ili dupliciranja poruka
- utvrdjuje granice svakog okvira jer fizicki nivo samo prenosi sekvencijalno niz bitova
- regulisanje brzine prenosa

### Mrezni Nivo 
- Upravlja radom komunikacione podmreze ( pouzdana komunikacija izmedju dva hosta)
	-  odreedjivanje puta poruka kroz mrezu ( na osnovu tablica )
	- Kontrola zagusenja
	- adresiranje ( identifikacija masina )
- Na putu od izvora do odredista paket moze da prolazi kroz visr mreza:
	-Problemi:
	- Na putu od izvora do odredista, paket moze da prolazi kroz vise mreza:
	-Problemi:
	- Nacini adresiranja u razlicitim mrezama mogu biti razliciti
	- protokoli koji se koriste mogu biti razliciti
- Mrezni nivo mora da prevazidje sve ove probleme i omoguci povezivanje heterogenih mreza

### Transportni nivo
- Pouzdana komunikacija izmedju aplikativnih procesa
- ? skriva ogranicenja komunikacione podmrexze od aplikativno zavisnih nivoa ? 
	- (Explanation) sposobnost transportnog nivoa da pruza uslugu aplikacijama tako da one ne moraju da se bave specificnostima i ogranicenjima u nivou mreze, kao sto su mrezni i fizicki nivo
- Segmentiranje poruka
- adresiranje ( koji proces u hostu )
- retransmisija poruka ( ako je to potrebno )
- otkrivanje duplikata ( ako je to potrebno )

### Nivo Sesije
- Upravljanje dijalogom ( token management )
	- Sesija moze dozvoliti saobracaj u oba smera u isto vreme ili u jednom smeru u jednom trenutku ( vodi racuna ko ima pravo da prenosi poruke )
- Sinhronizacija
	- Ako konekcija dugo traje, vrlo je verovatno da ce doci do prekida
	- Nivo sesije obezbedjuje ubacivanje kontrolnih tacaka ( check points ) u podatke koji se prenose, Ako nastupi prekid, nastavak prenosa ide od poslednje kontrolne tacke, a ne od pocetka
### Prezentacioni Nivo
- Bavi se Sintaksom podataka koji se prenose kroz mrezu
	- Aplikativni procesi koji se izvrsavaju na razlicitim hostovima mogu koristiti razlicite nacine predstavljanja podatka ( npr. EBCDIC ili ASCII)
	- Da bi komunikacij amogal da se obavi mora postojat izajednicki sintaksni oblik koji oba procesa razumeju
- Na prezentacionom nivou se definise zajednicki sintaksni oblik i obavlja se konverzija iz jednog u drugi sintaksni oblik
	- kompresija podataka
	- sifriranje podataka

### Aplikativni Nivo
- Pruza podrsku razlicitim korisnickim aplikacijama tj. pruza korisnicki interfejs za usluge koje pruza mreza, kao sto su: 
	- e-email
	- file transfer
	- udaljeni login
	- web
- Identifikacija korisnika po imenu

## TCP/IP referentni model
- () Kada je osmisljen OSI model, mnogo vremena je izgubljeno na standardizaciji i na pravljenju dokumentacije, protokoli su veoma spori, a slozeni , a prva verzija TCP/IP-a je bila veoma uspesna

4. Aplikativni ( ekvivalentan OSI aplikativnom i prezentacionom)
3. HOST-TO-HOST (transportni)
2. Internet ( mrezni )
1. Fizicki i nivo mreze
#### Application ( aplikativni )
- Servisi  ( protokoli )
	- Telnet
	- FTP
	- SMTP
	- DNS
#### Transport ( Transportni )
- Servisi ( protokoli )
	- TCP
	- UDP
#### Network ( mrezni )
- Servisi ( protokoli  )
	- IP
#### Mrezni i Fizicki Nivo
 -  Mreze
	 - ARPANET
	 - SATNET
	 - PACKET RADIO
	 - LAN

### Razlike izmedju TCP i OSI

###### TCP
- Ima 4 nivoa
- nema stroge granice izmedju pojedinih nivoa
- TCP/IP funkcije aplikativnog, nivoa sesije i prezentacionog nivoa su implementirane na aplikativnom nivou
- protokoli se ne mogu lako zameniti u tcp/ip modelu
###### OSI
- ima 7 nivoa 
- Ima stroge granice izmedju nivoa 
- ima poseban nivo sesije i prezentacije 
- protokoli se mogu lakse zameniti, sve dok interfejs ostaje isti

Vredno napomene, tehnicki tcp/ip model ne postoji

### Hibridni model 
	Protokol         Protokolske jedinice podataka
5. Aplikativni                  Poruke ( message )
4. transportni                 segment
3. mrezni                         datagram
2. nivo veze                     Ram, okvir ( frame )
1. fizicki nivo                    1 - PDU ( protocol data unit )



