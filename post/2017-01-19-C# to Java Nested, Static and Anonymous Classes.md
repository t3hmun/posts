{"description":"Java has a significantly different concept of nested classes to C#. This article investigates the meaning of Nested Static and Anonymous classes in both Java and C#"}

## What what and what?

Java has a significantly different concept of nested classes to C#.
As a result static class and anonymous class have different meanings.
All kinds of nested classes have different abilities.

As a C# dev working with Java you will want to read the following docs:

* [Nested Classes](http://docs.oracle.com/javase/tutorial/java/javaOO/nested.html)
* [Local Classes](http://docs.oracle.com/javase/tutorial/java/javaOO/localclasses.html)
* [Anonymous Classes](http://docs.oracle.com/javase/tutorial/java/javaOO/anonymousclasses.html)

Those three docs give a good overview of what these features are in Java.
This article contrasts these features to C# and looks a bit more at what is going on.


## Nested Classes

In C# a nested class is mostly used just like any other top level class.
The difference is that it is accessed through the name of the containing class.

```csharp
internal class Containing
{
    private readonly string aPrivateVar = "Hi.";

    public class Nested
    {
        public Nested()
        {
            // There is no such a thing as `Containing.this` in C#
            // Instead pass the `Containing` object in through the ctor (or a method, property etc..).
            Console.WriteLine("Nested ctor.");
        }

        public void FeelsLikeAnEncapsulationViolation()
        {
            // Nested class can access private vars from any instance of its Containing class.
            var instanceOfContaining = new Containing();
            Console.WriteLine(instanceOfContaining.aPrivateVar);
        }
    }
}

```

In C# the nested class can only get access to an containing class instance if a reference is provided.
The nested instance is independent from any instance of the containing class.

There is one special feature of nested classes in C#; 
it can access private and protected members of any references to instances of its containing class.

Java is a completely different beast.
Nested classes in Java are implicitly created with a reference to the containing class.
They cannot be created without an instance of the containing class to link to.

```java
public class Container {

    private String privateVar = "Hello.";

    public static void main(String[] args) {

        // This next commented line is invalid in Java.
        //Nested nestedInstance = new Nested();

        Container containerInstance = new Container();
        Nested nestedInstance = containerInstance.new Nested(" Created externally.");
        nestedInstance.AccessPrivateVarFromContainer(); // Prints `Hello. Created externally.`.
        containerInstance.CreateNestedInstance(); // Prints `Hello, Created internally.`.
    }

    private void CreateNestedInstance(){
        Nested nestedInstance = new Nested(" Created internally.");
        nestedInstance.AccessPrivateVarFromContainer();
    }

    public class Nested{
        private final String id;

        Nested(String id){
            this.id = id;
        }

        void AccessPrivateVarFromContainer(){
            String x = Container.this.privateVar;
            System.out.println(x + id);
        }
    }
}
```

## Static Classes

In C# a static class is a class that cannot be instantiated and hence can only contain static members.

In Java static class means something totally different.
To start with Java does not have a top level static class, they are always nested.
The Java nested static class is a nested class without an implicit reference to the containing class.
This is practically the same as a C# non-static nested class.

## Anonymous Classes

C# has anonymous types, which are just a collection of readonly properties.
Methods and other things cannot be added to anonymous types.

```csharp
var anon = {name = "monkeys", someThingElse = otherObject};
Console.WiteLine(anon.name)// monkeys
Console.WriteLine(anon.someThingElse) // The `.ToString()` of `otherObject`.
```

Java anonymous classes are again a kind of inner class.
An anonymous class is declared in an expression that looks like a normal new class followed by `{}`.
The expression creates a subclass with the overridden methods defined in the braces.
 

```java
public class Main {

    public static void main(String[] args) {
	    Main main = new Main();
	    main.DoVoodoo(); // prints `Hello \n wo0o0o0o \n Hi`
    }

    public void SayHello()
    {
        System.out.println("Hello ");
    }
    
    private void SayHi(){
        System.out.println(" Hi");
    }

    public void DoVoodoo(){
        Main voodoo = new Main(){
            // The public modifier is of no use on a non-overridden member of an anonymous class.
            // It is public here to demonstrate how useless it is.
            public void someMethod(){
                System.out.println(" wo0o0o0o ");
            }

            @Override
            public void SayHello() {
                super.SayHello();
                someMethod();
            }
        };
        
        voodoo.SayHello();
        voodoo.SayHi();
        
        // This next method can't work because it is not part of the `Main` class.
        // voodoo is equivalent to a subclass of `Main` cast to `Main`.
        //voodoo.someMethod();
    }
}
```

The next example helps reveal the true nature of the anonymous class.
This direct usage of the anonymous class isn't practical but it shows it exists.

```java
public class Main {

    public static void main(String[] args) {
	    Main main = new Main();
	    main.AnonymousNature();
    }

    public void AnonymousNature(){
        new Main(){
            void ratherSillyISay(){
                System.out.println("Rather silly.");
            }
        }.ratherSillyISay();

        // Can't do anything else with the anonymous class because there is no reference to it.
        // Can't create a reference because it has no name.
    }
}
```

Anonymous classes have another fascinating aspect, it is possible to new an interface.
Effectively it creates an empty class on which you implement the interface.