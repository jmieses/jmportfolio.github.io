---
title: "Random Points From Normal Distribution in OpenGL"
date: 2020-04-24
toc: true
excerpt: "OpenGL, Randomness, Normal Distribution"
mathjax: true
classes: wide	
---


## Creating a Normal Distribution


I will be using a pseudorandom generated set of points that can serve to test multiple algorithms that use points in their processing step. In particular, we can use a normal distribution to generate those points. Specifically, 2D points for our 2D OpenGL baseline. In addition,  I’d prefer that the geometries created from these points fall more or less within the center of our OpenGL window. For this , I will be using a sigmoid function to map the domain $[-\infty, \infty]$ from the normal distribution to the range [-1, 1]. This is the needed range since OpenGL processes our 2D coordinates in the range (-1, 1) and then transforms that to window coordinates in the range (SCREEN_WIDTH, SCREEN_HEIGHT).

We can get a set of pseudorandom points using the template class std::normal_distribution from STL. Starting from an uniform random generator to get a mean and a variance value, which serve as arguments for the std::normal_distribution constructor. After that we simply need to obtain the sample value that is returned from the std::normal_distribution object.   


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