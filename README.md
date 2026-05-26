# Incident Response Lab: IR01 - Phishing Triage (Ticket #IR-2026-0441)

## 📝 Executive Summary
During this incident response exercise, I acted as a SOC Analyst to triage a user-reported suspicious email from the LucentGrid ecosystem. Upon evaluating raw transport headers, querying reputation databases, and sandboxing the embedded tracking links, the event was officially classified as a **True Positive Phishing** campaign distributing both **Credential Harvesting and Malware**. 

The targeted corporate user account within the enterprise ecosystem was successfully compromised. The investigation mapped out highly targeted spear-phishing parameters, infrastructure tracking tokens, authentication alignment validation, and immediate identity containment protocols.

---

## 🎯 Lab Objectives
*   **Analyze** raw email headers for spoofing indicators (SPF, DKIM, DMARC alignment).
*   **Deconstruct** malicious URLs inside a safe sandbox environment to analyze page content and telemetry parameters.
*   **Query Threat Intelligence Databases** (VirusTotal, AbuseIPDB) to extract reputation metrics and telemetry scores.
*   **Document** actionable defensive recommendations and containment strategies.

---

## 🛠️ Tools Utilized
*   **Email Header Analyzer & Gateway Parser** – Used for structural header extraction and delivery path verification.
*   **VirusTotal / AbuseIPDB Integrated Matrix** – Deployed to fetch real-time threat scores, detection ratios, and ISP routing telemetry.
*   **URL Inspector / Safe Sandbox** – Deployed to safely render malicious web forms and parse URL tracking parameters.
*   **Terminal (WHOIS / Dig / NSLookup)** – Deployed for tactical command-line infrastructure footprinting.

---

## 🔍 Technical Analysis & Findings

### 1. Granular Email Header & Sender Verification
An inspection of the raw email headers from the inbound mail gateway (`://apexcorp.com`) exposed extensive domain spoofing and cryptographic verification failures:
*   **Target Enterprise Environment**: ApexCorp (`apexcorp.com`)
*   **Target Victim Address**: `sarah.miller@apexcorp.com`
*   **Display Name Used**: `Microsoft IT Security`
*   **Envelope Sender / From**: `microsoft-mailer.ru`
*   **Reply-To Header**: `no-reply@micros0ft-mailer.ru`
*   **Return-Path**: `microsoft-mailer.ru`
*   **Inbound Gateway Receiver**: `://apexcorp.com with ESMTP id e8si2948302qkj.14`
*   **X-Mailer / Generation Software**: `PHPMailer 6.8.0` 
*   **X-Spam-Status**: `Yes, score=8.4`
*   **Authentication Results**:
    *   **SPF Result**: `fail` | The sending domain `microsoft-mailer.ru` does not designate `185.220.101.47` as a permitted sender.
    *   **DKIM Result**: `fail` | Signature did not verify for `header.i=@microsoft.com`.
    *   **DMARC Result**: `fail` | Configured policy (`p=REJECT`), resulting in an `action=quarantine` intervention on the mail gateway for failing authentication alignment with `header.from=microsoft.com`.

### 2. Threat Intelligence & Reputation Metrics
Querying external threat intelligence databases returned highly critical scores confirming active adversarial infrastructure:
*   **VirusTotal (Domain: `mta.micros0ft-mailer.ru`)**:
    *   **Detection Ratio**: 🔴 **47 / 92 engines flagged** as malicious.
    *   **Categories**: Phishing, Malware Distribution.
    *   **First Seen**: 2026-05-05 (3 days prior to triage).
*   **AbuseIPDB (IP: `185.220.101.47`)**:
    *   **Abuse Score**: 🔴 **98 / 100**
    *   **Volume**: 1,247 reports within the last 90 days.
    *   **Geographic Country**: Netherlands (NL)
    *   **ISP**: Frantech Solutions
    *   **Usage Type**: Phishing Infrastructure / Bulletproof Hosting

### 3. URL Sandbox & Parameter Deconstruction
The embedded hyperlink (`https://micros0ft-security[.]com/verify`) was analyzed using a safe web sandbox environment:
*   **Page Content Summary**: The landing page renders a fake **Microsoft 365 login form**. It is designed to capture corporate usernames, passwords, and Multi-Factor Authentication (MFA) tokens. Upon submission, it redirects victims to the legitimate Microsoft site to minimize suspicion.
*   **Targeted URL Parameters**: `?user=sarah.miller&token=8f6hJ2kLmNpQ`
*   **Analysis of Parameters**: The URL was heavily customized as a spear-phishing campaign. The `user` string pre-populated Sarah Miller's email on the form, while the unique `token` served as an adversarial tracking ID to confirm which specific employee clicked the link.
*   **Sandbox Verdict**: 🔴 **MALICIOUS – Credential Harvesting**

### 4. Infrastructure Footprinting (WHOIS Analysis)
A `whois` lookup against the attacker infrastructure via the tactical console revealed a volatile setup tailored for evasion:
*   **Target Domain**: `micros0ft-security[.]com` *(Defanged)*
*   **Registrar**: Namecheap Inc.
*   **Registrant Country**: RU (Russian Federation)
*   **Name Servers**: `ns1.bullethost-anon[.]net` & `ns2.bullethost-anon[.]net` *(Bulletproof hosting used to resist standard security takedowns)*

### 5. Classification Summary & Artifact Inspection
*   **Final Verification Verdict**: Phishing Platform Distribution (True Positive)
*   **Attack Classification**: Credential Harvesting + Malware Vector
*   **Attachment Type**: `Word Macro (.docm)`
*   **Threat Vector**: Executes malicious macro scripts upon user execution to pull down secondary malware components.

---

## 🛡️ Remediation & Defensive Actions

### Immediate Containment Steps
1.  **Identity Isolation & Credential Revocation**: 
    *   **Target Victim**: **Sarah Miller**'s corporate account (`sarah.miller@apexcorp.com`) is confirmed compromised.
    *   **Action**: Force an immediate password reset, revoke all active OAuth/user sessions, and temporarily freeze token-issuance to halt active session hijack abuse.
2.  **Incident Reporting**: Submit the malicious phishing domain (`mta.micros0ft-mailer.ru`) and the hosting IP address (`185.220.101.47`) to Microsoft's official phishing reporting services and Frantech Solutions' abuse contact channel.
3.  **Network Perimeter Blocks**: Blacklist the sending IP address (`185.220.101.47`) and the defanged domains (`micros0ft-mailer[.]ru` & `micros0ft-security[.]com`) at the perimeter firewall, web proxy, and email gateway levels.
4.  **DNS Sinkholing**: Block outbound lookups to the bulletproof name servers (`bullethost-anon[.]net`) across internal domain controllers to stop any secondary malware callback chains.
5.  **Tenant Mail Purge**: Run an immediate search-and-destroy sequence across the mail gateway using the unique envelope headers to purge duplicate instances of this message from other employee mailboxes.

---


## 📷 Case Evidence Artifacts
Below are the raw image logs extracted during the tactical triage investigation. *Click any link to view the high-resolution upload directly:*

*   [View Artifact 01 - Email Triage](01_email_triage.png) | [View Artifact 02 - Header Analysis](02_header_analyzer.png) | [View Artifact 03 - Full Headers](03_full_headers.png) | [View Artifact 04 - Final Report](04_final_report.png)  


---

## 🎓 Credits & Acknowledgments
*   **Lab Content**: The design, scenario, and threat infrastructure variables of this triage lab were created and provided by my instructor, **Aaron Fitzpatrick**, for training and academic purposes. 
*   **Analysis**: The technical documentation, triage paths, image telemetry curation, and incident mitigation conclusions presented in this repository represent my own independent work and lab completion findings.
