# Versioning : Git & Unity

You want to use *git* for your *Unity* project, but sometimes, scenes or prefab crash ! In fact, two people modify the same file in the same time provoke many conflicts and by default, git don't solve this problem.   
You open your scene and you see that it is empty. A solution exists for that and this is what I present below.

## Create a .gitignore

This is important to add a **.gitignore** file to your project. This file will allow to ignore unity temporary file and IDE temporary file.  
Moreover, you can add a folder to ignore, for example, an asset folder from the *asset store* can weigh a lot and slow down your repository.

[see .gitignore Unity](https://github.com/github/gitignore/blob/master/Unity.gitignore)

## Store file in YAML

![Image of Unity Project Settings](https://s3.amazonaws.com/gamasutra/UnityVersionControlSettings.png)

Pass asset serialization mode in “Force Text”. This option converts binary files to YAML("YAML Ain’t Markup Language") files.
Git has more performed with YAML, but sometimes is not enough.  

By default, Unity store files in Binary format because it is lighter and more optimized.  


## Merge with Smart Merge

Since *Unity* 5.0, a **UnityYAMLMerge tool** is included with its installation.  
To use them, Copy/Paste the following lines in  **.git/config** file.

```
[merge]
tool = unityyamlmerge
[mergetool "unityyamlmerge"]
cmd = 'C:\\Program Files\\Unity\\Editor\\Data\\Tools\\UnityYAMLMerge.exe' merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
trustExitCode = false
keepTempories = true
keepBackup = false
```

Modify the "*C:\\ ...*" link to adapt with your Unity location. If you work in a team, you can use the same location for easy maintainable problem.  
:warning: **Warning : don't forget to use '\\\\' and no '\\' or '/'.**  

*[This merge tool can be used with other tools (Perforce, SVN, Mercurial, SourceTree), see documentation](https://docs.unity3d.com/Manual/SmartMerge.html)*  

This tool is very performing and allowed to work in team without risk of loss *scene* or *Prefab* files.
  
### How it's works ?

Use git as usual and when your *merge* command crash, use the smartmerge command:  "git mergetool". 

In this following example, Two branches are creating "Master" and "Another". Each branch modify "sceneTest" scene and commit. After, we try to merge and it crashes. And now,  Our "sceneTest" is empty. Then we tap "git mergetool" and we find our scene with the twice modification.

![Schema of Smart Merge](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/schemaSmartMergeVEn.PNG?token=AHjeDo_HuX2ADdAr7MqXYrBe-x9zlMyXks5bXzY1wA%3D%3D)

Limits of Smartmerge :  
  - Modify the same object in a scene  
  - Modify the same property of prefab  
  
In this case, you will be able to choose the best version (your scene or colleague scene).  


## Manage Large files

  Versioning large files (.png, .fbx, ...) can be hard. GitHub don't accept files that weigh more than 100 MB and git keep all versions of all your files. Versioning large files, make clone and recuperation for another user longer.  
  Git imposed to each user to have as much free space as used space (For example, if your project is 1 GB, Git need 2 GB).  
  If your project is compatible with [Git LFS](https://git-lfs.github.com/), use it ! (Git LFS can be used with GitHub / Bitbucket / Gitlab since 8.0 version)
  [Git LFS is an open source git extension](https://github.com/git-lfs/git-lfs?utm_source=gitlfs_site&utm_medium=repo_link&utm_campaign=gitlfs).  
  
  :warning: **Warning**, this solution works **only** for file **less than 2 GB**.
  
### How git LFS works ?

![Image Explicative de Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image9.png?token=AHjeDpsdDG7nmpaEzQkxpTVyQp2cHdTmks5bWG9nwA%3D%3D)

[Git LFS](https://git-lfs.github.com/) track chosen files by name, extensions, folder location, ...  
Chosen files will be stocked in a LFS server automatically and git keep only links to their files. This system don't versionning track files, but in most projects, assets 3D or 2D are already versioned by Designers.  

**For install git LFS, write the command :**

```
$ git lfs install
```
Git LFS is installed automatically and return this message :
```
>Updated git hooks.
>Git LFS initialized.
```
**use the following command to track your file (here all .png file)**
```
$ git lfs track "*.extension" 
>Tracking "*.png"
```

**Add a *.gitattributes* to your project**

```
$ git add .gitattributes
```

if you use *GitHub*, the notification “stored with Git LFS” appears in your tracked files.

![Image of Github Stored with Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image7.png?token=AHjeDnlpMeQpTc27ikncn_j53g50GMEOks5bWG4gwA%3D%3D)


For understand [Git LFS](https://git-lfs.github.com/), you can watch the Bitbucket video: 

[![video Btbucket Git LFS](http://img.youtube.com/vi/9gaTargV5BY/0.jpg)](http://www.youtube.com/watch?v=9gaTargV5BY "Git LFS explain")


Another solution for large file in git project : *Git annex, Git fat , Git media , Git Bigstore, Git sizer*, … 
But they have little or no documentation and they don't really adapt to *Unity* project's needs.

