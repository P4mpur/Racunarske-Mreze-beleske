### Mac Protokoli i Lokalne Mreze
- [ ] **Protokoli za emisione kanale : Dva tipa linkova** 
	- Point to Point
	- Broadcast ( deljivi medium )
	  
- [ ] **Protokoli za pristup emisionom kanalu**
	- Postoji samo jedan deljivi komunikacioni kanal
	- Problem je kako koordinisati komunikaciju i prenos izmedju njih
	- Moguce je da dodje do kolizije ( kada vise cvorova pokusava da pristupi )
	  
- [ ] **Idealni multiple access protokol ( MAC )**
	- [ ] Mac Protokoli : taksonomija
		- Protokoli sa podelom kanal ( Channel Partitioning )
			-- Podeliti kanal na manje 'delove' ( vremenske slotove, frekvencije , kodove)
			-- dodeliti 'parce' cvoru na eksluzivno koriscenje
		- Protokoli sa slucajnim pristupom 
			-- kanal nije podeljenje , dozvoljava koliziju
			-- 'oporavak' od kolizije
		- Taking turns protokoli ( sa definisanim redosledom pristupa ) - nema kolizije
			-- cvrsto koordinisani pristup
			
- [ ] **Mac Protokoli sa podelom kanala u vremenu TDMA** - time division multiple access
	- pristup se obavlja kruzno , mozda nesto slicno kao round robin
	- svaki cvor dobija  vremenski interval fiksne velicine, ako nema podatka za slanje, neiskoriscen je
	- Primer : lan sa 6 stanice , 1 3 4 imaju pakete 2 5 6 prazni
	
- [ ] **Mac protokoli sa podelom kanala u frekventnom domenu : FDMA** - frequency division multiple access
	- kanalni spektar podeljen na frekventne bandove sa fiksnim frekventim bandima , kao kod radio stanica
	- ako nema podatka za slanje, ne koristi se
	- primer : Lan sa 6-stanica 1,3,4 imaju pakete, frekventni opsezi 2,5,6
	  
- [ ] **Protokoli sa podelom kanala na osnovu kodova** (CDMA Code Division Multiple Access)
	- jedinstveni kod dodeljen svakom korisniku
	- Svi korisnici koriste istu frekvenciju ali imaju razlicitu kodnu sekvencu za sifriranje podataka
		- svaki bitski interval je podeljen na M kratkih intervala ( chips )
			-- obicno se koristi 64 ili 128 cipova po bitu
			-- da bi se prenela 1 stanica salje svoju kodnu sekvencu, S
			-- da bi se prenela 0 stanica salje se komplement od S
	- Kodirani Signal = (originalni podataka ) *x* ( kodna sekvenca )
	- dekodiranje = srednja vrednost unutrasnjeg proizvoda kodiranog signala i kodne sekvence
	- **ANALOGIJA :** komunikacija izmedju vise ljudi koji govore razlicite jezike 
	  
- [ ]  **Protokoli sa slucajnim pristupom ** - random access protocols
	- Kada cvor ima paket za slanje
		-- Nikakve a priori koordinacije izmedju cvorova nema 
		-- ako dva ili vise cvoroa prenosi -l 'kolizija'
	- protokol sa proizvoljnim pristupom prenosnom medijumu definise :
		-- kako se detektuje kolizija 
		-- kako se vrsi oporavak od kolizije
	- **Primeri** random access MAC protokola
		-- ALOHA
		-- slotted ALOHA
		-- CSMA, CSMA/CD
#### Aloha 
	Jednostavan nema sinhronizacije
	kada stanica ima ram za slanje, odma pokusava da ga prense
	velika verovatnoca nastanka kolizija
 - [ ] Aloha Protokol - nacin rada
	 - svi hostovi koriste istu frekv. za slanje do centralnog cvora
	 - nema provere da li je kanal zauzet pre pocetka slanja
	 - ako je paket stigao korektno do centralnog cvora, centralni cvor salje potrdu ali na drugoj frekvenciji
	- [ ] **DOLAZI DO KOLIZIJE** u slucaju da vise hostova prenosi u isto vreme
		- kada se desi kolizija centralni host ne salje potvrdu, a host ceka random vreme pre ponovnog slanja zahteva
#### Slotted Aloha 
	zasniva se na pretpostavkama
- [ ] Pretpostavke : 
	- svi ramovi su iste velicine
	- vreme je podeljeno na slotove jednake velicine
	- cvorovi pocinju sa slanjem ramova samo na pocetku slota
	- cvorrovi su sinhronizovani 
	- ako 2 ili vise cvorova prenose uistom slotu, svi cvorovi detektuju koliziju
- [ ] Rad protokola
	- Kada cvor dobije novi ram prenosi ga u sledecem  slotu
	- ako nema kolizije, cvor moze poslati novi ram u sledecem slotu
	- ako postoji kolizija, cvor pokusava sa retransmisijom u sledecem slotu sa verovatnocom p, sve dok ne uspe da posalje ram
	- neophodna je sinhronizacija da bi se znalo kada pocinje novi slot
		-- posebna stanica emituje bip signal na pocetku svakog intervala
- [ ] Dobro : 
	- jedan aktivni cvor moze kontinualno prenositi punom brzinom kanlaa
	- decentralizovan samo slotovi u cvoju zahtevaju sinhronizaciju 
	- jednostavam
- [ ] Lose : 
	- kolizije, gubitci slotova 
	- neupotrebljeni slotovi
	- cvorovi mogu detektovati koliziju za krace vreme nego sto je potrebno za prenos paketa 
	- losa efikasnost u najboljem slucaju 37%.

#### Osluskivanje nosioca signala CSMA - carrier sense multiple access

- [ ] CSMA - osluskuj kanal pre pocetka slanja ^f5a396
	- ako je kanal slobodan, prenosi se ceo ram
	- ako je kanal zauzet, odlozi prenos
	- **ANALOGIJA U LJUDSKOJ KOMUNIKACIJI: ** ne prekidaj drugog dok govori 
	- Kolizija ipak moze da se desi :
		-- zbog propagacionog kasnjenja dva cvora mogu da ne detektuju aktivnost onog drugog
- [ ] Colission detection
	- osluskivanje nosioca, odlaganje u slucaju zauzetosti kod CSMA
		-- kolizije se detektuju u kratkom vremenu
		-- u slucaju detekcije kolizije prenos se obustavlja
	- detekcija kolizije
		-- laka u slucaju zicanih LANova
		-- otezano kod bezicnih LANova
#### Struktura Ethernet okvira (frame)
- [ ] sve ethernet tehnologije imaju istu strukturu okvira![[Pasted image 20240514002849.png]] Preambula : 
      7 bajtova oblika 10101010 koji se koriste da se sinhronizuju brzine prenosa izvora i odredista
      SFD - start of Frame delimiet 10101011 ( pocetak rama )
- [ ] DA i SA , adrese izvora i odredista : po 6 bajtova 
	- globalno jedinstvena adresa dodeljena od strane proizvodjaca mrezne kartice 
- [ ] Type : 2 bita 
	- Ukazuje na protokol viseg nivoa, uglavnom IP
	- vrednosti iznad 0600 su rezervisane za kodove protokola
- [ ] data : polje podataka velicine 0-1500 byte
- [ ] Pad nule koje se dodaju da bi se postigla minimalna velicina okvira, od 0-46 bte (da bi se detektovala kolizija)
- [ ] CRC : za detekciju gresaka

- [ ] Minimalna velicina Ethernet rama je 64 byte bez preambule i SFD polja, a maksimalna 1518 byte. 
- [ ] Minimalna veličina data polja je 46 byte, a max 1500byte

#### Nepouzdani, bezkonekcioni servis 

- [ ] Bezkonekcioni servis : nema handshaking procedure iznedju izvornog i odredisnog adaptera
- [ ] Nepouzdani : prijemni adapter ne salje ack ili nak ramove izvornom adapteru
	- datagrami prosledjeni mreznom nivou mogu imati praznine
	- praznine ce biti popunjene ako aplikacija koristi tcp na transportnom nivou
	- u suprotnom, praznine ce biti vidljive aplikaciji

##### Ethernet koristi CSMA[[#^f5a396]] protokol za pristup prenosnom medijumu
Algoritam
- [ ] adapter prihvata datagram sa mreznog nivoa i kreira ram
- [ ] adapter osluskuje kanal i ako ustanovi da je free pocinjse sa prenosom
- [ ] ako adapter prenese ceo ram bez kolizije, uspesno je okoncao prenos
- [ ] ako adapter detektuje koliziju, prestaje sa slanjem rama i  salje Jam '[[#^c87675]]' sekvencu
- [ ] Posle detektovanja kolizije adapter ulazi u **Eksponencijalno povlacenje**
	- adapter ceka k\*512 bitskih intervala i bira slucajno izmedju 1 i 2^i-1 , posle 16 uzastopnih kolizija dostize se  attempt limit i caoooo.

- [ ] Jam Signal : obezbedjuje da svi ostali adapteri detektuju koliziju ^c87675
- [ ] bit time : 0.1 microsec za 10 Mbps ethernet za k = 1023, vreme cekanja iznosi oko 50ms

#### Povezivanje Lan segmenata
	Sta vazi za mostove vazi i za switcheve
	
- [ ] Hub
	- omogucava izolovanje Lan segmenta pomocu backbone hub-a
	- problem : kolizija u jednom segmentu se detektuje u celoj mrezi
	- ako cvorovi u dva razlicita segmenta otpocnu prenos u isto vreme dolazi do kolizije 
	- Ne mogu se povezati dva segmenta sa razlicitim brzinama prenosa
- [ ] Most 
	- Uredjaji data link nivoa
		-- pamti i prosledjuje ethernet ramove 
		-- analizira zaglavlje rama i selektivno prosledjuje ra mna osnovu odredisne mac adrese
		-- da bi prosledio ram koristi CSMA/CD  da bi pristupio segmentu 
	- hostovi nisu svesni prisustva mostova
	- plug and play samouceci uredjaju
	- Omogucava da se dodeli jedinstveni kolizioni domen
	  
	- Mostovi sadrze bridge table (ip tables ) koju stalno azurira 
		-- adresa LAN cvora , oznaku interfeisa mosta, vremek reiranja
		-- stati zapisi u tabeli se brisu posle 1h
	- 
- [ ] Switch 
	- MOSTOVI NA STEROIDI BEZ KOLIZIJE
	- obavljaju prosledjivanje, filtriranje ramova na osnovu LAN adresa

SPANNING TREE SABLON IAKO CE TI TREBA NE TREBA SAD DA GA CITAS