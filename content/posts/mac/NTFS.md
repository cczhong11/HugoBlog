---
title: "How to mount NTFS in Mac"
date: "2018-05-05"
description: "This blog tells you how to mount NTFS driver with write privilege in Mac"
categories:
    - "Mac"
    - "NTFS"
    - "Tips"
---

# Install FUSE

FUSE for macOS allows you to extend macOS's native file handling capabilities via third-party file systems. You could install it [here](https://osxfuse.github.io/).

# Install ntfs-3g

Use 
```
brew install ntfs-3g
```
If you run into error, `Error: Could not symlink sbin/mkntfs`. You need to use the absolute path to run the following function.

Next, we need to umount our driver, since it only has read permission. And create an endpoint for it. Then we could use ntfs-3g to mount the driver. 

```
sudo mkdir /Volumes/NTFS
# find your disk name
diskutil list

sudo umount /dev/disk2s1

sudo  /usr/local/Cellar/ntfs-3g/2017.3.23/bin/ntfs-3g /dev/disk3s1 /Volumes/NTFS -olocal -oallow_other
```

Now our Driver has the write permission.