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


```
$ cd lab
$ make
+ as kern/entry.S
+ cc kern/init.c
+ cc kern/console.c
+ cc kern/monitor.c
+ cc kern/printf.c
+ cc lib/printfmt.c
+ cc lib/readline.c
+ cc lib/string.c
+ ld obj/kern/kernel
+ as boot/boot.S
+ cc -Os boot/main.c
+ ld boot/boot
boot block is 414 bytes (max 510)
+ mk obj/kern/kernel.img
```

If you get errors, then you need to install Linux 32-bit library. Note, your QEMU PC emulator is 32bit
```
$ sudo apt-get install gcc-multilib
```

Now lets run our OS kernel that we just built using the QEMU emulator. QEMU takes the kernel image as a parameter 
that we just built. To check, open the Makefile in the JOS (lab) directory. Look for the following statement below.

```
qemu: $(IMAGES) pre-qemu
        $(QEMU) $(QEMUOPTS)

```

Your "qemu:" variable is already set with the command for launching the QEMU.

Now lets launch our OS

```
make qemu
```

You will see a separate window with the following output

```
Booting from Hard Disk...
6828 decimal is XXX octal!
entering test_backtrace 0
Welcome to the JOS kernel monitor!
Type 'help' for a list of commands.
K>
```

Now lets install a debugger to debug the PC emulator. You can close the emulator for now.

```
$ sudo apt-get install gdb
```

Now run gdb

```
$ gdb
(gdb)
```

Now open another terminal and navigate to your JOS source directory and execute

```
$ make qemu-gdb
```

Notice that a new QEMU window opens but is blank and waiting.  Now switch to terminal that is running gdb. You will see 
```
[f000:fff0] 0xffff0:    ljmp   $0xf000,$0xe05b
```

The first instruction 'jmp' is executed by the emulator.  Now,  lets step through next  5 instructions using
 ```
(gdb) si 
```

Next, lets try to  set a breakpoint  at the kernel initialize function. The function is defined in kern/init.c. You can also use 
b ( a shorthand for break)
 ```
(gdb) break i386_init 
```

Switch to your QEMU emulator window. You will see a message "Booting from Hardisk...."
 ```
(gdb) si
```

Now in your gdb terminal  step over each code line. Now if you want to look into register use the following command
Using just info will give you list of options to lookup

 ```
(gdb) info registers 
(gdb) info
```
 
Now, lets place a breakpoint at 0x7c00 . This is the location where our boot sector into memory. 
We can set breakpoints by using address also. Continue execution. 

 ```
(gdb) b *0x7c00 
(gdb) c
```
 


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





