# Lucru 18 — Client PostgreSQL pentru RDS și acces S3

[Lecția](../../lectii/lectia-18.md) · [Mediul WSL](../README.md)

## Prerechizite

- [17 finalizată](../lectia-17/README.md), configurația VPC/EC2 recreabilă, AWS CLI și Terraform.
- RDS privat, SG pentru conexiuni numai din EC2 și identitate EC2 cu drepturi minime.
- RDS poate necesita timp și cost suplimentar, inclusiv snapshoturi și stocare.

## Instalare / configurare

Nu instala un server PostgreSQL în WSL pentru a simula că ai testat RDS. Testul real se face de pe EC2, din VPC.

Dacă ai ales o imagine Ubuntu pentru EC2, după verificarea conexiunii SSH rulează **pe EC2**, nu în WSL:

```bash
sudo -A apt-get update
sudo -A apt-get install postgresql-client ca-certificates curl
psql --version
```

Aceste comenzi nu se aplică unei imagini Amazon Linux; folosește managerul de pachete al imaginii alese. Alege un client compatibil cu versiunea RDS.

Descarcă certificatul CA potrivit din [bundle-urile oficiale RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html). Configurează conexiunea cu `sslmode=verify-full` și certificatul CA, folosind **endpointul DNS RDS**, nu o adresă IP în locul numelui. Parola se introduce la prompt sau prin mecanismul de secret autorizat, nu în URL, istoric sau output Terraform.

AWS CLI din WSL este suficient pentru management S3. Dacă verifici accesul rolului de instanță din EC2, instalează AWS CLI v2 și acolo conform [instrucțiunilor din 16](../lectia-16/README.md), adaptate arhitecturii instanței. Nu copia cheile personale AWS pe instanță.

## Verificare și lucru

Continuă configurația Terraform comună și testele din lecție. Nu deschide RDS public pentru că WSL nu se poate conecta direct. Verifică sesiunea TLS din `psql`, drepturile userului aplicației și accesul S3 permis/refuzat.

La final inventariază snapshoturile, volumele, IP-urile și obiectele temporare înainte de cleanup. Nu șterge bucketul de state sau date care nu aparțin laboratorului.
