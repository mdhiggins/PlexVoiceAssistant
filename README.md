# PlexVoiceAssistant

Home Assistant blueprints for controlling Plex clients via Assist voice commands.

## Overview

These blueprints enable voice control of Plex playback using Home Assistant's conversation agent. They support playing movies, TV shows (with season/episode selection), and playlists. Will use area aware player selection if a player is not specified.

Two implementations are provided:
- **PlexVoiceAssistant.yaml**: Uses Plex.tv API for client discovery
- **PlexVoiceAssistantLocal.yaml**: Uses local server discovery and `media_player.play_media`

The main version queries Plex.tv for current client connection details, which provides more reliable routing and connection handling. The local variant avoids the Plex.tv dependency but relies on Home Assistant's client discovery, which can be slower and less responsive to client state changes. Both versions are included to support users who prefer fully local control without external API dependencies.

## Requirements

- Home Assistant with Plex integration configured
- Plex Media Server
- Plex clients registered in Home Assistant
- REST command integration (for API calls)
- Valid Plex authentication token

## Installation

### 1. Configure REST Commands

Add the REST command configuration from `rest_command.yaml` to your `configuration.yaml`. The file defines GET and POST helpers for Plex API calls.

If your Plex server uses self-signed certificates or you access it via local IP address, you may need to uncomment the `verify_ssl: false` lines to avoid SSL verification errors.

### 2. Import Blueprint

Import via URL or copy the blueprint file to your `blueprints/automation` directory:

```
https://github.com/yourusername/PlexVoiceAssistant/blob/main/PlexVoiceAssistant.yaml
```

### 3. Create Automation

Create a new automation from the blueprint and configure:

- **Plex Server**: Select your Plex Media Server device
- **Plex Token**: Your Plex authentication token (get from https://www.plex.tv/claim/)
- **Scan Clients Button**: Select the "Scan for clients" button entity from your Plex integration

## Usage

### Voice Commands

```
Play <media> [on <player>]
Play <movie/show/playlist> <name> [season <number>] [episode <number>] [on <player>]
Shuffle <media>
```

### Examples

```
Play The Office
Play The Office season 5
Play The Office season 5 episode 14
Play The Office on Living Room TV
Shuffle Always Sunny
Play playlist Favorites
```

### Player Targeting

Players can be targeted two ways:

1. **By name**: "Play X on Living Room TV"
2. **By area**: Triggers from a device in an area will target Plex clients in that area

## Technical Details

### Client Discovery

The main blueprint queries `/api/v2/resources` from Plex.tv to get current client connection information. For each targeted client:

1. Retrieves available connections (local/remote, HTTP/HTTPS)
2. Prefers local HTTP connections when available
3. Falls back to other connection types as needed

### REST Command Structure

The `rest_command.yaml` file defines two services:

- `rest_command.plex_api_get`: GET requests with token authentication
- `rest_command.plex_api_post`: POST requests with token and optional client targeting

Both commands include commented `verify_ssl: false` options that can be uncommented if needed for self-signed certificates.

## Troubleshooting

### No clients available

- Ensure Plex clients are actively running and registered
- Press the "Scan for clients" button to refresh client list
- Check that clients appear in Plex integration device registry

### SSL/certificate errors

If you encounter SSL verification errors, uncomment the `verify_ssl: false` line in both REST commands in your configuration. This is typically needed when accessing Plex via local IP address or when using self-signed certificates.
