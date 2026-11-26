# Lecția 05 — SCA, containere și secret scanning

**Săptămâna:** 2 · **Data de reper:** 03.12.2026 · **Durata:** 90 minute

## Obiective

- Adaugi Dependency-Check, Trivy și gitleaks.
- Distingi vulnerabilitățile dependențelor de cele ale imaginii.
- Definești politici de blocare verificabile.

## Intrări necesare

[Lecția 04](./lectia-04.md) finalizată; agent de laborator capabil să construiască imagini, fără a da controllerului Jenkins privilegii Docker inutile.

## Parcurs

1. **15 min — Politici și acces la date.** Stabilește pragurile: de exemplu CVSS ≥ 7 pentru SCA, HIGH/CRITICAL pentru container și orice secret detectat. Definește cum tratezi excepțiile aprobate. Pregătește cache-ul și accesul NVD necesar Dependency-Check; tokenurile rămân credentials.
2. **20 min — Dependency-Check.** Rulează `./gradlew dependencyCheckAnalyze` din `backend/`, salvează raportul și propagă codul de ieșire. Notează diferența dintre o vulnerabilitate și o eroare de actualizare a bazei scannerului.
3. **25 min — Imagine și Trivy.** Creează un Dockerfile minimal pentru JAR, cu imagine de bază fixată, și scanează exact imaginea rezultată. Configurează explicit ieșirea nenulă pentru severitățile alese. Hardeningul multi-stage este rezervat lecției 13.
4. **20 min — gitleaks.** Scanează repository-ul și istoricul relevant în CI, fără a depinde de disponibilitatea secret scanning pe GitHub. Testează cu un fixture sintetic recunoscut de regulă, niciodată cu o cheie reală. Redactează valorile din rapoarte.
5. **10 min — Verificare.** Confirmă că scannerul defect sau o bază inaccesibilă nu sunt confundate cu absența problemelor. Salvează rapoarte anonimizate.

## Livrabile

- Trei scanări în pipeline și o politică scrisă de blocare.
- Imagine de aplicație identificată prin versiune sau digest.
- Rapoarte utile, fără valori de secrete.

## Criterii de finalizare

- [ ] Dependency-Check inspectează dependențele aplicației, nu un director gol.
- [ ] Trivy inspectează imaginea construită din commitul curent.
- [ ] Fixture-ul sintetic gitleaks produce eșec, iar eliminarea lui permite continuarea.
- [ ] Problemele de infrastructură ale scanării sunt raportate explicit.

## Dovadă și recapitulare

Păstrează câte un raport și codurile de ieșire. Dacă apare un secret real, revocă-l și rotește-l; simpla ștergere din ultimul commit nu rezolvă incidentul.

**Navigare:** [Lecția precedentă](./lectia-04.md) · [Index](../README.md) · [Lecția următoare](./lectia-06.md)
