# Lecția 14 — Manifestele și rularea în Kubernetes

**Săptămâna:** 5 · **Data de reper:** 24.12.2026 · **Durata:** 90 minute

## Obiective

- Rulezi API-ul prin Deployment, Service și Ingress.
- Separi ConfigMap de Secret și configurezi probes și resurse.
- Verifici disponibilitatea și restricțiile de acces.

## Intrări necesare

[Lecția 13](./lectia-13.md) finalizată; imagine accesibilă k3s, PostgreSQL și Keycloak accesibile prin adrese rezolvabile din poduri, nu prin `localhost`.

## Parcurs

1. **20 min — Obiectele de bază.** Creează namespace, Deployment cu digestul imaginii și Service. Configurează accesul registry-ului privat printr-un secret separat, dacă este necesar.
2. **15 min — Configurație.** Pune setările nesensibile în ConfigMap și injectează secretele în afara Gitului. Reține că base64 nu înseamnă criptare și că RBAC/stocarea clusterului contează.
3. **20 min — Probes și resurse.** Configurează startup probe dacă pornirea o cere, apoi liveness/readiness pe endpointurile stabilite. Definește requests/limits CPU și memorie pe baza unei măsurători inițiale, nu arbitrar.
4. **20 min — Intrare și acces.** Configurează Ingress cu TLS și certificatul de laborator de încredere. Verifică issuerul OAuth2 și accesul la DB. Restricționează ServiceAccount/RBAC și testează o NetworkPolicy compatibilă cu pluginul de rețea.
5. **15 min — Verificare.** Simulează un pod care nu este ready și confirmă că nu primește trafic nou prin Service. Revino la starea sănătoasă și inspectează events, loguri și consumul.

## Livrabile

- Manifestele declarative ale aplicației.
- Configurație externă, probes, requests și limits.
- Acces HTTPS prin Ingress fără expunerea inutilă a administrării.

## Criterii de finalizare

- [ ] Aplicația funcționează prin Service și Ingress.
- [ ] Un pod not ready este eliminat din endpointurile eligibile pentru trafic.
- [ ] Aplicația are requests/limits și rulează non-root.
- [ ] Secretul nu apare în manifestele versionate.
- [ ] Politica de rețea este verificată prin acces permis și refuzat, nu doar aplicată.

## Dovadă și recapitulare

Păstrează statusul rolloutului și proba de readiness. Explică diferența dintre Service și Ingress, precum și rolul unui StatefulSet, doar conceptual.

**Navigare:** [Lecția precedentă](./lectia-13.md) · [Index](../README.md) · [Lecția următoare](./lectia-15.md)
