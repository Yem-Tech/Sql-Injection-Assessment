# SQL Injection Assessment
### Web Application Security Assessment — Manual and Automated Validation

> **Confidentiality Notice:** This report documents testing performed against an intentionally vulnerable training webpage for educational and portfolio purposes within an authorised context. Sensitive exploit strings, dumped data, and unsafe operational detail are intentionally excluded from this document in accordance with responsible disclosure standards.

---

## Project Overview

| Field | Details |
|---|---|
| **Project Type** | Web Application Security Assessment |
| **Assessment Focus** | Manual and Automated SQL Injection Validation |
| **Target** | Intentionally vulnerable training webpage — Bricks login form |
| **Prepared By** | Olayemi Owoeye — Security Analyst |
| **Document Purpose** | GitHub portfolio documentation and technical project evidence |
| **Authorisation** | Authorised educational context — designated training target |

---

## 1. Executive Summary

This project documents a web application security assessment focused on identifying and validating SQL injection vulnerabilities on an intentionally vulnerable login page. The exercise was conducted using both manual testing techniques and an automated validation workflow.

The manual phase centred on input testing and response observation using SQL injection test strings sourced from a local Kali Linux reference wordlist. The automated phase used **Burp Suite** to intercept and review the HTTP POST request, and **sqlmap** to validate the injection point and demonstrate security impact in a controlled manner.

The project demonstrated how weak server-side input handling exposes authentication logic and backend data to manipulation. It highlights why cybersecurity analysts must understand SQL injection — one of the most impactful vulnerability classes — before threat actors can exploit it in production environments.

---

## 2. Why SQL Injection Matters

When a user submits credentials on a login form, the application generates a database query — for example, selecting a record where the username and password match the submitted values. If the database finds a match it returns **True** and grants access. If not, it returns **False** and denies access.

SQL injection exploits this logic. Attackers know the application needs a **True** result to grant access. By injecting carefully crafted input — statements that always evaluate to True — they can bypass authentication entirely without knowing valid credentials.

Beyond authentication bypass, SQL injection can allow attackers to:

- **Exfiltrate** the entire contents of a database including usernames, passwords, and personal data
- **Manipulate** or destroy records
- **Bypass** access controls that rely on database queries
- **Expose** regulated data covered under frameworks such as HIPAA, GDPR, and PCI-DSS

This is why security analysts must proactively test for SQL injection vulnerabilities — particularly in AI-generated web applications where API-level input sanitisation may not have been implemented during development.

---

## 3. Scope, Objectives and Rules of Engagement

### Scope
Testing was limited exclusively to the intentionally vulnerable training login page. Activity focused on the login form, server responses, and database interaction behaviour associated with submitted credentials.

### Objectives
- Determine whether the login form was susceptible to SQL injection
- Verify the vulnerability manually through controlled input testing
- Confirm the risk using an automated assessment workflow
- Produce a professional security report suitable for portfolio presentation

### Rules of Engagement
Testing was performed in an educational and authorised context against an intentionally vulnerable target. Work was restricted to controlled validation of the vulnerability. Operational details that would facilitate misuse against real-world systems have been deliberately excluded from this document.

---

## 4. Tools and Environment

| Tool | Role in Assessment | Outcome |
|---|---|---|
| **Browser / Target Application** | Accessed and interacted with the Bricks login form | Observed application behaviour and response changes |
| **Kali Linux wfuzz Wordlist** | Provided SQL injection test strings for manual validation | Supported manual identification of injectable behaviour |
| **Burp Suite Community Edition** | Intercepted and reviewed the HTTP POST request | Confirmed request structure, parameters, and repeatable test point |
| **sqlmap** | Automated validation of SQL injection and controlled impact demonstration | Confirmed injectable parameter and database exposure risk |

---

## 5. Methodology

### 5.1 Understanding the Attack Surface

Before testing, the application login flow was analysed to understand how credentials are processed. The login form submits a POST request containing username and password parameters. The backend constructs a SQL query from these values and compares them against database records.

If the submitted values cause the query to evaluate as **True** — whether through valid credentials or injected logic — the application grants access. This is the fundamental weakness SQL injection exploits.

### 5.2 Manual SQL Injection Testing

**Step 1 — Locating the reference wordlist on Kali Linux:**

Kali Linux ships with the `wfuzz` wordlist library which includes a dedicated SQL injection payload list. This was navigated to and reviewed as the source of manual test strings.

```bash
cd /usr
cd share
cd wfuzz
cd wordlist
cd Injections
ls
# Output: All_attack.txt  bad_chars.txt  SQL.txt  Traversal.txt  XML.txt  XSS.txt
cat SQL.txt
```

### Screenshot 1 — Navigating to the Kali wordlist directory
> *Shows navigation from `/home/kali` through `/usr/share/wfuzz` to confirm wordlist availability*

![Navigating to wfuzz wordlist](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/01-kali_true_statement.png)

---

### Screenshot 2 — Locating the SQL Injection wordlist
> *Shows the Injections folder contents including SQL.txt, confirming the reference payload list is available*

![SQL injection wordlist folder](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/02-sql-injection_kali_statement.png)

---

### Screenshot 3 — SQL Injection payload reference (sanitised)
> *Shows the SQL.txt wordlist file opened — payload strings are intentionally obscured in this published version in accordance with responsible disclosure standards*

![SQL.txt wordlist - sanitised](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/03-sql-wordlist-blurred.png)

---

**Step 2 — Manual testing against the target:**

Selected test strings from `SQL.txt` were submitted into the login form username and password fields. Each string was designed to cause the backend SQL query to evaluate as **True**, bypassing the authentication check. Response behaviour was observed and compared against baseline normal input responses to identify indicators of injectable logic.

### 5.3 Automated Validation with Burp Suite and sqlmap

**Step 1 — Launching Burp Suite:**

Burp Suite Community Edition was launched from the Kali terminal and configured to intercept browser traffic between the client and the target application.

### Screenshot 4 — Burp Suite launch
> *Shows Burp Suite Community Edition launching from the Kali terminal*

![Burp Suite launching](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/04-burpsuite_kali.png)

---

### Screenshot 5 — Burp Suite interface with intercept off
> *Shows the Burp Suite Proxy tab with intercept in the off state before testing begins*

![Burp Suite intercept off](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/05-burpsuite-intercept_off.png)

---

**Step 2 — Accessing the target login page:**

The intentionally vulnerable Bricks login form was opened alongside Burp Suite to establish the testing environment.

### Screenshot 6 — Target login page and Burp Suite interface
> *Shows the Bricks login form (the assessment target) alongside the Burp Suite proxy interface*

![Target login page](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/06-burpsuite-interface.png)

---

**Step 3 — Enabling intercept and submitting test credentials:**

Burp Suite intercept was enabled. Test credentials were submitted through the login form, causing Burp Suite to hold the request for inspection.

### Screenshot 7 — Intercept enabled with credentials entered
> *Shows Burp Suite intercept turned on and test credentials visible in the login form*

![Intercept on with credentials](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/07-burpsuite-intercept_on.png)

---

**Step 4 — Capturing the POST request:**

The intercepted POST request was reviewed in Burp Suite, confirming the HTTP method, target path, and request body parameters including the username and password fields.

### Screenshot 8 — POST request captured in Burp Suite
> *Shows the full intercepted HTTP POST request including headers and parameter structure*

![POST request captured](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/08-burpsuite-capture_submit.png)

---

**Step 5 — Saving the request for sqlmap:**

A project directory was created and the captured request was saved to a text file to be passed into sqlmap for automated validation.

```bash
mkdir sql_project
cd sql_project
touch sqlfile.txt
mousepad sqlfile.txt
```

### Screenshot 9 — Request saved to sqlfile.txt
> *Shows the sql_project directory creation and the captured request saved to sqlfile.txt via Mousepad*

![Request saved to file](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/09-burpsuite-usernpass_saved.png)

---

### Screenshot 10 — Request file contents confirmed
> *Shows the contents of sqlfile.txt confirming the full POST request was saved correctly for sqlmap input*

![Request file contents](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/10-bursquite-pasted_sqlfiletxt.png)

---

**Step 6 — Running sqlmap:**

sqlmap was invoked against the saved request file, targeting the password parameter for automated injection validation.

```bash
sqlmap -l sqlfile.txt -p passwd
```

### Screenshot 11 — sqlmap initialised
> *Shows sqlmap launching successfully and parsing the saved request file*

![sqlmap initialised](https://github.com/Yem-Tech/Sql-Injection-Assessment/blob/main/Screenshots/11-sqlmap-in_kali.png)

---

**Step 7 — Automated validation results:**

sqlmap confirmed the `passwd` POST parameter was injectable. The tool identified the backend database management system and demonstrated the ability to enumerate database structure. Specific payload strings, database contents, and dump output are intentionally excluded from this document.

---

## 6. Key Findings

- The login form did not safely neutralise user-supplied input before constructing backend SQL queries
- The HTTP POST request contained parameters that provided a reproducible SQL injection test point
- Manual testing using reference payload strings produced response behaviour consistent with improper server-side query handling
- Automated validation with sqlmap confirmed the vulnerable parameter and demonstrated database enumeration capability
- The back-end DBMS was identified, confirming the extent of potential data exposure through the injectable parameter
- The demonstrated impact reflects real-world risk — in a production environment this class of vulnerability could expose all records stored in the connected database

---

## 7. Risk Statement and Business Impact

SQL injection is a high-severity vulnerability because it allows an attacker to alter query logic, bypass access controls, and interact directly with backend database content. It consistently appears in the **OWASP Top 10** as one of the most critical and widespread web application security risks.

| Risk Area | Assessment |
|---|---|
| **Likelihood** | High — the injection point was practically reproducible using standard tooling |
| **Impact** | High — backend database enumeration and credential exposure demonstrated |
| **Overall Risk** | Critical — high-priority remediation required |

**Regulatory context:** Organisations handling personal data are subject to frameworks including HIPAA, GDPR, and PCI-DSS. A SQL injection vulnerability exposing user credentials or health records could result in regulatory penalties, mandatory breach notification, and significant reputational damage.

---

## 8. Remediation Recommendations

- Replace dynamic query construction with **parameterised queries or prepared statements** across the entire application
- Implement **API-level input sanitisation** in the backend to filter SQL syntax before it reaches the database layer — this is particularly important for AI-generated web applications where input handling may not have been included during development
- Apply strict **server-side input validation** — client-side controls alone are insufficient
- Use **least-privilege database accounts** so the application cannot access tables or functions beyond its operational requirement
- Implement **secure error handling** to prevent database structure or query behaviour being disclosed through verbose error responses
- Introduce **SAST/DAST testing** and regular web application assessments into the development and deployment lifecycle
- Monitor authentication endpoints for anomalous query behaviour through **logging, alerting, and rate-limiting**

---

## 9. Skills Demonstrated

- Web application security assessment methodology
- SQL injection concepts — authentication bypass, boolean-based logic, time-based blind
- Manual payload testing using Kali Linux reference wordlists
- Burp Suite proxy configuration, intercept, and HTTP request capture
- sqlmap automated injection validation workflow
- Request file preparation and parameter targeting
- Risk articulation and remediation reporting aligned with OWASP standards
- Professional documentation suitable for technical and non-technical stakeholders

---

## 10. Conclusion

This project successfully demonstrated the identification and validation of a SQL injection vulnerability on an intentionally vulnerable web application. The combination of manual testing and controlled automation provided both technical depth and strong evidence of impact.

Understanding SQL injection is essential for any cybersecurity analyst — particularly as AI-generated web applications become more common. Without proper API-level input sanitisation implemented during development, these applications may be highly susceptible to injection attacks. Proactive security testing allows organisations to identify and remediate these weaknesses before threat actors can exploit them.

---

## Repository Structure

```
sql-injection-assessment/
├── README.md
└── screenshots/
    ├── 01-wordlist-navigation.png
    ├── 02-injection-wordlist-folder.png
    ├── 03-sql-wordlist-blurred.png
    ├── 04-burpsuite-launch.png
    ├── 05-burpsuite-intercept-off.png
    ├── 06-target-login-page.png
    ├── 07-burpsuite-intercept-on.png
    ├── 08-burpsuite-post-captured.png
    ├── 09-request-saved-to-file.png
    ├── 10-request-file-contents.png
    └── 11-sqlmap-initialised.png
```

---

## Author

**Cybersecurity Analyst | HyperTechAi**
Certifications: CCNA | Microsoft Azure | AWS | CompTIA Security+ | Python
Currently pursuing: Advanced Cybersecurity Specialisation

---

*This assessment was conducted in an authorised educational context against an intentionally vulnerable training target. All testing activity was restricted to the defined scope. Sensitive operational detail has been intentionally excluded in accordance with responsible disclosure standards. This document is prepared for cybersecurity portfolio purposes following professional security reporting standards.*

