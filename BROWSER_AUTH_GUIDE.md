# Browser Authentication Guide

## ⚠️ IMPORTANT: OAuth is Currently Broken

As of November 2024, OAuth authentication for YouTube Music is experiencing widespread issues due to server-side changes by Google/YouTube. Many users report HTTP 400 "Bad Request" errors even with valid OAuth credentials. This is a known issue tracked in ytmusicapi repository (issues #676, #682).

**The recommended solution is to use browser-based authentication instead.**

## Why Browser Authentication?

- **Works reliably** - Not affected by OAuth server-side issues
- **Simple to set up** - Just extract headers from your browser
- **Long-lasting** - Browser cookies remain valid for months when created in a private/incognito session

## How to Set Up Browser Authentication

### Step 1: Extract Browser Headers

Follow the official ytmusicapi guide:
https://ytmusicapi.readthedocs.io/en/stable/setup/browser.html

Quick summary:
1. Open YouTube Music in a **private/incognito** browser window
2. Log in to your account
3. Open Developer Tools (F12)
4. Go to Network tab
5. Refresh the page and look for a request to `music.youtube.com`
6. Copy the request headers (specifically the Cookie header)

### Step 2: Create Header File

The ytmusicapi documentation provides a tool to help you create the header file from your browser cookies. Run:

```bash
ytmusicapi browser
```

This will guide you through pasting your headers and save them to `oauth.json` (or you can specify a custom path).

### Step 3: Configure Home Assistant Integration

1. **Delete the existing integration** if you have one with OAuth errors
   - Go to Settings → Devices & Services
   - Find "YouTube Music Player"
   - Click the three dots and select "Delete"

2. **Add the integration again**
   - Go to Settings → Devices & Services → "+ Add Integration"
   - Search for "YouTube Music Player"

3. **Skip OAuth setup**
   - On the first screen, **UNCHECK** "Renew OAuth credentials"
   - Continue to the finish screen

4. **Specify your header file path**
   - Enter the path to the header file you created in Step 2
   - Default location: `/config/.storage/ytube_music_player_auth_header_<entity_name>.json`
   - Or use a custom path if you saved it elsewhere

5. **Complete setup**
   - Select your output devices
   - Configure any advanced options
   - Save

## Troubleshooting

**Q: Can I still try OAuth?**
A: Yes, but it's likely to fail with HTTP 400 errors. The integration will warn you about this. If you want to try anyway, keep "Renew OAuth credentials" checked and follow the OAuth setup flow.

**Q: How often do I need to update browser headers?**
A: When created in a private/incognito session, browser cookies can last for months. You'll know they've expired when the integration stops working and shows authentication errors.

**Q: The integration still shows OAuth-related errors**
A: Make sure you:
1. Completely removed the old integration (not just disabled it)
2. Deleted or renamed the old OAuth token file
3. Created a fresh header file using browser authentication
4. Unchecked "Renew OAuth credentials" during setup

## Additional Resources

- ytmusicapi Browser Authentication Guide: https://ytmusicapi.readthedocs.io/en/stable/setup/browser.html
- ytmusicapi OAuth Issue #676: https://github.com/sigma67/ytmusicapi/issues/676
- ytmusicapi OAuth Issue #682: https://github.com/sigma67/ytmusicapi/discussions/682
- Home Assistant Community: https://community.home-assistant.io/

## Future Updates

This guide will be updated when/if OAuth authentication is fixed by YouTube Music. Monitor the ytmusicapi GitHub repository for updates on the OAuth issue.
