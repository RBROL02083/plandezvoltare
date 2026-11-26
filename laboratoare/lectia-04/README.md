# Lucru 04 — SonarQube și quality gate

[Lecția](../../lectii/lectia-04.md) · [Mediul WSL](../README.md)

## Prerechizite

- [03 finalizată](../lectia-03/README.md), Docker, port 9000 liber și RAM disponibil.
- Citește [cerințele Linux pentru Community Build](https://docs.sonarsource.com/sonarqube-community-build/server-installation/pre-installation/linux/). La redactare cer minimum `vm.max_map_count=524288`, `fs.file-max=131072`, 131072 file descriptors și 8192 threads.
- Valoarea observată `vm.max_map_count=65530` nu este suficientă.

## Instalare

### Limitele Linux

```bash
sysctl vm.max_map_count fs.file-max
```

Doar cu acord pe distribuția partajată și numai dacă valoarea actuală este sub prag:

```bash
sudo -A sysctl -w vm.max_map_count=524288
```

Nu micșora o limită deja mai mare. Dacă și `fs.file-max` este prea mic, mărește-l separat la pragul cerut. Pentru persistență adaugă numai setările necesare prin `sudo -A -e /etc/sysctl.d/90-devops-sonarqube.conf`, apoi aplică fișierul cu `sudo -A sysctl -p /etc/sysctl.d/90-devops-sonarqube.conf`. Kernelul WSL este o resursă comună: verifică din nou după repornire.

### Containerul

Alege un tag exact compatibil din [imaginile oficiale](https://hub.docker.com/_/sonarqube/tags), ediția Community Build sau altă ediție la care ai drept de utilizare:

```bash
read -r -p 'Imagine SonarQube cu tag exact: ' SONAR_IMAGE
docker pull "${SONAR_IMAGE:?Introdu imaginea}"
SONAR_DIGEST="$(docker image inspect "$SONAR_IMAGE" --format '{{index .RepoDigests 0}}')"
docker run -d --name devops-sonarqube --network devops-lab \
  -p 127.0.0.1:9000:9000 \
  --ulimit nofile=131072:131072 --ulimit nproc=8192:8192 \
  --mount source=devops-sonar-data,target=/opt/sonarqube/data \
  --mount source=devops-sonar-extensions,target=/opt/sonarqube/extensions \
  --mount source=devops-sonar-logs,target=/opt/sonarqube/logs \
  "$SONAR_DIGEST"
docker logs --tail 80 devops-sonarqube
curl -fsS --max-time 10 http://localhost:9000/api/system/status
```

Așteaptă statusul `UP`, nu doar containerul pornit. Baza H2 implicită este acceptabilă **doar pentru acest laborator**, cu limitele documentate; nu este o arhitectură de producție. Pentru o bază externă urmează [configurarea oficială Docker](https://docs.sonarsource.com/sonarqube-community-build/server-installation/from-docker-image/set-up-and-start-container).

### Integrarea Jenkins

Instalează **SonarQube Scanner for Jenkins** din Update Center pentru configurarea serverului și `waitForQualityGate`. Creează proiectul și tokenul, apoi stochează tokenul în Jenkins Credentials. În blocul `plugins` din `backend/build.gradle`, adaugă [pluginul SonarScanner for Gradle](https://plugins.gradle.org/plugin/org.sonarqube) `org.sonarqube` cu o versiune exactă, compatibilă.

Rulează analiza din `backend/`, în același workspace, după `./gradlew test`; scannerul nu compilează și nu rulează testele automat. În `withSonarQubeEnv`, mapează tokenul Jenkins la variabila pe care o citește scannerul, fără interpolare Groovy. Exemplu pentru pasul shell:

```bash
set +x
SONAR_TOKEN="$SONAR_AUTH_TOKEN" ./gradlew --no-daemon sonar
```

Configurează `sonar.projectKey` în `build.gradle`, dar nu salva tokenul acolo. Pentru acoperire, aplică pluginul Gradle `jacoco`, activează `xml.required = true` în `reports` pentru `jacocoTestReport`, apoi rulează `./gradlew --no-daemon jacocoTestReport` după teste și înainte de `sonar`. Raportul implicit este `backend/build/reports/jacoco/test/jacocoTestReport.xml` din rădăcina repository-ului.

- Folosește același URL, `http://devops-sonarqube:9000`, în integrarea Jenkins și în scanner. Cu acord, adaugă în fișierul hosts al WSL aliasul `127.0.0.1 devops-sonarqube`, fără să suprascrii celelalte intrări; verifică prin `getent hosts devops-sonarqube` și `curl`. În Docker, numele este rezolvat deja de rețeaua `devops-lab` către container.
- Nu combina URL-ul intern al controllerului cu `localhost` în rapoartele scannerului: pașii care așteaptă gate-ul trebuie să poată accesa URL-urile analizei. Din browser poți deschide `http://localhost:9000`; pentru legăturile cu alias configurează rezoluția și în Windows.
- Webhook-ul SonarQube către Jenkins folosește `http://devops-jenkins:8080/sonarqube-webhook/`, cu `/` final și secretul webhookului.

## Verificare și lucru

Continuă Jenkinsfile-ul comun. Demonstrează verdictul gate-ului pentru analiza curentă, cu timeout și probă negativă. La pauză oprește numai `devops-sonarqube`; păstrează volumele.
