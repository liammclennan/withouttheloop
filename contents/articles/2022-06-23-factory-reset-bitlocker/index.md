---
title: Factory Reset a Windows Computer with Bitlocker
author: Liam McLennan
date: 2022-06-23 20:32
template: article.jade
---

In short, it's a nightmare. The best strategy is to boot Windows, disable bitlocker and then proceed with the factory reset. But I didn't do that, and I didn't have the bitlocker key, so completing a factory reset became difficult. 

The computer I tried to restore is a 2018 Dell XPS 13.

![XPS 13](xps13)

It comes with a tool called 'Dell SupportAssist OS Restore' which can factory reset the computer using an image stored on a disk partition. For reasons unknown, this tool does not work with a Bitlocker encrypted OS partition. 

The approach that (eventually) worked for me was:

1. Use another windows computer to create a Windows recovery media USB drive.
1. Edit the BIOS to boot from the USB drive (I think only one of my USB ports detected the drive, and possible only if connected without a USB hub).
1. Use the advanced setup option, delete the Bitlockered partition, create a new partition and choose it as the OS partition (you can't install over the Bitlocker partition). 

This is why I hate computers. 
