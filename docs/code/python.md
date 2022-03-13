---
layout: default
title: Python
parent: Code
nav_order: 4
---

# Python
This file is here to serve as a place to put python code

---

#### Sometimes its helpful to list out your algorithm(s) in a high level language and
#### then port it over to a HDL or HLS language. 

---

### Mandelbrot Plot 
* Graph the famous mandelbrot pattern
* **NOT MY CODE --- NOT MY CODE --- NOT MY CODE**

```python
import matplotlib.pyplot as plt
import numpy as np

def get_iter(c:complex, thresh:int =4, max_steps:int =25) -> int:
    # Z_(n) = (Z_(n-1))^2 + c
    # Z_(0) = c
    z=c
    i=1
    while i<max_steps and (z*z.conjugate()).real<thresh:
        z=z*z +c
        i+=1
    return i

def plotter(n, thresh, max_steps=25):
    mx = 2.48 / (n-1)
    my = 2.26 / (n-1)
    mapper = lambda x,y: (mx*x - 2, my*y - 1.13)
    img=np.full((n,n), 255)
    for x in range(n):
        for y in range(n):
            it = get_iter(complex(*mapper(x,y)), thresh=thresh, max_steps=max_steps)
            img[y][x] = 255 - it
    return img

n=3000
# Width and Height

img = plotter(n, thresh=4, max_steps=500)
plt.imshow(img, cmap="magma")
plt.axis("off")
plt.show()
```

---

### GCF Algorithm | Euclid's
* Non-recursive implementation

```python
x = 100
y = 15

#expanded
n = x%y
while(n != 0):
    x = y
    y = n
    n = x%y
print(y)
```

---

### GCF Algorithm Basic 
* Non-recursive implementation

```python
import time
startTime = time.time()

a = 100
b = 15

if (a > b):  #Variable Y will always be the smaller number
    x = a
    y = b
else:
    x = b
    y = a
    
j = 1

while (j != (y + 1)):
    j+=1
    if((x % j == 0) and (y % j == 0)):
        gcf = j

print(gcf)

execTime = (time.time() - startTime)
print('Time taken to run ' + str(execTime))

#subtraction method
#works with most cases, but not with 100 and 15 | or 2000 and 300 etc...

#mod method
#works fine
```
