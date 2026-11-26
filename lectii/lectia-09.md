# Lecția 09 — Rol, Vault și deploy din Jenkins

**Săptămâna:** 3 · **Data de reper:** 13.12.2026 · **Durata:** 180 minute

## Obiective

- Transformi playbook-ul într-un rol reutilizabil.
- Protejezi configurația bazei de date prin Ansible Vault.
- Execuți deploy-ul din Jenkins și dovedești idempotența.

## Intrări necesare

[Lecția 08](./lectia-08.md) finalizată; acces la Artifactory și un PostgreSQL de laborator, pornit în această lecție dacă nu există deja.

## Parcurs

1. **30 min — Rol și variabile.** Mută taskurile, defaults și handlers într-un rol. Separă configurația mediului de logica instalării și parametrizează versiunea artefactului.
2. **35 min — Configurație și bază de date.** Pornește PostgreSQL de laborator cu date persistente și user de aplicație limitat. Creează template-ul Jinja2 pentru `application.yml`; înlocuiește baza în memorie prin configurație externă, păstrând contractul API.
3. **30 min — Vault.** Criptează parola bazei în fișierul de variabile. Cheia/parola Vault vine dintr-un credential Jenkins, nu din repository. Aplică permisiuni restrictive fișierelor de configurare și protejează taskurile sensibile de log și diff.
4. **35 min — Deploy în CI.** Adaugă stage-ul care primește versiunea publicată, descarcă artefactul verificat și rulează rolul cu SSH credentials și host keys verificate. Execută un smoke test și raportează explicit eșecul.
5. **30 min — Idempotență și check mode.** Rulează de două ori pe aceeași versiune: a doua execuție trebuie să aibă `changed=0` și `failed=0`. Explorează `--check`, tags și limitele modulelor în check mode; nu afișa diff pentru secrete.
6. **20 min — Recuperare.** Reinstalează o versiune anterioară cunoscută ca bună și verifică API-ul. Explică `serial` pentru rollout pe mai multe gazde; pe un singur server nu poți demonstra disponibilitate continuă.

## Livrabile

- Rol reutilizabil, template și variabile secrete criptate.
- Deploy Jenkins → Artifactory → Ubuntu → systemd.
- Procedură scurtă de revenire la o versiune anterioară.

## Criterii de finalizare

- [ ] Datele persistă după restartul aplicației.
- [ ] Fișierele din Git nu conțin parola DB sau parola Vault în clar.
- [ ] Aceeași versiune produce `changed=0` la a doua execuție, fără a ascunde schimbările.
- [ ] Un smoke test eșuat face deploy-ul eșuat în Jenkins.
- [ ] Revenirea folosește artefactul anterior, nu un rebuild.

## Dovadă și recapitulare

Păstrează cele două recapitulări și identificatorul versiunii. Construiește povestea „deploy Ansible idempotent”: problemă, decizie, rezultat măsurat.

**Navigare:** [Lecția precedentă](./lectia-08.md) · [Index](../README.md) · [Lecția următoare](./lectia-10.md)
