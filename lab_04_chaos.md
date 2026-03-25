# Lab 4 – Analiza Chaosu w Układach Dynamicznych
**Metoda Obliczeń Symbolicznych i Numerycznych | Wolfram Mathematica 13.3**

---

## Sekcja 1 – Teoria

### 1.1 Odwzorowanie logistyczne

Wyobraź sobie populację owadów w zamkniętym ekosystemie. Każde pokolenie zależy od poprzedniego – im więcej osobników, tym silniejsza konkurencja o zasoby. Ten prosty mechanizm opisuje **odwzorowanie logistyczne**:

$$x_{n+1} = a \cdot x_n (1 - x_n), \quad x_n \in [0,1], \quad a \in (0,4]$$

| Symbol | Znaczenie |
|--------|-----------|
| $x_n$ | stan układu w kroku $n$ (np. znormalizowana populacja) |
| $a$ | parametr kontrolny (tempo wzrostu) |
| $(1-x_n)$ | czynnik ograniczający (pojemność środowiska) |

> **Kluczowa obserwacja:** Równanie jest nieliniowe (człon $x_n^2$). To nieliniowość jest źródłem chaosu.

---

### 1.2 Trajektorie i punkty stałe

**Punkt stały** $x^{*}$ spełnia $f(x^{*}) = x^{*}$, czyli:

$$a \, x^{*}(1 - x^{*}) = x^{*} \implies x^{*} = 0 \quad \text{lub} \quad x^{*} = 1 - \frac{1}{a}$$

**Stabilność** punktu stałego zależy od $|f'(x^{*})|$:

$$f'(x) = a(1 - 2x)$$

| Warunek | Charakter punktu |
|---------|-----------------|
| $\|f'(x^{*})\| < 1$ | stabilny (atraktor) |
| $\|f'(x^{*})\| = 1$ | bifurkacja |
| $\|f'(x^{*})\| > 1$ | niestabilny (repeler) |

---

### 1.3 Droga do chaosu – kaskada bifurkacji

Zwiększając $a$, obserwujemy kolejne **podwojenia okresu**:

```
a < 3.0   →  1 punkt stały (stabilny)
a ≈ 3.0   →  bifurkacja: cykl okresu 2
a ≈ 3.449 →  bifurkacja: cykl okresu 4
a ≈ 3.544 →  bifurkacja: cykl okresu 8
a ≈ 3.565 →  bifurkacja: cykl okresu 16
...
a ≈ 3.570 →  CHAOS (nieskończone podwojenia)
a = 4.0   →  pełny chaos (ergodyczny)
```

Kolejne bifurkacje zachodzą coraz szybciej. Stosunek odległości między nimi dąży do stałej **Feigenbauma**:

$$\delta = \lim_{n\to\infty} \frac{a_n - a_{n-1}}{a_{n+1} - a_n} \approx 4.6692...$$

> **Universalność:** Stała Feigenbauma jest taka sama dla *każdego* odwzorowania jednomodalnego. To jeden z najgłębszych wyników teorii chaosu.

---

### 1.4 Diagram bifurkacyjny

Algorytm generowania diagramu:

```
dla każdego a ∈ [a_min, a_max]:
    1. iteruj f M razy (odrzuć przejściowe)
    2. zbierz kolejne N wartości (atraktor)
    3. nanieś punkty (a, x) na wykres
```

```
x
1 |          .  . .:.:·:·:·:·:·:·:·:·:·:·:·:·:·
  |        .  .  .  .  . .:.:·:·:·:·:·:·:·:·:·:
  |      .  .  .  .  .  .  . .:.:·:·:·:·:·:·:·:
  |    ___________
  |   /           \___________
  |  /                        \___________________
0 +--+--------+--------+--------+--------+-------> a
  2.5        3.0      3.5      3.57     4.0
```

---

### 1.5 Diagram pajęczynowy (cobweb)

Geometryczna wizualizacja iteracji:

1. Narysuj $y = f(x)$ i prostą $y = x$
2. Startuj z $x_0$ na osi $x$
3. Idź **pionowo** do krzywej $f$ → punkt $(x_0, f(x_0))$
4. Idź **poziomo** do prostej $y=x$ → punkt $(x_1, x_1)$
5. Powtarzaj

```
y
1|    /f(x)
 |   /·····
 |  /  ·  ·
 | /·   ·  ·
 |/ ·    ·  ·
 +-----------> x
 0    x*   1
      ↑
   punkt stały = przecięcie f(x) z y=x
```

---

### 1.6 Wykładnik Lapunowa

Mierzy **średnie tempo rozchodzenia się** bliskich trajektorii:

$$\lambda = \lim_{N\to\infty} \frac{1}{N} \sum_{n=0}^{N-1} \ln|f'(x_n)| = \lim_{N\to\infty} \frac{1}{N} \sum_{n=0}^{N-1} \ln|a(1-2x_n)|$$

| $\lambda$ | Charakter układu |
|-----------|-----------------|
| $\lambda < 0$ | stabilny atraktor |
| $\lambda = 0$ | bifurkacja / granica chaosu |
| $\lambda > 0$ | **chaos** (efekt motyla) |

---

## Sekcja 2 – Zadania

### Zadanie 1 – Analiza symboliczna i numeryczna

```mathematica
f[x_] := a * x * (1 - x)
```

**a)** Wyznacz symbolicznie punkty stałe odwzorowania logistycznego:
```mathematica
Solve[f[x] == x, x]
```

**b)** Sprawdź stabilność obu punktów stałych – oblicz $|f'(x^{*})|$ dla $a = 2.8$ i $a = 3.2$.

**c)** Wygeneruj 5 kroków iteracji symbolicznie dla $x_0 = 1/2$:
```mathematica
NestList[f, 1/2, 5] // Simplify
```

**d)** Podstaw $a = 3$ i wygeneruj 20 wartości numerycznych od $x_0 = 0.5$. Czy układ osiąga punkt stały?

---

### Zadanie 2 – Trajektorie i wizualizacja

Zdefiniuj funkcję rysującą trajektorię:
```mathematica
TrajPlot[av_] := ListLinePlot[
  NestList[f /. a -> av, 0.5, 100],
  PlotRange -> {0, 1}, AxesLabel -> {"n", "x_n"}
]
```

**a)** Narysuj trajektorie dla $a \in \{2.8,\ 3.2,\ 3.5,\ 3.9\}$. Opisz charakter każdej.

**b)** Użyj `Manipulate` z suwakiem $a \in [2.5, 4.0]$:
```mathematica
Manipulate[TrajPlot[av], {av, 2.5, 4.0}]
```

**c)** Dla $a = 4.0$ wygeneruj dwie trajektorie: $x_0 = 0.5$ i $x_0 = 0.500001$. Narysuj na jednym wykresie. Po ilu krokach trajektorie się rozchodzą?

**d)** Wyjaśnij obserwację z (c) w kontekście **efektu motyla**.

---

### Zadanie 3 – Diagram bifurkacyjny

```mathematica
a1 = 2.5; a2 = 4.0; M2 = 300;
Traj[av_] := Drop[NestList[f /. a -> av, 0.5, M2], 250]

pts = Flatten[Table[{av, #} & /@ Traj[av], {av, a1, a2, 0.005}], 1];
ListPlot[pts, PlotStyle -> {Black, PointSize[0.001]}, Frame -> True]
```

**a)** Uruchom powyższy kod. Zidentyfikuj na diagramie: obszar stabilny, cykl-2, cykl-4, chaos.

**b)** Zdefiniuj funkcję `bif[a1_, a2_]` i użyj `Manipulate` do interaktywnego powiększania wybranego fragmentu diagramu.

**c)** Odczytaj z diagramu przybliżone wartości $a$ dla pierwszych trzech bifurkacji. Oblicz stosunek $\delta \approx (a_2-a_1)/(a_3-a_2)$ i porównaj ze stałą Feigenbauma.

**d)** *(Dodatkowe)* Zaznacz na diagramie okna periodyczności (np. cykl-3 przy $a \approx 3.83$).

---

### Zadanie 4 – Diagram pajęczynowy

```mathematica
cobweb[av_, x0_, n_] := Module[{pts, x = x0, fn = f /. a -> av},
  pts = Reap[Do[
    Sow[{x, x}]; x = fn[x]; Sow[{x - 0.0001, x}],
    {n}]][[2, 1]];
  Show[
    Plot[{fn[x], x}, {x, 0, 1}, PlotStyle -> {Blue, Red}],
    ListLinePlot[pts, PlotStyle -> Gray],
    PlotRange -> {{0,1},{0,1}}
  ]
]
```

**a)** Narysuj diagram pajęczynowy dla $a = 2.8$, $x_0 = 0.2$, $n = 30$. Opisz geometrię zbieżności.

**b)** Powtórz dla $a = 3.2$ i $a = 3.9$. Jak zmienia się wzorzec pajęczynowy?

**c)** Użyj `Manipulate` z suwakami dla $a$ i $x_0$.

**d)** Wygeneruj wizualizację 3D trajektorii (`ListPointPlot3D`) pokazującą ewolucję $\{n, x_n, x_{n+1}\}$.

---

### Zadanie 5* – Wykładnik Lapunowa

```mathematica
LyapunovExp[av_, n_] := Module[{orbit},
  orbit = NestList[f /. a -> av, 0.5, n];
  Mean[Log[Abs[(D[f, x] /. a -> av) /. x -> # & /@ Most[orbit]]]]
]
```

**a)** Oblicz $\lambda$ dla $a \in \{2.8,\ 3.2,\ 3.5,\ 3.9\}$. Zinterpretuj znaki.

**b)** Narysuj $\lambda(a)$ dla $a \in [2.5, 4.0]$:
```mathematica
Plot[LyapunovExp[av, 500], {av, 2.5, 4.0},
  PlotRange -> {-3, 1}, AxesLabel -> {"a", "λ"},
  PlotStyle -> Blue]
```

**c)** Porównaj wykres $\lambda(a)$ z diagramem bifurkacyjnym. Gdzie $\lambda = 0$?

**d)** Wyznacz numerycznie wartość $a_\infty \approx 3.5699$ (granica chaosu) jako miejsce, gdzie $\lambda$ zmienia znak.

---

*Wolfram Mathematica 13.3 | MOSIN Lab 4*
