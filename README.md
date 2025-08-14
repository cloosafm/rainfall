# Rainfall

## Description
This project is part of 42 security branch, from a pentesting/CTF point of view.
We are provided with a specific iso to work on, where we will work our way from level 0 to 9 for the mandatory part of the project, then we may chose to also work on the optional 4 levels.
We are to provide a walkthrough for each level, explaining how we manage to find the flag. We also provide all scripts that we used, and the flag on a separate file.




## Move files between host and VM
First, if IP address is 10.x.x.x, make sure that the VM network adapter settings is set to 'host-only'.

To copy files FROM the VM, use 
```scp -P <port> <user>@<host>:/path/in/vm /path/in/host```

To copy files INTO the VM, use:
```scp -P <port> <file_name> <user>@<host>:/path/in/vm```
In this case, writing rights are restricted so we will send files to /tmp in the VM.


## How to use Ghidra
https://medium.com/@zaid960928/ghidra-tutorial-introduction-a3889e138434


gdb fct:
info functions
disas run
disas main
