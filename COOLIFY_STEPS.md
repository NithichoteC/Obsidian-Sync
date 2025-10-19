# Coolify Deployment - Step by Step

## Choose: **Docker Compose**

---

## Step 1: Click "+ New Resource"

Top right corner

---

## Step 2: Select "Docker Compose"

NOT:
- ❌ Dockerfile
- ❌ Docker Image
- ❌ Git Public Repo

YES:
- ✅ **Docker Compose** ← Click this

---

## Step 3: Configure Source

**Source Type:** GitHub (or Git)

**Repository:** `NithichoteC/Obsidian-Sync`

**Branch:** `main`

**Docker Compose Location:** `docker-compose.yml`

Click "Check" or "Validate"

---

## Step 4: Add Environment Variables

Click "Environment Variables" tab

Add 2 variables:

**Variable 1:**
```
Name: COUCHDB_USER
Value: admin
```

**Variable 2:**
```
Name: COUCHDB_PASSWORD
Value: [paste strong password here]
```

**Generate password:**
```bash
openssl rand -base64 32
```

**SAVE THIS PASSWORD!** (password manager)

---

## Step 5: Configure Domain

**Domain:** `couchdb.yourdomain.com`

Replace `yourdomain.com` with your actual domain

**Enable:**
- ✅ Generate SSL Certificate
- ✅ Force HTTPS

---

## Step 6: Deploy

Click **"Deploy"** button

Wait 2-3 minutes

Watch logs - should see "CouchDB started"

---

## Step 7: Create Database

**Option A - Via Terminal:**

Coolify → Your service → "Execute Command"

Run:
```bash
curl -X PUT http://admin:YOUR_PASSWORD@localhost:5984/obsidian
```

**Option B - From your computer:**
```bash
curl -X PUT -u admin:YOUR_PASSWORD https://couchdb.yourdomain.com/obsidian
```

Expected response:
```json
{"ok":true}
```

---

## Step 8: Verify

**In browser:** `https://couchdb.yourdomain.com`

Should see:
```json
{"couchdb":"Welcome","version":"3.3.x"}
```

✅ **Done!** CouchDB running

---

## Next: Setup Obsidian

See: `LIVESYNC_SETUP_GUIDE.md`

---

## Troubleshooting

**Deploy fails?**
- Check environment variables correct
- Check domain DNS pointing to server
- Check logs for errors

**Can't access domain?**
- DNS not propagated yet (wait 5-10 mins)
- SSL generating (wait 2 mins)
- Check firewall allows 80, 443

**Database creation fails?**
- Check password matches exactly
- Try via Coolify terminal instead
- Check CouchDB container running
