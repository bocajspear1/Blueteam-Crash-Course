---
layout: default
title: Chapter 2 - Linux Basics
---

# Assumptions

* You got the VM running form the last chapter. Test out the commands you learn while you read.

> Note: You'll starting see these hats around: 
> ![RedTeam!](images/redteam.png) < This means to look out for red team stuff surrounding this topic.
> ![BlueTeam!](images/blueteam.png) < This means this is important to blue teams (you!). Be sure to know stuff about this! (Use Google to find more info)

# Basics

The following site provides some good essentials in an easy format. Start here: 
* https://ryanstutorials.net/linuxtutorial/

## Going Faster

Using "Tab Complete" will attempt to autocomplete commands or file paths. It will complete to as far as is unique (no other commands or files match). If nothing appears when you press tab, this means there are multiple possibilities. Press tab to see your options. 

> This is a great way to find commands if you've forgotten exactly how they are spelled.

You can also use the arrow keys to move around the console. Pressing up or down goes back in forth in the command history (it tracks what you typed before. The entire history can be viewed with the `history` command) while pressing left and right moves the cursor on the line (useful if you mis-typed something)

## Users 

This is mentioned a bit in the "Permissions" page in the tutorial above, but I'll give a little more detail on users and groups.

### Linux Users

Linux supports many users (in fact, your server already has a bunch of users!), and knowing what to do with users and groups is important to managing a Linux system. Linux users depend on their `uid`, or user id. The name doesn't matter, just the uid.

### Root

![RedTeam!](images/redteam.png) ![BlueTeam!](images/blueteam.png) The most important user in a system is `root`. Root has a uid of 0, and any user with a uid of 0 is root (remember Linux only cares about the uid, and multiple usernames can have the same uid). Root is your administration account, keep it safe! Don't forget the password to access it or remove it! This account can do **anything** on the system, including changing critial applications and configurations.

> The goal of any red team or hacker is to get root!

Ubuntu, by default, does not allow direct root access to keep things a bit safer. To run a command as root in Ubuntu you can use `sudo`. `Sudo` is related to the `su` command, which is short for "switch user." (Note the `su` in `sudo`) On other systems, you could run `su -` (the `-` ensures you login properly as that user), type the password of the root user and become root. Here, we can't do that, so we use `sudo`. To run a command as root using `sudo`, type:
```
sudo <COMMAND>
```
Then type **your** password. `sudo` is nice as it allows you to run `sudo` again without re-entering the password for a period of time. If you want, you could type:
```
sudo su - 
```
to switch to root, since you're essentially switching to root from root!

`Sudo` is also useful, since it can restrict users to only running certain commands as root. 

### Viewing Users

User info is stored in `/etc/passwd`. Password info for users is stored in `/etc/shadow`. These are just text files listing out users, so you can use `cat` to view them and other text tools to edit them. This makes it possible to add and remove users just by editing a file! ![RedTeam!](images/redteam.png)

`/etc/passwd` is readable by everybody. (The format can be viewed [here](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/)). The main thing we care about is that it gives the username and uid. 

`etc/shadow` is readable only by root. (The format can be viewed [here](https://www.cyberciti.biz/faq/understanding-etcshadow-file/)). This gives contains out passwords, though put a process called "hashing," which is essentially a one-way encryption. Passwords you type into prompts are hashed with the same the hash process and compared. This allows checking if a password is same without storing the password in the open (in plain-text).

### Groups

All users are in one or more groups. These group users together to be given permissions all at once. Group info is stored in `/etc/groups`, which is also a text file. (The format can be viewed [here](https://www.cyberciti.biz/faq/understanding-etcgroup-file/))

## Blue Team Tips

![BlueTeam!](images/blueteam.png) Here's some blue team tips:
* Use `history` to see what commands have been executed before. See if anybody's been up to something.
* Check your environment using `env` to see what variables (like `$SHELL`)
* Keep `/etc/passwd` and especially `/etc/shadow` safe. Watch them for modifications by red team!
* Use `ls -la` to view all files and find dotfiles (`.file`)
* Use `file` to check what type a file is. This is great is for determining if something is not what it should be. Experiment with your system to get an idea of what things should and shouldn't be.


# Futher Reading

* [More Linux Basics](https://www.funtoo.org/Linux_Fundamentals,_Part_1)




|         |  Navigation  |   |
| :-------------: |:-------------:| -----:|
| [< Ch 1](Chapter1-GettingStarted) | [Home](index) | [Ch 3 >](Chapter3-NetworkingBasics)  |



{% include footer.html %}