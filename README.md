# Suricata Setup and Configuration

## Table of Contents
1. [Setting Up Suricata](#setting-up-suricata)
   - Installing Suricata on a Virtual Machine
2. [Configuring Suricata](#configuring-suricata)
   - Modifying Suricata's main configuration file
3. [Running Suricata & Testing Traffic Capture](#running-suricata--testing-traffic-capture)
4. [Creating Custom Suricata Rules](#creating-custom-suricata-rules)
   - Example Rules


---

## 1. Setting Up Suricata  
### Installing Suricata on a Virtual Machine  

1. **Update your system:**  
   ```bash
   sudo apt update && sudo apt upgrade -y  # For Debian/Ubuntu
   sudo yum update -y  # For CentOS/RHEL
   ```
2. **Install dependencies:**  
   ```bash
   sudo apt install -y suricata jq libpcap-dev libnet1-dev  
   ```
3. **Verify installation:**  
   ```bash
   suricata --build-info
   ```


   <img width="651" alt="Screenshot 2025-02-24 at 1 47 08 PM" src="https://github.com/user-attachments/assets/ae46fef8-4940-402f-a82b-7f6a67ec629f" />

---

## 2. Configuring Suricata  
### Modify Suricata’s main configuration file  
Edit `/etc/suricata/suricata.yaml` to:  

- **Set up network interfaces for live traffic capture:**  
  ```yaml
  af-packet:
    interface: eth0
    cluster-id: 99
    cluster-type: cluster_flow
  ```
- **Enable logging in JSON and EVE format:**  
  ```yaml
  outputs:
    - eve-log:
        enabled: yes
        filename: /var/log/suricata/eve.json
        types:
          - alert
          - http
          - dns
          - ssh
  ```

---

## 3. Running Suricata & Testing Traffic Capture  
1. **Start Suricata in live mode:**  
   ```bash
   sudo suricata -c /etc/suricata/suricata.yaml -i eth0
   ```
2. **Generate network traffic to test logging:**  
   ```bash
   curl http://testmyids.com
   ping 8.8.8.8
   nmap -p 22 192.168.1.1
   ```
3. **Check logs:**  
   ```bash
   cat /var/log/suricata/eve.json | jq '.'
   ```

---

## 4. Creating Custom Suricata Rules  
### Example Rules  
Create a custom rule file:  
```bash
sudo nano /etc/suricata/rules/custom.rules
```
Add rules like:  

- **Detect SSH login attempts:**  
  ```
  alert tcp any any -> any 22 (msg:"SSH login attempt detected"; sid:100001;)
  ```
- **Detect HTTP requests to a suspicious domain:**  
  ```
  alert http any any -> any any (msg:"Suspicious HTTP Request"; content:"badwebsite.com"; sid:100002;)
  ```
- **Detect DNS queries to a specific domain:**  
  ```
  alert dns any any -> any any (msg:"Suspicious DNS Query"; content:"malicious.com"; sid:100003;)
  ```

Enable custom rules in `suricata.yaml`:  
```yaml
rule-files:
  - /etc/suricata/rules/custom.rules
```
Restart Suricata:  
```bash
sudo systemctl restart suricata
```
Generate matching traffic and check alerts!

---

## 5. Deliverables  
1. **Suricata Configuration Screenshot**  
   ```bash
   cat /etc/suricata/suricata.yaml | grep -v "^#" | grep -v "^$"
   ```
   Take a screenshot of your terminal showing the config file.

2. **Logs (ZIP File)**  
   ```bash
   sudo zip -r suricata_logs.zip /var/log/suricata/
   ```

3. **Report (3 Pages)**
   - **Introduction:** Overview of Suricata and its purpose.
   - **Setup:** Steps taken to install and configure Suricata.
   - **Findings:** Logs, detections, and triggered alerts.
   - **Issues & Solutions:** Problems encountered and fixes.

---

**Happy Monitoring! 🚀**
