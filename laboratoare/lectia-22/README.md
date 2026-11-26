# Lucru 22 — Vite, React și TypeScript în WSL

[Lecția](../../lectii/lectia-22.md) · [Mediul WSL](../README.md)

## Prerechizite

- [21 finalizată](../lectia-21/README.md), API-ul local securizat, DB și Keycloak disponibile. AWS poate rămâne fără resurse temporare.
- Node 26.13.0 și npm 11.20.0 există deja. Nu le reinstala și nu instala pachetul Node vechi din apt.
- Port 5173 liber, issuerul și CA de laborator de încredere în browser.

## Instalare

Verifică versiunile și cerințele [Vite](https://vite.dev/guide/). Alege un release exact de `create-vite` compatibil cu Node-ul folosit:

```bash
node --version
npm --version
read -r -p 'Versiune exactă create-vite: ' CREATE_VITE_VERSION
npm view "create-vite@${CREATE_VITE_VERSION:?Introdu versiunea}" engines
```

Numai dacă `~/task-tracker/frontend` nu există:

```bash
cd "$HOME/task-tracker"
npm create "vite@$CREATE_VITE_VERSION" frontend -- --template react-ts
cd frontend
npm install
node --version | sed 's/^v//' > .node-version
npm run build
```

`npm create vite@VERSIUNE` selectează pachetul `create-vite` la acea versiune. Păstrează manifestul și `package-lock.json`; în CI și la instalările ulterioare folosește `npm ci`. Dacă folderul există deja, citește manifestul și folosește lockfile-ul, nu genera din nou proiectul peste el.

Adaugă clientul OIDC ales, de exemplu `keycloak-js`, cu versiune exactă compatibilă:

```bash
read -r -p 'Versiune exactă keycloak-js: ' KEYCLOAK_JS_VERSION
npm install --save-exact "keycloak-js@${KEYCLOAK_JS_VERSION:?Introdu versiunea}"
```

În Keycloak creează un client **public** SPA cu Authorization Code + PKCE, redirect URI și origin exacte. Nu există client secret în frontend; variabilele `VITE_*` ajung în bundle-ul public.

## Verificare și lucru

```bash
cd "$HOME/task-tracker/frontend"
npm run build
npm run dev -- --host 127.0.0.1
```

Deschide URL-ul afișat, implicit `http://localhost:5173`. Configurează proxy same-origin sau CORS limitat și încrederea CA, fără să dezactivezi protecția API. Dev serverul rămâne în terminal și se oprește cu Ctrl+C. Codul rămâne în frontendul comun; aici păstrezi numai dovezi fără tokenuri.
