# NETWORKWALKS-B083-WK4-PENETRATION-TESTNG
Authorized web app penetration test of a hospital patient portal SQL injection, PDF password cracking, and exposed database backup findings, with full report.

# Mediroza General Hospital — Penetration Test

**Status:** Completed (training engagement)
**Authorization:** Written permission granted by Networkwalks for the simulated target `medirozahospital.com`
**Scope:** Patient portal web application, exposed backend files, associated server misconfigurations

> ⚠️ This repository documents a lab/training penetration test performed against a simulated target as part of a Networkwalks Academy exercise. All techniques described were authorized in writing for this target only and must never be used against any system without explicit permission from its owner.

## Overview

This engagement was broken into four milestones:

| Milestone | Objective | Status |
|---|---|---|
| [M1](milestones/M1-recon-and-access.md) | Recon + gain unauthorized access to the patient portal to retrieve 3 confidential PDF lab reports | ✅ Complete |
| [M2](milestones/M2-password-cracking.md) | Crack the encryption on all 3 retrieved PDFs | ✅ Complete |
| [M3](milestones/M3-data-exposure.md) | Identify a further critical data exposure (staff salaries + shareholder records) | ✅ Complete |
| [M4](milestones/M4-full-pentest-report.md) | Full penetration testing report | ✅ Complete |


## Summary of findings

1. **SQL Injection — Authentication Bypass (Critical)**: The patient portal login (`/patient/login.php`) was vulnerable to SQL injection, allowing authentication bypass with a payload such as `admin' --` and granting access to another patient's protected lab reports.
2. **Weak PDF Encryption (High)**: The 3 retrieved lab report PDFs were protected with weak, dictionary-guessable passwords (`123456`, `password`, and a short symbol string), all cracked via dictionary/wordlist attack against the PDF's `$pdf$` hash.
3. **Sensitive Data Exposure — Public Database Backup (Critical)**: An old `.sql` database backup file was found exposed on the server, containing full staff records (names, national ID numbers, salaries, contact details) and confidential shareholder/ownership data for the hospital.

See [M4-full-pentest-report.md](M4-full-pentest-report.md) for the complete writeup, risk ratings, and remediation steps.

