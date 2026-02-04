# Security Policy

## Supported Versions

MinnowVPN is currently in **alpha** development. Security updates are provided for the latest release only.

| Version | Supported          |
|---------|--------------------|
| 1.x.x   | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

**Please do NOT report security vulnerabilities through public GitHub issues.**

### How to Report

Use GitHub's private vulnerability reporting feature:

1. Go to the [Security tab](https://github.com/minnowvpn/minnowvpn/security)
2. Click "Report a vulnerability"
3. Fill out the form with details

### What to Include

- Description of the vulnerability
- Steps to reproduce the issue
- Potential impact (what could an attacker do?)
- Suggested fix (if you have one)
- Your contact information for follow-up

### What to Expect

- **Initial Response**: Within 48 hours
- **Status Update**: Within 7 days
- **Fix Timeline**: Critical issues within 7 days, others within 30 days

### Scope

Security issues we're interested in:

- Authentication/authorization bypasses
- SQL injection, command injection, XSS
- Secrets exposure (API keys, passwords in logs)
- Docker container escape vulnerabilities
- VPN protocol vulnerabilities
- Cryptographic weaknesses

### Out of Scope

- Denial of Service (DoS) attacks
- Social engineering
- Physical attacks
- Issues in third-party dependencies (report upstream)

## Security Best Practices

When deploying MinnowVPN:

1. **Use strong passwords** - Generate random secrets with `openssl rand -base64 32`
2. **Keep Docker updated** - Regularly update Docker and container images
3. **Firewall rules** - Only expose necessary ports (80, 443, 51820)
4. **Monitor logs** - Check for suspicious activity in audit logs
5. **Regular backups** - Use the included backup script

Thank you for helping keep MinnowVPN secure!
