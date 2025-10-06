+++
title = "Turning old McBook into an Ubuntu Server - Part 1"
description = "My very first attempts to DevOps"
date = 2025-10-04
[taxonomies]
tags = ["linux", "devops", "tech"]
[extra]
banner = "servers-blue.png"
toc = true
toc_inline = true
+++

This project is my first step into creating a personal lab where I can start learning DevOps tools and skills, and I’ll walk you through exactly how I got it up and running.

I had an old MacBook Air running elementary OS, which was the perfect machine for this project. Keep in mind that the steps I describe are specific to this MacBook
and may vary on other devices. Also, all commands I use bellow might be specific to my current MackBook which I will be using as a workstation. As a beginner,
there might be more efficient approaches out there, but these steps worked for me. I hope sharing my process helps other DevOps padawans get started with their own home labs.

The laptop I am turning into an Ubuntu Server:

```bash
Architecture:             x86_64
  CPU op-mode(s):         32-bit, 64-bit
  Address sizes:          36 bits physical, 48 bits virtual
  Byte Order:             Little Endian
CPU(s):                   4
  On-line CPU(s) list:    0-3
Vendor ID:                GenuineIntel
  Model name:             Intel(R) Core(TM) i5-2557M CPU @ 1.70GHz
    CPU family:           6
    Model:                42
    Thread(s) per core:   2
    Core(s) per socket:   2
    Socket(s):            1
    Stepping:             7
    CPU(s) scaling MHz:   38%
    CPU max MHz:          2700.0000
    CPU min MHz:          800.0000
```

## Part 1: Prepare the USB Drive

I decided to use a USB drive to boot from. Having an Ubuntu Server installer on a USB is handy. I used a 4GB drive, but to be safe, an 8GB drive might be a better choice.

Start with downloading the Ubuntu Server ISO file. You can find the latest LTS version [here](https://ubuntu.com/download/server).

- Insert a USB drive into your MacBook Air.
- Open the Disk Utility app (Applications > Utilities > Disk Utility).
- Select your USB drive in the sidebar.
- Click on Erase and choose the following options:
  - Format: MS-DOS (FAT)
  - Scheme: GUID Partition Map
- Click Erase to format the USB drive.

### Create a Bootable USB Using Terminal

- Open the Terminal (Applications > Utilities > Terminal) and there, convert the Ubuntu Server ISO to a disk image using following command:
```bash
hdiutil convert -format UDRW -o ~/ubuntu-server.img ~/Downloads/ubuntu-24.04-live-server-amd64.iso
```
You will need to replace the path with the actual one that you downloaded the ISO file into and also double check the name of the downloaded file as you might have a different version.

You should get output similar to this:
```bash
Reading Protective Master Boot Record (MBR : 0)…
Reading GPT Header (Primary GPT Header : 1)…
Reading GPT Partition Data (Primary GPT Table : 2)…
Reading ISO9660 (DOS_FAT : 3)…
.............................................................................................................................................................
Reading Appended2 (C12A7328-F81F-11D2-BA4B-00A0C93EC93B : 4)…
.............................................................................................................................................................
Reading Gap1 (DOS_FAT : 5)…
.............................................................................................................................................................
Reading Ubuntu-Server 24.04 LTS amd64    (Apple_ISO : 6)…
.............................................................................................................................................................
Reading GPT Partition Data (Backup GPT Table : 7)…
.............................................................................................................................................................
Reading GPT Header (Backup GPT Header : 8)…
Elapsed Time:  3.760s
Speed: 698.6MB/s
Savings: 0.0%
created: /Users/honza/ubuntu-server.img.dmg
```

As you can see at the bottom, the image is ready.

- Now, let's find the identifier of our USB drive, which we will need in next step:  
`diskutil list`

You should see your drive listed along with the others, identified by the name you assigned to it earlier. Copy or remember the IDENTIFIER (disk4 in my case):  
```bash
/dev/disk4 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *4.0 GB     disk4
   1:                        EFI EFI                     209.7 MB   disk4s1
   2:       Microsoft Basic Data UBUNTU SERV             3.8 GB     disk4s2
```

- Unmount the USB drive. Don't forget adjusting the identifier to match your own:  
```bash
diskutil unmountDisk /dev/disk4
```


Unmounting ensures that no other processes are accessing the disk. This guarantees that the entire disk space is available for the image writing process.

### Writing the disk image to the USB drive

```bash
sudo dd if=~/ubuntu-server.img.dmg of=/dev/rdisk4 bs=1m
```

If all went well, you should see output like this:

```bash
2627+1 records in
2627+1 records out
2754981888 bytes transferred in 478.478979 secs (5757791 bytes/sec)
```

The **2627+1 records in** and **2627+1 records out** lines show that **dd** processed the expected number of data blocks, and the **2754981888 bytes transferred** confirms that the full size of the image was written.

The dd command is used to write an image file (like an .iso or .img) directly to a storage device, such as a USB drive. This process is crucial for creating a bootable installation media.

An ISO file is a disk image that contains the filesystem and bootable information needed to install an OS. However, certain tools or installations might require converting this to a different format (like .img).

In many cases, you can use an ISO file directly to create bootable media. Tools like **Rufus** (for Windows) or **Etcher** (for cross-platform) can handle ISO files directly and write them to USB drives in a bootable format.

- Anyway, when the process completes, eject the USB drive:
```bash
diskutil eject /dev/diskN
```

Now that the bootable USB drive has been created, you can proceed with the installation of Ubuntu Server on your old laptop.

> [!IMPORTANT]
> See part 2
> [Turning old McBook into an Ubuntu Server - Part 2](/posts/turning-old-macbook-into-an-ubuntu-server-part-2/)

<div class="buttons centered">
  <a class="big colored" href="#top">Go to Top</a>
</div>

