# Lecția 03 — Credentials, rapoarte și declanșare automată

**Săptămâna:** 1 · **Data de reper:** 29.11.2026 · **Durata:** 180 minute

## Obiective

- Legi un push de execuția automată a pipeline-ului.
- Folosești credentials binding fără a divulga valorile.
- Publici rapoarte JUnit inclusiv la eșec și înțelegi parametrii și condițiile.

## Intrări necesare

[Lecția 02](./lectia-02.md) finalizată; acces administrativ la Jenkins și la repository-ul propriu.

## Parcurs

1. **30 min — Declanșare.** Configurează webhook dacă există o cale sigură de acces; altfel folosește polling SCM. Nu publica interfața Jenkins pe internet numai pentru webhook. Verifică asocierea cu branchul corect.
2. **35 min — Credentials.** Adaugă în Jenkins un credential de laborator cu privilegii minime pentru operația aleasă. Referențiază-l prin ID și limitează bindingul la stage-ul necesar. Evită interpolarea secretelor în Groovy, comenzile cu trasare și afișarea mediului.
3. **30 min — Parametri și condiții.** Introdu un parametru boolean pentru publicarea ulterioară și un `when` care permite operații de livrare numai de pe branchul de încredere. Separă buildurile de PR de operațiile care primesc secrete.
4. **35 min — Post actions.** Publică XML-urile JUnit într-un bloc `post` potrivit și păstrează artefactele de diagnostic nesensibile. Verifică atât succesul, cât și eșecul testelor; un director fără rapoarte nu trebuie acceptat tacit.
5. **30 min — Test cap-coadă.** Fă un push real și urmărește buildul fără declanșare manuală. Repetă cu un test eșuat și repară. Verifică absența secretelor în loguri și artefacte.
6. **20 min — Consolidare.** Explică `when`, `environment`, parametrii și shared libraries, acestea din urmă doar conceptual. Actualizează ghidul de rulare și notează un exemplu de utilizare AI verificat prin teste.

## Livrabile

- Pipeline automat cu rapoarte și credential referențiat sigur.
- Regulă explicită pentru branchurile care pot publica sau face deploy.
- Checkpointul săptămânii 1: cod → push → build → teste → rezultat.

## Criterii de finalizare

- [ ] Un push pornește automat buildul; pollingul poate introduce întârzierea configurată.
- [ ] Rapoartele testelor sunt accesibile și la build eșuat.
- [ ] Un PR neîncrezut nu poate primi credentials de publicare/deploy.
- [ ] Parametrul nu poate ocoli restricția de branch.
- [ ] Niciun secret nu apare în cod, loguri sau artefacte.

## Dovadă și recapitulare

Păstrează un build automat reușit și unul eșuat. Explică de ce mascarea în log nu înlocuiește izolarea credentialelor.

**Navigare:** [Lecția precedentă](./lectia-02.md) · [Index](../README.md) · [Lecția următoare](./lectia-04.md)
