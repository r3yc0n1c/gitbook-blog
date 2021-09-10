# Rem, Shinobu, Asuna

```python
#!/usr/bin/env python3
from Crypto.Util.number import *

FLAG = open('flag.txt', 'rb').read()

bits = 512
e = 65537
p = getPrime(bits)
q = getPrime(bits)
n = p*q
phi = (p-1)*(q-1)
d = inverse(e, phi)
hint = 2*d*(p-1337)

m = bytes_to_long(FLAG)
c = pow(m, e, n)

print(f"n = {hex(n)}")
print(f"e = {hex(e)}")
print(f"c = {hex(c)}")
print(f"hint = {hex(hint)}")

```

Typical RSA challenge and YOU DON'T NEED TO FACTORIZE **N** every time you see an RSA challenge!

We have,

$$
p \approx 512 \text{ bits} \\
q \approx 512 \text{ bits} \\
n = p*q\approx 1024 \text{ bits}\\
hint^{+} = 2*d*(p-1337)
\\~\\
\text{+ where d is the private exponent}
$$

Actually, it's [very hard to factor a 1024 bit RSA public modulus](https://en.wikipedia.org/wiki/RSA_Factoring_Challenge) but If we have some of the leaked data about private keys or the modulus then we can use some tricks to factor the 1024-bit modulus.

Let's start with [Fermat's little theorem](https://en.wikipedia.org/wiki/Fermat's_little_theorem) which states that,

$$
a^{p-1} \equiv 1 \pmod p \\
a^{p-1}-1 \equiv 0 \pmod p \\~\\
\text{i.e. $(a^{p-1}-1)$ is an integer  multiple of $p$}
$$

Let $$a \equiv 2^{e} \pmod n$$. Now we can apply this here,

$$
\begin{aligned} 
a^{hint} &= a^{2*d*(p-1337)} \pmod n\\
&= (2^{e})^{2*d*(p-1337)} \pmod n\\
&= (2^{2})^{e*d*(p-1337)} \pmod n\\
&= 4^{(p-1337)} \pmod n\\
&= 4^{(p-1) - 1336} \pmod n\\
&= 4^{(p-1)}*4^{-1336} \pmod n\\
a^{hint} * 4^{1336} &= 4^{p-1} \pmod n\\
\text{Therefore,}\\
p &= GCD(a^{hint} * 4^{1336} -1, n)
\end{aligned}
$$

With this trick, we can get one of the factors of the public modulus \(n\) ****and then simply decrypt the ciphertext!

Solve Script: [solve.py](https://github.com/r3yc0n1c/ctf-challs/blob/main/WORMCON-0x01/rem_shinobu_asuna/solve.py)

{% hint style="success" %}
Flag: wormcon{RSA\_And\_Fermat's\_Little\_Theorem!?}
{% endhint %}

{% hint style="info" %}
* [https://en.wikipedia.org/wiki/RSA\_\(cryptosystem\)](https://en.wikipedia.org/wiki/RSA_%28cryptosystem%29)
* [https://en.wikipedia.org/wiki/Fermat%27s\_little\_theorem](https://en.wikipedia.org/wiki/Fermat%27s_little_theorem)
* [https://crypto.stackexchange.com/questions/388/what-is-the-relation-between-rsa-fermats-little-theorem](https://crypto.stackexchange.com/questions/388/what-is-the-relation-between-rsa-fermats-little-theorem)
{% endhint %}

