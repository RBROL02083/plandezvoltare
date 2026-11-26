# Lucru 01 — Laborator, Jenkins și aplicația

[Lecția](../../lectii/lectia-01.md) · [Mediul WSL și regulile comune](../README.md)

## Prerechizite

- Citește [01A](../lectia-01a/README.md) înainte de partea practică.
- Java 21, Git, curl și Docker funcțional: există deja; aplică doar verificările din [baza WSL](../README.md#baza-wsl).
- Imaginea `jenkins/jenkins:2.580.3-lts` este deja descărcată și containerul `devops-jenkins` este creat, dar încă nu a fost pornit.
- Repository GitHub propriu și acces la internet pentru configurarea inițială și pluginurile.
- Alege o versiune Spring Boot stabilă compatibilă cu Java 21 și cu bibliotecile necesare planului. Nu selecta automat un milestone sau snapshot.

## Instalare și pornire

### Controllerul Jenkins

Imaginea și containerul au fost verificate în Docker:

```bash
docker image inspect jenkins/jenkins:2.580.3-lts --format '{{json .RepoDigests}}'
docker inspect devops-jenkins --format 'Stare={{.State.Status}} Porturi={{json .HostConfig.PortBindings}} Volume={{range .Mounts}}{{.Name}} -> {{.Destination}}{{end}}'
```

Containerul existent folosește imaginea descărcată, volumul `devops-jenkins-home` montat în `/var/jenkins_home` și portul loopback `127.0.0.1:8081` mapat la portul Jenkins `8080`. Nu mai rula `docker pull`, `docker volume create` sau `docker run`: ar fi inutil și `docker run` ar eșua deoarece numele containerului există deja.

Rețeaua `devops-lab`, necesară comunicării ulterioare dintre Jenkins și serviciile din Docker, nu este încă creată. Creeaz-o doar dacă lipsește, apoi pornește containerul existent:

```bash
if ! docker network inspect devops-lab >/dev/null 2>&1; then
  docker network create devops-lab
fi
docker start devops-jenkins
docker logs --tail 60 devops-jenkins
```

Așteaptă pornirea și deschide `http://localhost:8081`; verifică răspunsul cu `curl -I --max-time 10 http://localhost:8081/login`. Dacă apare ecranul de deblocare, citește parola numai în terminalul local:

```bash
docker exec devops-jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Nu copia parola în fișiere sau dovezi. Creează administratorul și instalează **Pipeline, Git și JUnit**, cu dependențele cerute. Notează versiunile pluginurilor. Dacă volumul păstrează deja configurarea Jenkins, autentifică-te cu administratorul existent; nu reseta datele.

### Agentul de build din WSL

Controllerul coordonează; buildurile rulează ca proces separat în WSL, unde ai deja Java și Git. Nu este necesar SSH sau portul 50000: folosim WebSocket.

1. În **Manage Jenkins → Nodes**, creează `wsl-agent`, tip permanent, un executor, label `wsl`. Folosește ca remote root calea absolută afișată de `printf '%s\n' "$HOME/jenkins-agent"`.
2. Selectează lansarea prin conectarea agentului la controller și activează WebSocket. Pune numărul de executori ai nodului built-in la `0`.
3. Creează în afara proiectului un fișier `~/.config/devops-lab/jenkins-agent.secret`, cu permisiuni `600`, și pune în el numai secretul afișat pentru acest agent. Nu-l trece în comandă sau în Git.
4. Într-un terminal WSL separat:

```bash
mkdir -p "$HOME/jenkins-agent"
curl -fSLo "$HOME/jenkins-agent/agent.jar" http://localhost:8081/jnlpJars/agent.jar
java -jar "$HOME/jenkins-agent/agent.jar" -url http://localhost:8081/ -name wsl-agent -webSocket -secret @"$HOME/.config/devops-lab/jenkins-agent.secret" -workDir "$HOME/jenkins-agent"
```

Agentul trebuie să apară online. Urmează cerința Java afișată de Jenkins dacă remotingul nu acceptă Java 21; instalează runtime-ul suplimentar separat, fără să schimbi JDK-ul folosit de Gradle. Procesul rulează în prim-plan și se oprește cu Ctrl+C. El execută cod cu drepturile contului tău: acceptă numai joburi și repository-uri proprii, de încredere. Pentru PR-uri neîncrezute ai nevoie de agent izolat, fără secretele contului.

### Aplicația comună

**Creează proiectul în Ubuntu (WSL), în `~/task-tracker` (`/home/u00025961/task-tracker`).** Acesta este folderul principal al aplicației și rădăcina viitorului repository Git. Codul Spring Boot intră în subfolderul `backend/`. Nu-l crea în `PlanDezvoltareDevOps` (acolo sunt lecțiile), în `~/jenkins-agent` sau în containerul Jenkins. În lecția 02, Jenkins va clona repository-ul în propriul workspace.

#### 1. Pregătește folderul backendului

În terminalul Ubuntu (WSL), rulează:

```bash
mkdir -p "$HOME/task-tracker/backend"
cd "$HOME/task-tracker/backend" && explorer.exe .
```

Ultima comandă deschide folderul **din WSL** în File Explorer. Dacă ai deja un proiect în `backend/`, nu extrage nimic peste el.

#### 2. Generează și extrage proiectul Gradle

La [Spring Initializr](https://start.spring.io) selectează **Project: Gradle - Groovy**, **Language: Java**, **Java: 21**, **Packaging: Jar** și **Artifact: task-tracker**. Adaugă Spring Web, Validation, Spring Data JPA și H2 Database, apoi apasă **Generate**.

Deschide arhiva ZIP descărcată și copiază **conținutul proiectului** în folderul `backend` deschis mai sus. Fișierele trebuie să fie direct în `backend/`, fără un nivel suplimentar `backend/task-tracker/`:

```text
~/task-tracker/            # rădăcina repository-ului Git
└── backend/               # proiectul Gradle
    ├── build.gradle
    ├── settings.gradle
    ├── gradlew
    ├── gradle/
    └── src/
```

#### 3. Verifică și pornește aplicația

După ce `build.gradle` și `gradlew` sunt direct în `backend/`, rulează din terminalul WSL:

```bash
cd "$HOME/task-tracker/backend"
chmod +x gradlew
./gradlew --version
./gradlew test bootJar
./gradlew bootRun
```

`bootJar` produce JAR-ul executabil în `build/libs/`. `bootRun` pornește aplicația pe portul 8080, rămâne în prim-plan și se oprește cu Ctrl+C. Rulează comenzile HTTP de verificare într-un alt terminal.

Nu instala Gradle global. Versiunea Gradle este fixată de wrapper: comite `gradlew` cu permisiune de execuție, `gradlew.bat`, fișierele din `gradle/wrapper/`, `build.gradle` și `settings.gradle`. Exclude `build/` și `.gradle/` din Git. Configurează remote-ul în repository-ul aplicației, nu în folderul cu planul.

## Verificare și lucru

- Verifică răspunsul Jenkins prin `curl -I --max-time 10 http://localhost:8081/login`.
- Pentru oprire și repornire folosește `docker stop devops-jenkins` și `docker start devops-jenkins`; volumul `devops-jenkins-home` păstrează configurarea. Nu șterge containerul sau volumul ca metodă de depanare.
- În lecția 02, verifică `java -version`, `git --version` și `./gradlew --version` pe agentul `wsl`, în workspace-ul clonat, nu doar în terminalul personal.
- Continuă implementarea și criteriile din lecție în același backend. Arhitectura și notițele nesensibile pot sta aici.
- La final oprește agentul prin Ctrl+C și containerul propriu prin `docker stop devops-jenkins`. Volumul rămâne. Dacă testezi recrearea containerului, păstrează digestul, opțiunile sale și același volum.
