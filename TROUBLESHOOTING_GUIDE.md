# Troubleshooting Guide

Common issues and solutions for self-hosted Obsidian sync setup.

---

## 🔍 Quick Diagnostic Commands

**Check everything at once:**

```bash
# CouchDB status
docker ps | grep couchdb

# CouchDB logs (last 50 lines)
docker logs obsidian-couchdb --tail 50

# CouchDB connectivity
curl https://couchdb.yourdomain.com

# Database list
curl -u admin:PASSWORD https://couchdb.yourdomain.com/_all_dbs

# Disk space
df -h

# Container resource usage
docker stats obsidian-couchdb --no-stream
```

---

## 🚨 Common Issues & Solutions

### Issue: CouchDB Container Won't Start

**Symptoms:**
- Container exits immediately
- `docker ps` doesn't show `obsidian-couchdb`
- Error in Coolify deployment

**Diagnosis:**
```bash
docker logs obsidian-couchdb
docker inspect obsidian-couchdb
```

**Common Causes & Fixes:**

**1. Port Already in Use**
```bash
# Check what's using port 5984
sudo lsof -i :5984
# or
sudo netstat -tulpn | grep 5984

# Solution: Change port in docker-compose.yml
ports:
  - "5985:5984"  # Use different external port
```

**2. Volume Permission Errors**
```bash
# Fix volume permissions
docker-compose down
sudo chown -R 5984:5984 /var/lib/docker/volumes/obsidian-sync_couchdb-data
docker-compose up -d
```

**3. Invalid Configuration**
```bash
# Check config file syntax
cat couchdb-config/local.ini
# Ensure no typos, valid INI format

# Recreate with known-good config
rm couchdb-config/local.ini
# Copy from guide again
```

**4. Memory/Resource Limits**
```bash
# Check server resources
free -h
df -h

# Solution: Increase server resources or add swap
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

---

### Issue: Can't Connect to CouchDB from Obsidian

**Symptoms:**
- "Connection failed" in LiveSync
- "Unauthorized" errors
- Timeout errors

**Diagnosis Steps:**

**1. Test Basic Connectivity**
```bash
# From server
curl http://localhost:5984

# From local machine
curl https://couchdb.yourdomain.com

# Expected: {"couchdb":"Welcome",...}
```

**2. Test Authentication**
```bash
curl -u admin:PASSWORD https://couchdb.yourdomain.com/_all_dbs

# Expected: ["obsidian"]
# If fails: Wrong credentials
```

**3. Check DNS Resolution**
```bash
# From local machine
nslookup couchdb.yourdomain.com
ping couchdb.yourdomain.com

# Should resolve to your server IP
```

**Common Solutions:**

**Wrong URL Format:**
```
❌ Wrong: https://couchdb.yourdomain.com/
✅ Correct: https://couchdb.yourdomain.com/obsidian

❌ Wrong: http://couchdb.yourdomain.com (HTTP)
✅ Correct: https://couchdb.yourdomain.com (HTTPS)
```

**Wrong Credentials:**
- Double-check username is exactly `admin`
- Password matches `docker-compose.yml` exactly
- No extra spaces or quotes
- Case-sensitive!

**Firewall Blocking:**
```bash
# Check server firewall
sudo ufw status
# Ensure ports 80, 443 allowed

# Check Coolify proxy
# Verify service is exposed in Coolify settings
```

**SSL Certificate Issues:**
```bash
# Check SSL certificate
curl -v https://couchdb.yourdomain.com 2>&1 | grep -i certificate

# If expired, regenerate in Coolify
# Or use Let's Encrypt certbot
```

---

### Issue: Sync Not Working Between Devices

**Symptoms:**
- Changes on one device don't appear on another
- "Sync failed" errors
- Sync stuck at "Syncing..."

**Diagnosis:**

**1. Check Both Devices Connected**
- LiveSync icon shows green dot on both?
- Both show same database in settings?
- Both authenticated successfully?

**2. Check Sync Settings**
```
Settings → Self-hosted LiveSync:
- Live Sync: ON
- Sync on Save: ON
- Periodic Sync: ON
- Sync Interval: 30 (or higher)
```

**3. Check for Conflicts**
```
Settings → Self-hosted LiveSync → Show Logs
Look for: "conflict detected"
```

**Common Solutions:**

**Different Database Names:**
- Desktop: `obsidian`
- Mobile: `Obsidian` (capitalized)
- **Solution:** Ensure exact same database name on all devices

**Encryption Mismatch:**
- One device encrypted, other not
- Different encryption passphrases
- **Solution:** Same encryption settings on ALL devices

**Network Issues:**
```bash
# Test from mobile device (using browser):
https://couchdb.yourdomain.com

# If fails on mobile but works on desktop:
# - Check mobile data/WiFi connectivity
# - Try different network
# - Check carrier doesn't block custom domains
```

**Initial Sync Not Complete:**
- First sync can take 5-30 minutes for large vaults
- Don't interrupt first sync
- Keep app open until complete

**Force Sync:**
1. Settings → Self-hosted LiveSync
2. Click "Fetch" (downloads from server)
3. Click "Replicate now" (full two-way sync)
4. Wait for completion

---

### Issue: Obsidian Plugins Not Syncing

**Symptoms:**
- Plugin installed on desktop but not on mobile
- Plugin settings different between devices
- Some plugins missing after sync

**Diagnosis:**

**1. Check Plugin Folder Syncing**
```
Settings → Self-hosted LiveSync → Sync Settings:
- Sync hidden files: ON (important!)
- Exclude patterns: Should NOT include ".obsidian"
```

**2. Check Mobile Plugin Support**
- Not all plugins work on mobile
- Check plugin page for "Mobile supported" badge

**Common Solutions:**

**Enable Hidden File Sync:**
```
Settings → Self-hosted LiveSync → Sync Settings:
✅ Sync hidden files
✅ Sync config files

Then force sync:
- Click "Fetch" on mobile
- Restart Obsidian app
```

**Manual Plugin Enable (Mobile):**
- Some plugins sync but aren't auto-enabled
- Settings → Community plugins
- Find synced plugin
- Toggle ON manually

**Plugin Cache Issues:**
```
# On device with issues:
1. Settings → Community plugins
2. Disable ALL plugins
3. Force sync (Fetch)
4. Re-enable plugins
5. Restart Obsidian
```

**Clean Reinstall:**
```
# Desktop:
1. Backup vault
2. Delete .obsidian/plugins/plugin-name
3. Force sync (should download from mobile)
4. Or reinstall plugin fresh

# Mobile:
1. Close Obsidian
2. Delete app data (iOS: reinstall app)
3. Reinstall Obsidian
4. Setup LiveSync
5. Sync will restore plugins
```

---

### Issue: MCP / AI Agent Can't Access Vault

**Symptoms:**
- Claude says "I can't access your Obsidian vault"
- "Connection refused" errors
- MCP server not showing in Claude

**Diagnosis:**

**1. Check Local REST API Plugin**
```
Settings → Community plugins → Local REST API:
- Plugin enabled? Toggle should be ON
- API enabled? "Enable" should be ON
- Port: 27124 (default)
```

**2. Test API Manually**
```bash
# Replace YOUR_API_KEY with actual key
curl http://127.0.0.1:27124/ -H "Authorization: Bearer YOUR_API_KEY"

# Expected: JSON response with vault info
# If fails: API not running or wrong port
```

**3. Check Claude Config**
```
# Windows:
%APPDATA%\Claude\claude_desktop_config.json

# Mac:
~/Library/Application Support/Claude/claude_desktop_config.json

# Verify:
- File exists
- Valid JSON syntax
- Correct vault path
- Correct API key
```

**Common Solutions:**

**Obsidian Not Running:**
- Local REST API only works when Obsidian is open
- **Solution:** Launch Obsidian before using Claude

**Wrong Vault Path:**
```json
❌ Wrong: "vault": "C:\Users\Name\Vault"
✅ Correct: "vault": "C:\\Users\\Name\\Vault"

❌ Wrong: "vault": "~/Documents/Vault"
✅ Correct: "vault": "/Users/Name/Documents/Vault"
```

**API Key Mismatch:**
1. Settings → Local REST API → Show API Key
2. Copy exact key
3. Update Claude config
4. Restart Claude Desktop

**Port Conflict:**
```bash
# Check if port in use
lsof -i :27124

# Solution: Change port
Settings → Local REST API → Port: 27125
# Update Claude config with new port
```

**Claude Config Invalid:**
```bash
# Validate JSON syntax
cat claude_desktop_config.json | jq .

# If error, use online JSON validator
# Fix syntax errors
```

**Firewall Blocking Localhost:**
```bash
# Windows: Allow Obsidian in Windows Firewall
# Mac: System Preferences → Security → Firewall → Allow Obsidian
# Linux: Check iptables doesn't block localhost
```

---

### Issue: High CouchDB Resource Usage

**Symptoms:**
- Server CPU at 100%
- High memory usage
- Slow sync
- Container frequently restarting

**Diagnosis:**
```bash
# Check resource usage
docker stats obsidian-couchdb

# Check database size
docker exec obsidian-couchdb du -sh /opt/couchdb/data

# Check number of documents
curl -u admin:PASSWORD http://localhost:5984/obsidian | jq '.doc_count'
```

**Common Solutions:**

**Large Vault Optimization:**
```bash
# Compact database
curl -X POST -u admin:PASSWORD \
  http://localhost:5984/obsidian/_compact \
  -H "Content-Type: application/json"

# Wait for compaction to finish (check logs)
docker logs -f obsidian-couchdb
```

**Too Frequent Sync:**
```
Settings → Self-hosted LiveSync:
- Sync Interval: Increase to 60 or 120 seconds
- Batch size: Increase to 100
```

**Exclude Large Files:**
```
Settings → Self-hosted LiveSync → Sync Settings:
- Exclude patterns: Add:
  *.pdf
  *.mp4
  *.zip
  attachments/*
```

**Increase Container Resources:**
```yaml
# In docker-compose.yml, add:
services:
  couchdb:
    deploy:
      resources:
        limits:
          memory: 2G
          cpus: '2.0'
```

**Database Cleanup:**
```bash
# Delete old revisions
curl -X POST -u admin:PASSWORD \
  http://localhost:5984/obsidian/_purge \
  -H "Content-Type: application/json" \
  -d '{"purge":"cleanup"}'
```

---

### Issue: Backup Failures

**Symptoms:**
- Obsidian Git plugin errors
- Manual backups fail
- GitHub sync not working

**Common Solutions:**

**Git Plugin Not Installed:**
1. Settings → Community plugins → Browse
2. Search: "Obsidian Git"
3. Install and enable

**GitHub Authentication:**
```
Settings → Obsidian Git:
- Username: your-github-username
- Personal Access Token: ghp_xxxxx (create at github.com/settings/tokens)
- Repository: https://github.com/username/repo.git
```

**Large Files Error:**
```
# GitHub blocks files >100MB
# Solution: Use .gitignore

.obsidian/workspace
*.pdf
*.mp4
videos/*
```

**Merge Conflicts:**
```
# If backup fails with merge conflict:
1. Settings → Obsidian Git → "Pull"
2. Resolve conflicts manually
3. Settings → Obsidian Git → "Commit and Push"
```

---

### Issue: Mobile App Issues

**iPhone Specific:**

**Sync Not Working on Cellular:**
- iOS may block background sync on cellular
- **Solution:** Settings → Obsidian → Allow Cellular Data

**App Crashes:**
- Clear app cache: Delete and reinstall
- Check iOS version (update if old)
- Reduce sync frequency

**Background Sync Not Working:**
- iOS limits background activity
- **Solution:** Keep app open during initial sync
- Enable Background App Refresh for Obsidian

**Android Specific:**

**Battery Optimization Killing Sync:**
```
Settings → Apps → Obsidian → Battery:
- Disable battery optimization
- Allow background activity
```

**Storage Permission Issues:**
```
Settings → Apps → Obsidian → Permissions:
- Allow Storage access
```

---

## 🔧 Advanced Diagnostics

### View Full CouchDB Logs

```bash
# Last 100 lines
docker logs obsidian-couchdb --tail 100

# Follow logs (real-time)
docker logs -f obsidian-couchdb

# Filter for errors
docker logs obsidian-couchdb 2>&1 | grep -i error

# Save logs to file
docker logs obsidian-couchdb > couchdb-debug.log
```

### Database Health Check

```bash
# Database info
curl -u admin:PASSWORD http://localhost:5984/obsidian

# Check views
curl -u admin:PASSWORD http://localhost:5984/obsidian/_design_docs

# Verify compaction status
curl -u admin:PASSWORD http://localhost:5984/obsidian | jq '.compact_running'
```

### Network Diagnostics

```bash
# Test DNS
nslookup couchdb.yourdomain.com

# Test SSL
openssl s_client -connect couchdb.yourdomain.com:443

# Test from different locations
curl -I https://couchdb.yourdomain.com
# Try from: server, local machine, mobile hotspot
```

### Performance Profiling

```bash
# Measure sync time
time curl -u admin:PASSWORD https://couchdb.yourdomain.com/_all_dbs

# Check database size over time
watch -n 60 'docker exec obsidian-couchdb du -sh /opt/couchdb/data'

# Monitor sync activity
docker logs -f obsidian-couchdb | grep -i sync
```

---

## 📝 Getting Help

### Before Asking for Help

**Gather this information:**

1. **Environment:**
   - OS: Windows/Mac/Linux/iOS/Android
   - Obsidian version: Settings → About
   - LiveSync version: Settings → Community plugins → Self-hosted LiveSync

2. **Error Messages:**
   - Exact error text
   - Screenshots of errors
   - CouchDB logs (last 50 lines)

3. **Configuration:**
   - CouchDB URL (hide sensitive parts)
   - Sync settings (screenshot)
   - Encryption enabled? Yes/No

4. **What You've Tried:**
   - List troubleshooting steps already attempted
   - Results of diagnostic commands

### Where to Get Help

**Community Support:**
- Obsidian Forum: https://forum.obsidian.md/
- Obsidian Discord: https://discord.gg/obsidianmd
- Reddit r/ObsidianMD: https://reddit.com/r/ObsidianMD

**Official Documentation:**
- LiveSync Plugin: https://github.com/vrtmrz/obsidian-livesync
- CouchDB Docs: https://docs.couchdb.org/
- Obsidian API: https://docs.obsidian.md/

**GitHub Issues:**
- LiveSync Issues: https://github.com/vrtmrz/obsidian-livesync/issues
- Search existing issues first
- Provide detailed info when creating new issue

---

## 🆘 Emergency Procedures

### If Everything Breaks

**Nuclear Option - Full Reset:**

```bash
# 1. Backup current state
docker exec obsidian-couchdb couchdb-backup -u admin -p PASSWORD \
  -H localhost -d obsidian -f /backup/emergency-backup.json

# 2. Stop and remove everything
docker-compose down -v

# 3. Clean slate
rm -rf couchdb-data/ couchdb-config/

# 4. Recreate from guides
# Follow COOLIFY_DEPLOYMENT_GUIDE.md from start

# 5. Restore from backup (if needed)
# Or start fresh and sync from local vault
```

**Restore from GitHub Backup:**

```bash
# 1. Clone backup repository
git clone https://github.com/username/obsidian-backup.git

# 2. Copy to Obsidian vault
cp -r obsidian-backup/* /path/to/vault/

# 3. Reopen Obsidian
# 4. Setup LiveSync fresh
# 5. New sync will upload to CouchDB
```

---

## 📞 Support Escalation Path

**Level 1: Self-Help (This Guide)**
- Common issues with quick fixes
- Diagnostic commands
- Configuration checks

**Level 2: Community Support**
- Obsidian Forum for general questions
- Discord for real-time help
- Reddit for advice from experienced users

**Level 3: GitHub Issues**
- Plugin-specific bugs
- Feature requests
- Detailed technical issues

**Level 4: Professional Help**
- Hire freelancer (Upwork, Fiverr)
- Managed hosting provider
- Professional IT support

---

**Most issues are quick fixes! Check this guide first before asking for help.**
