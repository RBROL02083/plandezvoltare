# Lecția 22 — React, TypeScript și lista de taskuri

**Săptămâna:** 8 · **Data de reper:** 12.01.2027 · **Durata:** 90 minute

## Obiective

- Creezi UI-ul cu Vite, React și TypeScript.
- Afișezi date reale din API-ul deja securizat.
- Modelezi explicit încărcarea, lista goală și eroarea.

## Intrări necesare

[Lecția 21](./lectia-21.md) finalizată; API disponibil în laboratorul local, Keycloak funcțional și Node.js compatibil cu versiunea Vite aleasă. Nu este necesară recrearea AWS.

## Parcurs

1. **15 min — Inițializare.** Generează proiectul Vite React/TypeScript, fixează versiunea Node și salvează lockfile-ul. Rulează verificarea TypeScript și buildul inițial.
2. **20 min — Tipuri și componente.** Definește interfața Task conform API-ului și o componentă de listă cu props tipizate. Distinge tiparea statică de validarea răspunsului primit la runtime.
3. **25 min — Integrare.** Configurează un client public Keycloak pentru SPA și Authorization Code cu PKCE, fără client secret în frontend. Folosește o bibliotecă potrivită fluxului și configurează redirect URI/origin exact. Tokenul nu este comis sau pus în variabile de build publice.
4. **20 min — Fetch și state.** Încarcă lista prin `useEffect`, gestionează rezultatul cu `useState` și verifică `response.ok`. Tratează separat 401/403, eroarea de rețea și eroarea de server; anulează cererea când componenta se demontează.
5. **10 min — Verificare.** Testează cu listă populată, goală și API indisponibil. Configurează CORS numai pentru originile necesare sau folosește un proxy same-origin.

## Livrabile

- Proiect frontend tipizat, cu listă funcțională.
- Configurație de autentificare SPA fără secrete.
- Stări vizibile de loading, empty și error.

## Criterii de finalizare

- [ ] Lista vine din API, nu din date hardcodate.
- [ ] Accesul fără sesiune cere autentificare, nu dezactivează protecția backendului.
- [ ] Eșecul fetch nu apare ca listă goală „reușită”.
- [ ] Type-check și build trec fără anularea tipării prin `any`.
- [ ] Parolele/tokenurile nu sunt în bundle, Git sau logurile browserului.

## Dovadă și recapitulare

Păstrează capturi fără date sensibile pentru cele trei stări și rezultatul buildului. Explică props, state și rolul efectelor.

**Navigare:** [Lecția precedentă](./lectia-21.md) · [Index](../README.md) · [Lecția următoare](./lectia-23.md)
