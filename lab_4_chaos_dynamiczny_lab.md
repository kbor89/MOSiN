# Analiza Chaosu w Układach Dynamicznych
### Materiały laboratoryjne – Metody Obliczeń Symbolicznych i Numerycznych
**Wolfram Mathematica 13.3** | Studia magisterskie

---

## Sekcja 1 – Teoria

### 1.1 Odwzorowanie logistyczne

Rozważamy **nieliniowe równanie rekurencyjne** (odwzorowanie logistyczne):

$$x_{n+1} = a \cdot x_n (1 - x_n), \quad x_0 = \frac{1}{2}, \quad a \in (0, 4]$$

Jest to najprostszy model populacyjny, w którym:
- $x_n \in [0,1]$ – znormalizowana liczebność populacji
- $a$ – parametr wzrostu (kontrolny)
- człon $(1 - x_n)$ – efekt nasycenia (ograniczone zasoby)

> **Intuicja Feynmana:** Wyobraź sobie rybę w stawie. Gdy jest jej mało – rośnie szybko (czynnik $a \cdot x_n$). Gdy jest jej dużo – brakuje jedzenia i populacja spada (czynnik $(1-x_n)$). Parametr $a$ to „agresywność" wzrostu.

---

### 1.2 Trajektorie i punkty stałe

**Punkt stały** $x^*$ spełnia $f(x^*) = x^*$, czyli:

$$a x^*(1 - x^*) = x^* \implies x^* = 0 \quad \text{lub} \quad x^* = 1 - \frac{1}{a}$$

**Stabilność** punktu stałego zależy od $|f'(x^*)| = |a(1 - 2x^*)|$:

| Warunek | Zachowanie trajektorii |
|---|---|
| $\|f'(x^*)\| < 1$ | punkt stały **stabilny** (atraktor) |
| $\|f'(x^*)\| > 1$ | punkt stały **niestabilny** (repeler) |
| $\|f'(x^*)\| = 1$ | bifurkacja |

---

### 1.3 Bifurkacje i droga do chaosu

Gdy parametr $a$ rośnie, układ przechodzi przez kolejne **bifurkacje podwojenia okresu**:

```
a ≈ 1       →  zbieżność do x* = 0
a ∈ (1, 3)  →  zbieżność do x* = 1 - 1/a  (stabilny punkt stały)
a ≈ 3.0     →  1. bifurkacja: cykl okresu 2
a ≈ 3.449   →  2. bifurkacja: cykl okresu 4
a ≈ 3.544   →  3. bifurkacja: cykl okresu 8
a ≈ 3.5688  →  ...
a ≈ 3.5699  →  CHAOS (akumulacja bifurkacji)
a = 4       →  pełny chaos na [0,1]
```

> **Stała Feigenbauma:** Stosunek kolejnych odległości między bifurkacjami dąży do $\delta \approx 4.6692...$  
> Jest to **stała uniwersalna** – pojawia się w każdym układzie przechodzącym przez kaskadę podwojeń okresu.

---

### 1.4 Diagram bifurkacyjny

Diagram bifurkacyjny to wykres **atraktora** układu w funkcji parametru $a$:

```
x
1 |          .  . .::.:::::::::::::::
  |        .    .  .::.:::::::::::::::
  |      .       . .::.:::::::::::::::
  |    .           .::.:::::::::::::::
  |  .              .  :::::::::::::::
0 +--+--+--+--+--+--+--+--+--+--+---> a
  1  1.5  2  2.5  3  3.2 3.4 3.6 3.8 4
         stały  cykl2 cykl4   CHAOS
```

Dla każdej wartości $a$: iterujemy $2M$ kroków, odrzucamy pierwsze $M$ (transjent), rysujemy ostatnie $M$ punktów.

---

### 1.5 Diagram pajęczynowy (cobweb)

Wizualizacja trajektorii na płaszczyźnie $(x_n, x_{n+1})$:

```
x_{n+1}
  1 |    /f(x)
    |   /
    |  /  ↗ (x_{n+1}, x_{n+1})
    | /  ↗
    |/ ↗
    +-----------> x_n
```

Algorytm: z punktu $(x_n, 0)$ idź pionowo do paraboli $f(x)$, potem poziomo do prostej $y=x$, powtarzaj.

---

### 1.6 Wykładnik Lapunowa

Mierzy **wrażliwość na warunki początkowe**:

$$\lambda = \lim_{N\to\infty} \frac{1}{N} \sum_{n=0}^{N-1} \ln|f'(x_n)| = \lim_{N\to\infty} \frac{1}{N} \sum_{n=0}^{N-1} \ln|a(1-2x_n)|$$

| Wartość $\lambda$ | Interpretacja |
|---|---|
| $\lambda < 0$ | trajektoria zbieżna (atraktor punktowy lub cykliczny) |
| $\lambda = 0$ | bifurkacja |
| $\lambda > 0$ | **chaos** – wykładnicza rozbieżność trajektorii |

---

## Sekcja 2 – Zadania

### Zadanie 1 – Symboliczna analiza odwzorowania logistycznego

Zdefiniuj odwzorowanie logistyczne i zbadaj jego właściwości symbolicznie.

```mathematica
f[x_] := a x (1 - x)
```

**a)** Oblicz symbolicznie pierwsze 5 iteracji startując z $x_0 = 1/2$:
```mathematica
NestList[f, 1/2, 5] // Simplify
```
Zinterpretuj wynik – jak złożoność wyrażeń rośnie z liczbą iteracji?

**b)** Wyznacz punkty stałe odwzorowania (rozwiąż $f(x^*) = x^*$) i określ dla jakich wartości $a$ punkt nietrywialny $x^* = 1 - 1/a$ leży w przedziale $(0,1)$.

**c)** Oblicz pochodną $f'(x)$ i wyznacz warunek stabilności punktu stałego $x^* = 1 - 1/a$. Dla jakich $a$ jest on stabilny?

**d)** Wyznacz punkty cyklu okresu 2, rozwiązując $f(f(x)) = x$ (po uprzednim wyeliminowaniu punktów stałych). Sprawdź, że pojawiają się dla $a > 3$.

---

### Zadanie 2 – Numeryczna analiza trajektorii

Ustal $a = 3$ i zbadaj zachowanie trajektorii numerycznie.

```mathematica
g[x_] = f[x] /. a -> 3;
NestList[g, 0.5, 20]
```

**a)** Wygeneruj i narysuj trajektorię dla $a = 3$ (100 iteracji, $x_0 = 0.5$) używając `ListLinePlot` oraz `ListPlot`. Nałóż oba wykresy poleceniem `Show`. Opisz obserwowane zachowanie.

**b)** Zdefiniuj funkcję `TrajPlot[av_]` rysującą trajektorię dla zadanego parametru $av$:
```mathematica
TrajPlot[av_] := Module[{g},
  g[x_] = f[x] /. a -> av;
  Show[
    ListPlot[NestList[g, 0.5, 100], PlotStyle -> Red, PlotRange -> {0,1}],
    ListLinePlot[NestList[g, 0.5, 100], PlotRange -> {0,1}]
  ]
]
```
Wywołaj `TrajPlot[2.5]`, `TrajPlot[3.2]`, `TrajPlot[3.5]`, `TrajPlot[3.9]`. Opisz różnice.

**c)** Użyj `Manipulate` do interaktywnej eksploracji trajektorii:
```mathematica
Manipulate[TrajPlot[av], {av, 2, 4}]
```
Znajdź wartości $a$, przy których następują kolejne bifurkacje (zmiana liczby wartości, do których zbiega trajektoria).

**d)** Zdefiniuj funkcję `Traj[av_]` zwracającą **tylko ostatnie $M$ punktów** trajektorii (po odrzuceniu transjentów):
```mathematica
M = 100;
Traj[av_] := Module[{g},
  g[x_] = f[x] /. a -> av;
  NestList[g, 0.5, 2M][[M+1 ;; 2M]]
]
```
Sprawdź działanie: `ListPlot[Traj[3.4], PlotRange -> {0,1}]`. Ile różnych wartości zwraca `Traj` dla $a = 3.4$? A dla $a = 3.84$?

---

### Zadanie 3 – Diagram bifurkacyjny

Skonstruuj diagram bifurkacyjny odwzorowania logistycznego.

**a)** Ustaw parametry i wygeneruj zbiór punktów diagramu:
```mathematica
a1 = 2.5; a2 = 4; M2 = 500;
points = Join @@ Table[
  With[{a = a1 + (a2 - a1) i/M2 // N},
    {a Table[1, {M}], Traj[a]} \[Transpose]
  ],
  {i, 0, M2}
];
```
Wyjaśnij rolę każdego parametru: `M`, `M2`, `a1`, `a2`.

**b)** Narysuj diagram bifurkacyjny:
```mathematica
Graphics[{PointSize[0.002], Point[points]},
  PlotRange -> {{a1, a2}, {0, 1}},
  Axes -> True
]
```
Zidentyfikuj na wykresie: obszar punktu stałego, cyklu okresu 2, cyklu okresu 4, obszar chaosu, okna periodyczności.

**c)** Zdefiniuj funkcję `bif[a1_, a2_]` generującą diagram dla dowolnego zakresu parametru i wywołaj ją dla zakresu $[3.7, 3.71]$:
```mathematica
bif[a1_, a2_] := Module[{points},
  points = Join @@ Table[
    With[{a = a1 + (a2 - a1) i/M2 // N},
      {a Table[1, {M}], Traj[a]} \[Transpose]
    ],
    {i, 0, M2}
  ];
  Graphics[{PointSize[0.002], Point[points]},
    PlotRange -> {{a1, a2}, {0, 1}}, Axes -> True]
]
bif[3.7, 3.71]
```
Co obserwujesz? Jak to zjawisko nazywa się w teorii chaosu?

**d)** Użyj `Manipulate` do interaktywnego powiększania diagramu:
```mathematica
Manipulate[bif[a1, a2],
  {a1, 2.5, 4}, {{a2, 4}, 2.5, 4},
  ContinuousAction -> False
]
```
Zbadaj samopodobieństwo diagramu – powiększ wybrany fragment i porównaj z całością.

---

### Zadanie 4 – Diagram pajęczynowy (cobweb)

Zwizualizuj dynamikę układu za pomocą diagramu pajęczynowego.

**a)** Ustaw $a = 3.84$ i oblicz trajektorię:
```mathematica
av = 3.84;
tr = Traj[av];
```

**b)** Narysuj diagram pajęczynowy łącząc: prostą $y = x$ (niebieski), ścieżkę pajęczynową (zielony) i parabolę $f(x)$ (czerwony):
```mathematica
Show[
  Graphics[{Blue, Line[{{0,0},{1,1}}],
    Green, Line[Join @@ Table[
      {{tr[[k]], tr[[k+1]]}, {tr[[k+1]], tr[[k+1]]}},
      {k, 1, Length[tr]-1}
    ]]
  }],
  Plot[f[x] /. a -> av, {x, 0, 1}, PlotStyle -> Red,
    PlotRange -> {{0,1},{0,1}}]
]
```
Opisz geometrię ścieżki pajęczynowej dla $a = 3.84$.

**c)** Opakuj powyższy kod w `Manipulate` z suwakiem dla parametru $a \in [2, 4]$:
```mathematica
Manipulate[
  tr = Traj[a1];
  Show[
    Graphics[{Blue, Line[{{0,0},{1,1}}],
      Green, Line[Join @@ Table[
        {{tr[[k]], tr[[k+1]]}, {tr[[k+1]], tr[[k+1]]}},
        {k, 1, Length[tr]-1}
      ]]
    }],
    Plot[f[x] /. a -> a1, {x, 0, 1}, PlotStyle -> Red,
      PlotRange -> {{0,1},{0,1}}]
  ],
  {a1, 2, 4}
]
```
Obserwuj jak zmienia się struktura diagramu przy przejściu przez kolejne bifurkacje.

**d)** Dla $a = 3.84$ użyj `ListPointPlot3D` do wizualizacji trajektorii w przestrzeni opóźnień $(x_n, x_{n+1}, x_{n+2})$:
```mathematica
Manipulate[
  tr = Traj[a1];
  ListPointPlot3D[
    Table[{tr[[k]], tr[[k+1]], tr[[k+2]]}, {k, 1, Length[tr]-2}]
  ],
  {a1, 2, 4}
]
```
Jak wygląda atraktor w przestrzeni 3D dla wartości chaotycznych vs. periodycznych?

---

### Zadanie 5 – Wykładnik Lapunowa *(zadanie dodatkowe)*

**a)** Napisz funkcję obliczającą wykładnik Lapunowa dla odwzorowania logistycznego:
$$\lambda(a) = \frac{1}{N}\sum_{n=0}^{N-1} \ln|a(1 - 2x_n)|$$

```mathematica
LyapunovExp[av_, N0_: 1000] := Module[{g, traj},
  g[x_] = f[x] /. a -> av;
  traj = NestList[g, 0.5, N0];
  Mean[Log[Abs[av (1 - 2 traj)]]]
]
```

**b)** Narysuj wykres $\lambda(a)$ dla $a \in [2.5, 4]$ i nałóż na diagram bifurkacyjny. Zidentyfikuj:
- wartości $a$, dla których $\lambda = 0$ (bifurkacje)
- wartości $a$, dla których $\lambda > 0$ (chaos)
- okna periodyczności ($\lambda < 0$ wewnątrz obszaru chaotycznego)

**c)** Sprawdź numerycznie stałą Feigenbauma: wyznacz kolejne wartości $a_n$ bifurkacji i oblicz stosunek $\delta_n = (a_n - a_{n-1})/(a_{n+1} - a_n)$. Czy zbiega do $\delta \approx 4.6692$?

---

*Materiały przygotowane na podstawie: Bifurkacje.nb | Wolfram Mathematica 13.3*
