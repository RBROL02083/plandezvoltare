# Lecția 17 — VPC și instanța EC2

**Săptămâna:** 6 · **Data de reper:** 31.12.2026 · **Durata:** 90 minute

## Obiective

- Creezi un modul de rețea cu subnets publice/private și route tables.
- Lansezi o instanță EC2 cu acces administrativ restrâns.
- Închei sesiunea prin distrugerea infrastructurii temporare.

## Intrări necesare

[Lecția 16](./lectia-16.md) finalizată; backend remote și identitate autorizată pentru resursele laboratorului.

## Parcurs

1. **25 min — Modulul VPC.** Definește CIDR, subnets publice în două zone și subnets private în două zone pentru RDS. Asociază route tables: ruta prin Internet Gateway este doar pentru subnets publice. Evită NAT Gateway în acest laborator pentru a limita costurile.
2. **20 min — Security groups și EC2.** Creează o instanță mică compatibilă cu aplicația. Pentru varianta simplă folosește subnet public, SSH doar din IP-ul stației/agentului autorizat și cheia dedicată; portul aplicației nu este deschis global.
3. **20 min — Plan și apply.** Rulează fmt, init, validate, plan și inspectează resursele înainte de apply. Exportă numai identificatorii și adresele necesare. Verifică rutarea și accesul permis/refuzat.
4. **10 min — Drift și workspaces.** Schimbă controlat un tag de laborator în consolă, observă diferența prin plan și readu configurația dorită. Explică de ce workspaces nu sunt o graniță suficientă de securitate între conturi.
5. **15 min — Cleanup.** Rulează plan de destroy și destroy pentru mediul temporar. Verifică în state și AWS că instanța și resursele asociate au dispărut. Backendul rămâne.

## Livrabile

- Modul VPC și configurație EC2 versionate.
- Outputuri fără secrete.
- Dovadă pentru apply, drift și destroy.

## Criterii de finalizare

- [ ] Subnets private nu au rută directă spre Internet Gateway.
- [ ] SSH nu este permis din `0.0.0.0/0`.
- [ ] O modificare externă apare în plan și este reconciliată.
- [ ] Resursele temporare sunt distruse la final, nu doar oprite.
- [ ] State-ul remote rămâne accesibil.

## Dovadă și recapitulare

Păstrează o diagramă simplă a rețelei și sumarul destroy. Explică diferența dintre route table și security group.

**Navigare:** [Lecția precedentă](./lectia-16.md) · [Index](../README.md) · [Lecția următoare](./lectia-18.md)
