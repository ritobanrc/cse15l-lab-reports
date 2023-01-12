# Week 1 Lab Report

Computer science courses often require logging in to a remote machine in order to do work. They often also have course-specific accounts setup on these machines, setup with software necessary for the course. I'll show you how to log in to such an account at UCSD in this post!

## Getting a Terminal
First, you'll need a terminal. The easiest way to get this is to install VSCode (which I already had on my computer, and you probably will too!). If you're using Linux, it should be as easy as `sudo pacman -S code` or opening up your software center and clicking install. If you're using a different operating system, I'm sure you can figure it out :D. 

Once you have VSCode open, click the terminal button as shown below:
![VSCode open with a red box around the Terminal button](images/vscode.png)

Alternatively, just open a terminal directly (I'm going to be using [`alacritty`](https://github.com/alacritty/alacritty) for the rest of the tutorial), but everything should just work identically if you're using VSCode's terminal or some other terminal.

## Remotely connecting
Now, you'll need to get your course-specific user account. At UCSD, you can get this from [UCSD's ETS Account Lookup](https://sdacs.ucsd.edu/~icc/index.php). After putting your AD username and student ID in, then click the button that says something like `cs15lwi23abc`. That's your username. You'll need to follow the rather convoluted instructions [here](https://docs.google.com/document/d/1hs7CyQeh-MdUfM9uv99i8tqfneos6Y8bDU0uhn1wqho/edit) to reset your password the first time [^1].

Now, once you have your username and password, you'll log into `ieng6.ucsd.edu`. Run the following command in your terminal (replacing `abc` with your own username).

```bash
ssh cs15lwi23ach@ieng6.ucsd.edu
```

You'll have to type `yes` in to the "The authenticity of host 'ieng6.ucsd.edu (128.54.70.227)' can't be established." message the first time you connect, and then type in your password, but after that, you should see something like this:

![Output of ssh-ing into ieng6](images/ssh.png)

I have a `TERM=screen-256color` before my `ssh` command. This is just because I'm using `tmux` on my local computer, and `ieng6` gives a bunch of `tput: unknown terminal "tmux-256color"` errors and doesn't show the bolding or colors correctly (incidentally, it also does not recognize `alacritty` as a terminal, the same trick fixes it there).

## Try running some commands
Finally, now that you're running commands on `ieng6`, try poking around. Here are some examples (I already have some files in my home directory, don't worry if you have a lot fewer):

Here's an example of me poking around (as is common in linux systems, you can find all sorts of information if you know where to look, like instructions about how to _setup_ a class as an instructor, or all the header files you might include if you're writing a C/C++ program).

```bash
ls -lat
ls /home/linux/ieng6/cs15lwi23/public/
cat /home/linux/ieng6/cs15lwi23/public/README.instructor
pwd
cd /usr/include
ls
```

Here's the output from those commands (long outputs have been truncated for space).

![outputs of ls commands](images/ls.png)
![output of cat-ing the readme](images/readme.png)
![output of ls in /usr/inclue](images/include.png)

As you can see, poking around a Linux system can be very fruitful -- have fun with it!


[^1]: As we discovered in lab, Safari seems to have problems with the password reset tool (it doesn't actually make a POST-request when you press enter in a textbox). If something similar happens to you, try using Chrome or Firefox.


