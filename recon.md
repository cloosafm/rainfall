# Recon checklist for a file

## Basic metadata and permissions
Identifies potential privilege escalation paths (setuid), and tells you who owns the file.
```sh
ls -l        # Long listing, check perms, ownership, setuid/setgid bits
ls -la       # Include hidden files (sometimes relevant in directories)
lsattr       # Extended file attributes on Linux
stat <file>  # Full metadata (timestamps, inode, etc.)
getfacl <file>  # See extended ACLs if '+' is shown in permissions
```

## Identify File Type
Reveals architecture (32-bit vs 64-bit), dynamically linked or static, debug symbols, stripped or not.
```file <file>```

## Hash & Compare
Lets you check if the binary matches known ones in writeups or malware DBs 
```sh
md5sum <file>
sha256sum <file>
```

## Checksec / Security Features
Tells you if binary has stack canaries, NX, PIE, RELRO, etc. → helps predict exploit strategy.
```checksec --file=<file>```

## Strings Search
Finds hardcoded passwords, hints, function names, error messages, suspicious file paths.
```strings -n 4 <file> | less```

## Symbol Table & Functions
If not stripped, you can see function names and variables directly.
```sh
nm -C <file>       # Symbols (demangled for C++)
objdump -t <file>  # Symbol table
readelf -s <file>  # ELF symbol table
```

## Disassembly / Static Reverse Engineering
See how it works without running it — important if it does something dangerous.
```objdump -d <file> | less```

(or use a GUI tool such as Ghidra or IDA)

## Dynamic Linking Info
Observe behavior — but do it in a VM / controlled env.
```./<file>```

Great for catching password checks, file accesses, and environment checks.
```sh
ltrace ./<file>   # Trace library calls
strace ./<file>   # Trace system calls
```

## Environment Manipulation
Try changing PATH, LD_PRELOAD, LD_LIBRARY_PATH, etc. before running to see if it picks up custom code.

## Fuzzing / Input Testing
Feed it long strings, format specifiers, unusual characters to test for overflows and format string bugs

```python3 -c 'print("A"*200)' | ./<file>```
