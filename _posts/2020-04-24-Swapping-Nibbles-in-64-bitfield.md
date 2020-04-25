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

In addition, let's suppose that we need to keep the nibbles in a specific ordered sequence; either nibble 1 is follow by nibble 2 or viceversa, otherwise the chain of information is broken. In other words, the order of the nibble sequence determines the validity of the information. This case would be true for an API expecting a sequence of flags in specific order.    

$$ \texttt{0x0A0B0C0E0D0F0F0D0} \& \texttt{0x00000000FFFFFFFF}  $$

```c
uint64 Swap_Nibbles_64Bit(uint64 val) {
    val = (val & 0x00000000FFFFFFFF) << 32 | (val & 0xFFFFFFFF00000000) >> 32;
    val = (val & 0x0000FFFF0000FFFF) << 16 | (val & 0xFFFF0000FFFF0000) >> 16;
    val = (val & 0x00FF00FF00FF00FF) << 8  | (val & 0xFF00FF00FF00FF00) >> 8;
    val = (val & 0x0F0F0F0F0F0F0F0F) << 4  | (val & 0xF0F0F0F0F0F0F0F0) >> 4;
    return val;
}
```