+++
title = "Turning old McBook into an Ubuntu Server - Part 2"
description = "My very first attempts to DevOps - second part 💁‍♂️"
date = 2025-10-04
[taxonomies]
tags = ["linux", "devops", "tech"]
[extra]
banner = "servers-blue.png"
toc = true
toc_inline = true
+++

## Install Ubuntu Server on your old laptop

Since my only internet connection is an unstable Wi-Fi, I encountered some challenges during the installation process, which I’ll cover later. If you have access to a wired connection, I recommend using it for a more stable experience.

- Insert the bootable USB drive into your old laptop.
- Start/Restart the laptop and enter the BIOS/UEFI settings by holding an option key.
- Set the USB drive as the primary boot device.
- Save and exit BIOS settings. The laptop should boot from the USB drive and start the Ubuntu Server installation process.

### Installer

- When the Ubuntu Server installer appears, select your language and press Enter.
- Follow the prompts to configure your keyboard layout.

I chose English for both the language and keyboard layout. However, if you’re using a Wi-Fi connection like I did and your Wi-Fi network name or password includes characters specific to your language, make sure to select your language accordingly.

If you’re using a wired connection, the setup will likely be straightforward. For Wi-Fi, select your visible network from the configuration screen and enter the password.

There’s an option to skip setting up the internet connection, which would delay downloading updates during the installation. However, since I was using Wi-Fi, the installation attempted to download updates and failed repeatedly. Checking the logs, I found that the errors were related to HTTP, likely due to an unstable connection. The laptop kept losing the Wi-Fi signal during the installation and package downloads. To resolve this, I moved closer to the router (in another room) to ensure a stronger signal, which allowed the installation to complete successfully.

If you encounter similar issues, you can access a terminal prompt by pressing **Ctrl+Alt+F2**. Check the logs in **/var/log** and inspect them using tools like **less** or **vi**, or search for errors with **grep**, for example: **grep -r "error" /var/log/installer/**. Since the logs can be lengthy, consider piping the output to less or another pager to make it easier to navigate.

During the installation, you'll have the option to install OpenSSH. I recommend selecting this option for easier access to your server. However, if you prefer, you can skip this step and install OpenSSH manually later.

If everything looks good, proceed by following the prompts to complete the installation. Add your server name, username for yourself and a password.
