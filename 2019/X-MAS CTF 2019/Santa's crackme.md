![Santa's crackme intro](/src/Santa's crackme intro.png)

We have to reverse the binary. Decompiled code:
![Santa's crackme decompiled](/src/Santa's crackme decompiled.png)

The program basically XOR our input with number 3 and then compare the result with 'flag_matrix' wich is:
![Santa's crackme string](/src/Santa's crackme string.png)

The XOR has the commutative property:

input ^ 3 = flag_matrix

flag_matrix ^ 3 = input

Let's write a program wich XOR the flag with 3:
```c
#include <stdio.h>
#include <stdlib.h>
#include <strings.h>
#include <string.h>

void main(){
	char v7[104] = "[.NBPx67m47\\26\\a7g\\74\\o2`0m60\\`k0`h2m5~" ;
	int i;
	char s1;

	for (i = 0; v7[i]; ++i){
		s1 = v7[i]^3;
		printf("%c", s1);
	}
	return;
}
```

# FLAG
X-MAS{54n74_15_b4d_47_l1c3n53_ch3ck1n6}
