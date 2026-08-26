# Write-up: Vigenère Cipher — Hackropole

## Category

Cryptography / Intro

## Challenge Description

We are given a message encrypted with the Vigenère cipher and the key `FCSC`.
The goal is to decrypt the message and extract the flag.

**Encrypted message:**

```text
Gqfltwj emgj clgfv ! Aqltj rjqhjsksg ekxuaqs, ua xtwk
n'feuguvwb gkwp xwj, ujts f'npxkqvjgw nw tjuwcz
ugwygjtfkf qz uw efezg sqk gspwonu. Jgsfwb-aqmu f
Pspygk nj 29 cntnn hqzt dg igtwy fw xtvjg rkkunqf.
```

## Working Principle

The Vigenère cipher is a **polyalphabetic substitution cipher**.
Each letter of the plaintext is shifted by a different amount depending
on a repeating key.

The key `FCSC` gives the following shifts:

- F = 5
- C = 2
- S = 18
- C = 2

The key then repeats throughout the message.

**Decryption formula:**

```text
plaintext_letter = (ciphertext_letter - key_letter + 26) mod 26
```

The alphabet is numbered as follows:

```text
A = 0, B = 1, C = 2, ..., Z = 25
```

Spaces and punctuation are not encrypted; they remain unchanged.

For example:

```text
G = 6
F = 5

6 - 5 = 1
1 = B
```

Therefore, `G` encrypted with the key `F` decrypts to `B`.

---

## The Board Method

I first solved this challenge **manually** using a handwritten Vigenère
table. This helped me understand how the cipher works before using Python
to automate the decryption.

### What is the Vigenère Table?

The Vigenère table is a 26×26 grid where each row represents a different
Caesar shift.

To decrypt one letter:

1. Take the current key letter.
2. Find the corresponding row.
3. Locate the ciphertext letter in that row.
4. Read the corresponding plaintext letter from the column header.

### Worked Example — Decrypting `Gqfltwj`

The key `FCSC` repeats while spaces and punctuation are skipped:

```text
Ciphertext : G  q  f  l  t  w  j
Key        : F  C  S  C  F  C  S
```

| # | Ciphertext | Key | Decryption | Plaintext |
|---|------------|-----|------------|-----------|
| 1 | G | F | 6 - 5 = 1 | **B** |
| 2 | q | C | 16 - 2 = 14 | **o** |
| 3 | f | S | 5 - 18 = -13 → 13 | **n** |
| 4 | l | C | 11 - 2 = 9 | **j** |
| 5 | t | F | 19 - 5 = 14 | **o** |
| 6 | w | C | 22 - 2 = 20 | **u** |
| 7 | j | S | 9 - 18 = -9 → 17 | **r** |

Result:

```text
Gqfltwj → Bonjour
```

This confirms that the first word of the encrypted message is `Bonjour`.

---

## Vigenère Reference Table

Below is the full 26×26 Vigenère reference table:

| Key \ Plain | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z |
|-------------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **A** | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z |
| **B** | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A |
| **C** | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B |
| **D** | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C |
| **E** | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D |
| **F** | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E |
| **G** | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F |
| **H** | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G |
| **I** | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H |
| **J** | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I |
| **K** | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J |
| **L** | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K |
| **M** | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L |
| **N** | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M |
| **O** | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N |
| **P** | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O |
| **Q** | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P |
| **R** | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q |
| **S** | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R |
| **T** | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S |
| **U** | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T |
| **V** | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U |
| **W** | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V |
| **X** | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W |
| **Y** | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X |
| **Z** | Z | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y |

---

## Solution

### Python Script

After solving the first part manually, I wrote a Python script to automate
the decryption.

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
                    (ord(char) - ord('A') - shift + 26) % 26 + ord('A')
                )
            else:
                decrypted = chr(
                    (ord(char) - ord('a') - shift + 26) % 26 + ord('a')
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

### Output

The Python script successfully decrypts the message and confirms the
manual result.

### Decrypted Message

```text
Bonjour cher agent ! Votre prochaine mission, si vous
l'acceptez bien sur, sera d'infiltrer le reseau
souterrain ou se cache nos ennemis. Rendez-vous a
Nantes le 29 avril pour le debut de votre mission.
```

### Flag

```text
FCSC{Nantes}
```

---

## Summary

| Step | What I did | Method |
|------|------------|--------|
| 1 | Identified the cipher | Vigenère (polyalphabetic) |
| 2 | Identified the key | `FCSC` → F=5, C=2, S=18, C=2 |
| 3 | Aligned the key | Skipped spaces and punctuation |
| 4 | Decrypted manually | Vigenère table |
| 5 | Verified the result | Python script |
| 6 | Extracted the flag | `FCSC{Nantes}` |

## Conclusion

The Vigenère cipher is straightforward to decrypt when the key is known:
simply subtract the value of each key letter from the corresponding
ciphertext letter.

Without the key, longer messages can still be attacked using frequency
analysis and other cryptanalysis techniques because the repeating key
creates detectable patterns.