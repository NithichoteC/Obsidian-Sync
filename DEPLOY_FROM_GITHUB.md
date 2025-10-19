# Deploy from GitHub to Coolify

**Much easier method!** Deploy directly from your GitHub repository to Coolify.

---

## 🎯 Advantages of GitHub Method

✅ **One-click deploy** - Coolify pulls from GitHub automatically
✅ **Auto-updates** - Push to GitHub → Coolify rebuilds
✅ **Version control** - Track all configuration changes
✅ **Easy rollback** - Revert to previous versions
✅ **No manual file transfer** - No SSH/SCP needed
✅ **Team collaboration** - Multiple people can contribute

---

## 🚀 Quick Start (5 Minutes)

### Step 1: Push Files to GitHub

**Your repo:** `https://github.com/NithichoteC/Obsidian-Sync`

**From your local folder:**

```bash
cd "/mnt/e/AI&CODING/New folder/obsidian-sync"

# Initialize git (if not already)
git init

# Add GitHub remote
git remote add origin https://github.com/NithichoteC/Obsidian-Sync.git

# Add all files
git add .

# Commit
git commit -m "Initial commit - Obsidian sync setup"

# Push to GitHub
git push -u origin main
```

**Or if branch is called `master`:**
```bash
git push -u origin master
```

---

### Step 2: Deploy in Coolify

**Method A: Docker Compose (Recommended)**

1. **Login to Coolify** (`https://your-coolify-domain.com`)

2. **Create New Resource:**
   - Click "+ New Resource"
   - Select "Docker Compose"

3. **Configure Source:**
   - **Source Type:** GitHub
   - **Repository:** `NithichoteC/Obsidian-Sync`
   - **Branch:** `main` (or `master`)
   - **Docker Compose Location:** `docker-compose.yml`

4. **Environment Variables:**
   - Click "Add Environment Variable"
   - Add these (Coolify will inject into compose file):

   ```
   COUCHDB_USER=admin
   COUCHDB_PASSWORD=[GENERATE_STRONG_PASSWORD]
   ```

   **Generate password:**
   ```bash
   openssl rand -base64 32
   ```

5. **Domain Configuration:**
   - **Domain:** `couchdb.yourdomain.com`
   - **SSL:** Enable "Generate SSL Certificate"
   - **Force HTTPS:** Enable

6. **Deploy:**
   - Click "Deploy"
   - Wait 2-3 minutes
   - Check logs for success

---

### Step 3: Verify Deployment

**Test from browser:**
```
https://couchdb.yourdomain.com
```

**Expected:** JSON response with `{"couchdb":"Welcome"...}`

**Create database:**

```bash
# SSH into your server
ssh root@your-server-ip

# Create obsidian database
docker exec -it [CONTAINER_NAME] curl -X PUT \
  http://admin:YOUR_PASSWORD@localhost:5984/obsidian

# Verify
docker exec -it [CONTAINER_NAME] curl -u admin:YOUR_PASSWORD \
  http://localhost:5984/_all_dbs
```

**Or via Coolify terminal:**
1. Go to your service in Coolify
2. Click "Execute Command"
3. Run:
   ```bash
   curl -X PUT http://admin:PASSWORD@localhost:5984/obsidian
   ```

---

## 🔄 Update Workflow

**When you need to change configuration:**

1. **Edit files locally** (or on GitHub website)

2. **Commit and push:**
   ```bash
   git add .
   git commit -m "Update CouchDB config"
   git push
   ```

3. **Coolify auto-deploys** (if auto-deploy enabled)
   - Or manually: Click "Redeploy" in Coolify

4. **Done!** Changes live in 1-2 minutes

---

## 📝 Using Environment Variables (Better Security)

**Update your `docker-compose.yml` to use env vars:**

```yaml
version: '3.8'

services:
  couchdb:
    image: couchdb:3.3
    container_name: obsidian-couchdb
    restart: unless-stopped

    environment:
      # Coolify injects these from UI
      COUCHDB_USER: ${COUCHDB_USER:-admin}
      COUCHDB_PASSWORD: ${COUCHDB_PASSWORD}

    ports:
      - "5984:5984"

    volumes:
      - couchdb-data:/opt/couchdb/data
      - couchdb-config:/opt/couchdb/etc/local.d
      - ./couchdb-config/local.ini:/opt/couchdb/etc/local.d/local.ini

    networks:
      - obsidian-network

    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5984/_up"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 40s

volumes:
  couchdb-data:
    driver: local
  couchdb-config:
    driver: local

networks:
  obsidian-network:
    driver: bridge
```

**Benefits:**
- ✅ No passwords in GitHub (Coolify stores securely)
- ✅ Easy to change password (update in Coolify UI only)
- ✅ Different passwords for dev/prod

---

## 🔐 Security Best Practices

### What to Commit to GitHub

✅ **DO commit:**
- `docker-compose.yml` (with env vars, not hardcoded passwords)
- `couchdb-config/local.ini` (no secrets here)
- All documentation (`.md` files)
- `.gitignore`

❌ **DON'T commit:**
- `.env` files (in `.gitignore`)
- Actual passwords (use Coolify env vars)
- Data volumes
- Logs
- Backup files

### GitHub Repository Settings

**Make repository PRIVATE:**
1. Go to: https://github.com/NithichoteC/Obsidian-Sync/settings
2. Scroll to "Danger Zone"
3. Click "Change visibility"
4. Select "Make private"
5. Confirm

**Why private?**
- Contains your infrastructure setup
- May reveal server details
- Better safe than sorry for agency data

---

## 🎨 Coolify Auto-Deploy Setup

**Enable automatic deployments on push:**

1. **In Coolify service settings:**
   - Enable "Auto Deploy"
   - Enable "Watch Paths" (optional - only deploy on specific file changes)

2. **Configure webhook (automatic):**
   - Coolify creates GitHub webhook automatically
   - Every push to `main` → auto-deploys

3. **Test:**
   ```bash
   # Make a small change
   echo "# Test change" >> README.md
   git add README.md
   git commit -m "Test auto-deploy"
   git push

   # Watch Coolify logs - should auto-deploy
   ```

---

## 📊 GitHub Repository Structure

**Your repo should look like:**

```
Obsidian-Sync/
├── .gitignore
├── README.md
├── DEPLOY_FROM_GITHUB.md         # This file
├── COOLIFY_DEPLOYMENT_GUIDE.md
├── LIVESYNC_SETUP_GUIDE.md
├── MCP_INTEGRATION_GUIDE.md
├── SECURITY_CHECKLIST.md
├── TROUBLESHOOTING_GUIDE.md
├── QUICK_REFERENCE.md
├── docker-compose.yml
└── couchdb-config/
    └── local.ini
```

---

## 🔧 Alternative: Deploy with GitHub Actions (Advanced)

**Create `.github/workflows/deploy.yml`:**

```yaml
name: Deploy to Coolify

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger Coolify Deployment
        run: |
          curl -X POST ${{ secrets.COOLIFY_WEBHOOK_URL }}
```

**Setup:**
1. Get webhook URL from Coolify service
2. Add to GitHub Secrets (Settings → Secrets → Actions)
3. Push → GitHub Actions → Coolify deploys

---

## 🆘 Troubleshooting

### Coolify Can't Access GitHub

**If private repo:**
1. Coolify → Settings → GitHub Integration
2. Connect GitHub account
3. Grant access to repository

**Or use deploy key:**
1. GitHub repo → Settings → Deploy keys
2. Add Coolify's SSH public key
3. Enable "Allow write access" if needed

### Deploy Fails

**Check Coolify logs:**
1. Service → Deployment Logs
2. Look for errors:
   - File not found → Check paths
   - Permission denied → Check deploy key
   - Syntax error → Validate docker-compose.yml

### Environment Variables Not Working

**Verify in Coolify:**
1. Service → Environment Variables
2. Ensure variables defined
3. Check spelling matches `${VAR_NAME}` in compose file

---

## 🎯 Recommended Workflow

### Initial Setup (Once)
1. ✅ Push code to GitHub
2. ✅ Deploy to Coolify from GitHub
3. ✅ Configure environment variables in Coolify
4. ✅ Test deployment works

### Regular Updates
1. Edit files locally
2. Test locally (optional)
3. Commit and push to GitHub
4. Coolify auto-deploys (or click "Redeploy")
5. Verify changes live

### Secrets Management
1. Store passwords in Coolify UI (not GitHub)
2. Use environment variables in compose file
3. Never commit `.env` files

---

## 🚀 Next Steps

**After deployment:**

1. ✅ **Verify CouchDB accessible:** `https://couchdb.yourdomain.com`

2. ✅ **Create database:**
   ```bash
   curl -X PUT -u admin:PASSWORD https://couchdb.yourdomain.com/obsidian
   ```

3. ✅ **Proceed to LiveSync setup:**
   - Open `LIVESYNC_SETUP_GUIDE.md`
   - Configure Obsidian apps

4. ✅ **Enable auto-deploy:**
   - Coolify → Enable "Auto Deploy"
   - Future updates = just git push!

---

## 💡 Pro Tips

**Use GitHub Releases:**
- Tag stable versions: `git tag v1.0.0 && git push --tags`
- Deploy specific version in Coolify
- Easy rollback to previous releases

**Branch Strategy:**
- `main` → Production deployment
- `dev` → Testing (optional separate Coolify service)
- Feature branches → Local testing only

**Documentation in Repo:**
- Keep all docs up to date
- Use GitHub Issues for tracking problems
- Use GitHub Wiki for extended documentation

**Collaboration:**
- Add team members to GitHub repo
- They can submit PRs for improvements
- Review changes before deploying

---

## ✅ Checklist

**GitHub Setup:**
- [ ] Repository created: `NithichoteC/Obsidian-Sync`
- [ ] Repository set to PRIVATE
- [ ] All files pushed
- [ ] `.gitignore` prevents secrets being committed

**Coolify Setup:**
- [ ] Service created from GitHub source
- [ ] Environment variables configured (password)
- [ ] Domain configured with SSL
- [ ] Auto-deploy enabled
- [ ] Deployment successful

**Verification:**
- [ ] CouchDB accessible via domain
- [ ] Authentication works
- [ ] Database created
- [ ] SSL certificate valid

**Next:**
- [ ] Proceed to `LIVESYNC_SETUP_GUIDE.md`

---

**This is the professional way! Version control + automated deployment = 🎉**
