# 2d Animations MatLibPlot Python

**Overview**
---
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
## References
[1] https://stackoverflow.com/questions/70723644/surface-animation-and-saving-with-matplotlib 
