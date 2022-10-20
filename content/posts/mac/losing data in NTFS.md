---
title: "Losing NTFS data in Mac"
date: "2020-02-12"
description: "How to recover data and what should not do!"
categories:
    - "Mac"
---


# Losing NTFS data in Mac

## What happened

In one day evening, I played with my WD 2TB external drive. All my important data is in it and I did not have a whole backup for this drive because it is the backup for other drive. I installed an App called Mounty this time, because it is free and looks good. However, it is stuck in the computer after some movements for the file, which results in I unplugged it without waiting the response for the finder. The result is when I plugged it again in my Mac. It told me the drive is corrupted and it could not open. I was anxiety because this drive contains all my precious data. 


## What you should not do!

I was frustruated and I searched online, I found they said you need a Windows computer to fix it because NTFS is a windows file system. However, I did not have that at home. However, I had a raspiberry pi, which running linux system on it. I thought I could try to fix it in Linux. 

In the linux, I ran the command `ntfsfix` on my drive. What this tool try to do is to rewrite the MBR record, which result in no operation system could get the correct partition information. This is very very bad!!! If I plugged it into Mac, it told me I need to format the drive and it could not read anything from it.

## What you should do

You should find a Windows Computer ASAP and run `chkdsk /F X:` immediately.

## How to recover data

I got a Windows computer and tried to run `chkdsk`, but there is no drive letter for my drive. Therefore, I could not use this command anymore. Open the `disk management`, I could only see the wrong partition information. So I could only use the software to reover the data. I downloaded the `easyrecovery` and I saw the data is still there so I still want to recover the partition. After trying several times, I rewritten the partition information and I saw I could add drive letter to it in the `disk management` tool. When I tried to do it, IT BEGAN TO FORMAT THE DRIVE!!! When I found it, I stopped it immediately. Now I found the MBR record is totally gone and I could only search 2TB with 40MB/s speed to try to recover the data. 

Use DiskGenius is the final way to recover the data which takes me a lot of time to get them all.

![](https://mksorb.com/wp-content/uploads/2019/01/DiskGenius-Professional-Crack-Preview.png)


Please have a backup in any data and do not use NTFS drive in Mac OS.