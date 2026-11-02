# Building Documentation with DocFX and Hosting on GitHub
Building and hosting documentation for a Visual Studio C# project using **DocFX** and **GitHub** involves generating static HTML pages from your source code's XML documentation comments and markdown files, then automating the deployment using GitHub Actions. 

Firstly create an example console project in Visual Studio and call it SmartHomeSimulator (you can call it what you like but you'll have to change the namespaces below to match).

Now we'll add some code that is already documented. Note this is about building a documentation site and not the fact that this code doesn't really do anything when run.

Add a new item (right click on th eproject and select new->item)
Call it IDecice and copy the code below into it.

```csharp
using System.Xml;

namespace SmartHomeSimulator
{


    /// <summary>
    /// Represents a generic controllable smart home device.
    /// </summary>
    public interface IDevice
    {
        /// <summary>
        /// Gets the unique identifier of the device.
        /// </summary>
        string DeviceId { get; }

        /// <summary>
        /// Gets a value indicating whether the device is currently turned on.
        /// </summary>
        bool IsOn { get; }

        /// <summary>
        /// Turns the device on.
        /// </summary>
        void TurnOn();

        /// <summary>
        /// Turns the device off.
        /// </summary>
        void TurnOff();
    }
}
```


Create another file called Thermostat.cs and add
```csharp
namespace SmartHomeSimulator
{
/// <summary>
/// Represents a smart thermostat capable of monitoring and adjusting room temperature.
/// </summary>
    public class Thermostat : IDevice
    {
        private double _currentTemperature;

        /// <inheritdoc />
        public string DeviceId { get; }

        /// <inheritdoc />
        public bool IsOn { get; private set; }

        /// <summary>
        /// Gets the current temperature measured by the thermostat in Celsius.
        /// </summary>
        public double CurrentTemperature => _currentTemperature;

        /// <summary>
        /// Gets or sets the target temperature set point in Celsius.
        /// </summary>
        public double TargetTemperature { get; set; }

        /// <summary>
        /// Initializes a new instance of the <see cref="Thermostat"/> class.
        /// </summary>
        /// <param name="deviceId">The unique identifier for the thermostat.</param>
        /// <param name="initialTemperature">The starting ambient temperature.</param>
        public Thermostat(string deviceId, double initialTemperature)
        {
            DeviceId = deviceId;
            _currentTemperature = initialTemperature;
            TargetTemperature = 21.0; // Default comfortable room temp
            IsOn = false;
        }

        /// <summary>
        /// Adjusts the target temperature by a specified delta value.
        /// </summary>
        /// <param name="delta">The amount to increase or decrease the target temperature.</param>
        /// <returns>The newly updated target temperature.</returns>
        /// <exception cref="InvalidOperationException">Thrown if the thermostat is currently turned off.</exception>
        public double AdjustTargetTemperature(double delta)
        {
            if (!IsOn)
            {
                throw new InvalidOperationException("Cannot adjust temperature while the thermostat is off.");
            }

            TargetTemperature += delta;
            return TargetTemperature;
        }

    /// <inheritdoc />
        public void TurnOn() => IsOn = true;

    /// <inheritdoc />
        public void TurnOff() => IsOn = false;
    }
}
```

Then one called SmartHub.cs
```csharp
using System.Collections.Generic;

namespace SmartHomeSimulator
{
    /// <summary>
    /// Manages a collection of connected smart home devices within a household.
    /// </summary>
    public class SmartHub
    {
        private readonly List<IDevice> _devices = new();

        /// <summary>
        /// Gets a read-only collection of all registered devices in the hub.
        /// </summary>
        public IReadOnlyCollection<IDevice> Devices => _devices.AsReadOnly();

        /// <summary>
        /// Registers a new device with the smart hub.
        /// </summary>
        /// <param name="device">The device instance to add.</param>
        public void RegisterDevice(IDevice device)
        {
            if (!_devices.Contains(device))
            {
                _devices.Add(device);
            }
        }

        /// <summary>
        /// Sends a broadcast command to turn off all registered devices.
        /// </summary>
        public void EmergencyShutdown()
        {
            foreach (var device in _devices)
            {
                device.TurnOff();
            }
        }
    }
}
```
It will have created program.cs that hides the main class and method, replce this horrible thing with:

```csharp
namespace SmartHomeSimulator
{
    /// <summary>
    /// The main entry point for the smart home simulation application.
    /// </summary>
    public class Program
    {
        /// <summary>
        /// Executes the simulation.
        /// </summary>
        /// <param name="args">Command-line arguments.</param>
        public static void Main(string[] args)
        {
            SmartHub hub = new SmartHub();
            Thermostat livingRoom = new Thermostat("TH-01", 18.5);

            hub.RegisterDevice(livingRoom);
            livingRoom.TurnOn();
        }
    }
}
```

We've now got a complete example project, just ensure it builds with no syntax errors.

Now we have an example we can create  our documentation site.

### **Phase 1: Enable XML Documentation in Visual Studio**
DocFX builds API references by reading the XML documentation files generated by the C# compiler.

1. Open your C# project or solution in Visual Studio.

2. Right-click your project ->**Properties**.

3. Go to the **Build** tab (or **Build ->Output** depending on your Visual Studio/.NET version).

4. Check the box for **XML documentation file** so that Visual Studio outputs an `.xml` file alongside your compiled binaries on every build.
### **Phase 2: Install and Initialize DocFX**
DocFX is managed as a .NET global tool. 

1. Install or update DocFX via your terminal/command-line: 

`dotnet tool update -g docfx` 

2. Navigate to your solution or project root directory and initialize a new docset:

`docfx init` 

```
Name (mysite): Smart Home Simulator
Generate .NET API documentation? [y/n] (y): y
.NET projects location (src): .
Markdown docs location (docs): docs
Enable site search? [y/n] (y): y
Enable PDF? [y/n] (y): n
```

*This creates a configuration file (*`docfx.json`*), a template structure, an* `articles/` *folder for manual markdown guides, and an* `index.md` *homepage.* 

1. **Configure** `docfx.json`**:** Open the `docfx.json` file and point the `metadata` section to your C# project file(s) (`.csproj`) so DocFX knows where to pull the source code and XML comments.


The standard DocFX file is too generic, as it expects a directory structure that Visual Studio does not use (source in an src directory). So change it to:

```json
"metadata": [
    {
      "src": [
        {
          "src": ".",
          "files": [
            "SmartHomeSimulator.csproj"
          ]
        }
      ],
      "dest": "api"
    }
  ]
```

### **Phase 3: Build and Test Locally**
1. Run the DocFX build and serve command from the directory containing your configuration file: 

`docfx docfx.json --serve` 

1. Open your browser and navigate to [**http://localhost:8080](http://localhost:8080/)** to preview your newly styled documentation site, complete with automated API reference trees and conceptual pages.

You should now see your documentation site running on a web server on your machine.



**Step 1: Generate the Website**

1. Open your repository folder in Visual Studio.

2. Open your terminal and ensure it is pointing to the root directory of your repository (where the `docfx.json` file is located). 

3. Run the following command: `docfx docfx.json`

4. Look in your file explorer to verify that a new folder named `docs/` has been created at the root of your repository. This folder contains your `index.html` and a `public/` styling folder.

**Step 2: Bypass GitHub's Jekyll Processor (Critical)**
Jekyll is a different documentation generation system to DocFX and we want to make sure Github does not use it.

By default, GitHub Pages uses a processor called Jekyll that will block your DocFX styling (CSS and logos) from loading.

1. Open the newly generated `docs/` folder.

2. Create a new, completely empty text file inside it and name it exactly `.nojekyll` (you must include the leading dot).

**Step 3: Push to GitHub**

1. Open your Git changes panel in your VS.

2. Stage the entire `docs/` folder (ensure the hidden `.nojekyll` file is included in the staged changes). Where it says "changes" click the three dots to the right and select "tree view", then you will see the docs directory, click it and click the +.

1. Commit your changes with a message like "Generated BOOSE documentation" and push to your main branch on GitHub.

**Step 4: Enable GitHub Pages**

1. Go to your repository on the GitHub website.

2. Click the **Settings** tab at the top.

3. On the left sidebar, click **Pages**.

4. Under the "Build and deployment" section, ensure the **Source** dropdown is set to "Deploy from a branch".

5. Under the **Branch** heading, select `main` from the first dropdown, and select `/docs` from the folder dropdown.

6. Click **Save**.

Within 1 to 2 minutes, GitHub will finish building your site in the background. A link will appear at the top of the Pages settings screen (or on your repository homepage under "Environments") where you can view your live documentation.

Note it is not running locally and you can publish this web address.


