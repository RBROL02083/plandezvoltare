# Lecția 20 — ALB, Route 53 și deploy în AWS

**Săptămâna:** 7 · **Data de reper:** 07.01.2027 · **Durata:** 90 minute

## Obiective

- Pui aplicația pe EC2 în spatele unui Application Load Balancer.
- Reutilizezi rolul Ansible și artefactul verificat de Jenkins.
- Creezi un record Route 53 fără a impune cumpărarea unui domeniu.

## Intrări necesare

[Lecția 19](./lectia-19.md) finalizată; rolul din [lecția 09](./lectia-09.md), artefact publicat și configurația AWS completă. Rezervă timp pentru timpii de creare RDS/ALB; dacă aceștia depășesc sesiunea, nu declara deploy-ul verificat.

## Parcurs

1. **20 min — ALB și rutare.** Adaugă ALB în cele două subnets publice, target group și health check pe readiness. Permite traficul de aplicație către EC2 numai din security group-ul ALB; păstrează administrarea restrânsă.
2. **20 min — Plan și apply.** Folosește pipeline-ul cu aprobare pentru recrearea mediului. În timp ce AWS creează resursele, pregătește variabilele Ansible din outputuri: gazdă, endpoint RDS și versiune JAR, fără parole în outputuri.
3. **25 min — Deploy real.** Din Jenkins rulează rolul pe EC2, configurează DB și issuerul OAuth2 accesibil din AWS și pornește serviciul. Dacă Artifactory de acasă nu este accesibil, agentul Jenkins descarcă artefactul și îl transferă prin canalul SSH autorizat; nu expune Artifactory public pentru exercițiu.
4. **15 min — DNS și test.** Cu domeniu propriu, folosește alias A spre ALB și certificat ACM pentru HTTPS. Fără domeniu, creează o zonă privată de laborator și verifică aliasul din VPC; testează API-ul prin tunel SSH către ALB, fără a expune HTTP public. Șterge zona după exercițiu: și aceasta poate costa.
5. **10 min — Cleanup.** Pornește destroy suficient de devreme, apoi verifică terminarea și absența resurselor temporare. Include ALB, recordurile și zona creată pentru laborator; nu doar EC2.

## Livrabile

- ALB cu target sănătos și record Route 53.
- Deploy Jenkins al aceleiași versiuni folosite în laboratorul local.
- Dovadă de acces prin ALB și de cleanup.

## Criterii de finalizare

- [ ] Targetul este healthy; simpla creare a ALB nu închide lecția.
- [ ] API-ul răspunde prin ALB și păstrează autentificarea.
- [ ] RDS rămâne privat, iar aplicația nu este expusă direct global.
- [ ] DNS-ul este verificat în contextul potrivit: public sau din VPC.
- [ ] Certificatul TLS nu este ocolit în varianta publică.
- [ ] Nu rămân resurse temporare facturabile neinventariate.

## Dovadă și recapitulare

Păstrează versiunea, checksumul, sănătatea targetului și răspunsul API redactat. Explică diferența dintre rezoluție DNS, health check și disponibilitatea funcției de business.

**Navigare:** [Lecția precedentă](./lectia-19.md) · [Index](../README.md) · [Lecția următoare](./lectia-21.md)
