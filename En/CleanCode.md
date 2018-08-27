# Clean code
 
> « There are two ways of constructing a software design: One way is to make it so simple that there are obviously no deficiencies, and the other way is to make it so complicated that there are no obvious deficiencies. The first method is far more difficult. »
> Hoare.
 
## What is a clean code?
 
![image focus shift wtf/min](https://raw.githubusercontent.com/jaayap/Unity_Best_Practices/master/Img/wtfm.jpg) 

 
- A clean code must be pleasant to read and easy to understand
- A clean code must be focus
- Each method, class, … must have a simple and independent behavior and not polluted by unnecessary details.
- A code without test is not a clean code.
- A clean code has not duplication
 
The clean of the code define the software quality.
 
## Why a clean code is so important?
 
The code is the working environment of the developer. It is therefore more pleasant for the developer to work with a good understanding of the system and to be able to make it evolve without rewriting everything.
Indeed, a clean code saves time when it is necessary to evolve the project. In addition, the internal quality of the code (structure) directly affects the external quality (operation) of the project. Having a clean code serves to maintain maintainability.
 
A clean code allows to evolve and maintain a project.
 
## The starting

### Names
 
When we develop, we forget to think about names we give to variables, methods and classes. This can lead to misunderstandings of the system by other developers and a longer proofreading of the code. Bad naming can also impact the maintainability of an application.
 
When you program:
- Use names that reveal intent (= descriptive names). For help you, you can ask “What will this method/class/variable do for?”
- Try to choose one word per concept. For example, if a class is used to instantiate an object, don’t mix “create”, “instantiate”, “generate”, … But choose a single word. If possible choose a word to what you do, or the word used by the business.
- Choose word easy to pronounce.
- Avoid magic number (place value without name).
 
### Comments
 
In a code, comments are important but sometimes abusively used (write a comment to explain the whole operation of an algorithm rather than rewrite it in a simple and understandable way).
Good comments are optional comments, code understanding should not be dependent on comments. They must serve as additional information, warning about the consequences or explaining an intention.
 
## The Law of Demeter (LoD)
 
The law of Demeter can be succinctly summarized in each of the following ways:
- Each unit should have only limited knowledge about other units: only units "closely" related to the current unit.
- Each unit should only talk to its friends; don't talk to strangers.
- Only talk to your immediate friends.
 
 
In practice, this means that a method can invoke methods:
- of the object itself
- method’ s parameters
- objects instantiated in the method
- properties and fields of the object
 
The advantage of following Demeter's law is that the software is more maintainable and adaptable. Since objects are less dependent on the internal structure of other objects, they can be changed without changing the code of their callers.
 
# Example:

Sample of code without LoD:

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

The same code with LoD:

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

## Principles
 
Since 90s, many principles are defined for help maintainable software development. Many of them come from the eXtreme Programming and Clean Code practices.
 
Simple design rules are defined by Kent Beck in 90s in his book “Extreme Programming Explained”. These rules can express like this:
- Tests pass
- Reveals intention
- No duplication (DRY (Don’t Repeat Yourself) principle)
-  Fewest elements:
  -  KISS: Keep It Simple & Stupid (an element has once reason to be modified)
  -  YAGNI: You Ain’t Gonna Need It (Trying to anticipate future problems is not a good idea. It’s better to take care of the present and not make assumptions about what the application could use).
 
watch [this site](https://martinfowler.com/bliki/BeckDesignRules.html) for more information about simple design. 
 
Several years later, Uncle Bob talks about SOLID principles and Component principles.
 
### SOLID principles**
 
- **SRP (Single Responsibility Principle)**
 
An active corollary to Conway’s law: The best structure for a software system is heavily influenced by the social structure of the organization that uses it so that each software module has one, and only one, reason to change.
 
- **OCP (Open-Closed Principle)**  
 
Bertrand Meyer made this principle famous in the 1980s : « A software artifact should be open for extension but closed for modification »
The gist is that for software systems to be easy to change, they must be designed to allow the behavior of those systems to be changed by adding new code, rather than changing existing code.  
 
- **LSP (Liskov Substitution Principle)**  
 
Barbara Liskov’s famous definition of subtypes, from 1988. In short, this principle says that to build software systems from interchangeable parts, those parts must adhere to a contract that allows those parts to be substituted one for another.  
  
 
- **ISP (interface Segregation Principle)**  
 
This principle advises software designers to avoid depending on things that they don’t use.
Language issue rather than an architecture issue.  
 
- **DIP (Dependency Inversion Principle)**  
 
The code that implements high-level policy should not depend on the code that implements low-level details. Rather, details should depend on policies.  
 
### The Three Component principles
 
Components are the units of deployment. They are the smallest entities that can be deployed as part of a system. In Java, they are jar files. In Ruby, they are gem files. In .Net, they are DLLs. In compiled languages, they are aggregations of binary files. In interpreted languages, they are aggregations of source files. In all languages, they are the granule of deployment.
 
Three principles can be defined like this:
 
- **REP (Reuse/Release Equivalence Principle)**  
  “the granule of reuse is the granule of release”
 
- **CCP (Common Closure Principle)**  
  “Gather into components those classes that change for the same reasons and at the same times. Separate into different components those classes that change at different times and for different reason”  

A component (class, method, …) should not have multiple reasons to change
 
- **CRP (Common Reuse Principle)**  
 "Don’t force users of a component to depend on things they don’t need."
 
 
 
 
 
 
 
 
 
