# Unit Tests & TDD

## Test : introduction

Tests are important in software development because they assure a **minimal software quality**
A lot of software are designed without testing and the consequences are:
- a lot of **bugs**
- bugs in prod and difficult to solve
- software grow with patch and new features is based on dirty code

Most of the time, an untested code leads to **technical debt**.

![Image of test pyramid](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/img_pyramide_des_tests-en.png)

The tests pyramid presents different **type** of tests : 

- **HMI (Human-machine interface) tests** are expensive because the application must be launched entierely.
    This tests can be automatised but to long to execute. They cover a few of scenario.
    Sometimes, we haven't automatised test and we engage tester.

- **Integration tests** are easy. They are close to the code, so refactor is easier.
    They cover much code and they can impact directly by modification

- **Unit tests** are very easy to take in place when they make in parallel of the code.
    They are the base of testing software and they give feedback quickly.
    They cover the major part of the code and are cheap. 
    They can be automated and they must be quick to execute.

:warning: **Warning** : Think to separate unit test (quick) to integration test (slower).

We are interested to unit testing and test driven development (TDD)

Write a unit test serves to check the behaviour code and with TDD, write a unit test tell us how code must work.


[Back to summary](Summary.md)

## Unit tests

Unit tests are in a test project. And it must have the same architecture than the software who test.
A Sofware Class = A Test Class.

### Why you should write unit tests?

- To be sure not to go back
- Reduce bugs in features (new or old)
- Easy refactoring code and fear about modification or new features in the project.
- Have a quick feedback
- more "robust" application
- minimal documentation

In fact, more quickly you know tests fail, more quickly you can correct the problem and resolve are cheaper.

### What is a good unit test?

A good unit test:
- is fully automatised
- return systematically the same result
- test one concept or one logic 
- name is clear and easy to understand

### How writes a unit test?

Different naming convention exists:

- Should_ExpectedBehavior_When_StateUnderTest
- When_StateUnderTest_Expect_ExpectedBehavior

Be careful to not have the name of the method in the name of the test because
when you change the method name, the name of the test does not mean anything anymore. The test can be more difficult to maintain.   


A unit test expresses an intention, it tests a feature and no an implementation.

A test has decomposed into three parts :
- **Arrange** : initialize step
- **Act** : call of testing method
- **Assert** : verification step

When you write a test,  you can begin by the Assert step. The assert is the answer of **"What do you want to test?"**

[Back to summary](Summary.md)

## How to put unit tests into practice with Unity ?


### Unity Test Runner

For make unit test, Unity creates a tool call [Unity Test Runner](https://docs.unity3d.com/Manual/testing-editortestsrunner.html).  
To display Test Runner window, *Windows > Tests Runner*.

![Image of Test Runner](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture1_ouvertureOnglet.PNG)


There are two modes : 


- **PlayMode** :
  
	- Tests are executed in **many frames**
	- Behaviour Awake(), Start(), ... are automatically executed
	- Is used for **Integration tests**
 
	- [UnityTest] is executed like a **Coroutine**
 
	- Open a **tests scene** for execution of tests (:warning: **Save** your scene before launch tests)



- **EditMode** :
  
	- Tests are executed in **one frame**
 
 	- Call **explicitly**  Awake() and Start() methods, which requires to pass them in **public**.
	- **Tests must be placed in Editor folder**
  
	- [UnityTest] is executed in editor with **"Editor.Application.Update"**


Select your favorite mode and click on *“Create PlayMode/EditMode Test Assembly Folder”*.

*Unity* create *Test folder* with *“Tests.asmdef file” (asmdef =“assembly definition”)*.  
  


Once the folder has been created, go inside and click on *“Create Test Script in current folder”*.



![Image of Test Runner create script](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture2_btn_create_script.PNG)


A .cs file is created by Unity with the code bellow : 

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

Tests must display in the Test Runner:

![Image of Test Runner tests with test](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture3_tests.PNG)


if you click on "Run All", tests pass **green**.

![Image of Test Runner tests green tests](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture4_green_tests.PNG)


Now, if you replace the first test by : 

```cs
    [Test]
    public void NewTestScriptSimplePasses() {
        // Use the Assert class to test conditions.
        Assert.AreEqual(4, 3);
    }
```

this test must be **red** because 3 and 4 are not equals.

![Image of Test Runner tests red test explanation](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/UnityTestsRunner/Capture5_red_tests_with_error_msg.PNG)



Today, *Unity* allow to launch tests in [command lines](https://docs.unity3d.com/Manual/PlaymodeTestFramework.html).

You can launch tests with your IDE ([*Visual Studio*](https://visualstudio.microsoft.com/), [*Rider*](https://www.jetbrains.com/dotnet/promo/unity/) , ...) too.


[Back to summary](Summary.md)

#### Tests Scripts

The Class name must be prefixed or suffixed by "Test". By default, Unity uses the C# unit test framework **NUnit**. 

Before each method of test, the attribute [Test] must be written :

```cs
[Test]
public void Method_Test() {
 
    ...
 
}
```

With **UnityEngine.TestTools;** you can write **"Unity Tests"** :

```cs
[UnityTest]
public IEnumerator Methode_Unity_Test() {
 
    ...
    yield return null;
    ...
 
}
```

A *Unity Test* is a [**Coroutine**](https://docs.unity3d.com/Manual/Coroutines.html).
Usually Coroutines are used for make Asynchronous processing. For example, with a Coroutine, you can wait a frame (yield return null) or wait x seconds (yield return new WaitForSecondes(x)) before or during or after instructions.

To realise an assertion with NUnit, you must use the Assert class, few examples: 
```cs
    Assert.IsNotNull(object);
    Assert.IsTrue(boolean);
    Assert.AreEqual(value1, value2)
```

For "Assert.AreEqual", value1 is the expected value and value2 is the tested value. 
It's important for having good error message ("Expected "value1" But was "value2").


Unity have added their own assertions (with using UnityEngine.Assertions; and UnityEngine.TestTools.Utils;) like :

- "Assert.AreApproximatelyEqual" and "Assert.AreNotApproximatelyEqual" with a default tolerance equals to 0.00001f. They can be used for compare two float like position in x axis.
- "ColorEqualityComparer"
- "QuaternionEqualityComparer"
- "Vector2EqualityComparer" ,"Vector3EqualityComparer" et "Vector4EqualityComparer"


If your tests have the same "Arrange" part, therefore you can use [SetUp] and [TearDown] attributes. the [SetUp] method is executed **before** all [Test] methods and the [TearDown] method is executed **after** all [Test] methods

If we have:
```cs
public class MyTestClass {
  [Test]
  public void Method_Test_0() {
    //Arrange
    GameManager game = new GameManager();
    ...
    //Act
    ...
    //Assert
    ...
  }

  [Test]
  public void Method_Test_1() {
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

we can write : 

```cs
public class MyTestClass {
  
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
  public void Method_Test_0() {
    //Arrange
    ...
    //Act
    ...
    //Assert
    ...
  }

  [Test]
  public void Method_Test_1() {
    //Arrange
    ...
    //Act
    ...
    //Assert
    ...
  }

```

:warning: if you want instantiate a Monobehaviour object, it's better to write:

```cs
MyComponent myComponent = new GameObject.AddComponent<MonComponent>();

```

then:

```cs
MyComponent myComponent = new MonComponent();

```

Source: Unite Austin 2017 - Testing for Sanity: Using Unity's Integrated TestRunner, https://www.youtube.com/watch?v=MWS4aSO7HAo

[Back to summary](Summary.md)


## Test-driven developpement (TDD)

TDD or Test-driven development is a method which consists in writing tests before code.
With TDD, unit test is not to validate behaviour, but to specify the behaviour of the future code.

TDD can be resumed like this:

![schema cycle TDD](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/RED-GREEN-REFACTO%20cycle.png?token=AHjeDtvHOiXwwhctewLgG-4a0KSUEjf7ks5bYXxlwA%3D%3D)


In the book "The Clean Coder", Uncle bob defines the 3 laws of TDD (p.80) :
- 1. You are not allowed to write any production code until you have first written a failing test
- 2. You are not allowed to write more of a unit test than sufficient to fail -- and not compiling is failing
- 3. You are not allowed to write more production code that is sufficient to pass the currently failing unit test

For more precision about TDD: 
- [TDD good habits manifesto](https://github.com/neomatrix369/refactoring-developer-habits/blob/master/02-outcome-of-collation/tdd-manifesto/tdd-good-habits-manifesto.md)
- [Mastering TDD](https://codurance.com/practices/extreme-programming/2016/06/14/mastering-TDD/)

[Back to summary](Summary.md)
