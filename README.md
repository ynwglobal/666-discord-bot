# 666 Discord Bot

A feature-rich Discord bot built on **hikari + lightbulb**, focused on media tools, configurable embeds, upload workflows, and moderation/notification utilities.

Primary runtime: `666_hikari.py`

## Overview

This bot is built around a configuration-first workflow with JSON-backed state.  
It supports:

- media downloads from YouTube, Twitter/X, and Instagram
- a themed embed system (including advanced layout themes)
- interactive upload menus with OG metadata presets
- raid/spam detection with moderation controls
- owner/admin tooling and notification routing

## Highlights

### Media Downloader Suite

- `/misc youtube` supports standard videos, Shorts, and `youtu.be` links.
- `/misc twitter` handles normal tweets, media tweets, replies, quotes, and GIF edge cases.
- `/misc instagram` supports reels, posts, stories, highlights, and many Instagram share URLs.

### Twitter/X Quality-of-Life

- Rich Twitter-style embeds for fallback/link-only cases and uploaded results.
- Embed content includes:
  - display name + `@handle`
  - profile image
  - tweet text
  - preview image (when available)
  - reply context
  - quote context
  - clickable quote-author link (when quote URL exists)
  - `Open Post` link
- Optional auto-convert feature for message links:
  - converts `twitter.com` / `x.com` links to `fxtwitter.com`
  - reposts converted links
  - deletes original message

### Upload Workflow

- Interactive domain + preset selection menu.
- Optional custom OG modal (`title`, `description`, `color`).
- Placeholder support in presets (`{user}`, `{date}`, `{time}`, `{total_uploads}`, `{file_size}`).
- Upload history, per-user/global stats, deletion, and owner purge.

### Theme System

- `/theme` command group for selecting, listing, previewing, adding, and deleting themes.
- Supports standard theme fields and v2 layout-style theme data.
- Global private mode integrates with command responses.

## Command Reference

### General Commands

| Command | Description |
|---|---|
| `/misc help` | Command browser with category selector |
| `/misc invite` | Bot install links |
| `/misc checkuser` | Username availability check (TikTok/YouTube/Twitter) |
| `/misc techstack` | Detect website technologies |
| `/misc youtube` | Download YouTube video/Shorts |
| `/misc twitter` | Download Twitter/X media or return rich tweet card |
| `/misc instagram` | Download Instagram reel/post/story/highlight |
| `/misc capture` | Capture network traffic report (authorized only) |
| `/ping` | Gateway/API latency check |
| `/projectinfo` | Runtime/system stats |
| `/changelog` | Paginated changelog |

### Upload Commands

| Command | Access | Description |
|---|---|---|
| `/misc upload` | Authorized | Interactive upload menu |
| `/misc delete` | Authorized | Delete by deletion URL |
| `/misc uploads` | Authorized | Upload stats/history |
| `/misc purge` | Owner | Remove upload log entry |

### Owner/Admin Commands

| Command | Description |
|---|---|
| `/misc access` | Grant/revoke authorized users |
| `/misc listauth` | List authorized users |
| `/misc private` | Toggle private response mode |
| `/misc notifystatus` | Show notification status |
| `/misc notifyconfig` | Toggle notification event types |
| `/misc setnotifychannel` | Set/disable notification channel |

### Theme Commands (Owner)

| Command | Description |
|---|---|
| `/theme select` | Set active theme |
| `/theme list` | List all themes |
| `/theme current` | Preview active theme |
| `/theme add` | Add theme from JSON |
| `/theme delete` | Delete theme |

### Guild Settings

| Command | Permission | Description |
|---|---|---|
| `/misc twitterpreview` | Manage Server or Owner | Toggle auto Twitter/X preview conversion per guild |
| `/misc prefix` | Manage Server or Owner | Set legacy prefix per guild |

### Context + Legacy

- Message command: `Enlarge Emoji/Sticker`
- Legacy reply commands: `.emoji` and `.sticker` (or configured prefix)

## Media Behavior Details

### YouTube

Format target:

- with ffmpeg: `bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best`
- without ffmpeg: `best[ext=mp4]/best`

This prioritizes quality while preserving Discord-friendly output.

### Twitter/X

- Uses robust fallback logic through status metadata + media endpoint parsing.
- GIF handling attempts true `.gif` extraction from FxTwitter unfurls via a configured probe channel.
- If no downloadable media exists, returns a rich tweet card instead of hard-failing.

### Instagram

- Normalizes many share URLs (`instagram.com/s/...`) into valid highlight/story candidates.
- Supports login-gated retry flow:
  - `YTDLP_COOKIES_FILE` if configured
  - `YTDLP_COOKIES_BROWSERS` list (default fallback: `firefox`)
- When ffmpeg is available, Instagram outputs are recoded to Discord-compatible H.264/AAC MP4.

## Permissions Model

- **Owner-only** commands remain strictly owner-gated.
- **Authorized-user** commands are controlled through `auth.json`.
- **Private mode** affects visibility of command responses (ephemeral where supported).

## Configuration Files

| File | Purpose |
|---|---|
| `themes.json` | Theme registry, active theme, private mode, default preview toggle |
| `auth.json` | Authorized user IDs |
| `uploads.json` | Upload records and deletion metadata |
| `upload_configs.json` | Upload domain endpoints/tokens |
| `og_presets.json` | OG metadata preset definitions |
| `bot_config.json` | Notification channel + per-guild preview config |
| `prefixes.json` | Per-guild legacy prefix mapping |

## Notes

- The bot auto-installs some missing Python packages at runtime.
- For Instagram stories/highlights/private content, a logged-in browser cookie source is required.
