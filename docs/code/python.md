---
layout: default
title: Python
parent: Code
nav_order: 4
---

# Python
This file is here to serve as a place to put python code

---

#### Somestimes its helpful to list out your algorithm(s) in a high level language and
#### then port it over to a HDL or HLS language. 

---

### Euclids Algorithm Basic 
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
