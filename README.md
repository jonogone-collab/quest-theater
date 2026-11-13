[README.md](https://github.com/user-attachments/files/34814477/README.md)
# Quest Theater for Plex

A single-file WebXR player for your own Plex server. It runs in the Meta Quest Browser — no sideloading, no developer mode.

## Put it online (one time, ~5 minutes)

The headset view only works on a page served over **https**, so `index.html` needs a home. Any static host works. GitHub Pages is free:

1. Create a new repository on github.com (public, or private on a paid plan), e.g. `quest-theater`.
2. **Add file → Upload files** → upload `index.html` → Commit.
3. **Settings → Pages** → Source: *Deploy from a branch* → Branch: `main`, folder `/ (root)` → Save.
4. After a minute your page is at `https://<your-username>.github.io/quest-theater/`.

The page is only the player. Your Plex sign-in is kept in the headset's browser storage and is sent only to plex.tv and your own server.

## First run in the headset

1. Open the page address in the Quest Browser and bookmark it.
2. **Sign in with Plex** → enter the 4-character code at plex.tv/link on your phone (or use the "approve in a new tab" button).
3. If the browser asks to let the site reach devices on your local network, allow it — that is how it talks to your server.
4. Pick a movie, then press **Enter theater**.

## Controls

**Control panel:** library · volume (tap the speaker to mute, drag the slider) · −10s / play-pause / +30s · quality dropdown · room (passthrough → dimmed → black) · exit.

| Action | How |
|---|---|
| Press anything | Point + trigger, or point + pinch with hands |
| Move the screen | Grab the bar under the screen and drag |
| Move the control panel | Grab the thin bar under the panel and drag. It stays where you leave it |
| Push / pull while holding a bar | Thumbstick up/down (scroll wheel in the flat preview) |
| Resize the screen | Point at the screen, grab a corner handle, pull out or in. Grows/shrinks from the centre; the size shows as a TV-style diagonal |
| Scrub | Drag along the progress bar; the time shows above it |
| Browse the library | Thumbstick up/down scrolls the grid (it speeds up the longer you hold). With hands or a mouse, drag the grid. Drag or tap the A–Z slider on the left to jump |
| Curve the screen | Curve button (between Quality and Room) → drag the slider from Flat to Wrap-around (up to a 150° arc), or tap Flat / Gentle / Wrap. The screen keeps its width as it bends; pull it closer for a wide-lens feel. Default is flat |
| Show / hide controls | Tap the screen. While playing they fade after 4 s without a pointer on them |
| Play / pause | A or X |
| Room mode | B or Y |

Until you move the control panel yourself, it stays tucked under the screen when you move or resize the screen. Screen size, position, curve, panel placement, room, volume and quality are remembered.

## Picture quality

- **Video layer:** in the headset, the video is handed straight to the Quest's compositor (a WebXR media layer) instead of being drawn as a texture in the 3D scene. This avoids a second resampling and is noticeably sharper. If the browser doesn't support it, the app falls back to the texture automatically.
- **Original** asks Plex for the file's native resolution (up to 4K) at about 1.5× its bitrate, and tells Plex the headset can take 4K H.264, so H.264 files stream untouched.
- The status next to the title tells you what's happening: *Direct play* / *Direct stream · original video* = untouched; *Converting* = your server is re-encoding (HEVC/HDR files always are, since the browser can't take them).
- **Stuck at a low resolution (e.g. "Converting 270p") whatever you pick?** Open the Quality dropdown: the *What's happening* box shows the connection and the server's own reason.
  - *Connection: Relay* → you're going through Plex's relay (1–2 Mbps max). Fix the local connection (see "Getting a direct connection").
  - *Connection: Local* but still low → Plex is treating the headset as remote. In Plex → Settings → Network, set **LAN Networks** to your home subnet (e.g. `192.168.1.0/24`) and turn on **Treat WAN IP As LAN Bandwidth**; in Settings → Remote Access set **Limit remote stream bitrate** to *Original (no limit)*.
- If it says Converting and still looks soft: in Plex → Settings → Transcoder, set *Transcoder quality* to "Make my CPU hurt" and turn on hardware-accelerated video encoding if your server supports it.

## Playback

- MP4 files with H.264 (and HEVC where the browser supports it) play directly.
- Everything else — MKV, HDR/Dolby Vision, DTS/TrueHD audio — is converted by your Plex server on the fly. The quality button cycles Original → 1080p 20 Mbps → 1080p 10 Mbps → 720p 4 Mbps; pick a lower one if your server struggles with 4K files.
- Watch progress is reported to Plex, so titles resume where you left off and show up under **Continue**.
- Search uses the box at the top of the page before you enter the theater (the headset keyboard works there). Inside, use the A–Z strip and paging.

## Getting a direct (full-quality) connection

The app tries, in order: your server's local plex.direct address, its plain local IP, its remote address, and last Plex's relay. The relay is capped at 1–2 Mbps, so the app now stops and explains instead of using it silently.

- **Allow local network access.** The first time, the Quest Browser may ask to let the site reach devices on your local network. Choose Allow. If you denied it, re-enable it in the browser's site settings for this page.
- **Router DNS rebinding protection** often blocks `*.plex.direct` (it resolves to a home IP). Add a `plex.direct` exception in the router, or set the Quest's Wi-Fi DNS to 1.1.1.1. Test from a computer: `nslookup 192-168-x-x.<id>.plex.direct` should return your server's IP.
- **Plex → Settings → Network:** Secure connections = Preferred; "List of IP addresses and networks that are allowed without auth" can stay empty.
- Same Wi-Fi as the server, no guest network or "client isolation".
- **Manual fallback:** "Connect manually" accepts `http://192.168.x.x:32400` plus your X-Plex-Token.
- Away from home: enable Remote Access in Plex (port forward) or you'll land on the relay.

## Not in this version

Subtitle and audio-track pickers (it uses whatever is selected for the title in Plex, burned in when converting), 3D/180° video, curved screen, next-episode autoplay.
