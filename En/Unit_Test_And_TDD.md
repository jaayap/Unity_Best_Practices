# Unit Tests & TDD

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

### How write a unit test ?

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


## How to put unit tests into practice with Unity ?

The Class name must be prefix or suffix by "Test".







## Test-driven-developpement (TDD)