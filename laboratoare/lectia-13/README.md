# Lucru 13 — Instalare k3s pe Ubuntu în WSL 2

[Lecția](../../lectii/lectia-13.md) · [Mediul WSL](../README.md)

## Prerechizite

- [12 finalizată](../lectia-12/README.md), Docker funcțional, imagine proprie verificată și registry accesibil.
- `systemd` activ; minimumul k3s nu include aplicațiile. Păstrează RAM pentru DB, Keycloak și cel puțin două replici pentru lecția 15.
- Instalarea k3s modifică rețeaua și serviciile distribuției. Pe WSL partajat nu continua fără acord; dacă există deja cluster, nu instala peste el.
- Verifică [cerințele k3s](https://docs.k3s.io/installation/requirements), CIDR-urile folosite și porturile 6443/10250. Restricționează firewall-ul la laborator înainte de pornire; nu dezactiva firewall-ul.

## Instalare

Imaginea aplicației este construită și publicată de pipeline; autentificarea registry-ului rămâne în credentials. Un `docker login` din WSL nu autentifică automat runtime-ul k3s.

Pentru targetul de build local al Dockerfile-ului, copiază fișierele Gradle Wrapper și sursele din `backend/`, apoi rulează `./gradlew --no-daemon bootJar` cu JDK 21. Pentru imaginea de livrare, copiază în context un singur JAR verificat din `backend/build/libs/`, de exemplu ca `artifact/app.jar`. Exclude `build/` și `.gradle/` din context, nu și fișierul verificat. Nu copia JAR-ul `*-plain.jar` și nu recompila la promovare.

Pentru cluster, alege un release k3s exact și suportat din [release-uri](https://github.com/k3s-io/k3s/releases), de forma `v1.xx.y+k3s1`:

```bash
mkdir -p "$HOME/.cache/devops-downloads"
curl -fSLo "$HOME/.cache/devops-downloads/install-k3s.sh" https://get.k3s.io
code "$HOME/.cache/devops-downloads/install-k3s.sh"
```

După inspectare și verificarea firewall-ului:

```bash
read -r -p 'Versiune k3s exactă, inclusiv v și +k3s: ' K3S_VERSION
sudo -A env INSTALL_K3S_VERSION="${K3S_VERSION:?Introdu versiunea}" \
  sh "$HOME/.cache/devops-downloads/install-k3s.sh" \
  server --disable servicelb --write-kubeconfig-mode 600
sudo -A systemctl status k3s --no-pager
sudo -A k3s kubectl get nodes
```

Păstrăm Traefik, dar dezactivăm ServiceLB pentru a nu ocupa automat porturile 80/443 ale gazdei. În 14 accesăm Ingress prin port-forward. Acest lucru nu restricționează singur API-ul Kubernetes; firewall-ul rămâne necesar. Nu expune portul VXLAN 8472 în afara laboratorului.

Nu modifica un kubeconfig existent. Creează o copie dedicată, numai dacă fișierul destinație nu există:

```bash
mkdir -p "$HOME/.kube"
sudo -A install -m 600 -o "$(id -u)" -g "$(id -g)" \
  /etc/rancher/k3s/k3s.yaml "$HOME/.kube/devops-lab.yaml"
export KUBECONFIG="$HOME/.kube/devops-lab.yaml"
kubectl get nodes
kubectl get pods -A
```

Installerul furnizează `kubectl` dacă nu exista deja. Dacă ai un client existent, verifică compatibilitatea versiunii cu serverul. Kubeconfigul copiat este administrativ și conține secrete: nu îl comite și nu îl da joburilor Jenkins.

## Verificare și lucru

Nodul trebuie să fie `Ready`, iar componentele necesare sănătoase. După o repornire WSL verifică adresa nodului, serviciul și certificatul API; adresele pot fi schimbate. Pentru pauză, cu acord și numai pentru propriul cluster: `sudo -A systemctl stop k3s`. Nu folosi scriptul de dezinstalare pentru o simplă oprire.
