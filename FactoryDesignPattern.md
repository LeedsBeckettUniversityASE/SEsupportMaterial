# The Factory Design Pattern
## Using the Shapes Example

Design Patterns are the best way of tackling a common problem in software design. Why reinvent the wheel? If you are using a design pattern then your code will be recognised by other developers because we all use the same design patterns. It's a bit like using inheritance, because it's a good idea to, but design patterns aren’t specifically built into the language and so can be more encompassing and general. Here we will look at one example but there are many.
 
 ###  The Factory Design Pattern
 The Factory Design Pattern is used to handle dependencies. Wherever there is a “new” keyword there is a dependency. This means that if you want to reuse the class then you must also have all of the classes it references with a new keyword, and all of the classes each of those references with a new keyword! It quickly makes reuse impractical. Instead the factory design pattern moves all the dependencies to one place, the factory, and if any are missing it will report so via an exception. You may still have enough for what you want. It simplified dependencies greatly, that’s why it is a good idea and that is why it has become the accepted solution for this problem.


 [The full example code is in this repo](https://github.com/LeedsBeckettUniversityASE/ShapesFactory)
 ### Stage 1 - Create an interface
 ```csharp
  interface Shapes
    {
        void set(Color c, params int[] list);
        void draw(Graphics g);
        double calcArea();
        double calcPerimeter();

    }

 ```
  
 We create an interface so that the classes we are going to use in our program and classes that could be added in the future all conform to the expectations of the application in hand. Here, the program is going to want to draw and calculate the area and perimeter, the main program calls these methods to do that. If a future developer write a new class that conforms to this interface, to draw a triangle say, then the main program does not need to be touched or viewed at all but will draw the new triangle. Also, the factory design pattern stipulates that you don’t use constructors. The reason for this is that the factory just makes stuff. Think of asking for a bike frame, you get back a raw, unpainted, carbon fibre frame. You would then paint it and add any other components. The constructor is like painting it. It isn’t the factory’s role (so the design pattern says). But, in the end you will still want your frame painted. We just do this after it has been created by the factory. So, in effect we just write any methods that take the place of any constructors and they can be called after the factory has created the object (this gets around the fact that a constructor is called only when creating an object). The factory therefore only uses blank constructors (i.e. newClass() ). To cut a long story short, the set() method in the interface is taking the place of the constructor.

 ```csharp
 \\old way
 Circle c = new Circle(Color.Red, 10, 10, 50); //uses a constructor
 \\using a factory (new way)
 Circle c = (Circle)ShapeFactory.getShape("Circle");
 c.set(Color.Red, 10, 10, 50); //uses a method to set the parameters

 ```

 ### Stage 2 Create an abstract class that implements the interface
  ```csharp
 abstract class Shape:Shapes
    {
        protected Color colour; //shape's colour
        protected int x, y; //not I could use c# properties for this
        public Shape()
        {
            colour = Color.Red;
            x = y = 100;
        }

      
        public Shape(Color colour, int x, int y)
        {

            this.colour = colour; //shape's colour
            this.x = x; //its x pos
            this.y = y; //its y pos
            //can't provide anything else as "shape" is too general
        }

        //the three methods below are from the Shapes interface
        //here we are passing on the obligation to implement them to the derived classes by declaring them as abstract
        public virtual void draw(Graphics g)
        {
            Font drawFont = new Font("Arial", 12);
            SolidBrush drawBrush = new SolidBrush(Color.Black);
            // Set format of string.
            StringFormat drawFormat = new StringFormat();
            drawFormat.FormatFlags = StringFormatFlags.NoClip;
            String text = this.ToString();
            g.DrawString(text, drawFont, drawBrush,this.x, this.y, drawFormat);
        }
        public abstract double calcArea(); //any derrived class must implement this method
        public abstract double calcPerimeter(); //any derrived class must implement this method

        //set is declared as virtual so it can be overridden by a more specific child version
        //but is here so it can be called by that child version to do the generic stuff
        //note the use of the param keyword to provide a variable parameter list to cope with some shapes having more setup information than others
        public  virtual void set(Color colour, params int[] list)
        {
            this.colour = colour;
            this.x = list[0];
            this.y = list[1];
        }


        public override string ToString() //get the standard object name and break hierarchy to get just the name
        {
            String text = base.ToString();
            String[] sut = text.Split('.');
            text = sut[sut.Length - 1];
            return text;
        }

    }
  ```
  This isn’t doing anything special. It is just the inheritance example. 

  ### Stage 3 Create the derived classes
  ```csharp
   class Circle : Shape
    {
        int radius;

        public Circle():base()
        {
            //it's not obvious why I've bothered to put an empty blank constructor, but it is bacause I want it to call the base class constructor, in case that does anything.
        }
        public Circle(Color colour, int x, int y, int radius) : base(colour, x, y)
        {

            this.radius = radius; //the only thingthat is different from shape
        }

      
        public  void set( Color colour, int x, int y, int radius)
        {
            //note: This is not overridden, it is overloaded as it isn't the same method signature as in Shape
            base.set(colour, x, y);
            this.radius = radius;
        }

    

        public override void draw(Graphics g)
        {

            Pen p = new Pen(Color.Black,2);
            SolidBrush b = new SolidBrush(colour);
            g.FillEllipse(b, x, y, radius * 2, radius * 2);
            g.DrawEllipse(p, x, y, radius * 2, radius * 2);
            base.draw(g);
        }

        public override double calcArea()
        {
            return Math.PI * (radius^2);
        }

        public override double calcPerimeter()
        {
            return 2 * Math.PI*radius;
        }

        public override string ToString() //all classes inherit from object and ToString() is abstract in object
        {
            String text = base.ToString() + "  " + this.radius; 
            return text;
        }
    }
  ```
  Notice that the blank constructor is defined explicitly because it is set to call the base constructor. In this case I wrote the base (shape in this case) class so I know it does something, but I might not have. This just makes sure that it is called in case it needs to be.

  Note also, that the set() method is not being overridden. It is being overloaded (confusing terminology potentially). Overloading means it has the same name but a different signature (i.e. the parameter list is different). So not “overload” keyword. Think about what is happening. When this method is called, it calls the similar set() method above to set the generic values before setting the radius.

  ### Stage 4 Create the Factory class
  ```csharp
   class ShapeFactory
    {
        public Shape getShape(String shapeType)
        {
            shapeType = shapeType.ToUpper().Trim(); //you could argue that you want a specific word string to create an object but I'm allowing any case combination
            
           
            if (shapeType.Equals("CIRCLE"))
            {
                return new Circle();

            }
            else if (shapeType.Equals("RECTANGLE"))
            {
                return new Rectangle();

            }
            else if (shapeType.Equals("SQUARE"))
            {
                return new Square();
            }
            else
            {
                //if we get here then what has been passed in is unknown so throw an appropriate exception
                System.ArgumentException argEx = new System.ArgumentException("Factory error: "+shapeType+" does not exist");
                throw argEx;
            }

           
        }
    }

  ```

  The factory doesn't do anything other than create a generic shape object based on what is asked for. Note that it uses the blank constructors only. The main program will then call the set() method to set it up.

  ### Stage 5 Use the Factory to create the objects
```csharp
class Circle : Shape
 {
     int radius;

     public Circle():base()
     {
         //it's not obvious why I've bothered to put an empty blank constructor, but it is bacause I want it to call the base class constructor, in case that does anything.
     }
     public Circle(Color colour, int x, int y, int radius) : base(colour, x, y)
     {

         this.radius = radius; //the only thingthat is different from shape
     }

      
     public  void set( Color colour, int x, int y, int radius)
     {
         //note: This is not overridden, it is overloaded as it isn't the same method signature as in Shape
         base.set(colour, x, y);
         this.radius = radius;
     }

    

     public override void draw(Graphics g)
     {

         Pen p = new Pen(Color.Black,2);
         SolidBrush b = new SolidBrush(colour);
         g.FillEllipse(b, x, y, radius * 2, radius * 2);
         g.DrawEllipse(p, x, y, radius * 2, radius * 2);
         base.draw(g);
     }

     public override double calcArea()
     {
         return Math.PI * (radius^2);
     }

     public override double calcPerimeter()
     {
         return 2 * Math.PI*radius;
     }

     public override string ToString() //all classes inherit from object and ToString() is abstract in object
     {
         String text = base.ToString() + "  " + this.radius; 
         return text;
     }
 }

```

The factory creates an object and returns its reference. We then call the newly created object’s set() method to set it up (a job that would formally have been done in the constructor. All the dependencies (new keyword) have been moved into the factory. In this trivial example they were all in the main program anyway, but in reality they would have been spread throughout a larger application, making reuse a nightmare.

# Other Design Patterns
Here is a list of some other design patterns that tend to fit into the three types of Creational, Structural and Behavioural.

## Creational
The Factory Pattern is an example of a Creational Design Pattern.
### Singleton

Ensures only one instance of a particular class exists throughout the lifetime of an application, and it provides a global point of access to that instance. This is useful when exactly one object is needed to coordinate actions across the system—for example, a logging service, configuration manager, or connection pool. In C#, the singleton is typically implemented by making the class constructor private, storing a static reference to the single instance within the class, and providing a public static property or method to retrieve it. This approach prevents multiple instances from being created while still allowing controlled and thread-safe access to the shared resource.```csharp
```csharp
public class Logger
{
    private static Logger _instance;
    private static readonly object _lock = new object();

    private Logger() { }

    public static Logger Instance
    {
        get
        {
            lock (_lock)
            {
                if (_instance == null)
                {
                    _instance = new Logger();
                }
                return _instance;
            }
        }
    }

    public void Log(string message)
    {
        Console.WriteLine(message);
    }
}

// Usage:
Logger.Instance.Log("Application started");
```

## Builder
separates the construction of a complex object from its representation, allowing the same construction process to create different types or configurations of objects. Instead of using a long or confusing constructor with many parameters, the builder provides a step-by-step approach where each method sets a specific property or part of the object. Once all parts are specified, a final Build() method returns the finished object. This makes code easier to read, maintain, and extend, especially when creating objects that require multiple optional or dependent components. In C#, the builder pattern is often implemented using a dedicated Builder class that holds an instance of the object being built and exposes fluent methods to configure it.
```csharp
public class Car
{
    public string Engine { get; set; }
    public string Color { get; set; }

    public override string ToString()
    {
        return "Car with " + Engine + " engine and " + Color + " color";
    }
}

public class CarBuilder
{
    private Car _car = new Car();

    public CarBuilder SetEngine(string engine)
    {
        _car.Engine = engine;
        return this;
    }

    public CarBuilder SetColor(string color)
    {
        _car.Color = color;
        return this;
    }

    public Car Build()
    {
        return _car;
    }
}

// Usage:
CarBuilder builder = new CarBuilder();
Car car = builder.SetEngine("V8").SetColor("Red").Build();
Console.WriteLine(car);
```

## Structural Patterns
### Adapter
Allows incompatible interfaces to work together. It acts as a bridge between an existing class (the adaptee) and a client that expects a different interface (the target). Rather than modifying the existing code—which might be part of a library or legacy system—the adapter wraps the adaptee and translates requests from the client into a form the adaptee understands. This promotes code reusability, flexibility, and separation of concerns, since the client can interact with the target interface without being aware of the underlying implementation details. In C#, this often involves creating a class that implements the target interface and internally holds a reference to the adaptee, delegating method calls to it as needed.

```csharp   
public interface ITarget
{
    void Request();
}

public class LegacyService
{
    public void OldRequest()
    {
        Console.WriteLine("Legacy request called");
    }
}

public class Adapter : ITarget
{
    private LegacyService _service;

    public Adapter()
    {
        _service = new LegacyService();
    }

    public void Request()
    {
        _service.OldRequest();
    }
}

// Usage:
ITarget adapter = new Adapter();
adapter.Request();
```
### Decorator
Allows behaviour to be added to individual objects dynamically, without altering the behavior of other objects from the same class. It achieves this by wrapping the original object within another object (the decorator) that implements the same interface and delegates most operations to the wrapped object, while optionally adding extra functionality before or after those calls. This pattern promotes flexibility and extensibility, as new features can be composed at runtime rather than through inheritance. In C#, the decorator pattern is often used to enhance classes like streams, UI components, or services—allowing developers to build complex behaviors by layering simple, reusable decorators around core functionality.
```csharp
public interface ICoffee
{
    double Cost();
    string Description();
}

public class SimpleCoffee : ICoffee
{
    public double Cost()
    {
        return 2.0;
    }

    public string Description()
    {
        return "Simple coffee";
    }
}

public class MilkDecorator : ICoffee
{
    private ICoffee _coffee;

    public MilkDecorator(ICoffee coffee)
    {
        _coffee = coffee;
    }

    public double Cost()
    {
        return _coffee.Cost() + 0.5;
    }

    public string Description()
    {
        return _coffee.Description() + ", milk";
    }
}

// Usage:
ICoffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
Console.WriteLine(coffee.Description() + " costs " + coffee.Cost());
```

### Facade
Provides a simplified interface to a complex subsystem, making it easier for clients to interact with the system without needing to understand its internal workings. It acts as a single point of access that encapsulates multiple classes or components, coordinating their interactions behind the scenes. This promotes loose coupling and improves code readability by hiding the subsystem’s complexity and exposing only the essential operations through a clear, high-level interface. In C#, the facade pattern is commonly used to simplify operations that involve multiple subsystems—such as starting up a computer system, managing APIs, or handling layered services—allowing the client to perform complex tasks with a single, straightforward method call.
```csharp
public class CPU
{
    public void Start()
    {
        Console.WriteLine("CPU started");
    }
}

public class Memory
{
    public void Load()
    {
        Console.WriteLine("Memory loaded");
    }
}

public class HardDrive
{
    public void Read()
    {
        Console.WriteLine("Hard drive read");
    }
}

public class ComputerFacade
{
    private CPU _cpu;
    private Memory _memory;
    private HardDrive _drive;

    public ComputerFacade()
    {
        _cpu = new CPU();
        _memory = new Memory();
        _drive = new HardDrive();
    }

    public void StartComputer()
    {
        _cpu.Start();
        _memory.Load();
        _drive.Read();
        Console.WriteLine("Computer ready to use");
    }
}

// Usage:
ComputerFacade computer = new ComputerFacade();
computer.StartComputer();
```
## Behavioural Patterns
### Observer
Defines a one-to-many relationship between objects, so that when one object (the subject) changes its state, all its dependent objects (the observers) are automatically notified and updated. This promotes loose coupling, as the subject does not need to know the details of its observers—it simply broadcasts updates, and observers decide how to respond. The pattern is especially useful for event-driven systems, user interface components, and real-time data updates. In C#, the observer pattern is often implemented using interfaces for the subject and observer, or through built-in mechanisms such as events and delegates, allowing efficient and flexible communication between objects while maintaining modular, maintainable code.
```csharp
public interface IObserver
{
    void Update(string message);
}

public class User : IObserver
{
    private string _name;

    public User(string name)
    {
        _name = name;
    }

    public void Update(string message)
    {
        Console.WriteLine(_name + " received: " + message);
    }
}

public class Channel
{
    private List<IObserver> _subscribers = new List<IObserver>();

    public void Subscribe(IObserver observer)
    {
        _subscribers.Add(observer);
    }

    public void Notify(string message)
    {
        foreach (IObserver sub in _subscribers)
        {
            sub.Update(message);
        }
    }
}

// Usage:
Channel channel = new Channel();
channel.Subscribe(new User("Alice"));
channel.Subscribe(new User("Bob"));
channel.Notify("New video uploaded!");
```
### Strategy
Allows an algorithm’s behaviour to be selected at runtime. It defines a family of interchangeable algorithms, encapsulates each one within its own class, and makes them interchangeable through a common interface. This enables a client to choose or change the algorithm without modifying the code that uses it, promoting flexibility, extensibility, and adherence to the Open/Closed Principle. In C#, the strategy pattern is typically implemented by defining an interface that represents a particular operation, such as sorting or calculating, and then creating multiple concrete classes that implement different versions of that operation. The client can then switch strategies dynamically by passing different implementations to a context class, making the system more adaptable and easier to maintain.

```csharp   
public interface ISortStrategy
{
    void Sort(List<int> list);
}

public class BubbleSort : ISortStrategy
{
    public void Sort(List<int> list)
    {
        Console.WriteLine("Sorting using Bubble Sort");
    }
}

public class QuickSort : ISortStrategy
{
    public void Sort(List<int> list)
    {
        Console.WriteLine("Sorting using Quick Sort");
    }
}

public class SortContext
{
    private ISortStrategy _strategy;

    public SortContext(ISortStrategy strategy)
    {
        _strategy = strategy;
    }

    public void SetStrategy(ISortStrategy strategy)
    {
        _strategy = strategy;
    }

    public void ExecuteStrategy(List<int> list)
    {
        _strategy.Sort(list);
    }
}

// Usage:
List<int> list = new List<int> { 3, 1, 2 };
SortContext context = new SortContext(new BubbleSort());
context.ExecuteStrategy(list);
context.SetStrategy(new QuickSort());
context.ExecuteStrategy(list);
```
### Command
Encapsulates a request or action as an object, allowing the client to parameterise, queue, or log requests, and support undoable operations. By decoupling the sender of a request from the object that performs the action, the pattern promotes flexibility and extensibility, as new commands can be added without changing existing code. In C#, the command pattern is typically implemented with an ICommand interface defining an Execute method, concrete command classes that implement the interface, and an invoker class that triggers the commands. This structure makes it easier to manage complex workflows, implement undo/redo functionality, or handle operations dynamically at runtime.

```csharp   
public interface ICommand
{
    void Execute();
}

public class Light
{
    public void On()
    {
        Console.WriteLine("Light is ON");
    }

    public void Off()
    {
        Console.WriteLine("Light is OFF");
    }
}

public class LightOnCommand : ICommand
{
    private Light _light;

    public LightOnCommand(Light light)
    {
        _light = light;
    }

    public void Execute()
    {
        _light.On();
    }
}

public class RemoteControl
{
    private ICommand _command;

    public void SetCommand(ICommand command)
    {
        _command = command;
    }

    public void PressButton()
    {
        if (_command != null)
        {
            _command.Execute();
        }
    }
}

// Usage:
Light light = new Light();
ICommand onCommand = new LightOnCommand(light);
RemoteControl remote = new RemoteControl();
remote.SetCommand(onCommand);
remote.PressButton();
```
