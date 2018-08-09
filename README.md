
### [Consulter le site en français](Fr/Summary.md) 

# Introduction

# __Plan :__

- [Versionning : Git & Unity](#versionning--git--unity)
  - [.gitignore](#gitignore)
  - [Store file in YAML](#store-file-in-yaml)
  - [Merge with Smart Merge](#merge-with-smart-merge)
  - [Large files (image, 3D object, ...)](#large-files)
        
- [Unit Tests & TDD](#tests-&-tdd)
  - [Tests : Introduction](#tests--introduction)
  - [Unit Tests **(In progress)**](#unit-tests)
    - [Why you should be write unit tests ?](#why-you-should-be-write-unit-tests-)
    - [What is a good unit test ?](#what-is-a-good-unit-test-)
    - [How write a unit test ?](#how-write-a-unit-test-)
    - [How to put unit tests into practice with Unity **(Soon)**](#)
  - [Test-driven-developpement (TDD) **(Soon)**](#test-driven-developpement-tdd)
 
- [Management of dependencies **(Soon)**](#)
- [Clean Code **(Soon)**](#clean-code)
- [Architecture **(Soon)**](#architecture)


# Versionning : Git & Unity

You want use *git* for your *Unity* project ? But sometimes, scenes or prefab crash ! In fact, two person modify same file in same time provoke many conflict and by default, git don't solve this problems.   
You open your scene and you see that it is empty. A solution exist for that and this is what I present below.

## .gitignore

This is important to add a **.gitignore** file to your project. This file will allow to ignore unity tempory file and IDE tempory file.  
Moreover, you can add folder to ignore, for exemple, an asset folder from the *asset store* can weight a lot and slow down your archive.

[see .gitignore Unity](https://github.com/github/gitignore/blob/master/Unity.gitignore)

## Store file in YAML

![Image of Unity Project Settings](https://s3.amazonaws.com/gamasutra/UnityVersionControlSettings.png)

Pass asset serialization mode in “Force Text”. This option convert binary file to YAML file.

## Merge with Smart Merge

Since *Unity* 5.0, a **UnityYAMLMerge tool** is include with its installation.  
For use them, Copy/Paste the following lines in  **.git/config** file.

```
[merge]
tool = unityyamlmerge
[mergetool "unityyamlmerge"]
cmd = 'C:\\Program Files\\Unity\\Editor\\Data\\Tools\\UnityYAMLMerge.exe' merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
trustExitCode = false
keepTempories = true
keepBackup = false
```

Modify the "*C:\\ ...*" link to adapt with your Unity location. If you work in team, you can use the same location for easy maintenable problem.  
**Warning : don't forget to use '\\\\' and no '\\' ou '/'.**  

*[this merge tool can be used with other tools (Perforce,SVN, Mercurial, SourceTree) , see documentation](https://docs.unity3d.com/Manual/SmartMerge.html)*  

This tool is very perform and allowed to work in team without minim risk of loss scene or prefab.
  
### How it's works ?

use git as usual and when your merge crash, use the smartmerge command :  "git mergetool". 

In this following exemple, Two branch are create "Master" and "Another". Each branch modify "sceneTest" scene and commit. After, we try to merge and it crash. And now,  Our "sceneTest" is empty. Then we tap "git mergetool" and we find our scene with the twice modification.

![Schema of Smart Merge](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/schemaSmartMergeVEn.PNG?token=AHjeDo_HuX2ADdAr7MqXYrBe-x9zlMyXks5bXzY1wA%3D%3D)

Limits of Smartmerge :  
  - Modify the same object in a scene  
  - Modify the same property of prefab  
  
In this cases, you will be able to choose the good version (your scene or colleague scene).  


## Large files

  Versionning large files (.png, .fbx, ...) can be hard. Github don't accept files that weigh more than 100 MB and git keep all versions of all your files. Versionning large files make clone and recuperation for other user longer.  
  Git imposed to each user to have as much free space as used space (For exemple, if your project is 1 GB, Git need 2 GB).  
  If your project is compatible with [Git LFS](https://git-lfs.github.com/), use it ! (GitHub / Bitbucket / Gitlab since 8.0 version)
  [Git LFS is a open source git extention](https://github.com/git-lfs/git-lfs?utm_source=gitlfs_site&utm_medium=repo_link&utm_campaign=gitlfs).  
  
  :!: **Warning**, this solution works **only** for file **less than 2 GB**.
  
### How git LFS works ?

![Image Explicative de Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image9.png?token=AHjeDpsdDG7nmpaEzQkxpTVyQp2cHdTmks5bWG9nwA%3D%3D)

[Git LFS](https://git-lfs.github.com/), track chosen files with name, extensions, folder location, ...  
Chosen files will be stock in a LFS server automatically and git keep only links to their files. This system don't versionning track files but in most project, assets 3D or 2D are already versioned by Designers.  

**For install git LFS, write the command :**

```
$ git lfs install
```
Git LFS is install automatically and return this message :
```
>Updated git hooks.
>Git LFS initialized.
```
**use the follow command for track your file (here all .png file)**
```
$ git lfs track "*.extension" 
>Tracking "*.png"
```

**Add a *.gitattributes* to your project**

```
$ git add .gitattributes
```

if you use *GitHub*,the notification “stored with Git LFS” appears in your traked files.

![Image of Github Stored with Git LFS](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/image7.png?token=AHjeDnlpMeQpTc27ikncn_j53g50GMEOks5bWG4gwA%3D%3D)


For understand [Git LFS](https://git-lfs.github.com/), you can watch the bitbucket video : 

[![video Btbucket Git LFS](http://img.youtube.com/vi/9gaTargV5BY/0.jpg)](http://www.youtube.com/watch?v=9gaTargV5BY "Git LFS explain")


Other solution for large file in git project : Git annex, Git fat , Git media , Git Bigstore, Git sizer… 
But they have few or no documentation and they don't really adapt to Unity project's needs.


# Tests & TDD

## Test : introduction

Tests are important in software developpement because they assure a **minimal software quality**
A lot of software are designed without testing and the consequences are:
- a lot of **bugs**
- bugs in prod and difficult to solve
- software grow with patch and new features is base on dirty code

Most of time, an untested code leads to **technical debt**.

![Image of test pyramid](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/img_pyramide_des_tests-en.png)

The tests pyramid present different **type** of test : 

- **HMI (Human-machine interface) tests** are expensive because the application must be launch entierrelly.
    This tests can be automatised but too longer to execute. They cover a few of scenario.
    Sometimes, we have'nt automatised test and we engage tester.

- **Integration tests** are easy. They are close to the code, so refacto is easier.
    They cover much code and they can impact dirrecly by modification

- **Unit tests** are very easy to take in place when they make in parrallel of the code.
    They are the base of testing and they give software feedback quickly.
    They cover the major part of code and are cheap. 
    They can be automated and they must be quick to execute.

:warning: **Warning** : Think to separate unit test (quick) to integration test (slower).

We interest to unit testing and test driven developpement (TDD)

Write a unit test serves to check behaviour code and with TDD, write a unit test tell us how code must work.

//Add source "pour en savoir plus"

## Unit tests

Unit tests is in a test project. And it must have the same architecture than the software who test.
A Sofware Class = A Test Class.

### Why you should be write unit tests ?

- To be sure
- Reduce bugs in features (new or old)
- Easy refactoring code and fear about modification or new features in the project.
- Quickly feedback
- more "robust" application
- minimal documentation

In fact, more quickly you know test fail, more quickly you can correct the problem and resolve be cheaper.

### What is a good unit test ?

A good unit test :
- is fully automatised
- return systematically the same result
- test one concept or one logic 
- name is clearly and easy to understand

### How write a unit test

Different naming convention exist :

- Should_ExpectedBehavior_When_StateUnderTest
- When_StateUnderTest_Expect_ExpectedBehavior

Be carreful to not have the name of the method in the name of the test because it's more difficult to maintaain.
When you change the method name, the name of the test does not mean anything anymore.
A unit test express an intention, it test a feature and no an implementation.

A test have decomposed in three part :
- **Arrange** : initialize step
- **Act** : call of tested method
- **Assert** : verification step

When you write a test,  you can begin by the Assert step. The assert is the answer of "What do you want to test?"


## In practice

The Class name must be prefix or suffix by "Test".







## Test-driven-developpement (TDD)
# Clean Code
# Architecture



