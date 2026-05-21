# CTF Write-up: AdveRSArial Crypto — Infant

**Platform:** [Hackropole](https://hackropole.fr) (FCSC — France Cybersecurity Challenge)  
**Category:** Cryptography  
**Difficulty:** Infant  
**Flag:** `FCSC{d0bf88291bcd488f28a809c9ae79d53da9caefc85b3790f57615e61c70a45f3c}`

---

## 1. Challenge Description

When I first opened this challenge, I saw a Python script and an `output.txt` file. Honestly my first reaction was... okay what am I supposed to do with this? 😅

The challenge message said:

> *"I just took a class on RSA but I think I forgot something. It seems to me that the teacher was talking about two things, but I don't know exactly what. Can you help me?"*

I read that like three times. **Two things?** I had just started learning about RSA so I wasn't sure what "two things" meant. After some research I realized — RSA needs **two prime numbers** to work, not one. That was the hint hidden in the description the whole time.

### What we are given

The file `adversarial-crypto-infant.py` is the script the challenge creator used to encrypt the flag:

```python
from Crypto.Util.number import getStrongPrime, bytes_to_long, long_to_bytes

n = getStrongPrime(2048)   # ← this line is the problem
e = 2 ** 16 + 1
flag = bytes_to_long(open("flag.txt", "rb").read())
c = pow(flag, e, n)

print(f"{n = }")
print(f"{e = }")
print(f"{c = }")
```

And `output.txt` gives us the public values:

```
n = 22914764349697556963541692665721076425490063991574936243571428156261302060328685591556514036751777776065771167330244010708082147401402002914377904950080486799957005111360365028092884367373338454223568447811216200859660057226322801828334633020895296785582519610777820724907394060126570265818769159991752144783469338557691407102432786644694590118176582000965124360500257946304028767088296724907062561163478654995994205065812479605136088813543435895840276066683243706020091519857275219422246006137390619897086478975872204136389082598585864385077220265194919486850918633328368814287347732293510186569121425821644289329813
e = 65537
c = 11189917160698738647911433493693285101538131455035611550077950709107429331298329502327358588774261161674422351739941120882289954400477590502272629693853242116507000433761914368814656180874783594812260498542390500221519883099478550863172147588922341571443502449435143090576514228274833316274013491937919397957017546671325357027765817692571583998487352090789855980131184451611087822399088669705683765370510052781742383736278295296012267794429263720509724794426552010741678342838319060084074826713065120930332229122961216786019982413982114571551833129932338204333681414465713448112309599140515483842800125894387412148599
```

| Variable | Meaning |
|----------|---------|
| `n` | The public modulus |
| `e` | The public exponent (65537 is standard) |
| `c` | The encrypted flag |

---

## 2. How It Works

### My understanding of RSA (as a beginner)

I will be honest — before this challenge I had only heard of RSA in class. So I had to look things up as I went. Here is what I understood:

RSA encryption works like this:

1. You pick **two large secret prime numbers**: `p` and `q`
2. You multiply them to get the public modulus: `n = p × q`
3. You encrypt the message with: `c = message^e mod n`
4. To decrypt, you need a private key `d`, and you compute: `message = c^d mod n`

To find `d` you need something called **φ(n)** (Euler's totient):

```
φ(n) = (p - 1) × (q - 1)
```

In normal RSA this is impossible to compute if you only know `n`, because factoring a huge number back into `p` and `q` is extremely hard. That's the whole point — the security comes from that difficulty.

### The bug I found

When I looked at the creator's script more carefully I noticed this line:

```python
n = getStrongPrime(2048)
```

It calls `getStrongPrime()` **once**. But RSA needs `n = p × q` — two primes multiplied together. The creator only generated one prime and used it directly as `n`.

That means `n` itself **is a prime number**. And when `n` is prime, there is a rule from mathematics called **Fermat's Little Theorem** that says:

```
φ(n) = n - 1    (only works when n is prime)
```

So instead of φ(n) being impossible to compute, it becomes **completely trivial**. I just subtract 1. That's it.

### My handwritten notes

I wrote everything down by hand to make sure I understood it before writing any code. It really helped me to see the math on paper first.

![Handwritten notes page 1](images/notes-page1.jpg)
![Handwritten notes page 2](images/notes-page2.jpg)

### Step by step

| Step | Formula | What I did |
|------|---------|------------|
| 1 | `φ(n) = n - 1` | Possible because `n` is prime |
| 2 | `d = e⁻¹ mod φ(n)` | Compute the private key |
| 3 | `flag_int = c^d mod n` | Decrypt the ciphertext |
| 4 | `flag = bytes(flag_int)` | Convert the big number back to readable text |

---

## 3. Solution

### My Python script

The first version I tried used `sympy` to verify that `n` is prime, but then I realized I didn't even need that — I already knew it was prime from reading the script. So I removed the import and kept it simple. This runs on any Python 3.8+ without installing anything:

```python
# solve.py

n = 22914764349697556963541692665721076425490063991574936243571428156261302060328685591556514036751777776065771167330244010708082147401402002914377904950080486799957005111360365028092884367373338454223568447811216200859660057226322801828334633020895296785582519610777820724907394060126570265818769159991752144783469338557691407102432786644694590118176582000965124360500257946304028767088296724907062561163478654995994205065812479605136088813543435895840276066683243706020091519857275219422246006137390619897086478975872204136389082598585864385077220265194919486850918633328368814287347732293510186569121425821644289329813
e = 65537
c = 11189917160698738647911433493693285101538131455035611550077950709107429331298329502327358588774261161674422351739941120882289954400477590502272629693853242116507000433761914368814656180874783594812260498542390500221519883099478550863172147588922341571443502449435143090576514228274833316274013491937919397957017546671325357027765817692571583998487352090789855980131184451611087822399088669705683765370510052781742383736278295296012267794429263720509724794426552010741678342838319060084074826713065120930332229122961216786019982413982114571551833129932338204333681414465713448112309599140515483842800125894387412148599

# n is prime so phi(n) = n - 1
phi_n = n - 1

# compute private key
d = pow(e, -1, phi_n)

# decrypt
flag_int = pow(c, d, n)

# convert number to text
flag = flag_int.to_bytes((flag_int.bit_length() + 7) // 8, 'big')
print(flag.decode())
```

I ran it on [Online Python (Pyodide)](https://www.online-python.com/pyodide) because I didn't have a local terminal set up. The editor showed some red warnings about line length but those are just style warnings — the code ran fine.

### Output screenshot

![Python output screenshot](images/screenshot.png)

### Flag

```
FCSC{d0bf88291bcd488f28a809c9ae79d53da9caefc85b3790f57615e61c70a45f3c}
```

---

## Summary

| Step | What I did | Result |
|------|-----------|--------|
| 1 | Read the challenge description | Found the hint: "two things" = two primes |
| 2 | Opened `adversarial-crypto-infant.py` | Spotted `getStrongPrime()` called only once |
| 3 | Understood the RSA theory | Normal RSA needs `n = p × q` |
| 4 | Identified the bug | `n` is a single prime, not a product of two |
| 5 | Applied Fermat's Little Theorem | `φ(n) = n - 1` becomes trivial |
| 6 | Wrote the solve script | 4 lines of pure Python, no libraries |
| 7 | Got the flag | ✅ Solved! |

---

## Conclusion

This was my first real cryptography challenge and I learned a lot from it. What surprised me the most is how **one small mistake** — using one prime instead of two — completely destroys the security of RSA. Like, the math behind RSA is solid, but if you implement it wrong even slightly, it falls apart immediately.

I also learned that reading the source code carefully is really important. The vulnerability was right there in the first line of the script. I almost missed it because I was focused on the math.

For anyone else who is a beginner like me: don't be scared of RSA challenges. Take your time, write down the math on paper, and read the code line by line. The hint is usually hiding in plain sight. 🙂
