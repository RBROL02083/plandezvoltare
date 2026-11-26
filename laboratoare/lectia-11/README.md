# Lucru 11 — Actuator și observabilitate

[Lecția](../../lectii/lectia-11.md) · [Mediul WSL](../README.md)

## Prerechizite

- [10 finalizată](../lectia-10/README.md), aplicație și serviciu systemd administrabile în laborator.
- curl există; `jq` este opțional pentru citirea răspunsurilor JSON.

## Instalare / configurare

Adaugă `implementation 'org.springframework.boot:spring-boot-starter-actuator'` în blocul `dependencies` din `backend/build.gradle`, dacă lipsește. Folosește versiunea administrată de Spring Boot. Micrometer vine prin integrarea Actuator; nu instala un server Micrometer.

Doar dacă vrei filtrare JSON și `jq` lipsește:

```bash
sudo -A apt-get update
sudo -A apt-get install jq
jq --version
```

În configurația aplicației activează explicit probes și expune numai endpointurile necesare. Configurează graceful shutdown și un timeout systemd mai mare decât fereastra de oprire. Nu instala Prometheus/Grafana: nu sunt prerechizite ale acestei lecții.

## Verificare și lucru

După redeploy:

```bash
curl -fsS --max-time 10 http://localhost:8080/actuator/health/liveness
curl -fsS --max-time 10 http://localhost:8080/actuator/health/readiness
sudo -A journalctl -u task-tracker -n 50 --no-pager
```

Aceste adrese presupun probele configurate în lecție. Un `404` înseamnă că nu sunt încă activate, nu că verificarea a trecut. După securizarea din 12 actualizează schema, portul și autentificarea verificărilor. Testează oprirea numai a serviciului propriu, nu oprirea WSL.
