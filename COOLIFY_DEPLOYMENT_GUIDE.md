# Coolify Deployment Guide - Obsidian CouchDB Sync

## Prerequisites

- Coolify installed and running on Hetzner
- Domain name pointing to your server (e.g., `couchdb.yourdomain.com`)
- SSH access to your server

---

## Step 1: Prepare Files on Server

**SSH into your Hetzner server:**

```bash
ssh root@your-server-ip
```

**Create deployment directory:**

```bash
mkdir -p /opt/obsidian-sync
cd /opt/obsidian-sync
```

**Upload the files:**

Transfer these files to `/opt/obsidian-sync/`:
- `docker-compose.yml`
- `couchdb-config/local.ini`

**OR** create them directly:

```bash
# Create config directory
mkdir -p couchdb-config

# Create local.ini (copy content from provided file)
nano couchdb-config/local.ini

# Create docker-compose.yml (copy content from provided file)
nano docker-compose.yml
```

---

## Step 2: Generate Strong Password

**Generate a secure password:**

```bash
openssl rand -base64 32
```

**Copy the output** and replace `CHANGE_THIS_TO_STRONG_PASSWORD_32_CHARS` in `docker-compose.yml`

**Edit docker-compose.yml:**

```bash
nano docker-compose.yml
```

Change this line:
```yaml
COUCHDB_PASSWORD: CHANGE_THIS_TO_STRONG_PASSWORD_32_CHARS
```

To:
```yaml
COUCHDB_PASSWORD: your-generated-password-here
```

**Save:** `Ctrl+X`, then `Y`, then `Enter`

**IMPORTANT:** Save this password securely! You'll need it for Obsidian setup.

---

## Step 3: Deploy in Coolify

### Option A: Using Coolify UI (Recommended)

1. **Login to Coolify** (`https://your-coolify-domain.com`)

2. **Create New Resource:**
   - Click "+ New Resource"
   - Select "Docker Compose"

3. **Configure Service:**
   - **Name:** `obsidian-couchdb`
   - **Server:** Select your Hetzner server
   - **Destination:** Default

4. **Paste Docker Compose:**
   - Copy entire content from `docker-compose.yml`
   - Paste into "Docker Compose" field
   - Ensure password is changed!

5. **Configure Domain:**
   - Add domain: `couchdb.yourdomain.com`
   - Enable "Generate SSL Certificate" (Let's Encrypt)
   - Enable "Force HTTPS Redirect"

6. **Environment Variables (if not in compose file):**
   - `COUCHDB_USER`: `admin`
   - `COUCHDB_PASSWORD`: `your-generated-password`

7. **Deploy:**
   - Click "Deploy"
   - Wait 2-3 minutes for deployment

### Option B: Using Docker Directly (Alternative)

```bash
cd /opt/obsidian-sync
docker-compose up -d
```

Then configure reverse proxy in Coolify manually.

---

## Step 4: Verify Deployment

**Check container status:**

```bash
docker ps | grep couchdb
```

You should see: `obsidian-couchdb` running

**Check CouchDB is responding:**

```bash
curl http://localhost:5984
```

Expected response:
```json
{"couchdb":"Welcome","version":"3.3.x"}
```

**Test authentication:**

```bash
curl -u admin:your-password http://localhost:5984/_all_dbs
```

Should return: `[]` (empty array, this is correct)

---

## Step 5: Configure CouchDB Database

**Create Obsidian database:**

```bash
curl -X PUT http://admin:your-password@localhost:5984/obsidian
```

Expected response:
```json
{"ok":true}
```

**Verify database exists:**

```bash
curl -u admin:your-password http://localhost:5984/_all_dbs
```

Should return: `["obsidian"]`

---

## Step 6: Test External Access

**Test HTTPS access from your local machine:**

```bash
curl https://couchdb.yourdomain.com
```

Expected response:
```json
{"couchdb":"Welcome","version":"3.3.x"}
```

**Test authentication:**

```bash
curl -u admin:your-password https://couchdb.yourdomain.com/_all_dbs
```

Should return: `["obsidian"]`

---

## Step 7: Security Configuration (Optional but Recommended)

### Enable IP Allowlist in Coolify:

1. Go to your `obsidian-couchdb` service in Coolify
2. Navigate to "Security" or "Settings"
3. Add allowed IPs:
   - Your home IP
   - Your office IP
   - Your mobile carrier IP range (if needed)

### Find Your Current IP:

```bash
curl ifconfig.me
```

---

## Troubleshooting

### Container won't start:

```bash
# Check logs
docker logs obsidian-couchdb

# Common issues:
# 1. Port 5984 already in use
# 2. Volume permission errors
# 3. Invalid configuration syntax
```

### Cannot access via domain:

```bash
# Verify DNS
nslookup couchdb.yourdomain.com

# Check Coolify reverse proxy
# Ensure SSL certificate generated
# Check Coolify logs for errors
```

### Authentication fails:

```bash
# Reset password in docker-compose.yml
docker-compose down
# Edit password
docker-compose up -d

# Verify credentials
curl -u admin:new-password http://localhost:5984/_all_dbs
```

### "unauthorized" errors:

- Check `require_valid_user = true` in `local.ini`
- Verify credentials match exactly
- Try recreating the container

---

## Important Information for Next Steps

**Save these details securely:**

- **CouchDB URL:** `https://couchdb.yourdomain.com`
- **Username:** `admin`
- **Password:** `your-generated-password`
- **Database Name:** `obsidian`

You'll need these for configuring Obsidian LiveSync plugin!

---

## Maintenance

### Backup CouchDB data:

```bash
# Create backup
docker exec obsidian-couchdb couchdb-backup -u admin -p your-password -H localhost -d obsidian -f /opt/couchdb/data/backup.json

# Copy to host
docker cp obsidian-couchdb:/opt/couchdb/data/backup.json ./backup-$(date +%Y%m%d).json
```

### Update CouchDB:

```bash
cd /opt/obsidian-sync
docker-compose pull
docker-compose up -d
```

### View logs:

```bash
docker logs -f obsidian-couchdb
```

---

## Next Step

✅ CouchDB deployed and running!

**Proceed to:** `LIVESYNC_SETUP_GUIDE.md` to configure Obsidian apps.
