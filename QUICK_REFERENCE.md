# Quick Reference Card

Essential commands and configurations for your Obsidian sync setup.

---

## 🔑 Your Configuration

**Save these details securely in your password manager:**

```
CouchDB URL: https://couchdb.yourdomain.com
Database Name: obsidian
Username: admin
Password: [YOUR_GENERATED_PASSWORD]
Encryption Passphrase: [IF_ENABLED]
API Key (Local REST API): [YOUR_API_KEY]
```

---

## 🚀 Essential Commands

### CouchDB Management

```bash
# Check CouchDB status
docker ps | grep couchdb

# View logs (last 50 lines)
docker logs obsidian-couchdb --tail 50

# Follow logs in real-time
docker logs -f obsidian-couchdb

# Restart CouchDB
docker restart obsidian-couchdb

# Update CouchDB
cd /opt/obsidian-sync
docker-compose pull
docker-compose up -d

# Database info
curl -u admin:PASSWORD https://couchdb.yourdomain.com/_all_dbs
```

### Backup Commands

```bash
# Manual backup
docker exec obsidian-couchdb couchdb-backup \
  -u admin -p PASSWORD \
  -H localhost -d obsidian \
  -f /backup/obsidian-$(date +%Y%m%d).json

# Compact database (reduce size)
curl -X POST -u admin:PASSWORD \
  http://localhost:5984/obsidian/_compact \
  -H "Content-Type: application/json"
```

### Health Checks

```bash
# Test connectivity
curl https://couchdb.yourdomain.com

# Test authentication
curl -u admin:PASSWORD https://couchdb.yourdomain.com/_all_dbs

# Check disk space
df -h

# Check resource usage
docker stats obsidian-couchdb --no-stream
```

---

## 📱 Obsidian LiveSync Settings

### Desktop/Mobile Configuration

```
URI: https://couchdb.yourdomain.com/obsidian
Username: admin
Password: [YOUR_PASSWORD]
Database: obsidian
Device Name: [My Desktop | My iPhone]

Live Sync: ON
Sync on Save: ON
Periodic Sync: ON
Sync Interval: 30 seconds
```

### Encryption Settings (If Enabled)

```
Enable E2E Encryption: ON
Passphrase: [SAME_ON_ALL_DEVICES]
```

---

## 🤖 MCP Integration (Claude Desktop)

### Config File Location

**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`
**Mac:** `~/Library/Application Support/Claude/claude_desktop_config.json`
**Linux:** `~/.config/Claude/claude_desktop_config.json`

### Config Template

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-obsidian",
        "--vault-path",
        "C:\\path\\to\\vault",
        "--api-key",
        "YOUR_API_KEY",
        "--api-url",
        "http://127.0.0.1:27124"
      ]
    }
  }
}
```

### Test API

```bash
curl http://127.0.0.1:27124/ -H "Authorization: Bearer YOUR_API_KEY"
```

---

## 🔧 Common Fixes

### Force Sync

**In Obsidian:**
1. Settings → Self-hosted LiveSync
2. Click "Fetch" (download from server)
3. Click "Replicate now" (full sync)

### Reset Sync

**If sync completely broken:**
1. Disable LiveSync on all devices
2. Delete local `.obsidian/plugins/obsidian-livesync/` folder
3. Reinstall plugin
4. Reconfigure with same settings
5. Force sync

### Clear CouchDB Cache

```bash
# Stop CouchDB
docker stop obsidian-couchdb

# Clear cache (keeps data)
docker exec obsidian-couchdb rm -rf /opt/couchdb/data/.shards

# Start CouchDB
docker start obsidian-couchdb
```

---

## 🔐 Security Quick Checks

```bash
# SSL certificate expiry
echo | openssl s_client -connect couchdb.yourdomain.com:443 2>/dev/null | openssl x509 -noout -dates

# Failed login attempts
docker logs obsidian-couchdb | grep "401"

# Database size
docker exec obsidian-couchdb du -sh /opt/couchdb/data
```

---

## 📊 Monitoring

### Daily Checks

- [ ] Sync working on all devices? (check green dot)
- [ ] Any error messages in LiveSync logs?
- [ ] CouchDB container running? (`docker ps`)

### Weekly Checks

- [ ] Backups successful?
- [ ] Disk space OK? (`df -h`)
- [ ] Review CouchDB logs for errors

### Monthly Checks

- [ ] Update CouchDB image
- [ ] Update Obsidian apps
- [ ] Test backup restoration
- [ ] Run security audit

---

## 🆘 Emergency Procedures

### CouchDB Won't Start

```bash
# Check logs
docker logs obsidian-couchdb

# Restart Docker
systemctl restart docker
docker-compose up -d
```

### Lost Access

```bash
# Reset password
# Edit docker-compose.yml with new password
nano /opt/obsidian-sync/docker-compose.yml
docker-compose down
docker-compose up -d
```

### Full System Restore

```bash
# 1. Stop everything
docker-compose down -v

# 2. Restore from GitHub
git clone https://github.com/username/backup.git

# 3. Redeploy CouchDB
docker-compose up -d

# 4. Setup LiveSync on devices
# 5. Initial sync will restore everything
```

---

## 📱 Mobile App Tips

### iPhone Battery Optimization

```
Settings → Self-hosted LiveSync:
- Sync Interval: 60-120 seconds (reduce frequency)
- Disable "Sync on Save" (manual sync)

iOS Settings:
- Obsidian → Allow Background App Refresh
- Obsidian → Cellular Data → ON
```

### Android Battery

```
Android Settings:
- Apps → Obsidian → Battery
- Disable battery optimization
- Allow background activity
```

---

## 🔗 Quick Links

**Documentation:**
- Main README: [README.md](./README.md)
- Deployment: [COOLIFY_DEPLOYMENT_GUIDE.md](./COOLIFY_DEPLOYMENT_GUIDE.md)
- Sync Setup: [LIVESYNC_SETUP_GUIDE.md](./LIVESYNC_SETUP_GUIDE.md)
- MCP Setup: [MCP_INTEGRATION_GUIDE.md](./MCP_INTEGRATION_GUIDE.md)
- Security: [SECURITY_CHECKLIST.md](./SECURITY_CHECKLIST.md)
- Troubleshooting: [TROUBLESHOOTING_GUIDE.md](./TROUBLESHOOTING_GUIDE.md)

**External Resources:**
- LiveSync Plugin: https://github.com/vrtmrz/obsidian-livesync
- CouchDB Docs: https://docs.couchdb.org/
- Obsidian Forum: https://forum.obsidian.md/
- MCP Protocol: https://modelcontextprotocol.io/

---

## 💡 Pro Tips

**Sync Performance:**
- Larger sync interval = better battery life
- Exclude large files (Settings → Exclude patterns)
- Compact database monthly

**Security:**
- Use password manager for all credentials
- Enable IP allowlist for agency data
- Enable vault encryption for sensitive notes
- Test backups monthly

**Workflow:**
- Use tags for organization
- Daily notes via Calendar plugin
- Tasks via Kanban plugin
- Search with Dataview

**AI Integration:**
- Keep Obsidian open for API access
- Use specific note names for Claude queries
- Create "AI" folder for generated content

---

**Print this page for quick reference!**
