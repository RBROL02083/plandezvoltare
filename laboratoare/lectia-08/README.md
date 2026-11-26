# Lucru 08 — Instalarea aplicației ca serviciu systemd

[Lecția](../../lectii/lectia-08.md) · [Mediul WSL](../README.md)

## Prerechizite

- [07 finalizată](../lectia-07/README.md): Ansible, SSH verificat și drepturi `become` pentru bootstrap.
- JAR publicat în Artifactory, coordonate exacte și checksum SHA-256 cunoscut.
- JDK 21 există deja în WSL; contul dedicat aplicației va fi creat prin playbook, separat de `devops-deploy`.

## Instalare / configurare

Nu instala un alt init system sau Java din nou. Verifică:

```bash
ps -p 1 -o comm=
java -version
ansible-playbook --version
```

În `~/task-tracker/ansible` scrie playbookul cerut de lecție: pachetul JDK necesar, contul aplicației, directoare, descărcarea verificată și unitul systemd. Prima rulare, cu numele de playbook pe care îl creezi:

```bash
cd "$HOME/task-tracker"
ansible-playbook -i ansible/inventory.ini ansible/site.yml \
  --private-key "$HOME/.ssh/devops-lab" --ask-become-pass
```

`ansible/site.yml` este un fișier de implementat în lecție, nu un fișier deja generat de aceste ghiduri. Parola `become` se introduce numai la promptul local. Credentials Artifactory nu intră în cod sau în argumentele comenzii.

Folosește numele de serviciu `task-tracker` și portul 8080 în configurația laboratorului. Nu opri un proces necunoscut ca să eliberezi portul; dacă este ocupat, alege alt port și actualizează verificările.

## Verificare și lucru

```bash
systemctl status task-tracker --no-pager
sudo -A journalctl -u task-tracker -n 50 --no-pager
curl -fsS --max-time 10 http://localhost:8080/api/tasks
```

Verifică utilizatorul procesului și a doua execuție idempotentă. Nu folosi `systemctl --user` pentru unitul de sistem instalat prin Ansible. Serviciul nu supraviețuiește opririi distribuției WSL; datele și fișierele persistă.
