***site en construction, merci de votre comprehension***

### [Consult this site in english](https://jaayap.github.io/Unity_Best_Practices/)


# Introduction

Guide des bonnes pratiques de développement avec Unity : Versionning, tests unitaires, TDD, Clean Architecture

# __Plan :__

- [Versionning : Git & Unity](#versionning--git--unity)
  - [Le .gitignore](#le-gitignore)
  - [Smart Merge](#smart-merge)
  - [Les gros fichiers (.png, .fbx, ...)](#les-gros-fichiers)
        
- [Tests & TDD](#tests-&-tdd)
  - [Tests : Introduction](#tests--introduction)
  - [Tests Unitaires](#tests-unitaires)
  - [TDD (Test-driven-developpement)ou développement piloté par les tests](#tdd-test-driven-developpement-ou-développement-piloté-par-les-tests)
        
- [Clean Code](#clean-code)
- [Architecture](#architecture)


# Versionning : Git & Unity

Si vous utiliser *git* pour versionner vos projets *Unity*, vous avez du remarquez que parfois les fichiers tels que les scènes et les prefabs subissent des régressions lors des merges. Deux personnes modifiant ainsi le même fichier en même temps provoquent un conflit que git, par défaut n’arrive pas à résoudre. Vous retrouvez alors votre scène entièrement vide ou votre *prefab* tout cassé. 
Voici la solution que j’ai choisi d’adopter.

## Le .gitignore

Il est important d’ajouter un fichier **.gitignore** à votre archive. Il permettra à git d’ignorer certains fichiers temporaires générés par *Unity* et par votre IDE (dans l’exemple visual studio, mais vous pouvez modifier le *gitignore* pour l’adapter aux outils que vous utilisé). Dans certains projets, il est pertinent d’ajouter des dossiers de votre projet afin de ne pas les enregistrer dans le dépôt en ligne, un *asset* de l’*asset store* par exemple (peut peser lourd dans l’archive, ce qui ralenti considérablement l’équipe lorsqu’elle doit mettre à jour le dépôt ou le récupérer).

[voir dépot .gitignore Unity](https://github.com/github/gitignore/blob/master/Unity.gitignore)

## Enregistrement des fichiers en YAML

![Image of Unity Project Settings](https://s3.amazonaws.com/gamasutra/UnityVersionControlSettings.png)

Passer le mode d’enregistrement des assets en “Force Text” (voir image ci-dessus). Vos fichiers s’enregistrent maintenant au format YAML.
Cela permet à Git de mieux gérer les conflits, mais cela peut s'avérer insuffisant.

## Smart Merge

Depuis la version 5.0 de *Unity*, un **UnityYAMLMerge tool** est fourni avec son installation. Pour l’utiliser, il suffit de copier/ coller les lignes suivantes dans le fichier **.git/config** :

```
[merge]
tool = unityyamlmerge
[mergetool "unityyamlmerge"]
cmd = 'C:\\Program Files\\Unity\\Editor\\Data\\Tools\\UnityYAMLMerge.exe' merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
trustExitCode = false
keepTempories = true
keepBackup = false
```

Pensez à modifier le lien "*C:\\ ...*" selon l'emplacement de votre installation Unity, Si vous travaillez en équipe, il est préférable d'utilisez le même lien.  
:warning: **Attention : n'oublier pas de mettre des doubles '\\\\' et non des simples '\\' ou '/'.**  
*[Ce merge tool peut être utilisé avec d’autres outils (Perforce,SVN, Mercurial, SourceTree) , je vous conseille de vous référez à la documentation](https://docs.unity3d.com/Manual/SmartMerge.html)*  
  
Cet outil est très performant et permet de travailler à plusieurs sans se soucier de tout perdre (fonctionne avec les scènes et les prefabs).

### Comment ça marche ?

Continuer à utiliser git comme à votre habitude et si lors d’un merge, vous avez un conflit, effectuez la commande “git mergetool” qui appellera alors le smartMerge qui s’occupera dans la plupart des cas de régler vos conflits. Un exemple est donné ci-dessous, Deux branches sont créées, “Master” et “Another”, Chacune modifie la scene “sceneTest”. Ensuite Chacune est commit sur le dépôt et on essaye de “fusionner” les branches. A ce moment là, git nous dit qu’il y a conflit et lorsque l’on ouvre la scène dans Unity, on s'aperçoit qu’on a tout perdu. On exécute alors Git mergetool et on retrouve une scène qui possède tous nos objets.  

![Schema explicatif du Smart Merge](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/schemaSmartMergeV2.PNG?token=AHjeDi_p8D-_yEHzg1tX2cRNjKBIPpT7ks5bWHrPwA%3D%3D)

Le SmartMerge possède ses limites. Il ne peut pas résoudre seul le conflit lorsque vous toucher au même objet de la scène. Dans ce cas, il vous demandera de choisir la bonne version ( la scène actuelle, l’ancienne scène ou celle de votre collègue ?)

## Les gros fichiers

  Versionner des fichiers volumineux (png, fbx, …) peut s'avérer douloureux, GitHub vous empêche de pousser des fichiers de plus de 100 Mo et votre dépôt Git contient toutes les versions de chaque fichier.  
  Les révisions multiples de fichiers volumineux augmentent les temps de clonage et de récupération pour les autres utilisateurs du dépôt.  
  
  Git demande à chaque utilisateur d'avoir autant d'espace libre sur un disque dur que d'espace consommé à tout moment. Par exemple, si une archive est de 1 Go, Git nécessite 1 Go d'espace libre supplémentaire pour être disponible.  
  
  Il est conseillé de garder les fichiers suivant dans le dépôt : fichiers de code, assets qui ont besoin d’être versionné (graphiques), les fichiers de configuration (volumineux ou non) et de ne pas versionner les fichiers suivants : Bases de données, fichiers temporaires (journaux, log, …).   
  
  Si le dépôt que vous utilisez est compatible avec [Git LFS](https://git-lfs.github.com/), je vous recommande de l’utiliser. ([Git LFS](https://git-lfs.github.com/) est compatible avec GitHub / Bitbucket / Gitlab depuis la version 8). [Git LFS est une extension de Git et est open source](https://github.com/git-lfs/git-lfs?utm_source=gitlfs_site&utm_medium=repo_link&utm_campaign=gitlfs).  

Cependant, cette solution fonctionne **seulement** pour les fichiers **inférieur à 2Go**.

### Comment ça marche ?

![Image Explicative de Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image9.png?token=AHjeDpsdDG7nmpaEzQkxpTVyQp2cHdTmks5bWG9nwA%3D%3D)

[Git LFS](https://git-lfs.github.com/), va tracker les fichiers que vous voulez, soit avec leurs noms précis, soit avec leurs extensions, soit ceux qui sont dans un dossier que vous spécifierez …. Ensuite, ces fichiers seront automatiquement stockés sur un serveur (Large File Storage) et seulement le lien de votre fichier sera gardé. Attention, ce système ne versionne pas les fichiers trackés. Mais avez-vous vraiment besoin d’un *versionning* de vos *assets* 3D ou de vos éléments UI ? Qui sont généralement déjà versionner en amont par les Designers.

**Pour installer git LFS, lancer la commande :**

```
$ git lfs install
```
Git LFS  devrait s’installer automatiquement, si c’est le cas, la commande devrait retourner ce message :
```
>Updated git hooks.
>Git LFS initialized.
```
**Utilisez la commande suivante pour tracker les fichiers avec les extentions voulu (remplacer le *.extension par *.png ou *.fbx)**
```
$ git lfs track "*.extension" 
>Tracking "*.png"
```

**Assurez vous que votre projet possède un *.gitattributes***

```
$ git add .gitattributes
```
et voilà le tour est joué, à présent vous pouvez ajouter vos fichier et commit comme vous le faisiez avant, la différence est que git va envoyer les fichier tracké sur [Git LFS](https://git-lfs.github.com/).  

Si vous utilisez github, la notification “stored with Git LFS” devrait apparaître dans l’interface web lorsque vous ouvrez votre fichier


![Image of Github Stored with Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image7.png?token=AHjeDnlpMeQpTc27ikncn_j53g50GMEOks5bWG4gwA%3D%3D)


Pour comprendre le fonctionnement de [Git LFS](https://git-lfs.github.com/), je vous conseille de regarder la vidéo proposé par bitbucket : 

[![video Btbucket Git LFS](http://img.youtube.com/vi/9gaTargV5BY/0.jpg)](http://www.youtube.com/watch?v=9gaTargV5BY "Git LFS explain")



D’autres solutions existes avec git : Git annex, Git fat , Git media , Git Bigstore, Git sizer… //moins adapté à l’usage de unity / plus de ligne de commande / moins de documentation claire sur internet


# Tests & TDD

## Tests : Introduction
  
Les tests sont importants dans la réalisation d’un logiciel car ils assurent une **qualité logicielle** minimale. Beaucoup de logiciel sont conçu sans test et les conséquences sont:  
- beaucoup d’**anomalies**
- des **bugs** compliqués à résoudre et parfois lancés en production.
- un logiciel qui grossit à cause des patchs et des nouvelles fonctionnalités s’appuyant sur un code bancal. 
En bref, un code non testé entraîne bien souvent une **dette technique**.

![Image de la pyramide des tests](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/img_pyramide_des_tests.png?token=AHjeDoKKyxTJRlb2sRXe6a4YPP5RwJQTks5bYCFzwA%3D%3D)

La **pyramide des tests** présente les différents **types** de tests :

- Les **tests IHM** (Interface Homme-Machine) coûtent cher car il faut exécuter l’appli de bout en bout, Ces tests peuvent être automatisés mais long à être exécuté. Ces tests ne couvrent que certains scénario précis.
Quand ces tests ne sont pas automatisés, on engage des testeurs.

- Les **tests d’intégration** sont assez simples. On s’affranchit des contraintes majeures (IHM, certaines dépendances). Ils sont également assez proches du code, ce qui facilite leur refactoring. Cependant, ils couvrent un spectre plus large de code et de ce fait, ont davantage de risques d’être impactés par une modification.

- Les **tests unitaires** sont simples à mettre en œuvre lorsqu’ils sont faits au fur et à mesure du code, ils permettent de couvrir une majeure partie du code à faible coût.Ils peuvent être automatisé et rapide à exécuter. Il est fortement recommandé de commencer par rédiger des tests unitaires, ils sont la base des tests et permettent de repérer rapidement les régressions ou les mauvais comportement du logiciel.

:warning: **Attention** : Séparer les tests unitaires (rapide) des tests d’intégrations (plus lent).

Pour le moment nous nous intéresserons aux tests unitaires et au TDD (test driven development).

**Rédiger des tests unitaires sert à vérifier le comportement du code, tandis qu’avec l’approche TDD, qui consiste à écrire les tests en premier, rédiger des tests unitaires sert à spécifier comment le code doit fonctionner**.

Pour en savoir plus sur les tests, je vous conseille [l'article sur la pyramide des tests du blog d'OCTO Technology](https://blog.octo.com/la-pyramide-des-tests-par-la-pratique-1-5) , [ou une de leurs publications, culture code](https://www.octo.com/fr/publications/20-culture-code).

## Tests Unitaires

Les **tests unitaires** peuvent être regroupés dans un **projet de tests**.  
Ce dernier doit avoir la même **structure** que le projet que l’on souhaite tester. Ainsi, **Une classe de l’application = Une classe de tests**.

### Pourquoi écrire un test unitaire ?

- Être sur de ne **pas** faire de **retour en arrière**, de ne pas avoir cassé un code en le modifiant avant la mise en production

-  **Réduire les bugs** dans les fonctionnalité (déjà implémenté ou nouvelle)

- **Refacto en toute quiétude** : ils réduisent la peur de faire des **modifications** et d’**ajouter de nouvelles fonctionnalités** dans le programme ou l’application.

- Traiter **en amont** des tests interface et utilisateurs certains mauvais comportements et donc de les **corriger plus tôt**.

- Avoir un feedback rapide sur le code que les developpeurs viennent d'ajouter ou modifier.

- Permet d’avoir une application plus **robuste**

- Avoir une documentation minimale

En conclusion, plus rapidement vous saurez si un test échoue, plus vite vous pourrez corriger le problème et moins cher il vous en coûtera de le faire.

### Qu'est-ce qu'un bon test unitaire ?

Un bon test unitaire :
- est **entièrement automatisé**
- retourne **toujours le même résultat** si le code n’est pas modifié
- test qu’**un seul concept** ou **une seule logique** de l’application
- test qu’**une seule méthode à la fois**
- porte un **nom clair et significatif**

### Comment écrire un test unitaire ? 

Différentes conventions de nommage existent :
- Should_ExpectedBehavior_When_StateUnderTest
- When_StateUnderTest_Expect_ExpectedBehavior

Attention à **ne pas avoir le nom de la méthode dans le nom du test** car difficile à maintenir. En cas de refactorisation du nom de la méthode, le test ne veut plus rien dire.
Un test unitaire exprime une intention, c'est à dire, qu'il test une fonctionnalité, et non son implémentation.

Un test se décompose en **3 parties** : 
- **Arrange** est l’étape d’initialisation
- **Act** correspond à l’appel de la méthode testée
- **Assert** est l’étape de vérification du résultat du comportement de la méthode testée

Lorsque l'on écrit un test unitaire, il est conseillé de commencer par rédiger l'**Assert**, qui est la réponse à la question : **Qu'est-ce qu'on veut tester?**

### Et dans la pratique ça se passe comment ?

Le nom de la classe de test doit avoir en préfixe ou en suffixe “Test”, selon vos préférences.  
Veillez à ce que votre code soit harmonisé, ainsi si vous choisissez de mettre “Test” en suffixe, faites le sur toutes vos classes de tests.  

Par défaut Unity inclus **NUnit (C#)** pour les tests unitaires.
Devant chaque méthode de test l'étiquette **[Test]** doit apparaître : 

```
[Test]
public void Methode_Test() {
 
    ...
 
}
```

Avec le **using UnityEngine.TestTools;**, vous pouvez créer des **"Unity Tests"** :

```
[UnityTest]
public IEnumerator Methode_Unity_Test() {
 
    ...
    yield return null;
    ...
 
}
```

Comme vous pouvez le voir dans l'exemple ci-dessus, un Unity Test est une Coroutine.
Cela permet d'attendre une frame (yield retunr null) ou un nombre de secondes x (yield return new WaitForSecondes(x))

Ces tests permettent donc de tester les comportements qui dépendent d'Unity. Ils s'apparentent le plus souvent à des **tests d'intégrations**


Pour réaliser une assertion avec un test NUnit, rien de bien compliqué, il suffit d'utiliser la classe Assert. Quelques exemples :

```
    Assert.IsNotNull(object); 
    Assert.IsTrue(boolean);
    Assert.AreEqual(value1, value2)
```

Dans le cas de Asset.AreEqual, la value1 correspond à la valeur attendue est la value2 est la valeur testée. Cet ordre est important et permet notamment d'avoir des messages d'erreurs cohérents :  Expected "value1" but was "value2".

Unity ajoute également ses **propres assertions** (avec using UnityEngine.Assertions) comme :
- “Assert.AreApproximatelyEqual “ et “Assert.AreNotApproximatelyEqual “ qui prennent par défaut une tolérance de  0.00001f qui permettent de comparer deux floats.
- “ColorEqualityComparer”
- “QuaternionEqualityComparer”
- “Vector2EqualityComparer” ,”Vector3EqualityComparer“ et “Vector4EqualityComparer”


:warning: Les méthode Awake, Start et Update doivent être passé en public pour être appelée avec les tests.

#### Unity Test Runner 

Page de la documentation : [ici] (https://docs.unity3d.com/Manual/testing-editortestsrunner.html)  
Pour afficher la fenetre "Test Runner", allez dans windows / Test Runner.

![Image of Github Stored with Git LFS](https://docs.unity3d.com/560/Documentation/uploads/Main/UnityTestRunner1.png)  

On peut voir sur la capture d'écran, qu'il existe deux modes : 
 - mode Play : Execute [UnityTest] as a Coroutine. Ce mode peut être désactivé lors du build afin de ne pas alourdir le fichier final.
 - mode Editor : Execute [UnityTest] in Editor.Application.Update, callback loop  


Source : Unite Austin 2017 - Testing for Sanity: Using Unity's Integrated TestRunner, https://www.youtube.com/watch?v=MWS4aSO7HAo

TODO : Explication : capture écran du test runner + introduire exercice, test à trou. 

### Dependances, Mocks & Substituts

## TDD (Test-driven-developpement) ou développement piloté par les tests
# Clean Code
# Architecture
