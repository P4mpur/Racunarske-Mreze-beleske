# Kriptografija sa javnim kljucem 

_Basically nacin kriptovanja gde obe strane imaju kljuc (nacin) za desifrovanje podataka pri cemu jedna strana (strana koja salje poruku) sifrira poruku koristeci taj kljuc, a druga strana (primalac te poruke) desifruje tu poruku koristeci isti taj kljuc ( nacin )

**Postoje dve vrste kljuceva**
- Javni 
	- Javni kljuc basically delis sa cvorom s kojim zelis da razmenjujes poruke, koristi se za sifrovanje poruka i verifikaciju digittalnog potpisa, zato se i zvoe **key signature**
- Privatni
	- Poznat samo primaocu poruke pomocu koga ce se ta poruka desifrovati

## Koraci RSA algoritma
1. Odabrati dva velika prosta broja, `p` i`q`, veca od 10^100
2. Izracunati $$n=pq$$ i
  $$z=(p-1)(q-1)$$
3.  Izabrati broj `d` koji je relativno prost u odnosu na `z`
4. naci `e` tako da vazi $$ (e * d)mod(z)=1 $$
5. Podeliti tekst koji treba da se sifrira ( plaintext ) na blokove velicine `k` bitova, pri cemu je `k` najveci broj za koji vazi  n>2^k
	- npr ako je n = 20, tada je k=4 jer 20 > 16 = 2^4
6. Sifrirovana poruka se dobija na osnovu $$ C = (P^e)Modn $$
7. Da bi se desifrovala poruka potrebno je izracunati $$ P = (C^d)mod n $$
## Digitalni sertifikati

- Ako koristite sistem sifrovanja javnim kljucem i zelite da nekom posaljete poruku, morate prvo dobiti njegov javni kljuc.
	Problem nastaje kada shvatite da ne mozete da budete sigurni da je to stvarno njegov kljuc
- Zato se koriste digitalni sertifikati ( licne karte )

**Kako to funkcionise**
CA - Certificate Authority
- Bob podnosi zahtev za izdavanje sertifikata CA kompaniji
