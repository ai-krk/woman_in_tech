# Wyjaśnienie: 02 Agent Playground

Ten notebook jest ogólnym placem zabaw do eksperymentowania z agentem. Pokazuje, jak jedna mała zmiana w roli, tonie, danych, promptach albo przepisie wpływa na lokalne wyniki i na odpowiedź modelu.

## 1. Przygotowanie playgroundu

### S01 - załadowanie helperów

Notebook importuje `pprint` oraz elementy z `workshop_support.py`, między innymi `AgentSession`, `SimulationClient`, `package_check`, `connect`, `run_coach` i `show_result`.

`package_check()` sprawdza wybrany kernel i wymagane pakiety. Zmienne `result`, `custom_result` i `simulated_result` są na początku ustawione na `None`.

### S02 - karty ról

`ROLES` zawiera dwie fikcyjne role: `project_coordinator` i `data_analyst`. Zmienne `ROLE_ID`, `FOCUS` i `TONE` wybierają aktualną rolę, umiejętność i ton.

### S03 - doświadczenia

`EVIDENCE` zawiera fikcyjne rekordy doświadczeń. Każdy rekord ma identyfikator, umiejętności, sytuację, zadanie, działanie i rezultat. Są to dane klasowe, nie prawdziwe CV.

### S04 - odczyt roli

`get_role(role_id)` sprawdza, czy rola istnieje w `ROLES`. Zwraca `found` z kartą roli albo `unsupported` z listą dozwolonych ról.

### S05 - wyszukanie dowodów

`get_evidence(skill)` filtruje `EVIDENCE` według umiejętności. Zwraca `found`, `missing` albo `unsupported`. Brak dowodu ma pozostać brakiem dowodu.

### S06 - pytania i tony

`QUESTIONS` zawiera pytania dla umiejętności, a `TONES` początki pytań, np. `friendly` i `direct`.

### S07 - jedno pytanie praktyczne

`get_question(skill, tone)` sprawdza dane wejściowe i łączy wybrany początek z pytaniem. Działa lokalnie, bez modelu.

### S08 - trzy przepisy umiejętności

`SKILLS` opisuje trzy zadania modelu: odczyt roli, pracę z dowodami STAR i ćwiczenie pytania. `ACTIVE_SKILLS` określa, które przepisy są aktywne.

### S09 - reguły faktów i bezpieczeństwa

`RULES` zawiera zasady, np. zakaz wymyślania doświadczeń, metryk, pracodawców i kwalifikacji. `INSTRUCTIONS` łączy te zasady z aktywnymi przepisami.

### S10 - dozwolone narzędzia

`TOOL_RULES` rejestruje trzy narzędzia: `get_role`, `get_evidence` i `get_question`. `describe_tools()` buduje ich schemat przekazywany modelowi.

### S11 - wykonanie tylko dozwolonego narzędzia

`execute_tool()` przekazuje żądanie do `execute_safe()`. Python ponownie sprawdza nazwę narzędzia, argumenty i dozwolone wartości. Nazwa, której nie ma w rejestrze, np. `send_email`, zostaje odrzucona.

### S12 - ograniczona pętla agenta

`run_coach()` wykonuje pętlę:

1. tworzy `AgentSession`,
2. wysyła zadanie do modelu,
3. odczytuje żądania narzędzi,
4. sprawdza je lokalnie,
5. wykonuje dozwolone narzędzia,
6. zwraca wyniki modelowi,
7. kończy się odpowiedzią albo limitem.

Jedna sesja ma maksymalnie 5 zapytań modelu i 6 wywołań narzędzi.

### S13 - podstawowe zadanie

`make_request()` buduje `REQUEST` z roli, umiejętności i tonu. To jest tekst, który agent ma wykonać.

### S14 - odświeżenie ustawień

`refresh_agent()` odbudowuje `INSTRUCTIONS`, `REQUEST` i `TOOLS` po zmianie ustawień. Jest ważne, bo zmiana wcześniejszej komórki nie zmienia automatycznie wartości już znajdujących się w pamięci kernela.

### S15 - czysty start eksperymentu

`reset_experiment()` przywraca ustawienia bazowe. Dzięki temu każdy eksperyment zaczyna się od porównywalnego punktu, a zmiany z poprzedniego eksperymentu nie przenikają do następnego.

### S16 - przygotowanie uruchomień

Notebook ustawia między innymi:

```python
CONNECT = False
RUN_LIVE = False
MAX_LIVE_RUNS = 2
live_runs = 0
runs = []
```

`runs` przechowuje migawki wykonanych prób w pamięci. Limit dwóch prób jest ograniczeniem warsztatowym, nie limitem konta Google.

## 2. Ustalenie wersji bazowej

Najpierw uruchamia się ustawienia bazowe lokalnie. Należy sprawdzić rolę, dowody, pytanie, instrukcje, narzędzia i zadanie, zanim zostanie użyty model.

## 3. Prywatne połączenie

`CONNECT = False` oznacza brak połączenia. Po zmianie na `True` funkcja `connect()` prosi o klucz w ukrytym promptcie i tworzy klienta.

Samo połączenie nie wysyła jeszcze zapytania. Klucza nie wolno wpisywać do kodu, notebooka ani wiadomości.

## 4. Jedno uruchomienie eksperymentu

Wspólna komórka uruchomieniowa sprawdza kolejno:

- czy `RUN_LIVE` jest włączone,
- czy istnieje klient,
- czy nie wykorzystano już dwóch prób,
- czy użytkownik wpisał `RUN`.

Potem odświeża ustawienia, zwiększa licznik, wywołuje `run_coach()`, zapisuje migawkę i pokazuje wynik.

`draft_needs_review` oznacza szkic wymagający ludzkiego sprawdzenia, a nie błąd techniczny.

## 5. Eksperymenty E01-E08

Eksperymenty nie wysyłają API same z siebie. Najpierw zmieniają jedną rzecz i wykonują lokalny test. Dopiero wspólna komórka uruchomieniowa może wykonać próbę na żywo.

### E01 - bardziej bezpośredni ton

Zmienia `TONE` z `friendly` na `direct`. Lokalne sprawdzenie potwierdza, że pytanie zaczyna się od `Be specific.`. Fakty z dowodów nie powinny się zmienić.

### E02 - inna rola

Zmienia `ROLE_ID` na `data_analyst`. Karta zawiera Python i SQL, ale sama obecność wymagania nie dowodzi doświadczenia kandydata.

### E03 - skupienie na planowaniu

Zmienia `FOCUS` na `planning`. Lokalny wynik powinien zawierać tylko rekord `event-1`. Pytanie praktyczne również się zmienia.

### E04 - uczciwy brak doświadczenia

Zmienia `FOCUS` na `negotiation`. Funkcja zwraca:

```python
{"status": "missing", "records": []}
```

Poprawna odpowiedź powinna przyznać brak dowodu i zaproponować ćwiczenie, zamiast wymyślać historię sukcesu.

### E05 - krótsza odpowiedź

Dodaje do promptu:

```python
PROMPT_ADDON = "Keep the complete answer under 100 words."
```

Należy sprawdzić, czy po skróceniu nadal zostały fakty, pytanie praktyczne i następny krok. Model może nie zachować dokładnego limitu słów.

### E06 - bardziej uporządkowany przepis STAR

Zmienia tylko `star_coach`, wymagając czterech nagłówków: Situation, Task, Action i Result. Dowody i uprawnienia pozostają bez zmian. Same nagłówki nie gwarantują prawdziwości odpowiedzi.

### E07 - zmiana pytania narzędzia

Zmienia dane w `QUESTIONS`, a następnie sprawdza, czy `get_question()` zwraca nowe pytanie. To eksperyment na danych narzędzia, nie na instrukcjach modelu.

### E08 - zachęcający ton

Dodaje ton `encouraging`, odświeża dozwolone argumenty i schemat narzędzi. Sama zmiana `TONES` bez odświeżenia rejestru spowodowałaby odrzucenie argumentu jako niedozwolonego.

## 6. Menu dodatków do promptu

`PROMPT_MENU` zawiera gotowe dodatki do zadania, np. prośbę o prostszy język, krótszą odpowiedź albo następny krok. `PROMPT_CHOICE` wybiera jeden dodatek.

Dodatek zmienia instrukcję, ale nie zmienia lokalnych uprawnień ani faktów.

## 7. Porównywanie obserwacji

Zapisane migawki można przeglądać bez kolejnego zapytania. Należy sprawdzać:

- czy osobiste twierdzenie ma dowód,
- czy odpowiedź nie przypisuje sobie cudzej pracy,
- czy brakujące szczegóły są oznaczone,
- czy sugestie są oznaczone jako przyszłe pomysły,
- czy trace pokazuje użyte narzędzia.

Jedna para wyników nie dowodzi, że zmiana zawsze poprawia działanie agenta. Imponujące sformułowanie nie zastępuje weryfikacji faktów.

## 8. Brak Gemini

Można kontynuować lokalnie: czytać role i dowody, wybierać eksperyment, przewidywać wynik i wykonywać testy. Taki wynik należy oznaczyć jako `NOT TESTED LIVE`.

Lokalny test sprawdza dane i Python, ale nie dowodzi, że Gemini zastosował instrukcję.

## 9. Zapis tego, czego się nauczono

`MY_OBSERVATIONS` służy do zapisania:

- co zostało zmienione,
- jaki był lokalny test,
- co pokazał wynik,
- jakie ograniczenie pozostało.

## 10. Bezpieczne zakończenie

Na końcu klient jest zamykany, a przełączniki są wyłączane:

```python
client = None
CONNECT = RUN_LIVE = False
```

Przed udostępnieniem notebooka należy zapisać plik, ustawić tryby live na `False`, usunąć wrażliwe dane i wyczyścić wyniki, jeśli zawierają informacje prywatne.
