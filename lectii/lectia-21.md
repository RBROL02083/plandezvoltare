# Lecția 21 — Lambda și sinteza serviciilor AWS

**Săptămâna:** 7 · **Data de reper:** 10.01.2027 · **Durata:** 180 minute

## Obiective

- Declanșezi o funcție Lambda printr-un upload S3.
- Înțelegi conceptual EKS, Managed Kafka și EFS, fără clustere costisitoare.
- Pregătești povestea de interviu despre controlul infrastructurii.

## Intrări necesare

[Lecția 20](./lectia-20.md) finalizată; acces AWS și backend Terraform funcțional. Pentru această lecție folosește o configurație temporară separată, cu cheie de state distinctă, numai pentru Lambda/S3; nu recrea inutil RDS și ALB.

## Parcurs

1. **35 min — Funcția.** Scrie un handler mic care extrage bucketul și cheia obiectului și scrie un log structurat. Alege un runtime suportat și familiar. Nu loga conținutul fișierelor sau date sensibile.
2. **30 min — Integrarea S3.** Definește în Terraform bucketul privat, rolul minim de execuție, permisiunea de invocare și notificarea filtrată după prefix/sufix. Nu scrie rezultatul în același prefix de intrare, pentru a evita buclele.
3. **30 min — Test și diagnostic.** Aplică prin fluxul cu aprobare și încarcă un obiect de test. Verifică execuția și logurile, inclusiv comportamentul la evenimente duplicate. S3 poate livra evenimente de mai multe ori; pentru acest handler doar de logare nu promite exact-once.
4. **25 min — EKS.** Citește modulul `terraform-aws-modules/eks/aws`, fixează versiunea consultată și desenează VPC, control plane, node groups, IAM și accesul clusterului. Separă resursele create de modul de cele furnizate de apelant. Nu crea un cluster.
5. **20 min — Kafka și stocare.** Explică rolul Amazon MSK, topicurilor, partițiilor și consumer groups. Compară EFS, EBS și S3 după modelul de acces, partajare, durabilitate și cost. Nu crea MSK sau EFS pentru a bifa conceptele.
6. **15 min — Interviu.** Scrie povestea Terraform: problemă, plan revizuit, aprobare, drepturi și rezultat. Menționează explicit ce ai implementat și ce ai studiat doar conceptual.
7. **25 min — Cleanup.** Șterge obiectele de test și distruge Lambda, bucketul temporar, notificările și rolurile dedicate. Verifică și log groups cu retenție/cost rezidual. Păstrează numai backendul necesar.

## Livrabile

- Funcție și trigger S3 definite din cod și testate.
- Diagramă EKS și tabel EFS/EBS/S3.
- A patra poveste de interviu și checklist de cleanup.

## Criterii de finalizare

- [ ] Uploadul produce o execuție observată, nu doar o configurație validă.
- [ ] Rolul funcției nu are acces general la toate serviciile.
- [ ] Nu există buclă S3 → Lambda → același trigger.
- [ ] EKS/MSK sunt marcate ca studiu conceptual, nu experiență operațională.
- [ ] Resursele și datele de test sunt curățate.

## Dovadă și recapitulare

Păstrează un eveniment redactat, un log și diagrama. Explică de ce serviciile gestionate reduc unele sarcini, dar nu elimină costurile și responsabilitățile de securitate.

**Navigare:** [Lecția precedentă](./lectia-20.md) · [Index](../README.md) · [Lecția următoare](./lectia-22.md)
