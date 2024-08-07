# Mrezni Nivo
- Primarni interface izmedju host-a i komunikacione podmreze
- zaduzen za prenost paketa izmedju dva host-a
- osnovne funkcije
	- rutiranje paketa
	- sprecava zagusavanje

- Konekcioni Servis  - obicno implementiran preko virtuelnogo
	- pre pocetka slanje podataka, salje se specijalni paket CALL REQUEST kojim se uspostavlja veza i odredjuje put izmedju izvornog i odredisnog hosta
		- paket sadrzi adresu izvora i adresu odredista i referentni broj ( logicki broj virtuelnog kanala)
		- kada se veza uspostavi svi paketi koriste isti put od izvora do odredista
		- paketi podataka ne sadrze adrese izvora i odredista vec samo broj Virtuelni channel ( VC )
	-  svaki ruter sadrzi tablicu u kojoj pamti redne brojeve VC koji su otvoreni preko tog rutera
		- odakle je stigao paket
		- sa kojim rednim brojem VC
		- po kom kom. kanalu se salje
		- sa kojim broj VC se salje
	- Kada se uspostvalja VC bira se najmanji neiskorisceni broj VC za dati kom. kanal i utiskuje u paket
		- broj VC koji je utisnut u paket u izvoru ne mora da bude isti kao broj sa kojim pristize u odrediste 
		- VC se brise i log broj se oslobadja kada se okonca razmena podataka

- Bezkonekcioni servis
	- nema inicijalnog uspostavljanja veze
	- svaki paket se salje nezavisno od prethodnog i sadrzi punu adresu izvora i odredista
	- paketi mogu putovati razlicitim putevima od ivora do odredista i pristizati u odredistee van redosleda
	- kontrolu gresaka i sekvenciranje obavlja transportni nivo

## Rutiranje paketa
- Odredjivane puta paketa kroz mrezu obavlja se na osnovu tablica koje se nalaze u svakom ruteru

### Algoritmi za rutiranje
- Princip optimalnosti
- Odredjivanje najkraceg puta
- Bujica
- Distance vector routing
- Link state rutiranje
- Hijerarhijsko rutiranje

- Algoritmi za rutiranje
	- Neadaptivni algoritmi - ne menjaju se 
	- Adaptivni - menjaju se 

- Aloritmi statickog rutiranja
	- Dijkstring algoritam
		- na pocetku svi cvorovi imaju oznaku (inf, -)m izuzev izvornog cvora
		- zatim se odredjuje rastojanje od izvora do najblizih suseda i labela se menja
		- bira se labela sa najmanjom vrednoscu rastojanja i proglasava za stalnu
		- zatim se za tu laeblu utvrdjuju susedi sa privremenim labelama i za njih se odredjuuje novo rastojanje
		- algoritam se okoncava kada labela odredista postane stalna
	- Bujica 
		- Svaki pristigli paket u ruter se salje po svim linijama izuzev po onoj kojoj je pristglo
		- Postoji brojac skokova koji sprecava beskonacno skakanje paket
		- postavljen 
			- uglavnom na duzinu puta od izvora do odredista
			- ili realno na dijametar mreze
- Adaptivni algoritmi
	- Distance vector routing
	- Link State routing

## Distance vector routing
- svaki ruter ima tabelu sa vrstom za svaki drugi ruter u mrezi. Svaka vrsta sadrzi:
	- najpozeljniju liniju za datu destinaciju
	- procenu rastojanja do te destinacije
- Izracunavanje nakon razmene tablica
	- Pretpostavimo da je ruter `y` upravo dobio tablicu rutiranja od suseda `x`
		- neka `xi` oznacava `x`-ovu cenu kasnjenja do rutera
	- Neka kasnjenje od `y` do `x` iznosi `m` msec
	- Ruter `y` zna kasnjenje do `x` na osnovu ECHO paketa
		-  Zakljucuje da do ruter `i` preko rutera `x` moze stici za `xi + m` msec
	- obavi ovakva izracunavanja za sve svoje susede 
	- najmanje kasnjenje do `i`ce definisati najpozeljniju izalznu liniju 
	- stara tabela se ne koristi u izracunavanju

## Link state routing 
- Koraci LS algoritma
	- otkriti svoje susede i ustanoviti njihove mrezne adrese
	- izmeriti kasnjenje do svakog suseda
	- konstruisati paket koji sadrzi sve sto znas
	- poslati paket svim ruterima
	- izracunati najbolji put do svih rutera

- Otkrivanje suseda se vrsi tako sto kada se ruter boot-uje on posalje svima `HELLO` paket po svim point-to-point linijma i ocekuju odgovor od drugih suseda koji sadrzi IP adresu suseda
- Kasnjenje se meri ECHO packet-om

- Distribucija Link State Paket-a 
	- Primaju se tabele i potvrdjuju  se medjusobno UVEK
	- Ako se primi tabele sa rednim brojem koji je nizi od vec idjenog odbacuje se
	- Koristi se modul, tako  da ako se dodje do najveceg broja ( opseg od 0 do 65540 ) i pocne se ispocetka, ruter ce da odbaci tabelu jer ce da misli da je stara
	- to se resava tako  sto se postavi age u tabelu, age je tehnicki broj koji se dekrementira pri svakom prolasku kroz svaki ruter 
	- Kada se prime sve tabele sad moze da se iskoristi 'Dijkstring' Algoritam u svakom ruteru da se pronadje najkraci put do odredista