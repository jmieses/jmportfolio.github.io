---
title: "Swapping Nibbles in a 64-bit Bitfield"
date: 2020-04-04
toc: true
excerpt: "Embedded, Endianess"
mathjax: true
classes: wide	
---


## The Bitfield Problem


A bitfield defined in big endian platform can present a problem in a little endian based platform. Indeed, for big endian the Most Significant Bit (MSB) would be a the top of the bitfield, while it would be the Least Significant Bit (LSB) for little endian. A quick and dirty solution would be to start using a swapping function where needed to change endianess accordingly. Of course, it would be advisible to change the bitfield itself to the prefer endianess, but for the sake of explanation let's suppose it is not possible.  

$$ 0A0B0C0E0D0F0F0D0 & 0x00000000FFFFFFFF  $$

```c
uint64 Swap_Nibbles_64Bit(uint64 val) {
    val = (val & 0x00000000FFFFFFFF) << 32 | (val & 0xFFFFFFFF00000000) >> 32;
    val = (val & 0x0000FFFF0000FFFF) << 16 | (val & 0xFFFF0000FFFF0000) >> 16;
    val = (val & 0x00FF00FF00FF00FF) << 8  | (val & 0xFF00FF00FF00FF00) >> 8;
    val = (val & 0x0F0F0F0F0F0F0F0F) << 4  | (val & 0xF0F0F0F0F0F0F0F0) >> 4;
    return val;
}
```