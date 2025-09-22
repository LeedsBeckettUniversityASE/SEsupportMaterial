# Visual Studio Exercise

## Laboratory Exercise

### Learning outcomes.

Familiarity with Visual Studio .NET IDE environment  
Creating simple applications containing data and methods  
Display message boxes and receive user input  
Converting data types  
Instantiating objects and calling methods  


### Task 1.  Familiarity with Visual C#.
I have used Community Edition for these examples, as it is free!
The labs have VS Professional. The only practical difference is the Professional version has multiple languages, which we won’t use.

Create a new project and select a Console App(.net framework).  You can give your project a name and change its location at this point. This will generate a bare-bones application for you (use File->New->Project), that will compile and run.  As it stands it won’t display anything.  The code it generates should look something like:
```csharpe
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

Namespaces

I was very lazy and didn’t even change the name of the project, that’s why I get “namespace ConsoleApplication1”, if you called your project anything else then your namespace will be called what you put.

A namespace is the .net equivalent of a Java package. Namespace/packages are to isolate our classes from the rest of the code universe. The goal of OO is to be able to just plug pre-existing classes together and not have to write anything (we are not there yet). However, what if I write a class called “queue” and so do you? How will we tell them apart? The answer is we put them in packages/namespaces. My queue will be in my namespace and yours will be in your namespace. Technically we use our ip address as our namespace (backwards), so mine would be:
```csharpe
uk.ac.leedsbeckett.mullier.<namespace>
```
yours would be
```csharpe
uk.ac.leedsbeckett.<your_student_id>.<namespace>
```
or if you owned your own domain
```csharpe
uk.mullier.<namespace>
```
We aren’t going to be unleashing our examples on the world, so we don’t need to go this far now, but you could if you wanted to.

Code
Each time you create a new console based application, you would be advised to modify the code to look like:
```csharpe
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            Program app = new Program();
        }

        public Program()
        {
            
        }
    }
}
```

An analysis of this program will tell you that the main method is calling the constructor (Program()) where it creates an object and rather than returning to the main method and starting our code there, we put our code inside the constructor. The reason why we have done it like this is because all programs must have an entry point, in most languages this is the “main” method. However, this is a “static” method. Static methods can be called without having to first create an object of the class it is in. That sounds good, but this is what is termed “a static context” and you can’t do everything in a static context, i.e. we are restricted in what we can do, we can’t, for example, use instance data. So the first thing we need to do is get out of the static context. 

We do this by creating an object of the class:
```csharpe
Program app = new Program();
```
This created an object of “Program” called app. (In actual fact it creates a reference called app which is attached to a nameless object but….). Also, this is why the “constructor” (a special method with the same name as the class which is used to “instantiate” an object of the class) has no return type. In the case of a constructor it is already returning something, the reference to the object (literally the address of it in memory).

Execution then goes inside the app object and we are no longer in a static context.

Next, compile and run the program using the Build and Debug menus.  

### Task 2.  Gaining familiarity with external libraries of code.

The next task is to gain familiarity with calling external code resident in libraries or assemblies. An assembly in the .net environment is simply a body of code that can be found in various forms, and which you can call (and therefore not have to design and implement).  We will gain familiarity with this by using the MessageBox class, a pre-written library of code available to us.

A message box allows simple GUI’s to be created that can display or receive messages from the user.


To add the facility to display MessageBoxes in C#, using the Visual Studio IDE, take the following steps:

Right click on the project in the project explorer and select “add->reference->assemblies” (On older versions this is done the menu option project->Add Reference) and select the System.Windows.Forms.dll library.  Any file ending with .dll is a dynamically linked library of code (i.e. a library that is linked to your executable file at runtime). It may be that this assembly is already added to your project (ticked) but that will be because you are using a form project, this is to add it to a console project.

Don’t worry about this if you can’t get it to work. I am just getting you to play with the environment. If you really wanted to use Windows Forms components in a project you’d use a Windows Forms project instead of a Console project and it would set it all up for you. This is what the project types are (when you create a new project), they are all just console projects with various assemblies added so you don’t have to (i.e. you could turn a console project into any type of project by adding the relevant assemblies).




The equivalent in Java would have been to have added a reference to a Jar file. In both cases the library is a previously compiled set of resources, without an entry point to execute, that can be called by our program. 

### Task 3.  Adding program statements to create a message box.


You will need to add the following statements to your code in an appropriate place. 
```csharpe
      MessageBox.Show("Message", "Title",
         MessageBoxButtons.OK, MessageBoxIcon.Exclamation);
```
Since the program also makes use of an additional library, you will also need to add a using statement at the beginning of the program. In actual fact if you paste the above line in VS will automatically add the required using statement.
```csharpe
using System.Windows.Forms;
```


### Task 4 – Create a body  mass index program.  Body mass index is a measure of health and can be calculated using the following formula:

BMI =              Weight in Pounds             
               ((Height in inches x Height in inches)  x 703)


or

BMI =             Weight in Kilograms             
             (Height in Metres x Height in Metres)


When run, the program should produce banded output as follows:

Adults  
Women  
Men   
severely underweight  
< 17.5   
< 18.5  
underweight   
17.5 - 19.1  
 18.5 - 20.7   
in normal range  
19.1 - 25.8  
20.7 - 26.4   
marginally overweight  
25.8 - 27.3  
26.4 - 27.8   
overweight  
27.3 - 32.3  
27.8 - 31.1   
very overweight or obese  
32.3 - 35   
31.1 - 35  
severely obese  
 35 - 40  
 morbidly obese    
 40 - 50  
 super obese  
 50 - 60    



HINT –You will need to make use of the following program statements to achieve this, taken from this week's lecture notes.

Reading a string from the keyboard.
```csharpe
string s = Console.ReadLine();
```
Converting the string to a floating point number:
```csharpe
float f = Single.Parse(s);
```
Converting a floating point number back to a string:
```csharpe
s = f.ToString();
```

You can perform the same operations on integer numbers.


You will need to make use of conditional (if-else) statements to make this program work.

Finally, make use of a loop to make the program repeatedly ask for new input.
