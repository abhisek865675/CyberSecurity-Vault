# Linux Auto Enumeration Scripts (OSCP Level)

## 1. LinPEAS (Linux Privilege Escalation Awesome Script)

Ye "King" hai. OSCP aur CTFs mein 90% log isi ka use karte hain.

- **Kyu use karein?** Ye script system ki har ek cheez scan karti hai (Kernel, Sudo, SUID, Cron, Config files, Passwords).
    
- **Sabse bada feature:** Iska **Color Coding**.
    
    - `RED/YELLOW`: 95% chance hai ki yahi PrivEsc vector hai.
        
    - `RED`: Isse check karna zaroori hai.
        
- **Command:**

    ```
     ./linpeas.sh
    ```


---

## 2. LinEnum

Ye ek classic script hai. Ye thodi purani hai par bahut clean output deti hai.

- **Kyu use karein?** Agar LinPEAS ka output bahut zyada "noisy" lag raha ho, toh LinEnum ek simple checklist bana kar deta hai.
    
- **Checks:** User privileges, SUID/GUID files, jobs, network connection, etc.

```
./LinEnum.sh -t -k password
```

---

## 3. Linux Exploit Suggester (LES)
Ye script specifically **Kernel Exploits** dhoondne ke liye banayi gayi hai.
*   **Kyu use karein?** Ye `uname -a` aur OS version ko read karke batati hai ki kaunsa CVE (jaise DirtyCOW, PwnKit) is machine par kaam karega.
*   **Kaise kaam karti hai?** Ye aapko direct exploit ka URL bhi de deti hai.
*   **Command:**
    ```bash
    ./les.sh
    ```

---

## 4. PSPY  (Process Monitoring)
Ye script nahi, balki ek tool hai jo bina root permissions ke background mein chal rahe processes ko monitor karta hai.
*   **Kyu use karein?** Agar koi **Cron Job** har 1 minute mein chal rahi hai par aapko `/etc/crontab` mein nahi dikh rahi, toh `pspy` use pakad lega.
*   **Feature:** Ye real-time mein dikhata hai ki root kaunsi commands chala raha hai.
*   **Command:** 
    ```bash
    ./pspy64
    ```

---

## 5. Linux Smart Enumeration (LSE)
Ye LinEnum jaisi hi hai par isme "Levels" hote hain.
*   **Kyu use karein?** Iska level system (`-l 1` or `-l 2`) aapko sirf wahi info dikhata hai jo important hai, taaki aap faltu data mein na phaso.

```
 ./lse.sh -l 1
```

---

