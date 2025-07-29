#  Reflection: SQL Injection Wireshark Analysis

This lab helped me understand how seemingly small oversights like unsanitized inputs can lead to full database breaches. It also trained me in:

- Analyzing HTTP GET requests at the packet level
- Recognizing common payloads like `1=1`, `UNION SELECT`, and `information_schema`
- Applying hash cracking techniques
- Thinking like an attacker to build better defenses

Next, I want to explore:
- Automating SQLi detection with IDS/IPS tools
- Writing SQLi detection rules for Suricata or Snort
