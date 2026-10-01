# Milestone 2 — Cracking the PDF Encryption

**Objective:** Crack the encryption on all 3 retrieved PDF lab reports.
**Authorization:** Written permission granted.

## 1. Analysing the encryption

Each of the 3 PDFs retrieved in Milestone 1 was protected with standard PDF owner/user password encryption (`$pdf$` hash format, RC4/AES depending on revision). Since brute-forcing arbitrary passwords is slow, a **dictionary / wordlist attack** was used instead — the same approach tools like John the Ripper use, matching each word in a wordlist against the extracted PDF password hash until a match is found.

The general workflow used for each file:

1. Extract the file's `$pdf$...` hash (PDF password hash format).
2. Paste the hash into a dictionary-attack tool, configured against a wordlist.
3. Run the attack until a match is found, then use the cracked password to open the PDF.

## 2. File 1 — Pathology Report (S. Dlamini)

- **Approach:** Built-in wordlist (100 common passwords).
- **Result:** Cracked on the first pass — password was `123456`, one of the most common passwords in any breach corpus.

**Evidence:** `evidence/m2-password-cracking/patientreport1paswd.PNG`

## 3. File 2 — Pathology Report (unknown at this stage / second file)

- **Approach:** The built-in 100-word list was **not sufficient** for this file — it did not contain the correct password.
- **Pivot:** Switched to a larger/custom wordlist (343 candidate passwords tried, ~14 passwords/sec), reflecting the hint that *"one method will not work for all 3 files."*
- **Result:** Cracked after 235/343 attempts — password was a symbol-based string: `!@#$%^&`

**Evidence:** `evidence/m2-password-cracking/patientreport2pswd.PNG`

## 4. File 3 — Pathology Report (third file)

- **Approach:** Built-in wordlist (100 common passwords).
- **Result:** Cracked — password was `password`, another extremely common/weak default.

**Evidence:** `evidence/m2-password-cracking/patientreport3paswd.PNG`

## 5. Recovered contents

All three PDFs were unlocked using their respective cracked passwords, revealing the full confidential pathology lab reports:

| Patient | Patient ID | DOB | Referring Dr. | Lab Ref | Notable flagged result(s) |
|---|---|---|---|---|---|
| Sipho Dlamini | MG-P-10231 | 1984-06-12 | Dr. Anita Naicker | LR-2024-1187 | White Cell Count — HIGH (11.8 x10⁹/L) |
| Priya Reddy | MG-P-10244 | 1991-02-28 | Dr. Johan van der Merwe | LR-2024-1192 | Total Cholesterol, LDL, Triglycerides — all HIGH |
| Emily Thompson | MG-P-10258 | 1978-09-03 | Dr. Ahmed Kara | LR-2024-1205 | Haemoglobin, Ferritin, Vitamin D — all LOW |

**Evidence:** `evidence/m2-password-cracking/recoverdreport1.PNG`, `recoverdreport2.PNG`, `recoverdreport3.PNG`

## Key takeaway

Even once an attacker is blocked by "encryption," weak, guessable, dictionary-crackable passwords mean the encryption provides little real protection. Two of the three files used passwords found in virtually every top-10,000 common password list.

## Deliverable

✅ All 3 PDFs successfully decrypted
✅ Full confidential patient lab report contents recovered and documented above

→ Continue to [Milestone 3 — Data Exposure](M3-data-exposure.md)
