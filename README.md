# matura-assist-demo
Demo to my CKE matura exam assistant with a tracker for students and a grading (based on an answer sheet or not) and automatic organization of students.

# Prymus Procent

## Projekt: Prymus Procent

### 1. Vision & Purpose (What & Why)

Prymus Procent to system klasy B2B/B2C wspierający nauczycieli przedmiotów ścisłych w ocenianiu arkuszy maturalnych.

**Problem:**
- Ręczne sprawdzanie zadań otwartych trwa godzinami.
- Proces jest podatny na błędy ludzkie i niespójności w punktacji.
- Nauczyciel traci czas na powtarzalne działania zamiast na diagnozę ucznia.

**Rozwiązanie:**
- Hybrydowy system AI, który analizuje zdjęcia rozwiązań uczniów.
- Silnik widzenia rozpoznaje pismo ręczne, wyciąga kroki rozwiązań i weryfikuje logikę matematyczną.
- System punktuje zgodnie z kluczem CKE i generuje raporty diagnostyczne dla klasy.

**Cel biznesowy:**
- Skrócenie czasu oceniania.
- Zwiększenie obiektywizmu ocen.
- Dostarczenie nauczycielowi danych o typowych błędach uczniów.

---

### 2. Core System Logic (How it works)

#### Wejście
- Nauczyciel przesyła zdjęcie lub skan arkusza wraz z kluczem odpowiedzi.
- System obsługuje pliki graficzne z rozwiązań ręcznych.

#### Vision Engine
- Gemini 2.5 Flash analizuje obraz za pomocą OCR + rozumowania.
- System nie skupia się wyłącznie na wyniku końcowym.
- Weryfikuje kroki pośrednie, poprawność wzorów, zapis matematyczny i logikę rozwiązania.

#### Privacy by Design
- Dane uczniów są obsługiwane jako anonimizowane tokeny, np. `(id-szkoly)-4C-05`.
- Żadne dane osobowe nie są przesyłane do chmury.
- Dane przechowywane w bazie są powiązane z tokenem, a nie z nazwiskiem ucznia.

#### Wyjście
- Sugerowana punktacja, np. `1/2 pkt`.
- Uzasadnienie błędu, np. `Błąd rachunkowy w 3. linijce`.
- Statystyka grupowa, np. `80% klasy nie rozumie logarytmów`.
- Personalne błędy ucznia, np. `"id-ucznia" ma powtarzający się problem z wyznaczaniem delty'`

---

### 3. Architecture Stack

#### Backend
- Python
- Zalecany framework: FastAPI dla obsługi asynchronicznej i skalowalnych endpointów.

#### AI Brain
- Vertex AI / Gemini 2.5 Flash
- Obsługa widzenia, OCR oraz interpretacji zapisu ręcznego.

#### Database
- Supabase (PostgreSQL)

#### Configuration
- Twardo odseparowana konfiguracja przez pliki `.env`.
- Brak twardokodowanych kluczy w repozytorium.

---

### 4. Technical Audit & Development Rules (Strict Mentor Mode)

#### A. Database Integrity

**Wymagania:**
1. **STOP Race Conditions**
   - Funkcja `pobierz_kolejny_numer()` zostaje usunięta.
   - Identyfikatory muszą być generowane automatycznie przez bazę danych.
   - Dozwolone typy: `BigSerial`, `Identity`.

2. **Relacje między tabelami**
   - Tabela `teachers` powinna być powiązana z `tests`.
   - Tabela `tests` powinna być powiązana z `results`.
   - Zastosowanie kluczy obcych (`Foreign Keys`).

3. **Przykładowy model relacji**
   - `teachers(id)`
   - `tests(id, teacher_id, ...)`
   - `results(id, test_id, student_token, ...)`

#### B. AI Reliability

**Wymagania:**
1. **Hybrid Grading**
   - Gemini wyciąga równania i zapis do formatu LaTeX.
   - Docelowo implementujemy bibliotekę `SymPy` do twardej weryfikacji matematycznej.
   - AI nie może samodzielnie decydować o poprawności obliczeń.

2. **Prompt Guard**
   - System musi zawierać instrukcje ograniczające `hallucinations`.
   - Jeśli zapis jest nieczytelny lub niejednoznaczny, system musi zwrócić status `MANUAL_REVIEW`.

#### C. Performance & Scaling

**Wymagania:**
1. **Asynchrony**
   - Wszystkie wywołania API muszą być asynchroniczne (`async/await`).
   - Interfejs nie może zawieszać się podczas przetwarzania obrazu.

2. **Image Pre-processing**
   - Zdjęcie musi być zoptymalizowane przed wysłaniem do chmury.
   - Stosujemy kompresję i korekcję kontrastu przez Pillow/OpenCV.
   - Cel: redukcja kosztów i czasu odpowiedzi.

#### D. Error Handling

**Wymagania:**
1. Każdy moduł musi mieć bloki `try-except`.
2. W przypadku awarii Gemini, system zapisuje obraz do kolejki do późniejszego przetworzenia.
3. Nie wolno zwracać błędu użytkownikowi, jeśli pipeline AI nie jest chwilowo dostępny.

---

### 5. Next Steps (Roadmap)

#### Faza 1 — Refaktoryzacja bazy danych
- Przejście na automatyczne ID w Supabase.
- Usunięcie mechanizmu ręcznej numeracji tokenów.
- Zdefiniowanie relacji między nauczycielem, testem i wynikami.

#### Faza 2 — Integracja Vision
- Implementacja asynchronicznego endpointu do wysyłki zdjęć do Gemini 1.5 Flash.
- Dodanie pre-processingu obrazu przed analizą.

#### Faza 3 — Prompt Engineering
- Tworzenie systemowych instrukcji dla AI.
- Obsługa polskich schematów oceniania CKE.
- Wprowadzenie zasad `MANUAL_REVIEW` i `SymPy` validation.

#### Faza 4 — Stabilizacja i operacyjność
- Monitoring błędów.
- Kolejki przetwarzania obrazów.
- Testy regresyjne dla gradingu i zapisów do bazy.

---

### 6. System Principles

- **SOLID**: czysty podział odpowiedzialności modułów.
- **Privacy first**: brak danych osobowych w chmurze.
- **Fail-safe**: awaria AI nie blokuje pracy użytkownika.
- **Auditability**: każdy wynik powinien być możliwy do wyjaśnienia.

---

### 7. Current Implementation Notes

Obecny projekt zawiera prototypowe elementy integracji z Vertex AI i Supabase. W ramach dalszego rozwoju należy przejść z podejścia opierającego się na ręcznej numeracji i twardokodowanej konfiguracji do modelu zgodnego z wymaganiami powyżej.

---

### 8. Expected Output Contract

Każda analiza powinna zwracać co najmniej:

- `student_token`
- `subject`
- `score`
- `mistake_description`
- `improvement_tip`
- `status`

Przykładowe statusy:
- `AUTO_GRADED`
- `MANUAL_REVIEW`
- `QUEUED_FOR_RETRY`

### 9. Matura exam progress tracker

- In the file final_countdown_II.py is the progress tracker
---

### 9. Final Instruction for AI in VS Code

> Działaj jako Senior Python Developer. Przestrzegaj zasady czystego kodu (SOLID). Nie naprawiaj błędów za użytkownika — dostarczaj szkielety kodu i wyjaśniaj błędy w terminalu. Zwracaj się do użytkownika: Panie Stark.
