# Lucru 06 — Artifactory și baza sa de date

[Lecția](../../lectii/lectia-06.md) · [Mediul WSL](../README.md)

## Prerechizite

- [05 finalizată](../lectia-05/README.md), Docker Compose, RAM și disc suficiente.
- Ediție Artifactory disponibilă legal, cu repository Maven. Verifică [instalarea oficială Docker](https://docs.jfrog.com/installation/docs/docker) și matricea bazei de date pentru versiunea aleasă.
- Portul 8082 este deja ocupat în mediul existent: folosim **8085 pe gazdă → 8082 în container**. Baza Artifactory nu este baza aplicației.

## Instalare

În acest folder creează `compose.yaml` după modelul de mai jos. Completează un fișier privat `~/.config/devops-lab/artifactory.env` cu `ARTIFACTORY_IMAGE` (referință exactă), `ARTIFACTORY_DB_IMAGE` (PostgreSQL compatibil, major 16 sau 17, cu tag exact) și `ARTIFACTORY_DB_PASSWORD` (parolă unică). Protejează-l cu `chmod 600`; nu-l salva în folderul lecției.

```yaml
services:
  artifactory-db:
    image: ${ARTIFACTORY_DB_IMAGE:?Setează imaginea PostgreSQL compatibilă}
    environment:
      POSTGRES_DB: artifactory
      POSTGRES_USER: artifactory
      POSTGRES_PASSWORD: ${ARTIFACTORY_DB_PASSWORD:?Setează parola}
    volumes:
      - artifactory_db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U artifactory -d artifactory"]
      interval: 10s
      timeout: 5s
      retries: 20
  artifactory:
    image: ${ARTIFACTORY_IMAGE:?Setează imaginea Artifactory}
    depends_on:
      artifactory-db:
        condition: service_healthy
    ports:
      - "127.0.0.1:8085:8082"
    environment:
      JF_SHARED_DATABASE_TYPE: postgresql
      JF_SHARED_DATABASE_DRIVER: org.postgresql.Driver
      JF_SHARED_DATABASE_URL: jdbc:postgresql://artifactory-db:5432/artifactory
      JF_SHARED_DATABASE_USERNAME: artifactory
      JF_SHARED_DATABASE_PASSWORD: ${ARTIFACTORY_DB_PASSWORD:?Setează parola}
    volumes:
      - artifactory_data:/var/opt/jfrog/artifactory
volumes:
  artifactory_db:
  artifactory_data:
networks:
  default:
    external: true
    name: devops-lab
```

Imaginea poate fi, conform ediției disponibile, `releases-docker.jfrog.io/jfrog/artifactory-oss:<tag-exact>`; înlocuiește întregul placeholder cu tagul publicat. Dacă ediția aleasă impune altă configurație, folosește bundle-ul Compose oficial pentru acea versiune, păstrând izolarea și portul gazdei.

```bash
cd "$HOME/PlanDezvoltareDevOps/laboratoare/lectia-06"
docker compose -p devops-artifactory --env-file "$HOME/.config/devops-lab/artifactory.env" config --quiet
docker compose -p devops-artifactory --env-file "$HOME/.config/devops-lab/artifactory.env" pull
docker compose -p devops-artifactory --env-file "$HOME/.config/devops-lab/artifactory.env" up -d
docker compose -p devops-artifactory --env-file "$HOME/.config/devops-lab/artifactory.env" logs --tail 80 artifactory
curl -fsS --max-time 10 http://localhost:8085/artifactory/api/system/ping
```

Așteaptă răspunsul `OK`, finalizează configurarea pe `http://localhost:8085` și schimbă credentialele inițiale. Nu scădea permisiunile directoarelor la `777` dacă serviciul raportează erori de volum; verifică UID-ul documentat pentru imagine.

### Publicare cu Gradle

În Artifactory creează repository-uri de tip Maven pentru snapshoturi și release-uri. „Maven” desemnează aici formatul repository-ului, nu unealta de build. Adaugă în blocul `plugins` din `backend/build.gradle` pluginul Gradle `id 'maven-publish'` și configurează o publicație care primește fișierul verificat, nu taskul `bootJar`:

```groovy
def verifiedJar = providers.gradleProperty('verifiedJar')
def releaseVersion = providers.gradleProperty('releaseVersion')
def repoUrl = providers.environmentVariable('ARTIFACTORY_REPO_URL')

if (verifiedJar.isPresent() && releaseVersion.isPresent() && repoUrl.isPresent()) {
    def endpoint = uri(repoUrl.get())
    publishing {
        publications {
            taskTracker(MavenPublication) {
                artifactId = 'task-tracker'
                version = releaseVersion.get()
                artifact(file(verifiedJar.get()))
            }
        }
        repositories {
            maven {
                name = 'artifactory'
                url = endpoint
                allowInsecureProtocol = endpoint.scheme == 'http' &&
                    endpoint.host == 'localhost' && endpoint.port == 8085
                credentials(PasswordCredentials)
            }
        }
    }
}
```

`groupId` vine din `group` în `build.gradle`. În Jenkins setează `ARTIFACTORY_REPO_URL` la URL-ul repository-ului ales (de exemplu `http://localhost:8085/artifactory/libs-release-local` pentru agentul WSL), iar utilizatorul și tokenul prin credentials în `ORG_GRADLE_PROJECT_artifactoryUsername` și `ORG_GRADLE_PROJECT_artifactoryPassword`. HTTP este permis doar pentru portul local al laboratorului; folosește HTTPS pentru un repository la distanță.

Din `backend/`, după validarea tuturor etapelor, rulează taskul cu calea exactă (`VERIFIED_JAR`) și versiunea (`RELEASE_VERSION`) stabilite de build. Verifică mai întâi cu `--dry-run` că nu apar `bootJar`, compilarea sau testele, apoi rulează fără acel flag:

```bash
./gradlew --no-daemon publishTaskTrackerPublicationToArtifactoryRepository \
  -PverifiedJar="${VERIFIED_JAR:?Lipsește calea JAR-ului verificat}" \
  -PreleaseVersion="${RELEASE_VERSION:?Lipsește versiunea artefactului}"
```

## Verificare și lucru

Descarcă versiunea explicită publicată și compară SHA-256 cu JAR-ul verificat de build. La pauză folosește aceeași comandă Compose cu `stop`, nu șterge volumele.
