# EquinorCTF 2021

Written by [Balberg Flagg & Service AS](https://ctftime.org/team/152116)
<br>
<br>

## Crypto/Really Solid Algebra
### Task
*24 solves / 390 points <br>*
Using all the latest math and crypto libraries, this new Really Solid Algebra system should be practically uncrackable! <br>
**Author**: null <br>
**Downloads**: [rsa.py]('Really Solid Algebra'/rsa.py)  [output.log]('Really Solid Algebra'/output.log) <br>
**writeup by**: Heitmann

### Writeup
Dette er åpenbart en RSA oppgave. Som kjent beror sikkerheten i RSA-kryptering på faktumet at det er veldig vanskelig å faktorisere produkter av store primtall. I RSA er det primtallene p og q, hvor `N = p*q`, som er nøkkelen. Har du enten p eller q er den andre lett å finne, og sikkerheten i krypteringen er brutt. Derfor er vårt mål i oppgaven her å finne p og q.

output.log inneholder både N og cipherteksten C:
> 131701686690366736...8768027 (617 siffer)<br>
b'$\x1f\xcd\x00=\xd8...b8.\xcep' (256 tegn)

rsa.py inneholder koden brukt til å genere dette.
```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP
from sympy import randprime, nextprime, invert

p = randprime(2**1023, 2**1024)
q = nextprime(p)
n = p * q
e = 65537
phi = (p-1)*(q-1)
d = int(invert(e, phi))
key = RSA.construct((n, e, d, p, q))
rsa = PKCS1_OAEP.new(key)
print(n)
print(rsa.encrypt(open('./flag.txt', 'rb').read()))

```
Her ser man at de velger et primtall på 1024-bit som p og det påfølgende primtallet som q.
```python
...
p = randprime(2**1023, 2**1024)
q = nextprime(p)
...
```

Det er ikke en trygg implementasjon av RSA ettersom man kan lett finne området hvor p og q ligger ved å ta kvadratrota av N.

Under er kode som først finner kvadratrota og kjører floor() på det for å gjøre om desimaltallet til et heltall. Deretter legger det på 1 og sjekker om N er delbart på det nye tallet helt til man finner noe som er det. Nå p er funnet er q lett å finne.

```python
sage: N = 13170168669036673658789415835821860...028768027
sage: p = floor(sqrt(N))
sage: while N%p!=0:
....:     p += 1
....:
sage: q = N/p
sage: p*q == N
True
sage: p
114761355294527058224622861107606630879034830600188669071381672438274915604836363270006073038488687244873202683844990751830702476759188657682705934187203554885828875215187956457004941023073622353514828385218517280681450849554176816689299473448025493234673090587933642765556764040289982864680558211516240542223
sage: q
114761355294527058224622861107606630879034830600188669071381672438274915604836363270006073038488687244873202683844990751830702476759188657682705934187203554885828875215187956457004941023073622353514828385218517280681450849554176816689299473448025493234673090587933642765556764040289982864680558211516240541749
```
Når man har p og q er man i teorien ferdig med oppgaven, men vi har enda ikke flagget. For å finne flagget brukes et nydelig verktøy kalt [RsaCtfTool](https://github.com/Ganapati/RsaCtfTool). For å bruke RsaCtfTool sin --uncipher trenger man et tall, eller en long, ikke en byte-streng som vi nå har. bruker derfor bytes_to_long for å gjøre om bytes-strengen i output.log til et tall.
```python
>>> from Crypto.Util.number import bytes_to_long
>>> bytes_to_long(b'$\x1f\xcd\x00=\xd8\xe7 ... \xcb\xb8.\xcep')
4560260530289670591071966546972057311292380653648738741985003352667396602092119923644295820997984485350436545094982530306631626653020934971105609138932523607578080374114272299064003885258824807361561484182636348509271814547801109106128521695532535076535613450243548228267142788364716468753394492419182879108193844294567914834080489865548510606652268802248326453481178461940357813190754865701003762550374929851113975612636421386206878118265861881815454815619305063746598980420073940719566911899562835599143763961693078991673152175896664350447284788248793863339782559559121834030137480742552404592581633973878422163056
```
RsaCtfTool kjøres slikt med `e=0x10001`som funnet i rsa.py.
```python
RsaCtfTool -p 114761355294...0542223 -q 114761355...541749 -e 0x10001 --uncipher 45602605302896705...163056

...
STR : b'EPT{5qrt_b3_sc4ry_owo}'
...
```
FLAG: **EPT{5qrt_b3_sc4ry_owo}**

## Crypto/Arbitrary Encoding System
### Task
*20 solves / 413 points <br>*
I heard that all the cool kids down the street had switched to this new cipher. Think it was called Arbitrary Encoding System or something... <br>
**Author**: null <br>
**Downloads**: [aes.py]('Arbitrary Encoding System'/aes.py)  [flag.png.enc]('Arbitrary Encoding System'/flag.png.enc) <br>
**writeup by**: Heitmann

### Writeup
Dette er en AES oppgave som inneholder to filer: **AES.py** og **flag.png.enc**. AES.py ble brukt til å kryptere flag.png som resulterte i flag.png.enc. AES en av de ledende blokkryptoalgoritmene som brukes i dag. Det at noe er blokkrypto betyr at algoritmen deler opp klarteksten som skal krypteres i blokker og krypterer hver av disse blokkene. Det vil i praksis si at endrer man et tegn i klarteksen vil hele blokken endres til noe annet. Typisk blokkstørrele er 16 byte. AES er en solid kryptoalgoritme, men slik som ved all krypto finnes det usikre implementasjoner. En av disse skal vi utnytte i oppgaven her.

For å begynne med oppgaven kan vi se på innholdet av **AES.py**:
```python
from PIL import Image
from Crypto.Cipher import AES

img = Image.open('flag.png')
assert img.mode == 'RGB'
assert img.height == 250
assert img.width == 2000
assert str([b for rgb in [[1, 2, 3], [4, 5, 6], [7, 8, 9]] for b in rgb]) == '[1, 2, 3, 4, 5, 6, 7, 8, 9]'
data = bytes([b for rgb in img.getdata() for b in rgb])  # flatten
assert len(data) % 16 == 0
key = open('/dev/urandom', 'rb').read(16)
aes = AES.new(key, AES.MODE_ECB)
ct = aes.encrypt(data)
open('flag.png.enc', 'wb').write(ct)
```
flag.png.enc kan ikke åpnes som et bilde og inneholder masse kryptert data uten smart innhold.

Verdt å merke seg fra aes.py er at *aes*-objektet som lages med `aes = AES.new(key, AES.MODE_ECB)` er i *MODE_ECB*. ECB, electronic code book, er en AES metode som er  vankelig å implemetere trygt og korrekt om man ikke vet hva man gjør. Selv vet jeg det ikke, men jeg har i alle fall lært meg hva dens sikkerhetsrisikoer er. [Denne blogposten](https://tripoloski1337.github.io/crypto/2020/07/12/attacking-aes-ecb.html) viser blant annet hvordan man kan "lure informasjon" ut av ECB-blokker, men det er ikke teknikken vi skal benytte oss av. Det som er spesielt med ECB-modusen i forhold til andre AES-moduser er at alle blokker med lik klartekst krypteres til like blokker med siffertekst.

Dette vises under med en 48 tegn lang klartekst - nøyaktig 3 blokker, en per linje. Sifferteksten er delt opp i blokker og fordelt med en blokk per linje.
```
Klartekst:
Jeg er Heitmann
Jeg er Heitmann
Jeg er Heitmann

Kryptert med AES i CBC-mode:
b'\xe3\xb9\xb4\xb6\xadN\xe9\x1b\n\xe2\x94\xa5\xc2;\x04*'
b'\xd6f\x12\xf0\xfcg\xc3\xe5b\xd5\xf9\x03f\x90\xc0e'
b'ru\xdf\xf9&g\xaf\x84\xfd\xed\x83\xdd\x1b\x8f,\x19'

kryptert med AES i ECB-mode:
b'\x85\xfe\x03g\n\x02R\x1b4\x11\r\x04\xf5Z\x1fR'
b'\x85\xfe\x03g\n\x02R\x1b4\x11\r\x04\xf5Z\x1fR'
b'\x85\xfe\x03g\n\x02R\x1b4\x11\r\x04\xf5Z\x1fR'
```
Når den ble kryptert i CBC-mode var de tre blokkene ulike og hadde gitt en angriper lite informasjon. Derimot var alle tre krypterte blokker i ECB-mode identiske og forteller en angriper at klarteksen består av identiske blokker det også.

Ettersom flagget vårt er et kryptert bilde kan dette utnyttes. Som vi vil se inneholder bildet store områder med lik farge, som også vil si lik klartekst. Alle disse blokkene vil da krypteres til det samme og vil kunne skape klare skiller mot andre enkeltfarga områder. Hadde det vært et mer komplekst bilde ville ikke dette lengre være et problem.

For å hente ut informasjonen beskrevet over og lese av flagget må man kunne vise bildet. Som vi har sett er hele bildefila med header kryptert og vil derfor ikke kjøre i noe program. Vi må derfor fremstille dette selv. Det er mulig å gjøre det selv med python.
```python
from PIL import Image

f = open('flag.png.enc', 'rb')
img = Image.frombytes('RGB', (2000, 250), f.read(), decoder_name='raw')
img.show("flag.png")
```
Men enda lettere ved hjelp av generate image i [cyberchef](https://gchq.github.io/CyberChef/#recipe=Generate_Image('RGB',2,2000)).
![image of cyberchef](/'Arbitrary Encoding System'/cyberchef.png)

![image of flag](/'Arbitrary Encoding System'/flag.png)
Det er mulig å lese av flagget fra bildet, og vi har løst oppgaven!

FLAG: **EPT{mode_of_operation_is_important}**
