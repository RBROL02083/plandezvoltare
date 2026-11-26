# Lucru 17 — Pregătire VPC și EC2

[Lecția](../../lectii/lectia-17.md) · [Mediul WSL](../README.md)

## Prerechizite

- [16 finalizată](../lectia-16/README.md): AWS CLI/Terraform instalate, identitate verificată și backend privat cu lock.
- Estimare de cost, acces pentru resursele lecției și CIDR-uri care nu se suprapun neintenționat cu laboratorul.
- Cheie SSH de laborator AWS separată de cheia pentru WSL.

## Instalare / configurare

Nu este necesar un pachet Ubuntu nou:

```bash
export PATH="$HOME/.local/bin:$PATH"
export AWS_PROFILE=devops-lab
export AWS_PAGER=""
aws sts get-caller-identity
terraform version
ssh -V
```

Numai dacă nu există deja cheia dedicată:

```bash
ssh-keygen -t ed25519 -f "$HOME/.ssh/devops-aws" -C devops-aws-lab
```

Importă în AWS numai cheia publică, în regiunea aleasă, prin configurația Terraform. Cheia privată rămâne locală sau în Jenkins Credentials.

Din `~/task-tracker/terraform/environments/lab`, după scrierea configurației:

```bash
terraform init
terraform fmt -check -recursive
terraform validate
terraform plan
```

`init` instalează providerii conform configurației/lockfile-ului. Nu rulează `apply` și nu validează singur toate permisiunile AWS. Nu este necesar un plugin AWS separat pentru VS Code ca să rulezi CLI.

## Verificare și lucru

Confirmă IP-ul **public de ieșire** al stației/agentului printr-o sursă autorizată; IP-ul privat WSL nu este sursa văzută de EC2. Restrânge SSH la acel `/32`, fără `0.0.0.0/0`, și verifică amprenta gazdei printr-un canal de încredere înainte de conectare.

Urmează apply/drift/destroy din lecție, numai în configurația temporară. Backendul bootstrap rămâne; distrugerea nu se execută din folderul lui.
