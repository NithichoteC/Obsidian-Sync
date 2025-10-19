# Obsidian LiveSync Setup Guide

Complete guide for syncing Desktop + iPhone via your self-hosted CouchDB.

---

## Prerequisites

✅ CouchDB deployed and accessible via `https://couchdb.yourdomain.com`
✅ You have: URL, username (`admin`), password, database name (`obsidian`)

---

## Part 1: Desktop Setup (Windows/Mac/Linux)

### Step 1: Install/Open Obsidian

**Download:** https://obsidian.md/download (if not installed)

**Create or open your vault:**
- Open Obsidian
- Create new vault OR open existing vault
- Choose a location on your computer

### Step 2: Enable Community Plugins

1. **Open Settings:** Click gear icon (⚙️) or press `Ctrl/Cmd + ,`
2. **Navigate:** Settings → Community plugins
3. **Turn off Safe Mode:** Click "Turn off Safe mode" button
4. **Confirm:** Click "Turn off Safe mode" in popup

### Step 3: Install Self-hosted LiveSync Plugin

1. **Browse plugins:** Click "Browse" button
2. **Search:** Type `Self-hosted LiveSync`
3. **Find plugin:** Look for "Self-hosted LiveSync" by vrtmrz
4. **Install:** Click "Install" button
5. **Enable:** Toggle switch to enable the plugin

### Step 4: Configure LiveSync Plugin

**Open plugin settings:**
- Settings → Community plugins → Self-hosted LiveSync → Options (gear icon)

**Setup Wizard (Recommended):**

1. **Click "Setup wizard"** button

2. **Choose setup type:**
   - Select "Set up with existing database"
   - Click "Next"

3. **Remote Database Configuration:**
   - **URI:** `https://couchdb.yourdomain.com/obsidian`
   - **Username:** `admin`
   - **Password:** `your-generated-password`
   - **Device Name:** `My Desktop` (or any name you want)
   - Click "Test Connection"
   - Should see: ✅ "Connection successful"

4. **Sync Settings:**
   - **Live Sync:** Enable (toggle ON)
   - **Sync on Save:** Enable (toggle ON)
   - **Periodic Sync:** Enable (toggle ON)
   - **Sync Interval:** 30 seconds (default is fine)
   - Click "Next"

5. **End-to-End Encryption (Optional but Recommended):**
   - **Enable encryption:** Toggle ON
   - **Passphrase:** Enter a strong passphrase (save it securely!)
   - **Important:** You'll need this passphrase on ALL devices
   - Click "Next"

6. **Complete Setup:**
   - Review settings
   - Click "Apply"
   - Click "Start Sync"

**Manual Configuration (if wizard doesn't work):**

1. **Remote Configuration:**
   ```
   URI: https://couchdb.yourdomain.com/obsidian
   Username: admin
   Password: your-generated-password
   Database name: obsidian
   ```

2. **Sync Settings:**
   - Enable: Live Sync
   - Enable: Sync on Save
   - Enable: Periodic Sync
   - Sync Interval: 30

3. **Advanced Settings:**
   - Batch database update: 50
   - Batch size: 50
   - Use dynamic iterations: ON

4. **Save and Start Sync:**
   - Click "Save"
   - Click "Replicate now" to start initial sync

### Step 5: Verify Sync is Working

**Check sync status:**
- Look for LiveSync icon in bottom-right status bar
- Should show: 🟢 Green dot = syncing
- Hover over icon to see sync status

**Test sync:**
1. Create a new note
2. Type some text
3. Wait 30 seconds
4. Check LiveSync icon shows sync activity

**View sync logs:**
- Settings → Self-hosted LiveSync → Show Logs
- Should see successful sync messages

---

## Part 2: iPhone Setup

### Step 1: Install Obsidian App

1. Open **App Store**
2. Search: `Obsidian`
3. Download: "Obsidian - Connected Notes"
4. Install and open app

### Step 2: Create/Open Vault

**Option A: Create New Vault (Recommended)**
1. Tap "Create new vault"
2. Name: Same name as desktop vault
3. Storage: iCloud or Local (your choice)
4. Tap "Create"

**Option B: Wait for Sync**
- Skip vault creation
- Install LiveSync plugin first
- Vault will sync automatically from server

### Step 3: Enable Community Plugins

1. Tap **Settings** icon (⚙️) bottom-right
2. Tap "**Community plugins**"
3. Tap "**Turn off Safe mode**"
4. Confirm by tapping "Turn off Safe mode"

### Step 4: Install Self-hosted LiveSync Plugin

1. Tap "**Browse**"
2. Search: `Self-hosted LiveSync`
3. Tap plugin: "Self-hosted LiveSync"
4. Tap "**Install**"
5. Tap "**Enable**" (toggle switch)

### Step 5: Configure LiveSync (EXACT SAME as Desktop)

**Important:** Use IDENTICAL configuration as desktop!

1. **Go to plugin settings:**
   - Settings → Community plugins → Self-hosted LiveSync (tap gear icon)

2. **Remote Configuration:**
   ```
   URI: https://couchdb.yourdomain.com/obsidian
   Username: admin
   Password: your-generated-password
   Database name: obsidian
   Device name: My iPhone
   ```

3. **Encryption (if you enabled on desktop):**
   - Toggle "Enable encryption": ON
   - Enter SAME passphrase as desktop
   - **Critical:** Must match exactly!

4. **Sync Settings:**
   - Live Sync: ON
   - Sync on Save: ON
   - Periodic Sync: ON
   - Sync Interval: 30

5. **Tap "Save"**

6. **Tap "Fetch" or "Replicate now"**

### Step 6: Initial Sync

**First sync will download all notes from server:**
- May take 5-30 minutes depending on vault size
- Keep app open during first sync
- Watch progress in LiveSync status

**Check sync status:**
- LiveSync icon in status bar
- Should show: 🟢 Green dot
- Tap icon to see sync details

**Verify notes synced:**
- Browse your vault
- All notes from desktop should appear
- Create test note on iPhone
- Check it appears on desktop after ~30 seconds

---

## Part 3: Multi-Device Sync Workflow

### Normal Operation

**Desktop changes:**
1. Edit note on desktop → Auto-saves
2. LiveSync syncs to CouchDB (~30s)
3. iPhone pulls changes (~30s)
4. Total delay: ~1 minute

**iPhone changes:**
1. Edit note on iPhone → Auto-saves
2. LiveSync syncs to CouchDB (~30s)
3. Desktop pulls changes (~30s)
4. Total delay: ~1 minute

### Conflict Resolution

**If editing same note on both devices:**
- LiveSync detects conflict
- Creates conflict file: `note-conflict-TIMESTAMP.md`
- Original note shows latest change
- Review and merge conflicts manually

**Best practice:**
- Finish editing on one device before switching
- Let sync complete before opening same note elsewhere

---

## Part 4: Plugin Installation & Sync

### How Plugins Sync

**Plugins sync automatically via LiveSync:**
- Install plugin on desktop → Syncs to iPhone
- Plugin configuration syncs
- Plugin files sync to `.obsidian/plugins/` folder

### Installing Additional Plugins (Desktop)

1. Settings → Community plugins → Browse
2. Search and install plugins:
   - **Dataview** (query notes like database)
   - **Templater** (advanced templates)
   - **Calendar** (daily notes calendar)
   - **Kanban** (project boards)
   - **Obsidian Git** (GitHub backups - optional)

3. Configure plugins as desired
4. Wait for sync (~1-2 minutes)
5. Check iPhone - plugins appear automatically

### Installing Additional Plugins (iPhone)

**Most plugins auto-install from desktop sync.**

**If manual install needed:**
1. Settings → Community plugins → Browse
2. Search and install
3. Will sync back to desktop

### Mobile-Specific Plugin Notes

**Some plugins may not work on mobile:**
- Check plugin description for "Mobile support"
- Desktop-only plugins won't break mobile
- Mobile will just ignore them

---

## Part 5: Verification Checklist

### Desktop Checklist

- [ ] Obsidian installed and vault created
- [ ] LiveSync plugin installed and enabled
- [ ] Connection to CouchDB successful (green dot)
- [ ] Created test note
- [ ] LiveSync shows sync activity
- [ ] No error messages in logs

### iPhone Checklist

- [ ] Obsidian app installed
- [ ] LiveSync plugin installed and enabled
- [ ] Same CouchDB configuration as desktop
- [ ] Initial sync completed
- [ ] All desktop notes visible on iPhone
- [ ] Created test note on iPhone
- [ ] Test note appears on desktop

### Sync Verification

- [ ] Edit note on desktop → appears on iPhone (~1 min)
- [ ] Edit note on iPhone → appears on desktop (~1 min)
- [ ] No conflict files appearing
- [ ] LiveSync status shows green on both devices

---

## Troubleshooting

### "Connection failed" Error

**Check:**
1. CouchDB is running: `curl https://couchdb.yourdomain.com`
2. Database exists: `curl -u admin:password https://couchdb.yourdomain.com/_all_dbs`
3. Credentials correct (username/password)
4. URI format: `https://domain.com/obsidian` (no trailing slash)

### "Unauthorized" Error

**Solutions:**
1. Verify username is `admin`
2. Verify password matches docker-compose.yml
3. Check CouchDB logs for authentication errors
4. Try resetting password in docker-compose.yml

### iPhone Sync Not Working

**Check:**
1. iPhone has internet connection
2. Not on VPN that blocks server
3. LiveSync plugin enabled (toggle may reset on iOS updates)
4. Background app refresh enabled for Obsidian
5. Try force-quit app and reopen

### Sync is Slow

**Optimization:**
1. Reduce sync interval (Settings → 60 seconds instead of 30)
2. Increase batch size (Advanced Settings → Batch size: 100)
3. Disable "Sync on Save" (manual sync instead)
4. Check server resources (CPU/memory on Hetzner)

### Conflict Files Appearing

**Causes:**
- Editing same note on multiple devices simultaneously
- Sync failed partway through
- Network interruption during sync

**Resolution:**
1. Open conflict file
2. Compare with original
3. Merge changes manually
4. Delete conflict file
5. Wait for sync to complete before editing again

### Plugin Not Appearing on iPhone

**Solutions:**
1. Check plugin has mobile support
2. Force sync: LiveSync → "Fetch" button
3. Restart Obsidian app
4. Check `.obsidian/plugins/` folder synced
5. Reinstall plugin manually on mobile

---

## Performance Tips

### Optimize Sync Performance

1. **Reduce sync frequency for large vaults:**
   - Periodic Sync: 60 seconds instead of 30

2. **Use selective sync:**
   - Settings → Sync Settings → Exclude folders
   - Exclude large attachment folders if not needed on mobile

3. **Compress attachments:**
   - Use smaller image sizes
   - Compress PDFs before adding to vault

4. **Monitor CouchDB size:**
   ```bash
   docker exec obsidian-couchdb du -sh /opt/couchdb/data
   ```

### Battery Optimization (iPhone)

1. **Increase sync interval:** 120 seconds for better battery
2. **Disable background sync:** Only sync when app open
3. **Use WiFi-only sync:** Settings → Network → Cellular off

---

## Next Steps

✅ Desktop syncing to CouchDB
✅ iPhone syncing to CouchDB
✅ Plugins installed and syncing

**Proceed to:** `MCP_INTEGRATION_GUIDE.md` for AI agent access.
