# Lecția 12 — OAuth2, TLS și evaluarea securității

**Săptămâna:** 4 · **Data de reper:** 20.12.2026 · **Durata:** 180 minute

## Obiective

- Protejezi API-ul ca OAuth2 resource server cu Keycloak.
- Validezi autentificarea, autorizarea, TLS și gestionarea secretelor.
- Închizi prima etapă cu un proiect demonstrabil și un profil profesional onest.

## Intrări necesare

[Lecția 11](./lectia-11.md) finalizată; acces de laborator pentru Keycloak și un nume de gazdă/certificat potrivit mediului.

## Parcurs

1. **35 min — Identity provider.** Pornește Keycloak cu stocare persistentă, creează realmul, rolurile de citire/scriere și utilizatorii de test. Pregătește clientul de test fără a publica secrete. Nu folosi contul administrator în fluxul aplicației.
2. **35 min — Resource server.** Configurează issuerul JWT, audiența API-ului și maparea rolurilor. Permite doar probele minime necesare fără autentificare; metricile și detaliile health rămân restricționate. Verifică 401 fără token, 403 cu drepturi insuficiente și succes cu drepturile corecte.
3. **30 min — TLS.** Activează HTTPS la un reverse proxy sau în serviciu și pe traseul de autentificare. Pentru CA de laborator configurează trustul explicit, fără dezactivarea verificării certificatului. Păstrează issuerul accesibil și consistent între clienți și API.
4. **25 min — Secrete și rotație.** Inventariază secretele din Git, Jenkins, Vault și gazdă. Rotește un credential de laborator și verifică refuzul celui vechi. Verifică istoricul repository-ului și rapoartele înainte de publicare.
5. **30 min — Evaluare.** Parcurge toate categoriile ediției OWASP Top 10 alese și notează pentru fiecare risc, control, dovadă sau limită explicită. Definește RTO/RPO ca obiective și descrie ce măsurători lipsesc înainte de a pretinde că sunt atinse.
6. **25 min — Portofoliu.** Actualizează arhitectura și instrucțiunile; publică repository-ul numai după verificarea secretelor și a drepturilor asupra conținutului. Actualizează CV-ul cu proiectul personal și pregătește poveștile pipeline, Ansible și dependență lentă.

## Livrabile

- API cu autentificare/autorizare și HTTPS verificabil.
- Inventar al secretelor și o rotație demonstrată.
- Evaluare OWASP, obiective RTO/RPO și checkpoint publicabil al proiectului.

## Criterii de finalizare

- [ ] Tokenurile cu issuer/audiență greșite sau expirate sunt respinse.
- [ ] Există probe distincte pentru 401, 403 și acces permis.
- [ ] Certificatul este verificat fără opțiuni de ocolire TLS.
- [ ] Niciun secret sau material de serviciu nu ajunge în repository-ul public.
- [ ] Deploy-ul Ansible și probele rămân funcționale după securizare.
- [ ] Cele trei povești au problemă, decizie și rezultat demonstrabil, nu experiență inventată.

## Dovadă și recapitulare

Păstrează numai rezultate HTTP redactate, fără tokenuri. Explică least privilege, rotația secretelor și diferența dintre RTO și RPO.

**Navigare:** [Lecția precedentă](./lectia-11.md) · [Index](../README.md) · [Lecția următoare](./lectia-13.md)
