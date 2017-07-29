<link href="http://kevinburke.bitbucket.org/markdowncss/markdown.css" rel="stylesheet"></link>

# LearnGit #

-------------------------------------------------

## Git Command ##

### Create Repositary ###
1. $ mkdir foldername 
2. $ cd foldername `//create a repositary`
3. $ pwd
4. $ git init `//initialize this repositary`

### Add&Commit ###
1. $ git add somefile.file `//add somefile to git stage`
2. $ git commit -m "desciption" `//commit to git`

### Status&Difference ###
1. $ git status `//show status of the working zone`
2. $ git diff somefile.file `//show the modification of somefile`

### Reset&Logging ###
1. $ git reset --hard HEAD^ `//return to the last version`
	// HEAD is a pointer pointing to the current version of file
	// '^' means last version, '^^' means two version ago
	// also, '^' can be represented by HEAD~(1)
	// "^^" can be represented by HEAD~(2)
	
2. $ git reset --hard 2ab1237f... `//return to the version corresponding to this version#`
	//2ab1237f... is a version#
3. $ git log `//show the records of modifying files`
4. $ git reflog `//show the records of your commands`

### Cancle Modificatin ###
1. Use $ git checkout --file to cancle the modification
   that is not added to the stage.
2. If you add the wrong file to the stage, use $ git reset HEAD file
   to cancle the adding, then use $ git checkout --file.
3. If you commit the modification after adding to stage,
   use $git reset --hard HEAD^ to return the last version

### Delete File ###
1. $ git rm file `//remove file`
2. $ git commit -m "description" `//commit`

### Branch ###
1. $ git checkout -b dev `//'-b' means create a new branch and switch to it`
2. $ git branch dev `//create a new branch dev`
3. $ git checkout dev `//switch to dev`
4. $ git branch `//show all the branches, with a '*' at current branch
5. $ git checkout master `//switch to master after complete dev`
6. $ git merge dev `//merge the dev branch`
7. $ git branch -d dev `//delete dev branch`
8. $ git branch -D dev `//delete dev even if it is not merged`

### Stash ###
1. $ git stash `//save current working status`
2. $ git stash list `//show the stash list`
3. $ git stash apply `//apply the stash`
4. $ git stash drop `//drop the stash`
5. $ git stash pop `//apply and drop the stash`
6. $ git stash pop stash@{0} `//apply and drop a specific stash`

### Tag ###
1. $ git tag <name> `//create a new tag`
2. $ git tag `//show all the tags`
3. $ git tag <name> <version#> `//label the tag to specific version#`
4. $ git show <tagname> `//show the details of the tag`
5. $ git tag -d <name> `//delete a tag`
6. $ git push origin <tagname> `//push to remote server`
7. $ git push origin --tags `//push all tags to remote server`
8. & git push origin :refs/tags/<name> `//delete a tag from the remote server`