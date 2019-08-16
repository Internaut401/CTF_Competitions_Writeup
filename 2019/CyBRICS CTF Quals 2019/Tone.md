
![Tone_0](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Tone_0.png)

The video linked in the challenge description is:

![Tone_1](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Tone_1.png)

The challenge tells us the guy in the video has composed the password on his phone during the streaming session; in fact, we can hear the keyboard sounds. Download the video, extract the audio track and open it in Audacity; amplify to see the track better 

![Tone_2](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Tone_2.png)

We can quickly recover the password by matching the sounds of a similar device in our possession and the sounds stripped from the above audio track

![Tone_3](https://github.com/Apollo3000/CTF_Writeup/blob/master/2019/CyBRICS%20CTF%20Quals%202019/img/Tone_3.png)

### FLAG 
```cybrics{secrettonalflag}```
