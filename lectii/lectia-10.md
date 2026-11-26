# Lecția 10 — Timeout, retry și circuit breaker

**Săptămâna:** 4 · **Data de reper:** 15.12.2026 · **Durata:** 90 minute

## Obiective

- Simulezi o dependență lentă sau indisponibilă, fără servicii publice instabile.
- Configurezi timeout, retry, circuit breaker și bulkhead.
- Măsori comportamentul, nu doar prezența configurării.

## Intrări necesare

[Lecția 09](./lectia-09.md) finalizată; teste care pot controla răspunsul unui serviciu HTTP de laborator.

## Parcurs

1. **30 min — Lectură și proiectare.** Compară failure modes: răspuns lent, timeout, eroare tranzitorie, eroare funcțională și suprasarcină. Alege un apel GET auxiliar pentru Task Tracker, fără să condiționezi salvarea taskului de el.
2. **20 min — Timeout și retry.** Configurează timeout real de conectare/citire în clientul HTTP. Folosește Resilience4j pentru retry limitat și, dacă modelul asincron ales o permite, TimeLimiter. Nu presupune că TimeLimiter întrerupe orice apel blocant. Nu retrimite automat operații ne-idempotente.
3. **20 min — Circuit și bulkhead.** Configurează praguri de laborator explicite, o fereastră mică și concurență limitată. Definește răspunsul către client când dependența nu este disponibilă; nu întoarce date inventate ca și cum apelul ar fi reușit.
4. **20 min — Experimente.** Rulează cazurile normal, lent, erori repetate și cereri concurente. Verifică deschiderea circuitului, răspunsul rapid ulterior și recuperarea half-open → closed.

## Livrabile

- Apel extern protejat și configurație explicată.
- Teste pentru timeout și tranzițiile circuitului.
- Un tabel cu latență, număr de încercări și rezultat observat.

## Criterii de finalizare

- [ ] O dependență lentă nu blochează cererea peste bugetul total de latență definit.
- [ ] Retry are număr maxim și interval; nu amplifică nelimitat încărcarea.
- [ ] Circuitul deschis reduce apelurile către dependență.
- [ ] Bulkhead respinge sau limitează concurența conform configurației.
- [ ] Erorile și eventualele răspunsuri degradate sunt explicite.

## Dovadă și recapitulare

Păstrează măsurătorile și testele. Explică backpressure, idempotența apelurilor și de ce ordinea decoratorilor poate schimba comportamentul retry/circuit breaker.

**Navigare:** [Lecția precedentă](./lectia-09.md) · [Index](../README.md) · [Lecția următoare](./lectia-11.md)
