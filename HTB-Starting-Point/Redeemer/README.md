# HTB - Starting Point - Redeemer

* **Target / Scope:** 'HTB - Redeemer (Very Easy)
* **Author / Analyst:** SaiiXP
* **Date of Assessment:** November 2026

---

## 1. Executive Summary
Performed a comprehensive port scan on target `10.129.183.16` to identify exposed network services and evaluate security configurations. Discovered an open Redis in-memory database running on port 6379 without any authentication controls enabled. By connecting directly with the Redis command-line utility, unauthorized database enumeration was achieved, leading to the extraction of sensitive information stored in database index 0. The fix is to enforce access controls and enable password authentication in the Redis configuration.

## 2. Lab Overview & Objectives
* **Target IP:** `10.129.183.16`
* **Objective:** Scan for open ports, identify the running service, interact with the database management interface, and retrieve the target flag.

## 3. Reconnaissance & Enumeration
* **Full Port Scan:** Ran a fast full-port scan using `nmap` to check all TCP ports against the target:
  ```bash
  sudo nmap -p- -T4 --min-rate=1000 10.129.183.16
  ```
* **What I Found:**
  * **Port 6379 (TCP):** Open, running **Redis** (an open-source, in-memory data structure store used as a database, cache, and message broker).

## 4. Exploitation & Access Methodology
* **Connection:** Connected to the remote Redis server instance using `redis-cli` with the hostname flag:
  ```bash
  redis-cli -h 10.129.183.16
  ```
* **Server Information:** Queried server statistics and version details using the `INFO` command.
* **Database Enumeration:** Selected database index 0 (`SELECT 0`) and listed all stored keys using `KEYS *`, revealing multiple entries including the target data key.

## 5. Evidence & Validation
* **Data Retrieval:** Extracted the value of the target flag using the `GET` command:
  ```text
  SELECT 0
  GET flag
  ```
* **Flag / Hash Retrieved:** `03e1d2b376c37ab3f5319922053953eb`

## 6. Remediation & Key Recommendations
* **The Risk:** Leaving Redis exposed without authentication allows anyone with network access to read, modify, or flush databases, or even execute arbitrary commands depending on configuration permissions.
* **How to Fix It:**
  1. Open the Redis configuration file (`redis.conf`).
  2. Set a strong password using the `requirepass` directive.
  3. Bind Redis exclusively to localhost (`bind 127.0.0.1`) if external network access is not required.
  4. Restart the Redis service to enforce the new security posture.

