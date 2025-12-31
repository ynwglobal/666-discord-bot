# 666 Discord Bot

This document details the internal mechanics, feature set, and command structure of the 666 Discord Bot. The bot is designed with a modular architecture focusing on advanced embed theming, file uploading with metadata injection, automated moderation, and system monitoring.

## System Architecture

The bot operates on a configuration-driven model using persistent JSON storage for state management. It utilizes asynchronous network requests to handle file uploads and status checks without blocking the main event loop.

### Configuration Files
*   **themes.json**: Stores visual themes and global display settings like private mode and Twitter preview toggles.
*   **auth.json**: Manages the whitelist of users authorized to use restricted commands.
*   **uploads.json**: Logs the history of all file uploads, including URLs, timestamps, and deletion keys.
*   **prefixes.json**: Stores custom command prefixes on a per-guild basis.
*   **bot_config.json**: Stores logging configuration, including the webhook URL and toggle states for specific event notifications.
*   **upload_configs.json**: Contains API credentials and endpoints for the file hosting services.
*   **og_presets.json**: Defines reusable templates for Open Graph meta tags used during file uploads.

## Core Features

### Theming Engine
The bot employs a dynamic theming system that intercepts outgoing embeds and applies visual properties based on the active configuration.

*   **Standard Themes**: Applies consistent colors, footer text, author icons, and thumbnail images to embeds.
*   **V2 Layout Engine**: Supports complex embed structures defined by a layout array. This allows for:
    *   **Text Injection**: Inserting static text or dynamic content placeholders.
    *   **Separators**: Visual dividers between content sections.
    *   **Images**: Defining specific image placements.
    *   **Sections**: Creating distinct fields within the embed.
*   **Private Mode**: A global toggle that forces all bot responses to be "ephemeral" (visible only to the command initiator), ensuring privacy during operation.

### File Upload System
A proxy system for uploading files to external hosts with custom metadata.

*   **Open Graph Injection**: The bot sends a JSON payload alongside the file data to inject custom `og:title`, `og:description`, and `og:color` tags into the resulting URL.
*   **Interactive Menu**: The upload command triggers a UI with dropdowns to select the destination domain and metadata presets.
*   **Presets**: Users can select pre-defined metadata templates. These templates support placeholders like `{user}`, `{date}`, `{time}`, and `{file_size}` which are populated at runtime.
*   **Logging & Management**: Every upload is logged. Users can view their upload history, and authorized users can delete files directly through the bot using the stored deletion keys.

### Automated Moderation
The bot monitors guild activity for patterns indicative of raids or spam.

*   **Raid Detection**: Triggers if a specific number of members join within a short timeframe (default: 5 members in 8 seconds).
*   **Spam Detection**: Triggers if a single user sends a specific number of messages rapidly (default: 5 messages in 5 seconds).
*   **Mitigation Tools**: When an alert is triggered, the bot sends a notification with interactive buttons to Ban, Mute (Timeout), or Dismiss the alert. The Mute button includes a dropdown to select the timeout duration.

### Notification System
A centralized logging system that forwards events to a configured webhook.

*   **Event Types**:
    *   Command Errors
    *   Member Bans and Unbans (includes moderator and reason from audit logs)
    *   Raid and Spam Detections
    *   Bot Guild Joins/Leaves
    *   Message Deletions and Edits
*   **Configuration**: The owner can toggle specific notification types on or off via a GUI menu.

### Utilities
*   **Service Status**: Checks the API status of Cloudflare, Vercel, and Netlify.
*   **Twitter/X Fixer**: Automatically detects `twitter.com` and `x.com` links in messages and replaces them with `fxtwitter.com` links to ensure media embeds render correctly. The original message is deleted.
*   **System Info**: Displays real-time CPU usage, RAM usage, uptime, and latency.

## Command Reference

### General & Utility
*   `/projectinfo`: Displays detailed system statistics, bot version, and resource usage.
*   `/checkstatus [service]`: Checks the operational status of Vercel, Cloudflare, or Netlify.
*   `/voice join`: Joins the voice channel the user is currently in.
*   `/voice leave`: Disconnects the bot from the current voice channel.
*   `/nightyscripting help`: Provides documentation links for Nighty UI scripting.
*   `/prefix [new_prefix]`: Sets the command prefix for the current server (Requires Manage Server permission).
*   `.emoji` (Reply Only): Enlarges custom emojis found in the replied message and provides their image links.

### Theme Management (Owner Only)
*   `/theme select [theme_name]`: Sets the active visual theme for the bot.
*   `/theme list`: Displays a list of all available themes.
*   `/theme current`: Shows a preview of the currently active theme.
*   `/theme add [name] [json_string]`: Adds a new theme configuration via a JSON string.
*   `/theme delete [theme_name]`: Deletes an existing theme configuration.

### File Uploads & Access (Authorized Users)
*   `/misc upload [file]`: Opens the interactive menu to upload a file with custom domain and Open Graph settings.
*   `/misc delete [deletion_url]`: Deletes a file from the host using its deletion URL.
*   `/misc nightyauth`: Displays the authentication GIF (Authorized users only).

### Administration & Configuration (Owner Only)
*   `/misc access [user] [grant/revoke]`: Grants or revokes access to restricted commands (like upload) for a specific user.
*   `/misc listauth`: Lists all users who have been granted access.
*   `/misc private [on/off]`: Toggles "Private Mode", making all bot responses ephemeral.
*   `/misc uploads [user]`: Views upload statistics. If a user is specified, shows their uploads; otherwise, shows global stats.
*   `/misc purge [upload_url]`: Removes a specific upload record from the bot's local log without deleting the file from the host.
*   `/misc setnotifychannel [channel]`: Sets the text channel where the bot will send log notifications.
*   `/misc notifystatus`: Checks the current status of the notification channel configuration.
*   `/misc notifyconfig`: Opens an interactive menu to toggle specific notification events (e.g., bans, errors, spam).