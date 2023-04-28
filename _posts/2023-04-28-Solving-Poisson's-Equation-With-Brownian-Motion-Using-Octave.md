# Solving Poisson's Equation With Brownian Motion Using Octave

**Overview**
---
Partial differential equations are very complex, and difficult problems to derive forms of exact solutions unless using assumptions to make solutions easier to derive. Because of this, many domains that are solved with these equations are of simple geometries leaving the challenge of trying to look at more obscure domains. One possible solution to this problem is using the Feynman-Kac formula to derive an approximate mapping of the PDE. This post will go through the basic ideas of what is needed to make a solver of this type and some examples of typical solutions.



## **Concepts Needed for Solution**

To create this numerical method, a few basic definitions need to be addressed to allow for the reader to understand the codes as developed. These will be breifly addressed each.

### What is Brownian Motion
--
Brownian motion in of itself is the random movement of a point/particle in some domain. To make sense of this in the contexts of a math, this is defined as a wiener processes and given requirements needed [1].

1. The wiener process must have a prescribed start point. In the case of our numerical method, this will be the point at which we want to solve the value of the PDE.
2. The process must be continuous along its path. This will ensure for proper integration.
3. The increments of each step must be independent from any other step within the system. 
4. The increment of each step is a normal distributed random variable with a mean of zero and a variance of the step size. This will be shown later within the code. 

An example code of this process can be seen below along with a graph showing mutliple unique sets of brownian motion from the same code.

![png](/Extra/BRWPDE/myfile.png)

```octave

n = 1000;
delta_t = sqrt(.001);

#Produces an array of random variables and multiples by time step to make Brownian Motion
dW = delta_t * randn(1001,1);
dW(1) = 0;
W = cumsum(dW);

plot(W);
```

#### Some PowerShell Code

```powershell
Write-Host "This is a powershell Code block";

# There are many other languages you can use, but the style has to be loaded first

ForEach ($thing in $things) {
    Write-Output "It highlights it using the GitHub style"
}
```
## References
[1] Bass, R. (2011). Stochastic Processes (Cambridge Series in Statistical and Probabilistic Mathematics). Cambridge: Cambridge University Press. doi:10.1017/CBO9780511997044

[2] Martin, C., Zhang, H., Costacurta, J. et al. Solving Elliptic Equations with Brownian Motion: Bias Reduction and Temporal Difference Learning. Methodol Comput Appl Probab 24, 1603–1626 (2022). https://doi.org/10.1007/s11009-021-09871-9
