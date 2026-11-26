# Lucru 20 — Dependențe pentru deploy-ul AWS

[Lecția](../../lectii/lectia-20.md) · [Mediul WSL](../README.md)

## Prerechizite

- [19 finalizată](../lectia-19/README.md), AWS CLI/Terraform/Checkov și flux cu aprobare.
- Rolul Ansible din 09, artefactul și checksumul, cheia AWS și host keys verificate.
- **Issuer OAuth2 HTTPS accesibil din EC2**, cu DNS și trust funcționale. Keycloak disponibil numai la `localhost` în WSL nu îndeplinește această cerință.
- Alege înainte de apply varianta cu domeniu propriu/ACM sau varianta privată cu tunel din lecție.

## Instalare / configurare

Nu ai nevoie de „server ALB” sau „server Route 53” instalat în WSL; sunt servicii AWS create prin Terraform.

```bash
export PATH="$HOME/.local/bin:$PATH"
export AWS_PROFILE=devops-lab
export AWS_PAGER=""
ansible-playbook --version
terraform version
aws sts get-caller-identity
```

Dacă vrei verificări DNS din CLI și lipsește `dig`:

```bash
sudo -A apt-get update
sudo -A apt-get install dnsutils
```

Configurează inventory-ul AWS separat de cel WSL: adresa EC2, utilizatorul potrivit AMI-ului, portul 22 autorizat și cheia dedicată. Nu reutiliza `127.0.0.1:2222` pentru EC2.

### Dependențe care trebuie să fie realmente accesibile

- Dacă EC2 nu poate accesa Artifactory local, agentul WSL descarcă JAR-ul verificat și Ansible îl transferă prin SSH. Nu publica Artifactory pe internet.
- Pentru issuerul local ai nevoie de o cale TLS autorizată accesibilă din AWS sau de o instanță IdP separată, securizată, cu același contract configurat. Aceasta trebuie pregătită și bugetată explicit. Dacă lipsește, oprește exercițiul înainte de apply; nu dezactiva OAuth2.
- Clientul PostgreSQL necesar testelor se instalează pe EC2 conform 18, nu pe ALB.
- Zona privată Route 53 se verifică din VPC. Un `dig` obișnuit din WSL nu dovedește funcționarea DNS-ului privat.

## Verificare și lucru

Continuă configurația Terraform și rolul comune. Verifică target healthy, API autentificat prin ALB, TLS unde se aplică și checksumul JAR-ului. Un ALB creat fără aplicație accesibilă nu încheie lecția. Include ALB, zona DNS și eventualul IdP temporar în auditul costurilor/cleanupului.
