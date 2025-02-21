---
title: Verse Cheat Sheet
parent: Verse
description: Quick and dirty reference for the Verse programming language
---

# Verse Cheat Sheet
The code snippet below is Verse code written to form a cheat sheet for various features of the programming language. It goes over variables, types, control flow and other trappings that you might find in a typical programming language cheat sheet. It is not comprehensive nor guaranteed to be accurate as Verse continues to evolve constantly.

```
cheat_sheet := class():
    # Verse Cheat Sheet (version 0.1 - UEFN 5.6/33.30)

    ## Comments ##
    # This is a Comment - the compiler ignores text, for just this line, after the first # symbol
    <# This is an Inline Comment, differs from single-line comments, ends with → #>
    <#> This is used to make Indented Comments
        <# and #> can be used for nested comments and to make Multi-Line comments too.
            This line is still part of the indented comment btw.
        Un-indenting will end this comment block.

    <#
    An Expression is the smallest unit of code that evaluates to a result.
    Everything in Verse is an expression!!
        <#
        An expression can succeed or fail.
        For example, integer division can fail since you could divide by zero.
        Such "failable" expressions can only exist in special contexts where we can
        handle what happens on failure.
        #>
    #>

    ## Functions ##
    <# Functions are a sequence of expressions that do something or
    take in an input (parameters) and produce an output (result). #>
    GiveMeA5() : int = # ← Defining a function called GiveMeA5 that outputs or returns an integer
        # This is a code block (a group of expressions) with its own scope
        2 + 3
        # The last expression's result is the output but you can use an explicit `return` too
    # The code block and its scope has ended here because of un-indenting.
    # You can invoke or call the above function with GiveMeA5()
    # Some functions can be set to fail, these use [] instead of ()

    # Functions can accept inputs by defining parameters. Parameters need a name and type.
    Add5To(InputParam : int) : int =
        GiveMeA5() + InputParam
    # Values are supplied as arguments when the function is invoked.
    UseTheAboveFunctions() : void =
        Add5To(10)            # supplying 10 as the argument
        Add5To( GiveMeA5() )    # supplying the result of GiveMeA5() as the input

    ## Variables & Types ##
    MyConstantVariable : int = 42       # This is a constant, cannot be changed at runtime
    # Note the 3 elements of variable declaration → name : type = value
    var MyMutableName : string = "foo"  # the var keyword allows value changing
    # set MyMutableName = "bar"         # the set keyword is used to change variables
    # [Styling] PascalCase variable names

    ### Basic Types ###
    BakersDozen : int = 13              # Integer, no fractional components
    EulersNumber : float = 2.71828      # Floating-point values, can also be NaN
    UserName : string = "admin"         # String, sequence of characters
    IsActive : logic = true             # Logic represents Boolean values true and false

    ### Container Types ###
    TriNumbers : []int = array{1, 3, 6}         # Array, stores values of the same type
    PowerLevel : ?int = option{9001}            # Option, either a value or empty
    # 0..3                                      # Range, series of integers (restricted use cases)
    Info : tuple(string, int) = ("Age", 18)     # Tuple, set of values of any type
    Pairs : [string]int = map{"Age" => 18}      # Map, collection of key-value pairs

### Composite Types ###
target := enum{Hi, Med, Low}            # Enumeration, set of named values, declared outside scope

attack := struct:                       # Structure, group of related variables. No functions (yet)!
    Name : string
    FirstHit : target = target.Med      #  using the target enum in the struct
    MaxDamage : int = 10

street_fighter := class:                # Class, a blueprint for creating objects
    Name : string
    HealthPoints : int = 100
    Special : attack := attack{Name:="Throw"}       #  using the attack struct in the class
    PlayIntro() : void =                            #  defining a function to make a class' method
        Print("Hello World!")

pro_wrestler := class(street_fighter):              # Subclass, extends another class
    HealthPoints<override> : int = 200              #  <override> allows superclass value changes

GenerateEnemy<constructor>() := street_fighter:     # Constructor, creates a class instance
    Name := "Scrub"
# Notice the lack of return type in the constructor
# Unlike some languages, a single class can have many constructors.

achievable := interface():                          # Interface, empty methods for classes to implement
    Unlock() : void
# Using the above interface
achievement := class(achievable):                   # Making a new class that implements interface
    Unlock<override>() : void =                     # Methods must be overridden and implemented
        Print("Achievement Unlocked!")
    <#> [Styling]
        lower_snake_case is used for the names of enums, structs, classes and interfaces.
        Constructors are special but they are still functions so they should use PascalCase
        Interface names should describe the properties of the classes using adjectives.
        If adjectives don't exist, suffix _interface.

    ### Other Types ###
    Error<localizes> : message = "Error"        # Message, localization friendly piece of text
    NIL : void = {}                             # Void, special type that means "not important"
    Add(First:any, Second:any):any = {}         # Any, special type that *all* types derive from
    IsZero(X:comparable):logic = false          # Comparable, allows types to be compared
    GiveMePI()<decides> : rational =
        MathPI : rational = 22 / 7              # Rational, division of two integers

    ## Operators ##
    <#> Special functions with symbols for names. Their parameters are called operands.
        An expression can use multiple operators. There is an evaluation order.
        ?       Query                   Check if logic value is true
        not     Not                     Negates success or failure of expression
        +       Positive                Unary plus operator, no operation on number
        -       Negative                Unary minus operator, negates a number
        *       Multiply                Multiplies two values
        /       Division                Divides left value by right value
        +       Addition                Adds or concatenates two values
        -       Subtraction             Subtracts right value from left value
        +=      Addition assignment     Add and assign at the same time
        -=      Subtract assignment     Subtract and assign at the same time
        *=      Multiply assignment     Multiply and assign at the same time
        /=      Divide assignment       Divide and assign at the same time
        =       Equal to                Succeeds when left value equals right value
        <>      Not equal to            Succeeds when left value equals right value
        <       Less than               Succeeds when left value is less than right value
        <=      Less than or eq to      Succeeds when left value is less than or equal to right value
        >       Greater than            Succeeds when left value is greater than right value
        >=      Greater than or eq to   Succeeds when left value is greater than or equal to right value
        and     And                     Succeeds when all operands succeed
        or      Or                      Succeeds if at least one operand succeeds
        :=      Initialization          Stores values in a constant or variable. Looks like walrus!
        =       Assignment              Updates values stored in variable

        Operator evaluation order can be modified with grouping with round brackets ( )
        2 * 1 + 2       results to 4 because multiply 2 and 1 first, then add 2 to it
        2 * (1 + 2)     results to 6 because we add group 1 and 2 first, then multiply that by 2

    ## Control Flow ##
    # It's the order in which instructions are evaluated.
    TestControlFlow() : void =
        block:                                  # Block, an explicit code block
            Item : string = "Rocks"
            block:                              #  nested code block
                Name : string = "Jenny"
                Print(Name + Item)              #  variable Item from the parent block is accessible
            # The second block has ended here, variable Name is no longer accessible
            # Print(Name) throws unknown identifier error
            Print(Item)                         #  variable Item still works, we're still in the first block
        # The first block has ended here, variable Item is now no longer accessible

        if (22 / 7):                            # If, conditional execution based on success/fail
            Print("The expression succeeded!")
        else:
            Print("The expression failed...")

        var SomeInt : int = 5
        case (SomeInt):                         # Case, choose expressions based on value comparisons
            0 => Print("Zero")
            1 => Print("One")
            _ => Print("Not Zero nor One")      #  a default clause is needed as we can't handle all choices

        defer:                                  # Defer, once evaluated, its code block executes 
            set SomeInt = 0                     # when leaving the current code block the it lives in
            Print("We've reset SomeInt!")
        # Defers cannot be the last code block

        loop:                                   # Loop, repeats the block until ended or suspended
            set SomeInt += 1
            if (SomeInt > 10):
                break                           #  here we stop the loop with `break`
            # One can use `return` as well 

        for (Index := 0..99):                   # For, like a Loop but uses a "generator" to make a sequence
            set SomeInt = Index                 # of values to iterate with

    ## Attributes & Specifiers ##
    <#
    Attributes are keywords that describe behaviors external to Verse.
        @editable           Indicates a value can be set in UEFN
        @doc                Documentation for classes and functions
    Specifiers are keywords that describe behaviors within Verse.
        <transacts>         Actions can be rolled back
        <varies>            Same input may not product same output.
        <computes>          No side effects and may not complete
        <converges>         No side effects and definitely completes
        <decides>           Can fail or is a failable expression
        <suspends>          Is Asynchronous
        <public>            Universally accessible
        <protected>         Can only be accessed by self or subtypes
        <private>           Can only be accessed in current scope
        <internal>          Can only be accessed in current module
        <abstract>          Instances cannot be made
        <concrete>          All fields must be initialized
        <unique>            Each instance is unique (even if all fields are equal)
        <final>             Subclasses cannot be made
        <native>            Definitions are implemented in C++
        <native_callable>   Native and also callable by other C++ code
        <localizes>         Defining a new message with a string
    #>

    ## Modules ##
    # Modules are redistributable Verse code files that can be imported and used in other Verse files.
    # Modules use folders. The folder name is the module name; contained Verse files are part of that module.
    # Modules are imported with `using` followed by the path name.
using { /Verse.org/Simulation }
    <#> Creating a module can be done like so
        my_module := module:
            my_class<public> := class{}

    ## Concurrency ##
    <# Verse expressions can be:
        * immediate     - its evaluation finishes within current update OR
        * async         - its evaluation may complete in a later update

        Async expressions need to run in an async context, specified with <suspends>
    #>

    Slow()<suspends> : void =
        Sleep(10.0)                     # the Sleep function is from the /Verse.org/Simulation module
    Fast()<suspends> : void = {}

    TestConcurrency()<suspends> : void =
        sync:                           # Sync, executes all expressions at once
            Slow()                      # waits for all of them to finish before moving on
            Fast()

        race:                           # Race, runs all expressions, first expression to finish "wins",
            Slow()                      # the rest are canceled and the code after the block runs
            Fast()

        rush:                           # Rush, runs all expressions, proceeds once 1st expression finishes,
            Slow()                      # the rest continue till they finish but can get canceled
            Fast()                      # if the async context completes first

        branch:                         # Branch, executes branch and the code after it.
            Slow()                      # Branch is canceled if code block or context finishes first
            Fast()

        Print("Let's spawn!")
        spawn{Slow()}                   # Spawn, executes only one async function but it is independent
        Print("Spawned a task.")        # of the context that spawned it!

        # All concurrent expressions return values.
        # For example, sync returns a tuple of all results and branch returns void.
        # Of note, Spawn returns a Task. Tasks can halt a code block until it finishes
        SomeTask := spawn{Slow()}
        SomeTask.Await()
        Print("This will only run after the spawned Slow() finishes.")

```