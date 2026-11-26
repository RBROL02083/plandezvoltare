# Lucru 05 — Dependency-Check, Trivy și gitleaks

[Lecția](../../lectii/lectia-05.md) · [Mediul WSL](../README.md)

## Prerechizite

- [04 finalizată](../lectia-04/README.md), Docker accesibil agentului WSL, JAR și Dockerfile minimal conform lecției.
- Acces pentru descărcarea bazelor de vulnerabilități și, dacă este necesar, cheie NVD în Jenkins Credentials.
- Folosim scanere în containere: nu este necesară instalarea globală Trivy sau gitleaks în Ubuntu.

## Instalare / pregătire

### Dependency-Check

În blocul `plugins` din `backend/build.gradle`, adaugă [pluginul Gradle OWASP](https://plugins.gradle.org/plugin/org.owasp.dependencycheck): `id 'org.owasp.dependencycheck' version '<versiune-exactă>'`. Înlocuiește placeholderul cu o versiune suportată de JDK-ul și wrapperul proiectului. Configurează pragul de blocare în același fișier:

```groovy
dependencyCheck {
    failBuildOnCVSS = 7.0
    failOnError = true
    nvd {
        def key = providers.environmentVariable('NVD_API_KEY').orNull
        if (key) {
            apiKey = key
        }
    }
}
```

Pentru prima rulare manuală:

```bash
cd "$HOME/task-tracker/backend"
./gradlew --no-daemon dependencyCheckAnalyze
```

Raportul HTML implicit este `backend/build/reports/dependency-check-report.html` din rădăcina repository-ului. În Jenkins, leagă credentialul NVD la `NVD_API_KEY` doar în etapa de scanare; nu pune cheia în comandă sau în `build.gradle`. O eroare de actualizare a bazei este eșec de scanare, nu rezultat curat.

### Trivy și gitleaks

Alege taguri exacte din [Trivy](https://github.com/aquasecurity/trivy/releases) și [gitleaks](https://github.com/gitleaks/gitleaks/releases). Folosește imaginile indicate de proiecte:

```bash
read -r -p 'Imagine aquasec/trivy cu tag exact: ' TRIVY_IMAGE
read -r -p 'Imagine ghcr.io/gitleaks/gitleaks cu tag exact: ' GITLEAKS_IMAGE
docker pull "${TRIVY_IMAGE:?Introdu imaginea}"
docker pull "${GITLEAKS_IMAGE:?Introdu imaginea}"
docker run --rm "$TRIVY_IMAGE" --version
docker run --rm "$GITLEAKS_IMAGE" version
```

Notează digesturile prin `docker image inspect`. Nu monta socketul Docker în controller sau în scaner. Pentru Trivy exportă imaginea exactă a aplicației într-o arhivă locală:

```bash
cd "$HOME/PlanDezvoltareDevOps/laboratoare/lectia-05"
read -r -p 'Imaginea aplicației construită din commitul curent: ' APP_IMAGE
docker image save -o task-tracker.tar "${APP_IMAGE:?Introdu imaginea aplicației}"
docker run --rm -v "$PWD:/work:ro" \
  --mount source=devops-trivy-cache,target=/root/.cache/ \
  "$TRIVY_IMAGE" image --input /work/task-tracker.tar \
  --severity HIGH,CRITICAL --exit-code 1
docker run --rm --user "$(id -u):$(id -g)" \
  -v "$HOME/task-tracker:/repo:ro" \
  "$GITLEAKS_IMAGE" git /repo --redact
```

Nu comite arhiva imaginii. În CI salvează referințele fixate în configurația pipeline-ului; variabilele terminalului personal nu sunt transferate automat jobului.

## Verificare și lucru

Aplică cele trei politici din lecție în pipeline. Testează numai cu secrete sintetice și rapoarte redactate. Un rezultat nenul trebuie să blocheze publicarea; nu ignora codurile de ieșire. Șterge doar arhiva proprie când nu mai este necesară.
