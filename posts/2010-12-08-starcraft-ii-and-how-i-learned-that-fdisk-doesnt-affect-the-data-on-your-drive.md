--- 
layout: post
title: Starcraft II and how I learned that fdisk doesn't affect the data on your drive
created: 1291870587
---
So I bought StarCraft II to join in the fun with my geek friends who are all playing it. However, it just so happens that I'm in bad position in terms of hardware. My main Windows rig is a company issued laptop with tight controls on the antivirus and other settings, and getting SC2 to run on it is proving impossible.  My main desktop rig is Linux, with an XP VM that runs in VirtualBox. Well, VirtualBox has direct to hardware 3d acceleration support. I gave it a whirl, but alas, my GeForce 8400 looks like a GeForce 6600 to XP within the VBox VM. SC2 doesn't like that.

So my next idea was to boot my XP VM directly. It's on it's own partition, so why not? So I edited my grub.conf and tried it.  No dice, grub doesn't find the partition. I played with some settings, including hiding partitions with grub, then suddenly grub starts throwing Error 17. I can't even boot my Linux install now.

After booting a linux USB thumb drive I have, and poking around, I find that the Partition System ID's are all changed to Amoeba.  WTH!?

So I fix them, luckily I follow the same pattern on all my linux installs.  Linux boots, but /usr and /home are missing, ah crap, those are on LVM.  LVM was using some extended partitions. Back into fdisk I go and I see that my extended partitions are gone! BP goes up.  This is my main dev rig for work.

After more poking around, I discover that I can see the dimensions of my extended partitions when I fdisk /dev/sda4, which was the originally the extended partition. I notice that it's ID is Linux, and I think, oh that should be Linux Extended. I change it, and fdisk says nope, can't do that, delete it first and create a new one. BP goes up again.  I'm thinking I've lost my /home and /usr forever.

So I do some reading, after googling for "recovering extended partitions". After reading a few blog posts, I realize that fdisk just alters the partition table, not the data on the drive, so you can, theoretically, delete partitions. Then go back and create them, exactly as they were before, and it will be like nothing ever happened.  Really? So I write down the dimensions from fdisk /dev/sda4. Then I fdisk /dev/sda and very nervously delete partition 4, then recreate it as an extended. Then I create my extended partitions within 4. I hit 'w' to write the table, take a deep breath, and reboot.

Linux stops and does an fsck on vg1 because it's last mount date is in the future. NICE! Who cares about that, it knows vg1 exists! Which was my main LVM volume group.

After fsck runs, I'm back in action. My XP VM was an extended partition too, so I fire up VirtualBox and boot XP, and there it was, just like nothing ever happened.

So, the moral of this story? Even when it looks like you just bought the farm, try all your options, it may not be as dire as it seems! 
