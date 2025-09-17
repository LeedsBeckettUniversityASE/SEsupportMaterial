# Using the GDI to create a WinForms App
### Introduction to Creating a Simple Forms Application in Visual Studio (C#)

Here we will create a WinForms project, which has all the necessary assemblies installed, to create a Windows Forms (windowing) project.
To create your first Windows Forms project in Visual Studio:

1. Open Visual Studio and choose **Create a new project**.
2. Select **Windows Forms App (.NET Framework)** or **Windows Forms App (.NET)** depending on your setup.
3. Give your project a name and click **Create**.
4. You’ll see a blank form appear in the designer — this is the window your program will show when it runs.

---

### The `Paint` Method

Behind the scenes, every form and control in WinForms knows how to draw itself onto the screen. The **`Paint` method** is central to this process. It is called whenever a part of your form needs to be redrawn, for example:

* When the form is first shown.
* When the window is resized.
* When it is covered and uncovered by another window.

The `Paint` method receives a special argument called `PaintEventArgs` which contains a `Graphics` object. This object gives you access to drawing tools such as pens, brushes, and shapes. For example, you can use it to draw lines, rectangles, ellipses, or even text directly on the form.

Here’s a basic example:

```csharp
protected override void OnPaint(PaintEventArgs e)
{
    base.OnPaint(e); // ensure the form still draws itself normally

    // Draw a red line across the form
    using (Pen redPen = new Pen(Color.Red, 2))
    {
        e.Graphics.DrawLine(redPen, 10, 10, 200, 10);
    }

    // Draw some text
    e.Graphics.DrawString("Hello, Windows Forms!", 
        new Font("Arial", 12), Brushes.Black, 10, 30);
}
```

---

### Changing the Paint Method

You don’t call `Paint` directly, as this would seize control of the windowing system, which has the respnsibility of updating all windows and not just yours. Instead, you **override** it (as in the example above) or attach to the `Paint` event of a form or control. This gives you complete control over how your form looks.
You provide the method that the windowing system calls (Windows in this case but it is very similar in all systems). Here we told it what method to call by passing it to the event handler. Windows in term will then call our method when it is time to update the form's contents.
This would be when it has been moved, brought to the front etc, or programatically by signalling that it needs to be updated (we can't call it directly remember), this might be because we drawn something onto it and we want to see it.
WHen Windows calls our paint method it passes to it a PaintEventArgs object and this has information about various things including the graphics context of the form itself. Below this is used to draw something onto the form with "e.Graphics".
For example, attaching to the `Paint` event:

```csharp
public Form1()
{
    InitializeComponent();
    this.Paint += new PaintEventHandler(Form1_Paint);
}

private void Form1_Paint(object sender, PaintEventArgs e)
{
    e.Graphics.FillRectangle(Brushes.LightBlue, this.ClientRectangle);
}
```

This code paints the form’s background light blue every time it redraws. Notice the second line with (+= new ...). This is passing a method as a parameter. This is very important because if you use the visual system to create the pain method then it will add this line into the hidden, managed code.
So you might not know it's there, but it is! You can't do this in Java, you have to stick to the name of the method that it wants you to use. In short C# (and C++) have a safe way of passing methods as parameters (called delegates) and Java does not.

---

In summary, the `Paint` method (or the `Paint` event) is your gateway to custom drawing in WinForms. You can keep your forms simple and use built-in controls, or make them unique and interactive by drawing your own graphics.

---

Let’s extend the tutorial and make your Windows Forms project a bit **interactive**. We’ll add a **button** that, when clicked, changes what the `Paint` method draws.

---

## Step-by-Step: Adding Interactivity to Your WinForms Project

### 1. Add a Button

1. In the **Form Designer**, open the **Toolbox** (usually docked on the left).
2. Drag a **Button** onto your form.
3. Change its properties in the Properties window:

   * `Name` → `btnChangeDrawing`
   * `Text` → `Change Drawing`

---

### 2. Add a Field for State

We’ll use a class-level variable to decide **what to draw**.

```csharp
public partial class Form1 : Form
{
    private bool drawShapes = true;  // keeps track of what to draw

    public Form1()
    {
        InitializeComponent();
        this.Paint += new PaintEventHandler(Form1_Paint);

        // Attach button click event
        btnChangeDrawing.Click += BtnChangeDrawing_Click;
    }
}
```

---

### 3. Handle the Button Click

When the button is clicked, toggle the state and **refresh** the form (which calls `Paint` again):

```csharp
private void BtnChangeDrawing_Click(object sender, EventArgs e)
{
    drawShapes = !drawShapes; // flip the state
    this.Invalidate();        // forces the form to redraw
}
```

---
As mentioned above we can programatically signal to Windows that we want our form to be redrawn and Windows will, as soon as it can. This is done with the Invalidate() call. This is an inherited method that signals that the display contents are now invalid and need to be redrawn.

### 4. Update the Paint Method

Now, change the drawing depending on `drawShapes`.

```csharp
private void Form1_Paint(object sender, PaintEventArgs e)
{
    e.Graphics.FillRectangle(Brushes.White, this.ClientRectangle);

    if (drawShapes)
    {
        // Draw shapes
        using (Pen bluePen = new Pen(Color.Blue, 3))
        {
            e.Graphics.DrawRectangle(bluePen, 50, 50, 200, 100);
        }

        using (Pen redPen = new Pen(Color.Red, 3))
        {
            e.Graphics.DrawEllipse(redPen, 300, 50, 100, 100);
        }

        e.Graphics.DrawString("Shapes Mode", 
            new Font("Arial", 14, FontStyle.Bold), Brushes.Black, 50, 200);
    }
    else
    {
        // Draw different graphics
        e.Graphics.FillEllipse(Brushes.Green, 100, 100, 200, 150);
        e.Graphics.DrawString("Alternative Drawing!", 
            new Font("Arial", 14, FontStyle.Italic), Brushes.DarkRed, 50, 300);
    }
}
```

---

### 5. Run the Program

* Start the app.
* At first, you’ll see the **shapes mode** (blue rectangle + red circle).
* Click the **Change Drawing** button → it switches to the **alternative drawing** (green filled ellipse + text).
* Click again to toggle back.

---

 **You now have a dynamic, interactive WinForms app!**

* The **button** changes program state.
* `Invalidate()` tells the form to redraw, which calls `Paint`.
* You’re mixing **GUI controls** with **custom graphics**, which is the heart of more advanced Windows Forms apps.

---
Awesome 😃 — let’s take this one step further and turn it into a **mini drawing app**, where you can draw on the form with the mouse!

We’ll keep it simple: every time you **click with the mouse**, the form will draw a circle at that point.

---

## Step-by-Step: Building a Simple Drawing App

### 1. Add a List to Store Drawn Shapes

We need a place to remember where the user clicked, so we can redraw the circles every time the form refreshes.

```csharp
public partial class Form1 : Form
{
    private List<Point> circles = new List<Point>(); // store circle centers

    public Form1()
    {
        InitializeComponent();
        this.Paint += new PaintEventHandler(Form1_Paint);

        // Handle mouse clicks
        this.MouseClick += Form1_MouseClick;
    }
}
```

---

### 2. Handle Mouse Clicks

When the user clicks, save the click location and force the form to redraw.

```csharp
private void Form1_MouseClick(object sender, MouseEventArgs e)
{
    // Add the clicked location
    circles.Add(e.Location);

    // Force redraw
    this.Invalidate();
}
```

---

### 3. Draw the Circles in `Paint`

Now, update the `Paint` method to draw all the circles in the list.

```csharp
private void Form1_Paint(object sender, PaintEventArgs e)
{
    e.Graphics.FillRectangle(Brushes.White, this.ClientRectangle);

    using (Brush circleBrush = new SolidBrush(Color.LightBlue))
    using (Pen circlePen = new Pen(Color.DarkBlue, 2))
    {
        foreach (Point p in circles)
        {
            // Draw circle centered at click
            int radius = 20;
            e.Graphics.FillEllipse(circleBrush, p.X - radius, p.Y - radius, radius * 2, radius * 2);
            e.Graphics.DrawEllipse(circlePen, p.X - radius, p.Y - radius, radius * 2, radius * 2);
        }
    }

    // Optional text hint
    e.Graphics.DrawString("Click to draw circles!", 
        new Font("Arial", 12), Brushes.Black, 10, 10);
}
```

---

### 4. Run the Program

* Launch the app.
* Every time you **click inside the form**, a circle appears where you clicked.
* Try resizing or covering the window — the circles remain because they’re stored in the `List<Point>` and redrawn every time `Paint` runs.

---

### 5. Optional Extensions 🚀

You can expand this mini drawing app in fun ways:

* **Different shapes:** use right-click for squares, left-click for circles.
* **Colors:** add buttons to switch between red, blue, green drawing.
* **Clear canvas:** add a “Clear” button to wipe the `List<Point>`.
* **Mouse drag drawing:** handle `MouseMove` to draw as you drag.

---
Now you’ve gone from a **basic form** → to **custom drawing** → to an **interactive button toggle** → and finally to a **mini drawing app with mouse input**.

---

