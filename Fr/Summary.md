### [Consult this site in english](https://jaayap.github.io/Unity_Best_Practices/)

Ce guide a pour but de rassembler des informations sur les bonnes pratiques de développement (versionning, tests, architecture, ...) et de montrer comment les appliquer dans un projet Unity.  
  
La mise en place des bonnes pratiques a pour but de générer des projets plus facilement maintenable et adaptable.  
  
Si vous n’avez jamais utilisé les bonnes pratiques de développement, il faudra les apprendre et les pratiquer pour les maîtriser.

# __Plan :__

- [Versionning : Git & Unity](Versionning.md/#versionning--git--unity)
  - [Créer un .gitignore](Versionning.md/#créer-un-gitignore)
  - [Enregistrer des fichiers en YAML](Versionning.md/#enregistrer-des-fichiers-en-yaml)
  - [Merger avec l'outil Smart Merge](Versionning.md/#merger-avec-loutil-smart-merge)
  - [Gérer les fichiers volumineux (Versionning.md/images, objets 3D, ...)](#gérer-les-fichiers-volumineux)
        
- [Tests unitaires & TDD](#tests-unitaires--tdd)
  - [Tour d'horizon sur les tests](#tour-dhorizon-sur-les-tests)
  - [Tests Unitaires (TU)](#tests-unitaires-tu)
      - [Pourquoi écrire un TU ?](#pourquoi-écrire-un-test-unitaire-)
      - [Qu'est-ce qu'un bon TU ?](#quest-ce-quun-bon-test-unitaire-)
      - [Comment écrire un TU ?](#comment-écrire-un-test-unitaire-)
      - [Comment mettre en pratique Les TU avec Unity ?](#comment-mettre-en-pratique-les-tu-avec-unity-)
        - [Unity Test Runner](#unity-test-runner)
        - [Les scripts de tests](#les-scripts-de-tests)   
  - [développement piloté par les tests (TDD)](#développement-piloté-par-les-tests-tdd)

- [Gestion des dépendances **(en cours)**](#gestion-des-dépendances)
  - [Les dépendances dans les TU  **(en cours)**](#les-dépendances-dans-les-tests-unitaires) 
  - [TU avec dépendances dans Unity **(en cours)**](#tu-avec-dépendances-dans-unity)
  - [Injection de dépendances  **(prochainement)**](#injection-de-dépendances)
  
- [Propreté du code avec Clean Code **(en cours)**](#propreté-du-code-avec-clean-code)
  - [Par où commencer ?](#par-où-commencer-)
    - [Les noms](#les-noms)
    - [Les commentaires](#les-commentaires)
    - [La loi de Déméter](#la-loi-de-déméter)
  - [Principes et méthodes](#principes-et-méthodes)
  
- [Concevoir un code mieux structuré **(prochainement)**](#concevoir-un-code-mieux-structuré)
  - [Un code facilement évolutif grâce à la clean architecture **(prochainement)**](#la-clean-architecture)
  - [ECS architecture **(prochainement)**](#ecs-architecture)

- [A propos **(prochainement)**](#a-propos)
- [Licence](#licence)




# A propos
# Licence

Licence MIT