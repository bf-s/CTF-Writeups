# EquinorCTF 2021

Written by [Balberg Flagg & Service AS](https://ctftime.org/team/152116)
<br>
<br>

## Crypto/Really Solid Algebra
### Task
*24 solves / 390 points <br>*
Using all the latest math and crypto libraries, this new Really Solid Algebra system should be practically uncrackable! <br>
**Author**: null <br>
**Downloads**: [rsa.py](/rsa/rsa.py)  [output.log](/rsa/output.log) <br>
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
