# HyperDeck Copy Tool

A native macOS app for copying recordings from
[Blackmagic HyperDeck](https://www.blackmagicdesign.com/products/hyperdeckstudio)
recorders to a local folder or NAS over Ethernet.

![icon](https://raw.githubusercontent.com/Meppies/hyperdeck-copy-tool-releases/main/icon.png)

## Install

1. Download the latest **HyperDeck Copy Tool.dmg** from the
   [Releases page](https://github.com/Meppies/hyperdeck-copy-tool-releases/releases/latest).
2. Open the DMG.
3. Drag **HyperDeck Copy Tool.app** onto the **Applications** folder.
4. First launch: right-click the app in Applications → **Open** →
   **Open** in the Gatekeeper dialog (one-time confirmation, the app
   isn't code-signed).

## Features

- Up to **9 concurrent recorders**, configurable parallel-copy limit.
- Two copy modes: **Normaal** (only finished files) and **Live**
  (capture while recording, with proper MXF / MOV finalisation).
- **HTTPS, FTP, SMB** source protocols with automatic protocol
  detection.
- Destination: local folder (incl. mounted NAS), SMB share, or FTP.
- Resume-safe — restarts from where it left off, never wipes a good
  copy.
- Blackmagic-styled dark UI (matches ATEM Software Control and
  HyperDeck Setup).

## Requirements

- macOS 11 (Big Sur) or newer
- Apple Silicon (arm64) — Intel is not currently bundled
- HyperDeck firmware 9.x recommended

## Issues / feedback

Open an issue on the
[issues tab](https://github.com/Meppies/hyperdeck-copy-tool-releases/issues).
