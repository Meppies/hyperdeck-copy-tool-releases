# HyperDeck Copy Tool

A native macOS app for copying recordings from
[Blackmagic HyperDeck](https://www.blackmagicdesign.com/products/hyperdeckstudio)
recorders to a local folder or NAS over Ethernet.

![icon](https://raw.githubusercontent.com/Meppies/hyperdeck-copy-tool-releases/main/icon.png)

## Install

1. Download **HyperDeck Copy Tool.dmg** from the
   [latest release](https://github.com/Meppies/hyperdeck-copy-tool-releases/releases/latest).
2. Open the DMG.
3. Drag **HyperDeck Copy Tool.app** onto the **Applications** folder.
4. First launch: right-click the app in Applications → **Open** →
   **Open** in the Gatekeeper dialog. macOS warns once because the
   build isn't code-signed; after that it launches normally.

## What it does

The tool watches up to **9 HyperDeck recorders at the same time** and
copies their recordings to a destination of your choice. It can run on
its own — leave it open during a concert, conference or church service
and it will quietly pull every clip off every recorder as soon as it's
available.

### Continuous polling — set it and forget it

After you press **Start**, the tool scans every recorder on a steady
interval. Every new clip that appears on a HyperDeck SSD is
automatically queued for copy. You don't need to come back between
takes, you don't need to refresh anything — new recordings show up on
your NAS within seconds of being created. The recorder counters in the
UI keep climbing as files finish: *3 done · 18.2 GB*, *4 done · 24.7
GB*, and so on.

### Live copy of DNxHR MXF files while still recording

This is the big one. Switch to **Live kopiëren** mode and the tool
starts pulling bytes off a `.mxf` clip while the HyperDeck is still
**actively recording it**. When the recording eventually stops, the
tool finalises the destination file — patches in the MXF Footer
Partition and Random Index Pack so the copy is a fully closed,
playable MXF. Result: the moment the operator hits stop on the
HyperDeck, the file is **immediately readable in DaVinci Resolve, Avid
Media Composer, FCP X, or any other MXF-aware tool** on the
destination. No waiting for the recorder to finish, no second copy
pass, no Media Offline gaps.

The tool also handles MOV recordings (ProRes), although live-MOV is
trickier because of the moov-atom rewrite at end-of-recording. In Live
mode, MOV files are queued and copied right after the recording stops
so they always land complete and playable.

### Multiple source protocols

| Protocol | When to use it |
| --- | --- |
| **HTTPS** | Default for firmware 9.x. Uses the HyperDeck's Web Media Manager API. Fast, reliable, and unaffected by macOS SMB caching. **Recommended for live copy.** |
| **FTP** | Falls back automatically if HTTPS isn't available. Works on older firmware. |
| **SMB** | For environments where SMB is preferred. Reads the HyperDeck's `ssd1` / `ssd2` shares directly. Tool uses macOS's native `mount_smbfs` so no extra dependencies. |
| **Auto** | Probes HTTPS → FTP → SMB and picks the first that responds with file listings. Set this per recorder if you're unsure. |

### Multiple destinations

- **Local folder** — anywhere on the Mac, including mounted NAS volumes
  (smb://… mounted in Finder, USB drives, Thunderbolt RAID, …).
- **SMB share** — direct SMB write to a NAS, no Finder mount needed.
- **FTP server** — write directly to an FTP destination.

Each recorder can have its own destination *and* its own subfolder, so
nine cameras can all share one NAS path but stay neatly separated
(`/Volumes/NAS/HyperDeck/Cam1/`, `/Cam2/`, …).

### Resume-safe, never destructive

- The tool **never deletes anything** from the HyperDeck SSDs. It's
  read-only on the source side.
- If a copy is interrupted (network blip, app crash, NAS full) the
  next scan picks up exactly where it left off — it appends the
  missing bytes instead of restarting from zero.
- The destination file is never truncated by a transient size
  mismatch (e.g. when the HyperDeck rewrites a moov-atom). A good
  copy in place is always kept safe.
- A per-session state file tracks which clips are already complete,
  so restarting the app on a half-done batch resumes cleanly.

### Pause, swap destinations mid-flight

Press **Pauze** during copying. While paused, you can change the
destination of any recorder — handy when a NAS fills up and you want
to redirect ongoing copies to a second volume. Press **Pauze** again
to resume; in-flight files continue to their new destination.

### Concurrency + bandwidth controls

- **Parallel** — how many copies run at the same time across all
  recorders. Default 4 in Normal mode, 2 in Live mode. Lower if your
  NAS is the bottleneck.
- **Bandwidth/rec** — optional per-recorder rate cap in MB/s. Default
  *Ongelimiteerd*. Useful if you need to leave headroom for other
  traffic on the same 10 GbE link.

### At-a-glance status per recorder

Every row in the UI shows:
- Live status LED (idle / scanning / copying / **live recording**).
- Active **SSD bay highlight** — SSD1 or SSD2 lights up so you can see
  which physical disk is currently being read.
- Filename, size copied / total, throughput in MB/s, ETA.
- Per-recorder running tally: number of clips done and total bytes.

## Tested with

- **HyperDeck Studio 4K Pro** running firmware 9.x.
- 10 GbE switch between the recorders and the Mac (gigabit also
  works, of course — just slower per recorder).
- 2160p25 recordings in **DNxHR SQ (MXF)** and **ProRes LT (MOV)**.
- Destinations tried: local SSD, Synology NAS over SMB (Finder-mounted
  and direct SMB), and FTP server.

## Requirements

- macOS 11 (Big Sur) or newer.
- Apple Silicon (arm64) — Intel is not currently bundled.
- HyperDeck firmware **9.x** recommended (Auto-mode falls back to FTP
  for older firmwares).
- Network reachability from the Mac to every recorder (either over
  Ethernet or via mDNS hostnames like `blackmagic-rec.local`).

## Languages

The UI follows your macOS system language. Currently shipped:
**English** and **Dutch (Nederlands)**.

## Issues / feedback

Open an issue on the
[issues tab](https://github.com/Meppies/hyperdeck-copy-tool-releases/issues).
