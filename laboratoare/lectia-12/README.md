# Lucru 12 — Keycloak și pregătirea TLS

[Lecția](../../lectii/lectia-12.md) · [Mediul WSL](../README.md)

## Prerechizite

- [11 finalizată](../lectia-11/README.md), port 8180 liber și resurse pentru Keycloak.
- OpenSSL și certificate CA în Ubuntu. Verifică `openssl version`; dacă lipsesc, instalează `openssl ca-certificates` prin `sudo -A apt-get install`.
- Decide un issuer canonic accesibil clienților și API-ului. `localhost` poate fi folosit numai cât toate procesele sunt locale; nu este o adresă validă pentru accesul ulterior din poduri sau AWS.

## Instalare

### Keycloak inițial, exclusiv local

Alege un tag exact din [imaginile oficiale Keycloak](https://quay.io/repository/keycloak/keycloak?tab=tags), conform [ghidului Docker](https://www.keycloak.org/getting-started/getting-started-docker). Creează privat `~/.config/devops-lab/keycloak.env`, cu `KC_BOOTSTRAP_ADMIN_USERNAME` și `KC_BOOTSTRAP_ADMIN_PASSWORD`, valori unice, apoi `chmod 600` pe fișier.

```bash
read -r -p 'Imagine quay.io/keycloak/keycloak cu tag exact: ' KEYCLOAK_IMAGE
docker pull "${KEYCLOAK_IMAGE:?Introdu imaginea}"
KEYCLOAK_DIGEST="$(docker image inspect "$KEYCLOAK_IMAGE" --format '{{index .RepoDigests 0}}')"
docker run -d --name devops-keycloak --network devops-lab \
  -p 127.0.0.1:8180:8080 \
  --env-file "$HOME/.config/devops-lab/keycloak.env" \
  --mount source=devops-keycloak-data,target=/opt/keycloak/data \
  "$KEYCLOAK_DIGEST" start-dev
docker logs --tail 60 devops-keycloak
curl -fsS --max-time 10 http://localhost:8180/realms/master/.well-known/openid-configuration
```

`start-dev` cu baza de dezvoltare și HTTP este **doar bootstrap local**, nu configurația finală a lecției. Nu-l publica în LAN sau internet. Creează realmul aplicației, rolurile și conturile de test; nu folosi realmul `master` pentru aplicație.

### Biblioteci și certificate

1. Adaugă în blocul `dependencies` din `backend/build.gradle` starterul OAuth2 Resource Server prin `implementation`. Folosește coordonatele potrivite versiunii tale: `org.springframework.boot:spring-boot-starter-oauth2-resource-server` pentru Boot 3 sau `org.springframework.boot:spring-boot-starter-security-oauth2-resource-server` pentru Boot 4.
2. Folosește o CA de laborator proprie sau una autorizată, cu cheia privată păstrată în `~/.config/devops-lab`, nu în Git.
3. Emite certificatul cu SAN pentru **numele exact folosit în URL**. Configurează HTTPS în Keycloak sau la un reverse proxy, conform [configurării TLS Keycloak](https://www.keycloak.org/server/enabletls). Montează numai certificatul/cheia serverului, nu cheia CA, și acordă citire doar identității serviciului.
4. Configurează HTTPS pentru API/reverse proxy și importă CA în truststore-urile folosite efectiv: browser Windows, Ubuntu/curl și JVM-ul backendului. Trustul din Windows nu se transferă automat în JVM sau în containere.
5. Actualizează issuerul, redirect URI-urile și verificările Jenkins/Ansible la URL-ul HTTPS final. Nu folosi `curl -k`, trust-all sau dezactivarea validării hostname.

Nu este obligatoriu un domeniu cumpărat. Un nume de laborator cere însă rezoluție configurată separat în Windows, WSL și ulterior în cluster. Dacă schimbi issuerul pentru Kubernetes, actualizează toți clienții și obține tokenuri noi.

## Verificare și lucru

Verifică discovery pe URL-ul HTTPS ales, cu `curl --cacert` pentru CA de laborator, apoi cazurile 401/403/succes din lecție. Nu marca lecția finalizată cu instalarea HTTP de bootstrap. Nu salva tokenuri sau capturi cu credentials.
