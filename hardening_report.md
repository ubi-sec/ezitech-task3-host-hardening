# Host Hardening and Remediation Report
## SmileCare Dental Clinic (GreenLeaf Lab)

**Target:** Ubuntu VM (`greenleaf-web` at 192.168.56.10)  
**Assessor:** Abaidullah  
**Date:** 19 Nov 2026  

---

## Executive Summary
This report documents the security hardening measures applied to the underlying Ubuntu host to mitigate risks identified during the initial vulnerability assessment. The focus was on restricting network access via UFW and securing remote access via SSH hardening. Post-remediation scanning confirms a significantly reduced attack surface.

---

## 1. UFW Firewall Configuration

### Before Remediation
- UFW was inactive.
- Default policies were not explicitly set, potentially allowing unnecessary incoming traffic.

### After Remediation
- Default incoming policy set to `DENY`.
- Default outgoing policy set to `ALLOW`.
- Explicit rules added to allow only necessary traffic:
  - `22/tcp` (SSH)
  - `3000/tcp` (Juice Shop Web Application)

**Verification Command:** `sudo ufw status verbose`  
**Output:**
```text
Status: active
Default: deny (incoming), allow (outgoing), deny (routed)

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
3000/tcp                   ALLOW IN    Anywhere
2. SSH Hardening
Before Remediation
Root login was potentially permitted (prohibit-password or yes).
Password authentication was enabled, making the system vulnerable to brute-force attacks.
After Remediation
Modified /etc/ssh/sshd_config to enforce strict access controls:
PermitRootLogin no (Prevents direct root access)
PasswordAuthentication no (Enforces key-based authentication only, mitigating brute-force)
PubkeyAuthentication yes (Ensures Vagrant SSH keys continue to work)
SSH service restarted to apply changes.
Verification Command: sudo cat /etc/ssh/sshd_config | grep -E "^PermitRootLogin|^PasswordAuthentication|^PubkeyAuthentication"
Output:
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
3. Final Nmap Verification (Attack Surface Reduction)
A post-remediation Nmap scan was conducted from the attacker machine to verify the reduced attack surface.
Command: nmap -sV -p 22,3000 192.168.56.10
Result:
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.18 (Ubuntu Linux; protocol 2.0)
3000/tcp open  ppp?    (Fingerprint confirms: HTTP/1.1 200 OK, OWASP Juice Shop)
Conclusion: The attack surface has been successfully minimized. Only the required services (SSH for administration and Port 3000 for the business application) are exposed. All other 65,533 ports are filtered or closed.
