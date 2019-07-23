
![Oldman_Reverse_0](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Oldman_Reverse_0.png)

visiting the link, the result is:

![Oldman_Reverse_1](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Oldman_Reverse_1.png)

It's an assembly code, so first we need to discover the architecture and related instruction set. Searching on google the architecture is MACRO-11, so with the [ISA](www.dmv.net/dec/pdf/macro.pdf) we are able to understand what code does and rewrite program in python :

```index = 0
string = "cp33AI9~p78f8h1UcspOtKMQbxSKdq~^0yANxbnN)d}k&6eUNr66UK7Hsk_uFSb5#9b&PjV5_8phe7C#CLc#<QSr0sb6{%NC8G|ra!YJyaG_~RfV3sw_&SW~}((_1>rh0dMzi><i6)wPgxiCzJJVd8CsGkT^p>_KXGxv1cIs1q(QwpnONOU9PtP35JJ5<hlsThB{uCs4knEJxGgzpI&u)1d{4<098KpXrLko{Tn{gY<|EjH_ez{z)j)_3t(|13Y}"
length = len(string)

for i in range(32):
    print(string[index], end="")
    index = index + 33
    index = index % length```

### FLAG
```cybrics{pdp_gpg_crc_dtd_bkb_php}```

