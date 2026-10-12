# DEMO — Prymus Procent (Public materials)

Ten folder zawiera wyłącznie materiały demonstracyjne i dokumentację projektu przeznaczoną do publicznego pokazu. Nie zawiera kodu produkcyjnego, wag modeli, skryptów treningowych ani żadnych poufnych danych.

## Cel tego dokumentu
- Pokazać pomysł i kontrakt (wejście/wyjście) bez ujawniania implementacji.
- Wyjaśnić architekturę i punkty integracji, aby zainteresowani mogli zrozumieć koncepcję.
- Udostępnić sposób zgłaszania chęci uzyskania dostępu do prywatnych komponentów (NDA/licencja).

---

## Co pokazujemy
- High-level flow: nauczyciel -> upload obrazu -> pipeline OCR + AI -> walidacja obliczeń (SymPy) -> wynik (score) + uzasadnienie + tipy do poprawy.
- Kontrakt API (przykładowe wejście/wyjście) bez udostępniania implementacji.
- Przykładowe przypadki użycia i wizualne diagramy (w formie tekstowej/schematów), które ułatwiają prezentację pomysłu na kółku naukowym.

---

## Kontrakt danych (bez implementacji)
Przykładowe polecenie (JSON) wysyłane do publicznego demo endpointu:

- student_token: unikalny token anonimowy powiązany z uczniem (nie PII)
- subject: nazwa przedmiotu, np. "matematyka"
- solution: zapis rozwiązania (LaTeX lub zwykły wyraz/wyrażenie) — w demo używamy symbolicznego sprawdzania
- answer_key: wzorzec/klucz odpowiedzi
- metadata: (opcjonalne) dodatkowe informacje o egzaminie/arkuszu

Przykładowa odpowiedź (JSON):
{
  "student_token": "anon-xxxxxxxx",
  "subject": "matematyka",
  "score": "1/1",
  "mistake_description": null,
  "improvement_tip": "Świetnie — wynik zgadza się z kluczem.",
  "status": "AUTO_GRADED",
  "signed_probe_id": "PROBE-..."  // dyskretne ID wodne / honeytoken
}

---

## Co NIE jest publiczne
- Modele (wagi), treningowe skrypty, pipeline treningowy, prywatne endpointy Vertex AI, rzeczywiste bazy danych (Supabase/Postgres) i produkcyjna logika gradingu.
- Wszystko, co stanowi wartość biznesową projektu, pozostaje prywatne i jest udostępniane wyłącznie na podstawie umowy.

---

## Jak pokazać demo na żywo (bez ujawniania kodu)
1. Przygotuj prostą prezentację z diagramami przepływu (możesz użyć slajdów).  
2. Użyj przykładowego request/response (jak wyżej) do pokazania „co robi system”.  
3. W demo żywym pokaż interfejs (jeśli masz front‑end) lub statyczne przykłady odpowiedzi; nigdy nie pokazuj kodu źródłowego ani kluczy.

---

## Jak prosić o dostęp do prywatnego core (NDA/licencja)
Zobacz plik ACCESS_REQUEST.md w tym repozytorium — tam jest szablon zgłoszenia i informacje o NDA.

---

## Notatka o ochronie IP
Publiczne materiały mają na celu demonstrowanie koncepcji i UX. Ochronę wartości zapewnia:
- brak publikacji modeli i skryptów treningowych,
- licencja własnościowa (Proprietary License) w katalogu głównym,
- procedura udostępniania przez NDA (opisane w ACCESS_REQUEST.md).

---

Dalsze pytania? Napisz issue z tagiem "access-request" lub otwórz kontakt opisany w ACCESS_REQUEST.md.
