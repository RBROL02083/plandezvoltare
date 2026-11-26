# Lucru 02 — Pipeline declarativ: build și teste

[Lecția](../../lectii/lectia-02.md) · [Mediul WSL](../README.md)

## Prerechizite

- [01 finalizată](../lectia-01/README.md): Jenkins pornit, agentul `wsl` online, backend cu Gradle Wrapper și repository GitHub accesibil.
- Agentul trebuie să aibă JDK-ul proiectului și Git, nu doar runtime-ul remotingului.

## Instalare / configurare

Nu sunt necesare alte pachete Ubuntu sau un plugin Jenkins pentru Gradle. Pluginurile Pipeline, Git și JUnit provin din lecția 01; jobul execută direct `./gradlew`.

1. Pornește containerul și agentul prin pașii din 01.
2. Creează jobul în Jenkins. „Pipeline from SCM” **nu este un tip de job**; este o opțiune din configurarea unui job de tip *Pipeline*:
   1. Pagina principală → **New Item** (sau **+ New Item**).
   2. **Enter an item name:** `task-tracker`. Alege tipul **Pipeline** → **OK**. Se deschide pagina **Configure**.
   3. Lasă nebifat tot din **General** și **Triggers**. Derulează **până jos**, la secțiunea **Pipeline**.
   4. **Definition** arată implicit *Pipeline script* și o căsuță de cod. Căsuța de cod este pentru scripturi scrise direct în Jenkins; noi nu o folosim. Apasă pe *Pipeline script* și alege din listă **Pipeline script from SCM**.
   5. **SCM:** schimbă *None* în **Git**. Abia acum apar câmpurile pentru repository.
   6. **Repository URL:** `https://github.com/<utilizator>/task-tracker.git`. Dacă sub câmp apare o eroare roșie, repository-ul este privat: adaugă la **Credentials** un credential *Username with password* (username GitHub + token cu **Contents: Read-only**).
   7. **Branches to build → Branch Specifier:** `*/` urmat de branchul tău, de exemplu `*/main` sau `*/master`.
   8. **Script Path:** `Jenkinsfile` (calea relativă la rădăcina repository-ului). → **Save**.

   Legătura dintre câmpuri și fișierele tale: Jenkins clonează *Repository URL* pe branchul indicat, apoi citește fișierul de la *Script Path*. De aceea `Jenkinsfile` trebuie să fie comis și împins pe GitHub, în rădăcină, lângă `backend/`. Fișierul local nepublicat nu este văzut de Jenkins.
3. Folosește `agent { label 'wsl' }`. Configurează credentials numai dacă repository-ul le cere; accesul local din VS Code nu este transferat automat în Jenkins. Dacă buildul rămâne în coadă cu *Waiting for next available executor* sau *There are no nodes with the label 'wsl'*, agentul din 01 nu este creat sau pornit.
4. În checkoutul agentului verifică:

```bash
git --version
java -version
cd backend
./gradlew --version
```

Dacă wrapperul nu este executabil, corectează permisiunea în repository-ul aplicației și comite acea schimbare, nu folosi `sudo` pentru build.

## Lucru și verificare

Editează `~/task-tracker/Jenkinsfile` și testele backendului, nu o copie nouă aici. Folosește `dir('backend')` pentru comenzile `./gradlew --no-daemon classes` și `./gradlew --no-daemon test` în două stage-uri, în aceeași execuție. Dacă adaugi `clean`, rulează-l înaintea compilării, nu între compilare și teste. Păstrează aici doar legături nesensibile către buildul roșu și cel verde. Verifică faptul că un test eșuat oprește etapele ulterioare.

Structura minimă de la care pornești (completezi singur stage-urile):

```groovy
pipeline {
    agent { label 'wsl' }
    stages {
        stage('Compile') {
            steps {
                dir('backend') {
                    // comanda de compilare
                }
            }
        }
        // stage-ul de test
    }
}
```

Ciclul de lucru: editezi Jenkinsfile → `git commit` → `git push` → în job **Build Now** → deschizi buildul → **Console Output**. Jenkins rulează mereu versiunea de pe GitHub, nu pe cea din editor.
