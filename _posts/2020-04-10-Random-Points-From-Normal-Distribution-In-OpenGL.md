---
title: "Random Points From Normal Distribution in OpenGL"
date: 2020-04-24
toc: true
excerpt: "OpenGL, Randomness, Normal Distribution"
mathjax: true
classes: wide	
youtubeId: tyNU6R2jDvA
---


## Creating a Normal Distribution


I will be using a pseudorandom generated set of points that can serve to test multiple algorithms that use points in their processing step. In particular, we can use a normal distribution to generate those points. Specifically, 2D points for our 2D OpenGL baseline. In addition,  I’d prefer that the geometries created from these points fall more or less within the center of our OpenGL window. For this , I will be using a sigmoid function to map the domain $(-\infty, \infty)$ from the normal distribution to the range (-1, 1). This is the needed range since OpenGL processes our 2D coordinates in the range (-1, 1) and then transforms that to window coordinates in the range (SCREEN_WIDTH, SCREEN_HEIGHT).

We can get a set of pseudorandom points using the template class std::normal_distribution from STL. Starting from an uniform random generator to get a mean and a standard deviation value, which serve as arguments for the std::normal_distribution constructor. After that we simply need to obtain the sample value that is returned from the std::normal_distribution object, and apply the sigmoid function for the mapping.   


## Implementation

At first, I decided to use the follwing sigmoid function: 

$$f(x) =  \frac{e^{2x} - 1}{e^{2x} + 1}$$

For sigmoid function, we will be using 

$$ f(x) = \frac{x}{1+ \lvert x \rvert}$$

this sigmoid function does the mapping $f(x) : (-\infty, \infty) \to (-1, 1)$.

```c++
void Normal_Distribution(float * sample) {

    static std::random_device rd;
    static std::mt19937 gen(rd());                                         // Mersenne twister PRNG
    static std::default_random_engine generator;
    static std::uniform_real_distribution<double> distribution(0.0, 1.0);

    static const float mean = distribution(generator);
    static const float std_dev = distribution(generator);
                                            
    static std::normal_distribution<float> normal_distribution(mean, std_dev); // instance of class std::normal_distribution with specific mean and stddev

    float x = normal_distribution(gen);
    *sample = x / (1 + std::abs(x)); // *sample in range (-1, 1) using sigmoid function
}
```
{% include youtubePlayer.html id=page.youtubeId %}