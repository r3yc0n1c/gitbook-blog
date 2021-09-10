# Invisible Cipher

```python
#!/usr/bin/env python3

def encrypt(pt, PTALPHA, CTALPHA):
	ct = ''
	for ch in pt:
		i = PTALPHA.index(ch)
		ct += CTALPHA[i]
	return ct


PTALPHA = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ '
CTALPHA = open('CTALPHA.txt').read().strip()
FLAG = open('flag.txt').read().strip()

assert len(CTALPHA) == len(PTALPHA)
assert all(x in PTALPHA for x in FLAG)

enc_flag = encrypt(FLAG, PTALPHA, CTALPHA)

with open('flag.enc','w') as f:
	f.write(enc_flag)
	print('DONE')
```

This was a basic Frequency analysis challenge and the ciphertext key-space/map was random Unicode characters. So,

* Pick up the nonsense phrase "ETAOIN SHRDLU" that represents the 12 most frequent letters in typical English language text.
* Check the frequency of each of the letters occurring in the ciphertext
* Start to map them to the original English Alphabet/map/key-space
* Assume the remaining common English words and totally recover the plaintext + FLAG

Solve Script: [solve.py](https://github.com/r3yc0n1c/ctf-challs/blob/main/WORMCON-0x01/invisible_cipher/solve.py)

> 🔰 you can uncomment this part in the solve script to see what's happening

```python
# visualize with
# pt = viz_us(pt)
pt = viz_idx(pt, enc_flag)
```

{% hint style="success" %}
Flag: wormcon{CAREFUL\_FREQUENCY\_ANALYSIS\_BREAKS\_SUBSTITUTION\_CIPHER}
{% endhint %}

{% hint style="info" %}
[https://en.wikipedia.org/wiki/Frequency\_analysis](https://en.wikipedia.org/wiki/Frequency_analysis)
{% endhint %}

