# Lecția 11 — Health, shutdown și observabilitate

**Săptămâna:** 4 · **Data de reper:** 17.12.2026 · **Durata:** 90 minute

## Obiective

- Separi liveness de readiness.
- Adaugi graceful shutdown, metrici Micrometer și loguri structurate.
- Urmărești aceeași cerere printr-un correlation ID.

## Intrări necesare

[Lecția 10](./lectia-10.md) finalizată; aplicația poate fi oprită și repornită în laborator.

## Parcurs

1. **20 min — Actuator.** Activează explicit health probes și verifică endpointurile liveness/readiness. Nu include dependențe externe în liveness; decide separat ce dependențe sunt obligatorii pentru readiness.
2. **20 min — Graceful shutdown.** Configurează un timeout de oprire și verifică terminarea unei cereri aflate în lucru când procesul primește semnalul de oprire. Asigură un timeout systemd suficient pentru această fereastră.
3. **20 min — Metrici.** Expune controlat metricile Micrometer necesare: cereri, latență, erori și starea circuitului. Nu este obligatoriu un stack Prometheus/Grafana. Evită etichetele cu cardinalitate nelimitată.
4. **20 min — Loguri.** Activează un format structurat și propagă un correlation ID validat sau generat. Curăță contextul după cerere și nu loga tokenuri, parole ori corpuri sensibile.
5. **10 min — Incident controlat.** Oprește dependența auxiliară și urmărește logurile și metricile. Confirmă că aplicația nu intră într-o buclă de restart doar fiindcă un serviciu extern este indisponibil.

## Livrabile

- Endpointuri de sănătate distincte și graceful shutdown verificat.
- Exemplu de log structurat cu correlation ID.
- Set minim de metrici utile pentru diagnostic.

## Criterii de finalizare

- [ ] Liveness răspunde despre proces, nu despre disponibilitatea întregului ecosistem.
- [ ] Readiness reflectă regulile alese și documentate.
- [ ] Cererea în lucru se finalizează în fereastra de oprire.
- [ ] Același ID poate fi urmărit în logurile unei cereri.
- [ ] Endpointurile administrative nu sunt expuse fără restricții.

## Dovadă și recapitulare

Păstrează un log anonimizat și rezultatul probei de shutdown. Explică de ce un status HTTP 200 la liveness nu garantează că toate funcțiile de business sunt disponibile.

**Navigare:** [Lecția precedentă](./lectia-10.md) · [Index](../README.md) · [Lecția următoare](./lectia-12.md)
