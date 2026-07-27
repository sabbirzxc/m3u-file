https://sabbirzxc.github.io/m3u-file/

# 📡 Channel Scan & File Output Architecture

A standardized naming convention and classification pipeline for pre-scan master channel lists and post-scan categorized output files.

---

## 🎯 Architecture & Output Logic

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                        FULL CHANNELS FOLDER                             │
│                  (No Scan: Working + Block + Dead)                      │
└───────────────────────────────────┬─────────────────────────────────────┘
                                    │
                                    ▼ [ Scan Operation ]
┌─────────────────────────────────────────────────────────────────────────┐
│                         FULL NAME (Scan File)                           │
│                      (Scanned: Working + Block)                         │
└─────────────────┬───────────────────────────────────────┬───────────────┘
                  │                                       │
                  ▼                                       ▼
    ┌───────────────────────────┐           ┌───────────────────────────┐
    │     FULL NAME + 1         │           │     FULL NAME + 2         │
    │     (Working Only)        │           │      (Block Only)         │
    └───────────────────────────┘           └───────────────────────────┘
```

---

## 📊 File Classification Matrix

| File / Folder Object | Scan Status | Included Streams | Description & Primary Purpose |
| :--- | :---: | :---: | :--- |
| **📁 Full Channels Folder** | `No Scan` | 🟢 Working<br>🟠 Block<br>🔴 Dead | Complete raw, unverified master backup before scanning. |
| **📄 Full Name** *(Scan File)* | `Scanned` | 🟢 Working<br>🟠 Block | Verified master list after scanning. All dead/offline streams are stripped out. |
| **📄 Full Name + 1** | `Filtered` | 🟢 Working | Active, playable streams only. Ready for production playlists. |
| **📄 Full Name + 2** | `Filtered` | 🟠 Block | Geo-restricted, auth-required, or blocked streams log. |

---

## 🏷️ Naming Convention Guidelines

When running automated scans, outputs follow this structured suffix pattern:

| Output Target | Suffix Formula | Example File / Folder Name |
| :--- | :--- | :--- |
| **Raw Master Folder** | `<BaseName>_Full_Channels/` | `US_Sports_Full_Channels/` |
| **Scanned Master File** | `<BaseName>.<ext>` | `US_Sports.m3u` |
| **Working Streams File** | `<BaseName>_1.<ext>` | `US_Sports_1.m3u` |
| **Blocked Streams File** | `<BaseName>_2.<ext>` | `US_Sports_2.m3u` |

---

## 📁 Practical Directory Structure Example

```text
root/
├── 📁 US_Sports_Full_Channels/          # Unscanned raw folder (Working + Block + Dead)
│   └── raw_list.m3u
│
├── 📄 US_Sports.m3u                     # Full Name: Scanned master (Working + Block)
├── 📄 US_Sports_1.m3u                   # Full Name + 1: Active streams (Working)
└── 📄 US_Sports_2.m3u                   # Full Name + 2: Restricted streams (Block)
```

---

## 💡 Channel Status Legend

- 🟢 **Working**: Fully playable streams with active data feed.
- 🟠 **Block**: Connected server, but stream is geo-blocked, user-agent restricted, or requires token auth.
- 🔴 **Dead**: Unreachable server, 404, timeout, or broken stream link (automatically excluded from scan outputs).

---

> 📌 **Note for Maintainers:** Keep raw files inside the `Full Channels` folder untouched as cold storage backups before executing the channel scanning script.
