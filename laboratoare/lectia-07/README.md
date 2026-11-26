# Lucru 07 — Ansible și SSH în WSL

[Lecția](../../lectii/lectia-07.md) · [Mediul WSL](../README.md)

## Prerechizite

- [06 finalizată](../lectia-06/README.md), `systemd` activ și acord pentru instalarea serviciului SSH.
- WSL ține loc atât de nod de control, cât și de gazdă administrată, printr-o conexiune SSH reală către loopback. Este un laborator pe o singură gazdă, nu două servere independente.
- Python 3.10 din Ubuntu rămâne nemodificat. `ansible-core` 2.22.0 cere Python ≥ 3.13 pe nodul de control; folosim un Python separat gestionat de uv.

## Instalare

### Python izolat și Ansible

Instalează [uv din PyPI](https://docs.astral.sh/uv/getting-started/installation/#pypi) într-un mediu virtual personal, numai dacă lipsește. Evităm instalarea globală și scriptul standalone: URL-urile sale de descărcare au întors HTTP 403 la verificarea din acest mediu.

```bash
sudo -A apt-get update
sudo -A apt-get install python3-venv
mkdir -p "$HOME/.local/share/devops-lab" "$HOME/.local/bin"
python3 -m venv "$HOME/.local/share/devops-lab/uv"
"$HOME/.local/share/devops-lab/uv/bin/python" -m pip install 'uv==0.13.10'
export PATH="$HOME/.local/bin:$HOME/.local/share/devops-lab/uv/bin:$PATH"
uv --version
```

Instalează apoi runtime-ul și Ansible în medii separate:

```bash
uv python install 3.13
uv tool install --python 3.13 'ansible-core==2.22.0'
ansible --version
ansible-playbook --version
ansible-vault --version
```

Notează versiunea patch Python instalată și folosește-o la reproducere. [Matricea oficială](https://docs.ansible.com/projects/ansible/latest/reference_appendices/release_and_maintenance.html#ansible-core-support-matrix) pentru 2.22 acceptă Python 3.13–3.15 pe controller și 3.9–3.15 pe țintă, deci Python 3.10 al țintei Ubuntu este potrivit. Descărcarea Pythonului gestionat cere acces la distribuțiile indicate de uv; dacă politica rețelei o blochează, solicită un runtime Python 3.13 aprobat și indică executabilul lui prin `uv tool install --python /cale/python3.13`, fără a ocoli politica. Nu instala Ansible cu `sudo pip` și nu schimba `/usr/bin/python3`.

### SSH numai pentru laborator

Verifică întâi că nu există o instalare SSH folosită de altcineva. Nu aplica următorii pași peste un server partajat existent. Instalează cu acord și configurează imediat ascultarea locală:

```bash
sudo -A apt-get update
sudo -A apt-get install openssh-server
sudo -A adduser devops-deploy
sudo -A -e /etc/ssh/sshd_config.d/60-devops-lab.conf
```

În fișierul nou pune:

```text
Port 2222
ListenAddress 127.0.0.1
PermitRootLogin no
PasswordAuthentication no
KbdInteractiveAuthentication no
AllowUsers devops-deploy
```

Contul și parola se creează local, nu se transmit în chat sau în fișiere. Pentru bootstrapul interactiv din 08, un administrator poate acorda temporar apartenența la `sudo` prin `sudo -A usermod -aG sudo devops-deploy`. Folosește `--ask-become-pass`, apoi înlocuiește accesul larg cu drepturile limitate cerute de deploy; nu folosi `NOPASSWD: ALL`.

Generează o cheie **nouă**, fără să suprascrii una existentă, și instalează cheia publică numai pentru noul cont:

```bash
mkdir -p "$HOME/.ssh"
chmod 700 "$HOME/.ssh"
ssh-keygen -t ed25519 -f "$HOME/.ssh/devops-lab" -C devops-wsl-lab
sudo -A install -d -m 700 -o devops-deploy -g devops-deploy /home/devops-deploy/.ssh
sudo -A install -m 600 -o devops-deploy -g devops-deploy \
  "$HOME/.ssh/devops-lab.pub" /home/devops-deploy/.ssh/authorized_keys
sudo -A /usr/sbin/sshd -t
sudo -A systemctl enable --now ssh
sudo -A systemctl restart ssh
sudo -A ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
ssh -p 2222 -i "$HOME/.ssh/devops-lab" devops-deploy@127.0.0.1
```

Compară amprenta afișată la prima conectare cu cea citită local mai sus. Nu folosi `StrictHostKeyChecking=no`. Folosește o cheie cu parolă și `ssh-agent` pentru lucrul interactiv; Jenkins o primește ulterior ca credential.

## Configurare și verificare

În repository-ul comun creează `ansible/inventory.ini`:

```ini
[lab]
wsl-lab ansible_host=127.0.0.1 ansible_port=2222 ansible_user=devops-deploy ansible_python_interpreter=/usr/bin/python3
```

```bash
cd "$HOME/task-tracker"
ansible -i ansible/inventory.ini lab --private-key "$HOME/.ssh/devops-lab" -m ansible.builtin.ping
ansible -i ansible/inventory.ini lab --private-key "$HOME/.ssh/devops-lab" -m ansible.builtin.setup
```

`pong` și facts despre Ubuntu confirmă accesul. Agentul Jenkins ales în 01 rulează nativ în aceeași distribuție, deci poate folosi acest loopback. Dacă îl muți în Docker, adresa nu mai este corectă și trebuie reproiectat accesul, fără expunere SSH globală.
