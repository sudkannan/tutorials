# Lab 1 - Tools

## Part 1. Getting hands dirty with git

In this part you will try the basics required for updating, 
committing, and submitting your code via. GIT. For more 
details about git at http://gitimmersion.com/index.html


Install git 

```
$ sudo apt-get install git  (in ubuntu)
```

Set your user name and email

```
$ git config --global user.name "Your Name"
$ git config --global user.email "your_email@whatever.com"
```

Get the CS3210 code source from git

```
$mkdir ~/cs3210
$ cd ~/cs3210
$ git clone git://tc.gtisc.gatech.edu/cs3210-lab lab
$ cd lab
```

Now check the status of our source 

```
$ git status

On branch master
nothing to commit, working directory clean
```

Your status will show that you are in the master branch
Now, from your root source directory execute

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

Now lets explore the git .config file.

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
Now, lets try adding a file, and staging our changes
create somefile, say "test.txt"

```
$ git add test.txt
$ git status

On branch lab
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
	modified:  test.txt
```


Now let us commit the changes to the sever

```
$ git commit -m "some comments here"
[lab 569aa96] Using ARGV
 1 files changed, 1 insertions(+), 1 deletions(-)
```


Now, try to revert your changes and open the .git/HEAD file. You will see that HEAD points to your 
current branch lab ref: refs/heads/lab

```
$ git revert HEAD --no-edit
[lab 569aa96] Using ARGV
 1 files changed, 1 insertions(+), 1 deletions(-)
```
Now, lets say, you are experimenting something different in your code, and don't 
want to break your current stable code. So you can try creating a branch.

```
$ git checkout -b "mybranch"
 Switched to a new branch 'mybranch'
```
Now open the .git/HEAD. It will point to ref: refs/heads/mybranch
Lets try adding a new file to mybranch

```
echo "Hello world" >> mycode.txt
git add mycode.txt
git commit -m "adding new file to branch"
```
All your changes, edits and commits will now happen to this branch.
Once you have finished you can switch back to your lab branch

```
$ git checkout lab
```
You will not see mycode.txt.

Now if you want to merge your mybranch changes with your lab
Merge your master with my branch

```
$ git checkout lab
$ git merge mybranch 
```


You will see the following output

```
Updating 9cc5f88..08226cf
Fast-forward
 mycode.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 mycode.txt
```

Now finally you can delete the dummy "mycode.txt" from your 
lab branch


```
$ git rm mycode.txt
$ git commit -m "deleting dummy file"
```


Now if you just want to just push your changes to 
remote repository and not submit the lab assignments

```
$ git push origin lab
```


Submitting your assignments.

 
```
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
```


## Part 2. Getting hands dirty with QEMU 
QEMU includes a built-in monitor that can inspect and modify the machine state in useful ways. To enter the monitor, press Ctrl-a c in the terminal running QEMU. Press Ctrl-a c again to switch back to the serial console. For a complete reference to the monitor commands, see the QEMU manual. Here are some particularly useful commands:

Display a hex dump of N words starting at physical address paddr. If N is omitted, it defaults to 1. This is the physical memory analogue of GDB's x command.
```
xp/Nx paddr
```

Display registers

```
info registers
```

Display a full dump of the machine's internal register state. In particular, this includes the machine's hidden segment state for the segment selectors and the local, global, and interrupt descriptor tables, plus the task register. This hidden state is the information the virtual CPU read from the GDT/LDT when the segment selector was loaded. Here's the CS when running in the JOS kernel in lab 1 and the meaning of each field:

```
CS =0008 10000000 ffffffff 10cf9a00 DPL=0 CS32 [-R-]
CS =0008
```
The visible part of the code selector. We're using segment 0x8. This also tells us we're referring to the global descriptor table (0x8&4=0), and our CPL (current privilege level) is 0x8&3=0.
```
10000000
```
The base of this segment. 
```
Linear address = logical address + 0x10000000.
ffffffff
```
The limit of this segment. Linear addresses above 0xffffffff will result in segment violation exceptions.
10cf9a00
The raw flags of this segment, which QEMU helpfully decodes for us in the next few fields.
DPL=0
The privilege level of this segment. Only code running with privilege level 0 can load this segment.
CS32
This is a 32-bit code segment. Other values include DS for data segments (not to be confused with the DS register), and LDT for local descriptor tables.
[-R-]


 Display mapped virtual memory and permissions
```
info mem
```
For example,
```
ef7c0000-ef800000 00040000 urw
efbf8000-efc00000 00008000 -rw
```
tells us that the 0x00040000 bytes of memory from 0xef7c0000 to 0xef800000 are mapped read/write and user-accessible, while the memory from 0xefbf8000 to 0xefc00000 is mapped read/write, but only kernel-accessible.


Display the current page table structure
```
info pg
```
The output is similar to info mem, but distinguishes page directory entries and page table entries and gives the permissions for each separately. Unbroken sequences of PDE's or PTE's with identical permissions are compressed into a single line, where the number in parenthesis gives the number of PDE's or PTE's in hex. For example,
PDE(001) 00000000-00400000 00400000 urw
 |-- PTE(000008) 00200000-00208000 00008000 urw
PDE(001) 00800000-00c00000 00400000 urw
 |-- PTE(000006) 00800000-00806000 00006000 urw
This shows two page directory entries, spanning 0x00000000 to 0x00400000 and 0x00800000 to 0x00c00000, respectively. The first PDE contains a sequence of 0x8 PTE's spanning 0x00008000 bytes of virtual memory from 0x00200000 to 0x00208000.



## Part 3. Understanding JOS makefile
JOS makefile
The JOS GNUmakefile includes a number of phony targets for running JOS in various ways. All of these targets configure QEMU to listen for GDB connections (the *-gdb targets also wait for this connection). To start once QEMU is running, simply run gdb from your lab directory. We provide a .gdbinit file that automatically points GDB at QEMU, loads the kernel symbol file, and switches between 16-bit and 32-bit mode. Exiting GDB will shut down QEMU.


Build everything and start QEMU with the VGA console in a new window and the serial console in your terminal. To exit, either close the VGA window or press Ctrl-c or Ctrl-a x in your terminal.
```
$ make qemu
```


Like make qemu, but run with only the serial console. To exit, press Ctrl-a x. This is particularly useful over SSH connections to Athena dialups because the VGA window consumes a lot of bandwidth.
```
$ make qemu-nox
```

Start gdb with the settings in the JOS .gdbinit.
```
$ make gdb
```


Like make qemu, but rather than passively accepting GDB connections at any time, this pauses at the first machine instruction and waits for a GDB connection.
```
make qemu-gdb
```

A combination of the qemu-nox and qemu-gdb targets.
```
make qemu-nox-gdb
```

Verbose mode. Print out every command being executed, including arguments.
```
make run-name
```

Stop after any failed grade test and leave the QEMU output in jos.out for inspection.
```
make V=1 ...
make V=1 grade
```

Specify additional arguments to pass to QEMU.
```
make QEMUEXTRA='*args*' ...
```





