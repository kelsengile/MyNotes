[⬅ Back to README](../../README.md)

# Cybersecurity

Welcome! This is a self-paced course for learning Cybersecurity, the practice of protecting systems, networks, and data from unauthorized access, misuse, and attack, from both an offensive and a defensive perspective.

---

## What is Cybersecurity?

Cybersecurity lets you:
- Understand the CIA triad, threats, vulnerabilities, and risk
- Analyze networks and traffic to spot suspicious activity
- Harden operating systems and enforce access control
- Apply cryptography correctly, and recognize when it's misused
- Find and understand common web application vulnerabilities (the OWASP Top 10)
- Recognize malware, social engineering, and phishing techniques
- Practice offensive security: reconnaissance, scanning, and exploitation basics
- Practice defensive security: monitoring, detection, incident response, and forensics
- Manage identity, access, and zero-trust architectures
- Secure cloud environments and the software development lifecycle
- Work within security policies, compliance frameworks, and legal boundaries

## Table of Contents

**Getting Started**  
    1. **[What is Cybersecurity? Careers & Domains Overview](./[1]-What-is-Cybersecurity.md)**  
       1.1 What is Cybersecurity?  
       1.2 Why Cybersecurity Matters  
       1.3 Common Career Domains  
       1.4 How This Course is Organized  
    2. **[Setting Up a Security Lab (VMs, Sandboxes)](./[2]-Setting-Up-a-Security-Lab.md)**  
       2.1 Why You Need an Isolated Lab  
       2.2 Virtual Machines (VMs)  
       2.3 Sandboxes and Containers  
       2.4 Building a Basic Practice Lab  
    3. **[Legal & Ethical Considerations in Security Research](./[3]-Legal-and-Ethical-Considerations.md)**  
       3.1 Why Authorization is Everything  
       3.2 Authorized Testing: Scope and Rules of Engagement  
       3.3 Responsible Disclosure  
       3.4 Professional Ethics  

**Core Concepts**  
    4. **[The CIA Triad & Security Principles](./[4]-The-CIA-Triad-and-Security-Principles.md)**  
       4.1 Confidentiality  
       4.2 Integrity  
       4.3 Availability  
       4.4 Balancing the Triad  
       4.5 Supporting Principles: AAA and Non-Repudiation  
    5. **[Threats, Vulnerabilities & Risk](./[5]-Threats-Vulnerabilities-and-Risk.md)**  
       5.1 Defining the Terms  
       5.2 Types of Threat Actors  
       5.3 Assessing and Prioritizing Risk  
       5.4 Risk Treatment Strategies  
    6. **[Common Attack Vectors Overview](./[6]-Common-Attack-Vectors-Overview.md)**  
       6.1 What is an Attack Vector?  
       6.2 Human-Targeted Vectors  
       6.3 Network and System-Targeted Vectors  
       6.4 Application-Targeted Vectors  
       6.5 Supply Chain and Physical Vectors  

**Networking Fundamentals for Security**  
    7. **[Networking Basics for Security Professionals](./[7]-Networking-Basics-for-Security.md)**  
       7.1 Why Networking Matters for Security  
       7.2 The OSI Model  
       7.3 IP Addresses and Subnetting Basics  
       7.4 DNS, MAC Addresses, and Basic Network Topology  
    8. **[TCP/IP, Ports & Protocols](./[8]-TCP-IP-Ports-and-Protocols.md)**  
       8.1 The TCP/IP Model  
       8.2 TCP vs. UDP  
       8.3 Ports and Common Services  
       8.4 Common Application Protocols  
    9. **[Firewalls & Network Segmentation](./[9]-Firewalls-and-Network-Segmentation.md)**  
       9.1 What is a Firewall?  
       9.2 Types of Firewalls  
       9.3 Network Segmentation  
       9.4 Defense in Depth  
    10. **[Packet Analysis with Wireshark](./[10]-Packet-Analysis-with-Wireshark.md)**  
        10.1 What is Packet Analysis?  
        10.2 Capturing Traffic  
        10.3 Reading a Packet  
        10.4 Practical Use Cases  

**Operating System Security**  
    11. **[Linux Security Fundamentals](./[11]-Linux-Security-Fundamentals.md)**  
        11.1 Why Linux Matters in Security  
        11.2 Users, Groups, and Privilege  
        11.3 The Linux File System and Key Security Files  
        11.4 Essential Security Commands  
        11.5 Basic Hardening Practices  
    12. **[Windows Security Fundamentals](./[12]-Windows-Security-Fundamentals.md)**  
        12.1 Why Windows Matters in Security  
        12.2 Active Directory and Domains  
        12.3 User Account Control (UAC) and Privilege Levels  
        12.4 Windows Security Tools and Logs  
        12.5 Basic Hardening Practices  
    13. **[File Permissions & Access Control](./[13]-File-Permissions-and-Access-Control.md)**  
        13.1 Access Control Models  
        13.2 Linux File Permissions  
        13.3 Windows Access Control (NTFS Permissions)  
        13.4 The Principle of Least Privilege in Practice  
    14. **[System Hardening](./[14]-System-Hardening.md)**  
        14.1 What is Hardening?  
        14.2 Reducing Attack Surface  
        14.3 Patch Management  
        14.4 Security Baselines and Benchmarks  
        14.5 Endpoint Protection  

**Cryptography**  
    15. **[Cryptography Fundamentals (Symmetric & Asymmetric)](./[15]-Cryptography-Fundamentals.md)**  
        15.1 What is Cryptography For?  
        15.2 Symmetric Encryption  
        15.3 Asymmetric Encryption  
        15.4 Choosing the Right Approach  
        15.5 A Word of Caution  
    16. **[Hashing & Digital Signatures](./[16]-Hashing-and-Digital-Signatures.md)**  
        16.1 What is Hashing?  
        16.2 Common Uses of Hashing  
        16.3 Password Hashing Algorithms  
        16.4 Digital Signatures  
    17. **[TLS/SSL & Certificates](./[17]-TLS-SSL-and-Certificates.md)**  
        17.1 From SSL to TLS  
        17.2 The TLS Handshake  
        17.3 Digital Certificates and the Chain of Trust  
        17.4 Why Certificate Validation Matters  
        17.5 Practical Considerations  
    18. **[Common Cryptographic Pitfalls](./[18]-Common-Cryptographic-Pitfalls.md)**  
        18.1 Using Broken or Outdated Algorithms  
        18.2 Weak Key Management  
        18.3 Misusing Hashing  
        18.4 Implementation Mistakes  
        18.5 Practical Takeaways  

**Web Application Security**  
    19. **[OWASP Top 10 Overview](./[19]-OWASP-Top-10-Overview.md)**  
        19.1 What is OWASP?  
        19.2 The Categories (Representative List)  
        19.3 Why This List Matters  
        19.4 How to Use It as a Learning Tool  
    20. **[Injection Attacks (SQL Injection, Command Injection)](./[20]-Injection-Attacks.md)**  
        20.1 What is Injection?  
        20.2 SQL Injection (SQLi)  
        20.3 Command Injection  
        20.4 Other Injection Types  
        20.5 General Defense Principles  
    21. **[Cross-Site Scripting (XSS) & CSRF](./[21]-Cross-Site-Scripting-and-CSRF.md)**  
        21.1 What is XSS?  
        21.2 Types of XSS  
        21.3 Defending Against XSS  
        21.4 What is CSRF?  
        21.5 Defending Against CSRF  
    22. **[Authentication & Session Vulnerabilities](./[22]-Authentication-and-Session-Vulnerabilities.md)**  
        22.1 Weak Authentication Practices  
        22.2 What is a Session?  
        22.3 Session-Related Vulnerabilities  
        22.4 Defending Authentication and Sessions  
    23. **[API Security Basics](./[23]-API-Security-Basics.md)**  
        23.1 Why APIs Need Dedicated Attention  
        23.2 Broken Object Level Authorization (BOLA)  
        23.3 Authentication and Rate Limiting  
        23.4 Input Validation and Mass Assignment  
        23.5 API Security Checklist  

**Malware & Threats**  
    24. **[Introduction to Malware Types](./[24]-Introduction-to-Malware-Types.md)**  
        24.1 What is Malware?  
        24.2 Common Malware Categories  
        24.3 How Malware Spreads  
        24.4 How Malware is Detected  
        24.5 Command and Control (C2)  
    25. **[Social Engineering & Phishing](./[25]-Social-Engineering-and-Phishing.md)**  
        25.1 What is Social Engineering?  
        25.2 Common Social Engineering Techniques  
        25.3 Phishing and Its Variants  
        25.4 Recognizing Phishing Attempts  
        25.5 Organizational Defenses  
    26. **[Ransomware & Data Exfiltration](./[26]-Ransomware-and-Data-Exfiltration.md)**  
        26.1 How Ransomware Works  
        26.2 Double and Triple Extortion  
        26.3 Ransomware-as-a-Service (RaaS)  
        26.4 Data Exfiltration  
        26.5 Prevention and Response  

**Offensive Security**  
    27. **[Introduction to Penetration Testing](./[27]-Introduction-to-Penetration-Testing.md)**  
        27.1 What is Penetration Testing?  
        27.2 Types of Penetration Tests  
        27.3 The Penetration Testing Methodology  
        27.4 Red Team vs. Penetration Test vs. Vulnerability Assessment  
    28. **[Reconnaissance & Information Gathering](./[28]-Reconnaissance-and-Information-Gathering.md)**  
        28.1 What is Reconnaissance?  
        28.2 Passive Reconnaissance  
        28.3 Active Reconnaissance  
        28.4 Social Engineering Reconnaissance  
        28.5 Why This Matters for Defense  
    29. **[Scanning & Enumeration](./[29]-Scanning-and-Enumeration.md)**  
        29.1 From Recon to Scanning  
        29.2 Host Discovery and Port Scanning  
        29.3 Service and Version Enumeration  
        29.4 Enumerating Specific Services  
        29.5 Why Enumeration is Critical  
    30. **[Exploitation Basics](./[30]-Exploitation-Basics.md)**  
        30.1 What is Exploitation?  
        30.2 Vulnerabilities vs. Exploits  
        30.3 Categories of Exploitation  
        30.4 Exploitation Frameworks  
        30.5 Payloads and Post-Exploitation Access  
    31. **[Privilege Escalation Basics](./[31]-Privilege-Escalation-Basics.md)**  
        31.1 What is Privilege Escalation?  
        31.2 Vertical vs. Horizontal Escalation  
        31.3 Common Privilege Escalation Techniques  
        31.4 Lateral Movement and Persistence  
        31.5 Defensive Takeaways  

**Defensive Security**  
    32. **[Security Monitoring & SIEM Basics](./[32]-Security-Monitoring-and-SIEM-Basics.md)**  
        32.1 Why Monitoring Matters  
        32.2 Logs: The Foundation of Monitoring  
        32.3 What is a SIEM?  
        32.4 The Security Operations Center (SOC)  
        32.5 Alert Fatigue  
    33. **[Intrusion Detection & Prevention Systems](./[33]-Intrusion-Detection-and-Prevention-Systems.md)**  
        33.1 IDS vs. IPS  
        33.2 Network-Based vs. Host-Based  
        33.3 Detection Methods  
        33.4 Where IDS/IPS Fits in the Bigger Picture  
        33.5 Common Evasion Considerations  
    34. **[Incident Response Fundamentals](./[34]-Incident-Response-Fundamentals.md)**  
        34.1 What is Incident Response?  
        34.2 The Incident Response Lifecycle  
        34.3 The Incident Response Team  
        34.4 Communication During an Incident  
        34.5 Why Preparation Matters Most  
    35. **[Digital Forensics Basics](./[35]-Digital-Forensics-Basics.md)**  
        35.1 What is Digital Forensics?  
        35.2 The Chain of Custody  
        35.3 Evidence Preservation Principles  
        35.4 Types of Digital Forensics  
        35.5 Forensics in the Incident Response Process  

**Identity & Access Management**  
    36. **[Authentication Methods (MFA, SSO)](./[36]-Authentication-Methods.md)**  
        36.1 Authentication Factors  
        36.2 Multi-Factor Authentication (MFA)  
        36.3 Single Sign-On (SSO)  
        36.4 Password Managers  
    37. **[Identity & Access Management Concepts](./[37]-Identity-and-Access-Management-Concepts.md)**  
        37.1 What is IAM?  
        37.2 The Identity Lifecycle  
        37.3 Privileged Access Management (PAM)  
        37.4 The Principle of Least Privilege, Revisited  
        37.5 Federated Identity  
    38. **[Zero Trust Architecture Basics](./[38]-Zero-Trust-Architecture-Basics.md)**  
        38.1 The Traditional "Castle and Moat" Model  
        38.2 The Zero Trust Principle  
        38.3 Core Components of Zero Trust  
        38.4 Zero Trust in Practice  
        38.5 Why Zero Trust Has Gained Traction  

**Cloud & Application Security**  
    39. **[Cloud Security Fundamentals](./[39]-Cloud-Security-Fundamentals.md)**  
        39.1 Cloud Service Models  
        39.2 The Shared Responsibility Model  
        39.3 Common Cloud Misconfigurations  
        39.4 Cloud-Specific Security Tools and Concepts  
        39.5 Multi-Tenancy and Isolation  
    40. **[Secure Software Development Lifecycle (SSDLC)](./[40]-Secure-Software-Development-Lifecycle.md)**  
        40.1 Why "Shift Left"?  
        40.2 The SSDLC Phases  
        40.3 Static and Dynamic Analysis  
        40.4 CI/CD Pipeline Security  
        40.5 Security Champions and Culture  
    41. **[Secure Coding Practices](./[41]-Secure-Coding-Practices.md)**  
        41.1 Input Validation  
        41.2 Output Encoding  
        41.3 Secure Error Handling  
        41.4 Managing Secrets and Dependencies  
        41.5 Code Review and Peer Review  
        41.6 Defense in Depth at the Code Level  

**Tools of the Trade**  
    42. **[Vulnerability Scanners (Nmap, Nessus)](./[42]-Vulnerability-Scanners.md)**  
        42.1 What is a Vulnerability Scanner?  
        42.2 Nmap  
        42.3 Nessus and Dedicated Vulnerability Scanners  
        42.4 Scanning Considerations  
        42.5 Scanning as a Continuous Process  
    43. **[Security Testing Frameworks (Metasploit, Burp Suite)](./[43]-Security-Testing-Frameworks.md)**  
        43.1 Metasploit Framework  
        43.2 Burp Suite  
        43.3 Other Notable Tools  
        43.4 Using These Tools Responsibly  
    44. **[Scripting for Security Automation](./[44]-Scripting-for-Security-Automation.md)**  
        44.1 Why Security Professionals Learn to Script  
        44.2 Python in Security  
        44.3 Bash Scripting  
        44.4 PowerShell  
        44.5 Getting Started  

**Governance & Compliance**  
    45. **[Security Policies & Frameworks (NIST, ISO 27001)](./[45]-Security-Policies-and-Frameworks.md)**  
        45.1 What is a Security Policy?  
        45.2 The NIST Cybersecurity Framework (CSF)  
        45.3 ISO/IEC 27001  
        45.4 Other Notable Frameworks  
        45.5 Why Frameworks Matter  
    46. **[Compliance Standards (GDPR, PCI-DSS, HIPAA Overview)](./[46]-Compliance-Standards.md)**  
        46.1 Compliance vs. Security  
        46.2 GDPR (General Data Protection Regulation)  
        46.3 PCI-DSS (Payment Card Industry Data Security Standard)  
        46.4 HIPAA (Health Insurance Portability and Accountability Act)  
        46.5 Common Threads Across Compliance Standards  

**Best Practices**  
    47. **[Building a Security Mindset & Threat Modeling](./[47]-Building-a-Security-Mindset-and-Threat-Modeling.md)**  
        47.1 What is a "Security Mindset"?  
        47.2 What is Threat Modeling?  
        47.3 Common Threat Modeling Approaches  
        47.4 Thinking Like an Attacker (Constructively)  
        47.5 Making Threat Modeling a Habit  
    48. **[Staying Current: CVEs, Patching & Threat Intelligence](./[48]-Staying-Current-CVEs-Patching-and-Threat-Intelligence.md)**  
        48.1 Why Cybersecurity Requires Continuous Learning  
        48.2 CVEs and Vulnerability Databases  
        48.3 Patch Management in Practice  
        48.4 Threat Intelligence  
        48.5 Building Sustainable Learning Habits  
    49. **[Cybersecurity Career Paths & Certifications](./[49]-Cybersecurity-Career-Paths-and-Certifications.md)**
        49.1 Common Entry Points  
        49.2 Specialization Paths  
        49.3 Certifications  
        49.4 Building Practical Experience  
        49.5 Where to Go From Here  