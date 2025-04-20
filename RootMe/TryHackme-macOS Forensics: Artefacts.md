

![image](https://github.com/user-attachments/assets/bbf1b268-f238-4838-a2bc-7ea707780421)


This is where you can find the room: https://tryhackme.com/room/macosforensicsartefacts

 It was in the macOS Forensics: The Basics room that we learned the basics of macOS forensics and some of the problems that we might run into when we do forensics on macOS.  Now that we know this, we need to find out what forensic artifacts are in macOS and how we can use them to our advantage.


 ### Task 1 Introduction ###

What command can be used to mount the image named mac-disk.img to the directory ~/mac in the attached VM, making sure the Data volume is mounted?

```apfs-fuse -v 4 mac-disk.img ~/mac```


### Task 2 Before We Begin ###

In the attached VM, which utility can we use to parse plist files?

```plistutil```


### Task 3 System Information ###

When was the OS installed on the disk image present in the attached VM? Write your answer as GMT in the format: YYYY-MM-DD hh:mm:ss

```2024-12-08 17:42:28```

What is the country code for this machine?

```AE```

When was the last time this machine booted up? Write your answer as GMT in the format: YYYY-MM-DD hh:mm:ss

```2025-01-19 15:47:05```


### Task 4 Network Information ###

What is the name of the machine’s built-in network interface?

```en0```

What is the IP address of the router this machine was last connected to?

```192.168.64.1```


### Task 5 Account Activity ###

What is the name of the last logged in user?

```thm```

What is the password hint for the user?

```count to 5```

When was the last time a user logged out of the machine? Format MMM DD hh:mm:ss

```Jan 19 07:52:43```


### Task 6 Evidence of Execution ###

What was the last command executed by the user on the machine?

```vim creds.txt```

What is the GUID of the terminal session in which this command was executed?

```452AEA93-AEE7-420B-871E-C57053E15DD0```

When was the last time the user closed the terminal? Format YYYY-MM-DD hh:mm:ss

```2025-01-19 15:52:33```

For how many seconds was the terminal in focus during this session?

```176```


### Task 7 File System Activity ###

What are the viewing options for the Users/thm folder?

```Open in list view```

What is the last directory visited by the user using the Finder application?

```Recents```


### Task 8 Connected Devices ###

Which stream in the knowledgeC database contains information about connected Bluetooth devices?

```Bluetooth/isConnected```


Task 9 Conclusion

Yoohoo! I loved exploring macOS Forensics!

```No answer needed```

![image](https://github.com/user-attachments/assets/51f1a58b-e4c4-4ce1-aafe-368e9b616c00)


