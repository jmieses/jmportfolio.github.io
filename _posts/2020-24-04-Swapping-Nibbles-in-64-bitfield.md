---
title: "Swapping Nibbles in a 64-bit Bitfield"
date: 2020-04-04
toc: true
excerpt: "Embedded, Endianess"
mathjax: true
classes: wide	
---


## The Bitfield Problem



```c
uint64 swapLong(uint64 val) {
    val = (val & 0x00000000FFFFFFFF) << 32 | (val & 0xFFFFFFFF00000000) >> 32;
    val = (val & 0x0000FFFF0000FFFF) << 16 | (val & 0xFFFF0000FFFF0000) >> 16;
    val = (val & 0x00FF00FF00FF00FF) << 8  | (val & 0xFF00FF00FF00FF00) >> 8;
    val = (val & 0x0F0F0F0F0F0F0F0F) << 4  | (val & 0xF0F0F0F0F0F0F0F0) >> 4;
    return val;
}
```