# Lomiri Findings
A catalog of images and documentation about Lomiri. These docs were produced on 28-MAR-2025.

# Lomiri Virtual Machine Experimentation

**Project Code:** 00013
**Date:** 28-03-2025

## Project Overview

This project documents my attempts to run the Lomiri desktop environment within a virtual machine. The primary goal was to get the full Lomiri shell working, but the project also involved exploring individual Lomiri applications and the challenges encountered.

## System Setup

* **Host Operating System:** macOS 15.2 (by the time of the creation of this document)
* **Virtualization Software:** UTM 4.6.3
* **Guest Operating System:** ARM Debian 12.10.0
* **Host Hardware:** Apple iMac M1

## Core Issue

The first error i got was: 

std::exception::what(): Failed to acquire DRM master: Operation not permitted

This one really threw me for a loop. From what I can tell, the DRM master is like the key to the graphics card. My understanding is that only one program can hold this key at a time to directly control the display. When I tried to start Lomiri, its underlying graphics system, Mir, needed to grab this "DRM master" permission so it could start drawing things on the screen. But the error, "Operation not permitted," tells me that I wasn't allowed to take control. It's like someone else already had the key locked up, or I just didn't have the right credentials to get it in the first place. This seems to be the fundamental roadblock stopping Lomiri from even getting started with the graphics.

The second error I got was:

std::exception::what(): Failed to find any platforms for current system

This error came right after the first one, and it makes sense. If I couldn't get that "DRM master" permission, it means I couldn't properly talk to the graphics hardware. So, when Mir then tried to figure out how to display things – what kind of graphics setup is available – it came up empty. It "failed to find any platforms" that it could use. It's like asking a builder to start constructing a house, but they can't get the basic foundation materials; they just can't proceed with any of the different building methods they know. Because I couldn't get control of the graphics through the DRM, Mir was left without any way to actually show a graphical interface.

## Successful Lomiri Components ("Gems")

Despite the core issue, some individual Lomiri applications were successfully run within the Openbox window manager:

* **2FA Manager:** Lomiri's two-factor authentication manager.
* **System Settings:** The Lomiri system configuration application (some features may be broken).
* **Morph Browser:** The web browser developed for Lomiri.
* **Ptyxis:** A third-party terminal emulator associated with Lomiri.

Probable Solution

Here is a propable solution for the DRM issue:

The error "Failed to acquire DRM master: Permission denied" usually means an application is trying to control the display (DRM) but doesn't have the necessary permissions, often because another application already holds the master. 
Here's a breakdown of the issue and potential solutions:
Understanding the Problem:
DRM (Direct Rendering Manager):
DRM is a Linux kernel subsystem that allows applications to directly access and control the graphics hardware. 
Master Permission:
Only one application can hold the "master" permission at a time, meaning it has exclusive control over the display. 
Permission Denied:
The error occurs when an application tries to acquire the master permission but another application is already holding it, or the application doesn't have the necessary privileges. 
Possible Causes:
Another Application Holding Master:
Another application, like a compositor (e.g., Wayland or X11), a video player, or even a bootloader, might be holding the DRM master. 
Insufficient Permissions:
The user or application might not have the necessary permissions to acquire the DRM master. 
Kernel or Driver Issues:
In rare cases, the issue might be related to a kernel bug or a problem with the graphics drivers. 
Troubleshooting Steps:
1. Identify the Application Holding Master:
Check /sys/kernel/debug/dri/cardX/clients: This directory contains information about applications that have opened DRM devices. Look for entries that have "drm" in their name and might be holding the master. 
Examine System Logs: Check system logs for messages related to DRM, especially those indicating which application is holding the master. 
2. Release the DRM Master:
Stop the Application: If you can identify the application holding the master, try stopping it gracefully or forcefully. 
Use drmDropMaster: If you know the PID of the application holding the master, you can try dropping the master permission using drmDropMaster (requires root privileges). 
3. Grant Necessary Permissions:
User Permissions: Ensure the user or application has the necessary permissions to access the DRM device (e.g., /dev/dri/cardX). 
Group Permissions: Consider adding the user to a group that has access to the DRM device. 
4. Check for Conflicts:
Compositors: If you're using Wayland or X11, make sure there aren't any conflicts between different compositors or display managers. 
Video Players: Some video players might grab the DRM master, so ensure they are not running in the background. 
5. Update Drivers and Kernel:
Mesa: Ensure your Mesa drivers are up to date. 
Kernel: Consider updating your kernel to the latest stable version. 
6. Reboot:
In some cases, a reboot might resolve the issue.

I may try to use this solution. I will update this page soon.

Images

2FA Manager:



![sfa1](https://github.com/user-attachments/assets/efdd4c13-53e1-4cba-91c4-5641ce3190f3)

Morph Browser:



![morph1](https://github.com/user-attachments/assets/e5c9e4a9-85a5-4b2c-865c-c003097f7c5f)



![morph2](https://github.com/user-attachments/assets/ecd97e6d-438d-4c47-aae5-9aeca7ba3493)


Settings:



![set1](https://github.com/user-attachments/assets/73d473b1-ec17-462a-a8e6-468be2161f89)

Ptyxis:


![pty1](https://github.com/user-attachments/assets/d5ba0ce0-502e-4add-a25a-602a02827826)



Linux is the life. Stay open to more.
#######################################################################################




 
