---
description: 253 solves / 175 points
---

# Gotta Decrypt Them All

Source files and Solve Script:  [CSAW-Quals/crypto/Gotta Decrypt Them All](https://github.com/r3yc0n1c/CTF-Writeups/tree/main/2021/CSAW-Quals/crypto/Gotta%20Decrypt%20Them%20All)

### Attack Idea

**STEP 1:** Identify the [Morse ](https://en.wikipedia.org/wiki/Morse_code)encoded string

**STEP 2:** Decrypt to get Integers

**STEP 3:** Convert them to ASCII characters

**STEP 4:** Identify [Base64](https://en.wikipedia.org/wiki/Base64) and decode it to get [RSA ](https://en.wikipedia.org/wiki/RSA_%28cryptosystem%29)parameters and encrypted message

**STEP 5:** Check the **small public exponent \(e\)** and take the **eth root** to get the message

{% hint style="info" %}
Why STEP 5 works?

In practice, we pick the RSA parameters and the message such that$$m^{e} \geq n$$. If we use a small message and a small exponent then it just becomes$$c = m^{e}$$ and we can directly calculate$$m = \sqrt[e]{}c$$ to get back our message.
{% endhint %}



