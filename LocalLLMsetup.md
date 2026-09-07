**Setting Up Visual Studio with an OnLine LLM**  
Staff and students do get access to Microsoft Office Copilot (as opposed to Microsoft GitHub Copilot), unfortunately this cannot be integrated into Visual Studio, but you can use its chat window to generate code. The advantage is that anything you submit to it is private and not exposed in anyway to the public. If, unlike a member of staff at LBU, you have a subscription to a decent LLM like Gemini, Claude or ChatGPT then you can set that up as your assistant inside Visual Studio. I’m going to set up Gemini, but if you have another, just ask it how to set it up.

ChatGPT doesn’t require this step, Claude and Gemini do. They require a bridge that mimics the same protocols that LM Studio uses. ChatGPT already uses these protocols. We must install LiteLLM and have it running when whenever we are developing.

Open the terminal

pip install litellm  
pip install 'litellm\[proxy\]'

Get your API key from Google or Anthropic

set GEMINI\_API\_KEY=AIzaSyYourCopiedKeyHere 

Choose your model, here I am using Gemini

litellm \--model gemini/gemini-2.5-flash (or whatever model)

#### **In Visual Studio**

#### **1\. For your "Local LLM Chat" Panel (Sidebar Chat)**

* Go to **Tools ➔ Options ➔ Local LLM Chat ➔ General**.  
* **API URL:** `http://127.0.0.1:4000/v1/chat/completions`  
* **Model Name:** `gemini-2.5-pro`

#### **2\. For "AI Studio" (Inline Code Generation & Diff View)**

* Open the AI Studio settings configuration panel.  
* **API URL:** `http://127.0.0.1:4000/v1` *(AI Studio appends its own routes, so it just needs the base link)*.  
* **Model Name:** `gemini-2.5-pro`

**Setting Up Visual Studio with your own Local LLM**  
Note that the following steps are the same for any application that requires a local model, whether that’s other programming environments and languages or other non-software engineering applications.

**Step 1: LM Studio Configuration**  
To host the AI model completely offline on your local machine:

> 1. **Launch LM Studio** and open the **Developer Tools** tab (the code brackets icon \</\> or plug icon on the far left vertical menu).  
> 2. **Load your local model:** Select **Llama-3.2-3B-Instruct-Q8\_0-GGUF\*** from the model dropdown at the top center to load it onto your graphics card hardware. This model is quite small and should work on most laptops. The disadvantage of a smaller model is that it may not remember a conversation for as many steps and it may not be quite as knowledgeable. The advantage is that it will run faster. Feel free to research and experiment and let me know.  
> 3. **Verify the Server status:** Ensure the Local Server toggle is active. The dashboard will display a green status indicator showing that the server is listening locally on port 1234\.  
> 4. **Identify the exact Model ID:** Look at the right-hand sidebar under **API Usage ➔ API Model Identifier**. Take note of the exact text string:  
>    llama-3.2-3b-instruct  
> 5. ![][image1]  
> 6. Note, on my setting above it has set the IP address strangely because I had a VPN running. We will still  [http://127.0.0.1:1234/](http://127.0.0.1:1234/), which is what it would normally be and yours might already say that.  
> 7. It will work regardless but you can make it match by going to **Server Settings** and turning **Server On Local Network** to **Off**  
> 8. ![][image2]  
> 9. Switch **Status: Running** to **Off** for a couple of seconds, then switch it back on  
> 10. It should then show a normal ip address  
> 11. ![][image3]

## **Step 2: Visual Studio & AI Studio Extension Setup**

To connect your IDE directly to your local graphics hardware:

> 1. **Install the Extension:** Inside Visual Studio, navigate to the top menu and select **Extensions ➔ Manage Extensions**. Search the marketplace for [**AI Studio**](https://marketplace.visualstudio.com/items?itemName=ekondur.AI-Studio) (by Emrah Kondur), click **Download**, and close Visual Studio completely to allow the installation wrapper to finish.

> Note if you have trouble installing from Visual Studio, then quite VS, go to the extension’s webpage, download it, then click it to install it.

> 2. **Open Global Settings:** Reopen Visual Studio and navigate to **Tools ➔ Options...**  
> 3. **Configure the AI Pipeline:** Scroll to the bottom of the left column tree, select **AI Studio**, and click **General**. Fill out the connection fields exactly like this:  
   * **API Provider:** OpenAI *(Since LM Studio perfectly mirrors OpenAI's API structure).*  
   * **API Endpoint URL:** http://127.0.0.1:1234/v1/ *(Using the exact IP digits to bypass any local firewall or active network adapters).*  
   * **API Key:** lmstudio *(Acts as a required placeholder text string so you can save).*  
   * **Language Model:** llama-3.2-3b-instruct *(Must precisely match the Model Identifier string from LM Studio).*  
> 4. Click **OK** to save and lock in the pipeline.

## **Step 3: Triggering Code Generation inside your Project**

To use the local AI assistant directly inside your C\# Windows Forms source files without leaving your editor workspace:

> 1. Create a new Windows Form Project  
> 2. In the Designer view create a textfield, two labels and one button  
> 3. Double click on the button to make VS create an event handler and pop up the method it will call (button1\_Click).  
> 4. Open **Form1.cs** in your main editor window.  
> 5. Inside your target code method (e.g., button1\_Click), type out your plain-English prompt requirement as a standard code comment. For example:  
>    C\#  
>    // Take input from textBox1 in astronomical units and convert to millions of kilometers,   
>    // storing the result in label1 and parsecs storing the result in label2, appending units to the end.   
>    // Use double.TryParse for calculation safety.

> 6. Use your mouse cursor to **highlight/select the entire comment text block**.  
> 7. **Right-click** the highlighted text, navigate down to the newly added **AI Studio** option in the context menu, and click **Code It** or **Refactor**.  
> 8. A clean diff/completion pane will split open right next to your active code, instantly streaming your verified maths operations and layout logic directly from your local Llama engine.  
> 9. When I did this it got the calculation for Parsecs the wrong way around (look it up a Parsec is much bigger than an Astronomical Unit (1 AU \= distance from the Earth to the Sun 96 million miles), so when it came up with a massive number for parsecs I knew it was wrong. This is a key point, it gets things wrong, you need to check it. You might be writing software for a NASA space probe as a software engineer, you’re not an astronomer, so you wouldn’t just know and neither might the AI, it has to be checked\!  
> 10. If you do need to refine the maths or logic, simply type your follow-up instruction into the horizontal input box at the very bottom of the **AI Studio Output** tray and hit Enter.  
> 11. Add an extra label and make it show light years.

>   
> 

## **To be a separate document**

## **Using AI Studio**

*Right click on a section of code*  
*![][image4]*  
*Here is exactly what each option in your **AI Studio** right-click context menu does.*  
*Because AI Studio sends your selected text alongside a specific instruction "recipe" over to **LM Studio**, each menu item triggers a completely different behavior from your **Llama 3.2 3B** model.*

### ***Add Unit Tests***

* ***What it does:** It scans your highlighted method or class and automatically writes a companion set of unit tests (typically using NUnit, xUnit, or MSTest frameworks).*  
* ***How it helps you:** If you highlight your maths conversion method, this will generate a test block that inputs specific values (like* 1.0 AU*) and asserts that the outputs match exactly what is expected, helping you catch mathematical or logical regressions automatically.*

### ***Add Summary***

* ***What it does:** It generates standard, structured **C\# XML documentation comments** (*/// \<summary\>*) directly above your method.*  
* ***How it helps you:** If you highlight a complex method, it will automatically populate its parameters, return types, and a clear description. When you hover your mouse over that method later elsewhere in your solution, Visual Studio will display a beautiful IntelliSense tooltip explaining exactly what the code does.*

### ***Add Comments***

* ***What it does:** It injects standard line comments (*//*) directly into the body of your code to document the workflow step-by-step.*  
* ***How it helps you:** It acts as an automatic code-explainer for future maintenance. It will break down your mathematical formulas line-by-line, explaining why a specific multiplier or divisor (like the astronomical constants) is being applied to the variable.*

### ***Refactor***

* ***What it does:** It cleans up, optimizes, and updates the structural design of your highlighted code **without changing its actual behavior**.*  
* ***How it helps you:** If your C\# logic is nested in messy or redundant* if/else *statements, clicking this tells the model to rewrite it using cleaner modern C\# syntax—such as pattern matching, collapsing duplicate lines, or switching to more memory-efficient variable types.*

### ***Explain***

* ***What it does:** It opens the **AI Studio Output** side tray and prints out a clear, plain-English breakdown of what the highlighted code is doing, its complexity, and potential bottlenecks.*  
* ***How it helps you:** It leaves your source code file completely untouched. It is perfect when you open an old or complex file and want a quick executive summary of how the logic flows before making changes.*

### ***Code It***

* ***What it does:** This is your primary **code generation driver**. It takes plain-English pseudo-code or comments that you have highlighted and transforms them completely into compilable, structural C\# code blocks.*  
* ***How it helps you:** This is the tool you just used to turn your description of astronomical units, parsecs, and metric targets into actual Windows Forms layout manipulation logic.*

### ***Security Check***

* ***What it does:** It audits your highlighted code block for vulnerabilities, logical flaws, input sanitization errors, or potential crash exploits.*  
* ***How it helps you:** In a desktop application, it will scan your code to make sure things like text box inputs can't trigger an unexpected* FormatException *or overflow error if a user accidentally types junk data, suggesting protective wrappers like* double.TryParse *if they are missing.*

I didn’t like the code it gave, using hard coded numbers (maybe I could have been clearer in my instructions). The danger of over relying on AI is that you get into a loop of it never quite doing what you want until you have to start from scratch. Far better to just make the simple alteration.  
![][image5]

So I started to type my constant and it guessed what I was trying to do and auto completed it.  
![][image6]  
It didn’t take a genius (luckily) to complete the rest.  
![][image7]  
The temptation is to mindlessly get AI to do everything, but you will soon find out that it never exactly knows what you want and so you end up in a loop of it never being quite right. Instead let it do the complex logic while you continually guide and check it.

If I type something rude into the input box (or anything that’s not a number), I get:  
![][image8]  
An unhandled exception. That’s not good. I can get my Copilot to sort this out for me by right clicking and selecting **AI Studio-\>Security** **Check**, a chatbox opens up and explains everything and gives me some code I can copy.

Or I can put a comment in again and get it to code it.

Now that I look at the code I see that it is structured poorly. Why have I got the calculation in the button’s event method? It should be in its own method. I will get the AI to refactor it by typing a comment inside the method:  
// change this code so that the calculations are done in separate methods and the input is validated before performing the calculations  
I only typed half of this before it anticipated the rest.  
Now right click and have it refactor the code.

### **Using Local LLM Chat with Visual Studio**

If you want a chat style window within Visual Studio where you can brainstorm ideas or have it generate code without affecting your source straight away then you can use another plugin called Local LLM Chat.

Go to Extensions and search for [**Local LLM Chat**](https://marketplace.visualstudio.com/items?itemName=MarkusBegerow.local-llm-chat-vs) and install it (download it if VS won’t install it).  
Configure it by going to **Tools-Options-Local LLM Chat**  
Set the server to [**http://127.0.0.1:1234/v1**](http://127.0.0.1:1234/v1)  
And the model name to **llama-3.2-3b-instruct**  
Once installed you will get **Tools-Open Local LLM Chat** which opens a window for chatting.