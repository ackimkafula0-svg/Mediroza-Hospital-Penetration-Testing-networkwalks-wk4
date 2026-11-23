# 🛡️ Mediroza General Hospital — Web Application Penetration Testing

### 🔐 Networkwalks Cybersecurity Training | Black-Box Penetration Testing Project

---

## 📖 Project Overview

This project documents a black-box penetration testing assessment conducted against the **Mediroza General Hospital educational testing environment** as part of my cybersecurity training with **Networkwalks**.

The primary objective was to identify and assess web application security vulnerabilities, demonstrate their potential impact, and provide practical remediation recommendations.

The assessment focused on authentication weaknesses, SQL injection, confidential document exposure, insecure database backup storage, and PDF password security.

The project was conducted in an authorized training environment and followed the scope defined in the assessment brief.

---

## 🎯 Project Objectives

The assessment consisted of four major milestones:

| 🎯 Milestone | 📋 Objective | ✅ Status |
| :--- | :--- | :--- |
| **M1 — Initial Access** | Identify an authentication vulnerability and retrieve three encrypted laboratory reports | Completed |
| **M2 — PDF Security** | Analyze the encrypted reports and assess their password protection | Completed |
| **M3 — Information Disclosure** | Identify exposed employee salary and shareholder information | Completed |
| **M4 — Reporting** | Produce a professional penetration testing report with findings, evidence, and remediation recommendations | Completed |

---

## 🖥️ Testing Environment

| ⚙️ Component | 📋 Details |
| :--- | :--- |
| **🐧 Operating System** | Ubuntu Linux |
| **🌐 Assessment Type** | Black-Box Web Application Penetration Testing |
| **🏥 Target Environment** | Mediroza General Hospital Educational Lab |
| **🧰 Authentication Testing** | Hydra |
| **🔐 Password Security Testing** | John the Ripper |
| **🌍 HTTP Analysis** | cURL |
| **🔎 Technology Fingerprinting** | WhatWeb |
| **🧪 Browser Analysis** | Browser Developer Tools |
| **📁 File Inspection** | Linux Terminal Utilities |
| **📄 Documentation** | Penetration Testing Report |
| **📅 Assessment Date** | November 2026 |

---

## 🛠️ Tools and Technologies

The following tools were used during the assessment:

| 🛠️ Tool | 🎯 Purpose |
| :--- | :--- |
| **Hydra** | Authentication testing and candidate username discovery |
| **John the Ripper** | Offline PDF password-security assessment |
| **cURL** | Inspecting HTTP responses and application behavior |
| **WhatWeb** | Identifying web server technologies |
| **WHOIS / DNS Utilities** | Gathering domain and infrastructure information |
| **Browser Developer Tools** | Inspecting HTML forms, network requests, and application responses |
| **Linux Terminal** | File identification, metadata inspection, and evidence collection |

---

# 🔍 Penetration Testing Methodology

## Phase 1: Reconnaissance and Information Gathering

The first phase involved gathering information about the target web application and identifying technologies used by the server.

### Step 1. Domain and DNS Reconnaissance

I used Linux networking utilities to identify the target's DNS configuration and publicly available infrastructure information.

**Example commands:**

```bash
host medirozahospital.com
```

```bash
whois medirozahospital.com
```

### Step 2. HTTP Header Inspection

I inspected the HTTP response headers to identify the web server and other disclosed technologies.

```bash
curl -I https://medirozahospital.com
```

The responses identified **LiteSpeed** as the web server.

The application also exposed PHP version information through an HTTP response header.

### Step 3. Technology Fingerprinting

I used WhatWeb to examine the application's observable technologies.

```bash
whatweb https://medirozahospital.com
```

The tool identified LiteSpeed and returned a `403 Forbidden` response during the scan.

This result was documented separately from the successful browser access to the homepage.

### Step 4. Reviewing Public Website Resources

I reviewed publicly available website resources, including:

```bash
curl -s https://medirozahospital.com/robots.txt
```

```bash
curl -s https://medirozahospital.com/sitemap.xml
```

The `robots.txt` file referenced the following directories:

- `/patient/`
- `/staff/`
- `/old/`

These entries were useful for understanding the application's publicly advertised directory structure.

> ⚠️ **Security Observation:** A `robots.txt` directive is not an access-control mechanism. Sensitive directories must be protected through proper authentication and server-side authorization.

---

## Phase 2: Authentication Security Assessment

### Step 1. Identifying Authentication Interfaces

During application mapping, I identified two login interfaces:

- Staff login portal
- Patient login portal

I inspected the HTML forms and observed that the patient portal submitted credentials through an HTTP POST request.

### Step 2. Authentication Testing Using Hydra

Hydra was used during the authentication assessment to investigate the login process and identify a candidate username.

The testing also revealed that the patient portal returned different error messages depending on whether the submitted username was recognized.

**Observed responses:**

```text
Username not found
```

```text
Incorrect password
```

These messages disclosed information about account existence.

### 🚨 Finding 1: Username Enumeration

| Attribute | Description |
| :--- | :--- |
| **Vulnerability** | Username Enumeration |
| **Affected Component** | Patient Login Portal |
| **Risk Level** | Medium |
| **Impact** | Disclosure of valid account identifiers |
| **Evidence** | Different authentication error messages |

**Recommended Remediation:**

- Return generic authentication failure messages.
- Apply appropriate login-attempt throttling.
- Monitor repeated authentication failures.
- Consider multifactor authentication for sensitive accounts.

### Step 3. Authentication Attempt Restrictions

During testing, repeated failed authentication attempts did not produce an observable account-lockout message.

This was recorded as a potential weakness requiring additional validation.

**Security Recommendation:** Implement appropriate authentication throttling, account protection controls, and monitoring.

---

## Phase 3: SQL Injection Assessment

### Step 1. Identifying Database Error Messages

During login testing, the patient portal displayed a MySQL syntax error.

The response included a database warning associated with `mysqli_query()`.

This indicated that the application was exposing internal database errors through the login interface.

### Step 2. Confirming Authentication Weakness

Further testing demonstrated that the patient authentication mechanism was vulnerable to SQL injection.

The weakness allowed access to the patient portal within the authorized training environment.

### 🚨 Finding 2: SQL Injection / Authentication Bypass

| Attribute | Description |
| :--- | :--- |
| **Vulnerability** | SQL Injection |
| **Affected Component** | Patient Authentication |
| **Risk Level** | Critical |
| **Impact** | Unauthorized access to protected functionality |
| **Evidence** | SQL error response and subsequent portal access |

**Recommended Remediation:**

- Use parameterized SQL queries.
- Avoid constructing database queries through direct string concatenation.
- Apply least-privilege database permissions.
- Disable detailed database errors in production responses.
- Review authentication logic and authorization controls.

---

## Phase 4: Patient Portal Access and PDF Retrieval

Following the successful authentication assessment, I accessed the patient portal.

The portal displayed three password-protected pathology laboratory reports.

### Step 1. Identifying Protected Documents

The application displayed three encrypted PDF documents with download options.

### Step 2. Downloading the Reports

All three reports were downloaded during the assessment.

**Files obtained:**

```text
patient_report_1.pdf
patient_report_2.pdf
patient_report_3.pdf
```

### 🚨 Finding 3: Exposure of Protected Patient Reports

| Attribute | Description |
| :--- | :--- |
| **Affected Component** | Patient Portal |
| **Security Issue** | Sensitive document access following authentication bypass |
| **Risk Level** | Critical |
| **Impact** | Potential disclosure of confidential medical information |
| **Evidence** | Patient portal and completed PDF downloads |

**Recommended Remediation:**

- Fix the underlying SQL injection vulnerability.
- Enforce secure authentication and authorization.
- Verify document ownership before allowing downloads.
- Maintain access logs for sensitive records.
- Apply least-privilege access controls.

> 🔒 **Privacy Note:** The original patient documents are not included in this public repository.

---

## Phase 5: PDF Encryption and Password Security Assessment

### Step 1. Inspecting the Encrypted PDFs

After downloading the three PDF reports, I examined their password-protection behavior.

The files required passwords before their contents could be accessed.

### Step 2. Offline Password Security Testing

I used **John the Ripper** to evaluate the strength of the PDF passwords.

The captured terminal evidence confirms recovery of one PDF password hash.

The assessment screenshots also show that all three reports were successfully opened.

### 🚨 Finding 4: Weak PDF Password Protection

| Attribute | Description |
| :--- | :--- |
| **Vulnerability** | Weak Document Password Protection |
| **Affected Component** | Encrypted PDF Reports |
| **Risk Level** | High — Subject to Password Policy Review |
| **Impact** | Reduced confidentiality if protected files are obtained |
| **Evidence** | Password-recovery output and opened PDF documents |

**Recommended Remediation:**

- Enforce strong and unique document passwords.
- Use modern, supported encryption algorithms.
- Deliver passwords through secure communication channels.
- Avoid predictable password patterns.
- Ensure document-level encryption complements server-side access controls.

> ⚠️ **Evidence Limitation:** The supplied terminal screenshot directly verifies one recovered password hash. Separate recovery logs would be required to independently verify password recovery for each of the three PDFs.

---

## Phase 6: Sensitive Database Backup Exposure

### Step 1. Discovering a Legacy Directory

During reconnaissance, I identified a publicly accessible legacy directory.

The directory displayed an automatically generated file index.

### Step 2. Identifying an Exposed Database Backup

The directory contained an SQL backup file:

```text
mediroza_db_backup_2019.sql
```

The file was accessible without authentication.

### Step 3. Inspecting the Backup

I used Linux file inspection utilities to identify the file type and examine its metadata.

```bash
file mediroza_db_backup_2019.sql
```

```bash
stat mediroza_db_backup_2019.sql
```

The backup contained SQL table structures and records associated with hospital staff and shareholders.

### Step 4. Reviewing the Exposed Information

The database included fields associated with:

**Employee Information:**
- Full name
- Job title
- Department
- Email address
- Phone number
- National identification number
- Monthly salary
- Employment date

**Shareholder Information:**
- Shareholder name
- Ownership percentage
- Number of shares
- Share classification

### 🚨 Finding 5: Publicly Accessible Database Backup

| Attribute | Description |
| :--- | :--- |
| **Vulnerability** | Sensitive Information Exposure |
| **Affected Component** | Public Legacy Directory |
| **Risk Level** | Critical |
| **Impact** | Disclosure of confidential employee and shareholder records |
| **Evidence** | Directory listing and SQL backup contents |

**Recommended Remediation:**

- Remove database backups from publicly accessible web directories.
- Store backups in restricted storage locations.
- Disable unnecessary directory indexing.
- Enforce appropriate file permissions.
- Monitor access to sensitive backup files.
- Review exposed information and apply incident-response procedures where appropriate.

---

# 📊 Vulnerability Summary

| ID | 🐛 Vulnerability | 🎯 Risk Level | 📌 Status |
| :--- | :--- | :--- | :--- |
| **V-01** | Username Enumeration | 🟡 Medium | Identified |
| **V-02** | SQL Injection / Authentication Bypass | 🔴 Critical | Demonstrated |
| **V-03** | Exposure of Protected Patient Reports | 🔴 Critical | Demonstrated |
| **V-04** | Weak PDF Password Protection | 🟠 High | Assessed |
| **V-05** | Publicly Accessible Database Backup | 🔴 Critical | Confirmed |
| **V-06** | Verbose Database Error Disclosure | 🟡 Medium | Observed |
| **V-07** | Insufficient Login Attempt Restrictions | 🟡 Requires Validation | Preliminary Observation |

---

# 🪲 Problems Encountered & Solutions

## Problem 1. Authentication Testing Did Not Immediately Produce Access

During the initial testing phase, authentication attempts did not result in successful portal access.

**What I Learned:**

Authentication testing requires careful analysis of server responses. A failed login attempt does not necessarily mean the application is secure.

I documented the authentication behavior and investigated other observable weaknesses within the authorized assessment scope.

## Problem 2. Different Login Error Messages

The patient portal returned different responses for recognized and unrecognized usernames.

**Solution:**

I documented the error messages as evidence of username enumeration and included remediation recommendations.

## Problem 3. SQL Errors Appearing in the Login Interface

The application exposed detailed database syntax errors.

**Solution:**

I recorded the error response and documented the associated SQL injection risk.

## Problem 4. Password-Protected PDF Documents

The retrieved PDF reports could not initially be opened without their passwords.

**Solution:**

I conducted an offline password-security assessment and documented the resulting evidence.

## Problem 5. Confidential Information in Evidence

The assessment involved sensitive information, including medical reports and employee records.

**Solution:**

I prepared redacted evidence for reporting and excluded confidential source files and recovered passwords from the public repository.

---

# 🧠 What I Learned

### 1. Black-Box Penetration Testing

I learned how to assess a web application without access to its source code or internal architecture.

### 2. Reconnaissance and Information Gathering

I gained experience identifying application technologies, inspecting HTTP responses, and mapping publicly accessible resources.

### 3. Authentication Security

I learned how authentication error messages can reveal valid usernames and why login-attempt protections are important.

### 4. SQL Injection

I gained practical experience recognizing SQL-related application errors and understanding how insecure database queries can affect authentication security.

### 5. Sensitive Information Exposure

I learned that publicly accessible database backups can create serious confidentiality risks even when the main application has authentication controls.

### 6. Document Encryption

I gained experience evaluating password-protected PDF files and understanding why strong encryption must be supported by secure password practices.

### 7. Evidence Collection

I learned how to capture screenshots, document observations, and distinguish confirmed findings from issues requiring further validation.

### 8. Professional Security Reporting

I improved my ability to prepare vulnerability descriptions, assign risk ratings, explain security impacts, and recommend remediation measures.

---

# 📄 Penetration Testing Report

The full report contains:

- Executive summary
- Assessment scope
- Methodology
- Tools used
- Vulnerability findings
- Risk ratings
- Evidence screenshots
- Remediation recommendations
- Milestone summary

📥 **[View the Full Penetration Testing Report](./Mediroza_Penetration_Test_Report_Updated.pdf)**

---

# 🔐 Security & Ethical Use

This project was conducted within an authorized educational penetration testing environment.

The purpose of the assessment was to identify vulnerabilities, understand their impact, and develop practical defensive security skills.

**This repository does not contain:**

- Unredacted patient medical reports
- Recovered passwords
- Confidential employee records
- Shareholder personal information
- Unredacted database backups
- Authentication credentials or session tokens

All public evidence should be reviewed and redacted before publication.

---

# 🏁 Conclusion

This penetration testing project provided valuable hands-on experience in web application security assessment.

The most significant findings involved SQL injection, authentication weaknesses, sensitive document exposure, and insecure database backup storage.

The project strengthened my understanding of vulnerability identification, security testing methodology, risk assessment, evidence collection, and professional reporting.

**Ethical hacking is not only about finding vulnerabilities — it is about understanding security risks and helping organizations prevent them.**

---

## 🏷️ Project Tags

`Cybersecurity` `Ethical-Hacking` `Penetration-Testing` `Web-Security` `SQL-Injection` `Hydra` `John-the-Ripper` `Ubuntu-Linux` `Vulnerability-Assessment` `Networkwalks`

---

## 👨‍💻 Author

**Ackim Kafula**

**Project:** Mediroza General Hospital Penetration Testing  
**Training Organization:** Networkwalks  
**Assessment:** Black-Box Web Application Penetration Testing  
**Status:** Completed
