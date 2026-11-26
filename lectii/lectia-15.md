# Lecția 15 — HPA, Helm și livrare din Jenkins

**Săptămâna:** 5 · **Data de reper:** 27.12.2026 · **Durata:** 180 minute

## Obiective

- Observi scalarea efectivă prin HPA.
- Execuți rolling update și rollback.
- Împachetezi aplicația într-un chart Helm și o livrezi din Jenkins.

## Intrări necesare

[Lecția 14](./lectia-14.md) finalizată; capacitate pentru cel puțin două replici și metrici Kubernetes funcționale.

## Parcurs

1. **35 min — HPA.** Verifică Metrics Server și metricile podurilor. Configurează scalarea CPU cu requests corecte și un interval mic de replici. Generează încărcare controlată și urmărește creșterea, apoi revenirea după fereastra de stabilizare.
2. **20 min — PDB.** Configurează un PodDisruptionBudget compatibil cu numărul de replici și discută ce evacuări voluntare limitează. Nu presupune că PDB protejează împotriva căderii nodului sau controlează strategia Deployment.
3. **30 min — Update și rollback.** Publică o versiune vizibil diferită, urmărește rolloutul și testează API-ul. Introdu controlat o imagine invalidă, observă eșecul și revino la versiunea bună.
4. **40 min — Helm.** Extrage manifestele într-un chart cu values pentru imagine, host, probes și resurse. Rulează lint și template. Nu introduce valorile secretelor în values versionate sau în logurile de randare.
5. **35 min — Jenkins.** Adaugă deploy Helm numai pentru branchul de încredere, folosind identitate Kubernetes limitată la namespace. Așteaptă finalizarea cu timeout și comportament de rollback la eșec, apoi rulează un smoke test autentificat.
6. **20 min — Consolidare.** Verifică o instalare/actualizare repetată și documentează diferența dintre rollbackul aplicației și migrarea datelor. Nu pretinde că Helm inversează automat modificări de schemă.

## Livrabile

- HPA, PDB și chart Helm.
- Deploy Jenkins cu verificare de rollout și smoke test.
- Procedură de rollback și dovadă de scalare.

## Criterii de finalizare

- [ ] HPA observă metrici și modifică efectiv numărul de replici sub încărcare.
- [ ] Replicile suplimentare devin ready, nu rămân toate pending.
- [ ] Update-ul reușit și rollbackul după eșec sunt demonstrate.
- [ ] Jenkins folosește permisiuni limitate, nu kubeconfigul administratorului.
- [ ] Un rollout nereușit nu produce un build verde.

## Dovadă și recapitulare

Păstrează evoluția HPA, istoricul release-ului și buildul de deploy. Explică limitele single-node, RBAC și NetworkPolicy.

**Navigare:** [Lecția precedentă](./lectia-14.md) · [Index](../README.md) · [Lecția următoare](./lectia-16.md)
