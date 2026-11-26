# Lecția 19 — Terraform în Jenkins, plan și aprobare

**Săptămâna:** 7 · **Data de reper:** 05.01.2027 · **Durata:** 90 minute

## Obiective

- Rulezi validarea și scanarea IaC înainte de modificarea AWS.
- Aplici numai planul revizuit și aprobat.
- Separi permisiunile, branchurile și concurența operațiilor.

## Intrări necesare

[Lecția 18](./lectia-18.md) finalizată; agent Jenkins cu Terraform/AWS CLI și acces securizat la backend.

## Parcurs

1. **15 min — Acces CI.** Configurează sesiuni temporare/rol AWS dacă mediul o permite; altfel un credential Jenkins limitat și cu rotație explicită. Nu acorda administrator doar pentru a elimina erorile IAM. Protejează branchul și identitatea care poate aproba.
2. **20 min — Verificări.** Adaugă stage-uri fmt check, init, validate și Checkov cu versiune fixată. Definește constatările care blochează și excepțiile documentate. Testează pe un fixture IaC nesigur care nu va fi aplicat.
3. **20 min — Plan.** Generează un plan salvat și un sumar de revizuit. Tratează corect exit code 2 de la `-detailed-exitcode`: înseamnă schimbări, nu eroare. Nu publica planul binar sau JSON-ul complet ca artefact public, deoarece pot conține secrete.
4. **20 min — Aprobare și apply.** Cere aprobare cu timeout, numai de la utilizatori autorizați. Aplică exact fișierul de plan aprobat din același commit și backend. La schimbarea codului sau expirarea planului cere plan și aprobare noi.
5. **15 min — Verificare și cleanup.** Confirmă că PR-urile neîncrezute nu pot primi secrete și că două apply nu rulează simultan. Demonstrează un apply mic, apoi distruge resursele temporare și verifică finalizarea.

## Livrabile

- Flux CI: fmt → validate → scan → plan → aprobare → apply.
- Reguli de acces și stocare privată a planului.
- Probă de blocare a unui exemplu IaC nesigur.

## Criterii de finalizare

- [ ] Scannerul și validarea pot opri fluxul înainte de apply.
- [ ] Fără aprobare sau după timeout nu se aplică nimic.
- [ ] Planul aplicat este cel aprobat, nu unul recalculat în ascuns.
- [ ] Refuzul sau eroarea AWS este vizibilă ca eșec.
- [ ] Mediul temporar este curățat la final.

## Dovadă și recapitulare

Păstrează identificatorul buildului, commitul și aprobarea, fără conținut sensibil de state. Explică cine poate face plan, cine poate aproba și cine poate aplica.

**Navigare:** [Lecția precedentă](./lectia-18.md) · [Index](../README.md) · [Lecția următoare](./lectia-20.md)
