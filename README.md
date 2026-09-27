# VulnVMa — Vulnerability Assessment & Penetration Testing

> Course-end project · Advanced Executive Program in Cybersecurity · IIIT-Bangalore × Simplilearn · May 2026

A full VAPT engagement against the VulnVMa vulnerable machine, working through reconnaissance, enumeration, and exploitation to recover all six flags. Each finding is documented with its severity, OWASP category, proof, business impact, and remediation.

📄 **[Read the full report](report/VulnVMa-VAPT-Report.pdf)**

---

## Summary

Six vulnerabilities were identified and confirmed against a single web-facing host (`10.0.2.3`), spanning misconfigured services, information disclosure, and weak access control. All six were low-complexity to exploit, which is the point worth making: none required a single exploit payload. They were reachable through default settings, exposed files, and predictable naming — the failures that show up most often in real small-business environments.

## Findings

| # | Vulnerability | OWASP (2021) | Severity | How it was found |
|---|---|---|---|---|
| 1 | Anonymous FTP login enabled | A05 Security Misconfiguration | Critical | FTP anonymous login |
| 2 | Information disclosure via robots.txt | A01 Broken Access Control | Medium | `curl /robots.txt` |
| 3 | Sensitive data in page source | A01 Broken Access Control | High | Browser view-source |
| 4 | Accessible admin directory (`/4dm1n/`) | A05 Security Misconfiguration | High | Gobuster (HTTP 301) |
| 5 | Public configuration directory (`/c0nf1g/`) | A05 Security Misconfiguration | High | Manual path enumeration |
| 6 | Sensitive data in HTTP responses | A01 Broken Access Control | High | Burp Suite intercept |

## Business Impact

Read as a set, these findings describe a host where an attacker needs no special tooling to get a foothold. Anonymous FTP alone allows reading and potentially writing files without credentials. The exposed config directory and in-source data hand over exactly the kind of internal detail that turns a first foothold into a fuller compromise. For any business running a public web presence, this is the difference between an attacker being slowed down and being handed a map.

## Methodology

The assessment followed a standard recon → enumerate → exploit → document flow:

1. **Reconnaissance** — Nmap service and version scan (`nmap -A`) to map open ports: FTP (21), SSH (22), HTTP (80).
2. **Enumeration** — Gobuster directory brute-forcing, `robots.txt` inspection, browser view-source, and manual path guessing using leet-style naming conventions (`admin` → `4dm1n`, `config` → `c0nf1g`).
3. **Exploitation** — Anonymous FTP file retrieval, direct requests to exposed directories, and Burp Suite request/response interception.
4. **Documentation** — Each flag mapped to a named vulnerability, severity, and remediation.

## Remediation Themes

Across all six findings, the fixes reduce to a short list: disable anonymous FTP and prefer SFTP; keep configuration and sensitive files out of the web root; never place secrets in client-side source; put authentication in front of admin interfaces; and enforce HTTPS so responses can't be read in transit.

## Tools

Nmap · curl · Gobuster · Burp Suite · Firefox · FTP client · Kali Linux (VirtualBox)

## Repository Contents

```
├── README.md
├── report/     Full VAPT report (PDF)
└── evidence/   Screenshots, one per finding
```

## Note on Attribution

The report was written under the author's name for a course submission. Target IP and environment are from an isolated VirtualBox lab network (`10.0.2.0/24`); they are not internet-routable.

## Disclaimer

All testing was performed in an isolated, self-owned lab against a deliberately vulnerable training machine, for educational purposes. No production or third-party systems were involved.
