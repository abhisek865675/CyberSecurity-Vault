#  Linux Networking & Firewalls (Enumeration & Bypass) 🛡️

Once you have a shell, you often need to check if the target is firewalled (iptables/nftables) or if you can pivot through it to reach other internal machines.

---

## 1. Firewall Enumeration (The "What's Blocking Me?" Phase)
Before running aggressive scans, check the local firewall rules.

* **Check `iptables` rules:**
```bash
  sudo iptables -L -n -v
  # -L: List rules
  # -n: Numeric (don't resolve DNS, faster)
  # -v: Verbose (see packet/byte counts)
- **Check `nftables` (Modern successor to iptables):**
  sudo nft list ruleset
```

    
- **Check UFW (Uncomplicated Firewall - common on Ubuntu):**
    
    
    ```bash
    sudo ufw status
    ```
    

## 2. Pivot & Port Forwarding (The "Bypass" Phase)

If you have compromised one machine but cannot reach another internal IP, you must perform **Pivoting** (Port Forwarding).

### A. Local Port Forwarding (SSH Tunneling)

If you have SSH access to the compromised machine, tunnel traffic from your Kali machine to the target's internal service.

- **Kali:**
    
    
    
    ```bash
    ssh -L 8080:127.0.0.1:80 user@<COMPROMISED_IP>
    ```
    
    _(Now, browsing `http://127.0.0.1:8080` on Kali connects to the target's port 80)_
    

### B. SSH Dynamic Forwarding (SOCKS Proxy)

Use this if you want to scan an entire internal network through the compromised machine.

- **Kali:**
    
    
    ```bash
    ssh -D 9050 user@<COMPROMISED_IP>
    ```
    
- **Usage:** Configure `proxychains` in `/etc/proxychains.conf` to use `127.0.0.1:9050`, then run tools:
    
    
    ```bash
    proxychains nmap -sT -Pn -p80,443 <INTERNAL_TARGET_IP>
    ```
    

### C. Socat Port Forwarding (No SSH required)

If you don't have SSH but have a shell:

- **Forwarding:**
    
    
    ```bash
    # Listen on Kali, forward to internal server
    socat TCP-LISTEN:8080,fork TCP:<INTERNAL_TARGET_IP>:80
    ```
    

## 3. Network Traffic Analysis

If you suspect you are being monitored, check your own connections:

- **Current Connections:**
    
    
    ```bash
    ss -tulpn
    # -t: TCP, -u: UDP, -l: Listening, -p: Process, -n: Numeric
    ```
    
- **Routing Table:** Check how the server routes its traffic.
    
    
    ```bash
    ip route
    ```
    

## 4. Key Security Takeaways

- **Egress Filtering:** Always check if the server can connect _out_ to the internet (e.g., `ping 8.8.8.8`). If outgoing traffic is blocked, your reverse shell payloads will fail. Use `tcpdump` on your Kali to see if the connection reaches you.
    
- **Bypass via Internal Ports:** Sometimes a firewall blocks external ports (like 80/443) but allows internal traffic on other ports. Always check if you can communicate between the compromised machine and the DB or internal apps on high-numbered ports.


---
