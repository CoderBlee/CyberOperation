# **Wireshark Network Traffic Analysis Project: DNS, TCP/UDP, HTTP/HTTPS**

---

## Introduction

In this comprehensive lab project, I used Wireshark and tcpdump tools to analyze multiple types of network traffic on the CyberOps Workstation VM. The goal of this project was to understand how common protocols such as DNS, FTP, TFTP, HTTP, and HTTPS work at the packet level, and to examine the security implications of each protocol in real-world use. Each section of the project is structured with commands I ran, what I observed, and detailed explanations of why each step matters.

---

## Part 1: DNS Capture and Analysis Using UDP

### Step 1: Record IP Configuration Information

I began by verifying the network configuration of my virtual machine. I needed to collect the MAC address, IP address, DNS server, and default gateway.

```bash
ifconfig
```

This displayed interface information. My interface was `enp0s3`, which showed my IP address and MAC address.

```bash
cat /etc/resolv.conf
```

This command listed the DNS servers configured for my system.

```bash
netstat -rn
```

This helped identify my default gateway IP address.

**Why This Matters:**
Understanding the IP configuration is foundational to analyzing packets. When filtering traffic later, I needed to know which IP was mine and which were remote.

**Security Note:**
DNS queries are often unencrypted. This means an attacker on the same network can potentially capture the sites I visit by sniffing DNS traffic.

---

### Step 2: Capture DNS Queries and Responses

I launched Wireshark using the command below:

```bash
wireshark &
```

Then I selected the interface `enp0s3` and clicked Start. I opened a web browser and navigated to `www.google.com`.

Once the page loaded, I stopped the capture.

### Step 3: Analyze the Packets

I applied the filter:

```bash
dns
```

I looked for a packet showing a standard DNS query (`A www.google.com`) and its response.

**Why This Matters:**
This helped me understand how DNS uses UDP (connectionless) to resolve domain names. DNS queries use port 53.

**Security Note:**
Since UDP does not guarantee delivery, applications must handle retries. Also, DNS is often exploited in DDoS attacks and data exfiltration.

---

## Part 2: Analyze TCP and UDP Captures (FTP and TFTP)

### Part 2.1: Analyze TCP Traffic via FTP

#### Step 1: Start Wireshark

```bash
wireshark &
```

I selected `enp0s3` and clicked Start.

#### Step 2: Start FTP Session

In another terminal, I connected to the CDC's FTP server:

```bash
ftp ftp.cdc.gov
```

Login credentials:

```
Name: anonymous
Password: [press Enter]
```

#### Step 3: Download File

```bash
ls
get Readme
quit
```

#### Step 4: Apply Wireshark Filter

In Wireshark:

```bash
tcp and ip.addr == 198.246.117.106
```

**Why This Matters:**
This part demonstrated TCP's reliability through SYN, ACK, FIN flags, and sequence numbers. I saw how a session is established and torn down.

**Security Note:**
FTP transmits data and credentials in plain text. Sensitive data can be stolen if used over insecure networks.

---

### Part 2.2: Analyze TFTP via UDP (Mininet)

#### Step 1: Start Mininet and Terminals

```bash
sudo lab.support.files/scripts/cyberops_topo.py
mininet> xterm H1 H2
```

#### Step 2: Start TFTP Server and Prepare Files

On H1:

```bash
/home/analyst/lab.support.files/scripts/start_tftpd.sh
echo "This file contains my tftp data." > /srv/tftp/my_tftp_data
```

On H2:

```bash
touch my_tftp_data
```

#### Step 3: Capture and Perform TFTP Transfer

On H1:

```bash
wireshark &
```

Select interface: H1-eth0

From H2:

```bash
tftp 10.0.0.11 -c get my_tftp_data
```

Filter in Wireshark:

```bash
tftp
```

**Why This Matters:**
This highlighted how UDP handles file transfers without session management. I saw how port numbers change dynamically.

**Security Note:**
TFTP is insecure by design. No authentication, no encryption. Only use it in trusted networks.

---

## Part 3: Analyze HTTP and HTTPS Traffic

### Part 3.1: HTTP Capture

#### Step 1: Start tcpdump to Capture HTTP

```bash
sudo tcpdump -i enp0s3 -s 0 -w httpdump.pcap
```

#### Step 2: Open Browser

Visited:

```http
http://www.altoromutual.com/login.jsp
```

Login credentials:

```
Username: Admin
Password: Admin
```

Stopped capture with CTRL+C.

#### Step 3: Analyze in Wireshark

Opened `httpdump.pcap` and filtered:

```bash
http
```

Looked for POST requests and decoded form data under `HTML Form URL Encoded`.

**Why This Matters:**
I clearly saw the login credentials sent in plain text. HTTP offers no confidentiality.

**Security Note:**
NEVER log into a website over HTTP. Use HTTPS to protect data.

---

### Part 3.2: HTTPS Capture

#### Step 1: Start tcpdump

```bash
sudo tcpdump -i enp0s3 -s 0 -w httpsdump.pcap
```

Visited:

```https
https://www.netacad.com
```

Logged in with dummy credentials.

Stopped capture with CTRL+C.

#### Step 2: Analyze in Wireshark

Filtered by:

```bash
tcp.port == 443
```

Looked for TLS Application Data.

**Why This Matters:**
Even though the packets were visible, the contents were encrypted and unreadable.

**Security Note:**
HTTPS provides encryption, authentication, and data integrity. However, not all HTTPS sites are safe (phishing sites also use HTTPS).

---

## Reflection Questions and Summary

### Why is HTTPS better than HTTP?

Because it encrypts all traffic and protects credentials from eavesdropping. It also authenticates servers via certificates.

### Is every HTTPS site safe?

No. Encryption doesn’t equal trustworthiness. Some attackers use HTTPS for phishing.

### How is TCP different from UDP?

TCP ensures reliable, ordered delivery with error checking. UDP is faster but doesn't guarantee delivery or order.

### Final Thoughts

This lab gave me valuable hands-on experience with real network traffic. I learned not only how protocols function but also how to examine their strengths and vulnerabilities. I now understand why encrypted, authenticated traffic is critical in today’s cyber landscape.
