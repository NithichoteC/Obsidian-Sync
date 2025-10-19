# MCP Integration Guide - AI Agents Access to Obsidian

Enable Claude and other AI agents to read, write, and search your Obsidian vault.

---

## Overview

**What You'll Get:**
- Claude can read your notes
- Claude can create new notes
- Claude can search your vault
- Claude can modify existing notes
- Other MCP-compatible AI tools can access vault

**Architecture:**
```
Obsidian Desktop
├── Local REST API plugin (creates API endpoint)
└── MCP Tools plugin (optional, alternative method)
        ↓
Claude Desktop (MCP Client)
└── Configured to connect to Obsidian API
```

---

## Method 1: Using Local REST API + External MCP Server (Recommended)

This method uses the official Obsidian Local REST API plugin and connects via MCP.

### Step 1: Install Local REST API Plugin (Obsidian)

1. **Open Obsidian Desktop**
2. **Settings → Community plugins → Browse**
3. **Search:** `Local REST API`
4. **Install:** "Local REST API" by coddingtonbear
5. **Enable:** Toggle switch ON

### Step 2: Configure Local REST API

1. **Go to plugin settings:**
   - Settings → Community plugins → Local REST API (gear icon)

2. **Enable API:**
   - Toggle "Enable" to ON

3. **API Configuration:**
   - **Port:** `27124` (default, or choose custom)
   - **Host:** `127.0.0.1` (localhost only for security)
   - **HTTPS:** OFF (local use, not needed)

4. **Generate API Key:**
   - Click "Show/Generate API Key"
   - Copy the generated key (looks like: `abc123...xyz789`)
   - **Save this key securely!**

5. **Permissions (Important):**
   - Enable: ✅ Read access
   - Enable: ✅ Write access
   - Enable: ✅ Create notes
   - Enable: ✅ Update notes
   - Enable: ✅ Delete notes (optional, be careful)
   - Enable: ✅ Search

6. **Test API is working:**
   ```bash
   curl http://127.0.0.1:27124/ -H "Authorization: Bearer YOUR_API_KEY"
   ```

   Should return vault info in JSON format.

### Step 3: Configure Claude Desktop (MCP Client)

**Locate Claude Desktop config file:**

**Windows:**
```
%APPDATA%\Claude\claude_desktop_config.json
```

**Mac:**
```
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Linux:**
```
~/.config/Claude/claude_desktop_config.json
```

**Create or edit the file:**

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-obsidian",
        "--vault-path",
        "C:\\path\\to\\your\\obsidian\\vault",
        "--api-key",
        "YOUR_LOCAL_REST_API_KEY",
        "--api-url",
        "http://127.0.0.1:27124"
      ]
    }
  }
}
```

**Important replacements:**
- Replace `C:\\path\\to\\your\\obsidian\\vault` with actual vault path
  - Windows: Use `\\` (double backslash)
  - Mac/Linux: Use `/` (forward slash)
- Replace `YOUR_LOCAL_REST_API_KEY` with key from Step 2

**Vault path examples:**
```
Windows: C:\\Users\\YourName\\Documents\\ObsidianVault
Mac:     /Users/YourName/Documents/ObsidianVault
Linux:   /home/yourname/Documents/ObsidianVault
```

### Step 4: Restart Claude Desktop

1. **Quit Claude Desktop completely**
2. **Reopen Claude Desktop**
3. **Check for MCP server:**
   - Look for small plug icon or MCP indicator
   - May see "Obsidian" in available tools

### Step 5: Test AI Access

**Open Claude chat and try:**

```
Read my note titled "Test Note"
```

```
Search my vault for notes about "project ideas"
```

```
Create a new note called "AI Test" with the content "This was created by Claude"
```

```
List all notes in my vault
```

**Expected behavior:**
- Claude should acknowledge it can access vault
- Should return actual content from your notes
- Should create notes that appear in Obsidian

---

## Method 2: Using Obsidian MCP Tools Plugin (Alternative)

This is a simpler method using a dedicated Obsidian plugin.

### Step 1: Install Obsidian MCP Tools Plugin

1. **Open Obsidian Desktop**
2. **Settings → Community plugins**
3. **Turn off Safe mode** (if not already)
4. **Click "Browse"**
5. **Search:** `MCP Tools` or `Obsidian MCP`
6. **Install:** "Obsidian MCP Tools" by jacksteamdev
7. **Enable:** Toggle switch ON

### Step 2: Configure Plugin

1. **Go to plugin settings:**
   - Settings → Community plugins → Obsidian MCP Tools

2. **Server Configuration:**
   - **Port:** `3000` (default)
   - **Host:** `127.0.0.1`
   - **Enable server:** Toggle ON

3. **Features to enable:**
   - ✅ Semantic Search
   - ✅ Template Integration
   - ✅ Note Creation
   - ✅ Note Reading

4. **Generate API Key (if required):**
   - Some versions auto-generate
   - Copy and save securely

### Step 3: Configure Claude Desktop

**Edit Claude config file** (same location as Method 1):

```json
{
  "mcpServers": {
    "obsidian-tools": {
      "command": "node",
      "args": [
        "path/to/obsidian-mcp-tools/server.js"
      ],
      "env": {
        "OBSIDIAN_API_URL": "http://127.0.0.1:3000",
        "OBSIDIAN_API_KEY": "your-api-key-if-required"
      }
    }
  }
}
```

### Step 4: Test

Same testing procedure as Method 1.

---

## Method 3: Using GitHub MCP Server (Advanced)

For the most control, install MCP server directly from GitHub.

### Step 1: Install Prerequisites

**Install Node.js:**
- Download from: https://nodejs.org/
- Install LTS version (20.x)

**Verify installation:**
```bash
node --version
npm --version
```

### Step 2: Install MCP Server

**Choose one:**

**Option A: obsidian-mcp-server (by cyanheads)**
```bash
npm install -g obsidian-mcp-server
```

**Option B: Clone and build:**
```bash
git clone https://github.com/cyanheads/obsidian-mcp-server.git
cd obsidian-mcp-server
npm install
npm run build
```

### Step 3: Configure Claude Desktop

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "obsidian-mcp-server",
      "args": [
        "--vault",
        "/path/to/vault",
        "--api-key",
        "your-local-rest-api-key"
      ]
    }
  }
}
```

---

## Security Considerations

### Local-Only Access (Recommended)

**Current setup is secure because:**
- API only listens on `127.0.0.1` (localhost)
- Not accessible from network/internet
- API key required for all requests
- Only authorized apps (Claude Desktop) can connect

### DO NOT Do This (Unsafe):

❌ **Don't bind to 0.0.0.0** (would expose to network)
❌ **Don't disable API key authentication**
❌ **Don't expose port to internet** (no port forwarding)

### If You Need Remote Access:

**Use SSH tunnel instead:**
```bash
ssh -L 27124:127.0.0.1:27124 user@your-server
```

This keeps API local-only but accessible remotely via secure SSH.

---

## Troubleshooting

### Claude Can't See Obsidian

**Check:**
1. Local REST API plugin enabled in Obsidian
2. API port correct (27124 by default)
3. API key correct in Claude config
4. Vault path correct (use absolute path)
5. Claude Desktop restarted after config change

**Test API manually:**
```bash
curl http://127.0.0.1:27124/ -H "Authorization: Bearer YOUR_API_KEY"
```

Should return JSON, not error.

### "Connection refused" Error

**Solutions:**
1. Ensure Obsidian is running (API only works when app open)
2. Check port not blocked by firewall
3. Verify plugin is enabled
4. Try different port in plugin settings

### Claude Creates Notes But They Don't Appear

**Check:**
1. Obsidian vault is the correct one
2. Note location settings in plugin
3. Refresh file explorer in Obsidian
4. Check trash/archive folders

### Permission Errors

**Solutions:**
1. Enable write permissions in Local REST API settings
2. Check file system permissions on vault folder
3. Try running Obsidian as administrator (Windows)

### MCP Server Not Loading in Claude

**Debug:**
1. Check Claude Desktop logs:
   - Windows: `%APPDATA%\Claude\logs`
   - Mac: `~/Library/Logs/Claude`
2. Verify JSON config syntax (use JSON validator)
3. Check node/npm installed correctly
4. Try absolute paths instead of relative

---

## Advanced Usage Examples

### Example 1: Semantic Search

**In Claude chat:**
```
Search my Obsidian vault for notes related to "machine learning algorithms" and summarize the key points.
```

### Example 2: Automated Note Creation

**In Claude chat:**
```
Create a daily note for today with sections for Tasks, Notes, and Reflections. Add 3 placeholder tasks.
```

### Example 3: Content Analysis

**In Claude chat:**
```
Read all notes tagged #project and create a summary document of project status.
```

### Example 4: Template Execution

**In Claude chat:**
```
Use my "Meeting Notes" template to create a new note for today's standup meeting.
```

---

## Integration with Other AI Tools

### Compatible MCP Clients

**Besides Claude Desktop:**
- Cline (VSCode extension)
- Continue.dev
- Other MCP-compatible tools

**Same configuration works** - just update their config files with Obsidian MCP server details.

### Multiple AI Agents

**Can run multiple MCP clients simultaneously:**
- Claude Desktop
- VSCode with Cline
- Custom scripts

All connect to same Local REST API endpoint.

---

## Performance & Limitations

### Performance

**Expected performance:**
- Small vaults (<1000 notes): Instant responses
- Medium vaults (1000-10000 notes): 1-3 second searches
- Large vaults (>10000 notes): May need indexing optimization

### Limitations

**Current limitations:**
- Obsidian must be running (Local REST API requires app open)
- Local-only access (unless using SSH tunnel)
- Some plugins may conflict with API
- Large binary files slow down operations

### Optimization Tips

1. **Index frequently searched folders**
2. **Exclude large attachment folders from API access**
3. **Use specific note searches instead of vault-wide**
4. **Keep vault organized with clear folder structure**

---

## Backup Recommendations

### Before AI Modifications

**Important:** AI agents can modify/delete notes.

**Protection strategies:**

1. **Enable Obsidian Git plugin** (auto-backup to GitHub)
   - See `BACKUP_GUIDE.md` for setup

2. **Use version control:**
   - Settings → Files & Links → File recovery
   - Obsidian keeps file history automatically

3. **Regular backups:**
   - Automated backup script
   - External drive backups
   - Cloud sync (iCloud, Dropbox, etc.)

4. **Test on copy first:**
   - Create duplicate vault
   - Test AI commands on copy
   - Verify behavior before using on main vault

---

## Next Steps

✅ MCP server configured
✅ Claude can access Obsidian
✅ AI agents can read/write notes

**Optional next steps:**
- Set up automated GitHub backups (`BACKUP_GUIDE.md`)
- Configure security hardening (`SECURITY_CHECKLIST.md`)
- Explore advanced MCP features

---

## Support Resources

**Official Docs:**
- Local REST API: https://github.com/coddingtonbear/obsidian-local-rest-api
- MCP Protocol: https://modelcontextprotocol.io/
- Obsidian API: https://docs.obsidian.md/

**Community:**
- Obsidian Forum: https://forum.obsidian.md/
- Obsidian Discord: https://discord.gg/obsidianmd
- MCP Discord: Check official MCP documentation

---

**Your Obsidian vault is now AI-accessible! 🎉**
