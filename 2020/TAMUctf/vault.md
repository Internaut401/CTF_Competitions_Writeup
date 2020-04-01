Main function calls deobfuscate:

```gdb
0x000000000000131d <+84>:	call   0x1185 <deobfuscate>
```

So, stepping with gdb, we can see the deobfuscated flag in the stack, pointed by some registers:

```
RAX  0x5555555592a0 ◂— 'gigem{p455w0rd_1n_m3m0ry1}'
```

# FLAG
```
gigem{p455w0rd_1n_m3m0ry1}
```
