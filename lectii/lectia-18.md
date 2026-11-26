# Lecția 18 — RDS, S3 și ciclul infrastructurii

**Săptămâna:** 6 · **Data de reper:** 03.01.2027 · **Durata:** 180 minute

## Obiective

- Adaugi PostgreSQL gestionat și un bucket de aplicație separat de state.
- Verifici conectivitatea EC2 → RDS fără expunerea bazei pe internet.
- Demonstrezi reproducibilitatea infrastructurii și cleanupul complet.

## Intrări necesare

[Lecția 17](./lectia-17.md) finalizată; configurația VPC/EC2 poate fi recreată din cod.

## Parcurs

1. **30 min — Resurse și secrete.** Definește RDS mic, Single-AZ pentru laborator, într-un DB subnet group privat pe două zone. Dezactivează accesul public și permite portul DB numai din security group-ul EC2. Preferă parola gestionată de serviciu; nu o pune în tfvars versionat.
2. **20 min — S3 pentru aplicație.** Creează un bucket distinct de backend, privat și criptat, cu o regulă simplă de ciclu de viață pentru datele de test. Pregătește un rol EC2 cu acces numai la resursele necesare, fără chei statice pe instanță.
3. **35 min — Apply.** Recreează rețeaua și instanța și așteaptă RDS. Inspectează outputurile și verifică din EC2 o conexiune PostgreSQL cu TLS conform configurației RDS. Instalează doar clientul necesar testului.
4. **25 min — Persistență și acces.** Creează date de laborator cu un user de aplicație limitat. Verifică accesul S3 permis și un acces în afara scopului refuzat. Nu afișa parola DB în consolă sau loguri.
5. **25 min — Reproducibilitate.** Rulează din nou plan: fără schimbări neintenționate. Explică modulele, dependențele dintre resurse și alegerea backendului separat de bucketul aplicației.
6. **45 min — Destroy și audit de cost.** Pentru datele exclusiv temporare alege explicit politica snapshoturilor RDS. Golește numai obiectele de test din bucketul aplicației, apoi distruge mediul. Verifică RDS, snapshoturi, volume, IP-uri și bucketuri reziduale; nu șterge state-ul.

## Livrabile

- Configurație Terraform pentru VPC, EC2, RDS și S3.
- Verificare de conexiune privată la DB și acces S3 limitat.
- Checklist de creare/distrugere reutilizabilă.

## Criterii de finalizare

- [ ] RDS nu este public și acceptă trafic doar din sursa autorizată.
- [ ] Bucketul aplicației nu este bucketul de state.
- [ ] Al doilea plan nu cere modificări neexplicate.
- [ ] Parolele nu apar în outputuri sau fișiere versionate.
- [ ] Destroy-ul este verificat în AWS; sunt inventariate costurile persistente.

## Dovadă și recapitulare

Păstrează diagrama, planul sumar redactat și auditul de cleanup. Nu extrapola acest destroy de laborator la date de producție.

**Navigare:** [Lecția precedentă](./lectia-17.md) · [Index](../README.md) · [Lecția următoare](./lectia-19.md)
