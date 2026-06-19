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

## Workflow
### Step 1 Create A Repo
You can do this in GitHub and clone it or you can do it in Visual Studio

**Git\>Create Git Repository**  
Fill in the details, they are self-explanatory.  
Note: If you have been asked to store work in the GitHub Classroom then ensure that you do.  
Click create and push.  
Now we will create a feature branch for the AI to work on.  
**Git\>New Branch**
  
Call it feature-create-star-class  
On your Solution Explorer click on Git you should now see your new branch on a drop down box.  
 
<img width="352" height="237" alt="branchDisplay" src="https://github.com/user-attachments/assets/745e3bfa-aff1-401d-b23e-59df45991248" />


If you click your branch, you will be able to change it to “main”. Main is our main branch that we will only update when we are happy with the changes.

**3 Create a class to store your star data**   
Right click your project and select add-class and call it star.cs

**4Get your local assistant to create the code for a Star**  
I used the following comment with **AI Studio>Code It**

**//create a class to store data for stars, it will need int id, string name, double distance, doubles for x,y and z, double for magnitude, string for spect, all in C\# uppercase variable names with setters and getters.**

Again, that is an easy task but the AI will do it quicker than me.

Right click on each of the methods it generates and **AI Studio\>Add Summary**  
This is an excellent opportunity to review what it created while it generates the documentation. Note also that documentation is also one of our software engineering topics.

It is now time to commit this to our repo.  
Type in an appropriate commit message “AI generated [star.cs](http://star.cs) added” then click the up arrow (push) then the circle arrow (refresh). Now look at your repo on GitHub.  

<img width="615" height="309" alt="mainBranch" src="https://github.com/user-attachments/assets/74c2c1be-5fd3-4691-aa2d-6f77fd3dc6e2" />

You can see that here it is on the master branch but commits have been made to feature-create-star-class  
When I click the drop down on master and swap to feature-create-star-class  
I see  

<img width="615" height="309" alt="featureBranch" src="https://github.com/user-attachments/assets/3ff617bf-5328-47a2-850f-cc71cd95a55c" /> 

You can see it is saying that this branch is 1 commit ahead of the master branch. In reality it is likely to get lots of commits ahead as we would only update the master branch when we have completed a feature. Here it was a pretty minor one and has only taken one commit.
We are now going to enter the **Review Phase** by creating a pull request.  
**Git-\>GitHub\>New Pull Request**   
It will show side by side changes. Here the only change is a load of new code where there was none. But ordinarily there could be a lot of changes that we can accept or reject.  
Click ***Create*** and Give it a sensible name that describes what you have completed.
***Git>GitHub>View Pull Requests***
Click your new pull request on the Window that opens up.
On the top right click ***Merge*** and on the resulting dialogue box select ***Create a merge commmit*** and ***Merge***
This will update the main brach so that it now holds the latest version of the project.
Strictly speaking the role of feature-create-star-class is over and you would probably delete it. Here though I want you to keep all of your branches so that I can see that it has been done properly!
You can create and merge pull requests in GitHub itself. You can also see your open and close pull requests by clicking ***pull requests*** on your repo.

***Doing it with GitHub instead***
You can also do this on the GitHub website and it is often the preferred method for many developers because the web interface is incredibly clean, updates instantly, and gives you a fantastic side-by-side visual comparison of the changes.

Here is exactly how to complete the merge on the GitHub website:

***1. Go to your Repository***

* Open your web browser, navigate to **GitHub.com**, and open your project repository (`NovaLeagueII`).

***2. Open the Pull Requests Tab***

* Along the top menu bar of your repository (next to *Code*, *Issues*, and *Actions*), click on the **Pull Requests** tab.
* You will see a list of active requests. Click on your specific feature branch PR (it will likely say something like `#3` or match the `feature-create-3d-mov...` branch name seen in your screenshot).

***3. Review and Click Merge***

* Scroll down to the bottom of that Pull Request's page.
* If there are no code conflicts, you will see a large, bright green button that says **Merge pull request**.
* Click **Merge pull request**, and then click the green **Confirm merge** button that appears right after it.

***Crucial Final Step: Bring the Code Back to Visual Studio***

Once you click "Confirm merge" on the website, your code is safely merged into `main` on the cloud, but your local computer doesn't know that yet.

Go back to Visual Studio and run these final steps to sync up:

1. Click your branch name in the bottom-right corner of Visual Studio and switch back to **`main`**.
2. Go to the top menu and select **Git > Pull** (or click **Sync**).

Your local machine will pull down the completed work from the cloud, your project will be perfectly up to date, and you're ready to start the next feature!

### Finally

You should complete the above process for every distict feature in your software.

[Here is the repo used in this example](https://github.com/dmullier/3DstarChart)
