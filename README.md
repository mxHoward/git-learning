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

