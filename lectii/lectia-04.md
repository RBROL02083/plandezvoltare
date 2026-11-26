# Lecția 04 — SonarQube și quality gate

**Săptămâna:** 2 · **Data de reper:** 01.12.2026 · **Durata:** 90 minute

## Obiective

- Distingi analiza statică de un quality gate.
- Integrezi SonarQube în Jenkins cu așteptarea rezultatului.
- Demonstrezi blocarea livrării atunci când gate-ul eșuează.

## Intrări necesare

[Lecția 03](./lectia-03.md) finalizată; resurse disponibile pentru SonarQube și o ediție potrivită laboratorului.

## Parcurs

1. **30 min — Lectură.** Urmărește într-un exemplu public unde se încheie analiza și unde se decide gate-ul. Recapitulează diferența dintre SAST, SCA și testele aplicației. Nu presupune că ediția aleasă suportă analiza tuturor branchurilor.
2. **20 min — Serviciu și proiect.** Pornește SonarQube cu date persistente și versiune compatibilă cu JDK/scannerul. Creează un proiect și un token limitat, stocat în Jenkins.
3. **20 min — Integrare.** Adaugă pluginul `org.sonarqube` în Gradle și rulează `./gradlew sonar` după compilare și teste. Generează raportul XML cu `jacocoTestReport` dacă regula gate-ului cere acoperire. Configurează webhook-ul intern Jenkins, secretul și un timeout pentru verdict.
4. **20 min — Probă negativă.** Folosește o modificare de laborator care încalcă o condiție concretă a gate-ului. Verifică eșecul, apoi repară modificarea. Un simplu avertisment care nu afectează gate-ul nu este o probă suficientă.

## Livrabile

- Proiect SonarQube și stage-uri Analysis / Quality Gate.
- Condiții și praguri consemnate explicit.
- O execuție blocată și una acceptată.

## Criterii de finalizare

- [ ] Jenkins așteaptă verdictul și tratează gate-ul eșuat ca eșec al livrării.
- [ ] Verdictul aparține analizei curente, nu unui build anterior.
- [ ] Timeoutul sau indisponibilitatea scannerului nu produc succes fals.
- [ ] Tokenul nu este în repository sau în loguri.

## Dovadă și recapitulare

Păstrează condiția, valoarea măsurată și verdictul. Explică de ce „scannerul a terminat cu succes” nu înseamnă „codul a trecut gate-ul”.

**Dacă te blochezi:** verifică accesul SonarQube → Jenkins, webhook-ul și compatibilitatea versiunilor, fără să dezactivezi gate-ul.

**Navigare:** [Lecția precedentă](./lectia-03.md) · [Index](../README.md) · [Lecția următoare](./lectia-05.md)
