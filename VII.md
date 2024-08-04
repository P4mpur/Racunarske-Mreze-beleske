# Mrezni nivo u internetu

**Protokoli mreznog nivoa**
- IP
- Upravljacki 
	- ICMP
	- ARP
	- DHCP
- Protokoli za rutiranje
	- RIP

**IP datagram - format zaglavnja, nast.**
- Sastoji se od dva dela: 20-byte fiksnog dela i opciono polje promenljive duzine do 40 byte max
- Prenosi se u Big-Endian redosledu - a byte order or endianness where the **most significant byte** (the “big end”) is stored at the **smallest memory address**
**IP zaglavlje**
- Polje verzija ( 4 bits ) :
	- verzija IP protokola kojoj datagram pripada.
- polje duzina zaglavlja ( IHL - 4 byte) : 
	- govori o velicini zaglavlja u 32-bitnim recima. Minimalna vrednost je 5, max je 15.
- Tip Servisa ( 8 bits ) 
	- dozvoljava hostu da kaze podmrezi koji tip usluge zeli.
		- Prva tri byte-a sadrze prioritet datagrama ( od 0 (normalni,http) do 7 (upravljacki)))
		- tri bita: D
			- D - Delay
			- T Troughput
			- R  Reliability
		- poslednja dva bita se ne koriste
- Poslje ukupna duina ( 16 bits ) 
	- definise velicinu celog datagrafma u baytovima ukljucujuci i zaglavlje
- Polje identifikacija ( 16 bits ) 
	- u slucaju da se vrsi fragmentacija datagrama, omogucava hostu da utvrdi kom datagramu pripada fragment koji je pristigao
- Polje DF - don't fragment ( 1 bit ) 
	- naredjenje ruteru da ne vrsi fragmentiranje
- polje mf - more fragments ( 1 bit ) 
	- jos fragmenta
- Polje fragment offset ( 13 bits )
	- redni broj fragmenta u datagramu.
	- Svaki fragmment, osim poslednjeg mora biti umnozak od 8 byte-a 
	- jedan datagram moze imati max 8192 fragmenta
- Polje vreme zivota ( TTL ) ( 8 bits ): brojac koji se koristi da ogranici vreme zivota paketa, da paketi ne bi beskonacno kruzili kroz mrezu.
- polje protokol: 
	- Koristi se kada kompletan datagram stigne u odrediste i kazuje kom protokolu transportnog nivoa je datagram namenjen
- Polje kontrola zaglavlja ( 16 bits ) 
- sluzi za detekciju greske u zaglavlju
- polja adresa izvora i odredista
	- 32 bitne adrese 
- polje opcije ( max 40 byte-a): svaka opcija pocinje 1-bajtnim kodom koji identifikuje opciju ( uglavnom se koriste u debbugging purpose)
	- Ima pet opcija
		- Security
		- striktno rutiranj - daje kompletan put od izvorado odredista kao niz IP adresa
		- dirigovano rutiranje - zahteva se d apaket obaezno prodje preko rutera cije su adrese navedene
		- record route - svaki ruter preko koga se prodje mora da upise svoju ip adresu
		- time stamp - svai ruter mora da upise svoju adresu i vreme prolaska

**Ip fragmentiranje i reasembliranje**
primer : 
4000 byte datagram
MTU = 1500 bytes
datagram:
	duzina  =4000 ID =x fraglag=0 offset=0
	20 byte-a je zaglavlje

1. duzina = 1500, ID = x , fragflax=1, offset=0
2. duzina = 1500, ID = x, fragflag=1, offset=1480/ 8 
	- Ako se ne varam trazis umnozak do 8 koji je celi broj kad se podeli sa 8, idk what i said, mozda jer i 20 byte-a zaglavlje
	- basically 1500%8 nije 0, ali 1480 jeste, pa vrv to ima veze
3. duzina= 1040, ID=x, fragflag=0, offset=370
	- Duzina je 1040 jer imas dva puta 20 zaglavlja koja su uzeta kod 1. i 2. 

## NAT
**ULOGA:** 
- Daje mogucnost da se jedna cela mreza uveze u jednu 'izlaznu' ip adresu koja ce da komunicira sa spoljnim svetom ( uglavnom )
**Posao:**
- Za odlazne datagramea
	- zameni izvorisnu ip adresu svakog oldaznog datagrama sa nati ip-jem
	zapamti sve translacije sa adresa izvorista na nat ip adresu
- za dolazne datagrame
	- zameni nat ip adresu u poljima odredista svakog dolazeceg datagrama sa odgovarajucom izvorisnom ip adresom uskladistenim u nat tabeli


## Internet upravljacki protokoli

**Protokoli po nivoima**
- layer 5 - > 
	- Telnet, FTP, SMTP, SNMP
- layer 4 -> 
	- TCP UDP
- layer 3 -> 
	- IP, ARp , ICMP
- layer 2 ->
	- ETHERNET, FDDI, ATM ,Frame Relay
- layer 1 
	- Copper wire, fiber optic

#### ICMP ( Internet Control Message Protocol)

- Sluzi za detekciju gresaka koju IP nema u bezkonekcionom servisu
- Koriste ga hostovi i ruter iza razmenu upravljackih paketa
- nije deo ip-a, vec se prenosi preko ip-a, nivo iznad
- primer:
	- Kada se izvrsava telnet, ftp, http, moze se primiti poruka  "destination network unreachable" - to je ICMP poruka

**Format poruke**
- Sve ICMP poruke su razlicite, osim prva 4 bajta
	- Type (8 bits)
	- Code ( 8 bits )
		- daje dodatne informacije o tipu poruke
	- Checksym ( 16 bits )

**Pitanje sa blanketa**
- Koje dve poruke moze da se posalju u trace komandi
	- time exceeded
	- trace complete

#### ARP protokol ( Adress resolution protocol )
- ip adrese se ne mogu koristiti za slanje paketa jer data link nivo ne razume IP aadrese
- ARP protokol omogucava preslikavanje ip adresa u adrese data link nivoa
- Sablon : WHo has xxx.xxx.xxx.xxx adress, time se izbegava pamcenje ip adresa u nekim tablicama kako bi moglo da se prenose podaci preko ethernet protokola


##### DHCP ( Dynamic Host Configuration Protocol )
- Omogucava da se ip adresa dodeli hostu automatski
- Klijent-server protokol
	- klijent je host koji trazi ip adresu i ostala podesavanja za mrezu
	- ako dhcp server ne postoji u datoj podmrezi, onda se korsiti dhcp agent koji  zna adresu dhcp servera za datu ip mrezu

**Kako funkcionise**
- Host salje DHCPDiscover poruku koja se prenosu po ip protokolu
	- host za destination postavlja broadcast adresu (255.255.255.255), a adresu izvora 0.0.0.0 i salje se njegova mac adresa
	- U slucaju da postoji dhcp server u mrezi, on mu daje adresu, u suprotnom koristi se dhcp agent
- Kada dhcp primi `DHCPDiscover` poruku odovara sa `DHCPOFFER` gde se nalazi
	- ip adresa, subnet maska
	- adresa gateway rutera
	- vreme vazenja ip  adrese
- Klijent bira ponundu i  vraca `DHCPREQUEST` sa kopijom `DHCPOFFER`
- Server nakon toga salje `DCHPACK` nakon cega se ip adresa vezuje za host

### RIP protokol za rutiranje
- Zasniva se na distance vector [[VI#Distance vector routing]] algoritmu rutiranja
**Protokoli za rutiranje** 
- Interior
	- RIP
	- OSPF
- Exterior
	- BGP

**Algoritam** :
```
1. **Add one hop to the hop count for each advertised destination:**
    
    - When a RIP message is received, the hop count for each destination listed        in the message is increased by one. This is because the message is coming        from a neighbor, so you need to account for the hop from your router to          that neighbor.
2. **Repeat the following steps for each advertised destination:**
    
    - For each destination in the received RIP message, the algorithm will check       whether to update the router’s own routing table.
        
    - **If the destination is not in the routing table:**
        
        - If the destination network isn’t currently in the router’s routing               table, the router adds it with the information (hop count and next               hop) received from the neighbor.
    - **Else:**
        
        - If the destination is already in the routing table, the router will              compare the existing information with the newly received information.
            
        - **If the next-hop field is the same:**
            
            - If the next hop (i.e., the neighbor that sent the update) is the                 same as the existing entry, the router replaces the existing entry               with the new one. This ensures that the router keeps the most                    current information.
        - **Else:**
            
            - If the next hop is different, the router needs to determine                      whether the new route is better.
                
            - **If the advertised hop count is smaller than the one in the                     table:**
                
                - If the hop count in the received message is smaller (i.e., a                     shorter or better path), the router updates its table with                       the new information.
            - **Else, do nothing:**
                
                - If the new path is not better (i.e., it has a higher hop                       count), the router ignores the update and keeps the existing                     entry.
3. **Return:**
    
    - Once all the destinations in the received RIP message have been processed, the algorithm completes, and the router's routing table is updated accordingly.
```

#### RIP format poruke
- Command : 
	- zahtev ili 
		- Poruka koju salje ruter koji se prvi put javlja u mrezi ili nakon nekog vremena
	- odgovor
		- **Odgovor na zahtev** : salje samo kao odgovor na request i sadrzi informaciju o zahtevanioj destinaciji specificiranoj u zahtevu
		- **regularni odgovor** : salje se na 30 sek i sadrzi se informacije, tj kompletnu tabelu rutiranja koji je salje

- Version
- Family : za TCP/IP vrednost je uvek 2
- Adress : adresa odredisne mreze
- Distance : broj skokova od rutera do odredisne mreze
  maksimalna vrednost je 15. vrednost 16 kaze da mreza nije sigurna
**Timers in RIP**
- Casovnik periode razmene poruka  (periodic timer)
- Casovnik isteka ( expiration timer )
- (garbage collection timer)
	- Kada informacija o odredjenoj destinaciji postane nevalidna, ruter nastavlja da za datu destinaciju u tabeli saopstava da je broj skokova 16 narednih 120 sec
	- kada brojac dostigne vrednost 0, destinacija se brise iz tabele rutiranja

## IPv6

- Veci adresni prostor
- End to end povezivanje, svaki cvor moze dobiti jedinstvenu IP adresu bez koriscenja NAT
- mogucnost autokonfigurisanja
	- nije neophodan dhcp server

### Tipovi IPv6 adresa
- Unicast
	- adresa pojedinacnog interface-a
	- datagram se salje na oznacenu adresu
- Multicast
	- Adresa skupa interface-a
	- datagram se salje svim interface-ima u skupu
- Anycast
	- Adresa skupa interface-a
	- datagram se salje samo jednom u interface-u, obicno onom do koga je cena puta najmanja

Adresa se posmatra kao skup od 8 16-bitnih bloka
- svaki block se predstavlja sa 4 hex cifre

### PRomene u zaglavlju IPv6 u odnou na IPv4
**Poboljsanja**
- fragmentiranje uklonjeno iz osnovnog zaglavlja
- IP opcije uklonjene iz zaglavlja
- Header Checksum uklonjeno
- Header length field uklonjeno
- Lenth field polje iskljucuje IPv6 header
**Modifikacije**
- Time to live Hop limit
- Protocol Next header
- Precedence & TOS, traffic class
- polje adresa prosireno sa 32 bits -> 128 bits
**Prosirenja**
- Flow label field dodato
