# Metody Obliczeń Symbolicznych i Numerycznych
## Ćwiczenie 2: Analiza profilu przekroju poprzecznego bieżni łożyska kulkowego

> **Środowisko:** Wolfram Mathematica 13.3  
> **Plik danych:** `pw6205.txt` (4245 punktów pomiarowych)  
> **Czas:** 2 × 45 min  
> **Wymagania wstępne:** znajomość MATLAB/Python, podstawy Mathematica (zajęcia 1)

---

## SEKCJA 1 – Teoria

### 1.1 Profilografometr – co to jest i jak działa?

Wyobraź sobie, że chcesz zmierzyć, jak bardzo okrągła jest bieżnia łożyska. Nie możesz tego zrobić linijką – odchyłki są rzędu **mikrometrów (µm)**. Do tego służy **profilografometr**.

```
         ┌─────────────────────────────────────────┐
         │         PROFILOGRAFOMETR                │
         │                                         │
         │    czujnik (igła stykowa)               │
         │         │                               │
         │    ┌────▼────┐                          │
         │    │  ramię  │──► przesunięcie          │
         │    └────┬────┘                          │
         │         │                               │
         │    ┌────▼────────────────┐              │
         │    │   BIEŻNIA ŁOŻYSKA  │               │
         │    └────────────────────┘               │
         │                                         │
         │  Wynik: chmura punktów (x, y) w µm      │
         └─────────────────────────────────────────┘
```

**Zasada działania:**
- Igła stykowa obraca się wokół osi łożyska
- Rejestruje odchylenia od idealnego okręgu
- Wynik: **4245 punktów** `(x, y)` w mikrometrach


---

### 1.2 Co analizujemy? – dwa parametry

```
         Profil rzeczywisty (wyolbrzymiony)

              ╭──╮    ╭─╮
         ────╯    ╰──╯   ╰────   ← profil rzeczywisty
         ─────────────────────   ← idealny okrąg (MNK)

         │←── odchyłka okrągłości ΔR ──→│

         Chropowatość (mikrostruktura):
         ┌──────────────────────────────┐
         │  ╭╮  ╭╮  ╭╮  ╭╮  ╭╮  ╭╮   │  ← szczyty
         │──╯╰──╯╰──╯╰──╯╰──╯╰──╯╰──  │  ← linia średnia
         │                              │  ← doliny
         └──────────────────────────────┘
              Ra = średnie odchylenie
              Rz = 5 szczytów - 5 dolin
```

| Parametr | Co mierzy | Typowe wartości |
|----------|-----------|-----------------|
| **ΔR** (odchyłka okrągłości) | Makrogeometria – jak bardzo profil odbiega od okręgu | 1–100 µm |
| **Ra** (chropowatość) | Mikrogeometria – średnia "szorstkość" powierzchni | 0.1–10 µm |
| **Rz** (chropowatość) | Mikrogeometria – różnica szczytów i dolin | 1–50 µm |

---

### 1.3 Metoda Najmniejszych Kwadratów (MNK) – dopasowanie okręgu

#### Problem: jak znaleźć "najlepszy" okrąg?

Mamy 4245 punktów `(xᵢ, yᵢ)`. Szukamy okręgu o środku `(xc, yc)` i promieniu `R`, który **najlepiej pasuje** do tych punktów.

**Kryterium MNK** – minimalizujemy sumę kwadratów odchyleń:

```
         N
f(xc, yc, R) = Σ  (√((xᵢ-xc)² + (yᵢ-yc)²) - R)²  →  min
        i=1
```

> **Analogia:** To jak szukanie środka tarczy do rzutek, gdy masz 4245 dziurek. Chcesz znaleźć punkt, od którego średnia odległość do wszystkich dziurek jest jak najbardziej "równa" – czyli minimalizujesz rozrzut odległości.

#### Dlaczego MNK, a nie np. okrąg przez 3 punkty?

```
         3 punkty → jeden okrąg (wrażliwy na szum)

         ●──────────────●
          \            /
           \          /
            ●        ← jeden z 4245 punktów

         MNK → okrąg "uśredniony" po wszystkich punktach
         (odporny na szum pomiarowy)
```


#### Jak to liczymy w Mathematica?

W Mathematica używamy `FindMinimum`

`FindMinimum[f, {{x, x0}, {y, y0}, {z, z0}}]` – minimalizuje funkcję `f` względem zmiennych `x`, `y`, `z` startując od punktów `x0`, `y0`, `z0`.

---

### 1.4 Parametry chropowatości Ra i Rz

Po wyznaczeniu okręgu MNK obliczamy **odchylenia radialne**:

```
rᵢ = √((xᵢ - xc)² + (yᵢ - yc)²) - R
```

To jest "profil chropowatości" – jak bardzo każdy punkt odstaje od dopasowanego okręgu.

#### Ra – średnie arytmetyczne odchylenie profilu

```
        1   N
Ra = ─── · Σ |rᵢ - r̄|
        N  i=1

gdzie r̄ = Mean[r]  (linia średnia)
```

```
         Profil odchyleń radialnych:

    +3µm │  ╭╮    ╭╮  ╭╮
    +2µm │ ╭╯╰╮  ╭╯╰╮╭╯╰╮
    +1µm │╭╯  ╰╮╭╯  ╰╯  ╰╮
     0µm ├─────────────────── ← linia średnia r̄
    -1µm │                 ╰╮
    -2µm │                  ╰╯
         └──────────────────────→ punkt pomiarowy

         Ra = średnia wartość |rᵢ - r̄|
```

#### Rz – wysokość chropowatości (norma ISO)

```
Rz = średnia(5 najwyższych szczytów) - średnia(5 najgłębszych dolin)
```

```
         Rz:
    +5µm │  ●  ●           ← 5 najwyższych szczytów
    +4µm │     ●  ●  ●
         │
    -3µm │              ●  ← 5 najgłębszych dolin
    -4µm │           ●  ●
    -5µm │  ●  ●

         Rz = Mean[{5,5,4,4,4}] - Mean[{-3,-4,-4,-5,-5}]
```

---

### 1.5 Mathematica vs Python/MATLAB – kluczowe różnice

| Cecha | Python/MATLAB | Mathematica |
|-------|---------------|-------------|
| Indeksowanie | `data[0]`, `data(1)` | `data[[1]]` (od 1!) |
| Kolumna danych | `data[:,0]` | `data[[All,1]]` |
| Pętla | `for i in range(n)` | `Table[..., {i,1,n}]` |
| Suma | `np.sum(x)` | `Total[x]` |
| Wartość bezwzględna | `np.abs(x)` | `Abs[x]` |
| Minimalizacja | `scipy.optimize.minimize` | `FindMinimum` |
| Wykres punktowy | `plt.plot(x,y)` | `ListLinePlot[data]` |
| Pierwiastek | `np.sqrt(x)` | `Sqrt[x]` |

> ⚠️ **Najważniejsza różnica:** W Mathematica **nawiasy kwadratowe `[]`** to wywołanie funkcji. Nawiasy okrągłe `()` służą tylko do grupowania wyrażeń matematycznych!

---

## SEKCJA 2 – Zadania dla studentów

### Blok 1 – Wczytanie i wizualizacja danych (25 min)

#### 🟢 Kod demonstracyjny (live coding)

```mathematica
(* Krok 1: Ustaw katalog roboczy – odpowiednik os.chdir() w Pythonie *)
SetDirectory[NotebookDirectory[]];

(* Krok 2: Wczytaj dane – odpowiednik np.loadtxt() *)
data = Import["pw6205.txt", "Table"];

(* Krok 3: Sprawdź wymiary – odpowiednik data.shape w Pythonie *)
Dimensions[data]
(* Oczekiwany wynik: {4245, 2} *)
```

```mathematica
(* Krok 4: Wizualizacja profilu – dane w mm (dzielimy przez 1000) *)
p = ListLinePlot[data/1000,
  AspectRatio -> Automatic,
  AxesLabel -> {"x [mm]", "y [mm]"},
  PlotLabel -> "Profil przekroju bieżni łożyska pw6205",
  ImageSize -> 72*8]
```

> `data/1000` – Mathematica automatycznie dzieli każdy element tablicy przez 1000.

---

#### 🔴 Zadanie 1.1 – Eksploracja danych

Sprawdź zakres wartości współrzędnych x i y:

```mathematica
(* Uzupełnij: użyj funkcji Min[] i Max[] *)
xMin = Min[data[[All, ___]]];
xMax = Max[data[[All, ___]]];
yMin = ___[data[[All, 2]]];
yMax = ___[data[[All, 2]]];

Print["Zakres x: ", xMin, " do ", xMax, " µm"]
Print["Zakres y: ", yMin, " do ", yMax, " µm"]
```

**Odpowiedz:** Jaki jest przybliżony promień łożyska w milimetrach?

---

#### 🔴 Zadanie 1.2 – Histogram odległości od środka układu

Oblicz odległość każdego punktu od początku układu współrzędnych i narysuj histogram:

```mathematica
(* Wskazówka: odległość punktu (x,y) od (0,0) to Sqrt[x^2 + y^2] *)
odleglosci = Table[
  Sqrt[data[[i, 1]]^2 + data[[i, 2]]^2],
  {i, 1, Length[data]}
];

(* Narysuj histogram – użyj Histogram[] *)
Histogram[odleglosci/1000,
  AxesLabel -> {"r [mm]", "Liczba punktów"},
  PlotLabel -> "Rozkład odległości od środka"]
```

**Odpowiedz:** Czy rozkład jest symetryczny? Co to oznacza dla kształtu profilu?

---

### Blok 2 – Dopasowanie okręgu MNK (35 min)

#### 🟢 Kod demonstracyjny (live coding)

```mathematica
(* Krok 1: Zdefiniuj funkcję celu – sumę kwadratów odchyleń *)
(* Odpowiednik: def f(params): return sum((dist(xi,yi,xc,yc)-R)**2 for ...) *)

f[xc_, yc_, R_] := Sum[
  (Sqrt[(data[[i, 1]] - xc)^2 + (data[[i, 2]] - yc)^2] - R)^2,
  {i, 1, Length[data]}
]
```

```mathematica
(* Krok 2: Minimalizacja – odpowiednik scipy.optimize.minimize lub fminunc *)
(* Punkt startowy: środek w (0,0), promień ~6000 µm = 6 mm *)

sol = FindMinimum[
  f[xc, yc, R],
  {{xc, 0}, {yc, 0}, {R, 6000}}
]
```

```mathematica
(* Krok 3: Wyciągnij wyniki *)
xc0 = xc /. sol[[2]];
yc0 = yc /. sol[[2]];
R0  = R  /. sol[[2]];

Print["Środek okręgu: (", xc0, ", ", yc0, ") µm"]
Print["Promień R = ", R0, " µm = ", R0/1000, " mm"]
```

```mathematica
(* Krok 4: Wizualizacja – profil + dopasowany okrąg *)
Show[
  p,
  Graphics[{Red, Thick,
    Circle[{xc0/1000, yc0/1000}, R0/1000]}],
  PlotLabel -> "Profil z dopasowanym okręgiem MNK"
]
```

---

#### 🔴 Zadanie 2.1 – Odchyłka okrągłości

Oblicz odchylenia radialne każdego punktu od dopasowanego okręgu:

```mathematica
(* Odchylenie radialne punktu i od okręgu MNK *)
r = Table[
  Sqrt[(data[[i, 1]] - xc0)^2 + (data[[i, 2]] - yc0)^2] - R0,
  {i, 1, Length[data]}
];

(* Odchyłka okrągłości = zakres odchyleń radialnych *)
deltaR = Max[r] - Min[r];
Print["Odchyłka okrągłości ΔR = ", deltaR, " µm"]
```

**Odpowiedz:** Czy wyznaczona odchyłka okrągłości mieści się w typowych tolerancjach dla łożysk kulkowych (klasa P0: ΔR < 15 µm)?

---

#### 🔴 Zadanie 2.2 – Wizualizacja odchyleń radialnych

Narysuj profil odchyleń radialnych w funkcji numeru punktu pomiarowego:

```mathematica
(* Uzupełnij: użyj ListLinePlot do narysowania listy r *)
ListLinePlot[___,
  AxesLabel -> {"Numer punktu", "Odchylenie radialne [µm]"},
  PlotLabel -> "Profil odchyleń radialnych od okręgu MNK",
  PlotStyle -> Blue]
```

---

### Blok 3 – Analiza chropowatości (25 min)

#### 🟢 Kod demonstracyjny (live coding)

```mathematica
(* Krok 1: Linia średnia *)
rMean = Mean[r];
Print["Linia średnia r̄ = ", rMean, " µm"]

(* Krok 2: Ra – średnie arytmetyczne odchylenie od linii średniej *)
(* Odpowiednik: np.mean(np.abs(r - r_mean)) *)
Ra = Mean[Abs[r - rMean]];
Print["Ra = ", Ra, " µm"]
```

```mathematica
(* Krok 3: Rz – 5 najwyższych szczytów minus 5 najgłębszych dolin *)
sorted = Sort[r];
Rz = Mean[Take[sorted, -5]] - Mean[Take[sorted, 5]];
Print["Rz = ", Rz, " µm"]
```

---

#### 🔴 Zadanie 3.1 – Wykres profilu z linią średnią

Narysuj profil odchyleń radialnych z zaznaczoną linią średnią:

```mathematica
Show[
  ListLinePlot[r,
    PlotStyle -> Blue,
    AxesLabel -> {"Numer punktu", "r [µm]"}],
  (* Dodaj poziomą linię na wysokości rMean *)
  Graphics[{Red, Dashed,
    Line[{{1, ___}, {Length[r], ___}}]}],
  PlotLabel -> "Profil chropowatości z linią średnią"
]
```

---

#### 🔴 Zadanie 3.2 – Porównanie Ra i Rz

Oblicz stosunek `Rz/Ra`. Dla typowych powierzchni obrobionych mechanicznie wynosi on 4–7.

```mathematica
stosunek = Rz / Ra;
Print["Rz/Ra = ", stosunek]
Print["Ocena: ", If[4 <= stosunek <= 7, "typowa powierzchnia", "powierzchnia atypowa"]]
```

---

#### 🔴 Zadanie 3.3 (dodatkowe) – Analiza widmowa profilu

Zastosuj transformatę Fouriera do profilu odchyleń radialnych, aby zidentyfikować dominujące częstotliwości falistości:

```mathematica
(* Transformata Fouriera – odpowiednik np.fft.fft() *)
spectrum = Abs[Fourier[r]];

(* Narysuj widmo amplitudowe (pierwsza połowa – bez aliasingu) *)
n = Length[r];
ListLinePlot[
  Take[spectrum, Floor[n/2]],
  AxesLabel -> {"Numer harmonicznej", "Amplituda [µm]"},
  PlotLabel -> "Widmo amplitudowe profilu chropowatości",
  PlotRange -> All
]
```

**Odpowiedz:** Ile "fal" na obwód dominuje w profilu? Co może być przyczyną tej falistości?

---

### Podsumowanie – tabela wyników

Uzupełnij tabelę na podstawie obliczeń:

| Parametr | Symbol | Wartość | Jednostka | Uwagi |
|----------|--------|---------|-----------|-------|
| Promień okręgu MNK | R | | µm | |
| Współrzędna środka x | xc | | µm | |
| Współrzędna środka y | yc | | µm | |
| Odchyłka okrągłości | ΔR | | µm | norma: < 15 µm (P0) |
| Chropowatość Ra | Ra | | µm | |
| Chropowatość Rz | Rz | | µm | |
| Stosunek Rz/Ra | — | | — | norma: 4–7 |

---

### Pytania kontrolne

1. Dlaczego do dopasowania okręgu używamy metody MNK zamiast np. okręgu opisanego na 3 punktach?
2. Jaka jest różnica między odchyłką okrągłości a chropowatością? Który parametr opisuje makrogeometrię, a który mikrogeometrię?
3. Co oznacza `data[[All, 1]]` w Mathematica? Jak zapisać to samo w Pythonie (NumPy)?
4. Dlaczego dane pomiarowe dzielimy przez 1000 przy wizualizacji?
5. Jak zmieni się wartość Ra, jeśli przesuniesz linię średnią o 1 µm w górę? Uzasadnij.

---

### Wymagania do pliku Notebook Mathematica

**Obowiązkowe wykresy:**
- [ ] Profil surowy bieżni (w mm)
- [ ] Profil z dopasowanym okręgiem MNK (czerwony)
- [ ] Profil odchyleń radialnych z linią średnią
- [ ] Histogram odległości od środka (Zadanie 1.2)

**Wyniki:**
- [ ] Tabela z wartościami R, xc, yc, ΔR, Ra, Rz
- [ ] Odpowiedzi na pytania kontrolne

**Kod:**
- [ ] Notebook Mathematica `.nb` z komentarzami


---

*Plik danych: `pw6205.txt` musi znajdować się w tym samym folderze co notebook.*
