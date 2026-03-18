# Ćwiczenie 3: Analiza profilu okrągłości walca

**Przedmiot:** Metody Obliczeń Symbolicznych i Numerycznych  
**Środowisko:** Wolfram Mathematica 13.3

---

## Niezbędne wiadomości

### 1. Okrągłość i odchyłka okrągłości

Profil okrągłości to przekrój poprzeczny walca mierzony w jednej płaszczyźnie. Odchyłka okrągłości **RONt** (Roundness) to różnica między maksymalnym a minimalnym promieniem względem okręgu referencyjnego:

$$\text{RONt} = W_p + W_v = r_{\max} - r_{\min}$$

gdzie $W_p$ – odchyłka szczytowa, $W_v$ – odchyłka dolinowa.

```
Profil rzeczywisty:          Okrąg referencyjny:

      *   *                       * * *
    *       *                   *       *
   *    +    *      →           *   +   *      RONt = Wp + Wv
    *       *                   *       *
      *   *                       * * *

  (nieregularny)              (idealny okrąg)
```

### 2. Metody pomiaru walcowości

| Metoda | Opis | Dane wejściowe |
|--------|------|----------------|
| Cross sections | Pomiar profili okrągłości na różnych wysokościach | Macierz: wiersze = kąty, kolumny = przekroje |
| Generatrix | Pomiar wzdłuż tworzących walca | Macierz: wiersze = kąty, kolumny = tworzące |

Plik `cross.txt` zawiera dane metody przekrojów poprzecznych.  
Pierwszy wiersz = wysokości pomiarów, kolejne kolumny = kolejne przekroje.

### 3. Aproksymacja szeregiem Fouriera (MNK)

Profil okrągłości aproksymujemy szeregiem Fouriera:

$$r(\varphi) = a_0 + \sum_{k=1}^{N} \left[ a_k \cos(k\varphi) + b_k \sin(k\varphi) \right]$$

Współczynniki $a_k$, $b_k$ wyznaczamy metodą najmniejszych kwadratów (MNK).  
Dla $n$ punktów pomiarowych $\varphi_i = \frac{2\pi i}{n}$, budujemy macierz układu:

$$\mathbf{A} \cdot \mathbf{c} = \mathbf{r}$$

$$\mathbf{A} = \begin{bmatrix}
1 & \cos\varphi_1 & \sin\varphi_1 & \cos 2\varphi_1 & \sin 2\varphi_1 & \cdots \\
1 & \cos\varphi_2 & \sin\varphi_2 & \cos 2\varphi_2 & \sin 2\varphi_2 & \cdots \\
\vdots & & & & & \ddots
\end{bmatrix}$$

Rozwiązanie MNK: $\mathbf{c} = (\mathbf{A}^T \mathbf{A})^{-1} \mathbf{A}^T \mathbf{r}$  
W Mathematica: `LeastSquares[A, r]`

### 4. Harmoniczne i ich znaczenie geometryczne

| Harmoniczna | Nazwa | Znaczenie |
|-------------|-------|-----------|
| $k = 1$ | Ekscentryczność | Przesunięcie środka |
| $k = 2$ | Owalność | Kształt elipsy |
| $k = 3$ | Trójkątność | Trójkąt |
| $k \geq 4$ | Falistość | Drobne nieregularności |

### 5. Kluczowe funkcje Mathematica

| Funkcja | Opis |
|---------|------|
| `Import["plik.txt"]` | Wczytanie danych z pliku |
| `ListPolarPlot[dane]` | Wykres w układzie biegunowym |
| `Table[f, {i, n}]` | Generowanie listy (odpowiednik `for`) |
| `LeastSquares[A, b]` | Rozwiązanie MNK układu $Ac = b$ |
| `NMaximize[{f, warunek}, x]` | Maksimum numeryczne |
| `NMinimize[{f, warunek}, x]` | Minimum numeryczne |
| `ListPlot3D[dane]` | Wykres 3D z danych |
| `Fourier[lista]` | Dyskretna transformata Fouriera |

---

## Zadanie 1.

Dane pomiarowe walcowości zapisane są w pliku `cross.txt` (metoda przekrojów poprzecznych). Pierwszy wiersz zawiera wysokości pomiarów, kolejne kolumny odpowiadają kolejnym przekrojom walca.

a) Wczytaj dane z pliku i wyświetl ich wymiary (liczba punktów kątowych, liczba przekrojów).  
b) Wyodrębnij dane pierwszego przekroju (pierwsza kolumna danych pomiarowych).  
c) Wykreśl profil pierwszego przekroju w układzie kartezjańskim i biegunowym.

---

## Zadanie 2.

Profil okrągłości aproksymowany jest szeregiem Fouriera z $N = 15$ harmonicznymi.

a) Zdefiniuj wektor kątów $\varphi_i = \frac{2\pi (i-1)}{n}$ dla $n$ punktów pomiarowych.  
b) Zbuduj macierz $\mathbf{A}$ układu równań MNK zgodnie ze wzorem z sekcji teorii.  
c) Wyświetl wymiary macierzy $\mathbf{A}$ i sprawdź jej poprawność.

---

## Zadanie 3.

Korzystając z macierzy $\mathbf{A}$ i danych pierwszego przekroju:

a) Wyznacz współczynniki aproksymacji $\mathbf{c} = [a_0, a_1, b_1, a_2, b_2, \ldots]$ metodą `LeastSquares`.  
b) Oblicz amplitudy harmonicznych $A_k = \sqrt{a_k^2 + b_k^2}$ dla $k = 1, \ldots, N$.  
c) Wykreśl widmo amplitud harmonicznych (wykres słupkowy).  
d) Wykreśl profil aproksymowany na tle danych pomiarowych.

---

## Zadanie 4.

Odchyłka okrągłości RONt wyznaczana jest jako różnica między maksymalnym a minimalnym odchyleniem od okręgu referencyjnego.

a) Zdefiniuj funkcję odchyłki $\Delta w(\varphi) = r(\varphi) - r_{\text{aproks}}(\varphi)$ jako funkcję ciągłą.  
b) Wyznacz $W_p$ i $W_v$ numerycznie przy użyciu `NMaximize` i `NMinimize`.  
c) Oblicz RONt $= W_p + W_v$.  
d) Porównaj wynik z wartością uzyskaną bezpośrednio z dyskretnych danych pomiarowych (`Max` i `Min`).

---

## Zadanie 5.

Analiza dotyczy wszystkich przekrojów walca.

a) Wyznacz współczynniki aproksymacji Fouriera dla każdego przekroju (pętla lub `Table`).  
b) Oblicz RONt dla każdego przekroju i wykreśl jego zmienność wzdłuż osi walca.  
c) Wyznacz i wyświetl przekrój o największej i najmniejszej odchyłce okrągłości.

---

## Zadanie 6. *(dodatkowe)*

Dane ze wszystkich przekrojów tworzą powierzchnię walca.

a) Zbuduj macierz odchyłek $\Delta w(\varphi_i, z_j)$ dla wszystkich kątów i wysokości.  
b) Wykreśl powierzchnię walca w postaci mapy 2D (`MatrixPlot` lub `ArrayPlot`).  
c) Wykreśl powierzchnię walca w postaci wykresu 3D (`ListPlot3D`).

---

## Pytania sprawdzające

1. Co to jest odchyłka okrągłości RONt i jak się ją oblicza?
2. Na czym polega metoda przekrojów poprzecznych pomiaru walcowości?
3. Jaką rolę pełni każda harmoniczna szeregu Fouriera w opisie profilu okrągłości?
4. Czym różni się rozwiązanie MNK od dokładnego rozwiązania układu równań?
5. Jak zinterpretować widmo amplitud harmonicznych profilu okrągłości?
