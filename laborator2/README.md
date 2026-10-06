# Laboratorul 2 – Cuantifică citirile Dashboard-ului

## Scop

Scopul laboratorului este transformarea comportamentului definit pentru Personal Investment Dashboard în cerințe măsurabile de calitate, estimări ale volumului de citiri, estimări de stocare și identificarea posibilelor blocaje ale sistemului.

Punctul de pornire este Dashboard-ul definit anterior, în care utilizatorul autentificat reprezintă actorul uman direct, iar Market Data Provider reprezintă sistemul extern.

Citirile analizate sunt:

- Overview;
- Filter;
- Stock price;
- History;
- Watchlist;
- Search.

---

# 1. Cerințe de calitate

În această secțiune sunt transformate așteptările clientului în obiective care pot fi măsurate și verificate.

## 1.1 Latența citirilor

Măsurarea latenței începe în momentul în care Dashboard-ul primește cererea utilizatorului și se termină atunci când rezultatul complet este disponibil utilizatorului.

### Cerință generală

În perioadele aglomerate:

- cel puțin 95% dintre citirile corecte trebuie să se finalizeze în maximum **2 secunde**;
- o citire care depășește 2 secunde este considerată o încălcare a țintei de latență.

Aceasta poate fi exprimată astfel:

```text
p95 latency <= 2 s
```

Pentru `Stock price`, deoarece clientul dorește ca prețurile să pară imediate, se adoptă o țintă mai strictă:

```text
p95 Stock price latency <= 500 ms
```

Un răspuns rapid care indică faptul că datele sunt indisponibile poate respecta cerința de latență, dar nu este considerat un rezultat corect al datelor.

---

## 1.2 Disponibilitate

Dashboard-ul este considerat disponibil atunci când utilizatorul autentificat poate efectua citirile principale și poate primi un rezultat valid sau o stare explicită de indisponibilitate.

Pentru acest laborator se consideră că programul normal de tranzacționare este:

```text
09:30 – 16:00 Eastern Time
```

Se aleg următoarele ținte:

| Interval | Țintă disponibilitate |
|---|---:|
| Ore de tranzacționare | 99,99% |
| Restul zilei | 99,9% |

Disponibilitatea este mai strictă în timpul orelor de tranzacționare deoarece în această perioadă informațiile despre prețuri au cea mai mare importanță pentru utilizator.

### Buget de indisponibilitate

Presupunem o perioadă de măsurare de 30 de zile cu aproximativ 22 de zile de tranzacționare.

Ore de tranzacționare:

```text
22 zile × 6,5 ore = 143 ore
```

Pentru o disponibilitate de 99,99%:

```text
143 h × 0,01% = 0,0143 h
                  ≈ 0,858 minute
                  ≈ 51,5 secunde
```

Bugetul de indisponibilitate este de aproximativ:

```text
52 secunde / 30 zile
```

în timpul orelor de tranzacționare.

Restul timpului:

```text
30 × 24 = 720 ore

720 - 143 = 577 ore
```

Pentru 99,9% disponibilitate:

```text
577 h × 0,1% = 0,577 h
              ≈ 34,62 minute
```

Bugetul de indisponibilitate pentru restul zilei este de aproximativ:

```text
34,6 minute / 30 zile
```

---

## 1.3 Consistență

Pentru `Stock price`, fiecare rezultat trebuie să includă ora furnizorului.

Întârzierea normală estimată a furnizorului este de aproximativ **15 minute**.

Se adoptă următoarea regulă:

- date cu vechime de maximum **15 minute** – acceptate;
- date mai vechi de 15 minute, dar nu mai vechi de 30 de minute – afișate ca **întârziate**;
- date mai vechi de **30 de minute** – considerate **indisponibile**.

Un preț indisponibil nu trebuie afișat niciodată ca valoarea `0`.

### Consistența Watchlist-ului

După finalizarea cu succes a unei modificări a Watchlist-ului, următoarea citire efectuată de același utilizator trebuie să reflecte modificarea.

Exemplu:

Dacă utilizatorul adaugă `AAPL` în Watchlist și operația se finalizează cu succes, următoarea citire a Watchlist-ului trebuie să conțină `AAPL`.

`AAPL` este simbolul bursier al companiei Apple.

Un utilizator nu poate citi sau modifica Watchlist-ul altui utilizator.

---

## 1.4 Debit

Sistemul trebuie să poată procesa volumul de citiri calculat pentru fiecare nivel de utilizatori.

Pentru nivelul maxim analizat, Dashboard-ul trebuie să poată susține ținta calculată la deschiderea pieței, inclusiv marja de capacitate de 10%, fără încălcarea cerinței de latență.

---

# 2. Estimări RPS în regim stabil

RPS înseamnă **Requests Per Second**, adică numărul de cereri pe secundă.

Formula utilizată este:

```text
RPS = utilizatori concurenți
      × procent participanți
      × acțiuni per utilizator
      / secunde
```

Sunt analizate trei niveluri:

- 300 utilizatori;
- 3.000 utilizatori;
- 30.000 utilizatori.

---

## Overview

70% dintre utilizatori reîmprospătează la fiecare 30 de secunde.

```text
300 × 0,70 / 30 = 7 RPS

3000 × 0,70 / 30 = 70 RPS

30000 × 0,70 / 30 = 700 RPS
```

---

## Filter

50% dintre utilizatori modifică filtrul de 3 ori pe minut.

```text
300 × 0,50 × 3 / 60 = 7,5 RPS

3000 × 0,50 × 3 / 60 = 75 RPS

30000 × 0,50 × 3 / 60 = 750 RPS
```

---

## Stock price

20% dintre utilizatori solicită prețul o dată pe secundă.

```text
300 × 0,20 = 60 RPS

3000 × 0,20 = 600 RPS

30000 × 0,20 = 6000 RPS
```

---

## History

20% dintre utilizatori solicită istoricul o dată la 5 minute.

5 minute:

```text
5 × 60 = 300 secunde
```

Calcul:

```text
300 × 0,20 / 300 = 0,2 RPS

3000 × 0,20 / 300 = 2 RPS

30000 × 0,20 / 300 = 20 RPS
```

---

## Watchlist

60% dintre utilizatori reîmprospătează o dată pe minut.

```text
300 × 0,60 / 60 = 3 RPS

3000 × 0,60 / 60 = 30 RPS

30000 × 0,60 / 60 = 300 RPS
```

---

## Search

10% dintre utilizatori caută de 3 ori pe minut.

```text
300 × 0,10 × 3 / 60 = 1,5 RPS

3000 × 0,10 × 3 / 60 = 15 RPS

30000 × 0,10 × 3 / 60 = 150 RPS
```

---

## Rezultatele în regim stabil

| Citire | 300 Utilizatori | 3.000 Utilizatori | 30.000 Utilizatori |
|---|---:|---:|---:|
| Overview | 7 | 70 | 700 |
| Filter | 7,5 | 75 | 750 |
| Stock price | 60 | 600 | 6.000 |
| History | 0,2 | 2 | 20 |
| Watchlist | 3 | 30 | 300 |
| Search | 1,5 | 15 | 150 |
| **Total în regim stabil** | **79,2 RPS** | **792 RPS** | **7.920 RPS** |

---

# 3. Estimări RPS la deschiderea pieței

La deschiderea pieței:

- 30% dintre utilizatori reîmprospătează Overview o dată într-un interval de 10 secunde;
- 60% dintre aceștia reîmprospătează și Watchlist;
- traficul suplimentar se adaugă traficului stabil;
- la final se aplică o marjă de capacitate de 10%.

## Flux Overview suplimentar

Formula:

```text
Utilizatori × 30% / 10 secunde
```

Calcul:

```text
300 × 0,30 / 10 = 9 RPS

3000 × 0,30 / 10 = 90 RPS

30000 × 0,30 / 10 = 900 RPS
```

---

## Flux Watchlist suplimentar

60% din grupul de 30%:

```text
Utilizatori × 0,30 × 0,60 / 10
```

Calcul:

```text
300 × 0,30 × 0,60 / 10 = 5,4 RPS

3000 × 0,30 × 0,60 / 10 = 54 RPS

30000 × 0,30 × 0,60 / 10 = 540 RPS
```

---

## Tabel final

| Calcul la deschiderea pieței | 300 Utilizatori | 3.000 Utilizatori | 30.000 Utilizatori |
|---|---:|---:|---:|
| Trafic stabil de citire | 79,2 | 792 | 7.920 |
| Flux suplimentar Overview | 9 | 90 | 900 |
| Flux suplimentar Watchlist | 5,4 | 54 | 540 |
| **Subtotal** | **93,6** | **936** | **9.360** |
| Marjă de capacitate 10% | 9,36 | 93,6 | 936 |
| Valoare cu marjă | 102,96 | 1.029,6 | 10.296 |
| **Țintă finală rotunjită în sus** | **103 RPS** | **1.030 RPS** | **10.296 RPS** |

Rotunjirea este aplicată numai rezultatului final.

---

# 4. Estimări de stocare

## 4.1 Definirea unui Stock

Pentru acest Dashboard, un `Stock` reprezintă un instrument de capital care oferă deținătorului o participație într-o companie.

Prima versiune urmărește acțiuni tranzacționate pe principalele două piețe americane:

- Nasdaq;
- NYSE.

Sunt excluse:

- ETF-uri;
- fonduri;
- instrumente derivate;
- criptomonede;
- obligațiuni.

Instrumentele inactive sau delistate nu vor putea fi adăugate ca instrumente noi în prima versiune.

Pentru estimarea capacității utilizăm aproximativ:

```text
Nasdaq: aproximativ 4.570
NYSE: aproximativ 2.400
```

Total:

```text
4.570 + 2.400 = 6.970 Stocks
```

Această valoare reprezintă o estimare pentru calculul capacității.

### Surse

Definiția Stock:

https://www.investor.gov/introduction-investing/investing-basics/investment-products/stocks

Nasdaq:

https://ir.nasdaq.com/

NYSE:

https://www.nyse.com/listings/why-nyse

---

# 4.2 Date sincronizate

## Date de referință

Pentru fiecare Stock sunt păstrate:

- symbol;
- company name;
- exchange;
- currency;
- instrument type;
- status.

Aceste informații sunt necesare pentru:

- Search;
- Filter;
- identificarea Stock-ului.

---

## Cel mai recent preț

Pentru fiecare Stock se păstrează:

- symbol;
- latest price;
- provider timestamp.

Aceste date sunt utilizate pentru:

- Overview;
- Stock price;
- Watchlist.

---

## Price history

Pentru istoric se păstrează:

- symbol;
- timestamp;
- open;
- high;
- low;
- close;
- volume.

Pentru prima versiune se presupune un interval de **15 minute** între punctele istorice.

Acest interval reduce cantitatea de date stocată și este suficient pentru un Dashboard destinat urmăririi generale a investițiilor, nu tranzacționării de foarte mare frecvență.

Programul normal al pieței este de aproximativ:

```text
6,5 ore
```

Transformăm în minute:

```text
6,5 × 60 = 390 minute
```

Având un punct la fiecare 15 minute:

```text
390 / 15 = 26 puncte pe Stock pe zi
```

Prin urmare:

```text
26 puncte / Stock / zi
```

Istoricul se păstrează pentru aproximativ:

```text
252 zile de tranzacționare
```

adică aproximativ un an.

---

# 4.3 Dimensiunea reprezentativă a înregistrărilor

## Date de referință

Exemplu:

```json
{
  "symbol": "AAPL",
  "name": "Apple Inc.",
  "exchange": "NASDAQ",
  "currency": "USD",
  "status": "active",
  "type": "common_stock"
}
```

Estimare:

```text
114 bytes / înregistrare
```

---

## Latest price

Exemplu:

```json
{
  "symbol": "AAPL",
  "price": 220.15,
  "providerTs": "2026-09-22T14:30:00Z"
}
```

Estimare:

```text
68 bytes / înregistrare
```

---

## Price history

Exemplu:

```json
{
  "symbol": "AAPL",
  "ts": "2026-09-22T14:30:00Z",
  "open": 219.8,
  "high": 220.4,
  "low": 219.75,
  "close": 220.15,
  "volume": 148250
}
```

Estimare:

```text
115 bytes / înregistrare
```

---

# 4.4 Calculul stocării

## Date de referință Stock

```text
6.970 × 114 bytes
= 794.580 bytes
≈ 0,76 MiB
```

---

## Cele mai recente prețuri

```text
6.970 × 68 bytes
= 473.960 bytes
≈ 0,45 MiB
```

---

## Price history pe zi

Avem:

```text
6.970 Stocks
26 puncte / Stock / zi
```

Numărul total de înregistrări pe zi:

```text
6.970 × 26
= 181.220 înregistrări / zi
```

Stocare brută pe zi:

```text
181.220 × 115 bytes
= 20.840.300 bytes
≈ 20,84 MB
≈ 19,87 MiB
```

---

## Price history pentru un an

Avem:

```text
6.970 Stocks
× 26 puncte pe zi
× 252 zile
```

Numărul de înregistrări:

```text
6.970 × 26 × 252
= 45.667.440 înregistrări
```

Stocare:

```text
45.667.440 × 115 bytes
= 5.251.755.600 bytes
```

În GB:

```text
5.251.755.600 / 1.000.000.000
≈ 5,25 GB
```

În GiB:

```text
5.251.755.600 / 1.073.741.824
≈ 4,89 GiB
```

Prin urmare, istoricul anual necesită aproximativ:

```text
5,25 GB
```

sau:

```text
4,89 GiB
```

---

## Tabelul estimării stocării

| Set de date | Decizia despre produs și perioada de păstrare | Calculul numărului de înregistrări | Octeți per înregistrare | Stocare brută |
|---|---|---:|---:|---:|
| Date de referință Stock | Stocks active | 6.970 | 114 | ~0,76 MiB |
| Cele mai recente prețuri | Ultimul preț per Stock | 6.970 | 68 | ~0,45 MiB |
| Price history | 15 minute, 252 zile | 45.667.440 | 115 | ~5,25 GB |
| Alte date selectate | Nu sunt necesare separat în v1 | 0 | 0 | 0 |
| **Total aproximativ** | | | | **~5,25 GB** |

Aceste valori reprezintă stocare brută.

Nu sunt incluse:

- indexuri;
- replicare;
- backup;
- metadate;
- alte costuri interne ale sistemului.

---

# 5. Posibile blocaje

În această secțiune sunt analizate posibilele puncte în care sistemul ar putea să nu mai respecte cerințele stabilite.

| Calitate | Posibil blocaj | Dovezi din laborator | Efect posibil | Ce trebuie măsurat |
|---|---|---|---|---|
| Latență | Citirile frecvente Stock price | Până la 6.000 RPS Stock price | Creșterea timpului de răspuns | p95 și p99 latency |
| Consistență | Date întârziate de la Market Data Provider | Furnizorul poate avea aproximativ 15 minute întârziere | Utilizatorul poate vedea informații vechi | Provider timestamp și vechimea datelor |
| Debit | Vârful de trafic la deschiderea pieței | 10.296 RPS la nivelul maxim | Cereri întârziate sau respinse | RPS, error rate și queue time |
| Disponibilitate | Dependența de Market Data Provider | Datele provin dintr-un sistem extern | Unele date pot deveni indisponibile | Disponibilitatea Dashboard-ului și furnizorului |

---

## Latență

Calea `Stock price` este cea mai solicitată.

Pentru 30.000 de utilizatori concurenți:

```text
6.000 RPS
```

Această valoare nu demonstrează existența unui blocaj, dar indică o zonă care trebuie testată.

Trebuie măsurate:

- p95 latency;
- p99 latency;
- rata de erori;
- timpul de răspuns la încărcare maximă.

---

## Consistență

Dashboard-ul depinde de Market Data Provider.

Dacă datele furnizorului sunt întârziate, Dashboard-ul poate răspunde rapid, dar informația prezentată poate fi prea veche.

Trebuie măsurată:

```text
current time - provider timestamp
```

pentru a determina vechimea informației.

---

## Debit

Ținta maximă calculată este:

```text
10.296 RPS
```

la deschiderea pieței pentru 30.000 de utilizatori concurenți.

Sistemul trebuie testat pentru a verifica dacă poate menține această sarcină fără să depășească țintele de latență și eroare.

---

## Disponibilitate

Market Data Provider reprezintă o dependență externă.

Dashboard-ul trebuie să diferențieze între:

- indisponibilitatea Dashboard-ului;
- indisponibilitatea datelor externe.

Dacă furnizorul nu oferă un preț valid, Dashboard-ul trebuie să prezinte:

```text
Preț indisponibil
```

și nu:

```text
0
```

Disponibilitatea Dashboard-ului și disponibilitatea furnizorului trebuie măsurate separat.

---

# Concluzie

În cadrul laboratorului au fost transformate cerințele generale ale Personal Investment Dashboard în obiective măsurabile.

Au fost definite cerințe pentru:

- latență;
- disponibilitate;
- consistență;
- debit.

Au fost analizate trei niveluri:

```text
300 utilizatori
3.000 utilizatori
30.000 utilizatori
```

Pentru nivelul maxim, traficul stabil estimat este:

```text
7.920 RPS
```

iar ținta de capacitate la deschiderea pieței este:

```text
10.296 RPS
```

Pentru Price history s-a ales un interval de **15 minute**, ceea ce produce:

```text
26 puncte / Stock / zi
```

Pentru aproximativ 6.970 Stocks și 252 de zile de tranzacționare rezultă:

```text
45.667.440 înregistrări
```

și aproximativ:

```text
5,25 GB
```

de date istorice brute pentru un an.

De asemenea, au fost identificate principalele zone care trebuie investigate pentru posibile probleme de latență, consistență, debit și disponibilitate.