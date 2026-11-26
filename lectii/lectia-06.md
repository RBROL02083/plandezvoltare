# Lecția 06 — Artifactory și lanțul de livrare verificat

**Săptămâna:** 2 · **Data de reper:** 06.12.2026 · **Durata:** 180 minute

## Obiective

- Publici și recuperezi un JAR versionat din Artifactory.
- Legi publicarea de toate verificările anterioare.
- Înțelegi snapshot, release și promovarea aceluiași artefact.

## Intrări necesare

[Lecția 05](./lectia-05.md) finalizată; ediție Artifactory disponibilă care suportă repository-uri Maven și resurse suficiente pentru pornire.

## Parcurs

1. **30 min — Repository de artefacte.** Pornește Artifactory cu stocare persistentă. Creează repository-ul Maven necesar și separă logic snapshoturile de release-uri. Restricționează accesul administrativ.
2. **30 min — Identitatea artefactului.** Alege coordonate `groupId:artifactId:version` și o versiune unică legată de build/commit. Pentru versiunile de deploy păstrează imutabilitatea. Configurează publicația în Gradle și păstrează tokenul în Jenkins Credentials.
3. **35 min — Publicare.** Publică numai după teste, gate și scanări. Folosește pluginul Gradle `maven-publish` pentru a publica exact fișierul JAR deja produs de `bootJar` și verificat, fără a lega taskul de publicare de o nouă compilare.
4. **25 min — Recuperare.** Dintr-un director curat descarcă versiunea explicită și verifică SHA-256 față de artefactul produs. Rulează JAR-ul și verifică endpointul. Nu descărca arbitrar „ultima versiune”.
5. **40 min — Încercări de blocare.** Pe branchuri de laborator, validează separat politica Sonar, SCA, container și secret scanning prin fixture-uri sau cazuri cunoscute controlate. Pentru fiecare, confirmă că publicarea nu s-a executat. Revino la o versiune curată.
6. **20 min — Promovare.** Documentează cum aceeași versiune verificată ajunge în test și apoi în alt mediu, fără recompilare. Salvează lanțul commit → build → coordonate → checksum.

## Livrabile

- JAR publicat și recuperabil prin coordonate explicite.
- Pipeline complet: checkout → build/test → analiză/scanări → publish.
- Matrice cu prag, probă negativă și rezultat pentru fiecare control.

## Criterii de finalizare

- [ ] Checksumul JAR-ului descărcat coincide cu cel al JAR-ului verificat.
- [ ] Niciun control eșuat nu permite publicarea.
- [ ] Release-ul nu este suprascris de o nouă execuție.
- [ ] Credentials au acces numai la repository-urile necesare.
- [ ] Știi explica diferența dintre snapshot, release și promovare.

## Dovadă și recapitulare

Păstrează coordonatele, checksumul și un build verde. Aceasta este baza pentru povestea de interviu „pipeline construit și quality gate eșuat”.

**Navigare:** [Lecția precedentă](./lectia-05.md) · [Index](../README.md) · [Lecția următoare](./lectia-07.md)
