# Lucru 02 — Pipeline declarativ: build și teste

[Lecția](../../lectii/lectia-02.md) · [Mediul WSL](../README.md)

## Prerechizite

- [01 finalizată](../lectia-01/README.md): Jenkins pornit, agentul `wsl` online, backend cu Gradle Wrapper și repository GitHub accesibil.
- Agentul trebuie să aibă JDK-ul proiectului și Git, nu doar runtime-ul remotingului.

## Instalare / configurare

Nu sunt necesare alte pachete Ubuntu sau un plugin Jenkins pentru Gradle. Pluginurile Pipeline, Git și JUnit provin din lecția 01; jobul execută direct `./gradlew`.

1. Pornește containerul și agentul prin pașii din 01.
2. În Jenkins creează **Pipeline from SCM** cu Git, URL-ul repository-ului propriu, branchul și calea `Jenkinsfile`.
3. Folosește `agent { label 'wsl' }`. Configurează credentials numai dacă repository-ul le cere; accesul local din VS Code nu este transferat automat în Jenkins.
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
