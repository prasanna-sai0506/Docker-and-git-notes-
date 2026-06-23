#### **GIT COMMANDS:**



**UNTRACKED FILES: the files in the folder which are need to be added and committed into the git are untracked files.**



**to check the status of the files which are pushed or need to be pushed we can use git status command**



1. **git status --> to the status of the files in the folder. If we add, modify, delete the file in the initialized directory this command will show the status of them.**
2. **git init --> to initialize empty git repository**
3. **git add . --> to add all the files in the folder to the git**
4. **git add file.type --> to add all the specific file into the git**
5. **git commit -m "message to commit" --> This command is use to commit the change to the files that are added into the git. The 'm' in command stands for message.**
6. **git restore --staged <file name> -->If any changes done to the file and not committed but staged (mean added to the git) again and you need to revert it we use the restore command. After restoring again you need to commit the changes done.**
7. **git log --> To check what are the operation or modification done on the initialized repository.**
8. **git reset <hash\_id from the git log> --> It will reset the change done to all above operations or modification ex: delete etc. It will reset up to the hash id given in the command.**
9. **git stash --> stores the status in the back stage. When ever we want we can bring it to the stage and commit them. This changes will not be recorded in the git log.**
10. **git stash pop  --> Used to retrieve the changes of the file from the backstage to front stage.**
11. **git stash clean --> To delete the status stored in the back stage.**
12. **git remote add origin <repository\_url>  --> To attach the repository to the project folder.**
13. **git remote -v --> Displays all the URL attached to the project directory or folder.**
14. **git push origin main/master --> used to push all the changed files into the git hub repository to the master or main branch.**
15. **git branch <branch_name>: This command is used to create a new branch.**
16.	**git branch: this command is used to check the number of origins present in the initialized repository.**
17.	**git checkout <branch_name>: this command is used to point the header to the created branch. **
18.	**git merge <branch_name>: this command is used to merge the committed changes on the other branch to the main branch.**
19. **Note: For in git hub is used to copy the project folder or repository of any public user to our account. we cannot make changes to any other account directly so we need to fork the project **
20.	**git clone <Repository_url>: used to clone the project repository.**
21.	**git remote add upstream <forked_url> : this command is used to add the forked URL to the project.**
22.	**git fetch --all --prune: Used to fetch references from all remotes while cleaning up deleted remote tracking branches.**
23.	**git push origin <branch name> -f:This command is used to force push the contents into the branch.**
24.	**Git reset --hard upstream/main: forcrs the current local branch to match the exat state of the upstream or main branch.**
25.	**Git pull upstream main: git fetch and git pull does the same thing.**
26. **Git rebase -i <hash id> : This is used to merge the multiple commits into the one commit  by converting then into squash and pick.** 


&#x20;

