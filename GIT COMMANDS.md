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

&#x20;

