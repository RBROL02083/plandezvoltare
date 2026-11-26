# Lucru 10 — Dependență HTTP controlată și Resilience4j

[Lecția](../../lectii/lectia-10.md) · [Mediul WSL](../README.md)

## Prerechizite

- [09 finalizată](../lectia-09/README.md), backend testabil prin Gradle Wrapper.
- Un port de test liber, de exemplu 18090, dacă stubul HTTP nu folosește port aleator.
- Un stub local care poate întoarce răspuns normal, întârziat și eroare; nu testa pe servicii publice.

## Instalare / configurare

Nu instala servicii noi în Ubuntu. Dependențele se adaugă în blocul `dependencies` din `backend/build.gradle`:

1. Adaugă modulul [Resilience4j](https://resilience4j.readme.io/docs/getting-started) prin `implementation`, cu o versiune fixată, compatibilă cu Spring Boot. Nu presupune că un starter pentru Boot 3 este compatibil cu orice versiune Boot ulterioară.
2. Adaugă un server HTTP de test, de exemplu [WireMock](https://wiremock.org/docs/download-and-installation/), prin `testImplementation`, cu versiune compatibilă, dacă infrastructura existentă nu acoperă deja stubul.
3. Configurează clientul HTTP și scenariile în teste. Folosește port aleator pentru a evita conflictele între rulările CI.

După schimbarea `build.gradle`:

```bash
cd "$HOME/task-tracker/backend"
./gradlew test
```

Gradle rezolvă bibliotecile prin wrapper; nu este necesar un executabil global Resilience4j sau WireMock. Un stub în procesul de test nu trebuie lăsat pornit separat.

## Verificare și lucru

Păstrează în acest folder numai tabelul de măsurători nesensibile: buget de latență, încercări și tranziții ale circuitului. Codul și testele rămân în backend. Nu considera simpla rezolvare a dependențelor dovadă că timeoutul funcționează.
