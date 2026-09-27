# Wyjaśnienie: 03 Software Engineer Interview

Ten notebook jest laboratorium przygotowania do fikcyjnej rozmowy na stanowisko Junior Software Engineer. Pokazuje, jak agent pracuje z dowodami dotyczącymi Pythona, Javy i debugowania, bez wymyślania brakujących faktów.

## 1. Poznanie kandydata i roli

Notebook używa fikcyjnej kandydatki oraz fikcyjnego stanowiska. Informacje są ćwiczeniem, a nie prawdziwą aplikacją rekrutacyjną.

Trzy ważne pojęcia to:

- dowód - fakt zapisany w dostarczonych danych,
- sugestia - pomysł na przyszłe ćwiczenie, który nie jest faktem z projektu,
- granica - lokalna reguła określająca, jakie narzędzia mogą zostać użyte.

## 2. Jednorazowe przygotowanie scenariusza

### S01 - helpery i model

Notebook importuje funkcje z `workshop_support.py` i ustawia nazwę modelu w `MODEL`.

### S02 - karta roli

`ROLES` zawiera kartę `junior_software_engineer`. Karta opisuje fikcyjną rolę oraz umiejętności potrzebne do ćwiczeń.

### S03 - dowody z Pythona

`PYTHON_RECORD` opisuje projekt w Pythonie. Rekord może być używany tylko do twierdzeń, które rzeczywiście się w nim znajdują.

### S04 - dowody z Javy

`JAVA_RECORD` opisuje projekt katalogu biblioteki w Javie. Zawiera informacje o wyszukiwaniu tytułów i testach.

### S05 - historia debugowania

`DEBUG_RECORD` opisuje problem z wyszukiwaniem bez uwzględniania wielkości liter, zmianę porównania i dodanie testu regresyjnego.

### S06 - odczyt faktów

`get_role(role_id)` zwraca kartę roli, a `get_evidence(skill)` filtruje rekordy według umiejętności. W tym notebooku `get_evidence("java")` powinno znaleźć rekordy Java i debugowania.

Wartości takie jak SQL lub HTTP mogą być znane jako obszary do nauki, ale jeśli nie ma ich w rekordach, wynik powinien być `missing`.

### S07 - pytania dla początkujących

`QUESTIONS` i `TONES` zawierają pytania oraz początki pytań. `get_question(skill, tone)` zwraca jedno dozwolone pytanie.

### S08 - trzy narzędzia tylko do odczytu

`TOOL_RULES` rejestruje trzy narzędzia:

- odczyt roli,
- wyszukanie dowodów,
- pobranie pytania.

Nie ma narzędzia do wysyłania aplikacji, wykonywania kodu ani wysyłania wiadomości.

Próby `send_email`, `send_application` lub `run_code` powinny zostać odrzucone.

### S09 - zasady uczciwego coacha

`RULES` wymaga, aby coach:

- używał tylko dostarczonych faktów,
- rozdzielał pracę kandydatki od pracy zespołu,
- oznaczał sugestie jako sugestie,
- nie wymyślał wyników, użytkowników, frameworków ani kwalifikacji.

### S10 - rodzaje pomocy

`SKILLS` zawiera przepisy, np. `plain_explainer`, `project_story`, `mock_interviewer`, `feedback_coach`, `technical_followup` i `study_planner`.

Przepisy mówią modelowi, jak pomagać, ale nie dają mu dodatkowych uprawnień.

### S11 - ta sama ograniczona pętla agenta

`run_coach()` działa tak samo jak w pozostałych notebookach:

1. tworzy sesję,
2. wysyła zadanie,
3. odbiera żądania narzędzi,
4. Python sprawdza je lokalnie,
5. wykonuje tylko dozwolone odczyty,
6. przekazuje wyniki modelowi,
7. kończy się szkicem albo limitem.

Limit jednej sesji to maksymalnie 5 zapytań modelu i 6 prób użycia narzędzi.

### S12 - jedno zadanie naraz

`set_case(title, focus, recipe, prompt)` ustawia aktualny przypadek:

- tytuł,
- umiejętność,
- przepis,
- prompt.

Następnie budowane są `CURRENT_CASE`, `REQUEST` i `INSTRUCTIONS`.

### S13 - mały budżet sesji

Kod przygotowuje maksymalnie trzy próby live dla całego notebooka. To warsztatowa ochrona przed przypadkowymi powtórzeniami, a nie limit konta Google.

### S14 - sprawdzenie nowej roli

Przed użyciem Gemini wykonywane są lokalne asercje sprawdzające rolę, rekordy, brakujące umiejętności i blokadę nieistniejących narzędzi.

## 3. Wybór zadania

### P01 - cel rozmowy

P01 przygotowuje podstawowe zadanie dla agenta, np. pomoc w przygotowaniu do roli Junior Software Engineer. Sama komórka wyboru przypadku nie wysyła zapytania.

## 4. Prywatne połączenie

`CONNECT = False` oznacza brak połączenia. Po ustawieniu `True` funkcja `connect()` prosi o klucz Gemini w ukrytym promptcie.

Połączenie nie oznacza jeszcze wykonania zapytania. Klucz nie powinien trafić do kodu, notebooka ani wiadomości.

## 5. Jedno uruchomienie wybranego przypadku

Wspólna komórka live jest jedyną komórką, która wysyła zapytania modelu.

Sprawdza:

- czy wybrano przypadek,
- czy istnieje klient,
- czy nie wykorzystano trzech prób,
- czy użytkownik wpisał `RUN`.

Następnie wywołuje `run_coach()`, zapisuje wynik w `runs` i wyświetla go przez `show_result()`.

Każde uruchomienie zaczyna świeżą rozmowę. Agent nie pamięta automatycznie poprzedniej odpowiedzi.

## 6. Dodatkowe przypadki P02-P08

### P02 - prostsze wyjaśnienie

Prosi o wyjaśnienie pojęć `unit test`, `regression test` i `API` prostym językiem, z przykładami.

Nowy przykład dydaktyczny musi być oznaczony jako przykład, a nie jako doświadczenie kandydatki.

### P03 - uczciwa historia projektu

Prosi o przygotowanie około 60-sekundowej odpowiedzi o projekcie Java.

Odpowiedź ma rozdzielać osobisty wkład od pracy kolegi lub zespołu i nie może dodawać liczb, użytkowników ani komercyjnych rezultatów.

### P04 - jedno pytanie z debugowania

Prosi o jedno przyjazne pytanie dotyczące debugowania oraz dwie krótkie wskazówki.

Agent nie powinien od razu udzielać pełnej odpowiedzi, bo celem jest samodzielna praktyka.

### P05 - informacja zwrotna do własnej odpowiedzi

`INTERVIEW_QUESTION` zawiera pytanie, a `MY_ANSWER` przykładową odpowiedź ćwiczeniową.

Późniejszy prompt prosi o:

- jedną rzecz zrobioną dobrze,
- dwa szczegóły do poprawy,
- jedno pytanie uzupełniające,
- mocniejszą wersję odpowiedzi opartą na faktach.

To nie jest automatyczna kontynuacja rozmowy. Pytanie i odpowiedź muszą zostać przesłane ponownie w nowym zadaniu.

### P06 - techniczne pytania uzupełniające

Prosi o trzy pytania dotyczące wyszukiwania tytułów i testów w projekcie Java.

Sugestie dodatkowych testów muszą być oznaczone jako pomysły do wypróbowania, a nie testy już napisane.

### P07 - mały plan przygotowania

Prosi o trzy sesje po 20 minut:

- wyjaśnienie projektu,
- ćwiczenie testowania lub debugowania,
- podstawowe pojęcie API albo SQL.

API i SQL powinny być pokazane jako obszary do nauki, nie jako wcześniejsze doświadczenie.

### P08 - zakwestionowanie przesadzonego twierdzenia

Prompt zawiera celowo przesadzoną wersję historii, np. samodzielne zbudowanie platformy produkcyjnej dla 1000 osób i poprawę wydajności o 90%.

Agent powinien wskazać wszystkie niepoparte twierdzenia i przepisać historię tak, aby trzymała się rzeczywistych rekordów.

Nie należy zachowywać zmyślonych liczb jako szacunków.

## 7. Sprawdzenie faktycznego działania agenta

Zapisane w `runs` wyniki można przeglądać bez kolejnego zapytania.

Podczas kontroli sprawdź:

- czy każde osobiste twierdzenie ma konkretny rekord,
- czy odpowiedź rozdziela wkład kandydatki i zespołu,
- czy brakujące informacje są przyznane,
- czy nowe przykłady są oznaczone jako sugestie,
- czy odpowiedź odpowiada na wybrane pytanie,
- czy trace pokazuje użycie właściwych narzędzi.

Sam identyfikator dowodu nie wystarcza. Trzeba porównać twierdzenie z treścią rekordu.

## 8. Ćwiczenie bez live API

Jeśli Gemini jest niedostępne, można czytać dane, wybierać przypadki, przewidywać odpowiedzi i wykonywać lokalne testy.

Tę pracę należy oznaczyć jako `NOT TESTED LIVE`. Lokalny test sprawdza kod i dane, ale nie dowodzi, że model zastosował instrukcje.

Przykład historii Java zawarty w notebooku jest autorskim przykładem klasowym, a nie wcześniejszą odpowiedzią Gemini.

## 9. Własna mała poprawa

Użytkownik wybiera jeden mały element do zmiany, np. prompt, ton, strukturę przepisu albo sposób zadawania pytania.

Dobra zmiana powinna mieć:

- obserwowalny efekt,
- lokalny test,
- jedno ograniczenie, które nadal pozostaje.

## 10. Bezpieczne zakończenie

Na końcu klient jest zamykany, a przełączniki live są ustawiane na `False`:

```python
client = None
CONNECT = RUN_LIVE = False
```

Przed udostępnieniem notebooka należy zapisać zmiany, nie udostępniać klucza, wyłączyć tryb live i sprawdzić, czy wyniki nie zawierają prywatnych danych.
