# Lecția 01A — Cum citești un pipeline Jenkins

**Durata:** 30 minute, incluse în [lecția 01](./lectia-01.md). **Ai nevoie de:** un browser și un loc pentru notițe. Nu instala și nu porni Jenkins încă.

**Folder de lucru:** [01A — prerechizite și organizare în WSL](../laboratoare/lectia-01a/README.md).

La final vei putea arăta **cine compilează codul, ce fișier produce buildul și ce oprește livrarea**.

## 1. Citește un Jenkinsfile — 12 minute

Deschide [exemplul declarativ din documentația oficială Jenkins](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#creating-a-jenkinsfile). Uită-te doar la primul exemplu, cu etapele `Build`, `Test` și `Deploy`. Comenzile `echo` afișează mesaje: exemplul arată structura, dar nu compilează, nu testează și nu instalează nimic.

Acum compară-l cu acest exemplu pentru proiectul Task Tracker. Citește-l fără să îl rulezi. Presupune că jobul preia codul din Git, agentul are eticheta `wsl`, Git și Java 21, iar `backend/gradlew` este executabil.

```groovy
pipeline {
    agent { label 'wsl' }

    stages {
        stage('Compilare') {
            steps {
                sh 'cd backend && ./gradlew --no-daemon clean classes'
            }
        }
        stage('Teste') {
            steps {
                sh 'cd backend && ./gradlew --no-daemon test'
            }
        }
        stage('Ambalare și arhivare') {
            steps {
                sh 'cd backend && ./gradlew --no-daemon bootJar'
                archiveArtifacts artifacts: 'backend/build/libs/*.jar', excludes: '**/*-plain.jar'
            }
        }
    }
}
```

| Ce vezi | Ce înseamnă în practică |
|---|---|
| `pipeline { ... }` | Definește fluxul declarativ. De regulă, îl păstrezi în fișierul `Jenkinsfile`, versionat împreună cu aplicația. |
| `agent { label 'wsl' }` | Jenkins alege un executor de pe un nod cu eticheta `wsl`. În exemplul oficial, `agent any` permite oricare nod eligibil, fără să garanteze mediul potrivit. |
| `stages` și `stage` | `stages` grupează etapele; fiecare `stage` marchează o etapă vizibilă în Jenkins. |
| `steps` | Comenzile și acțiunile dintr-o etapă. Aici `sh` execută o comandă pe agent, iar `archiveArtifacts` păstrează JAR-ul ca fișier descărcabil din execuția Jenkins. |

**Controllerul** coordonează jobul și alocă un executor. **Agentul** oferă mediul în care rulează comenzile. **Workspace-ul** este directorul de lucru al jobului pe acel mediu; nu este automat directorul tău deschis în VS Code.

**Exercițiu:** notează unde este ales agentul, ce etapă produce JAR-ul și ce etapă nu mai rulează dacă `./gradlew test` întoarce o eroare. `classes` compilează codul principal, iar `bootJar` produce JAR-ul executabil fără să ruleze din nou testele în configurația standard Spring Boot. Exemplul arhivează JAR-ul în Jenkins, dar nu îl publică în Artifactory și nu face deploy.

## 2. Desenează laboratorul tău WSL — 10 minute

Pornește de la schema de mai jos. Redeneaz-o din memorie și scrie pe fiecare săgeată ce se transferă: cod, comenzi sau un JAR. Marchează cu „mai târziu” Artifactory și deploy-ul.

```text
VS Code / terminal Ubuntu în WSL 2
    │ commit local, apoi git push
    ▼
GitHub (codul Task Tracker și Jenkinsfile)
    │ Jenkins preia codul la pornirea jobului
    ▼
Controller Jenkins (container Docker accesibil din laborator)
    │ alocă execuția
    ▼
Agent Jenkins: workspace → compilare → teste → JAR
    │ dacă testele trec: arhivare în Jenkins
    ▼
Mai târziu: publicare în Artifactory → deploy al aceluiași JAR în Ubuntu/WSL 2
```

- **Commitul** salvează local o versiune; `git push` o trimite în GitHub. Un push nu pornește singur Jenkins dacă nu ai configurat o declanșare.
- **Artefactul** este fișierul produs (aici JAR-ul). **Deploy-ul** instalează și pornește aplicația din acel artefact în mediul țintă, fără să o recompileze.
- **WSL 2** găzduiește mediul Ubuntu de laborator. În prima lecție lucrezi direct în terminal; accesul mai târziu dintr-un agent Jenkins la ținta de deploy este o configurare separată.

## 3. Verifică ce ai înțeles și notează experiența — 8 minute

Răspunde fără să te uiți înapoi:

1. Cine coordonează jobul și unde rulează Gradle?
2. Dacă testele eșuează, ce se întâmplă cu etapa de ambalare și arhivare?
3. Dacă poți descărca JAR-ul din Jenkins, înseamnă că aplicația rulează deja în WSL?

<details>
<summary>Răspunsuri de verificare</summary>

1. Controllerul coordonează jobul; Gradle rulează în workspace-ul agentului alocat.
2. Comanda de test întoarce un cod nenul, pipeline-ul eșuează și etapa ulterioară este sărită. Execuția respectivă nu arhivează un JAR.
3. Nu. Arhivarea păstrează fișierul; deploy-ul trebuie executat separat.

</details>

Pentru CV, scrie o propoziție despre **ce ai făcut deja**, nu despre ce urmează să faci. Dacă reflectă experiența ta: „Am urmărit execuțiile Jenkins și am verificat rezultatele în SonarQube și artefactele în Artifactory.” Formularea „Am proiectat și implementat un pipeline CI/CD” o folosești când poți arăta ce ai configurat și cum ai verificat rezultatul. Dacă ai autorizare la serviciu, poți solicita un task mic de Jenkins sau Ansible; nu este o condiție a lecției.

**Gata când:** ai o schiță cu rolurile și traseul JAR-ului, poți explica unde se oprește fluxul la un test eșuat și ai o notă de CV pe care o poți susține.

**Continuă:** revino la [lecția 01, punctul 2 — Jenkins](./lectia-01.md#parcurs).
