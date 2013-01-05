--- 
layout: post
title: Discovering a compromised server
created: 1213153200
---
I took a freelance job today helping someone with his compromised server. He wasn't sure what the problem was, but he knew he was getting emails from his ISP about abuse reports naming his IP address.

He gave me some SSH credentials and in I went. The first thing I noticed was a high CPU load, like 3.5. I also noticed about 20 processes, all with the same filename responsible for the system load. They were all owned by a regular user, and were running from a php engine located in /tmp!? At a casual glance, it looked just like a bunch of normal PHP threads, but the /tmp caught my eye. Who keeps their php binary in a hidden folder under /tmp?! Only people wanting to hide as far as I'm concerned.

So I asked my customer some questions about this user. He didn't think the processes were supposed to be run by that user, and that the user should never login. However, the SSH logs showed the user logging in several times a day.

SSH brute force attacks are very common these days. All my linux boxes get hammered all day long. Hackers known as bot herders run large bot nets for SPAM trafficking, launching SSH attacks like this, and who know's what else. So I thought, maybe this is what happens when you get attacked and the attack works? The attack looks for a common username with a weak password. The username was a common name, so the theory fit.

On that whim, I copied the customer's /etc/shadow to my local linux box and ran "John the Ripper" on it. I did a double take it happened so fast, but John the Ripper immediately showed me two user's passwords that it cracked in about 20 seconds. One of which was the suspect account, they other my customer's own account on the machine.

I have to admit, it was sweet as sugar to IM my customer his own password and then tell him how I found it. I'll protect the innocent by changing the names, but the compromised account was something like "bob" with a password of "bob123". Certainly something an SSH brute force attack loves to find.

So I changed the passwords to stronger ones, then killed all the php processes running for the suspect user account. The sever load immediately went to .15 or so, the sign of a healthy server with not much on it's plate. I made some quick checks of cronjobs etc to make sure there wasn't anything scheduled to run that looked suspect.

I then hunted for the PHP script, it was not in /tmp, but in /var/tmp/ in another hidden folder. In there I found the smoking gun. There were several text files of common usernames, common passwords, and IP addresses. Then the PHP script itself. Opening it up I found your garden variety SMTP and SSH scanner.

I'm recommending to my customer's sys admin that he install Denyhosts and use strong passwords for SSH. I would rather see them disable SSH passwords altogether, and use SSH keys, but the customer didn't seem to like that idea much. Denyhosts and strong passwords are a good combination though, and with both in place they will probably not see this problem again.

I noted that whoever was using the compromised account was coming from two US IP addresses. Most of these SSH attacks come from overseas. I looked them up at ARIN and contacted the people responsible for them. I received a prompt response from one of them, and he thanked me and passed my email to his downstream customer. So I might actually score 2 kills in the war against botnets. Makes me feel good!

This job was alot of fun, it's not everyday that I get to play investigator. I hope I can find more work like this. Maybe I can be "Dog the Bounty Hunter" in the cyber world?
