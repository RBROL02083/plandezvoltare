# Lucru 03 — Credentials, rapoarte și triggers

[Lecția](../../lectii/lectia-03.md) · [Mediul WSL](../README.md)

## Prerechizite

- [02 finalizată](../lectia-02/README.md), acces administrativ la Jenkins și repository propriu.
- Jenkins și agentul WSL pornite în timpul testului automat.

## Instalare / configurare

Nu instala servicii noi în WSL.

1. În **Manage Jenkins → Plugins**, verifică **Credentials Binding** și **JUnit**; instalează-le din Update Center dacă lipsesc. Notează versiunile și repornește Jenkins numai când nu rulează joburi.
2. În **Manage Jenkins → Credentials**, creează un credential de laborator limitat la operația necesară; în cod apare doar ID-ul.
3. Pentru laboratorul local configurează **Poll SCM**, de exemplu `H/5 * * * *`, în job sau în `triggers`, într-un singur loc. Un push poate aștepta până la următoarea verificare.
4. Publică XML-urile Gradle din `backend/build/test-results/test/TEST-*.xml` prin `junit` în `post { always { ... } }`, la nivelul pipeline-ului, din rădăcina workspace-ului. Nu permite lipsa rapoartelor. Curăță rezultatele vechi înainte de compilare, ca să nu publici rapoartele unui build anterior.

Un webhook GitHub nu poate ajunge direct la `localhost` din WSL. Pollingul evită expunerea Jenkins pe internet; nu instala un tunel public doar pentru exercițiu.

## Lucru și verificare

Continuă același Jenkinsfile. Fă un push real și așteaptă execuția automată. Verifică rapoartele inclusiv pentru eșec și restricția de branch pentru operațiile cu credentials. Nu salva tokenuri, parole sau capturi cu valorile lor aici.
