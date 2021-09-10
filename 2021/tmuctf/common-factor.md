---
description: 52 solves/ 383 points
---

# Common Factor

Source files and Solve Script: [TMUCTF/Common%20Factor](https://github.com/r3yc0n1c/CTF-Writeups/tree/main/2021/TMUCTF/Common%20Factor)

### Factoring N:

If we look closely at the first _hint_, we can find one of the factors of **N**,

$$
\begin{align}
n &= p_1 \cdot p_2 \cdot p_3 \cdot p_4 \cdot p_5 \\
x_2 + x_3 + y_1 &= p_2^2 + p_1p_2 + p_1^2p_2 + p_2^2p_1 = p_2 (p_1 + p_2)(p_1 + 1) = h_1 (say) \tag{1} \\
\therefore p_2 &= \gcd(n, h_1)
\end{align}
$$

From here, we can proceed to find another prime by solving the quadratic from \(1\)

$$
p_1^2p_2 + (p_2^2+p_2)p_1 + (p_2 - h1) = 0 \\~\\
\therefore p_1 = \frac{-(p_2^2+p_2) \pm \sqrt{(p_2^2+p_2)^2 - 4p_2(p_2-h1)}}{2p_2}
$$

From the _2nd hint,_ we can find another factor

$$
\begin{align}
y_2 + y_3 &= p_2^2 \cdot (p_3 + 1) - 1 + p_1 \cdot p_2 \cdot (p_3 + 1) - 1 = h_2 (say) \\
h_2 + 2 &= (p_3 + 1) \cdot (p_2^2 + p_1 \cdot p_2) \\
p_3 &= \frac{(h_2 + 2)}{(p_2^2 + p_1 \cdot p_2)} - 1
\end{align}
$$

At this point, we have 3/5 primes and I don't know how to find the other 2. Then I had a wild thought that if the flag is small enough i.e. $$Flag \leq 3*2048 \;bits$$ then we can decrypt it with these 3 primes we found! 

So, I used $$N = p_1 \cdot p_2 \cdot p_3$$ and got the flag!

```python
N = p1*p2*p3
phi = (p1-1)*(p2-1)*(p3-1)
d = inverse(e, phi)
m = pow(c,d,N)
print(long_to_bytes(m))
```

{% hint style="success" %}
Flag: TMUCTF{Y35!!!\_\_M4Y\_N0t\_4lW4y5\_N33d\_4ll\_p21M3\_f4c70R5}
{% endhint %}

### Note:

The flag is small enough\(**423 bits** only\). Thus we can use only **one prime** to solve this challenge!

```python
>>> p = 1790219150350431869249298318142647942912962940213432230093208907326419345731236923512405137703813455383171193311389221185396503166624248089450563913208309573240948978311631888770208
037787020200330113206552400119653261709264824328655007715928842654045773778279140586789508769027787206630445038315928935749253664040823298242382518017657335777114151740646677810822170721506
713319579227314265304324964929132392176156378849185348482575059832856778341315394429786594340752606742856546151539956126744508119509530051475559345042049323799727243545914017129273580301983
6543947409733063944596031206583742779877774484876687058741
>>> phi = (p-1)
>>> e = 65537
>>> d = inverse(e,(p-1))
>>> long_to_bytes(pow(c,d,p))
b'TMUCTF{Y35!!!__M4Y_N0t_4lW4y5_N33d_4ll_p21M3_f4c70R5}'
```



