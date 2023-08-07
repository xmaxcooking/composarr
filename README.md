# Composarr

Composarr offers a consolidated toolset designed to streamline and manage your media stack. With a myriad of docker images, it ensures optimal performance and functionality. This document briefly describes each Docker image used by Composarr.

## Docker Images Utilized by Composarr

1. **FlareSolverr** ([flaresolverr/flaresolverr](https://github.com/FlareSolverr/FlareSolverr))
   - A proxy server for bypassing anti-bot protections, assisting in scraping data from websites.

2. **Gluetun** ([qmcgaw/gluetun](https://github.com/qdm12/gluetun))
   - A robust VPN (Virtual Private Network) in a Docker container, essential for online privacy.

3. **Overseerr** ([linuxserver/overseerr](https://github.com/sct/overseerr))
   - Provides a frontend to manage requests for movies and TV shows across your other services.

4. **Plex**
   - A media server application that allows you to stream your movies, music, and TV series.

5. **Prowlarr** ([linuxserver/prowlarr](https://github.com/Prowlarr/Prowlarr))
   - An indexer management tool which integrates with Radarr, Sonarr, etc., to provide metadata and search capabilities.

6. **qBittorrent** ([linuxserver/qbittorrent](https://github.com/qbittorrent/qBittorrent))
   - A lightweight torrent client known for its ease of use.

7. **Radarr** ([linuxserver/radarr](https://github.com/Radarr/Radarr))
   - A movie collection manager for Usenet and BitTorrent users.

8. **Recyclarr** ([recyclarr/recyclarr](https://github.com/recyclarr/recyclarr))
   - A utility to help integrate Radarr and Sonarr for enhanced media management.

9. **Sonarr** ([linuxserver/sonarr](https://github.com/Sonarr/Sonarr))
   - Manages TV show series; fetches, sorts, and renames episodes.

10. **Tautulli** ([linuxserver/tautulli](https://github.com/Tautulli/Tautulli))
    - A monitoring application for Plex Media Server; provides analytics and notifications.

11. **Tdarr** ([ghcr.io/haveagitgat/tdarr](https://github.com/HaveAGitGat/Tdarr))
    - A self-hosted solution to transcode, organize, and process your media files.

12. **Watchtower** ([containrrr/watchtower](https://github.com/containrrr/watchtower))
    - Monitors running Docker containers and updates them to the latest versions.

By using Composarr, you harness the power of all these tools in a unified, efficient manner. Ensure to check out their individual documentations for in-depth configurations and details.

---

# Disclaimer
 
Composarr is purely a tool for media organization and does not host or distribute any copyrighted content. Users are responsible for ensuring the content they manage or view using this toolset complies with local laws and copyright regulations. The creator of Composarr accepts no responsibility or liability for any misuse or violation of laws by users. Always use this software responsibly and in accordance with applicable regulations.

---

## Prerequisites
1. Ensure Docker is installed on your system.
2. Make sure the necessary ports are not being used by other applications.

---

## Setup Instructions

### 1. VPN Configuration
Modify the `gluetun-compose.yml` file with your VPN provider's details. For setup instructions, consult the [provider-specific documentation](https://github.com/qdm12/gluetun-wiki/tree/main/setup/providers).

### 2. Initial Setup
Execute the setup command:

```console
npm start
```


### 3. Plex Configuration
After ensuring all containers are running, access Plex at `localhost:32400/web`:

- Sign in or create an account if you don’t have one.
- Give your server a descriptive name.
- Add media folders in the following order:
  - Movies: `/data/media/movies` (Type: Movies)
  - Series: `/data/media/series` (Type: Series)
  - Anime: `/data/media/anime` (Type: Series)
  
**Note**: Disable Plex Pass advanced features if you don't plan on purchasing it.

### 4. Sonarr Configuration
Connect to Sonarr at `localhost:8989`:

- Navigate to `Settings > Media Management`:
  - Add root folder: `/data/raw/series/`
  - Add another root folder: `/data/raw/anime/`
- Under `Settings > General`, copy the API key for future use.

### 5. Radarr Configuration
Connect to Radarr at `localhost:7878`:

- Navigate to `Settings > Media Management` and add root folder `/data/raw/movies/`.
- Under `Settings > General`, copy the API key for future use.

### 6. Prowlarr Configuration
Access Prowlarr at `localhost:9696`:

- Set a password (keep it safe). Optionally, you can disable authentication for local addresses.
- Under `Settings > Apps`, add the Sonarr and Radarr services using the previously saved API keys.
  
**Note**: Replace `localhost` in the Prowlarr and Radarr server input fields with your local IP.

### 7. Indexer Proxy Setup
In Prowlarr, navigate to `Settings > Indexers`:
  
- Add Flaresolverr as an indexer proxy. Update the localhost part in the URL with your IP and assign a 'flaresolverr' tag.

### 8. Add Public Indexers
At this stage, integrate a few public indexers into Prowlarr and synchronize them with your applications.

### 9. Overseerr Configuration
Connect to Overseerr at `localhost:5055`:

- Sign in with your Plex account and link your Plex service.
- Synchronize libraries for movies, anime, and series.
- Connect Radarr and Sonarr:
  - Provide all necessary inputs and select the previously configured root folders.

**Note**: No need to add Sonarr twice for different root folders.

### 10. Tautulli Setup
Connect to Tautulli at `localhost:8181`:

- Set up a login, authenticate with Plex, and connect the Plex service.

### 11. qBittorrent Configuration
Access qBittorrent at `localhost:8090` (Default credentials: `admin/adminadmin`):

- Under `Settings > Downloads`:
  - Set default save path: `/data/torrents/completed`
  - Ensure incoming torrents are kept at `/data/torrents/incoming`.
  - Define a monitored folder at `/data/torrents/watch`.
  - Add an external program for completed torrents: `unrar x "%D/*.r*" "%D/"`
- Under `Settings > Advanced`, set the network interface to `tun0`.

### 12. Tdarr Setup

#### Configure Transcode Options

- Implement the following plugin stack in sequence (find them in the community tab):
  
  1. **Tdarr_plugin_Img1_Reorder_Streams** - Reorders streams for optimal compatibility.
  2. **Remove burned closed captions** - Ensures that any hardcoded subtitles are removed.
  3. **Migz-Remove image formats from file** - Cleans out any unwanted image formats embedded in the media file.
  4. **Migz-Clean title metadata** (Options: `clean_audio: false`, `clean_subtitles: false`) - This cleans up the title metadata while preserving the audio and subtitle data.
  5. **Migs-Remux container** (Options: `container: mkv`, `force_conform: true`) - Remuxes files into the specified container for uniformity.
  6. **Remove data streams** - Clears out any non-media data streams.
  7. **Migz-Clean audio streams** (Options: `language: eng,jpn,und`, `commentary: false`, `tag_language: eng`, `tag_title: false`) - Cleans up audio streams based on given language preferences.
  8. **Migz-Clean subtitle streams** (Options: `language: eng,jpn`, `commentary: true`, `tag_language: eng`) - Similar cleanup for subtitle streams.
  9. **Migz-Convert audio streams** (Options: `acc_stereo: true`, `downmix: true`) - Converts audio streams to desired formats or channels.

> **Note**: The plugins' order and configurations are crucial for ensuring the best quality and compatibility. Adjust them as per your specific requirements.

---

### Wrapping Up

With all these configurations, you've now set up a robust media stack. Ensure regular backups of your configurations to prevent any data loss.

Should you encounter any issues or need further guidance, please refer to the individual documentation of each service or raise an issue on our [GitHub repository](https://github.com/xmaxcooking/composarr/issues).

Happy streaming! 🎬