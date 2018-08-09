# Versionning : Git & Unity

Si vous utiliser *git* pour versionner vos projets *Unity*, vous avez du remarquez que parfois les fichiers tels que les scènes et les *prefabs* subissent des régressions lors des merges. Deux personnes modifiant ainsi le même fichier en même temps provoquent un conflit que git, par défaut n’arrive pas à résoudre. Vous retrouvez alors votre scène entièrement vide ou votre *prefab* tout cassé. 
Voici la solution que j’ai choisi d’adopter.

## Créer un .gitignore

Il est important d’ajouter un fichier **.gitignore** à votre dépôt. Il permettra à git d’ignorer certains fichiers générés par *Unity* et par votre IDE (dans l’exemple Visual Studio, mais vous pouvez modifier le *gitignore* pour l’adapter aux outils que vous utilisez). Dans certains projets, il est pertinent d’ajouter des dossiers de votre projet afin de ne pas les enregistrer dans le dépôt en ligne, un *asset* de l’*asset store* par exemple (peut peser lourd dans l’archive, ce qui ralenti considérablement l’équipe lorsqu’elle doit mettre à jour le dépôt ou le récupérer).

<a href="https://github.com/github/gitignore/blob/master/Unity.gitignore" target="_blank">voir dépot .gitignore Unity</a>

[Retour au menu](Summary.md)

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
*[Ce merge tool peut être utilisé avec d’autres outils (Perforce, SVN, Mercurial, et SourceTree) , je vous conseille de vous référez à la documentation](https://docs.unity3d.com/Manual/SmartMerge.html)*  
  
Cet outil est très performant et permet de travailler à plusieurs sans se soucier de tout perdre (fonctionne avec les scènes et les prefabs).

### Comment ça marche ?

Continuez à utiliser *git* comme à votre habitude et si lors d’un *merge*, vous avez un conflit, effectuez la commande *“git mergetool”* pour appeller le *SmartMerge* qui s’occupera, dans la plupart des cas, de régler les conflits. Un exemple est donné ci-dessous, deux branches sont créées, *“Master”* et *“Another”*, Chacune modifie la scène *“sceneTest”*. Ensuite chacune est *commit* sur le dépôt et on essaye de “fusionner” les branches.  
A ce moment là, git nous dit qu’il y a un conflit et lorsque l’on ouvre la scène dans *Unity*, on s'aperçoit qu'elle est vide. On exécute alors *git mergetool* et on retrouve une scène qui possède tous nos objets.  

![Schema explicatif du Smart Merge](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/schemaSmartMergeV2.PNG?token=AHjeDgISOj3_CsXFl6gK4QxKd5kcyGPBks5baXFawA%3D%3D)

Le *SmartMerge* possède ses limites. Il ne peut pas résoudre seul le conflit lorsque vous toucher au même objet d'une scène. Dans ce cas, il vous demandera de choisir la bonne version (la scène actuelle, l’ancienne scène ou celle de votre collègue ?)

[Retour au menu](Summary.md)

## Gérer les fichiers volumineux

  Versionner des fichiers volumineux (image, objets 3D) peut s'avérer douloureux, *GitHub* vous empêche de pousser des fichiers de plus de 100 Mo et un dépôt *Git* contient toutes les versions de chaque fichier.  
  Les révisions multiples de fichiers volumineux augmentent les temps de clonage et de récupération pour les autres utilisateurs du dépôt.  
  
  *Git* demande à chaque utilisateur d'avoir autant d'espace libre sur un disque dur que d'espace consommé à tout moment. Par exemple, si une archive est de 1 Go, Git nécessite de 1 Go d'espace libre supplémentaire pour être disponible.  
  
  Il est conseillé de garder les fichiers suivant dans le dépôt : fichiers de code, assets qui ont besoin d’être versionné (graphiques), les fichiers de configuration (volumineux ou non) et de ne pas versionner les fichiers suivants : Bases de données, fichiers temporaires (journaux, log, …).   
  
  Si le dépôt que vous utilisez est compatible avec [Git LFS](https://git-lfs.github.com/), je vous recommande de l’utiliser. ([Git LFS](https://git-lfs.github.com/) est compatible avec GitHub / Bitbucket / Gitlab depuis la version 8). [Git LFS est une extension de Git et est open source](https://github.com/git-lfs/git-lfs?utm_source=gitlfs_site&utm_medium=repo_link&utm_campaign=gitlfs).  

Cependant, cette solution fonctionne **seulement** pour les fichiers **inférieurs à 2Go**.

### Comment ça marche ?

![Image Explicative de Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image9.png?token=AHjeDpsdDG7nmpaEzQkxpTVyQp2cHdTmks5bWG9nwA%3D%3D)

[Git LFS](https://git-lfs.github.com/), va *tracker* les fichiers que vous voulez, soit avec leurs noms, soit avec leurs extensions, soit avec leurs emplacements. Ensuite, ces fichiers seront automatiquement stockés sur un serveur (*Large File Storage*) et seulement le lien de votre fichier sera gardé. Attention, ce système ne versionne pas les fichiers trackés. Mais avez-vous vraiment besoin d’un *versionning* de vos *assets* 3D ou de vos éléments UI ? Généralement déjà versionnés en amont par les Designers.

**Pour installer git LFS, lancer la commande :**
```sh
git lfs install
```
Git LFS  devrait s’installer automatiquement, si c’est le cas, la commande devrait retourner ce message :
```sh
>Updated git hooks.
>Git LFS initialized.
```
**Utilisez la commande suivante pour tracker les fichiers avec les extensions voulu (remplacer le *.extension par *.png, *.fbx, ...)**

```sh
git lfs track "*.extension" 
>Tracking "*.extension"
```

**Assurez vous que votre projet possède un *.gitattributes***

```sh
git add .gitattributes
```
et voilà le tour est joué, à présent vous pouvez ajouter vos fichiers et *commit* comme vous le faisiez avant, la différence est que *Git* va envoyer les fichiers trackés sur [Git LFS](https://git-lfs.github.com/).  

Si vous utilisez github, la notification ***“stored with Git LFS”*** devrait apparaître dans l’interface web lorsque vous ouvrez votre fichier.

![Image of Github Stored with Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image7.png?token=AHjeDnlpMeQpTc27ikncn_j53g50GMEOks5bWG4gwA%3D%3D)

Pour comprendre le fonctionnement de [Git LFS](https://git-lfs.github.com/), je vous conseille de regarder la vidéo proposé par bitbucket : 

[![video Btbucket Git LFS](http://img.youtube.com/vi/9gaTargV5BY/0.jpg)](http://www.youtube.com/watch?v=9gaTargV5BY "Git LFS explain")


D’autres solutions existes avec *git* : *Git Annex*, *Git Fat* , *Git Media* , *Git Bigstore*, *Git Sizer*, … Ces dernières sont moins documentées, plus difficiles à mettre en place et à prendre en main et semblent moins adaptées à un projet *Unity*.

[Retour au menu](Summary.md)
