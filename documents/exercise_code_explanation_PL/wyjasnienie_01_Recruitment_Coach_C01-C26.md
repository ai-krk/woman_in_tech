# Wyjaśnienie notebooka C01-C26

Dokument opisuje po kolei wszystkie części notebooka `01_Recruitment_Coach_EN.ipynb`.

## C01 - otwarcie workspace

Notebook korzysta z kernela, czyli uruchomionej sesji Pythona, która pamięta zmienne między komórkami.

Pierwsza komórka:

```python
from pprint import pprint
```

importuje czytelny sposób wyświetlania słowników i list.

```python
from workshop_support import (...)
```

importuje funkcje i klasy z lokalnego pliku `workshop_support.py`, między innymi:

- `AgentSession` - zarządza sesją agenta,
- `SimulationClient` - wykonuje symulację bez sieci,
- `connect` - tworzy klienta Gemini,
- `package_check` - sprawdza środowisko,
- `show_result` - wyświetla wynik.

```python
result = custom_result = simulated_result = None
```

Tworzy trzy zmienne wynikowe i ustawia je jako puste.

```python
pprint(package_check())
```

Sprawdza wersję Pythona, ścieżkę kernela oraz wymagane pakiety.

Notebook powinien używać środowiska:

```text
C:\tmp\woman_in_tech\.venv\Scripts\python.exe
```

Jeżeli środowisko jest gotowe, pojawi się `LOCAL NOTEBOOK READY`. W przeciwnym razie można wykonywać tylko ćwiczenia lokalne do czasu naprawy konfiguracji.

## C02 - prywatne połączenie

```python
MODEL = MODEL_DEFAULT
```

Ustawia nazwę domyślnego modelu.

```python
client = None
CONNECT = False
```

Na początku nie ma klienta, a połączenie jest wyłączone.

Po zmianie na:

```python
CONNECT = True
```

funkcja `connect()` poprosi o klucz Gemini w ukrytym polu i utworzy klienta.

Jeśli połączenie się uda, pojawi się `CLIENT READY`. Samo utworzenie klienta nie wysyła jeszcze zapytania do modelu.

Błędy są przekazywane do `safe_error()`, aby nie wyświetlać surowych komunikatów mogących zawierać dane żądania.

## C03 - pierwsza odpowiedź

```python
RUN_LIVE = False
```

Chroni przed przypadkowym wysłaniem zapytania.

Po ustawieniu `RUN_LIVE = True` notebook prosi o wpisanie `RUN`. Jeśli potwierdzisz, wykonuje:

```python
first_response = run_demo(client, MODEL)
show_result(first_response)
```

To jest jedno krótkie zapytanie do modelu bez używania narzędzi.

Sukces powinien pokazać między innymi:

```text
MODE: LIVE GEMINI | STATUS: draft_needs_review
```

Odpowiedź modelu może się różnić. Należy sprawdzić, że zapytanie faktycznie zostało wysłane i że wynik wymaga ludzkiej oceny.

## C04 - wybór roli

`ROLES` to słownik z dwiema fikcyjnymi kartami stanowisk:

- `project_coordinator`,
- `data_analyst`.

Każda karta zawiera tytuł i listę umiejętności.

```python
ROLE_ID = "project_coordinator"
FOCUS = "communication"
TONE = "friendly"
```

Te zmienne wybierają aktualną rolę, umiejętność i ton.

```python
pprint(ROLES[ROLE_ID])
```

Wyświetla wybraną kartę. Jeśli wpiszesz nieistniejący klucz, np. `astronaut`, pojawi się `KeyError`.

Ta część działa lokalnie, bez modelu.

## C05 - baza dowodów

`EVIDENCE` jest listą fikcyjnych rekordów doświadczenia.

Każdy rekord zawiera:

- `id`,
- `skills`,
- `situation`,
- `task`,
- `action`,
- `result`.

Pierwszy rekord dotyczy organizacji wydarzenia i wspiera komunikację oraz planowanie. Drugi opisuje projekt z użyciem Pythona.

```python
pprint(EVIDENCE[0])
```

Wyświetla pierwszy rekord listy. Indeksy zaczynają się od zera, więc `EVIDENCE[0]` to pierwszy element.

Dane są fikcyjne. Nie należy dodawać prawdziwych CV, danych klientów ani prywatnych informacji.

## C06 - narzędzie `get_role`

```python
def get_role(role_id):
```

Definiuje funkcję wyszukującą kartę roli.

```python
if not isinstance(role_id, str) or role_id not in ROLES:
```

Sprawdza, czy identyfikator jest tekstem i czy istnieje w słowniku `ROLES`.

Dla niepoprawnej roli zwraca:

```python
{"status": "unsupported", "allowed_roles": list(ROLES)}
```

Dla poprawnej roli zwraca jej dane:

```python
{"status": "found", "role_id": role_id, **ROLES[role_id]}
```

`**ROLES[role_id]` rozpakowuje dane karty do nowego słownika.

Narzędzie tylko odczytuje lokalne dane. Nie ocenia osoby i nie wysyła zapytania do modelu.

## C07 - narzędzie `get_evidence`

Funkcja wyszukuje doświadczenia związane z umiejętnością:

```python
get_evidence("communication")
```

Możliwe statusy:

- `found` - znaleziono rekordy,
- `missing` - umiejętność jest znana, ale nie ma doświadczenia,
- `unsupported` - umiejętność nie jest obsługiwana.

Na przykład `get_evidence("sql")` zwraca brak doświadczenia, ponieważ żaden rekord nie zawiera SQL.

## C08 - baza pytań

Tworzy słowniki `QUESTIONS` i `TONES`.

`QUESTIONS` zawiera pytania według umiejętności, a `TONES` początki pytań:

```python
"friendly": "Take a moment to think. "
"direct": "Be specific. "
```

Komórka tylko tworzy dane i nie powinna wykonywać zapytania do modelu.

## C09 - narzędzie `get_question`

```python
def get_question(skill, tone):
```

Funkcja sprawdza, czy umiejętność i ton istnieją, a następnie łączy wstęp z pytaniem.

Przykład:

```python
get_question("communication", "friendly")
```

zwraca pytanie zaczynające się od `Take a moment to think.`.

Funkcja działa lokalnie.

## C10 - przepisy umiejętności

`SKILLS` zawiera instrukcje dla modelu:

- `role_decoder` - sprawdza rolę,
- `star_coach` - pracuje z dowodami i tworzy szkic STAR,
- `interview_practice` - zadaje pytanie praktyczne.

`ACTIVE_SKILLS` określa aktywne przepisy.

W tej części można dodać własne zdanie do jednego przepisu, np. `Explain any jargon in plain English.`.

## C11 - składanie instrukcji

`RULES` zawiera główne zasady coacha, np. zakaz wymyślania doświadczeń, używanie tylko dostarczonych narzędzi i podawanie identyfikatorów dowodów.

```python
INSTRUCTIONS = RULES + "\n" + "\n".join(
    SKILLS[name] for name in ACTIVE_SKILLS
)
```

Łączy reguły z aktywnymi przepisami. `INSTRUCTIONS` zostanie później przekazane modelowi.

## C12 - rejestracja narzędzi

`TOOL_RULES` opisuje narzędzia dostępne agentowi:

- `get_role`,
- `get_evidence`,
- `get_question`.

Dla każdego narzędzia określa funkcję, opis i dozwolone argumenty.

```python
TOOLS = describe_tools(TOOL_RULES)
```

tworzy schemat narzędzi dla Gemini.

## C13 - kontrola uprawnień

```python
def execute_tool(name, arguments):
    return execute_safe(name, arguments, TOOL_RULES)
```

Funkcja przekazuje żądanie do walidacji.

Model może nazwać narzędzie `send_email`, ale ponieważ nie ma go w `TOOL_RULES`, Python je odrzuci.

Najważniejsza zasada brzmi:

> Model może zaproponować działanie, ale Python decyduje, czy wolno je wykonać.

## C14 - pętla agenta

`run_coach` opisuje cały przepływ:

1. utworzenie `AgentSession`,
2. wysłanie zadania do modelu,
3. odczyt odpowiedzi,
4. znalezienie żądań narzędzi,
5. sprawdzenie uprawnień,
6. wykonanie dozwolonych narzędzi,
7. przekazanie wyników do modelu,
8. powtórzenie albo zakończenie szkicem odpowiedzi.

Każda sesja ma maksymalnie 5 zapytań do modelu i 6 wywołań narzędzi.

Zdefiniowanie funkcji nie oznacza jeszcze jej uruchomienia.

## C15 - przygotowanie zadania

`make_request` buduje tekst zadania z roli, umiejętności i tonu:

```python
REQUEST = make_request(ROLE_ID, FOCUS, TONE)
```

Wynik może zaczynać się tak:

```text
Prepare me for role project_coordinator. Focus on communication. Tone: friendly.
```

To jest zadanie, które później otrzyma agent.

## C16 - uruchomienie prawdziwego coacha

Domyślnie:

```python
RUN_LIVE = False
```

Po ustawieniu `True` i wpisaniu `RUN` wykonywane jest:

```python
result = run_coach(REQUEST, client)
show_result(result)
```

W tej części model może już poprosić o użycie trzech lokalnych narzędzi. Wywołanie jest ograniczone budżetem i nie ma automatycznych ponowień.

## C17 - sprawdzenie wyniku

Ta komórka pokazuje, co rzeczywiście wydarzyło się podczas sesji.

```python
result["status"]
```

pokazuje status, a wpisy w `result["trace"]` pokazują faktyczne wyniki narzędzi.

Nie należy zakładać, że model użył narzędzia tylko dlatego, że wspomniał o nim w odpowiedzi. Trzeba sprawdzić ślad `trace`.

## C18 - sześć testów lokalnych

Testy `assert` sprawdzają lokalne funkcje i uprawnienia:

- komunikacja ma dowody,
- SQL poprawnie zwraca brak danych,
- nieznana rola jest odrzucona,
- nieznany ton jest odrzucony,
- `send_email` jest zablokowane,
- dodatkowy argument jest odrzucony.

Komunikat:

```text
6 LOCAL CHECKS PASSED: no model evaluated.
```

oznacza, że sprawdzono kod lokalny, a nie model.

## C19 - celowo nieudany test

Zmiana:

```python
expected_status = "missing"
```

na:

```python
expected_status = "found"
```

powoduje `AssertionError`, bo dla SQL faktyczny status to `missing`.

To ćwiczenie pokazuje, że test wykrywa błędne oczekiwanie. Po eksperymencie trzeba przywrócić `missing`.

## C20 - granica bezpieczeństwa

Kod tworzy tekst udający złośliwą instrukcję i próbuje wywołać `send_email`.

Python odrzuca próbę, ponieważ narzędzia nie ma na liście dozwolonych.

To test lokalnej kontroli uprawnień. Nie jest to pełny test odporności modelu na prompt injection.

## C21 - personalizacja coacha

Zmienia się ustawienia, np.:

```python
ROLE_ID = "data_analyst"
FOCUS = "sql"
TONE = "direct"
```

Można także zmienić przepis `star_coach`.

Kod ponownie buduje `INSTRUCTIONS` i `REQUEST`.

Dla SQL wynik powinien wskazywać:

```python
{"status": "missing", "records": []}
```

Brak danych jest uczciwym wynikiem, a nie błędem.

## C22 - dodatkowy ton

Po ustawieniu:

```python
TRY_ADVANCED = True
```

dodawany jest ton `encouraging`.

Kod aktualizuje dane tonów, reguły argumentów, schemat narzędzi i zadanie.

Pokazuje to, że nową wartość trzeba dodać zarówno do danych, jak i do walidacji.

## C23 - porównanie zmienionego coacha

Można uruchomić kolejne zapytanie na żywo albo porównać lokalnie:

```python
get_evidence(FOCUS)
get_question(FOCUS, TONE)
```

Różnica w sformułowaniu odpowiedzi nie jest sama w sobie dowodem poprawy. Należy porównać fakty, użyte narzędzia i status.

## C24 - symulacja

Jeśli API nie działa, można ustawić:

```python
RUN_SIMULATION = True
```

`SimulationClient` udaje odpowiedzi i wywołania narzędzi, ale nie jest modelem i nie używa sieci.

Wynik musi być opisany jako symulacja, np.:

```text
SIMULATION: authored fixture, no model call
```

Symulacja jest planem awaryjnym, a nie dowodem gotowości API.

## C25 - refleksja i zapis

W `MY_LEARNING` wpisuje się:

```python
MY_LEARNING = "My change was ... My test checked ... A remaining limitation is ..."
```

Po ustawieniu:

```python
SAVE_NOTES = True
```

tekst zostanie zapisany do `my_coach_notes.md`.

Jeśli plik już istnieje, nie zostanie nadpisany.

## C26 - zamknięcie

Jeśli klient istnieje, połączenie jest zamykane:

```python
if client is not None:
    client.close()
```

Następnie:

```python
client = None
```

usuwa referencję do klienta, a:

```python
CONNECT = RUN_LIVE = RUN_SIMULATION = SAVE_NOTES = False
```

wyłącza wszystkie przełączniki.

Przed udostępnieniem notebooka sprawdź, czy:

- `CONNECT` jest ustawione na `False`,
- `RUN_LIVE` jest ustawione na `False`,
- `RUN_SIMULATION` jest ustawione na `False`,
- `SAVE_NOTES` jest ustawione na `False`,
- klucz API nie znajduje się w kodzie ani wynikach,
- używane były tylko fikcyjne dane.

Końcowy komunikat to:

```text
Client closed. Save your notebook and review it before sharing.
```
