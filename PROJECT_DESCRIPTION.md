# Opis projektu: ImpactHer Pay Equity

**ImpactHer Pay Equity** to prototypowy dashboard, który pomaga wskazać różnice w wynagrodzeniach warte dokładniejszego sprawdzenia. Odpowiada na problem nierówności płacowych mogących ograniczać bezpieczeństwo finansowe i rozwój zawodowy kobiet. Jest skierowany do zespołów HR, osób analizujących politykę wynagrodzeń oraz konsultantów prowadzących audyty.

Użytkownik może wczytać własny plik CSV albo uruchomić demonstrację na danych syntetycznych. Aplikacja waliduje dane, porównuje wynagrodzenia kobiet i mężczyzn ogółem oraz w kategoriach pracy, pokazuje przedziały niepewności i proste wskazówki, które obszary sprawdzić dalej. Małe grupy są ukrywane, a raporty eksportują agregaty bez rekordów pracowników i dokładnych liczebności płci. Osobny widok prezentuje wybrane metryki raportowania oraz eksperymentalną demonstrację korekty Heckmana na syntetycznym zbiorze selekcyjnym.

Wartością projektu w kategorii **Technology for Real Change** jest wykorzystanie narzędzi analitycznych do lepszego rozpoznawania barier ekonomicznych kobiet i kierowania uwagi na miejsca wymagające audytu. MVP samo nie zmienia wynagrodzeń, nie wykrywa dyskryminacji automatycznie i nie wydaje oceny prawnej. Jego wyniki są sygnałem do dalszej, eksperckiej analizy.

Prototyp działa lokalnie w Streamlit i korzysta z Pythona, pandas, NumPy, Plotly, statsmodels oraz SciPy. Nie używa zewnętrznego API ani modelu AI w czasie działania. Dane demonstracyjne są syntetyczne. Przed użyciem rzeczywistych danych potrzebne są odpowiednie podstawy przetwarzania, kontrola dostępu, zasady retencji i ocena ryzyka ponownej identyfikacji. Heckman może być wiarygodnie użyty poza demo dopiero po zebraniu danych o mechanizmie selekcji i uzasadnieniu zmiennej wykluczającej.
