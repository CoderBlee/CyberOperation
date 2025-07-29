#  SQL Injection Attack Analysis using Wireshark

This project is a hands-on forensic investigation into a **SQL Injection attack** using Wireshark. Using a captured PCAP file, I traced an attack step-by-step — from injection testing to database exfiltration — and learned how dangerous unsanitized inputs can be.

---

##  Lab Overview

**Goal:** Analyze an SQL injection attack and understand how malicious actors extract data from vulnerable web apps.

**Tools Used:**
-  Wireshark (packet capture analysis)
-  CyberOps Workstation VM
-  CrackStation (for password hash decoding)
-  PCAP file containing SQLi traffic

---

##  Steps Performed

###  Step 1: Open Wireshark & Load PCAP
- Loaded the capture file `SQL_Lab.pcap` in Wireshark.
- Identified the attacker and target IPs: `10.0.2.4` (attacker) and `10.0.2.15` (victim).

###  Step 2: Initial SQL Injection
- Found payload `1=1` in GET request.
- Application responded successfully, confirming vulnerability.

###  Step 3: Schema Discovery
- Used payload: `1’ or 1=1 union select database(), user()#`
- Found database: `dvwa`, user: `root@localhost`

###  Step 4: Version Information
- Used payload: `1’ or 1=1 union select null, version()#`
- Found DB version (see report).

###  Step 5: Table Enumeration
- Used: `1’or 1=1 union select null, table_name from information_schema.tables#`
- Enumerated all tables in the database.

###  Step 6: Extracting User Credentials
- Used: `1’or 1=1 union select user, password from users#`
- Captured usernames and password hashes.

###  Step 7: Cracking Password Hash
- Hash: `8d3533d75ae2c3966d7e0d4fcc69216b`
- Plaintext: `password` (cracked using CrackStation)

---

##  Key Learnings

- SQL injection attacks can happen in **just a few HTTP requests**.
- Wireshark is invaluable for **post-attack forensic analysis**.
- Even "simple" injection queries like `1=1` can be catastrophic when inputs aren’t sanitized.
- Understanding how attackers think improves your ability to defend.

---

##  How to Prevent SQL Injection

You can check out [`prevention.md`](./prevention.md) for a deeper dive, but here are the highlights:

- ✅ Use **prepared statements / parameterized queries**
- ✅ Perform **input validation** and **escaping**
- ✅ Use **least privilege principles** for DB accounts
- ✅ Employ **Web Application Firewalls (WAFs)**
- ✅ Regularly scan code for vulnerabilities (e.g., OWASP ZAP)

---


##  Files Included

| File                     | Description                                      |
|--------------------------|--------------------------------------------------|
| `SQL_Lab.pcap`           | Packet capture of full SQL injection attack     |
| `analysis_report`     | Detailed breakdown of each step in the attack   |
| `hash_crack_result`  | Result of cracking password hash                |
| `reflection.md`          | Personal notes and key takeaways                |
| `prevention.md`          | Defense strategies to prevent SQLi              |

---

## Disclaimer

**This project is for educational purposes only.**  
Please do not use any of these techniques against systems you do not own or have permission to test.

---

## Contributions

Pull requests are welcome! If you have ideas to expand the lab or want to improve the report, feel free to open a PR or Issue.

---

## License

MIT License — use, share, or fork responsibly.

---
