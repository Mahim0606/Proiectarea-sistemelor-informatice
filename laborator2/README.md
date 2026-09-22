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

Pentru `Stock price`, deoarece clientul dorește ca prețurile să pară imediate, se adoptă o țintă mai strictă:

- 95% dintre citirile `Stock price` trebuie să se finalizeze în maximum **500 ms** în condiții normale de operare.

Un răspuns rapid care indică faptul că datele sunt indisponibile poate respecta cerința de latență, dar nu este considerat un rezultat corect al datelor.

---

## 1.2 Disponibilitate

Dashboard-ul este considerat disponibil atunci când utilizatorul autentificat poate efectua citirile principale și poate primi un rezultat valid sau o stare explicită de indisponibilitate.

Pentru acest laborator se consideră că programul normal de tranzacționare este:

**09:30 – 16:00 Eastern Time, în zilele de tranzacționare.**

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

Bugetul de indisponibilitate în timpul orelor de tranzacționare este de aproximativ **52 secunde în 30 de zile**.

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

Bugetul de indisponibilitate pentru restul zilei este de aproximativ **34,6 minute în 30 de zile**.

---

## 1.3 Consistență

Pentru `Stock price`, fiecare rezultat trebuie să includă ora furnizorului.

Întârzierea normală estimată a furnizorului este de aproximativ 15 minute.

Se adoptă următoarea regulă:

- date cu vechime de maximum **15 minute** – acceptate;
- date mai vechi de 15 minute, dar nu mai vechi de 30 de minute – afișate ca **întârziate**;
- date mai vechi de **30 de minute** – considerate **indisponibile**.

Un preț indisponibil nu trebuie afișat niciodată ca valoarea `0`.

### Consistența Watchlist-ului

După finalizarea cu succes a unei modificări a Watchlist-ului, următoarea citire efectuată de același utilizator trebuie să reflecte modificarea.

Exemplu:

Dacă utilizatorul adaugă `AAPL` în Watchlist și operația se finalizează cu succes, următoarea citire a Watchlist-ului trebuie să conțină `AAPL`.

Un utilizator nu poate citi sau modifica Watchlist-ul altui utilizator.

---

## 1.4 Debit

Sistemul trebuie să poată procesa volumul de citiri calculat pentru fiecare nivel de utilizatori.

Pentru nivelul maxim analizat, Dashboard-ul trebuie să poată susține ținta calculată la deschiderea pieței, inclusiv marja de capacitate de 10%, fără încălcarea cerinței de latență.

---

# 2. Estimări RPS în regim stabil

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

Pentru 300 utilizatori:

```text
300 × 0,70 × 1 / 30 = 7 RPS
```

Pentru 3.000:

```text
3000 × 0,70 / 30 = 70 RPS
```

Pentru 30.000:

```text
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

20% solicită o dată pe secundă.

```text
300 × 0,20 = 60 RPS

3000 × 0,20 = 600 RPS

30000 × 0,20 = 6000 RPS
```

---

## History

20% solicită o dată la 5 minute.

5 minute = 300 secunde.

```text
300 × 0,20 / 300 = 0,2 RPS

3000 × 0,20 / 300 = 2 RPS

30000 × 0,20 / 300 = 20 RPS
```

---

## Watchlist

60% reîmprospătează o dată pe minut.

```text
300 × 0,60 / 60 = 3 RPS

3000 × 0,60 / 60 = 30 RPS

30000 × 0,60 / 60 = 300 RPS
```

---

## Search

10% caută de 3 ori pe minut.

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
- 60% dintre acești utilizatori reîmprospătează și Watchlist;
- acest trafic se adaugă traficului stabil;
- la final se aplică o marjă de capacitate de 10%.

## Flux Overview suplimentar

Formula:

```text
Utilizatori × 30% / 10 secunde
```

Pentru 300:

```text
300 × 0,30 / 10 = 9 RPS
```

Pentru 3.000:

```text
3000 × 0,30 / 10 = 90 RPS
```

Pentru 30.000:

```text
30000 × 0,30 / 10 = 900 RPS
```

---

## Flux Watchlist suplimentar

60% din grupul de 30%.

Formula:

```text
Utilizatori × 0,30 × 0,60 / 10
```

Pentru 300:

```text
300 × 0,30 × 0,60 / 10 = 5,4 RPS
```

Pentru 3.000:

```text
3000 × 0,30 × 0,60 / 10 = 54 RPS
```

Pentru 30.000:

```text
30000 × 0,30 × 0,60 / 10 = 540 RPS
```

---

## Tabelul final

| Calcul la deschiderea pieței | 300 Utilizatori | 3.000 Utilizatori | 30.000 Utilizatori |
|---|---:|---:|---:|
| Trafic stabil de citire | 79,2 | 792 | 7.920 |
| Flux suplimentar Overview | 9 | 90 | 900 |
| Flux suplimentar Watchlist | 5,4 | 54 | 540 |
| **Subtotal** | **93,6** | **936** | **9.360** |
| Marjă 10% | 9,36 | 93,6 | 936 |
| Valoare cu marjă | 102,96 | 1.029,6 | 10.296 |
| **Țintă finală rotunjită în sus** | **103 RPS** | **1.030 RPS** | **10.296 RPS** |

Rotunjirea este aplicată doar rezultatului final.

---

# 4. Estimări de stocare

## 4.1 Definirea unui Stock

Pentru acest Dashboard, un `Stock` reprezintă un instrument de capital care oferă deținătorului o participație într-o companie.

Prima versiune va urmări acțiunile companiilor tranzacționate pe principalele două piețe americane:

- Nasdaq;
- NYSE.

Sunt excluse din prima versiune:

- ETF-urile;
- fondurile;
- instrumentele derivate;
- criptomonedele;
- obligațiunile.

Instrumentele inactive sau delistate nu vor putea fi adăugate ca instrumente noi în prima versiune.

Pentru dimensionare utilizăm o estimare de aproximativ:

```text
Nasdaq: 4.570 companii listate
NYSE: aproximativ 2.400 emitenți

Total aproximativ:
4.570 + 2.400 = 6.970 Stocks
```

Această valoare este utilizată ca estimare de capacitate și nu ca inventar exact al tuturor instrumentelor.

### Surse

Definiția Stock:

https://www.investor.gov/introduction-investing/investing-basics/investment-products/stocks

Nasdaq:

https://ir.nasdaq.com/

NYSE:

https://www.nyse.com/listings/why-nyse

Date consultate în septembrie 2026.

---

## 4.2 Date sincronizate

Pentru fiecare Stock sunt necesare următoarele informații.

### Date de referință

- symbol;
- company name;
- exchange;
- currency;
- instrument type;
- status.

Aceste informații sunt utilizate pentru:

- Search;
- Filter;
- identificarea Stock-ului.

### Cel mai recent preț

Se păstrează:

- symbol;
- latest price;
- provider timestamp.

Aceste date sunt necesare pentru:

- Overview;
- Stock price;
- Watchlist.

### Price history

Pentru istoric se păstrează:

- symbol;
- timestamp;
- open;
- high;
- low;
- close;
- volume.

Se presupune un interval de **1 minut** în timpul programului normal al pieței.

O zi de tranzacționare conține aproximativ:

```text
6,5 ore × 60 = 390 puncte
```

Se păstrează istoricul pentru aproximativ **252 zile de tranzacționare**, adică aproximativ un an.

---

## 4.3 Dimensiunea reprezentativă a înregistrărilor

Exemplu date de referință:

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

Pentru estimare folosim aproximativ **114 bytes** per înregistrare.

Exemplu latest price:

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

Exemplu price history:

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

## 4.4 Calculul stocării

### Date de referință

```text
6.970 × 114 bytes
= 794.580 bytes
≈ 0,76 MiB
```

### Latest prices

```text
6.970 × 68 bytes
= 473.960 bytes
≈ 0,45 MiB
```

### Price history pe zi

Număr înregistrări:

```text
6.970 × 390
= 2.718.300 înregistrări/zi
```

Stocare:

```text
2.718.300 × 115 bytes
= 312.604.500 bytes
≈ 298 MiB / zi
```

### Price history pentru un an

```text
6.970 × 390 × 252
= 685.011.600 înregistrări
```

Stocare:

```text
685.011.600 × 115 bytes
= 78.776.334.000 bytes
≈ 78,78 GB
≈ 73,37 GiB
```

Aceste valori reprezintă stocare brută și nu includ indexuri, replicare, backup, metadate sau alte costuri interne.

| Set de date | Decizia despre produs și perioada de păstrare | Număr înregistrări | Bytes / înregistrare | Stocare brută |
|---|---|---:|---:|---:|
| Date de referință Stock | Stocks active | 6.970 | 114 | ~0,76 MiB |
| Cele mai recente prețuri | Ultimul rezultat per Stock | 6.970 | 68 | ~0,45 MiB |
| Price history | 1 minut, ~252 zile | 685.011.600 | 115 | ~78,78 GB |
| Alte date selectate | Nu sunt necesare separat în v1 | 0 | 0 | 0 |
| **Total aproximativ** | | | | **~78,78 GB** |

---

# 5. Posibile blocaje

În această secțiune sunt analizate posibilele puncte în care sistemul ar putea să nu mai respecte cerințele stabilite.

| Calitate | Posibil blocaj | Dovezi din laborator | Efect posibil | Ce trebuie măsurat |
|---|---|---|---|---|
| Latență | Citirile frecvente Stock price | Până la 6.000 RPS Stock price în regim stabil | Creșterea timpului de răspuns peste 500 ms sau 2 s | p95/p99 latency pentru Stock price |
| Consistență | Date întârziate de la Market Data Provider | Furnizorul poate avea ~15 minute întârziere | Utilizatorul poate vedea informații vechi | Vechimea datelor și provider timestamp |
| Debit | Vârful de trafic la deschiderea pieței | 10.296 RPS reprezintă ținta maximă calculată | Sistemul poate refuza sau întârzia cereri | RPS susținut, error rate și queue time |
| Disponibilitate | Dependența de Market Data Provider | Prețurile și istoricul depind de sistemul extern | Unele rezultate pot deveni indisponibile | Disponibilitatea Dashboard-ului și a furnizorului separat |

## Latență

Calea `Stock price` este cea mai solicitată.

Pentru 30.000 de utilizatori concurenți aceasta generează:

```text
6.000 RPS
```

Acest lucru nu demonstrează că există deja un blocaj, dar indică faptul că această cale trebuie testată atent.

Trebuie măsurate:

- p95 latency;
- p99 latency;
- rata de erori;
- timpul de răspuns la încărcare maximă.

---

## Consistență

Dashboard-ul depinde de Market Data Provider pentru prețurile pieței.

Dacă datele furnizorului sunt întârziate, Dashboard-ul poate primi în continuare răspunsuri rapide, dar informațiile pot să nu mai fie suficient de actuale.

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

Sistemul trebuie testat la această sarcină pentru a verifica dacă poate menține latența și rata de erori în limitele stabilite.

---

## Disponibilitate

Market Data Provider este o dependență externă importantă.

Dashboard-ul trebuie să diferențieze:

- indisponibilitatea Dashboard-ului;
- indisponibilitatea datelor externe.

Dacă furnizorul nu oferă un preț valid, Dashboard-ul trebuie să prezinte starea ca indisponibilă și nu să afișeze valoarea `0`.

Trebuie măsurate separat disponibilitatea Dashboard-ului și disponibilitatea informațiilor furnizate de sistemul extern.

---

# Concluzie

În cadrul laboratorului au fost transformate cerințele generale ale Personal Investment Dashboard în obiective măsurabile.

Au fost definite cerințe pentru latență, disponibilitate, consistență și debit și a fost estimat volumul de trafic pentru 300, 3.000 și 30.000 de utilizatori concurenți.

Pentru nivelul maxim, traficul stabil estimat este de **7.920 RPS**, iar ținta de capacitate la deschiderea pieței este de **10.296 RPS**.

De asemenea, a fost estimată stocarea necesară pentru datele de piață și au fost identificate principalele zone care trebuie investigate pentru posibile probleme de latență, consistență, debit și disponibilitate.