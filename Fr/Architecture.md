# Concevoir un code mieux structuré

Il existe plusieurs architectures logicielles permettant d'avoir une structure de code adaptable et maintenable.
La mise en place d'une bonne architecture doit permettre de : 
  - Séparer les responsabilités
  - Rendre le code facilement testable
  - Rendre le code facilement modifiable et maintenable dans le temps 
  - Séparer les parties facilement testable (fonctions métier ? stable)  et les parties difficilement testable (infrastructure ? volatile)
  
> “Architecture is not about tools and building materials, architecture is about usage.” 
> Robert C. Martin (Oncle Bob)

Une bonne architecture doit supporter :
  - Les cas d’utilisation (=l’intention) et le fonctionnement du système
  - La maintenance du système
  - Le développement du système
  - Et le déploiement du système
   
Elle est centrée sur les cas d’utilisation, les architectes logiciel peuvent donc décrire les structures qui les supportent en toute sécurité sans devenir dépendant des frameworks, des outils ou de l’environnement.   
Le choix d’un framework (bases de données, serveur web, …) ne doit pas impacter l’architecture.   
Oncle bob dit ***“les framework sont des produits commerciaux, tout est fait pour que ce soit simple à utiliser mais tout n'est pas une bonne idée.”***

[Retour au menu](Summary.md)

## Un code facilement évolutif grâce à la Clean Architecture

### Comprendre la Clean Architecture

La Clean Architecture repose sur un modèle plus simple appelé “Hexagonal Architecture, ports and adapters” développé par Alistair Cockburn (2005) et repris par [Steve Freeman et Nat Pryce dans leur livre intitulé “Growing Object Oriented Software with Tests”](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627). 

![Schema Archi Hexagonale](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/hexagonalArchitecture.png)
schéma repris du livre “Clean Architecture” Robert C. Martin
  
L’Architecture Hexagonale  est composée de deux couches : 
- **Le Domain** : Il contient le modèle de données conceptuel. Il est relatif au métier et implémente les règles de gestion. Il ne contient pas les Entrées/Sorties de l’application.
- **L’Infrastructure** : Partie dite “port and adapters”, elle contient une couche d’abstraction (adapters) et implémente les spécificités liées aux entrées/Sortie (ports).

Cette Architecture permet de séparer ce qui est important, le coeur de l’application, le ***Domain*** de ce qui ne l’est pas, ***l’infrastructure***. Et rend le ***Domain*** indépendant de ***l’infrastructure***.

Nous pouvons changer d’avis sur l’infrastructure à tout moment car il n’y a pas de dépendances directes entre notre code métier et elle. Par exemple, pour une application de réalité virtuelle, nous souhaitons séparer le code qui dépend d’un casque particulier, du code métier qui sera le même peu importe le matériel utilisé. 

Il existe d’autres architectures similaires comme [l’**Onion Architecture** de Jeffrey Palermo](https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/) ou  [la **Lean Architecure**  de James Coplien et Trygve Reenskaug](https://www.amazon.com/Lean-Architecture-Agile-Software-Development/dp/0470684208/)

**Toutes ces architectures produisent un code métier :**
  - **Indépendant des frameworks**  
  - **Testable unitairement. Les règles métiers peuvent être testé sans élément extérieur (UI, base de données, serveur web, ...)**  
  - **Indépendant de l’UI (User Interface), L'interface doit pouvoir changer facilement, sans impacter le reste de l’application.**  
  - **Indépendant des bases de données (mySQL, SQL ,  MongoDB, …)**  
  - **Indépendant de tout élément extérieur, pour être résilient peu importe l’infrastructure sur laquelle on la déploie**  

La Clean Architecture a été créer et expliqué par Oncle bob dans un article de blog [“The Clean Architecture”](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html) en 2012. Plus tard, il écrit le livre, devenue référence, [“Clean Architecture: A Craftsman's Guide to Software Structure and Design” (2017)](https://www.amazon.fr/Clean-Architecture-Craftsmans-Software-Structure/dp/0134494164)

![Schema Clean Archi](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/TheCleanArchitecture.png)


Les couches extérieures peuvent dépendre des couches intérieures mais pas l’inverse (flèche sur le schéma).  

La Clean Architecture définit 4 couches : 

- **« Enterprise Business Rules » ou « Entities »**, parfois appelé **Domain** par association à l’architecture hexagonale, est la partie la plus importante d’une application. Elle correspond aux règles métiers. Au cours d’un développement logiciel, c’est la partie la plus sujette à modification.  
**Cette couche est fermée, elle ne peut communiquer qu’avec elle-même.**

- **“Application Business Rules” ou “Use Cases”** : Les *Use Cases* correspondent aux fonctionnalités de l’application, ainsi une fonctionnalité = un “Use Case” (ou cas d’usage).
 Ils servent à orchestrer les données entrante et sortante de la couche “Entities” et à faire le “passe-plat” entre les “Controllers” et les classes “Entities”. Les Use Cases ne doivent pas posséder de logique ou d’intelligence (ou elle doit être minimal).
**Les changements de cette couche ne doivent pas affecter les entities**

- **“Interface Adapters”** : contient les “Controllers” de l’application qui va être appelé en fonction des entrées/sorties mais n’en a pas connaissance.Dans un MVC (Model-View-Controller), cette couche correspond aux Views et Controllers. Elle va convertir les données qu’il reçoit pour les transmettre correctement aux use cases. 

- **“Framework & Drivers”** : contient les entrées/sorties de l’application et leurs codes associés, c’est ici que va être appelé les fonctions du Controller.


“Interface Adapters” et “Framework & Drivers” correspondent à **l’Infrastructure** de l’application. La partie *Infrastructure* est très liée au choix technique de l’application. L'intelligence de l’application est essentiellement située dans le *Domain* et *l’Infra*.

:warning: Attention : Parfois, le mot *Domain* est utilisé pour désigner deux couches, “Entities” et “Use Cases”. Ici, nous ferons toujours la distinction entre “Entities” et “Use Cases”, quand le mot “Domain” sera utilisé, il correspondra à “Entities”.

Pour Oncle Bob, un logiciel (Software) est abstrait au matériel sur lequel il est exécuté, à l’inverse du logiciel embarqué ou micrologiciel (firmware) qui est dépendant de la plateforme (par exemple, un développeur Android qui ne sépare pas logique métier et API Android dans son code réalise un firmware et non un software).

![schema HAL](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/layer_HAL.png)
*/*HAL : Hardware Abstraction Layer*   
 
La clean architecture à donc pour objectif de réaliser des *softwares*. Oncle Bob va même encore plus loin et ajoute le système d’exploitation(OS) dans son schéma.  

![schema OSAL](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/layer_OSAL.png)
*/*OSAL : Operating System Abstraction Layer*  

Ainsi un logiciel doit également être indépendant du système d’exploitation et du matériel sur lequel il sera déployé.   

**Note:** Avec Unity, il est assez facile d’être indépendant du système d’exploitation car un même code peut être déployer sur plusieurs système différents d’un simple clic, unity se charge pour nous de convertir les méthodes qu’on utilise en langage exploitable pour le système ciblé.

Le Main est la partie la plus sale de l’application, et c’est normal. 
> « Think of Main as a plugin to the application […] that sets up the initial conditions and configurations, gathers all the outside resources, and then hands control over to the high-level policy of the application. »
> Clean Architecture, Robert C. Martin

La Clean Architecture peut être utilisé sur tout type de projet. La maîtriser peut donc permettre d’avoir des repère peu importe le projet. Si l’on maîtrise la Clean Archi, il est facile de comprendre rapidement un code qui l’utilise.

[Retour au menu](Summary.md)

### Les interfaces 

Dans la Clean Architecture, les interfaces reviennent systématiquement. 
Une définition très scolaire d’une interface est qu’elle permet le polymorphisme or ce n’est qu’un exemple d’usage secondaire d’une interface. Les interfaces permettent de créer une abstraction entre deux objets, elles servent de contrats entre ces deux derniers. Cet usage correspond à l’inversion de contrôle (D de SOLID).

L’intérêt des interfaces est de séparer ce que vous voulez faire de comment vous allez le faire.
Elles peuvent aussi servir à “mapper” une dépendance extérieure ou une fonction lié à Unity (ex : Random / Instantiate) pour utiliser le vocabulaire choisi par l’équipe dans le Domain. Avec des interfaces, le code est plus facile à tester car on peut substituer la dépendance  (bouchon, voir chapitre “gestion des dépendances dans les TU” - link).

D’autres usages des interfaces : 
Ne pas attendre que la classe X soit finie pour développer la classe  Y. 
Y peut être développer en utilisant un bouchon pour X (mock ou stub).
Réaliser des démonstrations dans des conditions différentes de l’appli final (pas de connexion internet,  …) 

Dans le code :
```cs
public interface IExample {
    public void IMethod();
}
```

Pour créer une interface, il suffit d’utiliser le mot clé “interface” à la place de “class”. Ensuite l’interface va comporter que les déclarations des méthodes.

```cs
public interface ExampleImpl : IExemple {
    public void IMethod(){
        ...
    }
}
```

La classe ExampleImpl est une implémentation IExemple, c’est ici que l’on va définir le corps des méthodes.

[Retour au menu](Summary.md)

### La Clean Architecture dans Unity

La Clean Architecture peut s’appliquer à tout développement logiciel peu importe les outils utilisés.  Elle permet de créer des applications maintenable dans le temps mais peut également répondre à d’autre besoins : 
Créer une application qui comporte plusieurs versions (version PC, version mobile). Nous ne souhaitons pas dupliquer le code métier qui est identique aux deux versions mais pouvons modifier le code correspondant aux interactions et à l’interface (couche Infrastructure).
Faire abstraction de certains SDK (Vuforia, Oculus, SteamVR, …). Mettre un maximum de code en commun pour pouvoir facilement changer la techno utilisé par le projet.


Dans Unity, les couches vont se transformer en trois dossier :

 ![capture écran Unity](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/folders_unity.png)

Les “Entities” et les “Use Cases” ne doivent pas ou presque pas dérivée de MonoBehaviour. Les classes contenus dans ces couches sont donc des classes C# classiques.

Exemple :
```cs
public namespace Script.Domain {
  public class Airplane : MonoBehaviour {

      public string Name;

      // Use this for initialization
      void Start () {

      }

      // Update is called once per frame
      void Update () {

      }

      public void ToLand(Airport EndAirport)
      {
              ....
      }
  }
}
```
La classe “Airplane” n’utilisent pas les fonctions prédéfinies start, update, … Elle peut donc être simplifié
```cs
public namespace Script.Domain {
  public class Airplane  {

      public string Name;

      public void ToLand(Airport EndAirport)
      {
              ....
      }
  }
}
```

Dérivée de MonoBehaviour, est à éviter pour être moins dépendant de Unity. Quant au “Using UnityEngine”, il n’est pas dérangeant s’il est utilisé pour les types de données (Vector3, Quaternion, …) car ces types sont lié au langage induit par Unity.

Chaque fonctionnalité de l’application doit correspondre à un “Use Case”, ainsi en ouvrant le dossier “Use Cases”, vous devez être capable de comprendre quels sont les fonctionnalités implémentées de l’application en lisant les noms des scripts. Une bonne pratique est de commencer chaque nom de “Use case” par un **verbe**.
Chaque cas d’usage doit être indépendant et leurs limites avec les méthodes de MonoBehaviour est à redéfinir pour chaque projet en fonction de vos besoin.

**Lorsque l’on implémente la clean architecture, il faut que le code soit le plus flexible possible.**  
  
Un code doit pouvoir être déplacer ou renommer (classe, méthode, variable) à tout moment, pour ça je vous conseille de bien connaître les raccourci fourni par votre IDE. Beaucoup d’outils sont fournis dans les IDE pour refacto du code automatiquement après un changement. Par exemple, ils nous permettent de renommer une classe ou une variable et de transformer toutes les occurrences dans le code en conséquence. Renommer ou déplacer un élément ne doit pas devenir une corvée pour un développeur.
**Les noms évoluent avec la compréhension que l'on a du système que l’on développe**, les noms définis au début ne seront pas obligatoirement les mêmes à la fin. Lorsque l’on travaille en équipe, il faut que les noms utilisés parlent à tout le monde. Ainsi, les noms utilisé lors des stand-up ou des réunions sont une bonne piste. En cas de doute sur un nom, demander au reste de l’équipe ce qu’il en pense.
Si vous avez un doute sur l’emplacement d’un script, essayer de procéder par élimination. Il est en général plus simple de voir ou un code ne va pas, plutôt que ou il va. Vous pouvez également regarder le nom du script, si les mots “JSON”, “SQL”, .. apparaissent dans le Domain, il y a quelque chose qui cloche car ce sont des choix techniques, ce script ou une partie de ce script devraient donc se trouver dans l’Infra. De la même manière si un use case possède de l'intelligence (switch case, if, ...), le script devrait se trouver dans l’Infra ou dans le Domain.

Appliquer la Clean Architecture à un projet Unity, génère quelques **inconvénients** : 
- Moins de contrôle depuis Unity. Si l’on travail avec des designers : plus de scripts passe plat entre l’interface et le code métier (réglage des valeurs)
- Complique l’utilisation des coroutines : le script doit dériver de MonoBehaviour pour lancer une coroutine.
- Dans certains cas, il est difficile de découpler la vue 3D du comportement métier de l’application


[Retour au menu](Summary.md)
