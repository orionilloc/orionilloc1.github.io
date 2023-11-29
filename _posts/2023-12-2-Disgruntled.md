---
layout: post
title: TryHackMe - Disgruntled
date: 2023-11-28 09:10:00 -500
categories: [TryHackMe]
tags: [digital-forensics, blue-team]     # TAG names should always be lowercase
img_path: /assets/img/Disgruntled/
---

# Introduction
Today, we will be exploring TryHackMe's Disgruntled room found at: `https://tryhackme.com/room/disgruntled`. This challenge is marked as easy difficulty and involves a quick look at fundamental Linux forensics. The room is more so a guided learning experience than an actual challenge- but it's nice to do a straight forward room. As I complete an easier room like this, I like to explore different commands and approaches to get to the same answer.


> Throughout this challenge, you'll want to have the TryHackMe-branded Linux Forensics Cheatsheet open in another tab for handy referencing.
{: .prompt-info }

# Questions
## Q1: The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND?
You can use the suggested commands under the "Evidence of Execution" section, namely for checking authentication logs: `cat /var/log/auth.log* | grep -i COMMAND`. This could also be simplified to `grep -i "COMMAND" /var/log/auth.log` like I've done below.
![question1info](/Q1FullCommandLogs.png)

## Q2: What was the present working directory (PWD) when the previous command was run?
Answer can be found from the above process.

## Q3: Which user was created after the package from the previous task was installed?
You can continue combing through the `/var/log/auth.log` file as in the previous question. This time, you will need to recall that the `useradd` command can be used to add or modify users on Linux systems. I used `grep -i "useradd" /var/log/auth.log` to find instances of the applicable command being run.
![question3info](Q3UseraddCommandLogs.png)

## Q4: A user was then later given sudo priveleges. When was the sudoers file updated? (Format: Month Day HH:MM:SS)
Answer can be found from the above process.

## Q5: A script file was opened using the "vi" text editor. What is the name of this file?
You can refer back to our handy cheatsheet here, which suggests reading information from the hidden `.viminfo` file. The only caveat here is that you'll need to view the `.viminfo` file from within one of the home directories or specify the absolute file path. The filename can be clearly seen when inspecting the file.
![question5info](Q5ScriptFileOpened.png)

## Q6: What is the command used that created the file `bomb.sh`?
We'll want to check the bash history on this machine to find additional details regarding file creation. In this case, we can simply view the `.bash_history` using any number of utilities, like `more`, `less`, `cat`, or even a text editor like vim or nano. Just make sure you're reading the `.bash_history` of the correct user here.
![question6info](Q6BashHistory.png)

## Q7: The file was renamed and moved to a different directory. What is the full path of this file now?
Answer can be found from the above process.

## Q8: When was the file from the previous question last modified? (Format: Month Day HH:MM)
You could find this information from typing `ls -als`. You really only need to put the `-a` flag for "all" to see user dotfiles. While looking for additional ways to see information about, I came across the `stat` command. This command shows you file information like time last accessed, modified, as well as additional user and directory information. It even shows file modification times down to the subsecond value, which is pretty cool.
![question8info](Q8FileLastModified.png)

## Q9: What is the name of the file that will get created when the file from the first question executes?
Read the shell script that's been generated. There's even a version history at the top of the script. It's pretty goofy as a script, but you can clearly recognize the fictitous malicious actor's intended result.
![question9info](Q9Script.png)

## Q10: At what time will the malicious file trigger? (Format: HH:MM AM/PM)
By reading the `.bash_history` file from before, you'll know that the script is being executed as a cronjob. Access a list of cronjobs by entering `crontab -e` at the command line.

> Reading the crontab isn't difficult, but it can be good to refresh your skills here at `https://www.hostinger.com/tutorials/cron-job` or by referring to one of LearnLinuxTV's videos here at `https://www.youtube.com/watch?v=7cbP7fzn0D8`.
{: .prompt-tip }

![question10info](Q10Crontab.png)

# Conclusion
We just investigated a fictional, if entertaining logic bomb investigation. I cannot tell if you this is remotely close to a real life incident response, clean-up, or facting-finding session. However, I did have a good time with it. This challenge room still gives a sense of narrative with the brief investigation. Disgruntled former employee plants a logic bomb on this Linux system. Said logic bomb in the form of a script removes a presumably mission critical application or resource. Evidence of execution can be found using Linux commands found on the system. I hope this writeup gave you something new to use in your arsenal. Cheers!
