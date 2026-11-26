# Lucru 21 — Pregătirea funcției Lambda

[Lecția](../../lectii/lectia-21.md) · [Mediul WSL](../README.md)

## Prerechizite

- [20 finalizată](../lectia-20/README.md), AWS CLI, Terraform și identitate autorizată.
- Backend remote disponibil. Configurația acestei lecții are **cheie de state distinctă**, fără recrearea RDS/ALB.
- Alege runtime-ul din [lista de runtime-uri Lambda suportate](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html). Java 21 local este o opțiune pentru un runtime Java compatibil; versiunea Node instalată local nu dovedește suportul aceleiași versiuni în Lambda.

## Instalare / configurare

Pentru împachetare, numai dacă `zip` lipsește:

```bash
sudo -A apt-get update
sudo -A apt-get install zip
zip -v
aws --version
terraform version
```

Nu instala SAM CLI, LocalStack, EKS, Kafka sau un server S3 doar pentru acest exercițiu.

- Creează codul handlerului și configurația într-un subdirector separat al repository-ului aplicației, de exemplu `terraform/environments/lambda-lab`.
- Pentru Java, folosește Gradle Wrapper în proiectul handlerului, separat de backendul Spring Boot. Declară bibliotecile oficiale Lambda în `build.gradle`, cu versiuni fixate. Împachetează clasele și dependențele runtime conform [ghidului AWS pentru Java și Gradle](https://docs.aws.amazon.com/lambda/latest/dg/java-package.html) și verifică numele handlerului. Nu folosi `bootJar` al aplicației Spring Boot pentru handler.
- Pentru JavaScript, folosește o versiune Node compatibilă cu runtime-ul ales și fixează dependențele numai dacă sunt necesare. Nu presupune că runtime-ul de cloud este Node 26 doar pentru că îl ai în WSL.
- Configurează filtrul de notificare S3, drepturile minime și retenția logurilor prin Terraform.

## Verificare și lucru

Rulează fluxul cu aprobare numai pe configurația Lambda/S3. Încarcă un obiect fără date sensibile și verifică o execuție reală în CloudWatch. Notează comportamentul la duplicate.

EKS, MSK și EFS rămân studiu conceptual: nu se instalează în WSL și nu se creează în AWS pentru această lecție. Distruge numai resursele temporare dedicate, inclusiv log groups prevăzute pentru cleanup; păstrează backendul.
