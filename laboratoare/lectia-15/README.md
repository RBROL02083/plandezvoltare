# Lucru 15 — Helm, metrici și identitatea Jenkins

[Lecția](../../lectii/lectia-15.md) · [Mediul WSL](../README.md)

## Prerechizite

- [14 finalizată](../lectia-14/README.md), aplicație și dependențe accesibile prin cluster.
- Spațiu CPU/RAM pentru minimum două replici, requests CPU configurate.
- Metrics Server, inclus de regulă în k3s, trebuie verificat, nu reinstalat automat.

## Instalare

### Helm în contul WSL

Exemplul fixează Helm 4.3.1, conform [ghidului oficial](https://helm.sh/docs/intro/install/). Dacă ai deja Helm, verifică versiunea și compatibilitatea înainte să schimbi ceva.

```bash
mkdir -p "$HOME/.cache/devops-downloads/helm" "$HOME/.local/bin"
cd "$HOME/.cache/devops-downloads/helm"
curl -fSLO https://get.helm.sh/helm-v4.3.1-linux-amd64.tar.gz
curl -fSLO https://get.helm.sh/helm-v4.3.1-linux-amd64.tar.gz.sha256sum
sha256sum -c helm-v4.3.1-linux-amd64.tar.gz.sha256sum
tar -xzf helm-v4.3.1-linux-amd64.tar.gz
install -m 755 linux-amd64/helm "$HOME/.local/bin/helm"
export PATH="$HOME/.local/bin:$PATH"
helm version
```

Continuă numai dacă verificarea SHA-256 reușește. Pentru o altă versiune înlocuiește coerent URL-urile și arhiva și verifică semnătura conform documentației.

### Metrici și CI

```bash
export KUBECONFIG="$HOME/.kube/devops-lab.yaml"
kubectl -n kube-system get deployment metrics-server
kubectl top nodes
kubectl top pods -A
```

Dacă metricile nu răspund, investighează serviciul și certificatele; nu adăuga automat `--kubelet-insecure-tls`. Pentru încărcare controlată poți folosi un mic client din instrumentele existente; nu este obligatorie instalarea unui stack de load testing.

În lecție creează o identitate Kubernetes dedicată Jenkins, cu RBAC limitat la namespace, și un kubeconfig separat. Nu copia kubeconfigul administrativ din 13 în Jenkins. Agentul nativ WSL poate ajunge la API-ul local, dar are nevoie de propriile credentials și PATH.

## Verificare și lucru

Chartul stă în `~/task-tracker/helm/task-tracker`. Rulează `helm lint` și `helm template` pe chartul creat, fără valori secrete în output. Pentru Helm 4 verifică opțiunile prin `helm upgrade --help`: rollbackul la eșec folosește `--rollback-on-failure`, nu copia orbește `--atomic` din exemple Helm 3. Configurează și așteptare/timeout.

Verifică scalarea efectivă, rolloutul, revenirea și smoke testul autentificat. Nu marca exercițiul complet doar pentru că Helm s-a instalat.
