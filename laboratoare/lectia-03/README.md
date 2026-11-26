# Lucru 03 — Credentials, rapoarte și triggers

[Lecția](../../lectii/lectia-03.md) · [Mediul WSL](../README.md)

## Prerechizite

- [02 finalizată](../lectia-02/README.md), acces administrativ la Jenkins și repository propriu.
- Jenkins și agentul WSL pornite în timpul testului automat.

## Instalare / configurare

Nu instala servicii noi în WSL.

1. În **Manage Jenkins → Plugins**, verifică **Credentials Binding** și **JUnit**; instalează-le din Update Center dacă lipsesc. Notează versiunile și repornește Jenkins numai când nu rulează joburi. Dacă lista de pluginuri e goală sau apare o eroare SSL, aplică pasul pentru [firewall-ul Zscaler din 01](../lectia-01/README.md#dacă-pluginurile-nu-se-instalează-firewall-zscaler).
2. În **Manage Jenkins → Credentials**, creează un credential de laborator limitat la operația necesară; în cod apare doar ID-ul.

   **Alege operația, apoi tipul.** În această lecție operația este un apel *read-only* la API-ul GitHub pentru propriul repository. Tipul potrivit este **Secret text** (un singur token). Celelalte tipuri sunt pentru alte operații: *Username with password* pentru login la registry/Artifactory, *SSH Username with private key* pentru Ansible (09), *Secret file* pentru kubeconfig sau fișiere de parolă.

   **Creează tokenul cu privilegii minime:** GitHub → **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**. Setează **Repository access:** *Only select repositories* → `task-tracker` și o expirare scurtă (7 zile). La **Permissions** nu adăuga nimic: rămâne automat *Metadata: Read-only*, suficient pentru a citi detaliile repository-ului. Dacă organizația nu permite tokenuri, folosește o valoare de test; vei verifica doar bindingul, nu și apelul.

   **Adaugă-l în Jenkins:** **Manage Jenkins → Credentials → System → Global credentials (unrestricted) → Add Credentials**. *Kind:* **Secret text**, *Secret:* tokenul, *ID:* `lab-github-token`, *Description:* scopul și limitările. Doar ID-ul apare în Jenkinsfile.

   **Folosește-l** numai în stage-ul care are nevoie de el, prin `withCredentials([string(credentialsId: 'lab-github-token', variable: '...')]) { ... }`. Scrie comanda `sh` între ghilimele simple (`'...'` sau `'''...'''`), ca variabila să fie citită de shell, nu interpolată de Groovy. Nu afișa variabila, mediul (`env`, `printenv`) și nu activa `set -x`. Testează tokenul cu `curl -fsS` către `https://api.github.com/repos/<utilizator>/task-tracker`; răspunsul așteptat este HTTP 200.
3. Pentru laboratorul local configurează **Poll SCM**, de exemplu `H/5 * * * *`, în job sau în `triggers`, într-un singur loc. Un push poate aștepta până la următoarea verificare.

   **Ce face:** la fiecare interval, Jenkins întreabă GitHub dacă branchul configurat are commituri noi. Dacă da, pornește buildul; dacă nu, nu face nimic. `H/5 * * * *` este sintaxă cron (minut, oră, zi, lună, zi a săptămânii) și înseamnă „la fiecare 5 minute”. `H` alege un minut fix pentru fiecare job, ca joburile să nu verifice toate odată.

   **Unde îl pui — o singură variantă:**
   - recomandat, în Jenkinsfile, la nivelul `pipeline`, înainte de `stages`: `triggers { pollSCM('H/5 * * * *') }`;
   - sau în job: **Configure → Triggers → Poll SCM → Schedule**.

   Nu le folosi pe amândouă: la fiecare rulare, Jenkinsfile-ul suprascrie setarea din pagina jobului.

   **Activare:** după push, rulează o dată manual **Build Now**. Jenkins citește blocul `triggers` doar când execută Jenkinsfile-ul. După acest build, în **Configure → Triggers** apare bifat *Poll SCM* cu programul din fișier.

   **Verificare:** fă un commit și `git push`, apoi așteaptă până la 5 minute. În job, **Polling Log** arată ultima verificare. Buildul nou trebuie să aibă cauza *Started by an SCM change*.
4. Publică XML-urile Gradle din `backend/build/test-results/test/TEST-*.xml` prin `junit` în `post { always { ... } }`, la nivelul pipeline-ului, din rădăcina workspace-ului. Nu permite lipsa rapoartelor. Curăță rezultatele vechi înainte de compilare, ca să nu publici rapoartele unui build anterior.

Un webhook GitHub nu poate ajunge direct la `localhost` din WSL. Pollingul evită expunerea Jenkins pe internet; nu instala un tunel public doar pentru exercițiu.

## Lucru și verificare

Continuă același Jenkinsfile. Fă un push real și așteaptă execuția automată. Verifică rapoartele inclusiv pentru eșec și restricția de branch pentru operațiile cu credentials. Nu salva tokenuri, parole sau capturi cu valorile lor aici.
