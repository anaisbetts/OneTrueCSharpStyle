# The One True C# Style Guide

TODO

## Zen and Inspiration

TODO

## Indentation >> *

TODO

## Structure and arrangement

Fundamentally, most classes are *data structures*. The first thing we want to understand when reading a class is to understand the *state* that it represents.

Next, classes are also a **Contract** between itself and its users (both a possibly explicit implementation of an interface, as well as the implicit contract of, "publically accessible members"). Once we understand what the class represents in terms of data, we want to understand its **interaction** with the rest of our application.

To do that, we intentionally arrange class members in the following order:

* **Fields** - Up-front, we want to see the State that this class encapsulates.

* **The Constructor** - Next, we want to see what this class depends on to operate correctly.

* **Properties** - Unfortunately, this is a crappy compromise - properties comprise State, but they're often cumbersome in terms of syntax (i.e. a non-auto property takes at least 4 lines per declaration).

* **Public Methods** - Next, are all public methods, arranged in an order that makes sense for the class. For example, if the class represents a workflow, arrange the methods you'd call in order from start to finish. If you can't think of a good arrangement, arrange them in order from high-level (i.e. methods that describe "what will be done"), to low-level. Arrange public utility or trivial methods at the bottom.

* **Private methods** - At the bottom of the page, we want our most boring methods - methods that just return simple transformations, or do the least-interesting work. Arrange private methods like public ones, from high-level private methods, down to simple low-level ones

## Files are a Unit of Organization

C# unfortunately cargo-culted the "One Class Per File" rule from Java. Don't follow Microsoft's lead; a C# source file is a **Unit of Organization**. We want to encapsulate an entire large concept / idea in a file, not take that concept and spread it among a bunch of files. This is especially unfortunate because C# also prefixes all interface names with "I", leading to hundreds of "I" files in the folder browser.

Interfaces which only have one public implementation are the contract for the class which implements it - where else would I possibly want to see this contract, than at the top of the associated class?

For libraries which will have several implementations of an Interface, consider grouping many interfaces into a single "Interfaces.cs" file (or perhaps, several "XYZInterfaces.cs" files). This allows contributors to quickly get a high-level overview of your library.

## Wrong code should Look Bad™

A style guide often focuses on attempting to make code look as good as possible. This is a wasted opportunity - we only want Good Code to look good. Code that is written well should also read well. Code that embraces bad practices should also look bad. 1TCS intentionally chooses certain style guidelines to be visually unappealing. For example, backing fields:

```cs
string _MyProperty;
public string MyProperty {
    get { return _MyProperty; }
}
```

Generally, directly manipulating a backing field should be a *rare* occurence - we want this to stick out like a sore thumb in the code in a Bad Way, because it's probably something worth noting, and if you see it everywhere, you know that you probably want to fix up this code.

When we access private methods via reflection or via internal / friend assemblies in 1TCS, the result *looks weird*:

```cs
myInstance.callingInternalMethod();
```

This is because we **want** it to look weird. This is a Good Thing.

## Configure your Editor for 1TCS

TODO

## 100ft Summary

```cs

// * Interfaces that only have one implementation should be put in the same file
//   as the implementation, above the class.
public interface IMyCoolClass
{
    IEnumerable<string> SomeMethod();
}

// * Pascal-case all classes
// * Never use the internal keyword for classes
class MyCoolClass : IMyCoolClass
{
    // * Put fields that aren't simple backing for Properties at the top of the
    //   class
    // * Readonly as much as possible, put all read-only fields together
    readonly ILogger theLogger;
    string someOtherField;
    bool aBooleanThatsCool;

    // * Order of items in class:
    //      - Fields
    //      - Constructors
    //      - Properties
    //      - Public Methods, from high-level => low-level
    //      - Private Methods, from high-level => low-level
    public MyCoolClass(ILogger logger = null)
    {
        theLogger = logger ?? new Logger();
    }

    // * Public classes and methods have brackets on separate line
    // * Pascal-case public and protected methods
    public IEnumerable<string> SomeMethod()
    {
        // * Compiler directives always go to the far left as possible
#if X86_ONLY
        someInternalMethod();
#endif
        // * Use var for all local variables
        // * Short variable names for variables used once, long variable names
        //   for variables used in more than one place.
        var result = default(IEnumerable<string>);

        // * Always use trailing commas whenever possible
        // * Align LINQ dots exactly one indentation in
        // * Put the source of the LINQ/Rx query on the same line, don't put
        //   operators on the same line unless they are a very related step
        //   of the computation.
        // * Use indentation to indicate flow in LINQ queries
        var result = new[] { 1, 2, 3, }
            .Select(x => x * 5)
            .SelectMany(x => new[] { x, x, x, }
                .Select(y => y.ToString()))
            .ToList();

        return result;
    }

    // * No private keyword, ever
    // * All opening brackets on same line except for public
    //   methods and classes
    // * Backing fields for properties are cased the same as the property, but
    //   with a leading underscore.
    string _MyProperty;
    public string MyProperty {
        get { return _MyProperty; }
    }

    /// <summary>
    /// Summaries and other comments should hard-wrap at 80 characters,
    /// regardless of how far you're indented in. Code lines themselves should
    /// wrap at 120 characters.
    /// </summary>
    void someOtherMethod()
    {
    }

    // * Camel-case internal and private methods
    void someInternalMethod()
    {
        // * Early returns are encouraged. Avoid nested indentation as much as
        //   possible.
        // * Always put brackets around ifs
        if (myProperty == null) {
            Console.WriteLine("It's Null!");
            return;
        }

        // * Unless they are one-liners **on the same line**
        if (myProperty == "bar") return;

        // * Case statements on the same line
        switch (myProperty) {
        case "Foo":
        case "Baz":
            break;
        default:
            Console.WriteLine("Something else!");
        }
    }
}
```
