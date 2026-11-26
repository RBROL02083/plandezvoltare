# Lecția 08 — Instalare și serviciu systemd

**Săptămâna:** 3 · **Data de reper:** 10.12.2026 · **Durata:** 90 minute

## Obiective

- Descrii instalarea aplicației prin module Ansible.
- Rulezi serviciul cu un cont dedicat, fără privilegii administrative.
- Repornești aplicația numai când se schimbă configurația sau versiunea.

## Intrări necesare

[Lecția 07](./lectia-07.md) finalizată; un JAR verificat din Artifactory și versiunea sa explicită.

## Parcurs

1. **20 min — Starea gazdei.** Scrie taskuri pentru JDK-ul necesar, grupul și userul de serviciu fără login interactiv, directoare și permisiuni. Nu actualiza toate pachetele sistemului fără legătură cu lecția.
2. **25 min — Instalarea aplicației.** Descarcă versiunea explicită din Artifactory cu verificarea checksumului. Stochează credentials în afara codului și ascunde taskurile care le manipulează. Nu folosi shell pentru descărcare dacă un modul dedicat acoperă operația.
3. **20 min — systemd.** Creează unitul cu userul dedicat, directorul de lucru și politica de restart. Gestionează `daemon_reload`, enable/start și handlerul de restart.
4. **15 min — Validare.** Rulează playbook-ul, inspectează statusul systemd și logurile și verifică API-ul prin HTTP. Procesul „active” nu este suficient dacă endpointul nu răspunde.
5. **10 min — Idempotență inițială.** Rulează din nou fără modificări și explică orice `changed`. Elimină taskurile care provoacă restart inutil.

## Livrabile

- Playbook care instalează și pornește versiunea aleasă.
- Unit systemd gestionat de Ansible.
- Handler de restart declanșat de modificări reale.

## Criterii de finalizare

- [ ] Procesul Java rulează ca userul aplicației, nu ca root.
- [ ] Versiunea instalată corespunde artefactului verificat.
- [ ] Serviciul repornește corect după o schimbare controlată.
- [ ] A doua rulare nu descarcă și nu repornește inutil aplicația.

## Dovadă și recapitulare

Păstrează recapitularea celor două rulări și răspunsul API. Explică de ce un handler este preferabil unui restart necondiționat.

**Navigare:** [Lecția precedentă](./lectia-07.md) · [Index](../README.md) · [Lecția următoare](./lectia-09.md)
