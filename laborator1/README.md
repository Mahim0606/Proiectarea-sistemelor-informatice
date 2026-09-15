# Laboratorul 1 – Definește produsul inițial

## Personal Investment Dashboard

### Scop

Scopul laboratorului este definirea primei versiuni a unui **Personal Investment Dashboard**, fără proiectarea arhitecturii interne sau alegerea tehnologiilor.

Cererea inițială este:

> Ajută-mă să îmi urmăresc investițiile.

Această cerere este prea generală. Pentru definirea produsului trebuie stabilite părțile interesate, actorii, obiectivele, cerințele și limitele sistemului.

---

# 1. Cercetarea produsului

## Întrebarea de cercetare

**Cum ajută produsele existente un utilizator să urmărească informațiile despre piață și ce funcționalități ar trebui să aparțină primei versiuni a Dashboard-ului?**

Pentru cercetare au fost analizate două produse existente:

- Google Finance
- TradingView

| Produs | Utilizatorul probabil și obiectivul său | Model reutilizabil |
|---|---|---|
| Google Finance | Investitor individual care dorește să urmărească active și informații despre piață | Watchlist pentru urmărirea activelor, prețurilor și schimbărilor |
| TradingView | Investitor sau trader care dorește să urmărească investițiile și performanța acestora | Portofoliu, watchlist și urmărirea performanței |

## Dovezi

### Google Finance

Google Finance permite utilizatorului să creeze liste de monitorizare și să urmărească diferite titluri de valoare.

Sursă:

https://support.google.com/websearch/answer/7579076

### TradingView

TradingView permite crearea unui portofoliu, inclusiv manual, și afișează valoarea portofoliului și performanța investițiilor.

Sursă:

https://www.tradingview.com/support/solutions/43000760937-tradingview-portfolios-track-your-assets-know-your-trades/

TradingView oferă și watchlist-uri pentru urmărirea activelor.

Sursă:

https://www.tradingview.com/support/solutions/43000745825-mastering-the-tradingview-watchlists/

## Decizia rezultată din cercetare

În urma cercetării, prima versiune a **Personal Investment Dashboard** va permite:

- introducerea manuală a investițiilor;
- urmărirea activelor deținute;
- vizualizarea valorii portofoliului;
- vizualizarea câștigului sau pierderii;
- crearea unei liste de active urmărite;
- utilizarea informațiilor actuale despre piață.

Prima versiune nu va permite tranzacționarea și nu se va conecta automat la broker.

---

# 2. Părți interesate și actori

## Părți interesate

| Parte interesată | Motivație | Influență | Motiv |
|---|---|---|---|
| Investitor individual | Ridicată | Ridicată | Este utilizatorul principal al Dashboard-ului |
| Proprietarul produsului | Ridicată | Ridicată | Decide funcționalitățile și domeniul produsului |
| Furnizorul datelor de piață | Scăzută | Ridicată | Furnizează informațiile necesare despre active |
| Autoritățile de reglementare | Scăzută | Ridicată | Pot influența regulile privind informațiile financiare |
| Broker | Scăzută | Scăzută | Nu este conectat direct în prima versiune |

## Matrice motivație–influență

| Motivație | Influență scăzută | Influență ridicată |
|---|---|---|
| Ridicată | - | Investitor individual, Proprietarul produsului |
| Scăzută | Broker | Furnizorul datelor de piață, Autoritățile de reglementare |

## Clasificarea actorilor

| Candidat | Clasificare | Apare în C4 System Context |
|---|---|---|
| Investitor individual | Actor uman direct | Da |
| Furnizor de date despre piață | Sistem extern | Da |
| Proprietarul produsului | Parte interesată | Nu |
| Autorități de reglementare | Parte interesată | Nu |
| Broker | Parte interesată | Nu |

Actorul principal al sistemului este **Investitorul individual**.

Sistemul extern principal este **Market Data Provider**, care furnizează informații despre piață.

---

# 3. Promisiunea și domeniul de aplicare ale produsului

## Promisiunea produsului

**Personal Investment Dashboard ajută investitorul individual să urmărească investițiile și evoluția activelor sale, astfel încât să poată înțelege rapid valoarea și performanța portofoliului său.**

## Obiective

1. Permite investitorului să introducă manual activele pe care le deține.

2. Permite investitorului să vadă valoarea actuală a portofoliului său.

3. Permite investitorului să vadă câștigul sau pierderea investițiilor.

4. Permite investitorului să urmărească active pe care încă nu le deține.

5. Informează utilizatorul atunci când informațiile despre piață lipsesc sau sunt învechite.

## Obiective excluse

1. Dashboard-ul nu permite cumpărarea sau vânzarea activelor.

2. Prima versiune nu conectează automat conturile de brokeraj.

3. Prima versiune nu oferă recomandări automate despre cumpărarea sau vânzarea investițiilor.

---

# 4. Cerințe funcționale

## DASH-1 – Adăugarea unei investiții

### Obiectivul actorului

Investitorul trebuie să poată înregistra o investiție pe care o deține.

### Povestea de utilizator

**Ca investitor, vreau să adaug un activ pe care îl dețin, astfel încât acesta să fie inclus în portofoliul meu.**

### Definițiile de Făcut

- Investitorul poate specifica activul și cantitatea deținută.
- Investiția apare în portofoliul utilizatorului.
- Investitorul poate specifica informațiile necesare pentru determinarea performanței.
- Dacă activul nu este acceptat, utilizatorul este informat clar.

---

## DASH-2 – Vizualizarea valorii portofoliului

### Obiectivul actorului

Investitorul trebuie să afle cât valorează investițiile sale.

### Povestea de utilizator

**Ca investitor, vreau să văd valoarea actuală a portofoliului meu, astfel încât să înțeleg situația investițiilor mele.**

### Definițiile de Făcut

- Este prezentată valoarea curentă a investițiilor.
- Utilizatorul poate vedea câștigul sau pierderea.
- Este indicat momentul ultimei actualizări a datelor.
- Dacă datele lipsesc sau sunt învechite, acest lucru este indicat clar.

---

## DASH-3 – Urmărirea performanței

### Obiectivul actorului

Investitorul trebuie să înțeleagă performanța fiecărei investiții.

### Povestea de utilizator

**Ca investitor, vreau să văd performanța fiecărui activ pe care îl dețin, astfel încât să identific investițiile care au crescut sau au scăzut în valoare.**

### Definițiile de Făcut

- Pentru fiecare investiție este prezentată valoarea disponibilă.
- Investitorul poate vedea câștigul sau pierderea.
- Fiecare activ poate fi analizat separat.
- Dacă informațiile lipsesc, utilizatorul este informat.

---

## DASH-4 – Lista de urmărire

### Obiectivul actorului

Investitorul trebuie să poată urmări active pe care nu le deține încă.

### Povestea de utilizator

**Ca investitor, vreau să adaug active într-o listă de urmărire, astfel încât să le pot monitoriza înainte de a lua o decizie.**

### Definițiile de Făcut

- Utilizatorul poate adăuga un activ în lista de urmărire.
- Utilizatorul poate elimina un activ.
- Sunt prezentate cele mai recente informații disponibile.
- Dacă informațiile nu sunt disponibile, această stare este indicată clar.

---

## DASH-5 – Actualizarea portofoliului

### Obiectivul actorului

Investitorul trebuie să poată menține portofoliul conform investițiilor sale reale.

### Povestea de utilizator

**Ca investitor, vreau să modific sau să elimin o investiție, astfel încât portofoliul să reflecte investițiile mele actuale.**

### Definițiile de Făcut

- Investitorul poate modifica informațiile unei investiții.
- Investitorul poate elimina o investiție.
- Valoarea portofoliului reflectă modificările.
- Modificarea unei investiții nu modifică alte investiții.

---

# 5. C4 System Context View

Diagrama prezintă actorul principal, sistemul analizat și sistemul extern cu care acesta interacționează.

```mermaid
flowchart LR

    User["👤 Investitor individual<br/>Person"]

    Dashboard["Personal Investment Dashboard<br/>Software System<br/><br/>Permite urmărirea investițiilor,<br/>valorii și performanței portofoliului"]

    Market["Market Data Provider<br/>External System<br/><br/>Furnizează informații<br/>despre piață"]

    User -->|"Adaugă și actualizează investiții<br/>și urmărește portofoliul"| Dashboard

    Dashboard -->|"Solicită informații<br/>despre active"| Market

    Market -->|"Furnizează prețuri și<br/>informații despre piață"| Dashboard