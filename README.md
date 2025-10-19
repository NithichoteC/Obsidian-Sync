# Self-Hosted Obsidian Sync

Run your own Obsidian sync server with mobile sync and AI agent integration - **$0/month**.

---

## 🎯 What You Get

✅ Native app sync across Desktop + iPhone/Android
✅ AI agent access (Claude can read/write your notes)
✅ 100% self-hosted on your Hetzner server
✅ Professional-grade security for agency data
✅ All plugins work and sync automatically

**Cost:** $0/month (vs. Obsidian Sync at $8/month = **$96/year savings**)

---

## 🏗️ Architecture

```
Hetzner Server (Coolify)
└── CouchDB (Docker) - Sync backend
         ↓
    ┌────┴────┬────────┐
    │         │        │
Desktop    iPhone   AI Agents
Obsidian   Obsidian  (Claude)
```

---

## 🚀 Quick Start

### 1. Deploy CouchDB to Coolify (5 mins)

**In Coolify Dashboard:**

1. **New Resource** → **Docker Compose**

2. **Source:** GitHub
   - Repository: `NithichoteC/Obsidian-Sync`
   - Branch: `main`
   - Docker Compose: `docker-compose.yml`

3. **Environment Variables:**
   ```
   COUCHDB_USER=admin
   COUCHDB_PASSWORD=[generate: openssl rand -base64 32]
   ```
   **Save this password securely!**

4. **Domain:**
   - Domain: `couchdb.yourdomain.com`
   - SSL: Enable "Generate SSL Certificate"
   - Force HTTPS: Enable

5. **Deploy** → Wait 2-3 mins

6. **Create Database:**
   ```bash
   curl -X PUT -u admin:YOUR_PASSWORD https://couchdb.yourdomain.com/obsidian
   ```

**Verify:** Visit `https://couchdb.yourdomain.com` → Should see JSON response

---

### 2. Setup Obsidian Apps

**Desktop + iPhone setup:**
👉 **[LIVESYNC_SETUP_GUIDE.md](./LIVESYNC_SETUP_GUIDE.md)**

**Quick version:**
1. Install "Self-hosted LiveSync" plugin
2. Configure:
   ```
   URI: https://couchdb.yourdomain.com/obsidian
   Username: admin
   Password: [your password]
   ```
3. Enable Live Sync → Start syncing

---

### 3. Enable AI Access (Optional)

**Claude integration:**
👉 **[MCP_INTEGRATION_GUIDE.md](./MCP_INTEGRATION_GUIDE.md)**

**Quick version:**
1. Install "Local REST API" plugin in Obsidian
2. Configure Claude Desktop MCP settings
3. Claude can now read/write your notes

---

## 📁 Repository Files

```
Obsidian-Sync/
├── README.md                    # This file
├── docker-compose.yml           # CouchDB deployment
├── couchdb-config/local.ini     # CouchDB settings
├── .env.example                 # Environment template
├── LIVESYNC_SETUP_GUIDE.md      # Detailed Obsidian sync setup
├── MCP_INTEGRATION_GUIDE.md     # AI agent integration
├── SECURITY_CHECKLIST.md        # Security hardening guide
└── TROUBLESHOOTING_GUIDE.md     # Common issues & fixes
```

---

## 🔐 Security

**Built-in:**
- ✅ HTTPS encryption (Let's Encrypt)
- ✅ Password authentication
- ✅ Environment variables (no passwords in code)

**Recommended for Agency Data:**
- Add IP allowlisting in Coolify
- Enable vault encryption in LiveSync
- See: [SECURITY_CHECKLIST.md](./SECURITY_CHECKLIST.md)

---

## 🔄 Updates

**Enable auto-deploy in Coolify:**
- Settings → Auto Deploy: ON
- Every `git push` → auto-deploys to server

**Update configuration:**
```bash
git add .
git commit -m "Update config"
git push
```

Coolify automatically redeploys!

---

## 🆘 Common Issues

**Sync not working?**
→ [TROUBLESHOOTING_GUIDE.md](./TROUBLESHOOTING_GUIDE.md)

**Can't connect to CouchDB?**
- Check URL format: `https://domain.com/obsidian` (no trailing slash)
- Verify password matches exactly
- Test: `curl -u admin:PASSWORD https://couchdb.yourdomain.com/_all_dbs`

**Plugins not syncing?**
- Enable "Sync hidden files" in LiveSync settings
- Force sync: Settings → LiveSync → "Fetch"

---

## 📚 Detailed Guides

- **[LIVESYNC_SETUP_GUIDE.md](./LIVESYNC_SETUP_GUIDE.md)** - Complete Obsidian sync setup
- **[MCP_INTEGRATION_GUIDE.md](./MCP_INTEGRATION_GUIDE.md)** - AI agent integration
- **[SECURITY_CHECKLIST.md](./SECURITY_CHECKLIST.md)** - Security hardening
- **[TROUBLESHOOTING_GUIDE.md](./TROUBLESHOOTING_GUIDE.md)** - Common fixes

---

## 💰 Cost Comparison

| Feature | Obsidian Sync | Self-Hosted |
|---------|--------------|-------------|
| Monthly Cost | $8 | $0 |
| Annual Cost | $96 | $0 |
| Storage | 10GB | Unlimited |
| Control | Limited | Full |
| AI Integration | No | Yes |

---

## 📝 License

**Software Used:**
- Obsidian: Proprietary (free for personal use)
- CouchDB: Apache 2.0
- LiveSync Plugin: MIT
- Docker: Apache 2.0

---

## 🙏 Credits

- **vrtmrz** - Self-hosted LiveSync plugin
- **Obsidian team** - Amazing note-taking app
- **CouchDB community** - Sync backend
- **Coolify team** - Self-hosting platform

---

**Questions?** Open an issue or check the detailed guides above.
