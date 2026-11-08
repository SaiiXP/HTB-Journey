[fawn_assessment_report.md](https://github.com/user-attachments/files/34546029/fawn_assessment_report.md)

# HTB Assessment Report

* **Target / Scope:** `HTB - Fawn (Very Easy)`
* **Author / Analyst:** `SaiiXP`
* **Date of Assessment:** `November 2026`

---

## 1. Executive Summary
Ran a quick security check on target `10.129.181.205` to see what services were exposed and check its hardening configuration. The main issue found is an insecure File Transfer Protocol (FTP) setup that allows anyone to log in anonymously without credentials. Because of this, unauthorized users can grab sensitive files straight from the root directory. The fix is simple: disable anonymous logins and lock down access controls.

---

## 2. Lab Overview & Objectives
* **Target IP:** `10.129.181.205`
* **Objective:** Find open network services, spot any authentication weaknesses, and prove exploitability by pulling down the target flag.

---

## 3. Reconnaissance & Enumeration
* **Port Scan:** Ran `nmap` with service version checks and default scripts against the target:
  ```bash
  nmap -p21,22,443 -sV -sC 10.129.181.205
  ```
* **What I Found:**
  * **Port 21 (FTP):** Open, running `vsftpd 3.0.3`.
  * **The Flaw:** The `ftp-anon` script showed that anonymous login was wide open (status code `230`).
  * **Ports 22 & 443:** Both SSH and HTTPS were closed.
  * **OS:** Standard Unix environment.

---

## 4. Exploitation & Access Methodology
* **Login:** Connected to the FTP server using the standard anonymous account:
  ```bash
  ftp anonymous@10.129.181.205
  ```
* **Enumeration:** Ran `ls` to check out the root files, which immediately pointed to `flag.txt`.
* **Exfiltration:** Pulled the file down locally using `get flag.txt`.

---

## 5. Evidence & Validation
* **Wrap-up:** Dropped the session with `exit`.
* **Verification:** Checked the local file to confirm the flag capture:
  ```bash
  cat flag.txt
  ```
  * **Hash retrieved:** `035db21c881520061c53e0536e44f815`

---

## 6. Remediation & Key Recommendations
* **The Risk:** Leaving anonymous FTP enabled lets outside users read or overwrite important system files without needing a real account or leaving a normal audit trail.
* **How to Fix It:**
  1. Open the FTP config file (`/etc/vsftpd.conf`).
  2. Find the anonymous setting and change it to `anonymous_enable=NO`.
  3. Restart the FTP service to apply the changes.
