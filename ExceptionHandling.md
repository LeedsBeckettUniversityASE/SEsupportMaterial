# Exceptions

## Introduction
Exceptions handling is about handling situations that we wish wouldn't occur, but in the real world they do.
So we have no choice but to write code to handle them.
An example is we want to open a file, process data from the file and then close it. But, exceptionally
when we open the file, it isn't found, or when we try and read from the file a disc sector error prevents it from happening. In these cases, we need to handle the exception and take appropriate action.

Exception handling is a programming mechanism designed to manage runtime errors and unexpected failures gracefully, preventing applications from crashing abruptly. 
Instead of letting an error halt execution, a program can intercept or "catch" the exception when it occurs (thrown) and redirect flow to a safe recovery, 
cleanup, or logging. This is achieved by using structures like `try`, `catch`, and `finally` blocks. This ensures that even when things go wrong—such as a missing file or a 
dropped database connection—the program can respond in a controlled, predictable manner rather than failing catastrophically. Furthermore, 
the code handling the exception is separated from the main code, making the code much clearer.

Separating exception handling from the main execution logic is vital for code readability, maintainability, and software design. 
When error-handling code is heavily intertwined directly with core business logic, the primary purpose of the code becomes obscured by 
endless conditional checks, making it difficult to read and update. By isolating error management into dedicated `catch` blocks, developers 
can focus purely on the intended workflow in the `try` section, allowing anomalies to bubble up cleanly and be handled separately without cluttering 
the main logic flow.

In pre OO languages such as C, handling runtime errors required manually passing error codes or status flags back up through every single layer of the 
call stack, forcing developers to write repetitive conditional checks (such as `if (status != 0)`) after every function call. This tedious approach 
cluttered core logic and made it easy to accidentally ignore failures. C++ revolutionized this by introducing native exception handling 
using `try`, `catch`, and `throw` keywords, which automatically unwinds the call stack when an exception occurs, cleanly bypassing intermediate functions and 
allowing exceptions to bubble up effortlessly until they reach a handler designed to deal with them.

## Exception handling in C#
### The try and catch blocks
The `try` keyword is used to define a block of code that may potentially throw an exception. 
When an exception occurs within this block, the normal flow of execution is interrupted, and control is transferred to the corresponding `catch` block.


```csharp
string input1 = "abc"; // Invalid integer, experiment and change these two values to see how the exception handling works
string input2 = "42";  // Valid integer

try
{
    int numberOne = int.Parse(input1);
    int numberTwo = int.Parse(input2);
}
catch (FormatException)
{
    // Output: "An invalid number format was provided."
    // Problem: We don't know if input1 or input2 failed!
    Console.WriteLine("An invalid number format was provided.");
}
```
In the above example, if `input1` or `input2` fails to parse, the catch block will execute, but we won't know which input caused the exception.
Either of the two Parse lines can cause an exception (if something is typed that cannot be converted to an integer). Usually this is fine,
something went wrong, so stop normal operation and handle the exception. But sometimes we want to know exactly what went wrong.

```csharp
string input1 = "abc"; // Invalid integer
string input2 = "42";  // Valid integer

int numberOne = 0;
int numberTwo = 0;

try
{
    numberOne = int.Parse(input1);
}
catch (FormatException ex)
{
    Console.WriteLine("An invalid number format was provided in the first input.");
}

try
{
    numberTwo = int.Parse(input2);
}
catch (FormatException ex)
{
    Console.WriteLine("An invalid number format was provided in the second input.");
}
```
It is up to the programmer to decide which is appropriate, but it is best to err on simplicity, if you don't need to differentiate then don't.

### The finally block

The `finally` block is an optional construct that follows the `try` and `catch`.
The **`finally` block** is an optional section added to the end of a `try-catch` structure. Its defining characteristic is that **its code always runs**, regardless of whether an exception was thrown, whether it was caught, or whether the code completed successfully.



```csharp
try
{
    // Code that might throw an exception
    OpenDatabaseConnection();
    RunRiskyQuery();
}
catch (SqlException ex)
{
    // Handles the database error
    Console.WriteLine("A database error occurred: " + ex.Message);
}
finally
{
    // This executes NO MATTER WHAT—even if an exception was thrown and caught,
    // or if no exception happened at all.
    CloseDatabaseConnection();
}

```

**Why the `finally` Block is Important**

The primary use for a `finally` block is **cleanup and resource management**.

When a program opens external resources—like files, network connections, or database handles—those resources need to be explicitly closed or released
so they don't leak memory or lock up the system. If an exception occurs midway through your `try` block, execution jumps straight to the `catch` block, 
completely bypassing any code that comes after the exception occurred.

By putting your cleanup code inside a `finally` block, you guarantee that the resource will be cleaned up safely before the application moves on, preventing 
leaks even when things fail unexpectedly.

### The `throw` keyword

The **`throw`** keyword in C# is used to explicitly signal that an exception has occurred (i.e. the programmer is signalling this), halting normal execution and sending an exception up the call stack.

Depending on how it is used, `throw` serves two main purposes: **creating a brand-new exception** or **rethrowing an existing one**.



#### 1. Throwing a New Exception

You use `throw` followed by a new exception object when your code detects a rule violation or a failure condition (such as invalid input or a missing file).

```csharp
public void SetAge(int age)
{
    if (age < 0 || age > 120)
    {
        // Stop execution and throw a new exception
        throw new ArgumentOutOfRangeException(nameof(age), "Age must be between 0 and 120.");
    }
    
    // This line won't run if age is invalid
    _age = age; 
}

```

* **What happens:** The moment the program hits `throw`, the current method stops dead in its tracks. It does not return a value or execute any remaining lines; 
* instead, it immediately looks for a matching `try-catch` block further up the call stack.
* It is up to the programmer to decide what exception type to throw. The .NET framework has a rich set of built-in exception types 
(like `ArgumentNullException`, `InvalidOperationException`, etc.), but you can also define your own custom exceptions by subclassing `Exception`.


#### 2. Rethrowing an Existing Exception (`throw;`)

When you catch an exception, you can use a bare `throw;` statement inside the `catch` block to pass that exact same error up to the next layer.

```csharp
try
{
    ProcessPayment();
}
catch (TimeoutException ex)
{
    // Log the error locally first
    LogErrorToDatabase(ex);

    // Rethrow the exact same exception up the call stack
    throw; 
}

```

#### Important Distinction: `throw;` vs. `throw ex;`

* **`throw;` (Correct):** Preserves the original stack trace, meaning you can still see the exact line number where the error originally occurred at the bottom of the stack.
* **`throw ex;` (Discouraged):** Resets the stack trace to point at the `catch` block where `throw ex;` was written, wiping out the history of where the error actually came from.

### The Exception Hierarchy
In C# and the .NET runtime, all errors inherit from a single root base class called **`System.Exception`**. This creates a tree-like structure known as the **exception hierarchy**, allowing exceptions to be categorized from broad categories down to very specific error types.

#### Key Layers of the Hierarchy

* **`System.Exception` (The Root):** The base class for all errors. Because everything inherits from it, a generic `catch (Exception)` block can catch any error that occurs in your application.
* **Built-in / Framework Exceptions:** These branch off the root to represent specific runtime issues managed by .NET. Examples include:
* `System.SystemException` (older classification for runtime issues, though most modern built-in exceptions inherit directly from `Exception`).
* `ArgumentException`, `NullReferenceException`, and `TimeoutException` (describing exact technical failures).


* **Custom Exceptions:** Developers can create their own exception classes by inheriting from `Exception` (or a more specific subclass like `InvalidOperationException`) to represent domain-specific business rules.

#### Why the Hierarchy Matters

Because of inheritance, **polymorphism** applies to exceptions. When an error is thrown, a `catch` block checks types from the bottom of the tree up. 
If you catch a specific exception like `FormatException`, it handles that exact error. If you use a broad catch like `Exception`, 
it matches anything because every exception *is-a* `System.Exception`. In a similar way to deciding whether to
have separate catch blocks for each line that generates an exception, or one block that covers several, we can also decide whether to
catch more general or more specific exceptions. For example, do we need to differentiate that a fileNotFoundException or more geneeral IOException was thrown?
Because with the latter the catch block could also handle a problem after the file opened (such as a bad read). 
It is generally better to catch a specific exception, but you may decide to go higher up the hierarchy because you want to handle more than one related
exception with one catch block.

Here are some of the most common exceptions you will encounter in C# and the .NET framework, organised by how they fit into the exception hierarchy:

#### 1. The Root Base Class

* **`System.Exception`**: The parent of all exceptions. Catching this catches any error in the system.

#### 2. Common Runtime & Logic Exceptions

These usually indicate a bug in the code, bad state, or invalid arguments passed into a method:

* **`ArgumentException`**: Thrown when a method is passed an inappropriate argument.
* *Subclasses:* `ArgumentNullException` (when an argument is unexpectedly `null`), `ArgumentOutOfRangeException` (when a number falls outside an acceptable range, like an invalid age or index).


* **`InvalidOperationException`**: Thrown when a method call is invalid for the object's current state (e.g., trying to read from a closed file or database connection).
* **`NullReferenceException`**: Thrown when code attempts to access a member on an object reference that evaluates to `null`.
* **`IndexOutOfRangeException`**: Thrown when trying to access an array or collection index that is outside its bounds (e.g., index `-1` or `10` on a list of 5 items).
* **`FormatException`**: Thrown when the format of an argument or string is invalid (e.g., calling `int.Parse("abc")`).

**Important Note:** Many of these exceptions indicate programming errors that should be fixed in code rather than handled at runtime. 
For example, a `NullReferenceException` usually means you forgot to check if an object was `null` before using it. 
While you can catch it, it's better to prevent it from happening in the first place.

#### 3. I/O and System Environment Exceptions

These represent external failures outside of direct code logic (i.e. there is nothing we can do in code to prevent them), 
such as network or hardware issues:

* **`IOException`**: The base class for input/output errors.
* *Subclasses:* `FileNotFoundException`, `DirectoryNotFoundException`.


* **`TimeoutException`**: Thrown when a time-allotted operation or network request expires before completion.
* **`UnauthorizedAccessException`**: Thrown when the operating system denies access due to an I/O error or security violation (e.g., trying to write to a protected folder without admin rights).

### The Exception Object
When an exception occurs and is intercepted by a `catch (Exception ex)` block, the runtime hands back an instance of a class that inherits from `System.Exception`. This object acts as a diagnostic bundle, packed with metadata about what went wrong, where it happened, and why.

The exception object provides several essential properties for inspecting and handling the error:

### Key Properties of the Exception Object

* **`Message`**: A string containing a human-readable description of the error (e.g., `"Connection timed out"` or `"Value cannot be null."`). This is typically what you log or display to a user.
* **`StackTrace`**: A string representing the exact call stack—the chain of method calls leading up to the moment the exception was thrown. In debug builds, it includes file names and line numbers, making it invaluable for tracking down bugs.
* **`InnerException`**: As seen in exception chaining, this property holds the *previous* exception that caused the current one. If it is the root cause, `InnerException` is `null`.
* **`Source`**: A string representing the name of the application or the assembly/object that caused the error (often the name of the project or DLL).
* **`Data`**: A key/value dictionary (`IDictionary`) that allows you to attach custom user-defined data or context variables to the exception object dynamically as it bubbles up.
* **`TargetSite`**: A reflection object representing the exact method that threw the exception.


### Example of Inspecting the Exception Object

Inside a catch block, you can access these properties directly to diagnose the failure:

```csharp
try
    {
        int result = int.Parse("not-a-number");
    }
    catch (FormatException ex)
    {
        Console.WriteLine($"Error Message: {ex.Message}");
        Console.WriteLine($"Source: {ex.Source}");
        
        // You can use the Data dictionary to attach custom context
        ex.Data["Timestamp"] = DateTime.Now;
        Console.WriteLine($"Logged at: {ex.Data["Timestamp"]}");
        
        Console.WriteLine($"\nStack Trace:\n{ex.StackTrace}");
    }

```

## A Simple Example

The following demonstrates a three levels program. 
It could for example be a program with a user interface at level 1, a compiler at level 2 and an individual command (being compiled) at level 3
```csharp
using System;
using System.Runtime.CompilerServices;

/// <summary>
/// Example of an exception system that might be employed by a compiler system.
/// The idea is that a compiler goes through each line in a program.
/// It asks the command factory to create a command.
/// The command factory may not be able to create a command, so it would throw an exception indicating no such command.
/// if a command found a problem, like it has been given invalid parameters, it would throw an exception.
/// Which the command factory would catch.
/// When the parser catches an exception it can add the line number on.
/// The user interface would report these to the user.
/// </summary>
class Program
{

    /// <summary>
    /// represents the User Interface
    /// </summary>
    static void Main()
    {
        try
        {
            Compile();
        }
        catch (Exception ex)
        {
            // .ToString() automatically prints the entire exception chain
           
            Console.WriteLine($"{ex.Message}.");
        }
    }

    /// <summary>
    /// Represents Level 1 the compiler
    ///  in a real compiler it would go through a program line by line, asking the commandfactory to create each command object
    ///  here it just calls it once as an example of it catching an exception
    /// </summary>
    /// <exception cref="Exception"></exception>
    static void Compile()
    {
        int line = 8; //simulated line number
          try
          {
              CommandFactory(); //get the command factory to create a command
          }
          catch (Exception ex)
          {
              // Catch, append info by passing 'ex' as the inner exception, and rethrow
              throw new Exception($"Syntax error at line {line} {ex.Message}", ex);
          }
        CommandFactory(); //get the command factory to creat a command
    }

    /// <summary>
    /// Level 2 The Command Factory
    /// It is asked by the compiler to create a command, if it can't it throws an exception (i.e. no such command)
    /// or if it catches an exception from the newly created command it passes it on.
    /// </summary>
    /// <exception cref="Exception"></exception>
    static void CommandFactory()
    {
        try
        {
            Circle();
        }
        catch (Exception ex)
        {
            // Catch, append info, and rethrow
            throw new Exception($"invalid command {ex.Message}", ex);
        }
    }

    /// <summary>
    /// Level 3 An example command
    /// It would check what parameters it has and if they were incorrect it would throw an exception
    /// </summary>
    /// <exception cref="Exception"></exception>
    static void Circle()
    {
        // The original exception source
        throw new Exception("Circle has invalid number of parameters ");
    }
}
```

If you comment out the try and catch in CommandFactory(), leaving just the call to Circle(),
You will see that the program still runs, that level is bypass and that information is left out.
```csharp
  static void CommandFactory()
    {
        /*
         try
          {
              Circle();
          }
          catch (Exception ex)
          {
              // Catch, append info, and rethrow
              throw new Exception($"invalid command {ex.Message}", ex);
          }
        */
        Circle();
    }
```

### Finally (get it?) differences between Java and C#/C++ exception handling

While Java, C#, and C++ all share a similar `try-catch-throw` syntax inspired by C++, they handle exception enforcement and method signatures very differently. The most significant differences lie in **checked exceptions** and the **`throws` keyword**.



#### 1. Checked vs. Unchecked Exceptions

* **Java (Enforces Checked Exceptions):** Java divides exceptions into two categories:
* **Checked Exceptions:** Errors that a well-written application should anticipate and recover from (such as `IOException` or `SQLException`). The Java compiler strictly enforces that you must either handle these exceptions using a `try-catch` block or declare them in your method signature. If you don't, the code **will not compile**.
* **Unchecked Exceptions:** Runtime errors like `NullPointerException` or `ArrayIndexOutOfBoundsException`, which represent programming bugs and do not require forced handling.


* **C# and C++ (Unchecked Only):** Both C# and C++ completely abandoned checked exceptions. **All exceptions in C# and C++ are unchecked.** The compiler never forces you to catch an exception, and you will never get a compilation error for failing to handle or declare an exception.



#### 2. The `throws` Keyword

* **Java:** Uses the `throws` keyword in a method signature to explicitly list the checked exceptions a method might throw. This forces any caller of that method to deal with those exceptions.
```java
// Java requires you to declare checked exceptions
public void readFile() throws FileNotFoundException 
{
    FileReader reader = new FileReader("config.txt");
}

```


* **C#:** **Does not have a `throws` keyword.** C# philosophy dictates that forcing developers to declare exceptions in method signatures creates heavy maintenance overhead and discourages refactoring. Any C# method can throw any exception at any time without prior declaration.
* **C++:** Historically had dynamic exception specifications (e.g., `void myFunc() throw(std::exception);`), but they were widely considered flawed, caused performance hits, and have been **completely removed** in modern C++ (C++17/C++20). Modern C++ uses `noexcept` to specify that a function *never* throws an exception, but it does not list what exceptions *might* be thrown.



#### Summary Comparison

| Feature | Java | C# | C++ |
| --- | --- | --- | --- |
| **Exception Type Enforcement** | Checked & Unchecked | Unchecked only | Unchecked only |
| **Compiler Enforcement** | Forces you to catch/declare checked exceptions | None | None |
| **`throws` Keyword in Signatures** | Yes (Mandatory for checked exceptions) | No | No |
| **Philosophy** | "Anticipate recoverable errors at compile time." | "Keep signatures clean; let developers decide what to catch." | "Zero-overhead principle; do not pay for exception safety if unused." |

