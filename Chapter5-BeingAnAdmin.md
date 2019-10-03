---
layout: default
title: Chapter 5 - Being an Admin
---

# Assumptions

* You read the last chapter
* You have a basic understanding of the networking topics we have covered.
* You have a server setup with an IP address.

# Accessing your System

To configure your server, you'll most likely use SSH. SSH stands for Secure Shell, and is a very common way to remotely access the command line interface of a system. SSH communications are encrypted, so the commands you type can't be seen if intercepted. 

To SSH into a box (people do use this as a noun), on a Linux system type:
```
ssh <USER>@<IP_OF_SYSTEM>
```
On Windows, use the PuTTY client to ssh into a system.

For both of these, a message will appear indicating if you trust this system. This happens when you first connect to a server, and indicate you want to continue (`yes` or `OK`). This will store the fingerprint of the server on your system, which is used to identify it in the future. This shouldn't change (you'll get a nasty error message if it does), unless you recreate the box (which creates a new fingerprint) or delete the server's fingerprint data.

# Looking Around

When you SSH in, you'll be placed into the user's home directory. You have already seen this, but you can easily refer to the user's home directory with the `~` symbol, so `~/Desktop` means the `Desktop` directory in the current user's home directory. The home directories are usually in `/home/<USERNAME>`, or for root, `/root`.

## The Shell

Usually the shell you are using is `bash` or `sh`. `bash` is actually a upgrade/successor to `sh`, but `sh` is usually still around. You can view your shell by running:

```
echo $SHELL
```

> Lookup bash scripting on how to utilize all the cool features of bash, which is almost a full programming language and allows you to automate tasks on the system.

## Your User

The current user should usually be displayed in the shell something like this:
```
<USER>@<HOSTNAME> $
```

If there is no username in the prompt, run the `id` command to view your current user, the user's uid and the groups the user is in. You can also do
```
id <USERNAME>
```
to view the same info for that user.

## Managing Users

![BlueTeam!](images/blueteam.png) As we've seen before, the simplest way to view existing users on the system is by reading `/etc/passwd`. You can view users that are logged in or have logged in in multiple ways:
* `lastlog`: Prints out the last time the user logged in
* `last`: Prints a list of user logins and system reboots. [More Info](https://www.cyberciti.biz/faq/linux-unix-last-command-examples/)
* `w`: Prints out who is currently logged in.
* Files `/var/log/secure` (CentOS) or `/var/log/auth/log` (Ubuntu): These files contain the logging output in regards to logins locally and remotely.

## Changing User Passwords

This is an important element of your user management, changing their passwords. ![BlueTeam!](images/blueteam.png) Do this especially quick at an event since the existing passwords on the system are usually known to the red team or very easy to guess.

To change your own password, you need to know the existing one. If you don't, well, you're out of luck, unless you know the password for root. Root is the only user that doesn't need to enter the existing password to change it. Root can change anybody's password.

To change the password, run the following command:
```
passwd <USERNAME>
```

> Note: During the next few prompts, no text will appear as you type, this is intentional to hide the password while you type.

If no username is given, the current user is assumed. If you are not root, you will first be prompted for your existing password. After this or if you are root, you will need to enter the new password twice, once to set it and the next to verify it. Now your new password should be in effect.

# Services

Usually, applications such as web servers and email are run on a system as services. Services are processes that run in the background and controlled through a service manager. This service manager helps ensure any processes the service needs are running and that files exist. This also allows services to be centrally managed.

In most recent (as of 2019) Linux distributions, services are managed through two commands. `systemctl` and `service` (which is mostly a wrapper for the `systemctl` command these days.)

### Service Names

Services are referenced by name, which can be different across Linux distributions, for example, the SSH server is referred to as the `ssh` service on Ubuntu, while referred to as `sshd` on CentOS. On Ubuntu, the Apache web server is `apache2` while on CentOS, it is `httpd`.

Mainly you'll have to get used to these naming changes. You can usually find out the service name by looking up that service for that Linux distro. You could also use the following commands to list out the services to see if you can identify the service you are looking for.
```
service --status-all
systemctl list-unit-files
```

### Service Control

By running applications as services gives you centralized control over these servers. This allows you to start, stop and restart service process much easier. If you didn't use the service manager, you'd have to manually start the server process, ensuring the right command line options are given and manually stopping the process (by name or figuring out its process id, or PID). 

#### Basic Controls

To start a service:
```
service <SERVICE_NAME> start
```
or
```
systemctl start <SERVICE_NAME>
```

To stop a service:
```
service <SERVICE_NAME> stop
```
or
```
systemctl stop <SERVICE_NAME>
```

#### Restarting

Almost all service need to be restarted after modifying their configuration files. The easiest way to do this is through the service manager.

To restart a service:
```
service <SERVICE_NAME> restart
```
or
```
systemctl restart <SERVICE_NAME>
```

#### Service Status

Another handy feature of service managers is getting the status of a service. This can tell you if it is running, if the service crashed, and other info.

To get the status of a service:
```
service <SERVICE_NAME> status
```
or
```
systemctl status <SERVICE_NAME>
```

|         |  Navigation  |   |
| :-------------: |:-------------:| -----:|
| [< Ch 4](Chapter4-MoreNetworking) | [Home](index) |   |