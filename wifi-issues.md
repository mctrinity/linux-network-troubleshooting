
# Fixing WiFi Connectivity Issues in Ubuntu (MT7921 Adapter)

## **Issue**
After installing Ubuntu, WiFi was connected but had no internet access, despite being able to ping external servers like `8.8.8.8`. The issue was related to incorrect network settings and missing DNS configurations.

---

## **Resolution Steps**

### **Step 1: Restart Network Manager**
Run the following command to restart the network manager:
```bash
sudo systemctl restart NetworkManager
```
Then, check if the DNS server is correctly assigned:
```bash
nmcli device show wlp4s0 | grep IP4.DNS
```
If the output shows `192.168.1.1`, the system is using the router as a DNS server, but it may not be resolving domain names correctly.

---

### **Step 2: Manually Set a Public DNS**
To use Google DNS (`8.8.8.8`) and Cloudflare DNS (`1.1.1.1`), update the connection settings:
```bash
sudo nmcli con mod chocobo_5G ipv4.dns "8.8.8.8 1.1.1.1"
sudo nmcli con up chocobo_5G
```
Verify the changes:
```bash
nmcli device show wlp4s0 | grep IP4.DNS
```
Expected output:
```
IP4.DNS[1]: 8.8.8.8
IP4.DNS[2]: 1.1.1.1
```

---

### **Step 3: Manually Update `resolv.conf` (If Needed)**
If DNS settings do not persist, manually update the configuration file:
```bash
sudo nano /etc/resolv.conf
```
Replace the content with:
```
nameserver 8.8.8.8
nameserver 1.1.1.1
```
Save and exit (`Ctrl + X`, then `Y`, then `Enter`). Restart the network manager:
```bash
sudo systemctl restart NetworkManager
```
Test if domain name resolution works:
```bash
ping -c 4 google.com
```

---

### **Step 4: Flush DNS Cache**
If websites still do not load, clear the DNS cache:
```bash
sudo systemd-resolve --flush-caches
sudo systemctl restart systemd-resolved
```
Try pinging again:
```bash
ping -c 4 google.com
```

---

## **Final Verification**
Ensure WiFi is correctly configured:
```bash
nmcli device show wlp4s0 | grep IP4.DNS
cat /etc/resolv.conf
```
If DNS is correctly set and websites are accessible, the issue is resolved. 🎉

---

## **Conclusion**
This guide resolves WiFi connectivity issues on Ubuntu with the MediaTek MT7921 adapter by properly configuring the DNS settings and ensuring the correct network method is used.

