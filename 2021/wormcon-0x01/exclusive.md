# Exclusive

```python
#!/usr/bin/env python3
import os

def splitit(n):
	return (n >> 4), (n & 0xF)

def encrypt(n, key1, key2):
	m, l = splitit(n)
	e = ((m ^ key1) << 4) | (l ^ key2)
	return e

FLAG = open('flag.txt').read().lstrip('wormcon{').rstrip('}')
alpha = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ_'

assert all(x in alpha for x in FLAG)

otp = int(os.urandom(1).hex(), 16)
otpm, otpl = splitit(otp)

print(f"{otp = }")
cipher = []

for i,ch in enumerate(FLAG):
	if i % 2 == 0:
		enc = encrypt(ord(ch), otpm, otpl)
	else:
		enc = encrypt(ord(ch), otpl, otpm)
	cipher.append(enc)

cipher = bytes(cipher).hex()
print(f'{cipher = }')

open('out.txt','w').write(f'cipher = {cipher}')
```

Everyone loves XOR because it's easy to use but the cipher becomes vulnerable if you use only a single byte Key. We can easily brute-force the Key and recover our plaintext!

We already know that `otp = int(os.urandom(1).hex(), 16)` will generate the $$0 \leq otp \leq 255$$. The encryption works like this

* Split the _otp byte_ into two halves i.e. `otpm = upper nibble(upper 4 bits), otpl = lower nibble(lower 4 bits)`
* Split the _plaintext byte_ into two halves i.e. `m = upper nibble, l = lower nibble`
* Makes the XOR encryption and finally joins the ciphertext bytes.

$$
\text{ciphertext byte} = 
\begin{cases}
(m \oplus otpm)||(l \oplus otpl),  & \text{for even indices} \\
(m \oplus otpl)||(l \oplus otpm),  & \text{for odd indices}
\end{cases} \\~\\
\text{where $||$ represents bits concatenation}
$$

Thus, we can

* Reverse this and make our decryption function
* Brute-force the key
* Try to decrypt the ciphertext and check if all the characters are in the character space or not

Solve Script: [solve.py](https://github.com/r3yc0n1c/ctf-challs/blob/main/WORMCON-0x01/exclusive/solve.py)

{% hint style="success" %}
Flag: wormcon{x0r\_n1bbl3\_c1ph3r\_15\_4\_h0m3\_br3w3d\_c1ph3r}
{% endhint %}

