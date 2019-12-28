The challenge gives us a program and we need to crack it.
The code isn't obfuscated and could be reversed without problem, so let's start decompile it with IDA PRO.

NOTE: Functions and variables have been renamed by me.

main:

//main screen

The main takes a username and a password. Then it checks if the username is: *`1_4m_th3_wh1t3r0s3`*.
After that, it call *`check_credentials`*, and if the function return 1, it print the flag (which is the password).

check_credentials:

//check_credentials screen

The function do some check about the leght of username and password. 
Knowing the username, we discover that the password is 36 characters long.
Then the function call *`check_password_0`*

//check_password_0 screen

The function check the password 2 caracters at time with a cycle.
First check if the characters, even and odd, are between a set of characters and get the position with function *`check_password_characters`*

//check_password_characters screen

//set of characters even and odd

then it call *`binary_number_generator`*:

//screen binary_number_generator

