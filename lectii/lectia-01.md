# Lecția 01 — Laborator, Jenkins și aplicația de bază

**Săptămâna:** 1 · **Data de reper:** 24.11.2026 · **Durata:** 90 minute

## Obiective

- Înțelegi traseul commit → build → teste → artefact → deploy.
- Pornești Jenkins în Docker și pregătești repository-ul Task Tracker.
- Stabilești contractul API folosit până la finalul planului.

## Intrări necesare

Ubuntu în WSL 2, Docker funcțional din terminalul Ubuntu, Git/GitHub și JDK compatibil cu versiunea Spring Boot aleasă. Vezi [pregătirea laboratorului](../README.md#pregătirea-laboratorului).

### Mediul folosit: Ubuntu în WSL 2

În acest laborator, distribuția Ubuntu din WSL 2 ține locul serverului Ubuntu separat. WSL este mediul în care rulează Ubuntu, nu o distribuție Linux în sine. Pentru lecția 1 lucrezi direct în terminalul Ubuntu din VS Code; nu ai nevoie de conexiune SSH.

- Păstrează proiectul în sistemul de fișiere Linux, de exemplu `~/task-tracker`, nu în `/mnt/c`.
- Docker poate rula prin Docker Desktop cu integrarea WSL activată pentru Ubuntu sau prin Docker Engine instalat în distribuție; folosește o singură variantă. Verifică prin `docker version` că sunt accesibile atât clientul, cât și serverul Docker.
- Jenkins rulează în Docker, nu se instalează ca serviciu Windows. Pentru acces local din browserul Windows, publică interfața pe un port liber legat la loopback, de exemplu `127.0.0.1:8081:8080`, apoi deschide `http://localhost:8081`. Portul 8080 rămâne pentru aplicație. Vezi [folderul de lucru și instalarea](../laboratoare/lectia-01/README.md).
- WSL nu este un server permanent: oprirea distribuției întrerupe procesele care rulează în ea. Pentru lecțiile ulterioare cu Ansible și servicii Linux, verifică separat accesul SSH din executorul de automatizare și suportul `systemd`.

## Parcurs

1. **30 min — [Lectură și orientare](./lectia-01a.md).** Citește un pipeline declarativ, desenează fluxul de livrare al proiectului Task Tracker în WSL și notează ce poți susține deja în CV.
2. **25 min — Jenkins.** Pornește o versiune fixată, cu volum persistent pentru date. Finalizează configurarea inițială și pluginurile Pipeline, Git și JUnit. Limitează accesul la laborator și pregătește un executor de build; nu expune administrarea pe internet.
3. **25 min — Aplicația.** Generează un proiect Spring Boot cu **Gradle - Groovy**, Java 21, Web, validare, persistență și teste. Creează entitatea Task și operațiile GET/POST din contract. Folosește o bază în memorie pentru această etapă.
4. **10 min — Verificare și salvare.** Rulează aplicația, verifică lista, adăugarea și respingerea unui titlu gol. Salvează codul fără fișiere generate, parole sau configurații private.

## Livrabile

- Jenkins accesibil în laborator, cu date persistente.
- Repository cu aplicația și un scurt ghid de pornire.
- Contract API consemnat, inclusiv câmpurile și codurile HTTP.

## Criterii de finalizare

- [ ] Jenkins își păstrează configurarea după recrearea containerului cu același volum.
- [ ] `GET /api/tasks` întoarce o listă, iar `POST /api/tasks` creează un task.
- [ ] Un titlu gol este respins cu `400`.
- [ ] Gradle Wrapper rulează local; repository-ul nu conține secrete.

## Dovadă și recapitulare

Păstrează commitul inițial și rezultate HTTP fără date sensibile. Explică diferența dintre controllerul Jenkins și agentul de build.

**Dacă te blochezi:** verifică porturile, permisiunile volumului și compatibilitatea JDK. Nu rezolva problemele de acces prin dezactivarea autentificării.

**Navigare:** [Index](../README.md) · [Lecția următoare](./lectia-02.md)
