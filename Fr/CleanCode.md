# Propreté du code avec Clean Code

> « Il existe deux façons de concevoir un logiciel : une façon est de le rendre si simple qu'il n'a visiblement aucun défaut, et une autre est de le rendre si complexe qu'il n'y a pas de défaut visible. La première façon est de loin la plus difficile. » 
> Hoare

**Qu’est-ce qu’un code propre ?** 

![Image intro](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/wtfm.jpg)  

- Un code propre doit être agréable à lire.
- Un code propre doit prêter attention aux détails et doit être focalisé.
- Chaque fonction, chaque classe, chaque objet doit avoir une attitude simple qui reste entièrement indépendante, et non polluée par les détails non nécessaires.
- Un code non testé n’est pas un code propre, peu importe s’il est lisible et accessible.
- Ne contient pas de duplication.
- Évident, simple et convaincant: quand on lit un code propre, on ne doit pas dépenser trop d’effort pour le comprendre.

La propreté du code définit la **qualité logicielle**.

**Pourquoi avoir un code simple et propre est si important ?**

Le code est le premier **environnement de travail** du développeur. Il est donc plus agréable pour le développeur de travailler avec une bonne compréhension du système et de pouvoir le faire évoluer sans tout réécrire. En effet un code propre permet de gagner du temps lorsqu’il faut faire évoluer le projet.  De plus, la qualité interne du code (structure) **influe** directement sur la qualité externe (fonctionnement) du projet. Avoir un code propre sert à **maintenir la maintenabilité**.

Un code propre permet donc de **faire évoluer** et de **maintenir** un projet.

[Retour au menu](Summary.md)

## Par où commencer ? 

### Les noms

Lorsque nous développons, bien trop souvent nous oublions de réfléchir aux noms que nous donnons aux variables, aux méthodes et parfois même aux classes.  Ce qui peut entraîner des incompréhensions du système par d’autres développeurs et une relecture du code plus longue. Un mauvais nommage peut également impacter la maintenabilité d’une application.

Lorsque vous programmer : 

- Utilisez des noms qui révèlent l’intention (= noms descriptifs), pour vous aider posez vous la question “à quoi va servir cette méthode/classe/variable ? “
- Essayer de choisir qu’un seul mot par concept. Par exemple, si une classe sert à instancier un objet ne mélangez pas, “create”, “instantiate”, generate”, … Mais choisissez un seul mot qui  correspond à ce que vous faites ou le mot utilisé par le métier.
- Choisissez des mots facile à prononcer.
- Evitez de placer des valeurs sans les nommer (magic number)

[Retour au menu](Summary.md)

### Les commentaires

Dans un code, les commentaires sont importants mais parfois utilisés de manière abusives (écrire un commentaire pour expliquer tout le fonctionnement d’un algorithme plutôt que de le réécrire de manière simple et compréhensible). 

Les bons commentaires sont des commentaires facultatifs, si possible, la compréhension du code ne doit pas être dépendante des commentaires. Ils doivent servir de complément d’information, d’avertissement sur les conséquences ou à expliquer une intention.

[Retour au menu](Summary.md)

### La loi de Déméter

La loi de Déméter (en anglais Law of Demeter ou LoD) peut s’énoncer de la manière suivante :
“Chaque unité doit avoir une connaissance limitée des autres unités, chaque unité doit voir que les unités étroitement liées à l’unité actuelle.”

Cela signifie qu’un objet devrait faire le moins d’hypothèse possible à propos des autres structures que lui même. 

En pratique, cela veut dire qu’une méthode peut invoquer les méthodes :
- de l’objet lui-même
- des paramètres de la méthode
- des objets créés / instanciées dans la méthode
- des propriétés et champs de l’objet 

L'avantage de suivre la règle de Déméter est que le logiciel est plus maintenable et adaptable. Puisque les objets sont moins dépendants de la structure interne des autres objets, ceux-ci peuvent être changés sans changer le code de leurs appelants.

**Exemple**

**Code qui ne respecte pas la loi de Déméter**

```cs
public class VideoService {
    public IVideoRepository videoRepository;
    
    public void Play(string videoId) {
        …
        var video = this.videoRepository.GetVideo(videoId);
        var format = video.GetFormat();
        var codec = format.GetCodec();
        codec.DecodeVideo(video.GetContent());
    }
}
```
**Refacto de ce code pour qu'il respecte la loi de Déméter**

```cs
public class VideoService {
    public IVideoRepository videoRepository;
    
    public void Play(string videoId) {
        …
        var video = this.videoRepository.GetVideo(videoId);
        var codec = video.GetCodec();
        DecodeVideo(video, codec);
    }

    private void DecodeVideo(Video video, Codec codec) {
        codec.DecodeVideo(video.GetContent());
    }
}

public class Video {

    public void GetCodec() {
        return this.GetFormat().GetCodec();
    }
}
```

[Retour au menu](Summary.md)

## Principes et méthodes

Depuis les années 1990/2000, plusieurs méthodes et principes ont été définis pour apporter une ligne directrice dans le développement de logiciel plus fiable et plus robuste. Parmis eux, beaucoup proviennent des pratiques eXtreme Programming et Clean Code. Ces pratiques sont populaires aujourd’hui car elles répondent aux questions soulevé par l’agilité.

Ainsi, les règles d’un design simple sont définis par *Kent Beck* en 1990 dans son livre *“Extreme Programming Explained”*, ces règles sont les suivantes : 

1. les tests passent (signifie que le code fonctionne correctement et sous entend que le code doit être couvert par les tests)
2. L’intention du code est claire
3. **DRY** principle (Don’t Repeat Yourself) :      
       Il n’y a pas de duplication de code
4. Le code a le moins d’éléments possible :
    - **KISS : Keep it Simple & Stupid**             
                Principe de la responsabilité unique (un élément possède qu'une seule raison d'être modifié)
    - **YAGNI  : You ain’t gonna need it**            
                Essayer d'anticiper les problèmes futurs, n'est pas une bonnes idée. Il est préférable de s'occuper du présent et de ne pas faire d'hypothèse sur ce que l'application pourrait utiliser.


Quelques année plus tard, *Robert Cecil Martin alias Oncle Bob* met en avant plusieurs grands principes : Les principes *SOLID* et les principes composants. 

**Les principes SOLID sont définis comme suit :**

- **<mark>S</mark>RP (Single Responsibility Principle)**      
Ne faire qu’une seule chose mais la faire bien. Une classe doit avoir une et une seule raison de changer. 

- **<mark>O</mark>CP (Open-Closed Principle)**      
Une classe doit être ouverte à l'extension mais fermée à la modification (plugins)

- **<mark>L</mark>SP (Liskov Substitution Principle)**     
Toutes implémentation d’une interface doit pouvoir se substituer à une autre

- **<mark>I</mark>SP (Interface Segregation Principle)**    
Une classe ne doit implémenter une interface que si elle à réellement le besoin de 
remplir son contrat 

- **<mark>D</mark>IP (Dependency Inversion Principle)**     
Le code ne doit pas interagir directement avec l'extérieur mais doit passer par des 
abstractions (et vice versa).


**Les trois principes composants**

Les composants sont les unités du déploiement. Ils sont les plus petits morceaux qui peuvent être déployés comme une partie du système (fichier .jar / fichier .gem/ .DLL). 

Les trois principes sont les suivants : 

- **REP (Reuse/Release Equivalence Principle)**    
Créer un code qui peut être réutilisé (plugin, code générique avec le moins de dépendance possible)
“the granule of reuse is the granule of release” 
"Le germe de la réutilisation est le germe de la libération"

- **CCP (Common Closure Principle)**     
Un composant (une classe, une méthode, ...)  ne doit pas avoir plusieurs raisons de changer

- **CRP (Common Reuse Principle)**        
“Don’t force users of a component to depend on things they don’t need.” 
“Ne forcez pas les utilisateurs d’un composant à dépendre d’une chose dont ils n’ont pas besoin” 

[Retour au menu](Summary.md)