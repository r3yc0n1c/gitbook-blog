# Sir Oracle

```python
#!/usr/bin/env python3
from Crypto.Util.number import *
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from Crypto.Hash import SHA256
import os


bits = 256
bs = AES.block_size
FLAG = open('flag.txt').read()

menu = """
+-------------------------+
|                         |
|        M E N U          |   
|                         |
| [1] DH Parameters       |
| [2] View PublicKeys     |
| [3] Encrypt Flag        |
| [4] Generate PublicKey  |
|                         |
+-------------------------+
"""

def encrypt(m, key):
	key = SHA256.new(str(key).encode()).digest()[:bs]
	iv = os.urandom(bs)
	cipher = AES.new(key, AES.MODE_CBC, iv)
	enc = cipher.encrypt(pad(m.encode(), 16))
	return (enc.hex(), iv.hex())

def gen_pubkey(g, p, privkey):
	l = privkey.bit_length()
	m = int(input("Enter some random integer > "))
	new_privkey = privkey ^ m
	new_pubkey = pow(g, new_privkey, p)
	return new_pubkey

if __name__ == '__main__':
	g = 2
	p = getPrime(bits)

	# Rick Astley
	a = getRandomRange(1, p-1)
	R = pow(g,a,p)

	# Kermit the Frog
	b = getRandomRange(1, p-1)
	K = pow(g,b,p)

	s = pow(R,b,p)
	enc_flag, iv = encrypt(FLAG, s)

	# test
	with open('priv.txt','w') as f:
		f.write('a='+str(a)+'\n')
		f.write('b='+str(b))

	print(menu)
	l = p.bit_length() + 4
	
	try:
		for _ in range(l):
			ch = input("Choice ? ").strip().lower()

			if ch == '1':
				print("[DH parameters]")
				print(f"{g = }")
				print(f"{p = }\n")
			
			elif ch == '2':
				print("[Rick's PublicKey]")
				print(f"{R = }\n")
				print("[Kermit's PublicKey]")
				print(f"{K = }\n")
			
			elif ch == '3':
				print("[ENC FLAG]")
				print(f"{enc_flag = }\n")
				print("[IV]")
				print(f"{iv = }\n")

			elif ch == '4':
				npk = gen_pubkey(g, p, b)
				print("[Kermit's New PublicKey]")
				print(f"{npk = }\n")
			else:
				print(f":( Invalid Choice !!!")
				break
	except Exception as e:
		print(e)
		exit()

```

  
After connecting to the server, we can see that the server runs a [Diffie Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) Oracle which provides 4 options. It's generic DH and the $$4^{th}$$option is for generating a new public key by XORing the original secret key with any value we provide. As we can control this value \(let's say mask\), we can leak the bits of the secret key!

If we select the mask as the power of$$2$$, then we can check the following properties \(basically the properties of XOR\) for any integer.

$$
b \oplus 2^{0} = 
\begin{cases}
b + 2^{0},  & \text{if $1st$ bit was 0} \\
b -  2^{0}, & \text{if $1st$ bit was 1}
\end{cases} \\
b \oplus 2^{1} = 
\begin{cases}
b + 2^{1},  & \text{if $2nd$ bit was 0} \\
b -  2^{1}, & \text{if $2nd$ bit was 1}
\end{cases} \\
b \oplus 2^{2} = 
\begin{cases}
b + 2^{2},  & \text{if $3rd$ bit was 0} \\
b -  2^{2}, & \text{if $3rd$ bit was 1}
\end{cases}
$$

Now we can jump back to the challenge and use this knowledge to leak the private key$$(b)$$. Let's see what happens actually when we send$$2^{0}$$as the mask.

$$
npk \equiv g^{b \oplus 2^{n}} \equiv 
\begin{cases}
g^{b+2^{n}} \equiv  g^{b}*g^{2^{n}} \equiv  K*g^{2^{n}},  & \text{if $nth$ bit was 0} \\
g^{b-2^{n}} \equiv  g^{b}*g^{-2^{n}} \equiv  K*g^{-2^{n}}, & \text{if $nth$ bit was 1}
\end{cases} \pmod p
$$

As we have $$K,g \hspace{1mm} and \hspace{1mm} p$$ we can calculate $$g^{-2^{n}} \pmod p$$and check if

$$
key\_bit_{n} = 
\begin{cases}
1, & \text{if $K*g^{-2^{n}} \equiv npk \pmod p$} \\
0, & \text{if $K*g^{-2^{n}} \not\equiv npk \pmod p$} 
\end{cases}
$$

With this knowledge, we can easily leak the individual/multiple bits of the _secret\_key_ \(b\) and then calculate $$s \equiv R^{b} \equiv g^{ab} \pmod p$$ and use it as the AES key to decrypt the FLAG.

Solve Script: [`dh_oracle_exploit.py`](https://github.com/r3yc0n1c/ctf-challs/blob/main/WORMCON-0x01/sir_oracle/dh_oracle_leak.py)

{% hint style="success" %}
Flag: wormcon{00p5!\_n0\_m45k\_n0\_FL4G}
{% endhint %}

{% hint style="info" %}
* [https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman\_key\_exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)
* [https://en.wikipedia.org/wiki/Exclusive\_or](https://en.wikipedia.org/wiki/Exclusive_or)
* [http://www.cs.umd.edu/class/sum2003/cmsc311/Notes/BitOp/xor.html](https://web.archive.org/web/20051204172015/http://www.cs.umd.edu/class/sum2003/cmsc311/Notes/BitOp/xor.html)
{% endhint %}

