### [Consult this site in english](https://jaayap.github.io/Unity_Best_Practices/)

Ce **guide** a pour but de rassembler des informations sur les **bonnes pratiques de développement** (versionning, tests, architecture, ...) et de montrer comment les **appliquer dans un projet Unity**.  
  
La mise en place des bonnes pratiques a pour but de générer des projets plus facilement maintenable et adaptable.  
  
Si vous n’avez jamais utilisé les bonnes pratiques de développement, il faudra les apprendre et les pratiquer pour les maîtriser.  
  
Ce guide requiert des bases de programmation C# et une connaissance du logiciel Unity.   
  
:warning: Les pratiques présentées dans ce guide ne sont pas des uniques solutions et ne sont pas toujours adaptés à votre projet ou à votre équipe. Certaines personnes peuvent obtenir de meilleurs résultats en suivant leurs propres méthodes acquises avec leur expérience. 
Mon but n’est pas de vous imposer une méthode stricte pour vos projets mais de vous amener à réfléchir sur des problématiques courantes et à vous donner des bases théoriques et pratiques sur les solutions que j’ai étudié.


# __Plan :__

- [Versioning : Git & Unity](Versioning.md/#versioning--git--unity)
  - [Créer un .gitignore](Versioning.md/#créer-un-gitignore)
  - [Enregistrer des fichiers en YAML](Versioning.md/#enregistrer-des-fichiers-en-yaml)
  - [Merger avec l'outil Smart Merge](Versioning.md/#merger-avec-loutil-smart-merge)
  - [Gérer les fichiers volumineux (images, objets 3D, ...)](Versioning.md/#gérer-les-fichiers-volumineux)
        
- [Tests unitaires & TDD](Unit_Test_And_TDD.md/#tests-unitaires--tdd)
  - [Tour d'horizon sur les tests](Unit_Test_And_TDD.md/#tour-dhorizon-sur-les-tests)
  - [Tests Unitaires (TU)](Unit_Test_And_TDD.md/#tests-unitaires-tu)
      - [Pourquoi écrire un TU ?](Unit_Test_And_TDD.md/#pourquoi-écrire-un-test-unitaire-)
      - [Qu'est-ce qu'un bon TU ?](Unit_Test_And_TDD.md/#quest-ce-quun-bon-test-unitaire-)
      - [Comment écrire un TU ?](Unit_Test_And_TDD.md/#comment-écrire-un-test-unitaire-)
      - [Comment mettre en pratique Les TU avec Unity ?](Unit_Test_And_TDD.md/#comment-mettre-en-pratique-les-tu-avec-unity-)
        - [Unity Test Runner](Unit_Test_And_TDD.md/#unity-test-runner)
        - [Les scripts de tests](Unit_Test_And_TDD.md/#les-scripts-de-tests)   
  - [développement piloté par les tests (TDD)](Unit_Test_And_TDD.md/#développement-piloté-par-les-tests-tdd)

- [Gestion des dépendances **(en cours)**](ManagementOfDependancies.md/#gestion-des-dépendances)
  - [Les dépendances dans les TU  **(en cours)**](ManagementOfDependancies.md/#les-dépendances-dans-les-tests-unitaires) 
  - [TU avec dépendances dans Unity **(en cours)**](ManagementOfDependancies.md/#tu-avec-dépendances-dans-unity)
  - [Injection de dépendances  **(prochainement)**](ManagementOfDependancies.md/#injection-de-dépendances)
  
- [Propreté du code avec Clean Code **(en cours)**](CleanCode.md/#propreté-du-code-avec-clean-code)
  - [Par où commencer ?](CleanCode.md/#par-où-commencer-)
    - [Les noms](CleanCode.md/#les-noms)
    - [Les commentaires](CleanCode.md/#les-commentaires)
    - [La loi de Déméter](CleanCode.md/#la-loi-de-déméter)
  - [Principes et méthodes](CleanCode.md/#principes-et-méthodes)
  
- [Concevoir un code mieux structuré avec une aproche par couche **(en cours)**](Architecture.md/#concevoir-un-code-mieux-structuré)
  - [Un code facilement évolutif grâce à la Clean Architecture **(en cours)**](Architecture.md/#un-code-facilement-evolutif-grace-a-la-clean-architecture)
      - [Comprendre la Clean Architecture](Architecture.md/#comprendre-la-clean-architecture)
      - [Comprendre les interfaces](Architecture.md/#comprendre-les-interfaces)
      - [Appliquer la Clean Architecture dans Unity](Architecture.md/#appliquer-la-clean-architecture-dans-unity)
      
- [Concevoir un code mieux structuré avec une aproche par composants **(prochainement)**](#)
  - [ECS (Entity-Component-System) architecture **(prochainement)**](#)

- [A propos](About.md)
- [Licence](../LICENSE)
