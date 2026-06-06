

## By schedule task :-

* Schedule task is like cronjobs in linux.
* When the system is booting or if we have a schedule task we set then we for the schedule task do the task when for ex. any programme execute at every-time we boot the system.
* Services are different for schedule task. Services are running until the system are not going to shut down.

### RDP
```
xfreerdp /u:user /p:Password /cert:ignore /v:[IP] /smart-sizing
```

> `/smart-sizing` , `/dynamic-resolution` $\rightarrow$ uses to increase the size of the rdp machine we have logged in.

---

## 1st Enumeration :-

### Find Schedule task :-
`schtasks /query /fo LIST /v | findstr /I taskname`

```
schtasks  /fo   LIST | findstr /I taskname | findstr /I /v microsoft
```

   `schtasks:- For finding schedule tasks
   `/fo :- For format LIST`
   `findstr :- just like find in linux`
   `/I :- ignore cases`
   `taskname :- something`
   `/v :- inverse
   
- Find task name
    
- Then want to see what is inside
    

`schtasks /query /tn SaveCred /fo LIST /v`

`` /tn --> taskname
    
`` /v --> verbose
    

 ``
 ```
 runas /SaveCred /user:admin cmd.exe
 ```

`runas --> like sudo`
    
 `/user:admin --> which user`
    
`cmd.exe --> what to run
    

## 2nd Enumeration

Through suspicious file, we found `cleanup.ps1` (A Powershell file).

- We have to check if we have any write privilege or not.
    
- It has 3 method :-
    

### 1st Method: icacls

```
icacls cleanup.ps1
```

It has permission in 2 parts: 1st for file or 2nd for folder.

We have `(M)` `(F)` `(I)`

` **M** --> modify --> It can read, write, delete file.
    
` F --> full access --> In full access we can do all things like modify but we can change the permissions of the file.
    
`I --> inherit
    

### 2nd Method: Accesschk :-

We have to transfer it then:

```
accesschk.exe cleanup.ps1
```

```
>accesschk.exe -accepteula -q c:/devtools/cleanup.ps1 /v
```

`-u --> user --> for my user only
    
` -v --> for verbose
    

### 3rd Method: Exploitation :-

`echo hello > cleanup.ps1`

If we can write in this file, we have the priv.

`echo echo ^> C:\users\user\test.txt^" > cleanup.ps1`

- `^` $\rightarrow$ character escaping.
    

If the command execute, we confirm that we have exploit this. We put reverseshell of powershell.
Listen through netcat .
**Boom we are nt authority\system** .


---


