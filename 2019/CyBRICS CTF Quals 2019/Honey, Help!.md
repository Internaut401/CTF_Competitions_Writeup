
![Honey,_Help!_0](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Honey%2C_Help!_0.png)

We can retrieve with knowing symbols in the screenshot the command ```cat flag```. Trying the command ```echo $'\e(0'``` on different Linux distribution and pressing keys to see printed symbols, we find out Kali Linux is the distribution on the screenshot, so basically we can retrieve the output of the ```cat flag``` command by matching the symbols in our shell and those shown by the challenge

![Honey,_Help!_1](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Honey%2C_Help!_1.jpg)

### FLAG 
```cybrics{h0ly_cr4p_1s_this_al13ni$h_0r_w4t?}```
