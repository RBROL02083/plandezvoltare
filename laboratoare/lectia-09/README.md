# Lucru 09 — PostgreSQL, Vault și deploy din Jenkins

[Lecția](../../lectii/lectia-09.md) · [Mediul WSL](../README.md)

## Prerechizite

- [08 finalizată](../lectia-08/README.md), serviciu funcțional, Ansible și agentul `wsl`.
- Port 15432 liber. Nu folosim portul 5433 deja ocupat.
- Docker și o imagine PostgreSQL **major 17, patch exact**, compatibilă cu driverul ales. Pentru major 18+ se schimbă și convențiile volumului; nu schimba majorul fără migrare.

## Instalare

### Baza aplicației

Creează aici `compose.yaml`:

```yaml
services:
  app-db:
    image: ${APP_DB_IMAGE:?Setează imaginea PostgreSQL 17 cu tag exact}
    environment:
      POSTGRES_DB: task_tracker
      POSTGRES_USER: lab_admin
      POSTGRES_PASSWORD: ${APP_DB_ADMIN_PASSWORD:?Setează parola administratorului}
    ports:
      - "127.0.0.1:15432:5432"
    volumes:
      - app_db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U lab_admin -d task_tracker"]
      interval: 5s
      timeout: 5s
      retries: 20
volumes:
  app_db:
```

Completează privat `~/.config/devops-lab/postgres.env` cu cele două variabile, apoi `chmod 600` pe fișier:

```bash
cd "$HOME/PlanDezvoltareDevOps/laboratoare/lectia-09"
docker compose -p devops-postgres --env-file "$HOME/.config/devops-lab/postgres.env" config --quiet
docker compose -p devops-postgres --env-file "$HOME/.config/devops-lab/postgres.env" up -d
docker compose -p devops-postgres --env-file "$HOME/.config/devops-lab/postgres.env" exec app-db pg_isready -U lab_admin -d task_tracker
```

Deschide `psql` interactiv prin același `docker compose ... exec app-db psql -U lab_admin -d task_tracker`. Creează un rol de login separat pentru aplicație și un schema deținut de el; setează parola prin `\password numele_rolului`, nu într-o comandă salvată în istoric. Nu conecta aplicația ca `lab_admin`, care are drepturi administrative.

### Backend și CI

- Adaugă `runtimeOnly 'org.postgresql:postgresql'` în blocul `dependencies` din `backend/build.gradle`, folosind versiunea administrată de BOM-ul Spring Boot.
- URL-ul JDBC pentru procesul din WSL este `jdbc:postgresql://127.0.0.1:15432/task_tracker`.
- `ansible-vault` este deja instalat prin `ansible-core`; verifică `ansible-vault --version`.
- Criptează variabilele aplicației în repository. Parola Vault și cheia SSH sunt credentials Jenkins distincte. Folosește bindingul SSH Agent sau un fișier temporar de credential; instalează pluginul **SSH Agent** doar dacă alegi acel binding.
- Configurează PATH-ul agentului cu `~/.local/bin` și verifică `ansible-playbook --version` în job. Host keys trebuie verificate și în mediul efectiv al jobului.
- Automatizarea `become` trebuie pregătită explicit: drepturi sudo limitate sau credential de escaladare gestionat sigur, fără prompt interactiv și fără reguli globale `NOPASSWD: ALL`.

## Verificare și lucru

Continuă rolul și Jenkinsfile-ul comun. După deploy verifică API-ul, persistența după restart și `changed=0, failed=0` la a doua execuție. Oprește PostgreSQL prin `docker compose ... stop`, păstrând volumul.
