# Lucru 14 — Rețea, Ingress și manifestele aplicației

[Lecția](../../lectii/lectia-14.md) · [Mediul WSL](../README.md)

## Prerechizite

- [13 finalizată](../lectia-13/README.md), cluster Ready și imagine disponibilă prin registry.
- DB, Keycloak, issuer HTTPS și CA accesibile din **poduri**, nu doar din terminalul WSL.
- Un nume de gazdă pentru Ingress și certificatul aferent din lecția 12.

## Instalare / configurare

Nu instala un al doilea cluster sau un alt Ingress controller. Folosește kubectl și Traefik deja instalate:

```bash
export KUBECONFIG="$HOME/.kube/devops-lab.yaml"
kubectl get nodes
kubectl -n kube-system get deployment traefik
kubectl -n kube-system get service traefik
```

### Accesul la serviciile externe clusterului

Containerele DB și Keycloak din lecțiile anterioare sunt publicate pe loopback pentru siguranță. Podurile nu pot folosi acele adrese. Înainte de deploy:

1. Alege o interfață/adresă internă de laborator pe WSL și verifică rutarea din cluster. Nu copia o adresă presupusă dintr-un exemplu.
2. Configurează publicarea serviciilor numai pe acea adresă, cu firewall care permite sursele necesare clusterului. Păstrează accesul local pentru joburile existente. Nu înlocui pur și simplu `127.0.0.1` cu `0.0.0.0`.
3. Configurează rezoluția issuerului canonic în Windows, WSL și DNS-ul clusterului, către endpointul TLS accesibil. Montează CA în JVM-ul aplicației dacă este privată.
4. Actualizează JDBC, issuerul și redirect URI-urile fără a schimba contractul API. Testează DNS, TCP și HTTPS dintr-un pod de diagnostic cu imagine fixată, apoi elimină numai acel pod.

Alternativ, poți muta serviciile în cluster, dar aceasta cere manifestele și migrarea datelor; simplul `kubectl apply` al aplicației nu le mută automat. Nu continua cu adrese `localhost` care ascund problema.

### Ingress local fără ocuparea porturilor gazdei

După crearea manifestelor din lecție și verificarea Service-ului Traefik, într-un terminal dedicat:

```bash
kubectl -n kube-system port-forward --address 127.0.0.1 \
  service/traefik 8880:80 8444:443
```

Folosește `https://numele-din-certificat:8444` cu rezoluție locală potrivită. `curl --resolve` poate testa numele către loopback fără să schimbi DNS global; păstrează verificarea CA. Port-forward funcționează numai cât procesul rămâne pornit.

## Verificare și lucru

Manifestele stau în `~/task-tracker/k8s`. Creează namespace-ul și secretele prin mecanismul ales în lecție, nu prin valori în Git. Testează rollout, readiness, acces DB/Keycloak și NetworkPolicy în ambele sensuri. Un manifest valid fără accesul dependențelor nu înseamnă deploy reușit.
