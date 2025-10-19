# Hetzner Firewall Setup for CouchDB Security

## ⚠️ IMPORTANT: Firewall Protection Required

CouchDB is now accessible on port 5984. **You MUST configure firewall rules** to prevent unauthorized access.

---

## Step 1: Get Your Current IP Address

Visit: https://whatismyip.com/

Copy your IP address (e.g., `123.45.67.89`)

---

## Step 2: Configure Hetzner Cloud Firewall

### Option A: Via Hetzner Cloud Console (Recommended)

1. **Log in to Hetzner Cloud Console**
   - https://console.hetzner.cloud/

2. **Navigate to Firewalls**
   - Select your project
   - Click "Firewalls" in left sidebar
   - Click "Create Firewall" (or edit existing firewall)

3. **Add Inbound Rule for CouchDB**
   - **Name**: `Allow CouchDB from My IP`
   - **Direction**: Inbound
   - **Source**: `YOUR_IP/32` (replace with your IP from Step 1)
     - Example: `123.45.67.89/32`
   - **Protocol**: TCP
   - **Port**: `5984`
   - **Action**: Allow

4. **Apply Firewall to Your Server**
   - Select your Coolify server
   - Apply the firewall rules

### Option B: Via SSH (Alternative)

If you prefer using `ufw` firewall on the server:

```bash
# SSH into your Hetzner server
ssh root@YOUR_SERVER_IP

# Allow only your IP to access port 5984
sudo ufw allow from YOUR_IP to any port 5984 proto tcp

# Verify the rule
sudo ufw status

# Enable UFW if not already enabled
sudo ufw enable
```

---

## Step 3: Test Access

### Test from Your Computer

```bash
curl http://YOUR_SERVER_IP:5984
```

**Expected response:**
```json
{"couchdb":"Welcome","version":"3.3.3","git_sha":"40afbcfc7","uuid":"...","features":["access-ready","partitioned","pluggable-storage-engines","reshard","scheduler"],"vendor":{"name":"The Apache Software Foundation"}}
```

### Test from Another IP (Should Fail)

From a different network (e.g., mobile hotspot), try:
```bash
curl http://YOUR_SERVER_IP:5984
```

**Expected**: Connection timeout or refused

---

## Step 4: Configure Obsidian LiveSync

### Desktop App

1. **Install Obsidian LiveSync Plugin**
   - Settings → Community Plugins → Browse
   - Search "Self-hosted LiveSync"
   - Install and Enable

2. **Configure Remote Database**
   - Open LiveSync settings
   - Remote Database Configuration:
     - **URI**: `http://YOUR_SERVER_IP:5984`
     - **Database Name**: `obsidian-vault` (or your choice)
     - **Username**: `admin` (from COUCHDB_USER)
     - **Password**: Your COUCHDB_PASSWORD
     - **End to End Encryption**: Enable (recommended)

3. **Test Connection**
   - Click "Test Database Connection"
   - Should show success

4. **Enable Sync**
   - Toggle "LiveSync" to ON
   - Choose sync options (Live Sync recommended)

### iPhone App

1. **Install Obsidian from App Store**

2. **Install LiveSync Plugin**
   - Same as desktop

3. **Configure Same Remote Database**
   - Use same settings as desktop
   - Same URI, database name, credentials

---

## Security Best Practices

### 1. **Use Strong Password**
Your COUCHDB_PASSWORD should be:
- At least 32 characters
- Include uppercase, lowercase, numbers, symbols
- Use a password manager

### 2. **Enable End-to-End Encryption**
In Obsidian LiveSync settings:
- Enable E2E encryption
- Use a strong passphrase
- Your vault data is encrypted before leaving your device

### 3. **Update Firewall When Your IP Changes**
If your home IP address changes:
1. Check new IP: https://whatismyip.com/
2. Update Hetzner firewall rule with new IP
3. Or use dynamic IP range if available

### 4. **Monitor Access Logs**
Check CouchDB logs regularly:
```bash
# In Coolify UI
Logs → Select CouchDB service
```

Look for unexpected access attempts.

### 5. **Consider VPN for Mobile Access**
If you travel and need access:
- Set up WireGuard VPN on your server
- Connect to VPN before syncing
- More secure than opening firewall to multiple IPs

---

## Troubleshooting

### "Connection Refused" Error

**Check firewall rules:**
```bash
# On your Hetzner server
sudo ufw status
```

Verify rule exists for your IP and port 5984.

### "Unauthorized" Error

**Check credentials:**
- Username matches COUCHDB_USER (default: admin)
- Password matches COUCHDB_PASSWORD
- Check Coolify → Environment Variables for correct values

### "Database Does Not Exist" Error

**Create database manually:**
```bash
curl -X PUT http://admin:PASSWORD@YOUR_SERVER_IP:5984/obsidian-vault
```

Replace `PASSWORD` with your actual COUCHDB_PASSWORD.

### Sync Works at Home But Not Away

**Your IP changed:**
- Mobile networks use different IPs
- Either: Update firewall to allow mobile IP
- Or: Set up VPN (recommended)

---

## Advanced: Multiple Device IPs

If you have multiple locations (home, office, etc.):

### Option 1: Add Multiple Rules

In Hetzner firewall, create separate rules:
- Rule 1: Home IP → Allow 5984
- Rule 2: Office IP → Allow 5984
- Rule 3: Mobile IP → Allow 5984

### Option 2: Use VPN (Better)

Install WireGuard on your server:
- Only allow VPN subnet to access 5984
- Connect to VPN from any location
- Access CouchDB through VPN tunnel

---

## Backup Recommendations

1. **Automated Backups**
   - Configure in Coolify or use cron job
   - Backup to Hetzner Object Storage or S3

2. **Local Git Backup**
   - Enable Obsidian Git plugin
   - Push to private GitHub repo
   - Redundant backup method

3. **Export Vault Regularly**
   - Manual export as ZIP
   - Store on external drive

---

## Cost Considerations

### Current Setup (Free)
- ✅ CouchDB hosted on existing Hetzner server
- ✅ No additional monthly cost
- ✅ Uses server resources you already pay for

### Future Scaling (If Needed)
- Hetzner Object Storage: ~€0.005/GB/month (if you add backups)
- Larger server: Only if current server becomes slow

---

## Support & Resources

- **CouchDB Docs**: https://docs.couchdb.org/
- **Obsidian LiveSync**: https://github.com/vrtmrz/obsidian-livesync
- **Hetzner Firewall**: https://docs.hetzner.com/cloud/firewalls/
- **This Setup**: Optimized for zero additional cost ✨
