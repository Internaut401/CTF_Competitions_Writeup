Main:

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v3; // ST10_4
  int v5; // [rsp+14h] [rbp-1Ch]
  char s1; // [rsp+1Ah] [rbp-16h]
  char v7; // [rsp+1Bh] [rbp-15h]
  char v8; // [rsp+1Ch] [rbp-14h]
  char v9; // [rsp+1Dh] [rbp-13h]
  char v10; // [rsp+1Eh] [rbp-12h]
  char v11; // [rsp+1Fh] [rbp-11h]
  char v12; // [rsp+20h] [rbp-10h]
  char v13; // [rsp+21h] [rbp-Fh]
  char v14; // [rsp+22h] [rbp-Eh]
  char v15; // [rsp+23h] [rbp-Dh]
  char v16; // [rsp+24h] [rbp-Ch]
  char v17; // [rsp+25h] [rbp-Bh]
  char v18; // [rsp+26h] [rbp-Ah]
  char v19; // [rsp+27h] [rbp-9h]
  unsigned __int64 v20; // [rsp+28h] [rbp-8h]

  v20 = __readfsqword(0x28u);
  s1 = 'p';
  v7 = 'l';
  v8 = 'e';
  v9 = 'a';
  v10 = 's';
  v11 = 'e';
  v12 = '\0';
  v13 = 'p';
  v14 = 'r';
  v15 = 'e';
  v16 = 't';
  v17 = 't';
  v18 = 'y';
  v19 = 0;
  if ( argc == 1 )
  {
    puts("Ask Nicely");
  }
  else if ( !strncmp(&s1, argv[1], 7uLL) )
  {
    puts("Ask Nicer!");
  }
  else if ( argc == 3 )
  {
    v3 = strncmp(&s1, argv[2], 7uLL);
    v5 = strncmp(&v13, argv[1], 7uLL);
    if ( !v3 && !v5 )
      flag();
  }
  return 0;
}
```

Flag function:
```c
int flag()
{
  putchar('D');
  putchar('a');
  putchar('w');
  putchar('g');
  putchar('C');
  putchar('T');
  putchar('F');
  putchar('{');
  putchar('+');
  putchar('h');
  putchar('@');
  putchar('n');
  putchar('K');
  putchar('_');
  putchar('Y');
  putchar('0');
  putchar('U');
  putchar('}');
  return putchar('\n');
}
```
# FLAG
`DawgCTF{+h@nK_Y0U}`
