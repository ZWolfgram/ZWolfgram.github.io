# 2d Animations MatLibPlot Python

## Overview

Using just the MatLibPlot, one can create an animation video that is purely built into the python program without outside software. This a an extention of the post [1], but more centric towards a finite difference code. 

## **Animation Code**

```python
import matplotlib.pyplot as mpl
import numpy as np
from mpl_toolkits.mplot3d import axes3d
from matplotlib.animation import FuncAnimation

fig = mpl.figure()
ax = mpl.axes(projection='3d')
mpl.xlim(0,5)
mpl.ylim(0,5)

domainx = np.outer(np.linspace(0, 5, 500), np.ones(500))
domainy = domainx.copy().T

rho = 1 #kg/m^3
Lambda = 1 #Pa
Shear_Modulus = 1 #Pa 

Deltax = .01 #m
Deltat = .005 #s
Time_total = .005

Boundary = 0 #N m

def f(x, y):
    return -20*np.cos(x/np.pi)*np.cos(y/np.pi)

xline = np.linspace(-5,5,500)
yline = np.linspace(-5,5,500)
X,Y = np.meshgrid(xline, yline)
Z = f(X,Y)

M11P = np.zeros([500,500])
M22P = np.zeros([500,500])
M11N = np.zeros([500,500])
M22N = np.zeros([500,500])
M11C = Z
M22C = Z

for p in range(500):
    for q in range(500):
        if q == 0:
                M11N[p,q] = Boundary
                M22N[p,q] = Boundary
        elif q == 499:
                M11N[p,q] = Boundary
                M22N[p,q] = Boundary
        elif p == 0:
                M11N[p,q] = Boundary
                M22N[p,q] = Boundary
        elif p == 499:
                M11N[p,q] = Boundary
                M22N[p,q] = Boundary
for p in range(1,499):
    for q in range(1,499):
            M11N[p,q] = M11C[p,q] + ((Shear_Modulus*.000000001**2)/(rho))*((1/(Deltax**2))*(1+((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus)))*(-2*M11C[p,q]+M11C[p,q+1]+M11C[p,q-1]) + (1/(Deltax**2))*(-2*M11C[p,q]+M11C[p-1,q]+M11C[p+1,q]) + (((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus))/(Deltax**2))*(-2*M22C[p,q]+M22C[p,q+1]+M22C[p,q-1]))
            M22N[p,q] = M22C[p,q] + ((Shear_Modulus*.000000001**2)/(rho))*((1/(Deltax**2))*(1+((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus)))*(-2*M22C[p,q]+M22C[p+1,q]+M22C[p-1,q]) + (1/(Deltax**2))*(-2*M22C[p,q]+M22C[p,q+1]+M22C[p,q-1]) + (((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus))/(Deltax**2))*(-2*M11C[p,q]+M11C[p+1,q]+M11C[p-1,q])) 

M11P = M11C            
M11C = M11N
M22P = M22C
M22C = M22N
M11N = np.zeros([500,500])
M22N = np.zeros([500,500])

def animate(frame):
    global Deltat, Deltax, M11C, M11N, M11P, M22C, M22N, M22P, domainx, domainy,Shear_Modulus,rho,Lambda
    ax.clear()
    t = Deltat
    for p in range(1,499):
        for q in range (1,499):
                M11N[p,q] = -M11P[p,q] + 2*M11C[p,q] + ((Shear_Modulus*t**2)/(rho))*((1/(Deltax**2))*(1+((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus)))*(-2*M11C[p,q]+M11C[p,q+1]+M11C[p,q-1]) + (1/(Deltax**2))*(-2*M11C[p,q]+M11C[p-1,q]+M11C[p+1,q]) + (((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus))/(Deltax**2))*(-2*M22C[p,q]+M22C[p,q+1]+M22C[p,q-1]))
                M22N[p,q] = -M22P[p,q] + 2*M22C[p,q] + ((Shear_Modulus*t**2)/(rho))*((1/(Deltax**2))*(1+((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus)))*(-2*M22C[p,q]+M22C[p+1,q]+M22C[p-1,q]) + (1/(Deltax**2))*(-2*M22C[p,q]+M22C[p,q+1]+M22C[p,q-1]) + (((2*Lambda+2*Shear_Modulus)/(3*Lambda+2*Shear_Modulus))/(Deltax**2))*(-2*M11C[p,q]+M11C[p+1,q]+M11C[p-1,q]))  
    M11P = M11C
    M11C = M11N
    M22P = M22C
    M22C = M22N
    M11N = np.zeros([500,500])
    M22N = np.zeros([500,500])
    ax.set_zlim(-20,20)
    ax.plot_surface(domainx,domainy,M11C, edgecolor='none')
    print(2*t+t*frame)
    return fig

anim = FuncAnimation(fig=fig,func = animate,frames=2000,interval=20)
anim.save('A.mp4',fps=100)
```
## **Necessary Portions In the Animate Function**

What one will find is the necessary of global variables. Examples of [1] show the usage of the frame number to be used in some way to calculation a way of the field changing. However, within an actual finite difference code, one needs to update a set matrix with the initial conditions starting the 2d field. In this case, we have two seperate fields being calculated as a function of past, present, and future fields in regards to time step. This is done with the initialization of the variables of global in the first line of the function.
```python
global Deltat, Deltax, M11C, M11N, M11P, M22C, M22N, M22P, domainx, domainy,Shear_Modulus,rho,Lambda
```
We next want to clear the graph to make sure we do not overlap the values on the graph as they update. This will also ensure that each frame is the designated field you just calculated.
```python
ax.clear()
```
We then have some form of calculation done with whatever function is taking place within the domain. In general, this can be done with another definined function if they want which also accounts for boundary conditions. In this case, the boundary conditions are set to be a Dirichlet boundary condition as zero giving this calculation type. 

After one finishes their calculations, we need to make our fields change to have a new past and present field from the ones calculated. I found that setting the future matrix to be set to an empty matrix of zeros also ensures this will not affect calculations when doing the next time step or frame.
```python
M11P = M11C
M11C = M11N
M22P = M22C
M22C = M22N
M11N = np.zeros([500,500])
M22N = np.zeros([500,500])
```
We then finish the function by graphing the designated field, setting the axis limit for the calculated field, and calculating the amount of time that has passed. The last portion is catered to the programmer to know how many time steps have been taken since the start of the program. Also, setting the axis limit is a must as the problem with this method is the graphing of the field will update the axises max and min to the current highest and lowest value. This will ultimately make ugly graph and begin to show the error found in the calculation. 
## **Animation Example**
![mp4](/Extra/2dAnimator/5X5,looking good.mp4)
https://github.com/ZWolfgram/ZWolfgram.github.io/assets/131365793/0fa71ea0-3253-4cfb-9c78-181821f400b8
## **References**

[1] https://stackoverflow.com/questions/70723644/surface-animation-and-saving-with-matplotlib 
