# Lecția 23 — Formular, rutare și tratarea erorilor

**Săptămâna:** 8 · **Data de reper:** 14.01.2027 · **Durata:** 90 minute

## Obiective

- Adaugi al doilea ecran: formularul de creare.
- Folosești React Router și validare coerentă cu backendul.
- Pregătești interfața pentru trei teste e2e stabile.

## Intrări necesare

[Lecția 22](./lectia-22.md) finalizată; user de laborator cu drept de creare și contract POST stabil.

## Parcurs

1. **15 min — Navigare.** Configurează rutele listei și formularului și navigarea dintre ele. Verifică accesul direct și refreshul pe o rută; serverul frontend trebuie să servească fallbackul SPA corespunzător.
2. **25 min — Formular controlat.** Tipizează state-ul și evenimentele, asociază labelurile și afișează validarea titlului gol. Blochează trimiterea multiplă cât timp cererea este în curs.
3. **20 min — POST.** Trimite datele cu tokenul potrivit și tratează explicit 400, 401, 403 și erorile de server. După succes revino la listă și reîncarcă datele sau actualizează state-ul fără duplicate.
4. **15 min — Accesibilitate și selectori.** Verifică folosirea cu tastatura și asocierea mesajelor de eroare. Adaugă selectori stabili pentru teste, independenți de clasele CSS și de poziția elementelor.
5. **15 min — Repetabilitate.** Definește o bază de test izolată și metoda de curățare printr-o operație administrativă disponibilă numai profilului de test sau prin resetarea DB între scenarii. Pregătește autentificarea de test printr-o sesiune reală reutilizabilă, nu printr-un token hardcodat.

## Livrabile

- Două ecrane conectate prin Router.
- Formular cu validare și mesaje de eroare reale.
- Strategie de date și autentificare pentru Cypress.

## Criterii de finalizare

- [ ] Un task valid apare în listă după creare și refresh.
- [ ] Titlul gol este respins în UI și rămâne respins de API.
- [ ] O eroare de rețea nu produce notificare de succes.
- [ ] Formularul este utilizabil cu tastatura și are labeluri corecte.
- [ ] Resetarea datelor de test nu poate șterge datele mediului personal/producție.

## Dovadă și recapitulare

Păstrează rezultatul unui flux valid și al unuia invalid. Explică diferența dintre validarea pentru UX și validarea obligatorie pe server.

**Navigare:** [Lecția precedentă](./lectia-22.md) · [Index](../README.md) · [Lecția următoare](./lectia-24.md)
