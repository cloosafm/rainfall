# Walkthrough for level0

First, you want to use "level0" both as username and as password. You will then be greeted with the following screen (see screenshot file) :
```
  GCC stack protector support:            Enabled
  Strict user copy checks:                Disabled
  Restrict /dev/mem access:               Enabled
  Restrict /dev/kmem access:              Enabled
  grsecurity / PaX: No GRKERNSEC
  Kernel Heap Hardening: No KERNHEAP
 System-wide ASLR (kernel.randomize_va_space): Off (Setting: 0)
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH      FILE
No RELRO        No canary found   NX enabled    No PIE          No RPATH   No RUNPATH   /home/user/level0/level0
```

Looking at .bashrc, we find that this is the result of the command ```checksec``` performed on the file ```level0```.

to get more info on the file we can try facl :
```~$ getfacl level0
# file: level0
# owner: level1
# group: users
# flags: s--
user::rwx
user:level0:r-x
user:level1:r-x
group::---
mask::r-x
other::---
```

so this file is actually owned by level1, which is the next level we want to achieve


```sh
level0@RainFall:~$ file level0
level0: setuid ELF 32-bit LSB executable, Intel 80386, version 1 (GNU/Linux), statically linked, for GNU/Linux 2.6.24, BuildID[sha1]=0x85cf4024dbe79c7ccf4f30e7c601a356ce04f412, not stripped
```

Using objdump or xxd generates way too much text to comfortably analyze, so let's send the ELF file to Ghidra for disassembly.
We find that the main function looks like this :

```c
undefined4 main(undefined4 param_1,int param_2)

{
  int iVar1;
  char *local_20;
  undefined4 local_1c;
  __uid_t local_18;
  __gid_t local_14;
  
  iVar1 = atoi(*(char **)(param_2 + 4));
  if (iVar1 == 0x1a7) {
    local_20 = strdup("/bin/sh");
    local_1c = 0;
    local_14 = getegid();
    local_18 = geteuid();
    setresgid(local_14,local_14,local_14);
    setresuid(local_18,local_18,local_18);
    execv("/bin/sh",&local_20);
  }
  else {
    fwrite("No !\n",1,5,(FILE *)stderr);
  }
  return 0;
}
```

"0x1a7" is hexadecimal, in decimal it converts to "423". Running the ELF with "423" as arg will start a shell, anything else will just print "No !".
Once we have started the shell, we can try to ```cd``` into the user "level1", where we find a file that was not present in level0. The file is ```.pass```, which contains our flag.

Now we can escalate to the next level : run ```su level1``` and use the flag as password.