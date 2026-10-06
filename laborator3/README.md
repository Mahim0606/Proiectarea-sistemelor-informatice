# Laboratorul 3 – Desenează limita Dashboard-ului

## Scop

Scopul laboratorului este proiectarea limitelor și structurii unui **Personal Investment Dashboard** folosind diagrame Mermaid.

Dashboard-ul permite unui utilizator autentificat să:

- răsfoiască Stocks;
- filtreze Stocks;
- caute Stocks;
- citească prețuri;
- citească price history;
- administreze un Watchlist privat.

Sistemul extern **Market Data Provider** furnizează date despre piață, dar poate:

- returna date invalide;
- limita numărul de cereri;
- deveni temporar indisponibil.

Proiectarea trebuie să respecte volumul de lucru analizat în Laboratorul 2.

---

# 1. Diagrama System Context

Diagrama System Context prezintă sistemul ca un întreg și relațiile sale cu actorul uman și sistemul extern.

Nu sunt prezentate componentele interne ale Dashboard-ului.

```mermaid
flowchart LR

    User["Utilizator autentificat<br/>Person"]

    Dashboard["Personal Investment Dashboard<br/>Software System<br/><br/>Permite răsfoirea și filtrarea Stocks,<br/>citirea prețurilor și istoricului<br/>și administrarea unui Watchlist privat"]

    Provider["Market Data Provider<br/>External System<br/><br/>Furnizează informații despre Stocks,<br/>prețuri și price history"]

    User -->|"Răsfoiește, caută și filtrează Stocks;<br/>citește prețuri și istoric;<br/>administrează Watchlist-ul"| Dashboard

    Dashboard -->|"Primește date sincronizate<br/>despre piață"| Provider
```

## Explicație

**Utilizatorul autentificat** este actorul uman direct al sistemului.

**Personal Investment Dashboard** este sistemul analizat.

**Market Data Provider** este sistemul extern care furnizează datele despre piață.

Dashboard-ul trebuie să gestioneze situațiile în care furnizorul:

- returnează date invalide;
- limitează cererile;
- nu răspunde;
- furnizează date întârziate.

---

# 2. Diagrama Container

Diagrama Container deschide limita sistemului și prezintă principalele părți de execuție și stocările necesare.

```mermaid
flowchart LR

    User["Utilizator autentificat"]

    Provider["Market Data Provider<br/>External System"]

    subgraph Dashboard["Personal Investment Dashboard"]

        App["Dashboard Application<br/><br/>Procesează cererile utilizatorului,<br/>verifică identitatea și accesul,<br/>servește Stocks, prețuri, istoric<br/>și Watchlist"]

        Collector["Market Data Collector<br/><br/>Sincronizează și validează<br/>datele de la furnizor"]

        Scheduler["Sync Scheduler<br/><br/>Pornește sincronizarea<br/>periodică a datelor"]

        MarketStore[("Market Data Store<br/><br/>Stocks, latest prices<br/>și price history")]

        WatchlistStore[("Watchlist Store<br/><br/>Watchlist-uri private<br/>ale utilizatorilor")]

    end

    User -->|"Cereri Dashboard"| App

    App -->|"Citește Stocks,<br/>prețuri și istoric"| MarketStore

    App -->|"Citește și modifică<br/>Watchlist-ul utilizatorului"| WatchlistStore

    Scheduler -->|"Pornește sincronizarea"| Collector

    Collector -->|"Solicită date despre piață"| Provider

    Provider -->|"Returnează date sau eroare"| Collector

    Collector -->|"Salvează numai date validate"| MarketStore
```

## Responsabilități

### Dashboard Application

Este responsabilă pentru:

- validarea cererilor utilizatorului;
- verificarea identității;
- verificarea drepturilor de acces;
- Search;
- Filter;
- Overview;
- Stock price;
- History;
- Watchlist.

### Market Data Collector

Este responsabil pentru:

- comunicarea cu Market Data Provider;
- validarea datelor primite;
- respingerea datelor invalide;
- păstrarea ultimelor date acceptate atunci când o sincronizare eșuează;
- salvarea datelor valide în Market Data Store.

Datele de autentificare pentru Market Data Provider sunt păstrate pe server și nu sunt expuse utilizatorului.

### Sync Scheduler

Pornește periodic procesul de sincronizare.

### Market Data Store

Păstrează:

- informații despre Stocks;
- latest prices;
- provider timestamp;
- price history.

### Watchlist Store

Păstrează Watchlist-urile private ale utilizatorilor.

---

# 3. Diagrama Component

Diagrama Component deschide numai containerul **Dashboard Application**.

```mermaid
flowchart LR

    User["Utilizator autentificat"]

    MarketStore[("Market Data Store")]

    WatchlistStore[("Watchlist Store")]

    subgraph App["Dashboard Application"]

        Access["Identity & Access Component<br/><br/>Verifică identitatea utilizatorului,<br/>datele de intrare și permisiunile"]

        MarketRead["Market Read Component<br/><br/>Gestionează Overview, Search,<br/>Filter, Stock price și History"]

        Watchlist["Watchlist Component<br/><br/>Citește și modifică<br/>Watchlist-ul privat"]

    end

    User -->|"Trimite cerere"| Access

    Access -->|"Cerere validată pentru<br/>date despre piață"| MarketRead

    Access -->|"Cerere validată pentru<br/>Watchlist"| Watchlist

    MarketRead -->|"Citește Stocks,<br/>prețuri și history"| MarketStore

    Watchlist -->|"Citește și modifică<br/>Watchlist"| WatchlistStore

    MarketRead -->|"Returnează rezultat"| User

    Watchlist -->|"Returnează rezultat"| User
```

## Componente

### Identity & Access Component

Responsabil pentru:

- identificarea utilizatorului autentificat;
- validarea datelor de intrare;
- verificarea permisiunilor;
- respingerea accesului neautorizat.

### Market Read Component

Responsabil pentru:

- Overview;
- Search;
- Filter;
- Stock price;
- price history;
- verificarea vechimii datelor înainte de prezentare.

### Watchlist Component

Responsabil pentru:

- citirea Watchlist-ului;
- adăugarea unui Stock;
- eliminarea unui Stock;
- verificarea faptului că utilizatorul modifică doar propriul Watchlist.

---

# 4. Diagrame de secvență

## A. Sincronizează datele de piață

Această diagramă prezintă sincronizarea datelor de la Market Data Provider.

```mermaid
sequenceDiagram

    participant Scheduler as Sync Scheduler
    participant Collector as Market Data Collector
    participant Provider as Market Data Provider
    participant Store as Market Data Store

    Scheduler->>Collector: Pornește sincronizarea

    Collector->>Provider: Solicită date despre piață

    alt Furnizorul returnează date valide

        Provider-->>Collector: Date despre Stocks și prețuri

        Collector->>Collector: Validează datele

        Collector->>Store: Salvează datele acceptate

        Store-->>Collector: Salvare reușită

        Collector-->>Scheduler: Sincronizare finalizată

    else Furnizorul returnează date invalide

        Provider-->>Collector: Date invalide

        Collector->>Collector: Detectează datele invalide

        Collector-->>Scheduler: Sincronizare respinsă

        Note over Collector,Store: Datele acceptate anterior rămân disponibile

    else Timeout sau rate limit

        Provider--xCollector: Timeout / rate limit

        Collector-->>Scheduler: Sincronizare eșuată

        Note over Collector,Store: Datele acceptate anterior nu sunt șterse

    end
```

### Ipoteze

- Sincronizarea este inițiată periodic de **Sync Scheduler**.
- Datele de autentificare pentru Market Data Provider sunt păstrate pe server.
- Datele sunt validate înainte de salvare.
- Datele invalide nu înlocuiesc datele valide existente.
- Dacă furnizorul nu răspunde sau limitează cererile, ultima versiune acceptată a datelor rămâne disponibilă.
- Dashboard-ul trebuie să poată marca aceste date ca întârziate dacă depășesc limita acceptată.

---

## B. Citește prețul unui Stock

Această diagramă prezintă citirea prețului unui Stock de către utilizator.

```mermaid
sequenceDiagram

    participant User as Utilizator autentificat
    participant Access as Identity & Access Component
    participant Market as Market Read Component
    participant Store as Market Data Store

    User->>Access: Solicită prețul unui Stock

    Access->>Access: Verifică identitatea
    Access->>Access: Validează identificatorul Stock

    alt Identitate sau date de intrare invalide

        Access-->>User: Cerere respinsă

    else Cerere validă

        Access->>Market: Citește Stock price

        Market->>Store: Solicită latest price

        alt Preț valid și suficient de actual

            Store-->>Market: Preț + provider timestamp

            Market->>Market: Verifică vechimea datelor

            Market-->>User: Preț + ora furnizorului

        else Preț lipsă

            Store-->>Market: Niciun preț disponibil

            Market-->>User: Preț indisponibil

        else Date mai vechi decât limita acceptată

            Store-->>Market: Preț vechi + timestamp

            Market->>Market: Detectează date învechite

            Market-->>User: Date întârziate sau indisponibile

        else Defectare Market Data Store

            Store--xMarket: Eroare de citire

            Market-->>User: Date indisponibile temporar

        end

    end

    Note over User,Store: Citirea utilizatorului nu solicită direct date noi de la Market Data Provider
```

### Ipoteze

- Utilizatorul trebuie să fie autentificat.
- Identificatorul Stock este validat înainte de citirea datelor.
- Citirea utilizatorului este servită din **Market Data Store**.
- Citirea unui Stock de către utilizator nu generează o cerere nouă către Market Data Provider.
- Sincronizarea cu furnizorul este realizată separat de Market Data Collector.
- Un preț lipsă este prezentat ca **indisponibil**, nu ca `0`.
- Dacă datele sunt mai vechi decât limita acceptată, utilizatorul este informat.

---

## C. Modifică un Watchlist privat

Această diagramă prezintă modificarea Watchlist-ului privat al utilizatorului.

```mermaid
sequenceDiagram

    participant User as Utilizator autentificat
    participant Access as Identity & Access Component
    participant Watchlist as Watchlist Component
    participant Store as Watchlist Store

    User->>Access: Adaugă sau elimină Stock din Watchlist

    Access->>Access: Verifică identitatea
    Access->>Access: Validează datele de intrare
    Access->>Access: Verifică permisiunea

    alt Utilizator neautorizat

        Access-->>User: Acces respins

    else Utilizator autorizat

        Access->>Watchlist: Trimite modificarea validată

        Watchlist->>Store: Modifică Watchlist-ul utilizatorului

        alt Salvare reușită

            Store-->>Watchlist: Modificare persistentă

            Watchlist-->>User: Modificare finalizată cu succes

            Note over User,Store: Succesul este raportat numai după confirmarea salvării

            User->>Access: Citește din nou Watchlist-ul

            Access->>Watchlist: Cerere validată

            Watchlist->>Store: Citește Watchlist

            Store-->>Watchlist: Watchlist actualizat

            Watchlist-->>User: Returnează noua stare

        else Defectare Watchlist Store

            Store--xWatchlist: Salvarea eșuează

            Watchlist-->>User: Modificarea nu a fost finalizată

        end

    end
```

### Ipoteze

- Fiecare Watchlist aparține unui singur utilizator.
- Utilizatorul poate modifica numai propriul Watchlist.
- Identitatea, datele de intrare și permisiunea sunt verificate înainte de modificare.
- Succesul este raportat numai după ce Watchlist Store confirmă salvarea.
- Dacă salvarea eșuează, sistemul nu raportează operația ca reușită.
- După o modificare reușită, următoarea citire făcută de același utilizator trebuie să afișeze noua stare a Watchlist-ului.

---

# Relația cu Laboratorul 2

Proiectarea din acest laborator trebuie să poată susține volumul de lucru calculat anterior.

Pentru nivelul maxim analizat în Laboratorul 2:

```text
30.000 utilizatori concurenți
```

Traficul estimat în regim stabil a fost:

```text
7.920 RPS
```

iar ținta calculată pentru deschiderea pieței, inclusiv marja de capacitate, a fost:

```text
10.296 RPS
```

Aceste valori reprezintă ținte de proiectare și testare, nu demonstrează singure existența unui blocaj.

---

# Concluzie

În cadrul Laboratorului 3 a fost definită arhitectura Personal Investment Dashboard la trei niveluri.

**System Context** prezintă relația dintre utilizator, Dashboard și Market Data Provider.

**Container Diagram** separă responsabilitățile principale între Dashboard Application, Market Data Collector, Sync Scheduler și stocările de date.

**Component Diagram** detaliază Dashboard Application prin componente pentru identitate și acces, citirile datelor despre piață și gestionarea Watchlist-ului.

Cele trei diagrame de secvență verifică proiectarea pentru:

1. sincronizarea datelor de piață;
2. citirea prețului unui Stock;
3. modificarea unui Watchlist privat.

Fluxurile includ atât cazurile normale, cât și date invalide, indisponibilitatea furnizorului, date învechite, acces neautorizat și defectări ale stocării.