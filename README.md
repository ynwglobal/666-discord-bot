# 666 Discord Bot (Hikari)

Current runtime is `666_hikari.py` (hikari + lightbulb).

## Features

- Embed theming engine (`/theme ...`) with v1/v2 theme formats.
- Global private mode (`/misc private`) to make command responses ephemeral.
- Upload system with domain/preset picker, custom OG modal, upload logs, delete/purge tools.
- Twitter/X auto-preview fixer (guild-level toggle) that converts Twitter/X links to FxTwitter and deletes original message.
- Media downloader commands for YouTube, Twitter/X, and Instagram.
- Raid/spam detection with moderation action components.
- Notification channel + per-event notification toggles.
- Legacy reply commands `.emoji` / `.sticker` (custom per-guild prefix).

## Config Files

- `themes.json`: themes, current theme, private mode, default twitter preview toggle.
- `auth.json`: authorized users.
- `uploads.json`: upload history and deletion metadata.
- `upload_configs.json`: upload host domains/tokens.
- `og_presets.json`: OG preset definitions.
- `bot_config.json`: notification channel and per-guild twitter preview settings.
- `prefixes.json`: per-guild legacy prefix.

## Media Download Notes

### YouTube (`/misc youtube`)

- Supports normal videos + Shorts + `youtu.be`.
- Format preference:
  - with ffmpeg: `bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best`
  - without ffmpeg: `best[ext=mp4]/best`
- This targets highest practical quality while keeping Discord-friendly outputs.

### Twitter/X (`/misc twitter`)

- Uses API/media fallback logic for better reliability.
- For GIF posts, tries extracting true `.gif` via FxTwitter unfurl probe channel.
- For text-only/no-media posts, returns a rich Twitter-style embed instead of failing.
- Twitter result embeds now include:
  - display name + `@handle`
  - profile image
  - tweet text
  - preview media image when available
  - reply context (`Replying to @...`) when present
  - quote context with clickable quoted-author link when available
  - `Open Post` link

### Instagram (`/misc instagram`)

- Supports reel/post/tv/story/highlight URLs, including many `instagram.com/s/...` share links.
- Highlight share links are normalized and resolved through multiple candidate URL forms.
- For login-gated content (stories/highlights/private media), downloader retries with cookies:
  - `YTDLP_COOKIES_FILE` (if provided)
  - browsers from `YTDLP_COOKIES_BROWSERS` (default fallback: `firefox`)
- With ffmpeg, Instagram outputs are recoded to Discord-safe H.264/AAC MP4.

## Commands

### Public / Utility

- `/misc help`
- `/misc invite`
- `/misc checkuser`
- `/misc techstack`
- `/misc youtube`
- `/misc twitter`
- `/misc instagram`
- `/misc capture` (authorized users)
- `/ping`
- `/projectinfo`
- `/changelog`

### Uploads

- `/misc upload` (authorized users)
- `/misc delete` (authorized users)
- `/misc uploads` (authorized users)
- `/misc purge` (owner only)

### Owner/Admin

- `/misc access`
- `/misc listauth`
- `/misc private`
- `/misc notifystatus`
- `/misc notifyconfig`
- `/misc setnotifychannel`
- `/theme select`
- `/theme list`
- `/theme current`
- `/theme add`
- `/theme delete`

### Guild Settings

- `/misc twitterpreview` (Manage Server or owner)
- `/misc prefix` (Manage Server or owner)

### Context + Legacy

- Message command: `Enlarge Emoji/Sticker`
- Legacy reply commands: `.emoji`, `.sticker` (or configured guild prefix)

## Permission Model

- Owner-only commands are still owner-gated.
- Non-owner responses follow normal visibility unless private mode is on.
- When private mode is enabled, command responses use ephemeral where applicable.
