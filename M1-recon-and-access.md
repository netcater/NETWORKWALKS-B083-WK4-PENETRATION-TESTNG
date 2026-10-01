# Milestone 1 — Reconnaissance & Unauthorized Access

**Objective:** Attack the website and find the 3 confidential PDF lab reports of patients.
**Authorization:** Written permission granted.

## 1. Reconnaissance

Initial fingerprinting was performed against `medirozahospital.com`:

- `whatweb` identified the stack: LiteSpeed server, powered by "Mediroza CMS 1.4.2" (custom CMS, version disclosed in `meta generator` tag).
- A direct `curl -i` request revealed the server headers and the homepage source, confirming the CMS version and exposing site structure (`/staff/login.php`, `/contact.html`, `/patient/portal.php`, etc.) via visible links in the HTML.
- `dnsrecon` and `wafw00f` were attempted; the WAF check (`wafw00f`) returned no conclusive result (connection timeout), and DNS tooling did not surface additional subdomains in this pass.

**Evidence:** `evidence/m1-recon-and-access/recon1.PNG`, `recon2.PNG`

### Key takeaway
The CMS version disclosure (`Mediroza CMS 1.4.2`) is itself a minor information-disclosure finding — it narrows the search for known vulnerabilities/exploits against that specific CMS version.

## 2. Identifying the entry point

The site exposes a **Patient Portal** (`/patient/login.php`) that authenticates patients and, once logged in, lists that patient's password-protected lab reports (`/patient/portal.php`) with a **Download** button per report.

This login form is the main authenticated entry point into protected content and was selected as the primary attack surface.

## 3. Testing the authentication mechanism

A single quote (`'`) was submitted in the **Username** field to test how the backend handles unsanitized input.

**Result:** The application threw a raw MySQL error:

```
Warning: mysqli_query(): You have an error in your SQL syntax; check the manual
that corresponds to your MySQL server version for the right syntax to use near
"'" #' at line 1
```

This confirms:
- User input is concatenated directly into a SQL query (no parameterized queries / prepared statements).
- Error messages are not suppressed in production, directly revealing the query behavior to an attacker.

**Evidence:** `evidence/m1-recon-and-access/SQLinjection.PNG`

## 4. Exploiting the SQL injection — Authentication bypass

Based on the error behavior, a classic comment-based SQL injection authentication bypass payload was submitted in the Username field:

```
admin' --
```

With the password field left blank, this payload is designed to comment out the rest of the WHERE clause (e.g. the password check) in a query shaped like:

```sql
SELECT * FROM users WHERE username = '$username' AND password = '$password'
```

becoming:

```sql
SELECT * FROM users WHERE username = 'admin' --'
```

**Evidence:** `evidence/m1-recon-and-access/testingSQLinjection.PNG`

## 5. Gaining unauthorized access

Submitting the payload successfully bypassed authentication and granted access to the restricted **Patient Portal** area, which lists another patient's confidential lab reports:

- Pathology Report — S. Dlamini (Lab Ref LR-2024-1187)
- Pathology Report — P. Reddy (Lab Ref LR-2024-1192)
- Pathology Report — E. Thompson (Lab Ref LR-2024-1205)

All three reports were downloaded as encrypted/password-protected PDFs.

**Evidence:** `evidence/m1-recon-and-access/accessGranted.PNG`

## Deliverable

✅ Proof of unauthorized access via SQL injection authentication bypass
✅ 3 confidential, password-protected PDF lab reports retrieved for S. Dlamini, P. Reddy, and E. Thompson

→ Continue to [Milestone 2 — Password Cracking](M2-password-cracking.md)
