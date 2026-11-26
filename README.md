# Plan practic DevOps — 24 de lecții în 8 săptămâni

## Analiza documentului

Sursa consultată este documentul **Plan fast learning DevOps: 8 săptămâni**, de 5 pagini. PDF-ul de referință rămâne local și nu este redistribuit în acest repository.

Planul urmărește construirea unui singur proiect: un API Spring Boot, un UI React și un flux de livrare Jenkins către un mediu Ubuntu de laborator și AWS. În acest laborator, Ubuntu în WSL 2 înlocuiește serverul Ubuntu separat din sursă. Primele 4 săptămâni prioritizează Jenkins, scanning/Artifactory și Ansible, apoi reziliența și securitatea. Ultimele 4 introduc Docker/Kubernetes, Terraform/AWS și frontend/testare e2e.

Am transformat fiecare săptămână în **3 lecții: 90 + 90 + 180 de minute**. Rezultatul este **24 de lecții, 48 de ore**, fără ore suplimentare ascunse în teme obligatorii. Datele păstrează calendarul sursei; pot fi decalate integral, păstrând ordinea și duratele.

### Ce am clarificat față de sursă

- Fiecare lecție are intrări necesare, pași temporizați, un livrabil și verificări observabile.
- Cele 30 de minute de lectură recomandate înaintea săptămânilor 1–4 sunt incluse în prima lecție a săptămânii. Se citesc configurații publice sau de laborator. Materialele de serviciu se consultă numai cu autorizare, fără copiere în proiect sau în instrumente AI.
- Publicarea artefactelor precedă deploy-ul; deploy-ul reutilizează aceeași versiune, fără recompilare.
- Backendul are de la început operațiile necesare UI-ului, pentru a evita schimbarea contractului în ultima săptămână.
- AWS include și deploy-ul efectiv al aplicației, nu doar crearea unor resurse.
- Bugetele AWS nu sunt plafoane de cheltuieli. Resursele temporare se distrug la fiecare sesiune; state-ul remote are un ciclu de viață separat.
- Existența unui manifest, a unui scanner sau a unui fallback nu este considerată dovadă de funcționare: se verifică efectiv comportamentul.

### Limite realiste

Cele 48 de ore sunt un parcurs intensiv pentru cineva care cunoaște deja Java/Spring, Git și Linux de bază. Nu reprezintă pregătire completă pentru producție și nici nivel intermediar React. Documentul estimează încă 20–30 de ore pentru aprofundarea React; acestea **nu sunt incluse** aici.

Pornirea serviciilor și exercițiile sunt incluse în lecții. Instalarea sistemului de operare, recuperarea conturilor, depanarea hardware și descărcările foarte lente pot necesita timp separat. Dacă un criteriu de ieșire nu este îndeplinit, lecția se reia înaintea celei dependente; nu se declară artificial respectat termenul de 48 de ore.

## Pregătirea laboratorului

- Ubuntu în WSL 2, cu spațiu liber și resurse suficiente pentru serviciile active; Git, Docker/Compose, JDK și `curl` disponibile din terminalul Ubuntu. Nu este necesar un server Ubuntu separat.
- Pentru lecția 1 lucrezi direct în WSL, fără SSH. Docker poate fi furnizat de Docker Desktop cu integrare WSL sau de Docker Engine în Ubuntu. Pentru lecțiile care folosesc SSH, `systemd` sau k3s, verifică cerințele în WSL înainte de execuție; accesul local din terminal nu garantează accesul dintr-un container Jenkins.
- Cont și repository GitHub pentru cod propriu. Jenkins, SonarQube, Artifactory și Keycloak rămân accesibile doar în rețeaua de laborator sau prin acces securizat.
- Alege și fixează versiuni compatibile înaintea folosirii lor; nu utiliza tagul `latest`. Verifică cerințele și licențierea edițiilor disponibile, în special SonarQube și Artifactory.
- Folosește Gradle Wrapper (`./gradlew`) și Gradle Groovy DSL (`build.gradle`) pentru backend, cu Java 21 atât local, cât și pe agentul Jenkins. Păstrează versiunea Gradle fixată de wrapper și compatibilă cu versiunea Spring Boot aleasă.
- Rulează numai serviciile necesare lecției. Nu este necesar ca toate componentele laboratorului să fie pornite simultan.
- Înainte de săptămâna 6: cont AWS accesibil, MFA și metodă de plată înțeleasă. Nu presupune eligibilitatea pentru free tier.
- Nu este necesar un domeniu cumpărat: lecția Route 53 oferă și o variantă de laborator fără domeniu public.

### Foldere de lucru pentru WSL

Fiecare lecție are un folder în [laboratoare](./laboratoare/README.md), inclusiv [sublecția 01A](./laboratoare/lectia-01a/README.md). Ghidurile conțin prerechizite, instrucțiuni de instalare/configurare și verificări adaptate mediului existent: Ubuntu 22.04 în WSL 2, systemd, Java 21, Docker/Compose și Node.js deja disponibile. **Nu au fost instalate automat servicii sau pachete.**

Începe cu [ghidul comun WSL și indexul folderelor](./laboratoare/README.md), apoi cu [folderul lecției 01](./laboratoare/lectia-01/README.md). Folderele sunt pentru pregătire și lucru local; codul Task Tracker rămâne într-un singur repository, recomandat `~/task-tracker`, fără copii separate pentru fiecare lecție.

### Versionarea lecțiilor și laboratoarelor

Acest folder are un repository separat: [RBROL02083/plandezvoltare](https://github.com/RBROL02083/plandezvoltare). Se versionează acest README, [lecțiile](./lectii/), [laboratoarele](./laboratoare/) și fișierele `.gitignore`. PDF-ul, secretele, cache-urile și rezultatele generate sunt excluse prin [.gitignore](./.gitignore).

Aplicația din `~/task-tracker` rămâne într-un repository propriu. Jobul Jenkins al aplicației trebuie să citească repository-ul **Task Tracker**, nu `plandezvoltare`, care conține ghidurile.

Pentru modificările viitoare, din terminalul WSL:

```bash
cd "$HOME/PlanDezvoltareDevOps"
git status --short
git add -- README.md .gitignore lectii/ laboratoare/
git diff --cached --stat
git diff --cached
git commit -m "Actualizeaza lectiile si laboratoarele"
git push
```

Revizuiește fișierele selectate înainte de commit. `.gitignore` nu detectează parole copiate în documentație și nu elimină un secret deja comis.

## Proiectul comun: Task Tracker

Păstrează același repository de-a lungul celor 24 de lecții:

```text
backend/                 Spring Boot, Gradle Wrapper, teste
frontend/                React + TypeScript + Cypress, din săptămâna 8
ansible/                 inventory, playbook și rol de aplicație
k8s/                     manifestele de învățare
helm/task-tracker/       chartul folosit ulterior la deploy
terraform/bootstrap/     backendul de state, ciclu de viață separat
terraform/environments/lab/  infrastructura temporară AWS
Jenkinsfile              pipeline incremental
README.md                pornire, arhitectură și dovezi
```

Aceasta este structura recomandată a **viitorului repository de aplicație**, nu o listă de fișiere implementate în acest folder.

Contract minim: `GET /api/tasks`, `POST /api/tasks`, câmpuri `id`, `title`, `completed`; titlul gol primește `400`. După activarea autentificării, accesul fără token primește `401`, iar drepturile insuficiente `403`. Health probes folosesc endpointurile Actuator, nu API-ul funcțional.

Se poate folosi o bază în memorie la început. În săptămâna 3 se conectează PostgreSQL de laborator, iar în AWS se reutilizează contractul prin RDS. Testele e2e folosesc date izolate, nu baza personală.

## Calendar și lecții

| Săpt. | Data de reper | Lecție | Durată |
|---|---|---|---|
| 1 | 24.11.2026 | [01 — Laborator, Jenkins și aplicația de bază](./lectii/lectia-01.md) | 90 min |
| 1 | 26.11.2026 | [02 — Pipeline declarativ: build și teste](./lectii/lectia-02.md) | 90 min |
| 1 | 29.11.2026 | [03 — Credentials, rapoarte și declanșare automată](./lectii/lectia-03.md) | 180 min |
| 2 | 01.12.2026 | [04 — SonarQube și quality gate](./lectii/lectia-04.md) | 90 min |
| 2 | 03.12.2026 | [05 — SCA, containere și secret scanning](./lectii/lectia-05.md) | 90 min |
| 2 | 06.12.2026 | [06 — Artifactory și lanțul de livrare verificat](./lectii/lectia-06.md) | 180 min |
| 3 | 08.12.2026 | [07 — Ansible: inventory și acces](./lectii/lectia-07.md) | 90 min |
| 3 | 10.12.2026 | [08 — Instalare și serviciu systemd](./lectii/lectia-08.md) | 90 min |
| 3 | 13.12.2026 | [09 — Rol, Vault și deploy din Jenkins](./lectii/lectia-09.md) | 180 min |
| 4 | 15.12.2026 | [10 — Timeout, retry și circuit breaker](./lectii/lectia-10.md) | 90 min |
| 4 | 17.12.2026 | [11 — Health, shutdown și observabilitate](./lectii/lectia-11.md) | 90 min |
| 4 | 20.12.2026 | [12 — OAuth2, TLS și evaluarea securității](./lectii/lectia-12.md) | 180 min |
| 5 | 22.12.2026 | [13 — Docker multi-stage și k3s](./lectii/lectia-13.md) | 90 min |
| 5 | 24.12.2026 | [14 — Manifestele și rularea în Kubernetes](./lectii/lectia-14.md) | 90 min |
| 5 | 27.12.2026 | [15 — HPA, Helm și livrare din Jenkins](./lectii/lectia-15.md) | 180 min |
| 6 | 29.12.2026 | [16 — AWS, costuri și state remote](./lectii/lectia-16.md) | 90 min |
| 6 | 31.12.2026 | [17 — VPC și instanța EC2](./lectii/lectia-17.md) | 90 min |
| 6 | 03.01.2027 | [18 — RDS, S3 și ciclul infrastructurii](./lectii/lectia-18.md) | 180 min |
| 7 | 05.01.2027 | [19 — Terraform în Jenkins, plan și aprobare](./lectii/lectia-19.md) | 90 min |
| 7 | 07.01.2027 | [20 — ALB, Route 53 și deploy în AWS](./lectii/lectia-20.md) | 90 min |
| 7 | 10.01.2027 | [21 — Lambda și sinteza serviciilor AWS](./lectii/lectia-21.md) | 180 min |
| 8 | 12.01.2027 | [22 — React, TypeScript și lista de taskuri](./lectii/lectia-22.md) | 90 min |
| 8 | 14.01.2027 | [23 — Formular, rutare și tratarea erorilor](./lectii/lectia-23.md) | 90 min |
| 8 | 17.01.2027 | [24 — Cypress, pipeline complet și portofoliu](./lectii/lectia-24.md) | 180 min |

Ultima săptămână se încheie pe 18.01.2027, conform sursei; ultima sesiune practică este duminică, 17.01.

## Mod de lucru și evaluare

1. Citește intrările necesare și pornește numai serviciile folosite.
2. Execută pașii în ordinea propusă, folosind numai cod și date de laborator.
3. Păstrează dovada cerută: log anonimizat, raport de test, versiune de artefact sau captură fără secrete.
4. Marchează lecția terminată numai după toate verificările. Dovezile se păstrează în repository-ul aplicației sau în rapoartele CI.
5. Folosește un instrument AI pentru explicații, generare asistată și diagnostic, dar verifică rezultatele prin teste. Sursa menționează Claude Code; obiectivul este utilizarea verificabilă, nu dependența de un furnizor. Nu încărca materiale confidențiale.

Nu există teme suplimentare obligatorii. Recuperarea unei lecții nefinalizate și aprofundările opționale se planifică separat.

## Checkpointuri și trasabilitate

| Cerință din sursă | Lecții | Dovada de final |
|---|---|---|
| Jenkins, DSL declarativ, triggers, credentials, JUnit | 01–03 | Un push pornește build/test și o eroare de test oprește livrarea |
| Sonar, Dependency-Check, Trivy, gitleaks | 04–06 | Scanările rulează; o încălcare controlată blochează publish |
| Artifactory, snapshot/release, promovare | 06 | Versiune publicată, descărcată și identificată prin checksum |
| Ansible, roluri, Jinja2, Vault, idempotență | 07–09 | Deploy Jenkins și a doua execuție fără schimbări |
| Resilience4j, probes, metrici, loguri | 10–11 | Dependență lentă izolată și sănătate observabilă |
| OAuth2/Keycloak, TLS, secrete, OWASP, RTO/RPO | 12 | Teste 401/403/200, HTTPS și evaluare documentată |
| Docker, k3s, resurse, HPA, PDB, Helm | 13–15 | Scalare observată, upgrade și rollback verificate |
| Terraform, VPC, EC2, RDS, S3, state/locking | 16–18 | Apply/destroy și acces privat la baza de date |
| Terraform CI, IaC scanning, ALB, Route 53 | 19–20 | Aprobare înainte de apply și API accesibil prin ALB |
| Lambda/S3, EKS, Managed Kafka, EFS | 21 | Eveniment procesat; comparații arhitecturale, fără clustere costisitoare |
| React, TypeScript, Router, Cypress | 22–24 | Două ecrane și exact trei scenarii e2e în CI |
| CV, povești de interviu, AI tools | 01, 12, 21, 24 | Profil onest, exemple demonstrate și patru povești problemă–decizie–rezultat |

La sfârșitul săptămânii 4 există un proiect CI/CD demonstrabil. La sfârșitul săptămânii 8 există o parcurgere cap-coadă a temelor adiționale, nu o afirmație de expertiză în toate tehnologiile.
