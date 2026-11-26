# Lucru 23 — React Router și pregătirea datelor e2e

[Lecția](../../lectii/lectia-23.md) · [Mediul WSL](../README.md)

## Prerechizite

- [22 finalizată](../lectia-22/README.md), frontend funcțional cu autentificare și API securizat.
- User de test cu drept de creare și contract POST stabil.
- Strategie de DB/date separate de laboratorul personal, înainte de orice resetare e2e.

## Instalare

Nu ai nevoie de servicii Ubuntu noi. În proiectul existent instalează [React Router](https://reactrouter.com/start/declarative/installation) cu versiune exactă și verifică cerințele acelei versiuni:

```bash
cd "$HOME/task-tracker/frontend"
read -r -p 'Versiune exactă react-router-dom: ' ROUTER_VERSION
npm view "react-router-dom@${ROUTER_VERSION:?Introdu versiunea}" engines
npm install --save-exact "react-router-dom@$ROUTER_VERSION"
npm run build
```

Dacă proiectul are deja routerul potrivit, nu îl instala a doua oară. Păstrează un singur model de rutare și urmează API-ul documentației pentru majorul ales; exemplele din alte majore pot diferi.

## Configurare și verificare

- Implementează rutele în `~/task-tracker/frontend`, cu labeluri, mesaje și selectori stabili.
- Verifică accesul direct și refreshul pe o rută; configurează fallbackul SPA în serverul care livrează frontendul, nu în API.
- Pregătește DB/user/realm sau conturi de test izolate. Operația de resetare trebuie disponibilă numai profilului de test.
- Nu instala Cypress încă dacă doar pregătești aplicația; instalarea și bibliotecile Linux sunt în 24.

Rulează buildul, apoi verifică manual crearea, titlul gol, erorile și folosirea cu tastatura. Nu transforma un eșec de rețea într-o notificare de succes.
