# Lecția 13 — Docker multi-stage și k3s

**Săptămâna:** 5 · **Data de reper:** 22.12.2026 · **Durata:** 90 minute

## Obiective

- Întărești imaginea inițială fără să schimbi aplicația.
- Rulezi containerul ca utilizator non-root.
- Instalezi un cluster k3s de laborator și verifici accesul.

## Intrări necesare

[Lecția 12](./lectia-12.md) finalizată; server Ubuntu cu resurse și porturi disponibile. Redu serviciile inactive înainte de pornirea clusterului.

## Parcurs

1. **30 min — Multi-stage.** Creează un target de build cu Gradle Wrapper (`./gradlew --no-daemon bootJar`) și unul runtime minimal. Pentru livrare, targetul runtime primește JAR-ul executabil din `build/libs/` deja verificat; targetul de build rămâne util pentru compilare locală reproductibilă. Nu reconstrui alt JAR după quality gates.
2. **15 min — Execuție sigură.** Adaugă user non-root, directoare cu permisiuni minime și `.dockerignore`. Verifică pornirea, propagarea semnalelor și health endpoints fără a include secrete în straturi.
3. **15 min — Scanare și registry.** Repetă Trivy și publică imaginea într-un registry accesibil clusterului, cu tag imutabil/digest. Registry-ul poate fi cel GitHub pentru proiectul propriu; nu presupune că ediția Artifactory aleasă include suport Docker.
4. **20 min — k3s.** Instalează o versiune fixată conform documentației oficiale, după inspectarea instrucțiunilor. Protejează kubeconfigul și verifică nodul, DNS-ul și componentele de sistem.
5. **10 min — Model mental.** Identifică control plane, nod, pod și namespace. Notează că un cluster single-node nu oferă disponibilitate ridicată la pierderea gazdei.

## Livrabile

- Dockerfile multi-stage și imagine non-root scanată.
- Imagine disponibilă clusterului printr-un registry.
- k3s funcțional și kubeconfig protejat.

## Criterii de finalizare

- [ ] UID-ul procesului aplicației nu este 0.
- [ ] Imaginea nu conține chei, tokenuri sau configurații secrete.
- [ ] JAR-ul din imagine corespunde celui verificat/publicat.
- [ ] Nodul este `Ready`, iar podurile de sistem necesare sunt funcționale.
- [ ] Accesul administrativ Kubernetes nu este public sau comis în Git.

## Dovadă și recapitulare

Păstrează digestul imaginii, UID-ul procesului și statusul nodului. Explică diferența dintre imagine, container și pod.

**Navigare:** [Lecția precedentă](./lectia-12.md) · [Index](../README.md) · [Lecția următoare](./lectia-14.md)
