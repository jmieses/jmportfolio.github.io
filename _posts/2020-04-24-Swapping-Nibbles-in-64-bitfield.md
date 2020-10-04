---
title: "Swapping Nibbles in a 64-bit Bitfield"
date: 2020-04-24
toc: true
excerpt: "Embedded, Endianess"
mathjax: true
classes: wide	
---


## The Bitfield Problem


A bitfield defined in big endian platform can present a problem in a little endian based platform. Indeed, for big endian the Most Significant Bit (MSB) would be a the top of the bitfield, while it would be the Least Significant Bit (LSB) for little endian. A quick and dirty solution would be to start using a swapping function where needed to change endianess accordingly. Of course, it would be advisible to change the bitfield itself to the prefer endianess, like for example [this compiler switch in linux kernel](http://lxr.linux.no/linux+v2.6.38/include/linux/ip.h). But for the sake of explanation let's suppose it is not possible to change the bitfield structure. 

In addition, let's suppose that we need to keep the nibbles in a specific ordered sequence. In other words, the order of the nibble sequence determines the validity of the information. This case would be true for an API expecting a sequence of flags from a bitfield in which each flag is one bit. The flags are expected in specific order, where the MSB comes first.    


## How It Works


By taking the value and dividing it in half, we can move the front to the back half, and the back half to the front half. We achieve this by first clearing the halfs that we want 

$$ \frac{\qquad \texttt{0123456789ABCDEF} \\ \& \quad \texttt{00000000FFFFFFFF}}{\qquad \texttt{0000000089ABCDEF}} \hspace{3em} 
\frac{\qquad \texttt{0123456789ABCDEF} \\ \& \quad \texttt{FFFFFFFF00000000}}{\qquad \texttt{0123456700000000}} $$

Consequently, we can move each half to it's expected place. This is achieve using the right and left shift operator moving the bit sequence by 32 bits in each direction.

$$ \hspace{3em} \texttt{(0000000089ABCDEF << 32) = 89ABCDEF00000000} \\
\hspace{3em} \texttt{(0123456700000000 >> 32) = 0000000001234567}$$

Then, it is just a matter of using the OR operator to get complete the swapped value. 

$$\frac{\quad \texttt{89ABCDEF00000000} \\ | \quad \texttt{0000000001234567}}{\quad \texttt{89ABCDEF01234567}} $$

Finally, repeat this recipe for the next 2 bytes, 1 byte, and the nibbles. That will reverse the order sequence of the byte list based on the nibbles.

```c
typedef unsigned long long uint64;

void Swap_Nibbles_64Bit(uint64& val) {
    val = (val & 0x00000000FFFFFFFF) << 32 | (val & 0xFFFFFFFF00000000) >> 32; // remove line for 32-bit swap
    val = (val & 0x0000FFFF0000FFFF) << 16 | (val & 0xFFFF0000FFFF0000) >> 16;
    val = (val & 0x00FF00FF00FF00FF) << 8  | (val & 0xFF00FF00FF00FF00) >> 8;
    val = (val & 0x0F0F0F0F0F0F0F0F) << 4  | (val & 0xF0F0F0F0F0F0F0F0) >> 4;	// remove line for byte swap
}
```

A nice thing about this function, is that by removing the last or the first line, we can make a 32-bit swap and a byte swapp respectively.