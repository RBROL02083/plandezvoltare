# Lucru 19 — Checkov și unelte Terraform pe agent

[Lecția](../../lectii/lectia-19.md) · [Mediul WSL](../README.md)

## Prerechizite

- [18 finalizată](../lectia-18/README.md), AWS CLI și Terraform din 16.
- Agentul nativ `wsl`, uv și Python izolat din 07.
- Identitate AWS de CI cu drepturi limitate; autentificarea SSO interactivă a utilizatorului nu este o soluție automată permanentă pentru Jenkins.

## Instalare

Instalează [Checkov](https://www.checkov.io/2.Basics/Installing%20Checkov.html) separat de Python-ul de sistem și de mediul Ansible. Reperul verificat la redactare este 3.4.11:

```bash
export PATH="$HOME/.local/bin:$HOME/.local/share/devops-lab/uv/bin:$PATH"
uv tool install --python 3.13 'checkov==3.4.11'
checkov --version
terraform version
aws --version
```

Nu folosi `sudo pip`. Dacă uv lipsește, urmează [07](../lectia-07/README.md), nu instala o a doua copie Ansible.

În configurația agentului Jenkins:

1. Asigură PATH pentru cele trei CLI-uri și verifică versiunile prin job.
2. Configurează rolul/sesiunea AWS sau credentialul limitat din lecție. Un plugin AWS Jenkins nu este obligatoriu dacă folosești CLI cu bindingul de credential potrivit.
3. Configurează accesul la backend și fișierul de lock; nu da administrator pentru a evita o eroare IAM.
4. Configurează stocarea privată a planului, aprobarea autorizată și blocarea execuțiilor concurente.

## Verificare și lucru

Din repository-ul aplicației:

```bash
cd "$HOME/task-tracker"
checkov -d terraform/environments/lab --framework terraform
```

Rulează scanarea și pe fixture-ul nesigur **care nu va fi aplicat**. Nu activa opțiuni de upload al codului sau integrări externe neautorizate. Un exit code nenul trebuie să blocheze fluxul conform politicii.

Continuă Jenkinsfile-ul comun. `terraform plan -detailed-exitcode` întoarce `2` pentru schimbări, `1` pentru eroare și `0` fără schimbări; tratează-le distinct. Aplică exact planul aprobat și verifică destroy-ul.
