# Lecția 16 — AWS, costuri și state remote

**Săptămâna:** 6 · **Data de reper:** 29.12.2026 · **Durata:** 90 minute

## Obiective

- Pregătești accesul AWS cu privilegii limitate și MFA.
- Creezi backendul Terraform separat de infrastructura temporară.
- Demonstrezi accesul la state și lockingul.

## Intrări necesare

[Lecția 15](./lectia-15.md) finalizată; cont AWS accesibil, metoda de plată înțeleasă și o regiune aleasă. În această lecție instalezi/configurezi AWS CLI și Terraform dacă lipsesc.

## Parcurs

1. **20 min — Identitate.** Activează MFA și evită root pentru operații zilnice. Configurează o identitate cu drepturi limitate; preferă sesiuni temporare prin IAM Identity Center/rol. Dacă exercițiul folosește un user IAM, documentează scopul și rotația cheilor. Verifică identitatea și regiunea active.
2. **15 min — Buget.** Creează alertă de buget, taguri de laborator și o estimare pentru EC2, RDS, ALB, DNS și stocare. Alerta poate întârzia și nu oprește automat resursele.
3. **25 min — Bootstrap.** Creează dintr-o configurație Terraform separată bucketul de state: nume unic, blocarea accesului public, criptare și versionare. Inițial state-ul bootstrapului este local; după creare, migrează-l controlat în backend și protejează copia locală.
4. **20 min — Remote și lock.** Configurează backendul S3 al mediului lab și lockingul nativ S3 dacă versiunea Terraform aleasă îl suportă. Verifică politica pentru fișierul de lock și demonstrează că două operații de scriere concurente nu rulează simultan. Nu crea DynamoDB doar din inerție.
5. **10 min — Cicluri de viață.** Separă explicit bootstrapul persistent de resursele care vor fi distruse la fiecare sesiune. Explică providers, resources, variables, outputs și sensibilitatea state-ului.

## Livrabile

- Identitate AWS verificată și alertă de buget.
- Backend S3 privat și versionat, cu locking testat.
- Două configurații separate: bootstrap și mediu temporar.

## Criterii de finalizare

- [ ] Nicio cheie AWS, copie de state sau plan binar nu este comisă.
- [ ] Backendul nu este inclus în destroy-ul zilnic al mediului.
- [ ] O operație concurentă este blocată sau eșuează explicit din cauza lockului.
- [ ] Știi că `sensitive` ascunde afișarea, dar nu elimină valoarea din state.
- [ ] Notezi costurile reziduale ale bucketului persistent.

## Dovadă și recapitulare

Păstrează configurația fără secrete și un log redactat al lockului. Nu folosi `force-unlock` cât timp o operație legitimă este activă.

**Navigare:** [Lecția precedentă](./lectia-15.md) · [Index](../README.md) · [Lecția următoare](./lectia-17.md)
