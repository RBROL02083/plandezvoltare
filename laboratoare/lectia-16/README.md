# Lucru 16 — AWS CLI v2 și Terraform în WSL

[Lecția](../../lectii/lectia-16.md) · [Mediul WSL](../README.md)

## Prerechizite

- [15 finalizată](../lectia-15/README.md), cont AWS autorizat, MFA, regiune și metodă de plată înțelese.
- Contul poate genera costuri; nu crea resurse înainte de estimare și alertă de buget.
- curl/unzip există. Folosim executabile **Linux x86_64**, nu variante Windows.

## Instalare

### AWS CLI v2 în contul utilizatorului

Alege o versiune v2 exactă din [changelog](https://raw.githubusercontent.com/aws/aws-cli/v2/CHANGELOG.rst). Urmează [verificarea semnăturii AWS](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) și importă cheia publică oficială cu amprenta confirmată acolo înainte de `gpg --verify`.

```bash
mkdir -p "$HOME/.cache/devops-downloads/aws" "$HOME/.local/bin"
cd "$HOME/.cache/devops-downloads/aws"
read -r -p 'Versiune AWS CLI v2 exactă, fără v: ' AWS_CLI_VERSION
curl -fSLo awscliv2.zip \
  "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${AWS_CLI_VERSION:?Introdu versiunea}.zip"
curl -fSLo awscliv2.zip.sig \
  "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${AWS_CLI_VERSION}.zip.sig"
gpg --verify awscliv2.zip.sig awscliv2.zip
```

Numai după verificare reușită și dacă nu există deja instalarea:

```bash
unzip awscliv2.zip
./aws/install --install-dir "$HOME/.local/aws-cli" --bin-dir "$HOME/.local/bin"
export PATH="$HOME/.local/bin:$PATH"
export AWS_PAGER=""
aws --version
```

La actualizare folosește instrucțiunile AWS și directoarele instalării existente; nu instala peste fișierele altui utilizator.

### Terraform

Exemplu fixat la 1.17.2, conform [distribuției oficiale](https://developer.hashicorp.com/terraform/install), cu locking S3 disponibil:

```bash
mkdir -p "$HOME/.cache/devops-downloads/terraform" "$HOME/.local/bin"
cd "$HOME/.cache/devops-downloads/terraform"
curl -fSLO https://releases.hashicorp.com/terraform/1.17.2/terraform_1.17.2_linux_amd64.zip
curl -fSLO https://releases.hashicorp.com/terraform/1.17.2/terraform_1.17.2_SHA256SUMS
grep 'terraform_1.17.2_linux_amd64.zip$' terraform_1.17.2_SHA256SUMS | sha256sum -c -
```

Continuă numai dacă verificarea are rezultat `OK`. Pentru verificarea autenticității checksumurilor urmează și [verificarea semnăturii HashiCorp](https://developer.hashicorp.com/well-architected-framework/operational-excellence/verify-hashicorp-binary).

```bash
unzip terraform_1.17.2_linux_amd64.zip
install -m 755 terraform "$HOME/.local/bin/terraform"
export PATH="$HOME/.local/bin:$PATH"
terraform version
```

## Configurare și verificare

Preferă un profil cu sesiuni temporare:

```bash
aws configure sso --profile devops-lab
aws sso login --profile devops-lab
export AWS_PROFILE=devops-lab
export AWS_PAGER=""
aws sts get-caller-identity
aws configure get region --profile devops-lab
```

SSO cere o organizație/instanță IAM Identity Center configurată, nu apare automat într-un cont nou. Dacă folosești alt mecanism autorizat, configurează-l conform lecției fără chei în repository. Verifică explicit contul și regiunea înainte de fiecare sesiune.

Creează configurațiile în `~/task-tracker/terraform/bootstrap` și `terraform/environments/lab`. Fixează `required_version` și providerul, păstrează `.terraform.lock.hcl`, exclude `.terraform`, state, planuri și fișierele cu secrete în `.gitignore` al **aplicației**. Backendul S3 și lockingul se configurează în lecție; instalarea CLI nu le creează.
