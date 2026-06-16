# Using GitHub with AI Assisted Programming
Effectively we are now working in a team of two. Looking back at the GitHub lecture we know all of the pros and cons that this involves and that’s why we use version control systems such as GitHub (other version control systems exist). Our partner, the AI, might do something we don’t like and so we will use version control to keep a strict check on it, so that we can undo any changes we don’t like, or accept them if they meet our strict requirements, we are in control.

Any project is adding a series of facilities. Each facility should have a new branch in the repo. We then proceed to develop the facility with the AI. When we are happy that the facility is finished we will merge the current facility branch into the main branch and repeat with the next facility. This maintains the integrity of the main branch, as it will always hold a clean version of the application with the latest facility working.   

### It Creates a "Sandbox" for the AI
LLMs are fantastic, but they can easily fall into "hallucination loops," suggest outdated code, or completely mess up formatting.
Without Branches: If the AI writes 50 lines of broken code directly into your main branch, you have to manually untangle it or risk doing a destructive git reset --hard that might wipe out your own good work.
With Branches: If the AI completely destroys a feature branch, you don't have to panic. You can simply delete that branch and start a new one from main in five seconds.
### The Pull Request is Your "Code Review" Gate
Even if you are a solo developer and aren't pushing to GitHub.com, Visual Studio allows you to look at a local Pull Request or a Diff View before merging.
This forces a moment of pause where you can review exactly what lines of code the local LLM changed.
It stops you from accidentally merging hidden bugs, temporary testing classes, or unwanted file deletions into your clean main branch.

Workflow for GitHub in Visual Studio
```git
git checkout main
git pull
git checkout -b feature/read-star-chart
```
