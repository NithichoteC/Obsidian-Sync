# Security Hardening Checklist

Comprehensive security guide for protecting your self-hosted Obsidian infrastructure.

---

## 🔐 Security Levels

### Level 1: Basic (Minimum Required) ⭐
- Strong passwords
- HTTPS encryption
- Regular updates

### Level 2: Standard (Recommended for Agency) ⭐⭐
- IP allowlisting
- 2FA for admin access
- Automated backups
- Monitoring

### Level 3: Advanced (Enterprise-Grade) ⭐⭐⭐
- VPN-only access
- Encrypted vaults
- Audit logging
- Intrusion detection

**Choose based on data sensitivity.**

---

## 📋 Pre-Deployment Security Checklist

### Server Security (Hetzner)

- [ ] SSH key-based authentication enabled
- [ ] Password authentication disabled
- [ ] Root login disabled
- [ ] Firewall configured (UFW/iptables)
- [ ] Fail2ban installed and configured
- [ ] Automatic security updates enabled
- [ ] Non-standard SSH port (optional but recommended)

**Quick setup:**
```bash
# Install firewall
apt install ufw
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw enable

# Install fail2ban
apt install fail2ban
systemctl enable fail2ban
systemctl start fail2ban

# Disable root login
nano /etc/ssh/sshd_config
# Set: PermitRootLogin no
# Set: PasswordAuthentication no
systemctl restart sshd
```

### Coolify Security

- [ ] Coolify admin password strong (32+ characters)
- [ ] Coolify accessible only via HTTPS
- [ ] Coolify dashboard not exposed publicly (optional: use SSH tunnel)
- [ ] 2FA enabled on Coolify (if available)
- [ ] Regular Coolify updates applied

---

## 🔒 CouchDB Security Checklist

### Authentication & Access Control

- [ ] **Admin password:** Strong, randomly generated (32+ characters)
- [ ] **Password stored securely:** Password manager (1Password, Bitwarden, etc.)
- [ ] **Admin panel disabled:** Or restricted to localhost only
- [ ] **require_valid_user enabled:** All requests require authentication

**Verify:**
```bash
curl https://couchdb.yourdomain.com/_utils/
# Should require login, not open publicly
```

### Network Security

- [ ] **HTTPS only:** HTTP redirect enabled
- [ ] **TLS 1.2+ only:** Disable older protocols
- [ ] **Valid SSL certificate:** Let's Encrypt auto-renewal working
- [ ] **Certificate monitoring:** Alerts before expiry

**Check SSL:**
```bash
curl -I https://couchdb.yourdomain.com
# Should show: HTTP/2 200 or HTTP/1.1 200
# Should NOT show security warnings
```

### IP Allowlisting (Level 2)

**Recommended for agency data.**

- [ ] **Coolify IP restrictions enabled**
- [ ] **Office IP added** to allowlist
- [ ] **Home IP added** to allowlist
- [ ] **Mobile IP range added** (if needed)
- [ ] **Backup access method** documented (in case IP changes)

**How to implement in Coolify:**

1. Go to CouchDB service → Settings
2. Add environment variable:
   ```
   ALLOWED_IPS=1.2.3.4,5.6.7.8/24
   ```
3. Or use Coolify's built-in IP allowlist feature
4. Test access from allowed/blocked IPs

**Find your IP:**
```bash
curl ifconfig.me
```

### Database Security

- [ ] **Regular backups configured** (daily minimum)
- [ ] **Backup encryption enabled**
- [ ] **Backup restoration tested** (at least once)
- [ ] **Old backups rotated** (keep last 30 days)

**Backup command:**
```bash
docker exec obsidian-couchdb couchdb-backup \
  -u admin -p YOUR_PASSWORD \
  -H localhost -d obsidian \
  -f /backup/obsidian-$(date +%Y%m%d).json
```

---

## 🛡️ Obsidian Security Checklist

### Vault Security

- [ ] **Vault encryption enabled** (LiveSync plugin setting)
- [ ] **Encryption passphrase strong** (16+ characters)
- [ ] **Passphrase stored securely** (password manager)
- [ ] **Encryption enabled on ALL devices**

**Enable encryption:**
1. Settings → Self-hosted LiveSync
2. Encryption → Enable E2E encryption
3. Enter strong passphrase
4. Apply to all devices

### Plugin Security

- [ ] **Only install trusted plugins** (check ratings, reviews, source code)
- [ ] **Plugins from official Obsidian repository only**
- [ ] **Regular plugin updates** (check monthly)
- [ ] **Remove unused plugins**
- [ ] **Review plugin permissions** before installing

**Plugin review checklist:**
- GitHub repository exists and active?
- Recent updates (within 6 months)?
- Good community ratings?
- Source code readable and safe?
- Necessary permissions only?

### API Security (MCP Integration)

- [ ] **Local REST API:** localhost only (`127.0.0.1`)
- [ ] **API key:** Strong, randomly generated
- [ ] **API key stored securely:** Not in plain text files
- [ ] **Minimum permissions:** Only enable needed features
- [ ] **API monitoring:** Check logs for unusual activity

**Verify API is local-only:**
```bash
# This should work (from same machine):
curl http://127.0.0.1:27124/ -H "Authorization: Bearer YOUR_KEY"

# This should fail (from different machine):
curl http://your-server-ip:27124/
# Should: Connection refused
```

---

## 🔐 Access Control Checklist

### Password Policy

- [ ] **CouchDB admin:** 32+ random characters
- [ ] **Encryption passphrase:** 16+ characters, mix of types
- [ ] **GitHub (if using Git backup):** Strong password + 2FA
- [ ] **Coolify admin:** 32+ random characters
- [ ] **Password manager:** All credentials stored, not in plain text

**Generate strong passwords:**
```bash
# 32-character random password
openssl rand -base64 32

# 64-character for maximum security
openssl rand -base64 64
```

### Multi-Factor Authentication (MFA)

**Where to enable 2FA:**
- [ ] GitHub account (if using Git backups)
- [ ] Hetzner account (server provider)
- [ ] Domain registrar (DNS provider)
- [ ] Coolify admin (if supported)

### Access Logging

- [ ] **CouchDB access logs enabled**
- [ ] **Failed login attempts logged**
- [ ] **Log rotation configured** (don't fill disk)
- [ ] **Log monitoring alerts** (optional but recommended)

**View CouchDB logs:**
```bash
docker logs obsidian-couchdb | tail -100
docker logs obsidian-couchdb | grep "401"  # Failed auth
```

---

## 🚨 Monitoring & Alerts Checklist

### Health Monitoring

- [ ] **Coolify health checks enabled**
- [ ] **Uptime monitoring** (UptimeRobot free tier, or similar)
- [ ] **SSL certificate expiry alerts**
- [ ] **Disk space monitoring** (alert at 80% full)

### Security Monitoring

- [ ] **Failed login attempt alerts**
- [ ] **Unusual access pattern detection**
- [ ] **Backup failure alerts**
- [ ] **Container restart alerts**

**Simple uptime monitoring:**
1. Sign up: https://uptimerobot.com (free)
2. Add monitor: `https://couchdb.yourdomain.com`
3. Set alert email
4. Enable: Email on downtime

### Log Review Schedule

- [ ] **Daily:** Check Coolify dashboard for errors
- [ ] **Weekly:** Review CouchDB access logs
- [ ] **Monthly:** Security audit (this checklist)
- [ ] **Quarterly:** Restore test from backups

---

## 🛠️ Maintenance Checklist

### Weekly Maintenance

- [ ] Check CouchDB container status
- [ ] Verify sync working on all devices
- [ ] Review recent error logs
- [ ] Check disk space usage

**Quick health check:**
```bash
docker ps | grep couchdb  # Should be "Up X days"
docker logs obsidian-couchdb --since 24h | grep ERROR
df -h  # Check disk space
```

### Monthly Maintenance

- [ ] Update CouchDB image
- [ ] Update Obsidian apps on all devices
- [ ] Update plugins
- [ ] Review and rotate logs
- [ ] Test backup restoration
- [ ] Review access logs for anomalies

**Update CouchDB:**
```bash
cd /opt/obsidian-sync
docker-compose pull
docker-compose up -d
```

### Quarterly Maintenance

- [ ] Full security audit (re-run this checklist)
- [ ] Password rotation (if required by policy)
- [ ] Review IP allowlist (remove old, add new)
- [ ] Disaster recovery drill (restore from backup completely)
- [ ] Review and update security policies

---

## 🔥 Incident Response Plan

### If Unauthorized Access Detected

1. **Immediate Actions:**
   - [ ] Change CouchDB admin password immediately
   - [ ] Revoke API keys
   - [ ] Enable IP allowlist (if not already)
   - [ ] Check access logs for extent of breach

2. **Investigation:**
   - [ ] Review all logs for unauthorized activity
   - [ ] Check what data was accessed/modified
   - [ ] Identify entry point (weak password, exposed port, etc.)

3. **Recovery:**
   - [ ] Restore from last known good backup (if data corrupted)
   - [ ] Implement additional security controls
   - [ ] Document incident for future reference

### If Server Compromised

1. **Immediate Actions:**
   - [ ] Shut down CouchDB container
   - [ ] Isolate server from network (if possible)
   - [ ] Contact Hetzner support
   - [ ] Preserve logs for analysis

2. **Recovery:**
   - [ ] Rebuild server from scratch
   - [ ] Restore CouchDB from backup
   - [ ] Implement enhanced security (Level 3)
   - [ ] Audit all access credentials

### If Backup System Fails

1. **Immediate Actions:**
   - [ ] Investigate backup failure cause
   - [ ] Create manual backup immediately
   - [ ] Test backup restoration

2. **Prevention:**
   - [ ] Set up redundant backup system
   - [ ] Enable backup monitoring alerts
   - [ ] Document backup procedures

---

## 📊 Security Audit Checklist (Monthly)

### Access Review

- [ ] Review recent CouchDB access logs
- [ ] Check for unusual IP addresses
- [ ] Verify all access from known devices
- [ ] Remove inactive users/devices (if multi-user)

### Configuration Review

- [ ] Verify HTTPS working correctly
- [ ] Check SSL certificate valid and auto-renewing
- [ ] Confirm IP allowlist current
- [ ] Test all security controls functioning

### Backup Review

- [ ] Verify backups running on schedule
- [ ] Check backup file sizes reasonable
- [ ] Test backup restoration (sample file)
- [ ] Confirm backup retention policy followed

### Update Review

- [ ] CouchDB image up to date
- [ ] Obsidian apps updated on all devices
- [ ] Plugins updated
- [ ] Server OS security patches applied

---

## 🎯 Security Recommendations by Use Case

### Personal Use (Low Sensitivity)

**Minimum (Level 1):**
- Strong passwords
- HTTPS
- Regular backups

**Recommended tools:**
- Password manager (Bitwarden free)
- Let's Encrypt SSL (automatic)
- Weekly manual backups

### Small Team / Agency (Medium Sensitivity)

**Recommended (Level 2):**
- All Level 1 items
- IP allowlisting
- Automated backups
- Basic monitoring

**Recommended tools:**
- 1Password Teams or Bitwarden
- UptimeRobot (free tier)
- Automated backup script
- Coolify built-in monitoring

### Enterprise / Client Data (High Sensitivity)

**Required (Level 3):**
- All Level 1 & 2 items
- VPN-only access (Tailscale)
- Vault encryption mandatory
- Comprehensive audit logging
- Intrusion detection
- Regular security audits

**Recommended tools:**
- Tailscale (free for personal, paid for teams)
- Encrypted vault backups
- SIEM solution (Wazuh, etc.)
- Professional security audit (annual)

---

## 🔗 Additional Security Resources

### Recommended Reading

- OWASP Top 10: https://owasp.org/www-project-top-ten/
- CouchDB Security Docs: https://docs.couchdb.org/en/stable/intro/security.html
- Obsidian Security FAQ: https://help.obsidian.md/

### Security Tools

- **Password Manager:** Bitwarden, 1Password, KeePass
- **2FA Apps:** Authy, Google Authenticator, 1Password
- **Uptime Monitoring:** UptimeRobot, Pingdom, StatusCake
- **VPN:** Tailscale, WireGuard, OpenVPN
- **Backup Encryption:** GPG, age, Cryptomator

### Community Resources

- Obsidian Security Forum: https://forum.obsidian.md/c/share-showcase/security/
- r/selfhosted Security Wiki: https://www.reddit.com/r/selfhosted/wiki/
- CouchDB IRC/Discord for security questions

---

## ✅ Quick Security Score

**Complete this audit to calculate your security level:**

### Basic Security (10 points each)
- [ ] Strong admin password (10 pts)
- [ ] HTTPS enabled (10 pts)
- [ ] Regular backups (10 pts)
- [ ] Firewall configured (10 pts)
- [ ] SSH keys only (10 pts)

### Standard Security (5 points each)
- [ ] IP allowlisting (5 pts)
- [ ] 2FA on critical accounts (5 pts)
- [ ] Monitoring enabled (5 pts)
- [ ] Vault encryption (5 pts)
- [ ] Log review process (5 pts)

### Advanced Security (3 points each)
- [ ] VPN access (3 pts)
- [ ] Intrusion detection (3 pts)
- [ ] Audit logging (3 pts)
- [ ] Incident response plan (3 pts)
- [ ] Regular security audits (3 pts)

**Your Score:** ___ / 94

**Rating:**
- 0-30: ⚠️ **Critical** - Immediate action required
- 31-50: ⚠️ **Basic** - Acceptable for personal use only
- 51-70: ✅ **Standard** - Suitable for agency/small team
- 71-94: ✅ **Advanced** - Enterprise-grade security

---

**Security is ongoing, not one-time. Review this checklist monthly!**
