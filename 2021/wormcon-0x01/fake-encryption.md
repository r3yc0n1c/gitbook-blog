# Fake Encryption

```python
#!/usr/bin/env python3
from Crypto.Cipher import DES
import random


def splitn(text, size):
	return [text[i:i+size] for i in range(0,len(text),size)]

def encrypt(m, key):
	cipher = DES.new(key, DES.MODE_ECB)
	return cipher.encrypt(m)


flag = open('flag.png','rb').read()
ff = splitn(flag, 8)

assert len(flag) % 8 == 0, len(flag)

key = random.randbytes(8)
enc_flag = encrypt(flag, key)

random.seed(ff[0])
random.shuffle(ff)

ff = b''.join(ff)
enc_ff = encrypt(ff, key)

open('flag.png.enc', 'wb').write(enc_flag)
open('ff_error.png', 'wb').write(ff)
open('ff_error.png.enc', 'wb').write(enc_ff)

```

### The Encryption

The source script encrypts the image file which contains the flag using [Data Encryption Standard \(DES\)](https://en.wikipedia.org/wiki/Data_Encryption_Standard) [ECB MODE](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_%28ECB%29) as follows:

* Splits data into the size of a single DES chunk \(i.e. 8 bytes\)
* Encrypts the Orignal Flag image
* Uses the first block as a seed to generate the random sequence
* Shuffles the original data chunks of the image and saves it
* Encrypts the shuffled data 

ECB MODE "_lacks_ [_diffusion_](https://en.wikipedia.org/wiki/Confusion_and_diffusion)_" i.e._ in __ECB mode, encryption of identical plaintext blocks results in identical ciphertext blocks.

### The Attack

With this info, we can retrieve the flag if we map the ciphertext blocks to the plaintext blocks and then try to unshuffle the plaintext data. As the flag was a PNG image, the first block\(that's used as the seed\) is the PNG file Signature\(hex value = `89 50 4E 47 0D 0A 1A 0A`\) which remains the same for every PNG file.

Thus, we can set this seed and generate a sample sequence and collect the shuffled indices. Then we can use this to recreate the flag from the shuffled chunks.

Solve Script: [solve.py](https://github.com/r3yc0n1c/ctf-challs/blob/main/WORMCON-0x01/fake_encryption/solve.py)

{% hint style="success" %}
Flag: wormcon{ECB\_lacks\_diffiusion}
{% endhint %}

{% hint style="info" %}
* [https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_%28ECB%29)
* [https://en.wikipedia.org/wiki/Portable\_Network\_Graphics](https://en.wikipedia.org/wiki/Portable_Network_Graphics)
* [http://www.libpng.org/pub/png/spec/1.2/PNG-Structure.html](http://www.libpng.org/pub/png/spec/1.2/PNG-Structure.html)
{% endhint %}

