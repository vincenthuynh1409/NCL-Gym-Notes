# Cryptography

## Decryption

### 🛠️ Tools

- https://gchq.github.io/CyberChef/ (CyberChef)
- https://www.dcode.fr/cipher-identifier (dCode Cipher Identifier)

### Number Bases (Easy) Write-Up

1. **0x73636f7270696f6e**

> **Hexadecimal** → b/c the leading `0x` is the standard computer science prefix explicitly used to signal that the characters following it are in **hexadecimal (Base 16)** format!

2. **c2NyaWJibGU=**

> **Base64** → b/c It only uses uppercase letters (`A–Z`), lowercase letters (`a–z`), numbers (`0–9`), and sometimes the plus sign (`+`) or slash (`/`). It often ends with one or two equal signs (`=`) used as padding to reach that multiple of four!

3. **01110011 01100101 01100011 01110101 01110010 01100101 01101100 01111001**

> **Binary** → b/c it is Base 2 system, meaning its alphabet consists of absolutely nothing but **zeros and ones**. There are no other numbers, letters, or punctuation marks!

4. **01100010 01000111 00111001 01110011 01100010 01000111 01101100 01110111 01100010 00110011 01000001 00111101**

> Convert from **Binary** > then turns into **Base64** > Decrypt!

### Shift (Easy) Write-Up

1. **iveghny ynxr**

> Use dCode to identify cipher > **ROT13**

### @Bash (Easy) Write-Up

1. **hzuvob lyerlfh xzev**

> Use dCode to identify cipher > **AtBash Cipher**


### Beep (Easy) Write-Up

1.  **.... . / ... . -.-. .-. . - / --- ..-. / --. . - - .. -. --. / .- .... . .- -.. / .. ... / --. . - - .. -. --. / ... - .- .-. - . -.. / ... -.- -.-- / -.. -.- ...- -... / ----. ---.. .---- -....**

> **Morse Code** → b/c it  relies entirely on two characters: dots (`.`) representing short signals, and dashes (`-` or `_`) representing long signals.


## French (Medium) Write-Up

1. `Y ln xkv lubj swlzqvkht, A vmzb pjk bbua we ddgs ILQ-GQYU-8026` 
	- Key = `qizkwcgqbs`

> **Vigenère Cipher** → likely because It gives an alphabetic key: `QIZKWCGQBS`, Vigenère uses a repeating key, Spaces/punctuation remain unchanged. 


## Fencing (Medium) Write-Up

Indicated the keys = "3" and "5".

1. `Cair eruSA-0org sgaeudrpesr K-II98.ue cn seYQ3`
	- use key = `3`

2. `F daS-eefn n KZ3eheadty.YI8lta oiwy-Q0. r aI2`
	- use key = `5`

> **Rail Fence cipher** → Rail Fence uses a number of rails as its key, commonly 3, 4, 5, etc.; The ciphertext looks like letters have been rearranged rather than substituted; Spaces/punctuation are still present in unusual positions, which can happen with a transposition cipher.


## XOR (Medium) Write-Up

1. `2*,K(,81Y>+5.$+/%E.#$-K<6>E1*3<K)<*77.!Y8. F$7,)T[VM^`**
	- Key = `01101011 01100101 01111001`

> **XOR** → - The binary (`01101011 01100101 01111001`) converts to "key"`; The ciphertext contains **lots of symbols** (`* , > + $ % # <`), which is common when binary/ASCII data is XOR-encrypted.
> 
> "XOR Decode" > type in `01101011 01100101 01111001` in "Key" > change key type to binary **OR** convert binary to latin > change key type to LATIN1


## Strings (Easy) Write-Up

Finding hidden flag inside JPG image file given:
1. OPTION 1 = `$ strings Steg1.jpg | grep SKY`
2. OPTION 2 = https://29a.ch/photo-forensics/ > "Open File" > "String Extraction"
3. OPTION 3 = https://georgeom.net/StegOnline/upload > Upload file > "Show Strings"


