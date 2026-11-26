# Lucru 24 — Cypress headless pe Ubuntu 22.04 în WSL

[Lecția](../../lectii/lectia-24.md) · [Mediul WSL](../README.md)

## Prerechizite

- [23 finalizată](../lectia-23/README.md), frontend/backend/DB/Keycloak de test izolate.
- Node și npm compatibile cu Cypress ales; documentația consultată acceptă Node 26 și Ubuntu 22.04 x64. Verifică din nou pentru versiunea fixată.
- Agentul Jenkins nativ WSL, cu aceleași unelte și biblioteci. Nu este necesară interfață grafică Windows pentru rularea headless.

## Instalare

### Biblioteci Linux

Lista este pentru **Ubuntu 22.04 existent**, conform [Cypress](https://docs.cypress.io/app/get-started/install-cypress#Linux-Prerequisites). Pe Ubuntu 24.04 unele nume au sufixul `t64`; nu amesteca listele:

```bash
sudo -A apt-get update
sudo -A apt-get install libgtk-3-0 libgbm-dev libnotify-dev libnss3 \
  libxss1 libasound2 libxtst6 xauth xvfb
```

Unele biblioteci există deja; apt instalează lipsurile și dependențele cerute. Nu rula browserul cu `--no-sandbox` ca soluție implicită pentru o eroare.

### Pachetul și binarul Cypress

Alege o versiune exactă suportată:

```bash
cd "$HOME/task-tracker/frontend"
read -r -p 'Versiune exactă Cypress: ' CYPRESS_VERSION
npm view "cypress@${CYPRESS_VERSION:?Introdu versiunea}" engines
npm install --save-dev --save-exact "cypress@$CYPRESS_VERSION"
npx --no-install cypress install
npx --no-install cypress verify
npx --no-install cypress version
```

Instalarea explicită a binarului este importantă și când politica npm blochează scriptul `postinstall`. Nu dezactiva global politicile de scripturi ca să elimini avertismentul. Păstrează lockfile-ul. Dacă folosești raportorul JUnit inclus prin opțiunea `--reporter junit`, nu este obligatoriu un alt pachet de raportare.

## Configurare și verificare

Implementează configurația și **cele trei scenarii** din lecție în frontendul comun. Apoi, cu mediul de test pornit și readiness verificat:

```bash
cd "$HOME/task-tracker/frontend"
npm ci
npm run build
npx --no-install cypress install
npx --no-install cypress verify
npx --no-install cypress run --headless --browser electron
```

Electron este inclus de Cypress; nu instala Chrome global numai pentru prima rulare. În CI:

- Verifică PATH-ul, cache-ul binarului și bibliotecile Linux pentru identitatea agentului. Un agent în Docker are nevoie de aceste dependențe în imagine, nu doar pe WSL.
- Așteaptă readiness prin probe cu timeout, nu printr-un `sleep` fix.
- Injectează conturile de test prin Jenkins Credentials, fără parole în fixtures, capturi sau loguri.
- Publică rapoartele și diagnosticul și la eșec; oprește numai mediul de test propriu într-un bloc de cleanup executat și la eroare.

Verifică eșecul controlat și blocarea promovării. La încheiere inventariază serviciile locale și resursele AWS fără prune global și fără ștergerea datelor altor proiecte.
