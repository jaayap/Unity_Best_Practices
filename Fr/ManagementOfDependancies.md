 Gestion des dépendances

## Les dépendances dans les tests unitaires 

Dans la pratique, un TU ne doit jamais s’appuyer sur **une dépendance extérieure** (service web, base de données, librairie, …). En effet, cela peut entraîner un **biais** et peut rendre les TU plus difficiles à maintenir dans le temps.  
Une dépendance extérieure aura tendance à **rallonger le temps d'exécution**.

Pour être sûr de tester un seul comportement, sans aucune dépendance, il est possible de créer **des bouchons (mock ou stub)** pour remplacer une classe.
Cela permet d’éviter un null exception en renseignant un objet de substitution, ou encore de vérifier qu’une fonction est bien appelée sans exécuter son code.

**Quelle est la différence entre Stub et Mock ?**

**Un Stub** est un objet que vous pouvez contrôler, qui sert de substitut **à une dépendance extérieure** (exemple : un faux service web qui renvoie toujours la même information)  
  
**Un Mock** est un objet de substitut **dans le système**, qui décide si un test unitaire passe ou échoue (exemple : une base de données qui attend une certaine requête bien précise)

## TU avec dépendances dans Unity

Pour créer des mocks ou des substituts avec *Unity*, vous pouvez installer **NSubstitute**, pour cela il suffit de placer le fichier **NSubstitute.dll** dans votre projet *Unity*. Vous pouvez télécharger ce fichier [ici](https://github.com/jaayap/Unity_Best_Practices/tree/master/NSubstitute/dll). 

**Exemple :**
 On veut s’assurer que la méthode *InitializeObject()* du *GameManager.cs* est appelée avec le bon paramètre lors du *Start()* du script *ObjectBehaviour.cs*

```cs
[Test]
public void WhenGameStartObjectInitialize()
{
    //Arrange
    ObjectBehaviour object = new GameObject().AddComponent<ObjectBehaviour>();
    GameManager gameManagerMock = Substitute.For<GameManager>();
    object .GameManager = gameManagerMock;

    //Act
    object.Start();

    //Assert
    gameManagerMock.Received().InitializeObject(object);
}
```

## Injection de dépendances 

D de SOLID (voir partie suivante)
 => Zenject