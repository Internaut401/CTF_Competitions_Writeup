A ".pyc" file is given, so the first step is to decompile it using *uncompyle6*.
The challenge to anything unless you provide the correct flag.
Inside the file there is an interesting string:
<br><br>
babababa = 'you-may-need-this-key-1337' 
<br><br>
Looking in the code where the string is used, we can find a `lababa` function:
```python
def lababa(lebula):
    alalalalalalal = [
     73, 13, 19, 88, 88, 2, 77, 26, 95, 85, 11, 23, 114, 2, 93, 54, 71, 67, 90, 8, 77, 26, 0, 3, 93, 68]
    result = ''
    for belu in range(len(alalalalalalal)):
        if lebula[belu] != chr(alalalalalalal[belu] ^ ord(babababa[belu])):
            return 'bbblalaabalaabbblala'
        b2a = ''
        a2b = [122, 86, 75, 75, 92, 90, 77, 24, 24, 24, 25, 106, 76, 91, 84, 80, 77, 25, 77, 81, 92, 25, 92, 87, 77, 80, 75, 92, 25, 74, 77, 75, 80, 87, 94, 25, 88, 74, 25, 95, 85, 88, 94]
        for bbb in a2b:
            b2a += chr(bbb ^ 57)
        else:
            return b2a
```
<br><br>
Function reverse solver:
```python
a2b = [122, 86, 75, 75, 92, 90, 77, 24, 24, 24, 25, 106, 76, 91, 84, 80, 77, 25, 77, 81, 92, 25, 92, 87, 77, 80, 75, 92, 25, 74, 77, 75, 80, 87, 94, 25, 88, 74, 25, 95, 85, 88, 94]
b2a = '' 
for bbb in a2b: 
    b2a += chr(bbb ^ 57) 
print(b2a)

res=''
babababa = 'you-may-need-this-key-1337' 
alalalalalalal = [ 73, 13, 19, 88, 88, 2, 77, 26, 95, 85, 11, 23, 114, 2, 93, 54, 71, 67, 90, 8, 77, 26, 0, 3, 93, 68]
for belu in range(len(alalalalalalal)):
    res+=(chr(alalalalalalal[belu] ^ ord(babababa[belu])))
print(res)
```
# FLAG
`darkCON{0bfu5c4710ns_v5_4n1m4710ns}`
