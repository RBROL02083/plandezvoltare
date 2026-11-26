# Lecția 07 — Ansible: inventory și acces

**Săptămâna:** 3 · **Data de reper:** 08.12.2026 · **Durata:** 90 minute

## Obiective

- Separi nodul de control de serverul administrat.
- Configurezi inventory, SSH și variabilele de grup.
- Rulezi comenzi ad-hoc înainte de a scrie un playbook.

## Intrări necesare

[Lecția 06](./lectia-06.md) finalizată; server Ubuntu de laborator și un utilizator SSH dedicat. Nu folosi servere de serviciu.

## Parcurs

1. **30 min — Lectură.** Analizează un playbook public: hosts, tasks, modules, vars și handlers. Compară starea dorită cu un script care execută mereu aceleași comenzi.
2. **20 min — Instalare și inventory.** Instalează o versiune fixată de Ansible pe nodul de control. Creează grupul `lab`, adresa serverului și utilizatorul de conectare. Pune configurația comună în `group_vars`.
3. **20 min — Acces SSH.** Configurează o cheie dedicată și verificarea host key pe baza amprentei confirmate printr-un canal de încredere. Testează conectivitatea cu modulul `ping` și inspectează facts. Nu dezactiva global verificarea cheilor gazdelor.
4. **10 min — Comenzi ad-hoc.** Verifică spațiul, versiunea sistemului și existența utilizatorului de serviciu. Folosește module dedicate când există, nu shell pentru orice operație.
5. **10 min — Drepturi.** Testează o operație care necesită `become` și documentează privilegiile necesare instalării. Planifică separarea accesului administrativ de deploy-ul curent.

## Livrabile

- Inventory de laborator și variabile de grup fără secrete.
- Conectivitate Ansible verificată.
- Listă minimă a privilegiilor necesare.

## Criterii de finalizare

- [ ] Ansible identifică serverul corect și întoarce facts.
- [ ] Cheia privată nu este în repository.
- [ ] O amprentă SSH neașteptată produce investigație, nu ocolirea controlului.
- [ ] Explici diferența dintre inventory, facts și `group_vars`.

## Dovadă și recapitulare

Păstrează ieșirea anonimizată pentru ping și identificarea sistemului. Explică de ce „ping Ansible” nu este un simplu ping ICMP.

**Navigare:** [Lecția precedentă](./lectia-06.md) · [Index](../README.md) · [Lecția următoare](./lectia-08.md)
