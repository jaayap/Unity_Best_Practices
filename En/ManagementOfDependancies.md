# Management of dependancies

## Dependancies in unit tests

In practice, unit test must never rely on external things ( Web Service, Database,...).
Indeed, this can make unit tests more difficult to maintain over time and this will tend to lengthen the execution time of the test.

To be sure to test a single behaviour without any dependance, it's possible to create "plugs" (mock or stub) to replace a class.
This makes it possible to avoid a null exception by informing a substitution object or to verify that method is called without executing its code.

What is the difference between Stub and Mock?

- A Stub is an object that you can control, which serves as a substitute for an external dependency (example: a fake web service that always returns the same result).
- A Mock is a substitute object in the system, which decides whether a unit test pass or fail (example: a database waiting for a specific request)

## Unit test and dependancies in Unity

For use mocks or subtitutes with Unity, you can install *NSubstitute*. For this, you have just to place the file [NSubstitute.dll](https://github.com/jaayap/Unity_Best_Practices/tree/master/NSubstitute/dll) in your Unity project folder.

**Example :**

We want to be sure that method *InitializeObject()* of *GameManager.cs* is called with the good parameter with the Start() method of *ObjectBehaviour.cs*
 
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

[Retour au menu](Summary.md)
