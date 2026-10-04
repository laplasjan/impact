# ImpactHer Pay Equity

**Znajdź różnice warte sprawdzenia. Zacznij od porównywalnej pracy.**

Krótki opis projektu: [`PROJECT_DESCRIPTION.md`](PROJECT_DESCRIPTION.md).

Lokalny dashboard do wstępnego przeglądu wynagrodzeń. Pomaga zespołom HR, osobom analizującym wynagrodzenia i konsultantom wskazać kategorie pracy, w których warto przeprowadzić dokładniejszy audyt. Wynik jest sygnałem do analizy, nie dowodem dyskryminacji ani certyfikatem zgodności z prawem.

## Dlaczego ImpactHer?

Nierówności płacowe mogą ograniczać bezpieczeństwo finansowe i rozwój zawodowy kobiet. Projekt ma pomóc dostrzec takie ryzyko w danych o wynagrodzeniach i skierować uwagę na porównania w obrębie kategorii pracy. Wpisuje się w kategorię **Technology for Real Change** przez użycie narzędzia analitycznego do wsparcia równości ekonomicznej. Obecne MVP nie wdraża zmian płacowych ani nie ocenia pracodawcy automatycznie.

## MVP i demonstracja

Aplikacja w Streamlit udostępnia cztery widoki:

1. **Dashboard** — ogólna luka jako kontekst, różnice w kategoriach pracy, przedziały bootstrap, rekomendacje do sprawdzenia i bezpieczny raport CSV.
2. **Raport UE** — wybrane metryki opisowe: średnia i mediana, wynagrodzenie dodatkowe, kwartyle oraz screening progu 5%.
3. **Benchmark** — agregatowy eksport kategorii. Dla danych rzeczywistych wymaga zaznaczenia uprawnienia do ich agregacji i ponownego użycia; checkbox nie zastępuje oceny prawnej.
4. **Metodyka / Heckman** — opcjonalna demonstracja dwuetapowego modelu Heckmana na osobnym, syntetycznym zbiorze selekcyjnym.

**Przebieg demo:** uruchom aplikację na syntetycznych danych, przejdź od ogólnego wskaźnika do kategorii, pokaż ukrywanie małych grup i niepewność wyniku, pobierz raport, a na końcu (opcjonalnie) uruchom demo Heckmana. Do dashboardu można też wgrać własny CSV.

> W ZIP-ie źródłowym nie było GIF-a, zrzutów ekranu ani innych materiałów demonstracyjnych. Nie dodano więc podglądów ani przykładowych wyników do README.

## Jak uruchomić

Wymagany jest Python 3.11 lub nowszy. Polecenia sprawdzono składniowo; uruchomienia aplikacji i testów nie udało się zweryfikować w tym środowisku, ponieważ dostęp do PyPI był zablokowany.

```bash
# 1. Sklonuj repozytorium i przejdź do jego katalogu
# git clone <URL_REPOZYTORIUM>
cd impacther-project

# 2. Utwórz i aktywuj środowisko wirtualne
python -m venv .venv
# macOS / Linux:
source .venv/bin/activate
# Windows PowerShell:
# .venv\Scripts\Activate.ps1

# 3. Zainstaluj przypięte zależności
python -m pip install -r requirements.txt

# 4. Uruchom aplikację
streamlit run app.py
```

Streamlit wyświetli lokalny adres aplikacji w terminalu (zwykle `http://localhost:8501`). Testy jednostkowe:

```bash
python -m pip install -r requirements.txt
PYTHONPATH=src python -m pytest -q
```

Testy wymagają pakietu `pytest`, który znajduje się w `requirements.txt`.

## Dane wejściowe i wyniki

Aplikacja działa bez zewnętrznego API. Można użyć wbudowanego generatora albo przesłać CSV do 10 MB. Wymagane kolumny i pełny schemat opisano w [`data/README.md`](data/README.md). Dane przesłane do aplikacji są walidowane lokalnie przez uruchomiony proces Streamlit; repozytorium nie implementuje trwałego magazynu ani uwierzytelniania.

Wyniki to wskaźniki i raporty obliczane z wczytanego zbioru:

- luka średniej i mediany płac oraz różnice wewnątrz kategorii;
- 95% przedziały bootstrap dla różnic średnich;
- udział kobiet i mężczyzn w kwartylach płacowych oraz udział osób otrzymujących składniki dodatkowe;
- rekomendacje oparte na prostych regułach i próg alertu ustawiany przez użytkownika;
- raport CSV dla widocznych kategorii i eksport agregatów do przyszłego benchmarkingu;
- wynik surowy i skorygowany Heckmanem wraz z przedziałem bootstrap, wyłącznie dla zgodnego zbioru selekcyjnego.

Eksporty nie zawierają rekordów pracowników ani dokładnych liczebności kobiet i mężczyzn. Ukrywanie małych grup nie gwarantuje anonimowości.

**Przykład z dołączonego syntetycznego CSV:** smoke check przy minimum 8 osób każdej płci zaakceptował 360 rekordów i 9 kategorii; ogólna niekorygowana luka średniej wyniosła 2,0% (95% bootstrap: od −4,3% do 8,4%), a 2 z 9 kategorii przekroczyły próg 5%. To wynik generatora demo, nie obserwacja rynku.

## Architektura

```text
app.py (interfejs Streamlit)
  ├─ validation.py       walidacja CSV, normalizacja płac i kategorii
  ├─ metrics.py          luka, bootstrap, segmentacja, reguły rekomendacji
  ├─ directive.py        metryki opisowe i raport kategorii
  ├─ reporting.py        raporty i eksporty agregatów
  └─ heckman.py          opcjonalna korekta selekcji (statsmodels / SciPy)
```

Szczegóły: [`docs/architecture.md`](docs/architecture.md) i [`docs/methodology.md`](docs/methodology.md).

## Ograniczenia i odpowiedzialne użycie

- Wbudowane dane są syntetyczne; nie opisują rzeczywistej firmy ani rynku.
- Porównania używają `work_category`, jeśli kolumna jest kompletna. W przeciwnym razie stosują proxy `role + level`, które samo nie dowodzi równej pracy ani pracy o równej wartości.
- Analiza obejmuje kategorie `woman` i `man`; nieznane lub inne wartości płci są odrzucane. To istotne ograniczenie zakresu.
- Kwoty wejściowe są interpretowane jako miesięczne wynagrodzenie w PLN; użytkownik musi zadbać o spójny zakres, walutę i dane brutto. Brak `hours_weekly` oznacza przyjęcie 40 godzin tygodniowo dla 1.0 FTE.
- Próg 5% jest wyłącznie ustawieniem screeningu. Aplikacja nie ustala obiektywnego uzasadnienia różnicy ani jej usunięcia w wymaganym terminie.
- Heckman wymaga obserwacji z `selected_for_pay=0` i `1` oraz wiarygodnej zmiennej wykluczającej, która wpływa na selekcję, ale nie bezpośrednio na płacę. Demo spełnia to założenie z konstrukcji generatora; dla danych rzeczywistych wymaga uzasadnienia i przeglądu metodologicznego.
- Nie używaj narzędzia jako porady prawnej, decyzji kadrowej ani jedynej podstawy oceny osoby lub firmy. Przed użyciem danych pracowniczych ustal podstawę prawną, dostęp, retencję, minimalizację danych, ryzyko ponownej identyfikacji i uprawnienia do agregacji/benchmarkingu.

Więcej: [`docs/methodology.md`](docs/methodology.md), [`docs/roadmap.md`](docs/roadmap.md), [`docs/ai-disclosure.md`](docs/ai-disclosure.md).

## Technologie i zasoby

Python, Streamlit, pandas, NumPy, Plotly, statsmodels, SciPy oraz pytest. Wersje są przypięte w [`requirements.txt`](requirements.txt). Repozytorium nie zawiera zewnętrznego modelu ML, kluczy API ani zewnętrznego datasetu. Pełną listę zasobów oraz ujawnienie AI podają [`docs/sources.md`](docs/sources.md) i [`docs/ai-disclosure.md`](docs/ai-disclosure.md).

## Status zgłoszenia

Tytuł projektu jest dostępny: **ImpactHer Pay Equity**. Pliki nie podają potwierdzonej nazwy zespołu ani listy członków, więc nie wpisano ich do README. Wymagania zgłoszenia hackathonowego i konkretne TODO znajdują się w [`docs/roadmap.md`](docs/roadmap.md).

Nie dodano licencji projektu, ponieważ nie została określona w dostarczonych plikach.
