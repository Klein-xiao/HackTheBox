# 🚩 Completed HackTheBox Machines

**Total Machines Rooted**: `3`  
**Current Goal**: Unlock 5 more machines! 🎯  

---

## 📋 Machines Overview

| Machine Name | Difficulty | Date       | Key Tools Used      | Tags                 | Status |
|--------------|------------|------------|---------------------|----------------------|--------|
| **Archetype**| 🟢 Easy    | 2025-03-19 | `nmap`, `smbclient` | #Windows #PrivEsc    | ✅      |
| **Oopsie**   | 🟢 Easy    | 2025-03-20 | `gobuster`, `curl`  | #Web #API #FileUpload| ✅      |
| **Three**    | 🟠 Medium  | 2025-03-20 | `sqlmap`, `john`    | #SQLi #HashCracking  | ✅      |

---

## 🎯 Highlights & Tips

### 🔍 Archetype
- **Key Steps**:  
  - SMB enumeration → Found sensitive credentials in `prod.dtsConfig`.  
  - MSSQL → Impacket's `mssqlclient.py` → WinPEAS for privilege escalation.  
- **Fun Fact**: First box with Windows Server! 🖥️  

### 🔍 Oopsie
- **Key Steps**:  
  - Cookie manipulation → Admin dashboard access → File upload RCE.  
  - **Pro Tip**: Always check `/uploads/` directories! 📂  

### 🔍 Three
- **Key Steps**:  
  - SQL injection → Dumped hashes → Cracked with John → Docker escape.  
  - **Lesson**: Never reuse passwords across services! 🔐  

---

## 📊 Progress Snapshot

```plaintext
🟢 Easy: 2/3    ████████ (66%)  
🟠 Medium: 1/3   ███ (33%)  
🔴 Hard: 0/3     (0%)

🚩 Next Targets:  
- [ ] Netmon  
- [ ] ScriptKiddie  
- [ ] Lame
