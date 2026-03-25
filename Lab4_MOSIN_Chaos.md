# Materiały Laboratoryjne: Analiza Chaosu w Układach Dynamicznych
## Metoda Obliczeń Symbolicznych i Numerycznych | Wolfram Mathematica 13.3

### Sekcja 1 – Teoria (Metoda Feynmana)

Wyobraź sobie, że masz bardzo proste równanie opisujące populację ryb w stawie. Wydawałoby się, że taki system powinien być przewidywalny jak zegarek. Nic bardziej mylnego. W układach dynamicznych nieliniowość sprawia, że mała zmiana na początku prowadzi do gigantycznych różnic na końcu. To jest właśnie **Chaos**.

#### 1. Równanie Logistyczne
To fundament analizy chaosu. Zapisujemy je jako:
$$x_{n+1} = a \cdot x_n \cdot (1 - x_n)$$
Gdzie:
*   $x_n$ – stan układu (np. populacja) w kroku $n$ (wartość od 0 do 1).
*   $a$ – parametr kontrolny (np. dostępność pożywienia).

**Intuicja:**
*   Część $a \cdot x_n$ odpowiada za wzrost (im więcej ryb, tym więcej młodych).
*   Część $(1 - x_n)$ odpowiada za ograniczenia (głód, brak miejsca).

#### 2. Co to jest Bifurkacja?
Słowo "bifurkacja" oznacza po prostu **rozwidlenie**. Wyobraź sobie rzekę, która nagle dzieli się na dwie mniejsze. W naszym układzie, gdy zwiększamy parametr $a$, system nagle "decyduje", że zamiast dążyć do jednej stałej wartości, będzie przeskakiwał między dwiema, potem czterema, aż w końcu wpadnie w całkowity chaos.

#### 3. Wyjaśnienie graficzne (Diagram pajęczynowy)
W Mathematice analizujemy, jak $x$ ewoluuje:
*   **Dla małych $a$ (< 3):** Układ dąży do jednego punktu stałego (spokój).
*   **Dla $a pprox 3.2$:** Układ oscyluje między 2 wartościami (cykl).
*   **Dla $a > 3.57$:** Chaos. Trajektoria nigdy się nie powtarza, a minimalna zmiana warunków początkowych daje zupełnie inny wynik (efekt motyla).

---

### Sekcja 2 – Zadania dla studentów (na podstawie Bifurkacje.nb)

*Uwaga dla programistów C++/Python/Matlab: W Mathematice funkcje zaczynają się z wielkiej litery, a argumenty podajemy w nawiasach kwadratowych `[ ]`. Definicja funkcji wymaga podkreślnika `x_`.*

**Zadanie 1: Iteracja i Symbolika**
*   **a)** Zdefiniuj funkcję logistyczną: `f[x_] := a * x * (1 - x)`.
*   **b)** Wygeneruj 5 pierwszych kroków iteracji w formie symbolicznej dla $x_0 = 1/2$ używając `NestList[f, 1/2, 5] // Simplify`.
*   **c)** Podstaw pod parametr $a$ wartość 3 (`g[x_] := f[x] /. a -> 3`) i wygeneruj listę 20 kolejnych wartości numerycznych zaczynając od $x_0 = 0.5$.
*   **d)** Stwórz wykres liniowy (`ListLinePlot`) dla 100 iteracji przy $a = 2.8$. Czy układ jest stabilny?

**Zadanie 2: Droga do Chaosu (Bifurkacje)**
*   **a)** Zmień parametr $a$ na $3.2$ i narysuj wykres 100 iteracji. Ile punktów skupienia widzisz?
*   **b)** Powtórz krok (a) dla $a = 3.5$ oraz $a = 3.9$. Opisz różnicę w charakterze wykresu.
*   **c)** Użyj funkcji `Manipulate`, aby stworzyć suwak dla parametru $a \in [2.5, 4.0]$ i obserwuj na żywo zmiany w `ListLinePlot`.

**Zadanie 3: Wrażliwość na warunki początkowe (Efekt Motyla)**
*   **a)** Przyjmij $a = 4.0$. Wygeneruj dwie listy wartości (50 kroków): jedna dla $x_0 = 0.5$, druga dla $x_0 = 0.500001$.
*   **b)** Narysuj obie listy na jednym wykresie. Po ilu iteracjach trajektorie całkowicie się rozbiegają?

**Zadanie 4: Diagram Bifurkacyjny (Zaawansowane)**
*   **a)** Napisz pętlę (używając `Table`), która dla $a$ od 2.5 do 4.0 (krok 0.01) zbierze ostatnie 50 wartości z 200 iteracji.
*   **b)** Przedstaw wynik na wykresie punktowym. Gdzie zaczyna się chaos?
