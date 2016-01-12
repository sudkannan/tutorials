# Lab 1 - Tools

## Part 1. Getting hands dirty with git

In this part you will try the basics required for updating, 
committing, and submitting your code via. GIT. For more 
details about git at http://gitimmersion.com/index.html


1. Item 1
2. Item 2
3. Item 3
   * Item 3a
   * Item 3b

1. Install git. 

```
$ sudo apt-get install git  (in ubuntu)
```

2. Set your user name and email

```
$ git config --global user.name "Your Name"
$ git config --global user.email "your_email@whatever.com"
```

3. Get the CS3210 code source from git

```
$mkdir ~/cs3210
$ cd ~/cs3210
$ git clone git://tc.gtisc.gatech.edu/cs3210-lab lab
$ cd lab
```

4. Now check the status of our source 

```
$ git status
```

Your status will show that you are in the master branch
On branch master
nothing to commit, working directory clean


5. Now, from your root source directory execute

```
$ ls -C .git

Your output will be
COMMIT_EDITMSG	ORIG_HEAD	index		objects
HEAD		config		logs		refs
```


The .git directory contains all the bookeeping information required
to track versions.

Try executing the following command to see all the objects in git

```
$ ls -C .git/objects
```


6. Now lets explore the git .config file.

```

[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = git://tc.gtisc.gatech.edu/cs3210-lab
        fetch = +refs/heads/*:refs/remotes/origin/*

```
Look into the origin and the branch information


7. Now, lets try adding a file, and staging our changes
create somefile, say "test.txt"

```
$ git add test.txt
$ git status
```


you will see the following output

```
On branch lab
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
	modified:  test.txt
```


8. Now let us commit the changes to the sever

```
$ git commit -m "some comments here"
[lab 569aa96] Using ARGV
 1 files changed, 1 insertions(+), 1 deletions(-)
```


9. Now, try to revert your changes

$ git revert HEAD --no-edit
[lab 569aa96] Using ARGV
 1 files changed, 1 insertions(+), 1 deletions(-)

Open the .git/HEAD file. You will see that HEAD points to your 
current branch lab ref: refs/heads/lab

---------------------------

10. Now, lets say, you are experimenting something different in your code, and don't 
want to break your current stable code. So you can try creating a branch.

$git checkout -b "mybranch"
 Switched to a new branch 'mybranch'

Now open the .git/HEAD. It will point to ref: refs/heads/mybranch

Lets try adding a new file to mybranch

echo "Hello world" >> mycode.txt
git add mycode.txt
git commit -m "adding new file to branch"

All your changes, edits and commits will now happen to this branch.
Once you have finished you can switch back to your lab branch

$git checkout lab

You will not see mycode.txt.
------------------------------

11. Now if you want to merge your mybranch changes with your lab

$git checkout lab

Merge your master with my branch
$git merge mybranch 

You will the following output

Updating 9cc5f88..08226cf
Fast-forward
 mycode.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 mycode.txt

Now finally you can delete the dummy "mycode.txt" from your 
lab branch
 
$ git rm mycode.txt
$ git commit -m "deleting dummy file"

---------------------------------
12. Now if you just want to just push your changes to 
remote repository and not submit the lab assignments

$ git push origin lab
---------------------------------

13. Submitting your assignments.

$ git commit -am "ready to submit my lab"
[lab1 c2e3c8b] ready to submit my lab
 2 files changed, 18 insertions(+), 2 deletions(-)

$ make handin
git archive --prefix=lab1/ --format=tar HEAD | gzip > lab1-handin.tar.gz

Get an API key for yourself by visiting https://tc.gtisc.gatech.edu/cs3210/2016/submit/handin.py

Please enter your API key: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 50199  100   241  100 49958    414  85824 --:--:-- --:--:-- --:--:-- 85986$

-----------------------------------------------------------------------
Part 2. Getting hands dirty with QEMU 
-----------------------------------------------------------------------
-TBD-



-----------------------------------------------------------------------
Part 3. Understanding JOS makefile?
-----------------------------------------------------------------------


