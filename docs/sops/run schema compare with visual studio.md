# Schema compare with visual studio

This SOP will guide you through a set of repeatable steps to compare your database against a repository and compare changes between them. This makes it easy to detect changes in a database and prevents manual human errors.

## Requirements
- Access to Visual Studio, not to be confused with Visual Studio Code
- Repository for the database files to be compared in
- Database to compare with

## Walkthrough

First of navigate towards Visual Studio, once there you get the option to open your repository. Select the folder where your repository resides in. Once it's open make sure you have the latest version of the online repository by pulling it from remote.

!!! note

    Some images are large, make sure to click on them if they aren't readable for you. If you aren't much of a reader it also allows you to cycle through each image beautifully.

![](../assets/schema%20compare/01%20-%20Pull%20the%20repository.png)

You now have the lastest version of your repo. Next you will have to create a branch so that your peers can review your changes.

![](../assets/schema%20compare/02%20-%20Create%20a%20new%20branch.png)

Make you sure you have changed to your branch by looking at the bottom bar. It should have the name of your new branch. Up next will be checking your repository for any solutions. Solutions are copies of each database that you have.

![](../assets/schema%20compare/03%20-%20Check%20solution%20explorer.png)

Next a window will open which will be schema compare window. Press "Compare" to see the changes.

![](../assets/schema%20compare/04%20-%20Run%20compare.png)

![](../assets/schema%20compare/05%20-%20Compare%20screen.png)

Make sure to select the changes that you want to bring to production and deselect changes that aren't relevant. Once that's done, press "update"

![](../assets/schema%20compare/06%20-%20Press%20update%20next%20to%20the%20compare%20button.png)

Make sure to commit the changes you have made to the branch and push them too so your peers can make a review

![](../assets/schema%20compare/07%20-%20Commit%20your%20changes.png)
![](../assets/schema%20compare/08%20-%20Push%20your%20changes.png)