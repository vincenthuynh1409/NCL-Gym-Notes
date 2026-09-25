# Cryptography

#Cybersecurity #CTF #NCL #Resources 

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


