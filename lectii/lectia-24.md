# Lecția 24 — Cypress, pipeline complet și portofoliu

**Săptămâna:** 8 · **Data de reper:** 17.01.2027 · **Durata:** 180 minute

## Obiective

- Rulezi exact cele trei scenarii e2e din sursă, inclusiv în Jenkins.
- Demonstrezi traseul complet al aplicației fără ocolirea controalelor.
- Închizi planul cu dovezi, documentație și o evaluare realistă a nivelului.

## Intrări necesare

[Lecția 23](./lectia-23.md) finalizată; mediu e2e separat cu backend, frontend, DB și Keycloak. Folosește conturi/date de test și un agent care suportă browserul headless.

## Parcurs

1. **25 min — Cypress.** Adaugă versiunea compatibilă și configurează base URL, selectori și sesiunea autentificată. Parolele de test vin din Jenkins; nu sunt în fixtures sau configurația versionată. Dezactivează afișarea lor și verifică capturile rezultate.
2. **45 min — Trei scenarii.** Scrie testele: încărcarea listei cu date cunoscute; adăugarea unui task și verificarea persistenței după refresh; validarea formularului gol fără creare. Resetează izolat datele și evită ordinea obligatorie între teste.
3. **35 min — Headless în Jenkins.** Instalează reproductibil dependențele din lockfile, rulează type-check/build și pornește mediul de test. Așteaptă readiness prin verificări cu timeout, nu prin sleep fix. Rulează Cypress headless și propagă eșecul.
4. **25 min — Rapoarte și probă negativă.** Publică rapoarte JUnit și capturi utile redactate, inclusiv la eșec. Provoacă temporar eșecul unei aserțiuni și confirmă că livrarea nu continuă. Oprește mediul de test la final, inclusiv în caz de eroare.
5. **25 min — Demonstrație finală.** Fă un push și urmărește buildul, scanările, publicarea și testele frontend/e2e înainte de promovare. Arată deploy-ul local; pentru AWS folosește dovezile lecției 20, fără resurse ținute pornite inutil. Nu pretinde un nou deploy AWS dacă nu îl execuți.
6. **25 min — Portofoliu și bilanț.** Actualizează README-ul aplicației cu arhitectura, pornirea, pipeline-ul și limitele. Completează CV-ul cu tehnologiile efectiv exersate, cele patru povești și un exemplu AI verificat. Inventariază/oprește serviciile și verifică resursele AWS reziduale.

## Livrabile

- Trei teste Cypress și un stage headless în Jenkins.
- Pipeline complet cu rapoarte și comportament de eșec demonstrat.
- Repository documentat, portofoliu și bilanț al celor 24 de lecții.

## Criterii de finalizare

- [ ] Cele trei teste trec împreună și independent, fără date reziduale între ele.
- [ ] Cel puțin o verificare negativă produce build eșuat, apoi este reparată.
- [ ] Testul de adăugare verifică persistența reală, nu doar un răspuns API simulat.
- [ ] Rezultatele e2e blochează promovarea; rapoartele lipsă nu sunt acceptate ca succes.
- [ ] Nici logurile, nici capturile/video nu dezvăluie credentials.
- [ ] Documentația distinge laboratorul single-node de producție și practica AWS de studiul conceptual.

## Evaluare finală

Poți explica și demonstra: pipeline cu gate eșuat, deploy Ansible idempotent, dependență izolată prin reziliență și infrastructură Terraform cu aprobare. Poți arăta două ecrane React și trei scenarii e2e.

React intermediar, Kubernetes de producție, operarea EKS/MSK și recuperarea în caz de dezastru rămân aprofundări separate. Nu sunt rezultate promise în cele 48 de ore.

**Navigare:** [Lecția precedentă](./lectia-23.md) · [Index și toate lecțiile](../README.md)
