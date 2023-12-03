---
layout: post
title: TryHackMe - Tardigrade
date: 2023-12-02 09:08:00 -500
categories: [TryHackMe]
tags: [digital-forensics, blue-team]     # TAG names should always be lowercase
img_path: /assets/img/Tardigrade/
---

# Introduction
Today, we will be exploring TryHackMe's Tardigrade room found at: `https://tryhackme.com/room/tardigrade`. This challenge is marked as medium difficulty and involves a quick look at fundamental Linux forensics and persistence techniques. The room name is a nod to the hardy tardigrade. I suppose that a tardigrade wouldn't mind living physically living inside a computer. Like the previous room I completed, this room is still more a guided learning experience than an actual challenge. That's okay, though. I'll work my way to additional blue-team themed challenges that involve less handholding soon enough.

> You'll need to start this room by using `ssh` to remote into the machine we need to investigate.
{: .prompt-info }

# Questions
## Task 1 Q1: What is the server's OS version?
Our Incident Response team has graciously handed over the credentials for one of the users on this system (in plaintext, might I add!). Upon logging in to the server, you'll be confronted with a friendly motd (message of the day) which contains our first answer. A motd can be usually configured to whatever the user might want. In this case, the default Ubuntu motd is being displayed. The server's OS version is displayed at the very top of the motd.

![question1info](/T1Q1.png)

## Task 2 Q1: What's the most interesting file you found in giorgio's home directory?
Let's change directory to Giorgio's home directory, per the question's guidance. You would see nothing if you were to run a simple `ls` command without any other flags. From here, I'll run an `ls -als` to see if there's anything out of the ordinary. You'll see a dotfile that looks slightly of place. I can't recall seeing a dotfile being intentionally named something like that!

![question1info](/badconfigls-al.png)

## Task 2 Q2: Another file that can be found in every user's home directory is the .bashrc file. Can you check if you can find something interesting in giorgio's .bashrc?
This is a case where I like to use vim for navigating a longer text file, like a .bashrc file for instance. Let's take a look, shall we?

![question2info](/T2Q1qmostofbashrc.png)

I don't see anything out of the ordinary here. However, if you scroll further down, you'll see a list of aliases- some normal and one quite abnormal!

![question2info](/T2Q2SusLsAlias.png)

## Task 2 Q3: Did you find anything interesting about scheduled tasks?
The question is here asking us to check out any cronjobs that might be doing anything malicious as an additional form of persistence. I used the `crontab -e` command to check it out. There's only one cronjob listed in here- definitely not a cronjob that I would want running on any machine I own.

![question2info](/T2Q3ScheduledTasks.png)

## Task 3 Q1: What is the flag?
Just treat this section like expository dialogue. There's a nice flag at the end for some extra points.

![question3info](/dirtywordlistfreepoint.png)

I hadn't heard of this piece of terminology before, so I decided to do a quick Google search on it.

![question3info](/dirtywordlistapparentlyanonoword.png)

Apparently this is somehow in the same category as the word "blacklist" as a piece of non-inclusive language. Not going to comment on the logic required to make that kind of distinction in your mind, but I suppose it's interesting to note.

## Task 4 Q1: A few moments after logging on to the root account, you find an error message in your terminal. What does it say?

To begin, we'll need to effectively log in as root. But we don't know the credentials of the root user, you might say. However, we can check to see if the current user giorgio has sudo access by running the `sudo -l` command.

![question4info](/sudo-l.png)

Great! Knowing that we can become root, let's go ahead by using the `sudo su` command.

![question4info](/T4Q1loginasrootsudosu.png)

We're immediately confronted with a piece of text, letting us know that something has happened. I can't really describe it without giving it away fully, but it involves some sort of timeout message and a familiar network connection utility.

## Task 4 Q2: After moving forward with the error message, a suspicious command appears in the terminal as part of the error message.What command was displayed?
Press enter a couple times and you're presented with the suspicious command. A running process just got foregrounded or backgrounded or errored out-  what just happened here?

![question4info](/ncattimeoutbackgroundedprocess.png)

## Task 4 Q3: You might wonder, "how did that happen? I didn't even do anything? I just logged as root, and it happened." Can you find out how the suspicious command has been implemented?
If you were like me, you were probably a bit stumped here. All I did was log in to the system as root. Naturally, I thought about the priority of bash configuration files, naturally. You'll need to change directory to the root directory and locate this bash configuration file there.

## Task 5 Q1: What is the last persistence mechanism?
We've already looked through forms of persistence using cronjobs, bash configuration files, and aliases. What else might there be to explore? The question guides us to look at something or someone, specifically. The traditional way of viewing all users is by looking at the /etc/passwd file, which contains all users on the system. As usual, you see the extensive list of users on this Ubuntu machine. Of course, these aren't actual users with say, a person sitting behind a keyboard. These are primarily users for system and daemon management. You can think of it like a separation of duties- so that certain processes are only being invoked within the context of a particular user. If a service fails or gets compromised, it won't necessarily affect other services or users. I recalled that there was always one particular user in /etc/passwd that always seemed off to me, like a shadowy entity residing on my system. I even recall searching about this particular user a couple of times in the past year. It's normal to have that user, though. I'll have to mention the answer directly here, which is the `nobody` user. I was perplexed and needed to read more about this specific user. I will admit, I would have spent far too long on this question. I got pretty lucky when recalling interesting looking users on a Linux system.

## Task 6 Q1: Finally, as you've already found the final persistence mechanism, there's value in going all the way through to the end. The adversary left a golden nugget of "advise" somewhere. What is the nugget?
I like to start with the obvious here- which is trying to directly log in to a service or user account with default or default-ish credentials. I came back to this room and used `ssh` to log directly into the user account we want to access using the username of the service we suspected of compromise. I lucked out since the login username and password were the exact same. From there, I simply ran an `ls -al` to view any hidden files or dotfiles within the nobody home directory. Considering we've found other flags that way, I figured that was the way to go.

![question6info](/foundlastanswerls.png)

The .youfoundme file looks interesting. Just `cat` out the contents of that file to obtain the flag.

# Conclusion
That was a fun room with a lot of lucky moments on my first runthrough. I especially enjoyed learning about the specific user account used to maintain persistence. I would encourage you to do some additional reading on that specific user here. Take care and see you soon.
