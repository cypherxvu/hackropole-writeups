# Vigenère Cipher — Hackropole

## Category

Cryptography / Symmetric Cryptography

## Difficulty

Intro

## Challenge Description

This was my first real CTF cryptography challenge.

The challenge provides a message encrypted with the Vigenère cipher and the key `FCSC`.

The goal is to decrypt the message and recover the flag.

### Encrypted Message

```text
Gqfltwj emgj clgfv ! Aqltj rjqhjsksg ekxuaqs, ua xtwk
n'feuguvwb gkwp xwj, ujts f'npxkqvjgw nw tjuwcz
ugwygjtfkf qz uw efezg sqk gspwonu. Jgsfwb-aqmu f
Pspygk nj 29 cntnn hqzt dg igtwy fw xtvjg rkkunqf.
```

## Working Principle

The Vigenère cipher is a **polyalphabetic substitution cipher**.

Each letter of the plaintext is shifted according to a repeating key.

The key used in this challenge is:

```text
FCSC
```

Each letter corresponds to a numerical value:

```text
A = 0
B = 1
C = 2
...
Z = 25
```

Therefore:

```text
F = 5
C = 2
S = 18
C = 2
```

The key repeats throughout the encrypted message.

### Decryption Formula

To decrypt a letter:

```text
plaintext = (ciphertext - key + 26) mod 26
```

For example:

```text
G = 6
F = 5

6 - 5 = 1
```

And:

```text
1 = B
```

Therefore:

```text
G encrypted with key F → B
```

Spaces and punctuation are not encrypted and are therefore preserved.

## Manual Decryption — The Vigenère Board

I first solved the challenge manually using the traditional Vigenère table.

The Vigenère table is a 26×26 table containing all possible Caesar shifts.

For decryption:

1. Take the current key letter.
2. Find the row corresponding to that key.
3. Find the ciphertext letter in that row.
4. Read the corresponding plaintext letter.

### Example

Let's decrypt:

```text
Gqfltwj
```

using the key:

```text
FCSC
```

The key repeats under the ciphertext:

```text
Ciphertext : G  q  f  l  t  w  j
Key        : F  C  S  C  F  C  S
```

The first letter can be decrypted mathematically:

```text
G = 6
F = 5

6 - 5 = 1
```

Therefore:

```text
1 = B
```

For the second letter:

```text
Q = 16
C = 2

16 - 2 = 14
```

Therefore:

```text
14 = O
```

For the third letter:

```text
F = 5
S = 18

5 - 18 + 26 = 13
```

Therefore:

```text
13 = N
```

Continuing the same process gives:

```text
Gqfltwj → Bonjour
```

This confirms that the beginning of the plaintext is:

```text
Bonjour
```

## Mathematical Method

The same process can be performed directly using modular arithmetic.

For every encrypted letter:

```text
P = (C - K + 26) mod 26
```

Where:

- `P` = plaintext letter
- `C` = ciphertext letter
- `K` = key letter

The key `FCSC` repeats:

```text
F C S C F C S C F C S C ...
```

Spaces and punctuation are skipped when advancing through the key.

## Python Solution

After solving part of the message manually, I used Python to automate the complete decryption.

The following script works with uppercase and lowercase letters while preserving spaces and punctuation.

```python
def vigenere_decrypt(ciphertext, key):
    key = key.upper()
    result = []
    key_index = 0

    for char in ciphertext:
        if char.isalpha():
            shift = ord(key[key_index % len(key)]) - ord('A')

            if char.isupper():
                decrypted = chr(
                    (ord(char) - ord('A') - shift + 26) % 26
                    + ord('A')
                )
            else:
                decrypted = chr(
                    (ord(char) - ord('a') - shift + 26) % 26
                    + ord('a')
                )

            result.append(decrypted)
            key_index += 1

        else:
            result.append(char)

    return ''.join(result)


ciphertext = """Gqfltwj emgj clgfv ! Aqltj rjqhjsksg ekxuaqs, ua xtwk
n'feuguvwb gkwp xwj, ujts f'npxkqvjgw nw tjuwcz
ugwygjtfkf qz uw efezg sqk gspwonu. Jgsfwb-aqmu f
Pspygk nj 29 cntnn hqzt dg igtwy fw xtvjg rkkunqf."""

key = "FCSC"

print(vigenere_decrypt(ciphertext, key))
```

## Decrypted Message

Running the Python script gives:

```text
Bonjour cher agent ! Votre prochaine mission, si vous
l'acceptez bien sur, sera d'infiltrer le reseau
souterrain ou se cache nos ennemis. Rendez-vous a
Nantes le 29 avril pour le debut de votre mission.
```

The important information is:

```text
Nantes
```

This is the location mentioned in the decrypted message.

## Flag

```text
FCSC{Nantes}
```

## Summary

| Step | Action | Result |
|------|--------|--------|
| 1 | Identified the cipher | Vigenère |
| 2 | Identified the key | `FCSC` |
| 3 | Converted letters to numbers | A=0, B=1, ..., Z=25 |
| 4 | Applied the decryption formula | `(C - K + 26) mod 26` |
| 5 | Solved part manually | Using the Vigenère board |
| 6 | Automated the decryption | Python |
| 7 | Read the plaintext | Found `Nantes` |
| 8 | Extracted the flag | `FCSC{Nantes}` |

## What I Learned

This challenge helped me understand how the Vigenère cipher works rather than simply using an online decoder.

I learned three different approaches:

1. **The manual approach** using the Vigenère table.
2. **The mathematical approach** using modular arithmetic.
3. **The programming approach** using Python.

The Python method is obviously the fastest for a complete message, but doing the first part manually helped me understand what the algorithm was actually doing.

The main lesson is that cryptography becomes much easier to understand when the mathematical principle behind the algorithm is clear.