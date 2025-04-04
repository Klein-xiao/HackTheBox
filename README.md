# HackTheBox Penetration Testing Repository 🔒

A repository dedicated to documenting penetration testing methodologies, exploits, and walkthroughs for HackTheBox machines and challenges.

---

## 📂 Repository Structure

```bash
.
├── Archetype.md        # Penetration report for "Archetype" machine
├── Bucket.md           # Full walkthrough for "Bucket" challenge 
├── Oopsie.md           # Vulnerability exploit analysis for "Oopsie"
├── Three.md            # Multi-stage attack documentation for "Three" machine
└── ...                 # Ongoing updates for other challenges
```

## 🔍 Penetration Testing Documentation

### 1. Bucket.md  
**Target:** AWS S3 Bucket Misconfiguration  
**Key Steps:**  
- S3 Bucket Hijacking via Subdomain Takeover  
- Reverse Shell Exploitation (Python HTTP Server)  
- Privilege Escalation via Cron Job Manipulation  

### 2. Archetype.md  
**Target:** Windows Domain Compromise  
**Key Steps:**  
- SMB Vulnerability Exploitation (CVE-2020-0796)  
- Credential Dumping with Mimikatz  
- Lateral Movement via Pass-the-Hash  

### 3. Oopsie.md  
**Target:** Web Application Chain Exploit  
**Key Steps:**  
- Insecure Direct Object Reference (IDOR) Exploit  
- JWT Token Forgery for Admin Privilege Escalation  
- File Upload to RCE (Remote Code Execution)  

### 4. Three.md  
**Target:** Multi-Layered Network Attack  
**Key Steps:**  
- SNMP Enumeration for Network Mapping  
- FTP Credential Brute-forcing  
- Docker Breakout via Misconfigured Socket  

## 🛠️ Tools & Techniques Highlighted

| Tool/Technique       | Use Case                                      |
|----------------------|----------------------------------------------|
| Nmap                | Port Scanning & Service Enumeration         |
| Metasploit          | Exploit Development & Payload Delivery      |
| Burp Suite         | Web Proxy for Traffic Manipulation          |
| John the Ripper     | Password Cracking                           |
| LinPEAS/WinPEAS     | Privilege Escalation Automation            |

---

## HackTheBox
> "To beat the hacker, you need to think like one."
