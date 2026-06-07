## What is weak file permissions ?

In this we discuss about 3 files *`/etc/shadow` , *`/etc/passwd` ,` /etc/souders`*

>/etc/shadow- In which all the passwords store of all systems
>/etc/passwd- In which all users of linux  stored
>/etc/souders- In which we can configured all users permissions

`/etc/shadow`

>if we have only privillage to read `/etc/shadow`
then we can read root users password and crack it through `john , hashcat` 
in linux passwords stores in SHA-512crypt format .

***for john* :-**

```
john hash.txt --wordlists=/usr/share/wordlists/rockyou.txt --format=SHA512crypt
```

***for hashcat* :-**

```
hashcat -a 0 -m 100 hash.txt /usr/share/wordlists/rockyou.txt
```

***2nd method :- ***

 **If we have permission to write on it . we can use 2 methods :-**
 
 ***1st method :-***
 
 >we can change the password of root user and replace by own custom password which is in SHA512crypt format
 >for generating password in SHA512crypt we can use openssl .
 
 ```
 openssl passwd -6 hello
 ```
 
 *passwd :- Denotes that we have to create a password*
 *-6 for SHA512crypt format*
 *hello is the passwd* 

>we can now write it in /etc/shadow .Then we can login as root by our password hello.

```
su root
Enter password hello
```

we are root !

`/etc/passwd`

**By default all uses can read /etc/passwd file . But if we have permission to write on it we have methods :-**

***1st method :-***

>we can remove x from root users entry . Then we can login as root without password.

***2nd method :-***

>we can change the x value to our custom password hash . so that when we write su root linux 1st go to /etc/passwd file if the value was x then linux go to /etc/passwd file if we write custom made hash in the exchange of x then linux will directly login as we set the custom password .

***3rd method:-***

>we can make a new user and gave  our own password and replace uid , gid & homedirectory as like as root then when we type su newuser and enter the password we can login as root .

`/etc/souders`

In this file we can set permission to a user what the user can access or not .
#### **1st method: If we have Write Permission**

If we have permission to write to this file, we can grant full **Root** access to any normal user. We just need to add the following line at the bottom of the file:

```
attacker-user ALL=(ALL:ALL) NOPASSWD:ALL
```


- **attacker-user:** Your username.
    
- **NOPASSWD:** This means you won't need a password to become root.
    
- Now, just type `sudo su` in the terminal and you are **root**!

- ` ALL=(ALL:ALL)ALL`

- 1st `ALL` means the rules applies with all hosts(useful in multi server setups).

-  `(ALL:ALL)` These `ALL` means run as any user and any group.

- Last `ALL` means can run all commands .


#### **2nd method: Exploiting Specific Binary (GTFOBins)**

Sometimes, an admin allows a user to run only one specific command with root privileges without a password.

- **Example Rule:** Inside `/etc/sudoers` it says: `user1 ALL=(root) NOPASSWD: /usr/bin/find`
    
- **Exploit:** Since the `find` command has an option to execute a shell, we can exploit it to get a root shell: `sudo find . -exec /bin/sh \; -quit`
    

#### **3rd method: Permission Misconfiguration**

By default, the permissions for `/etc/sudoers` should be **0440** (only root can read/write). If an admin sets it to **777** or **666**, the system is easily compromised because any user can edit the file and add themselves to the Admin group.

---
