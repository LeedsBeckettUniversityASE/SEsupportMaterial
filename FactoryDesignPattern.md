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
```csharpe
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