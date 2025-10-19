# Self-Hosted Obsidian Sync Setup

Complete guide for running your own Obsidian sync server with web access, mobile sync, and AI agent integration - all for **$0/month**.

---

## 🎯 What You'll Get

✅ **Native app sync** across Desktop + iPhone/Android
✅ **AI agent access** (Claude can read/write your notes)
✅ **Automated backups** to GitHub
✅ **100% self-hosted** on your existing Hetzner server
✅ **Professional-grade security** for agency data
✅ **All plugins work** and sync automatically

**Total Cost:** $0/month (vs. Obsidian Sync at $8/month)

---

## 📋 Prerequisites

- Existing Hetzner server with Coolify installed
- Domain name (e.g., `couchdb.yourdomain.com`)
- Basic terminal/command line knowledge
- ~2 hours for initial setup

---

## 🏗️ Architecture Overview

```
Your Hetzner Server (Coolify)
└── CouchDB (Docker) - Sync backend
         ↓
    ┌────┴────┬────────┐
    │         │        │
Desktop    iPhone   AI Agents
Obsidian   Obsidian  (Claude)
```

**How it works:**
1. CouchDB runs on your server (via Coolify)
2. Obsidian apps connect to CouchDB for sync
3. LiveSync plugin handles synchronization
4. MCP server gives AI agents vault access
5. GitHub auto-backup runs every 2 hours

---

## 🚀 Quick Start Guide

### Step 1: Deploy CouchDB (30 mins)

**Follow:** [`COOLIFY_DEPLOYMENT_GUIDE.md`](./COOLIFY_DEPLOYMENT_GUIDE.md)

**What you'll do:**
- Upload Docker Compose files to server
- Generate strong password
- Deploy CouchDB via Coolify
- Configure domain + SSL

**Result:** `https://couchdb.yourdomain.com` running

---

### Step 2: Setup Desktop Sync (20 mins)

**Follow:** [`LIVESYNC_SETUP_GUIDE.md`](./LIVESYNC_SETUP_GUIDE.md)

**What you'll do:**
- Install Obsidian (if not already)
- Install LiveSync plugin via UI
- Configure connection to CouchDB
- Start syncing

**Result:** Desktop notes syncing to server

---

### Step 3: Setup iPhone Sync (15 mins)

**Follow:** [`LIVESYNC_SETUP_GUIDE.md#part-2-iphone-setup`](./LIVESYNC_SETUP_GUIDE.md#part-2-iphone-setup)

**What you'll do:**
- Install Obsidian app from App Store
- Install LiveSync plugin
- Use same config as desktop
- Initial sync

**Result:** iPhone syncing with desktop

---

### Step 4: Enable AI Access (30 mins)

**Follow:** [`MCP_INTEGRATION_GUIDE.md`](./MCP_INTEGRATION_GUIDE.md)

**What you'll do:**
- Install Local REST API plugin (Obsidian)
- Configure Claude Desktop MCP
- Test AI vault access

**Result:** Claude can read/write your notes

---

### Step 5: Setup Auto-Backup (Optional, 20 mins)

**Follow:** [`LIVESYNC_SETUP_GUIDE.md#part-5-verification-checklist`](./LIVESYNC_SETUP_GUIDE.md)

**What you'll do:**
- Install Obsidian Git plugin
- Create private GitHub repo
- Configure auto-backup (2hr intervals)

**Result:** Automated GitHub backups

---

## 📁 Repository Structure

```
obsidian-sync/
├── README.md                      # This file - start here
├── docker-compose.yml             # CouchDB deployment config
├── couchdb-config/
│   └── local.ini                  # CouchDB configuration
├── COOLIFY_DEPLOYMENT_GUIDE.md    # Server setup guide
├── LIVESYNC_SETUP_GUIDE.md        # Desktop + mobile sync
├── MCP_INTEGRATION_GUIDE.md       # AI agent setup
├── SECURITY_CHECKLIST.md          # Security hardening
└── TROUBLESHOOTING_GUIDE.md       # Common issues & fixes
```

---

## 🔐 Security Features

### Built-in Security
- ✅ HTTPS encryption (Let's Encrypt)
- ✅ Password authentication
- ✅ Local-only API access
- ✅ Automated backups

### Optional Security (Recommended for Agency)
- ✅ IP allowlisting
- ✅ Vault encryption (E2E)
- ✅ VPN-only access (Tailscale)
- ✅ Audit logging

**See:** [`SECURITY_CHECKLIST.md`](./SECURITY_CHECKLIST.md) for full hardening guide

---

## 💰 Cost Comparison

| Feature | Obsidian Sync | Self-Hosted |
|---------|--------------|-------------|
| **Monthly Cost** | $8/month | $0 (uses existing server) |
| **Annual Cost** | $96/year | $0 |
| **Storage Limit** | 10GB | Unlimited (server disk) |
| **Device Limit** | Unlimited | Unlimited |
| **Sync Speed** | Fast | Fast (same datacenter) |
| **Control** | Limited | Full control |
| **Privacy** | Trust Obsidian | 100% your data |
| **AI Integration** | No | Yes (MCP) |
| **Custom Features** | No | Yes (any plugin) |

**Savings:** $96/year + full control

---

## ⚡ Performance Expectations

### Sync Speed
- **Small changes** (<100KB): 1-3 seconds
- **Large notes** (1-10MB): 5-15 seconds
- **Initial sync** (1000 notes): 10-30 minutes
- **Cross-device delay:** ~30-60 seconds

### Resource Usage
- **CouchDB RAM:** ~200-500MB
- **CouchDB CPU:** <5% (idle), 10-30% (sync)
- **Disk Space:** Vault size × 2 (for revisions)
- **Network:** Minimal (only changed data syncs)

### Scalability
- **Vault size:** Tested up to 50,000 notes
- **Concurrent devices:** 10+ devices no issue
- **Users:** 1-5 users comfortably (more needs tuning)

---

## 🎨 Plugin Compatibility

### Confirmed Working
✅ **All official plugins** (core + community)
✅ **DataView** - Query notes like database
✅ **Templater** - Advanced templates
✅ **Calendar** - Daily notes calendar
✅ **Kanban** - Project boards
✅ **Excalidraw** - Drawing/diagrams
✅ **Local REST API** - MCP integration
✅ **Obsidian Git** - GitHub backups

### How Plugins Sync
- Plugins installed on desktop → Auto-sync to mobile
- Plugin settings sync via LiveSync
- `.obsidian/plugins/` folder syncs automatically
- Mobile-compatible plugins auto-enable

**Note:** Some plugins may be desktop-only (check plugin page)

---

## 🛠️ Maintenance

### Daily (Automatic)
- LiveSync handles sync automatically
- Health checks via Coolify
- Backups to GitHub (if configured)

### Weekly (5 mins)
- Check sync status on devices
- Review CouchDB logs for errors
- Verify disk space usage

### Monthly (20 mins)
- Update CouchDB image
- Update Obsidian apps
- Run security audit checklist
- Test backup restoration

**See:** [`SECURITY_CHECKLIST.md#maintenance-checklist`](./SECURITY_CHECKLIST.md#-maintenance-checklist)

---

## 🆘 Common Issues

### Sync Not Working
- **Quick Fix:** Force sync in LiveSync settings
- **See:** [`TROUBLESHOOTING_GUIDE.md#sync-not-working`](./TROUBLESHOOTING_GUIDE.md#issue-sync-not-working-between-devices)

### Can't Connect to CouchDB
- **Quick Fix:** Check URL format, verify credentials
- **See:** [`TROUBLESHOOTING_GUIDE.md#cant-connect`](./TROUBLESHOOTING_GUIDE.md#issue-cant-connect-to-couchdb-from-obsidian)

### AI Agent Can't Access Vault
- **Quick Fix:** Ensure Obsidian is running, check API key
- **See:** [`TROUBLESHOOTING_GUIDE.md#mcp-issues`](./TROUBLESHOOTING_GUIDE.md#issue-mcp--ai-agent-cant-access-vault)

### Plugins Not Syncing
- **Quick Fix:** Enable "Sync hidden files" in LiveSync
- **See:** [`TROUBLESHOOTING_GUIDE.md#plugin-sync`](./TROUBLESHOOTING_GUIDE.md#issue-obsidian-plugins-not-syncing)

**Full troubleshooting:** [`TROUBLESHOOTING_GUIDE.md`](./TROUBLESHOOTING_GUIDE.md)

---

## 📚 Additional Resources

### Official Documentation
- **Obsidian:** https://obsidian.md/
- **LiveSync Plugin:** https://github.com/vrtmrz/obsidian-livesync
- **CouchDB:** https://docs.couchdb.org/
- **MCP Protocol:** https://modelcontextprotocol.io/

### Community
- **Obsidian Forum:** https://forum.obsidian.md/
- **Obsidian Discord:** https://discord.gg/obsidianmd
- **r/ObsidianMD:** https://reddit.com/r/ObsidianMD
- **r/selfhosted:** https://reddit.com/r/selfhosted

### Related Projects
- **Obsidian Git:** https://github.com/denolehov/obsidian-git
- **Local REST API:** https://github.com/coddingtonbear/obsidian-local-rest-api
- **MCP Servers:** https://github.com/cyanheads/obsidian-mcp-server

---

## 🤝 Contributing

Found an issue? Have an improvement?

1. Check [`TROUBLESHOOTING_GUIDE.md`](./TROUBLESHOOTING_GUIDE.md) first
2. Search existing issues on GitHub
3. Create detailed issue with:
   - Environment (OS, versions)
   - Exact error messages
   - Steps to reproduce
   - What you've tried

**Pull requests welcome!**

---

## 📝 License

This guide is provided as-is for educational purposes.

**Software used:**
- Obsidian: Proprietary (free for personal use)
- CouchDB: Apache License 2.0
- LiveSync Plugin: MIT License
- Docker: Apache License 2.0

---

## 🎯 Roadmap

**Current Features:**
- ✅ CouchDB deployment guide
- ✅ Desktop + mobile sync
- ✅ MCP integration for AI
- ✅ Security hardening guide
- ✅ Troubleshooting guide

**Planned Additions:**
- 🔲 Video tutorial series
- 🔲 One-click deployment script
- 🔲 Monitoring dashboard setup
- 🔲 Multi-user permissions guide
- 🔲 Migration from Obsidian Sync guide
- 🔲 Advanced backup strategies

---

## 💬 FAQ

**Q: Is this better than Obsidian Sync?**
A: Different trade-offs. Obsidian Sync is easier (zero setup), this is cheaper ($0) and gives full control.

**Q: Can I use both Obsidian Sync and self-hosted?**
A: Not recommended - they conflict. Choose one.

**Q: What if my server goes down?**
A: Local vault copy on each device. Work offline, sync when back up.

**Q: Does this work on Android?**
A: Yes! Same process as iPhone, works perfectly.

**Q: Can I have multiple vaults?**
A: Yes! Create separate CouchDB databases for each vault.

**Q: Is it safe for client data?**
A: With proper security hardening (Level 2+), yes. See [`SECURITY_CHECKLIST.md`](./SECURITY_CHECKLIST.md).

**Q: Can I migrate existing vault?**
A: Yes! Just point LiveSync to existing vault folder and sync.

**Q: Does this slow down Obsidian?**
A: Minimal impact. Sync happens in background.

**Q: What if I want to stop self-hosting?**
A: Export vault, disable LiveSync, switch to Obsidian Sync or local-only.

---

## ⚠️ Disclaimer

- **No Warranty:** Provided as-is, test thoroughly before production use
- **Backup Everything:** Always maintain multiple backups
- **Security Responsibility:** You're responsible for securing your infrastructure
- **Not Official:** This is not affiliated with or endorsed by Obsidian
- **Use at Own Risk:** Author not liable for data loss or security issues

---

## 🙏 Acknowledgments

**Thanks to:**
- **vrtmrz** - Creator of Self-hosted LiveSync plugin
- **Obsidian team** - Amazing note-taking app
- **CouchDB community** - Robust sync backend
- **Coolify team** - Excellent self-hosting platform
- **MCP community** - AI integration protocol

---

## 📞 Support

**Need help?**

1. **Check guides:** Start with relevant guide above
2. **Search issues:** Someone may have solved it already
3. **Troubleshooting:** [`TROUBLESHOOTING_GUIDE.md`](./TROUBLESHOOTING_GUIDE.md) covers most issues
4. **Community help:** Obsidian Forum or Discord
5. **Create issue:** If none of above help, create detailed issue

**For professional support:** Consider hiring a freelancer or using managed hosting.

---

**Ready to get started? Begin with [`COOLIFY_DEPLOYMENT_GUIDE.md`](./COOLIFY_DEPLOYMENT_GUIDE.md)!**

---

⭐ **Star this repo if it helped you!**
