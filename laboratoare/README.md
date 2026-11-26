# Foldere de lucru — Ubuntu în WSL 2

Fiecare folder conține prerechizitele, instalarea și verificările pentru lecția respectivă. Acestea sunt **instrucțiuni**, nu instalări executate automat. Citește mai întâi lecția, apoi instalează numai ce lipsește. Comenzile sunt pentru Bash în Ubuntu, dacă nu scrie explicit PowerShell.

## Mediul existent

Constatat la 26.11.2026 prin verificări fără modificări:

| Componentă | Stare |
|---|---|
| Sistem | Ubuntu 22.04.5 LTS, x86_64, WSL 2 |
| Init | `systemd` activ ca PID 1; nu trebuie activat din nou |
| Java | OpenJDK/JDK 21.0.13.1 |
| Docker | Engine 29.7.2 în Ubuntu, daemon accesibil utilizatorului |
| Compose | `docker compose`, versiunea 5.5.0; nu comanda veche `docker-compose` |
| Git / curl | Instalate |
| Node.js / npm | 26.13.0 / 11.20.0, instalate în contul utilizatorului |
| Python de sistem | 3.10.12; îl păstrăm nemodificat |
| Lipsesc din PATH | Ansible, Terraform, AWS CLI, kubectl/k3s, Helm, Trivy, gitleaks, `psql`, `jq` |
| SSH server | Neinstalat; clientul SSH există |
| Resurse vizibile | Aproximativ 15 GiB RAM și 858 GiB liberi; acestea nu sunt rezervate laboratorului |
| Porturi deja ocupate | 8082 și 5433; nu opri serviciile care le folosesc |
| Limită pentru SonarQube | `vm.max_map_count=65530`, sub cerința documentată curentă; vezi lecția 04 |

Starea se poate schimba. `command -v` verifică un executabil, nu și configurarea serviciului sau accesul la cont.

## Organizarea lucrului

- **Aici**, în folderul lecției, poți crea schițe, configurații de laborator și rezultate nesensibile.
- **Codul aplicației rămâne într-un singur repository**, recomandat `~/task-tracker`: backend, frontend, Jenkinsfile, Ansible, Kubernetes, Helm și Terraform. Nu crea 24 de copii ale aplicației.
- Lecția 01A este sublecția de 30 de minute din lecția 01, nu o sesiune suplimentară.
- Descărcările pot sta în `~/.cache/devops-downloads`, iar secretele în `~/.config/devops-lab`, în afara ambelor repository-uri.
- Comenzile cu `sudo -A`, modificările de rețea, serviciile și clusterul afectează distribuția WSL. Pe un sistem partajat obține acordul administratorului; nu opri sau modifica resursele altora.
- Nu instala toate serviciile simultan. Închide numai containerele și procesele tale, pe nume/PID explicit. Nu folosi comenzi globale de prune și nu șterge volumele pentru o simplă oprire.

Pregătire a directoarelor personale, numai când începi practica:

```bash
mkdir -p "$HOME/.local/bin" "$HOME/.cache/devops-downloads"
mkdir -p "$HOME/.config/devops-lab"
chmod 700 "$HOME/.config/devops-lab"
export PATH="$HOME/.local/bin:$HOME/.local/share/devops-lab/uv/bin:$PATH"
```

Variabilele exportate și `cd` sunt valabile în terminalul curent. Repetă-le în terminale noi sau adaugă doar configurarea nesensibilă necesară în profilul Bash. Nu păstra parole în profil. Agentul Jenkins are propriul PATH: verifică-l separat.

## Baza WSL

Verifică înainte de orice instalare:

```bash
cat /etc/os-release
uname -r
ps -p 1 -o comm=
git --version
curl --version
java -version
javac -version
docker version
docker compose version
ss -ltn
```

Nu reinstala WSL, Docker sau Java dacă verificările sunt bune. Nu instala Docker Desktop peste Engine-ul existent doar pentru lecții. Dacă lipsesc utilitare de bază, instalează numai pachetele necesare:

```bash
sudo -A apt-get update
sudo -A apt-get install ca-certificates curl git unzip
```

Numai dacă JDK-ul 21 lipsește:

```bash
sudo -A apt-get install openjdk-21-jdk
java -version
javac -version
```

Nu instala Gradle global: aplicația folosește `./gradlew`, care descarcă distribuția fixată în `gradle/wrapper/gradle-wrapper.properties`. Java 21 al proiectului și Java necesar controllerului Jenkins sunt cerințe distincte; imaginea controllerului își aduce propriul runtime.

Pentru o distribuție nouă fără Docker funcțional, urmează [instalarea oficială Docker Engine pentru Ubuntu](https://docs.docker.com/engine/install/ubuntu/), inclusiv pluginul Compose. Accesul la socketul Docker echivalează cu privilegii administrative asupra gazdei: nu schimba permisiunile în `666` și nu monta socketul în controllerul Jenkins.

`systemd` este deja activ. Numai pentru altă distribuție consultă [instrucțiunile Microsoft](https://learn.microsoft.com/en-us/windows/wsl/systemd); nu suprascrie configurația existentă. `wsl --shutdown`, executat în PowerShell, oprește toate distribuțiile utilizatorului și poate întrerupe alte lucrări.

## Versiuni și secrete

- Când un ghid cere o imagine, introdu o referință completă cu **tag exact** din sursa indicată, nu `latest` sau un alias mobil. După `docker pull`, notează digestul și folosește-l la recreare.
- Versiunile concrete de CLI din ghiduri sunt repere verificate în sursele publice la redactare, nu promisiuni că rămân cele mai noi. Verifică suportul și notificările de securitate înainte de instalare și fixează orice versiune înlocuitoare.
- Notează versiunile alese în ghidul aplicației, fără secrete. Nu suprascrie o instalare existentă ca să obții versiunea din exemplu.
- Fișierele `.env` se completează într-un editor local, se protejează cu `chmod 600` și nu se comit. Creează-le doar dacă nu există deja. Nu folosi `docker compose config` fără `--quiet` pe configurații cu parole.
- Administratorii Docker pot vedea configurația containerelor, inclusiv variabilele de mediu. Aceste exemple sunt de laborator; nu sunt un secret manager.
- La o eroare oprește-te și verifică logurile. Nu adăuga `|| true`, nu dezactiva TLS și nu declara instalarea reușită doar pentru că un container apare în listă.

## Rețea și porturi

Porturi propuse, de reverificat prin `ss -ltn` înainte de pornire:

| Serviciu | Acces de pe WSL / Windows |
|---|---|
| API Task Tracker | `http://localhost:8080`, apoi HTTPS conform lecției 12 |
| Jenkins | `http://localhost:8081` |
| SonarQube | `http://localhost:9000` |
| Artifactory | `http://localhost:8085` (router); 8086 numai dacă este necesar accesul direct |
| PostgreSQL al aplicației | `127.0.0.1:15432`, fără a ocupa 5433 |
| Keycloak inițial | `http://localhost:8180`, înainte de configurarea HTTPS |
| Frontend Vite | `http://localhost:5173` |
| SSH de laborator | `127.0.0.1:2222` |
| Kubernetes API | 6443, restricționat la laborator |
| Ingress prin port-forward | `127.0.0.1:8880` / `127.0.0.1:8444` |

`localhost` este **mediul procesului care face cererea**:

- Browser Windows → port publicat pe loopback în WSL: de regulă funcționează prin localhost forwarding. Dacă nu, verifică [rețeaua WSL](https://learn.microsoft.com/en-us/windows/wsl/networking), nu publica automat pe toate interfețele.
- Agent Jenkins lansat ca proces în WSL → `localhost:8081` ajunge la controllerul publicat.
- Containere din aceeași rețea Docker → numele containerului și **portul intern**, de exemplu `http://devops-jenkins:8080`.
- Pod Kubernetes → `localhost` este podul, nu WSL și nu un container Docker separat. Lecția 14 cere adrese rutabile și DNS explicit.

Pentru containerele laboratorului folosim rețeaua `devops-lab`. Verifică `docker network inspect devops-lab`; numai dacă nu există, creeaz-o prin `docker network create devops-lab`. Nu reutiliza o rețea cu același nume dacă aparține altui proiect.

## Node.js existent

Nu instala Node prin `apt`: ai deja o versiune disponibilă în cont.

```bash
node --version
npm --version
command -v node
command -v npm
```

În lecția 22 se verifică compatibilitatea Vite și în lecția 24 cea a Cypress. Fixează aceeași versiune în dezvoltare și CI. Pentru alt cont fără Node, descarcă binarul Linux x64 și verifică SHA-256 conform [distribuțiilor oficiale Node.js](https://nodejs.org/en/download); nu folosi executabilul Windows din WSL.

## Indexul folderelor

| Lecție | Folder de lucru | Instalări / pregătiri noi |
|---|---|---|
| 01 | [Laborator și Jenkins](./lectia-01/README.md) | Controller Docker, agent WSL, proiect cu Gradle Wrapper |
| 01A | [Citirea unui pipeline](./lectia-01a/README.md) | Nicio instalare |
| 02 | [Build și teste](./lectia-02/README.md) | Nicio unealtă nouă |
| 03 | [Credentials și triggers](./lectia-03/README.md) | Pluginuri Jenkins și polling SCM |
| 04 | [SonarQube](./lectia-04/README.md) | Limite Linux, container, integrare Jenkins |
| 05 | [Scanări](./lectia-05/README.md) | Dependency-Check, Trivy și gitleaks |
| 06 | [Artifactory](./lectia-06/README.md) | Artifactory cu baza proprie |
| 07 | [Ansible și SSH](./lectia-07/README.md) | Python izolat, Ansible și SSH local |
| 08 | [Serviciu systemd](./lectia-08/README.md) | Instalarea aplicației prin Ansible |
| 09 | [Rol, Vault și PostgreSQL](./lectia-09/README.md) | PostgreSQL, driver JDBC, credentials CI |
| 10 | [Reziliență](./lectia-10/README.md) | Dependențe Gradle și serviciu HTTP de test |
| 11 | [Observabilitate](./lectia-11/README.md) | Actuator și utilitare de diagnostic |
| 12 | [OAuth2 și TLS](./lectia-12/README.md) | Keycloak și certificate de laborator |
| 13 | [k3s](./lectia-13/README.md) | Cluster local și acces registry |
| 14 | [Manifestele Kubernetes](./lectia-14/README.md) | DNS, acces DB/Keycloak și Ingress |
| 15 | [Helm și HPA](./lectia-15/README.md) | Helm, metrici și identitate CI |
| 16 | [AWS și Terraform](./lectia-16/README.md) | AWS CLI v2, Terraform, cont și backend |
| 17 | [VPC și EC2](./lectia-17/README.md) | Cheie SSH separată și provider AWS |
| 18 | [RDS și S3](./lectia-18/README.md) | Client PostgreSQL pe EC2 și TLS |
| 19 | [Terraform în Jenkins](./lectia-19/README.md) | Checkov și identitate AWS în CI |
| 20 | [Deploy AWS](./lectia-20/README.md) | Ansible/AWS, DNS și issuer accesibil |
| 21 | [Lambda și S3](./lectia-21/README.md) | `zip` și runtime Lambda suportat |
| 22 | [React și TypeScript](./lectia-22/README.md) | Vite, React, TypeScript, OIDC client |
| 23 | [Formular și Router](./lectia-23/README.md) | React Router și date de test |
| 24 | [Cypress](./lectia-24/README.md) | Biblioteci Linux și browser headless |

**Navigare:** [Planul principal](../README.md) · [Lecțiile](../lectii/)
