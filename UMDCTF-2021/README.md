# Writeup for UMDCTF 2021
Written by [Balberg Flagg & Service AS](https://ctftime.org/team/152116) <br><br>

## IOT project
### Task
> IoT is so cool and secure! Check out my project from April 7 at [https://azeemsm-umdctf.github.io/]

The website was full of buttons that didnt do anything, and there was not very much we got from the site.

### Writeup
./robots.txt gave us some info

>I really like robots! Maybe my next project will be a robot... I'll jot some ideas here
BAYMAX!
Ironman suit: https://www.tiktok.com/@bradensmith02 (so cool!)
wow i just rly like robots from movies huh, no original ideas :'(
oh wait that was super easy to setup my second project... value1=normal text, value2=hashtag

Here we noticed the last values
> value1=normal text, value2=hashtag
At this point we didnt know what do with the info, but we wrote it down.

We then found the github files for the site at [https://github.com/azeemsm-umdctf/azeemsm-umdctf.github.io]
From here we opened the commit history and found the program.py script together with the master.key file [https://github.com/azeemsm-umdctf/azeemsm-umdctf.github.io/commit/066a11bbbcc442ea21d865d31c7e89d87d17b407].

The two final thing to do was to change the value of f on line 5 to the string from master.key and change the value1 and value2 that we got a hint for in robots.txt.
We changed value1 to our private email adress, and value2 to '#Azeem'.

When we ran the script we got an email containing the flag.

```python
#Azeem's code
import serial
import time
import requests

#Read secret key from file for security!
f="iWEKorMwH5rgGjyHb4jzedP9m9LA7yNkTZ0dvfzDSUO"
secret_key=f
my_data = { "value1" : "private@email.com", "value2" : "#Azeem" }
#Connect to arduino through serial (mine is on /dev/ttyUSB0 but this may change depending on which port I plug into)

r.requests.post("https://maker.ifttt.com/trigger/flag/with/key/")+secret_key, data=my_data)
print(r.content)
```

Flag : <br>
```
UMDCTF-{g!t_h00k3d}
```

---

## Art Class
### Task
In the art class challange we got a png file that contained this picture:
![Art Class](https://raw.githubusercontent.com/sonjoh/balberg-flagg-og-service/main/UMDCTF-2021/art-class/Art_Class.png)

### Writeup
We google'd boat flags and quickly found out the flags sere letters.
We translated the flags to text and got the flag.
> After further reading after the event we found out that
> the dcode site has a tool for this cipher.
Flag: <br>
```
UMDCTF-{F1AG_0F_7LA9S}
```

---

## Bomb1 Quiz Time
### Task
Hurry the Bomb is about to go off. Get through these blocks to get to
the flag that will stop the bomb!

### Writeup
1. Unzip the .jar file
2. Open bomb1.class
3. Extract flag in plaintext
<br>
Flag: <br>
```
UMDCTF-{c00l_math_f0r_c0llege_kidZ}
```


---

## Card Galore
### Writeup
After a google search for playing card cipher we found this site: [https://www.codewars.com/kata/59c2ff946bddd2a2fd00009e]

We tried to decipher the picture with this but didnt get anything that made sense.
Then the tought that the last word has to be "cards" hit us.
This made us realise that the cipher linked to above was shifted by one. 
After deciphering it again with the same cipher but shifted by one we got these words
1. thanks
2. for
3. sorting
4. my
5. cards
We wrapped this into UMDCTF-{} and got the flag.

Flag:
```
UMDCTF-{thanks_for_sorting_my_cards}
```

---

# Card Obsession?
### Writeup
Used DTMF-detector to detect numbers from phone numpad
Ex: 2 - 2 - 2 = "c"
https://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/1200px-Telephone-keypad2.svg.png

This got us the string "umdctfidontlikecards" as we split into words and wrapped into the correct format.

Flag:
```
UMDCTF-{i_dont_like_cards}
```

---

# Celebration
### Writeup
We quicly found out there was a cipher called dancing stickman after a google search.
In the same search we found the site [https://www.dcode.fr/dancing-men-cipher].
After finding the site, we plotted in the stickmans and got the flag.

Flag:
```
UMDCTF-{yo_its_a_partyyy}
```

---

# Magic
### Writeup
1. Searched for steganography magic eye on google
2. Upload the image to https://magiceye.ecksdee.co.uk/
3. Wrap UMDCTF-{ } around the text that appears on the image

Flag:
```
UMDCTF-{th15_15_b14ck_m4g1k}
```

---

# Not slick
### Writeup
After opening the file in hexedit we found out the bytes in the file was reversed.
At the bottom of the file we saw (in ascii) GNP., this is .PNG backwards as is the "magic number" or start ov a png file.

We reversed the file back to normal with this python script
```python
with open("notslick.png", "rb") as fd:
  data = fd.read()[::-1]
  with open(outslick.png, "wb") as fd2:
    fd2.write(data)
```

After opening outslick.png the flag was drawed in plaintext at the picture.

Flag:
```
UMDCTF-{abs01ute1y-r3v3r53d}
```

---

# Starbucks
### Writeup
We decompiled the original .class file with an online decompiler.
We then edited the file as shown below
```java
public static void main(String[] args) {
  System.out.println(f3());
}
```

Then we compiled the file again with
> $javaac Challange.java
The filename must be Challange.java!

We ran the new file with
> $java Challange
This gave us the flag in plaintext.

Flag:
```
UMDCTF-{pyth0n_1s_b3tt3r}
```

We agree with the flag..

---

# Testudo's pizza
### Task
My local pizzeria is trying out a new logo that is bringing in a lot of new customers. I think something fishy is going on. What are they doing?
<br>
*Download link for .jpg file*

### Writeup
1. Edit in notepad
2. Find the flag at the bottom of the file

Flag:
```
UMDCTF-{W3_ar3_th3_b3st_P1ZZ3r1a}
```

---

# The Matrix
### Writeup

When we tried to enter ./the-matrix it stood that it was only accessible to robots.
This made us think about the UserAgent in HTTP, so we made this python script:

```python
from urllib.request import urlopen, Request
request = Request('http://chals5.umdctf.io:4000/the-matrix')
request.add_header('User-Agent', 'robots')
print(urlopen(request).read().decode('utf-8'))
```

When we ran this we got another HTTP site back where the flag stood in plaintext at the bottom of the site.

Flag:
```
UMDCTF-{r0b0t_r3b3ll!0n}
```

---

# Traveling
### Task
I've been walking along the streets of this cool city and saw this neat looking building, but can't quite remember its name. Can you help me out?
<br> 
Flag format: UMDCTF-{This is the Flag Format}
<br>
https://drive.google.com/drive/folders/1D9dBgba101Tp7R4hlBG4-CmGqmafoDtq?usp=sharing

The drive folder contained this picture:
![Building](https://raw.githubusercontent.com/sonjoh/balberg-flagg-og-service/main/UMDCTF-2021/traveling/Building.jpg)

### Writeup
After zooming in on the signs we saw Pine with a smaller sign above with the number 500.
We went to Pine 500 in google street view and looked around until we found the building.
We then placed a marker on the building in google maps and found the building name.
After wrapping the name in UMDCTF-{} we got the entire flag.

Flag:
```
UMDCTF-{Bank of America Center}
```

---

# Vacation
### Solution
On the picture we saw Rum bar on the sign over the bar.
We searched it up while hovering the caribbean in google maps and found this [Google maps](https://www.google.no/maps/place/Rum+Therapy+Bar+%26+Treatment+Centre/@14.0148542,-60.9960513,21z/data=!4m12!1m6!3m5!1s0x8c4067abf37b7959:0x5cccd5a5c9764d48!2sRum+Therapy+Bar+%26+Treatment+Centre!8m2!3d14.0148012!4d-60.9960526!3m4!1s0x8c4067abf37b7959:0x5cccd5a5c9764d48!8m2!3d14.0148012!4d-60.9960526)

Scince the challage asked for a brewing company, we searched for "brewing company" while google maps was zoomed in on the bar. There was a brewing company called Antilla Brewing company right across the street from the bar.
We wrapped UTMCTF-{} around Antilla and Castries as this was the town the bar lies in and got the flag.

Flag:
```
UMDCTF-{Castries_Antilla}
```
