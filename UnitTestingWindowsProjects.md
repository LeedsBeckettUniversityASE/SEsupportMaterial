# Unit Testing Windows Projects

## The problem
When a MSTest project is selected it gives you the option to select the framework and it must match the framework of the project under test,
However, it does not give you the option of selecting a Windows Form project. Therefore, if you have a Windows Forms project and you create a MSTest project in the same solution, 
when you add a reference to it you will see a yellow warning triangle on the project dependencies of the test project where it refers to the project under test.
This is because the projects don't match.  

Furthermore, when you try and reference the main project via a using <main project namespace> from the MStest project, you will get a syntax error because it can't find the namespace.

## The fix
Double click on the main project in the project explorer to see the contents on the csproj file. This is an xml file describing the project.
```xml
<TargetFramework>net8.0-windows</TargetFramework>
```
Now double click on the MStest project and select properties and you will see.
```xml
<TargetFramework>net8.0</TargetFramework>
```
They don't match and this is why you are getting the warning. The fix is simple, just make the MStest project..
```xml
<TargetFramework>net8.0-windows</TargetFramework>
```
Once you have done this build your solution and the warning will disappear. You will now be able to put the relevant using into your MStest project, or accept the "show potential fixes" that puts it in where your test code references your main code.

## A final potential problem
Your unit testing code is not likely to be in the same namespace as your project under test. Therefore, if you see a syntax error that says "due to it's protection level", it is because you have defined your classes without an appellation of "public" (make sure it isn’t defined as "internal").
