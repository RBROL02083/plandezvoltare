# Lecția 02 — Pipeline declarativ: build și teste

**Săptămâna:** 1 · **Data de reper:** 26.11.2026 · **Durata:** 90 minute

## Obiective

- Scrii propriul Jenkinsfile declarativ, versionat împreună cu aplicația.
- Separi checkout, compilare și execuția testelor.
- Demonstrezi că un test eșuat oprește pipeline-ul.

## Intrări necesare

[Lecția 01](./lectia-01.md) finalizată; agentul Jenkins are Git și JDK, iar Gradle Wrapper este executabil.

## Parcurs

1. **15 min — Pregătirea jobului.** Creează un Pipeline from SCM sau un Multibranch Pipeline. Configurează repository-ul și branchul de laborator. Verifică directorul efectiv în care rulează comenzile backendului.
2. **25 min — Pipeline minim.** Adaugă stage-urile Checkout, Build și Test. Folosește un singur checkout explicit sau pe cel implicit, nu ambele. Din `backend/`, rulează `./gradlew classes` pentru compilare și `./gradlew test` în stage-ul Test. Nu folosi `build` în stage-ul Build: acel task include deja testele.
3. **25 min — Teste utile.** Acoperă crearea unui task valid și respingerea titlului gol. Rulează aceleași teste local și în Jenkins prin Gradle Wrapper, fără dependență de IDE.
4. **15 min — Eșec controlat.** Pe un branch de laborator introdu temporar o aserțiune greșită. Verifică statusul eșuat și faptul că stage-urile ulterioare nu rulează. Repară testul.
5. **10 min — Explicație.** Identifică `pipeline`, `agent`, `stages`, `stage`, `steps` și `environment`. Notează unde se află workspace-ul.

## Livrabile

- Jenkinsfile cu build și teste reproductibile.
- Două comportamente relevante acoperite de teste.
- O execuție roșie și una verde pentru același flux.

## Criterii de finalizare

- [ ] Un checkout curat poate compila și testa aplicația.
- [ ] Agentul folosește versiunea JDK stabilită, nu una implicită necunoscută.
- [ ] Eșecul testului este vizibil ca eșec al pipeline-ului.
- [ ] Nu există `|| true`, ignorarea globală a erorilor sau rezultate verzi false.

## Dovadă și recapitulare

Păstrează legăturile către cele două builduri și commitul reparat. Explică diferența dintre un stage și un step și de ce Jenkinsfile trebuie versionat.

**Dacă te blochezi:** compară JDK, directorul de lucru și permisiunea de execuție a `gradlew` dintre local și agent.

**Navigare:** [Lecția precedentă](./lectia-01.md) · [Index](../README.md) · [Lecția următoare](./lectia-03.md)
