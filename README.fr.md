### [Consult this site in english](https://jaayap.github.io/Unity_Best_Practices/)

Ce guide a pour but de rassembler des informations sur les bonnes pratiques de développement (versionning, tests, architecture, ...) et de montrer comment les appliquer dans un projet Unity.  
  
La mise en place des bonnes pratiques ont pour but de générer des projets plus facilement maintenable et adaptable.  
  
Si vous n’avez jamais utilisé les bonnes pratiques de développement, il faudra les apprendre et les pratiquer pour les maîtriser.

# __Plan :__

- [Versionning : Git & Unity](#versionning--git--unity)
  - [Créer un .gitignore](#créer-un-gitignore)
  - [Enregistrer des fichiers en YAML](#enregistrer-des-fichiers-en-yaml)
  - [Merger avec l'outil Smart Merge](#merger-avec-loutil-smart-merge)
  - [Gérer les fichiers volumineux (images, objets 3D, ...)](#gérer-les-fichiers-volumineux)
        
- [Tests unitaires & TDD](#tests-unitaires--tdd)
  - [Tour d'horizon sur les tests](#tour-dhorizon-sur-les-tests)
  - [Tests Unitaires (TU)](#tests-unitaires-tu)
      - [Pourquoi écrire un TU ?](#pourquoi-écrire-un-test-unitaire-)
      - [Qu'est-ce qu'un bon TU ?](#quest-ce-quun-bon-test-unitaire-)
      - [Comment écrire un TU ?](#comment-écrire-un-test-unitaire-)
      - [Comment mettre en pratique Les TU avec Unity ?](#comment-mettre-en-pratique-les-tu-avec-unity-)
        - [Unity Test Runner](#unity-test-runner)
        - [Les scripts de tests](#les-scripts-de-tests)   
  - [développement piloté par les tests (TDD)](#tdd-test-driven-developpement-ou-développement-piloté-par-les-tests)

- [Gestion des dépendances **(en cours)**](#gestion-des-dépendances)
  - [Les dépendances dans les TU  **(en cours)**](#les-dépendances-dans-les-tests-unitaires) 
  - [Injection de dépendances  **(prochainement)**](#injection-de-dépendances)
  
- [Propreté du code avec Clean Code **(en cours)**](#propreté-du-code-avec-clean-code)
  - [Par où commencer ?](#par-où-commencer-)
    - [Les noms](#les-noms)
    - [Les commentaires](#les-commentaires)
    - [La loi de demeter](#la-loi-de-demeter)
  - [Principes et méthodes](#principes-et-méthodes)
  
- [Architecture **(prochainement)**](#architecture)
  - [La clean architecture **(prochainement)**](#la-clean-architecture)


# Versionning : Git & Unity

Si vous utiliser *git* pour versionner vos projets *Unity*, vous avez du remarquez que parfois les fichiers tels que les scènes et les prefabs subissent des régressions lors des merges. Deux personnes modifiant ainsi le même fichier en même temps provoquent un conflit que git, par défaut n’arrive pas à résoudre. Vous retrouvez alors votre scène entièrement vide ou votre *prefab* tout cassé. 
Voici la solution que j’ai choisi d’adopter.

## Créer un .gitignore

Il est important d’ajouter un fichier **.gitignore** à votre archive. Il permettra à git d’ignorer certains fichiers temporaires générés par *Unity* et par votre IDE (dans l’exemple visual studio, mais vous pouvez modifier le *gitignore* pour l’adapter aux outils que vous utilisé). Dans certains projets, il est pertinent d’ajouter des dossiers de votre projet afin de ne pas les enregistrer dans le dépôt en ligne, un *asset* de l’*asset store* par exemple (peut peser lourd dans l’archive, ce qui ralenti considérablement l’équipe lorsqu’elle doit mettre à jour le dépôt ou le récupérer).

[voir dépot .gitignore Unity](https://github.com/github/gitignore/blob/master/Unity.gitignore)

## Enregistrer des fichiers en YAML

![Image of Unity Project Settings](https://s3.amazonaws.com/gamasutra/UnityVersionControlSettings.png)

Passer le mode d’enregistrement des assets en “Force Text” (voir image ci-dessus). Vos fichiers s’enregistrent maintenant au format YAML.
Cela permet à Git de mieux gérer les conflits, mais cela peut s'avérer insuffisant.

## Merger avec l’outil Smart Merge

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

![Schema explicatif du Smart Merge](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/schemaSmartMergeV2.PNG?token=AHjeDgISOj3_CsXFl6gK4QxKd5kcyGPBks5baXFawA%3D%3D)

Le SmartMerge possède ses limites. Il ne peut pas résoudre seul le conflit lorsque vous toucher au même objet de la scène. Dans ce cas, il vous demandera de choisir la bonne version ( la scène actuelle, l’ancienne scène ou celle de votre collègue ?)

## Gérer les fichiers volumineux

  Versionner des fichiers volumineux (png, fbx, …) peut s'avérer douloureux, GitHub vous empêche de pousser des fichiers de plus de 100 Mo et votre dépôt Git contient toutes les versions de chaque fichier.  
  Les révisions multiples de fichiers volumineux augmentent les temps de clonage et de récupération pour les autres utilisateurs du dépôt.  
  
  Git demande à chaque utilisateur d'avoir autant d'espace libre sur un disque dur que d'espace consommé à tout moment. Par exemple, si une archive est de 1 Go, Git nécessite de 1 Go d'espace libre supplémentaire pour être disponible.  
  
  Il est conseillé de garder les fichiers suivant dans le dépôt : fichiers de code, assets qui ont besoin d’être versionné (graphiques), les fichiers de configuration (volumineux ou non) et de ne pas versionner les fichiers suivants : Bases de données, fichiers temporaires (journaux, log, …).   
  
  Si le dépôt que vous utilisez est compatible avec [Git LFS](https://git-lfs.github.com/), je vous recommande de l’utiliser. ([Git LFS](https://git-lfs.github.com/) est compatible avec GitHub / Bitbucket / Gitlab depuis la version 8). [Git LFS est une extension de Git et est open source](https://github.com/git-lfs/git-lfs?utm_source=gitlfs_site&utm_medium=repo_link&utm_campaign=gitlfs).  

Cependant, cette solution fonctionne **seulement** pour les fichiers **inférieurs à 2Go**.

### Comment ça marche ?

![Image Explicative de Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image9.png?token=AHjeDpsdDG7nmpaEzQkxpTVyQp2cHdTmks5bWG9nwA%3D%3D)

[Git LFS](https://git-lfs.github.com/), va tracker les fichiers que vous voulez, soit avec leurs noms, soit avec leurs extensions, soit avec leurs emplacements. Ensuite, ces fichiers seront automatiquement stockés sur un serveur (Large File Storage) et seulement le lien de votre fichier sera gardé. Attention, ce système ne versionne pas les fichiers trackés. Mais avez-vous vraiment besoin d’un *versionning* de vos *assets* 3D ou de vos éléments UI ? Généralement déjà versionnés en amont par les Designers.

**Pour installer git LFS, lancer la commande :**
```
$ git lfs install
```
Git LFS  devrait s’installer automatiquement, si c’est le cas, la commande devrait retourner ce message :
```
>Updated git hooks.
>Git LFS initialized.
```
**Utilisez la commande suivante pour tracker les fichiers avec les extensions voulu (remplacer le *.extension par *.png ou *.fbx)**
```
$ git lfs track "*.extension" 
>Tracking "*.extension"
```

**Assurez vous que votre projet possède un *.gitattributes***

```
$ git add .gitattributes
```
et voilà le tour est joué, à présent vous pouvez ajouter vos fichiers et *commit* comme vous le faisiez avant, la différence est que *Git* va envoyer les fichiers trackés sur [Git LFS](https://git-lfs.github.com/).  

Si vous utilisez github, la notification ***“stored with Git LFS”*** devrait apparaître dans l’interface web lorsque vous ouvrez votre fichier.


![Image of Github Stored with Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image7.png?token=AHjeDnlpMeQpTc27ikncn_j53g50GMEOks5bWG4gwA%3D%3D)


Pour comprendre le fonctionnement de [Git LFS](https://git-lfs.github.com/), je vous conseille de regarder la vidéo proposé par bitbucket : 

[![video Btbucket Git LFS](http://img.youtube.com/vi/9gaTargV5BY/0.jpg)](http://www.youtube.com/watch?v=9gaTargV5BY "Git LFS explain")


D’autres solutions existes avec git : Git annex, Git fat , Git media , Git Bigstore, Git sizer… //moins adapté à l’usage de unity / plus de ligne de commande / moins de documentation claire sur internet


# Tests unitaires & TDD

## Tour d'horizon sur les tests
  
Les tests sont importants dans la réalisation d’un logiciel car ils assurent une **qualité logicielle minimale**. 
Beaucoup de logiciel sont conçu sans test et les conséquences sont:  
- beaucoup d’**anomalies**
- des **bugs** compliqués à résoudre et parfois lancés en production.
- un logiciel qui grossit à cause des patchs et des nouvelles fonctionnalités s’appuyant sur un code bancal. 
En bref, un code non testé entraîne bien souvent une **dette technique**.

![Image de la pyramide des tests](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/img_pyramide_des_tests.png?token=AHjeDoKKyxTJRlb2sRXe6a4YPP5RwJQTks5bYCFzwA%3D%3D)

La **pyramide des tests** présente les différents **types** de tests :

- Les **tests IHM** (Interface Homme-Machine) coûtent cher car il faut exécuter l’appli de bout en bout. Ces tests peuvent être automatisés mais long à être exécutés. Ces tests ne couvrent que certains scénario précis.
Quand ces tests ne sont pas automatisés, on peut engager des testeurs.

- Les **tests d’intégration** sont assez simples. On s’affranchit des contraintes majeures (IHM, certaines dépendances). Ils sont également assez proches du code, ce qui facilite leur refactoring. Cependant, ils couvrent un spectre plus large de code et de ce fait, ont davantage de risques d’être impactés par une modification.

- Les **tests unitaires** sont simples à mettre en œuvre lorsqu’ils sont faits au fur et à mesure du code, ils permettent de couvrir une majeure partie du code à faible coût. Ils peuvent être **automatisés** et **rapide à exécuter**. Il est fortement recommandé de commencer par rédiger des tests unitaires, ils sont la **base** des tests et permettent de repérer rapidement les régressions ou les mauvais comportements du logiciel.

:warning: **Attention** : Séparer les tests unitaires (rapide) des tests d’intégrations (plus lent).

Pour le moment nous nous intéresserons aux **tests unitaires et au TDD (test driven development)**.

**Rédiger des tests unitaires sert à vérifier le comportement du code, tandis qu’avec l’approche TDD, qui consiste à écrire les tests en premier, rédiger des tests unitaires sert à spécifier comment le code doit fonctionner**.

Pour en savoir plus sur les tests, je vous conseille [l'article sur la pyramide des tests du blog d'OCTO Technology](https://blog.octo.com/la-pyramide-des-tests-par-la-pratique-1-5) , [ou une de leurs publications, culture code](https://www.octo.com/fr/publications/20-culture-code).

## Tests Unitaires (TU)

Les **tests unitaires** peuvent être regroupés dans un **projet de tests**.  
Ce dernier doit avoir la même **structure** que le projet que l’on souhaite tester. Ainsi, **Une classe de l’application = Une classe de tests**.

### Pourquoi écrire un test unitaire ?

- Être sur de ne **pas** faire de **retour en arrière**, de ne pas avoir cassé un code en le modifiant avant la mise en production

-  **Réduire les bugs** dans les fonctionnalités (déjà implémentés ou nouvelles)

- **Refacto en toute quiétude** : ils réduisent la peur de faire des **modifications** et d’**ajouter de nouvelles fonctionnalités** dans le programme ou l’application.

- Traiter **en amont** des tests interfaces et utilisateurs certains mauvais comportements et donc de les **corriger plus tôt**.

- Avoir un **feedback rapide** sur le code que les developpeurs viennent d'ajouter ou modifier.

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
- **Assert** est l’étape de vérification du comportement de la méthode testée

Lorsque l'on écrit un test unitaire, il est conseillé de commencer par rédiger l'**Assert**, qui est la réponse à la question : **Qu'est-ce qu'on veut tester?**

### Comment mettre en pratique les TU avec Unity ?

#### Unity Test Runner

Pour réaliser des tests unitaires, Unity met à disposition un outil appelé le [**Unity Test Runner**](https://docs.unity3d.com/Manual/testing-editortestsrunner.html)  
Pour afficher la fenêtre "Test Runner", allez dans *Windows > Test Runner*.

![Image of Test Runner](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture1_ouvertureOnglet.PNG)  

On peut voir sur la capture d'écran, qu'il existe **deux modes** :

 - **PlayMode** : 
    - permet d'executer des tests sur **plusieurs frames**
    - comportement Awake(), Start(), ... sont executés **automatiquement**
    - Sert davantage pour les **tests d'intégration**
    - [UnityTest] = exécuté comme une **Coroutine classique**
    - Ouvre une **scène** de test pour executé les tests (:warning: pensez à bien **enregistrer** votre scène avant de lancer les tests car votre scène sera écrasée au lancement des tests)

  - **EditMode** :
    - Chaque test est executé en **une frame**
    - Il faut appeler **explicitement** les méthodes Awake() et Start(), ce qui necessite de les passer en **public**.
    - **Doit être placés dans un dossier Editor**
    - [UnityTest] est executé dans l'editor avec **"Editor.Application.Update"**

Sélectionnez le mode qui vous intéresse puis cliquez sur le bouton *“Create PlayMode/EditMode Test Assembly Folder”*. 
Unity va créer automatiquement un dossier Test avec un fichier *“Tests.asmdef”de type “assembly definition”*.  
  
Une fois le dossier créé, placez-vous à l’intérieur puis cliquez sur *“Create Test Script in current folder”*.

![Image of Test Runner create script](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture2_btn_create_script.PNG)  

Unity va alors créer un fichier *template C#* avec le code suivant :

```cs
using UnityEngine;
using UnityEngine.TestTools;
using NUnit.Framework;
using System.Collections;

public class NewTestScript {

    [Test]
    public void NewTestScriptSimplePasses() {
        // Use the Assert class to test conditions.
    }

    // A UnityTest behaves like a coroutine in PlayMode
    // and allows you to yield null to skip a frame in EditMode
    [UnityTest]
    public IEnumerator NewTestScriptWithEnumeratorPasses() {
        // Use the Assert class to test conditions.
        // yield to skip a frame
        yield return null;
    }
}
```

Ces deux tests devraient apparaître dans le Test Runner : 

![Image of Test Runner tests with test](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture3_tests.PNG)

Si vous cliquer sur **“Run All”**, les tests vont se lancer, vu que ces tests n’ont pas d’assertion, les tests devrait passer au **vert** directement. 

![Image of Test Runner tests green tests](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture4_green_tests.PNG)

Maintenant, si nous remplaçons le premier test par celui ci : 

```cs
    [Test]
    public void NewTestScriptSimplePasses() {
        // Use the Assert class to test conditions.
        Assert.AreEqual(4, 3);
    }
```

le test devrait être **rouge** car 3 et 4 ne sont pas égaux :

![Image of Test Runner tests red test](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture5_red_tests.PNG)

Si vous cliquez sur le test en question, vous pouvez voir un message d’erreur et pourquoi le test a échoué (dans l’exemple : Expected : 4, But was : 3) : 

![Image of Test Runner tests red test explanation](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture5_red_tests_with_error_msg.PNG)

Aujourd'hui, *Unity* ne permet pas de lancer les tests en lignes de commande.
Les tests appelant des *MonoBehaviour* ne peuvent pas être éxécuté depuis [*Visual Studio*](https://visualstudio.microsoft.com/). 
Si vous utilisez [***Rider***](https://www.jetbrains.com/dotnet/promo/unity/), vous pourrer les lancer directement dans son interface.

#### Les Scripts de tests

**Le nom de la classe de test doit avoir en préfixe ou en suffixe *“Test”*, selon vos préférences.**  
Veillez à ce que votre code soit harmonisé, ainsi si vous choisissez de mettre “Test” en suffixe, faites le sur toutes vos classes de tests.  

Par défaut Unity inclus le framework de test unitaire C#  **[NUnit](https://nunit.org/)** pour les tests unitaires.
Devant chaque méthode de test l’attribut **[Test]** doit apparaître :

```cs
[Test]
public void Methode_Test() {
 
    ...
 
}
```

Avec le **using UnityEngine.TestTools;**, vous pouvez créer des **"Unity Tests"** :

```cs
[UnityTest]
public IEnumerator Methode_Unity_Test() {
 
    ...
    yield return null;
    ...
 
}
```

Un Unity Test est une [**Coroutine**](https://docs.unity3d.com/Manual/Coroutines.html). Dans Unity, les Coroutines sont généralement utilisé pour gérer le rendu. Nous pouvons par exemple, **attendre une frame** (yield return null) ou un **nombre de secondes x** (yield return new WaitForSecondes(x)) pour effectuer une action.

Ces tests permettent donc de tester les comportements qui dépendent d'Unity. Ils s'apparentent le plus souvent à des **tests d'intégrations**

Pour réaliser une assertion avec un test NUnit, il faut utiliser la classe **Assert**, quelques exemples :

```cs
    Assert.IsNotNull(object);
    Assert.IsTrue(boolean);
    Assert.AreEqual(value1, value2)
```

**Dans le cas de Asset.AreEqual, la value1 correspond à la valeur attendue est la value2 est la valeur testée. Cet ordre est important et permet notamment d'avoir des messages d'erreurs cohérents :  Expected "value1" but was "value2".**

Unity ajoute également ses **propres assertions** (avec using UnityEngine.Assertions et UnityEngine.TestTools.Utils) comme :
- “Assert.AreApproximatelyEqual “ et “Assert.AreNotApproximatelyEqual “ qui prennent par défaut une tolérance de  0.00001f qui permettent de comparer deux floats.
- “ColorEqualityComparer”
- “QuaternionEqualityComparer”
- “Vector2EqualityComparer” ,”Vector3EqualityComparer“ et “Vector4EqualityComparer”


:warning: Les méthode Awake, Start et Update doivent être passé en public pour être appelée avec les tests.

Si tous vos tests ont le même "Arrange" vous pouvez utilisez les attributs [SetUp] et [TearDown].
La méthode sous l'attribut [SetUp] s'executera avant toutes les méthodes [Tests] et la méthode sous l'attribut [TearDown] s'executera aprés  toutes les méthodes [Tests].

Ainsi : 
```cs
public class MaClassTest {
  [Test]
  public void Methode_Test_0() {
    //Arrange
    GameManager game = new GameManager();
    ...
    //Act
    ...
    //Assert
    ...
  }

  [Test]
  public void Methode_Test_1() {
    //Arrange
    GameManager game = new GameManager();
    ...
    //Act
    ...
    //Assert
    ...
  }
}
```
Devient : 
```cs
public class MaClassTest {
  
  GameManager game;
  
  [SetUp]
  public void Init(){
    game = new GameManager();
  }
  
  [TearDown]
  public void Dispose() {
    Destroy(game);
  }

  [Test]
  public void Methode_Test_0() {
    //Arrange
    ...
    //Act
    ...
    //Assert
    ...
  }

  [Test]
  public void Methode_Test_1() {
    //Arrange
    ...
    //Act
    ...
    //Assert
    ...
  }

```


Source : Unite Austin 2017 - Testing for Sanity: Using Unity's Integrated TestRunner, https://www.youtube.com/watch?v=MWS4aSO7HAo




## TDD (Test-driven-developpement) ou développement piloté par les tests

Le **TDD ou Développement piloté par les tests** est une technique de développement qui consiste à écrire les tests **avant** d’écrire le code. Cela garantit davantage la structure du code comme testable et maintenable.
En utilisant la technique TDD, les tests unitaires ne servent plus à valider un code existant mais à **spécifier** le futur code implémenter.

![schema cycle TDD](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/RED-GREEN-REFACTO%20cycle.png?token=AHjeDtvHOiXwwhctewLgG-4a0KSUEjf7ks5bYXxlwA%3D%3D)

Comme nous pouvons le voir sur le schéma ci-dessus, la méthode TDD possède **3** grandes étapes : 
- **RED** : On commence par écrire un test et on vérifie que ce dernier échoue (car le code n'est pas implémenté). Ce test spécifie le comportement d'une méthode (ce qu'elle doit renvoyer, ce qu'elle doit appeler, ...).
- **GREEN** : On écrit le code minimum pour que le test passe au vert.
- **REFACTOR** : On améliore le code sans changer son comportement.
Puis on passe à l'écriture d'un autre test, et la boucle recommence.

Dans le livre Clean Code (p.80), Oncle Bob définit les 3 lois du TDD :
   1. Vous n'êtes pas autorisé à écrire du code métier tant que vous n'avez pas écrit un premier test unitaire qui échoue.
   2. Vous n'êtes pas autorisé à écrire plus qu'un test unitaire qui est suffisant pour échouer et qui ne compile pas.
   3. Vous n'êtes pas autorisé à écrire plus de code que ce qui est suffisant pour passer au vert le test unitaire.
  
:warning: Oncle Bob, insiste sur le fait, qu'écrire des tests unitaires ou pratiquer le TDD n'est pas magique, et qu'on peut très bien continuer d'écrire du mauvais code et d'écrire des mauvais tests.
Il précise également que suivre les trois lois n'est pas toujours appropprié, il faut donc trouver des solutions adaptés à notre projet.


TODO : exercice à trou (suite ou reprise du même exo) TU avec TDD. 

# Gestion des dépendances

## Les dépendances dans les tests unitaires 

Dans la pratique, un TU ne dois jamais s’appuyer sur **une dépendance extérieure** (service web, base de données, librairie, …). En effet, cela peut entraîner un **biais** et peut rendre les TU plus difficiles à maintenir dans le temps. 
Une dépendance extérieure aura tendance à **rallonger le temps d'exécution**.

Pour être sûr de tester un seul comportement, sans aucune dépendance, il est possible de créer **des bouchons (mock ou stub)** pour remplacer une classe.
Cela permet d’éviter un null exception en renseignant un objet de substitution, ou encore de vérifier qu’une fonction est bien appelée sans exécuter son code.

**Quelle est la différence entre Stub et Mock ?**

**Un Stub** est un objet que vous pouvez contrôler, qui sert de substitut **à une dépendance extérieure** (exemple : un faux service web qui renvoie toujours la même information)  
  
**Un Mock** est un objet de substitut **dans le système**, qui décide si un test unitaire passe ou échoue (exemple : une base de données qui attend une certaine requête bien précise)

Pour créer des mocks ou des substitut avec *Unity*, vous pouvez installer **NSubstitute**, pour cela il suffit de placer le fichier **NSubstitute.dll** dans votre projet *Unity*. Vous pouvez télécharger ce fichier [ici](https://github.com/jaayap/Unity_Best_Practices/tree/master/NSubstitute/dll). 

PROCHAINEMENT : Exemple de code avec TU

## Injection de dépendances 

D de SOLID (voir partie suivante)
 => Zenject

# Propreté du code avec Clean Code

> « Il existe deux façons de concevoir un logiciel : une façon est de le rendre si simple qu'il n'a visiblement aucun défaut, et une autre est de le rendre si complexe qu'il n'y a pas de défaut visible. La première façon est de loin la plus difficile. » 
> Hoare

Qu’est-ce qu’un code propre ? 

![Image intro](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/wtfm.jpg)  

- Un code propre doit être agréable à lire.
- Un code propre doit prêter attention aux détails et doit être est focalisé.
- Chaque fonction, chaque classe, chaque objet doit avoir une attitude simple qui reste entièrement indépendante, et non polluée par les détails non nécessaires.
- Un code non testé n’est pas un code propre, peu importe s’il est lisible et accessible.
- Ne contient pas de duplication.
- Évident, simple et convaincant: quand on lit un code propre, on ne doit dépenser trop d’effort pour le comprendre.

La propreté du code définit la **qualité logicielle**.

**Pourquoi avoir un code simple et propre est si important ? **

Le code est le premier **environnement de travail** du développeur. Il est donc plus agréable pour le développeur de travailler avec une bonne compréhension du système et de pouvoir le faire évoluer sans tout réécrire. En effet un code propre permet de gagner du temps lorsqu’il faut faire évoluer le projet.  De plus, la qualité interne du code (structure) **influe** directement sur la qualité externe (fonctionnement) du projet. Avoir un code propre sert à **maintenir la maintenabilité**.

Un code propre permet donc de **faire évoluer** et de **maintenir** un projet.

## Par où commencer ? 

### Les noms

Lorsqu’on programme, bien trop souvent on oublie de réfléchir aux noms que nous donnons aux variables, aux méthodes et parfois même aux classes.  Ce qui peut entraîner des incompréhensions du système par d’autres développeurs et une relecture du code plus longue. Un mauvais nommage peut également impacter la maintenabilité d’une application.

Lorsque vous programmer : 
- Utilisez des noms qui révèlent l’intention (= noms descriptifs), pour vous aider posez vous la question “à quoi va servir cette méthode/classe/variable ? “
- Essayer de choisir qu’un seul mot par concept. Par exemple, si une classe sert à instancier un objet ne mélangez pas, “create”, “instantiate”, generate”, … Mais choisissez un seul mot qui  correspond à ce que vous faites ou le mot utilisé par le métier.
- Choisissez des mots facile à prononcer.
- Evitez de placer des valeurs sans les nommer (magic number)

### Les commentaires

Dans un code, les commentaires sont importants mais parfois utilisés de manière abusives (écrire un commentaire pour expliquer tout le fonctionnement d’un algorithme plutôt que de le réécrire de manière simple et compréhensible). 

Les bons commentaires sont des commentaires facultatifs, si possible, la compréhension du code ne doit pas être dépendant des commentaires. Ils doivent servir de complément d’informations, d’avertissement sur les conséquences ou à expliquer une intention.

### La loi de demeter

La loi de Déméter (en anglais Law of Demeter ou LoD) peut s’énoncer de la manière suivante :
“Chaque unité doit avoir une connaissance limitée des autres unités, chaque unité doit voir que les unités étroitement liées à l’unité actuelle.”

Cela signifie qu’un objet devrait faire le moins d’hypothèse possible à propos des autres structures que lui même. 

En pratique, cela veut dire qu’une méthode peut invoquer les méthodes :
- de l’objet lui-même
- des paramètres de la méthode
- des objets créés / instanciées dans la méthode
- des propriétés et champs de l’objet 

:plus: L'avantage de suivre la règle de Déméter est que le logiciel résultat est plus maintenable et plus adaptable. Puisque les objets sont moins dépendants de la structure interne des autres objets, ceux-ci peuvent être changés sans changer le code de leurs appelants.

:moins: L'inconvénient  de la loi de Déméter est qu'elle requiert l'écriture d'un grand nombre de petites méthodes "wrapper" pour propager les appels de méthodes à leurs composants. Cela peut augmenter le temps de développement initial, accroître l'espace mémoire utilisé, et diminuer les performances.
TODO : vérifier info / ajouter sources

## Quelques règles

Depuis les années 1990/2000, plusieurs méthodes et principes ont été définis pour apporter une ligne directrice dans le développement de logiciel plus fiable et plus robuste. Parmis eux, beaucoup proviennent des pratiques eXtreme Programming et Clean code. Ces pratiques sont populaires aujourd’hui car elles répondent aux questions soulevé par l’agilité.

Ainsi, les règles d’un design simple sont définis par Kent Beck en 1990 dans son livre “Extreme Programming Explained”, ces règles sont les suivantes : 

1. les tests passent (signifie que le code fonctionne correctement et sous entend que le code doit être couvert par les tests)
2. L’intention du code est claire
3. **DRY** principle (Don’t Repeat Yourself) :
            Il n’y a pas de duplication de code
4. Le code a le moins d’éléments possible :
    - **KISS : Keep it Simple & Stupid**
                Principe de la responsabilité unique (un élément possède qu'une seule raison d'être modifié)
    - **YAGNI  : You ain’t gonna need it**
                Essayer d'anticiper les problèmes futurs, n'est pas une bonnes idée. Il est préférable de s'occuper du présent et de ne pas faire d'hypothèse sur ce que l'application pourrait utiliser.


Quelques année plus tard, Robert Cecil Martin alias Oncle Bob met en avant plusieurs grands principes : Les principes SOLID et les principes composants. 

**Les principes SOLID sont définis comme suit : **

- <mark>**S**</mark>RP (Single Responsibility Principle) 
Ne faire qu’une seule chose mais la faire bien. Une classe doit avoir une et une seule raison de changer. 

- **O**CP (Open-Closed Principle) 
Une classe doit être ouverte à l'extension mais fermée à la modification (plugins)

- **L**SP (Liskov Substitution Principle) 
Toutes implémentation d’une interface doit pouvoir se substituer à une autre

- **I**SP (interface Segregation Principle) 
Une classe ne doit implémenter une interface que si elle à réellement le besoin de 
remplir son contrat

- **D**IP (Dependency Inversion Principle) 
Le code ne doit pas interagir directement avec l'extérieur mais doit passer par des 
abstractions (et vice versa).


**Les trois principes composants**

Les composants sont les unités du déploiement. Ils sont les plus petits morceaux qui peuvent être déployés comme une partie du système (fichier .jar / fichier .gem/ .DLL). 

Les trois principes sont les suivants : 

- **REP (Reuse/Release Equivalence Principle)** : 
Créer un code qui peut être réutilisé (plugin, code générique avec le moins de dépendance possible)
“the granule of reuse is the granule of release” 
"Le germe de la réutilisation est le germe de la libération"

- **CCP (Common Closure Principle)** : Un composant (une classe, une méthode, ...)  ne doit pas avoir plusieurs raisons de changer

- **CRP (Common Reuse Principle)** : 
“Don’t force users of a component to depend on things they don’t need.” 
“Ne forcez pas les utilisateurs d’un composant à dépendre d’une chose dont ils n’ont pas besoin” 

# Architecture

## La Clean Architecture
