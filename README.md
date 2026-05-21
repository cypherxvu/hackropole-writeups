# Symmetric Crypto — Vigenère Cipher

This was my first real CTF cryptography challenge and honestly it wasn't that hard once I understood the concept. What I liked about it is that I chose to solve it three different ways — the old-school board method like cryptographers did before computers, then the math formula, and finally Python to automate everything.

## The three methods I used

**Method 1 — The board (old-school)**
I drew the 26×26 Vigenère table by hand and decrypted letter by letter. It takes time but you really understand what's happening.

**Method 2 — The formula**
Each letter has a number (A=0, B=1... Z=25). To decrypt: subtract the key value from the ciphertext value. For example G=6, key F=5, so 6-5=1=B. Simple once you see it.

**Method 3 — Python**
I wrote a script to decrypt the whole message automatically. This is the fastest way and works for any Vigenère cipher.

![My handwritten board](https://raw.githubusercontent.com/cypherxvu/hackropole-writeups/main/Board.jpg.jpeg)

## Flag
`FCSC{Nantes}`