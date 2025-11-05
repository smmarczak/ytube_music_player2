# Quick Start: Browser Authentication Setup

## What You Need to Do Right Now

While Home Assistant is restarting, follow these steps to create your browser authentication file.

## Step 1: Open YouTube Music in Incognito/Private Window

**Important:** Use an incognito/private window so cookies last longer!

1. Open your browser's incognito/private mode:
   - Chrome/Edge: `Ctrl+Shift+N` (Windows) or `Cmd+Shift+N` (Mac)
   - Firefox: `Ctrl+Shift+P` (Windows) or `Cmd+Shift+P` (Mac)
   - Safari: `Cmd+Shift+N`

2. Go to https://music.youtube.com

3. Log in to your YouTube Music account

## Step 2: Extract Browser Headers

### Method 1: Using Browser Developer Tools (Manual)

1. **Open Developer Tools**: Press `F12`

2. **Go to Network tab**

3. **Filter for "browse"**: Type `browse` in the filter box at the top

4. **Refresh the page**: Press `F5` or click the refresh button

5. **Click on a "browse" request**: Look for a request to `music.youtube.com` in the list

6. **Copy Request Headers**:
   - Find the "Request Headers" section (usually on the right side)
   - Look for these specific headers and copy their values:
     - `Cookie:` (most important!)
     - `User-Agent:`
     - `Accept-Language:`
     - `X-Goog-AuthUser:`

### Method 2: Using ytmusicapi Command (Easier)

If you have Python installed on your local machine:

```bash
# Install ytmusicapi
pip install ytmusicapi

# Run the browser auth setup
ytmusicapi browser
```

This will guide you through pasting headers and automatically create the JSON file.

## Step 3: Create the Header File

### JSON Format

Create a file with this structure:

```json
{
  "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36...",
  "Accept": "*/*",
  "Accept-Language": "en-US,en;q=0.5",
  "Accept-Encoding": "gzip, deflate, br",
  "Content-Type": "application/json",
  "X-Goog-AuthUser": "0",
  "X-Goog-Visitor-Id": "...",
  "Authorization": "...",
  "Cookie": "__Secure-1PSID=...; __Secure-1PAPISID=...; __Secure-3PSID=...; __Secure-3PAPISID=..."
}
```

**CRITICAL:** The `Cookie:` header MUST include these values:
- `__Secure-1PSID`
- `__Secure-1PAPISID`
- `__Secure-3PSID`
- `__Secure-3PAPISID`

### Where to Save the File

**Option A:** Default location (recommended)
```
/config/.storage/header_ytube_music_player.json
```

**Option B:** Custom location
Save anywhere you want, but remember the path for the integration setup.

## Step 4: Copy File to Home Assistant

### Method 1: Using File Editor Add-on

1. Install "File editor" add-on if not already installed
2. Navigate to `.storage` folder
3. Create/upload your `header_ytube_music_player.json` file

### Method 2: Using Samba/SMB Share

1. Connect to your Home Assistant via network share
2. Navigate to `config/.storage/`
3. Copy your JSON file there

### Method 3: Using SSH/Terminal

```bash
# Copy from your local machine to Home Assistant
scp header_ytube_music_player.json root@homeassistant.local:/config/.storage/
```

## Step 5: Configure the Integration

Once Home Assistant has restarted:

1. **Go to**: Settings → Devices & Services → "+ Add Integration"

2. **Search for**: "YouTube Music Player"

3. **First screen**:
   - Enter entity name: `ytube_music_player` (or whatever you want)
   - **UNCHECK** "Use OAuth authentication" (it's broken anyway)
   - Click Submit

4. **Final screen**:
   - Select your output devices (speakers/media players)
   - **File path for the header file**: Enter the path where you saved it
     - If you used default location: `/config/.storage/header_ytube_music_player.json`
     - Otherwise: enter your custom path
   - Language: `en` (or your preference)
   - Click Submit

## Step 6: Test It!

1. Go to Media Browser
2. Navigate to YouTube Music Player
3. Browse your playlists
4. Play a song!

## Troubleshooting

### "Unknown error occurred"

**Cause:** The header file doesn't exist at the path you specified or is malformed.

**Fix:**
- Double-check the file path is correct
- Verify the JSON is valid (use https://jsonlint.com)
- Make sure the file has the Cookie header with all required fields

### "HTTP 401: Unauthorized"

**Cause:** Your cookies have expired.

**Fix:**
- Repeat steps 1-4 to extract fresh headers
- Replace the old header file with the new one
- Restart Home Assistant

### "HTTP 403: Forbidden"

**Cause:** Missing PO tokens (but this should be fixed now!)

**Fix:**
- Make sure you have the latest code with PO token support
- The integration should automatically generate PO tokens

### Playback doesn't work but browsing does

**Cause:** PO tokens not being generated (rare).

**Fix:**
- Check Home Assistant logs for pytubefix errors
- Ensure nodejs-wheel-binaries is installed
- Restart Home Assistant

## How Long Do Cookies Last?

- **Created in incognito/private mode:** Several months
- **Created in normal mode:** Could expire sooner
- **You'll know they expired when:** Integration stops working with 401 errors

## Need More Help?

- Full guide: See `BROWSER_AUTH_GUIDE.md` in this repository
- ytmusicapi docs: https://ytmusicapi.readthedocs.io/en/stable/setup/browser.html
- Home Assistant Community: https://community.home-assistant.io/

---

**Pro Tip:** Bookmark the page in your browser showing the Developer Tools with the request headers. If your cookies expire, you can quickly re-open it and grab new ones!
