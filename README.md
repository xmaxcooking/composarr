# Composarr

Composarr is a comprehensive repository that offers a quickstart solution to set up an 'arr-stack', aiming to streamline and optimize your media management processes. It combines multiple tools to provide seamless management of media content, from selecting and indexing to downloading, transcoding, and finally streaming.

## Features:
- **Media Selection**: Using **Overseerr**, you can easily choose the media content you wish to add to your collection.
- **Indexer Management**: **Prowlarr** handles and syncs your indexers.
- **Media Requests**: **Sonarr** and **Radarr** receive requests from Overseerr and relay them to **qBittorrent** for downloading.
- **Downloading**: **qBittorrent** fetches the requested torrents and relocates them to a designated folder.
- **Transcoding**: **Tdarr** inspects the download folder, queues media for transcoding, and upon completion, shifts them to Plex media folders for streaming. 
- **Captchas**: **Flaresolverr** allows you to bypass captchas of public torrent indexers.
- **Self-Updating**: **Watchtower** automatically updates all your services and keeps your containers up-to-date and secure.
- **Anonymous**: **Gluetun** provides a secure network layer for downloading. (Warning! Not all services are using this layer by default. Sonarr and Radarr are currently optional)
- **Self-Configuring**: **Recyclarr** automatically syncs the newest media filters into your services to prevent low-quality media from appearing in your requests.
- **Statistics**: **Tautulli** provides insights into your Plex usage.

Harness the capabilities of all these features, setting up a powerful media stack with ease!

# Disclaimer

Composarr is purely a tool for media organization and does not host or distribute any copyrighted content. Users are responsible for ensuring the content they manage or view using this toolset complies with local laws and copyright regulations. The creator of Composarr accepts no responsibility or liability for any misuse or violation of laws by users. Always use this software responsibly and in accordance with applicable regulations.

## Links

1. **FlareSolverr** ([flaresolverr/flaresolverr](https://github.com/FlareSolverr/FlareSolverr))
2. **Gluetun** ([qmcgaw/gluetun](https://github.com/qdm12/gluetun))
3. **Overseerr** ([linuxserver/overseerr](https://github.com/sct/overseerr))
4. **Prowlarr** ([linuxserver/prowlarr](https://github.com/Prowlarr/Prowlarr)
5. **qBittorrent** ([linuxserver/qbittorrent](https://github.com/qbittorrent/qBittorrent))
6. **Radarr** ([linuxserver/radarr](https://github.com/Radarr/Radarr))
7. **Recyclarr** ([recyclarr/recyclarr](https://github.com/recyclarr/recyclarr))
8. **Sonarr** ([linuxserver/sonarr](https://github.com/Sonarr/Sonarr))
9.  **Tautulli** ([linuxserver/tautulli](https://github.com/Tautulli/Tautulli))
10. **Tdarr** ([ghcr.io/haveagitgat/tdarr](https://github.com/HaveAGitGat/Tdarr))
11. **Watchtower** ([containrrr/watchtower](https://github.com/containrrr/watchtower))

## Prerequisites
1. Clone the Repository: 
```console
git clone https://github.com/xmaxcooking/composarr.git
```
2. Ensure Docker is installed on your system.
3. Make sure the necessary ports are not being used by other applications.

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

### 4. qBittorrent Configuration
Access qBittorrent at `localhost:8090` (Default credentials: `admin/adminadmin`):

- Under `Settings > Downloads`:
  - Set default save path: `/data/torrents/completed`
  - Ensure incoming torrents are kept at `/data/torrents/incoming`.
  - Define a monitored folder at `/data/torrents/watch`.
  - Add an external program for completed torrents: `unrar x "%D/*.r*" "%D/"`
- Under `Settings > Advanced`, set the network interface to `tun0`.

### 5. Sonarr Configuration
Connect to Sonarr at `localhost:8989`:

- Navigate to `Settings > Media Management`:
  - Add root folder: `/data/raw/series/`
  - Add another root folder: `/data/raw/anime/`
- Under `Settings > General`, copy the API key for future use.
- Under `Settings > Download Client`, add qBittorent.
  - Add your IP as Host and Port to `8090`

### 6. Radarr Configuration
Connect to Radarr at `localhost:7878`:

- Navigate to `Settings > Media Management` and add root folder `/data/raw/movies/`.
- Under `Settings > General`, copy the API key for future use.
- Under `Settings > Download Client`, add qBittorent.
  - Add your IP as Host and Port to `8090`

### 7. Prowlarr Configuration
Access Prowlarr at `localhost:9696`:

- Set a password (keep it safe). Optionally, you can disable authentication for local addresses.
- Under `Settings > Apps`, add the Sonarr and Radarr services using the previously saved API keys.
  
**Note**: Replace `localhost` in the Prowlarr and Radarr server input fields with your local IP.

### 8. Indexer Proxy Setup
In Prowlarr, navigate to `Settings > Indexers`:
  
- Add Flaresolverr as an indexer proxy. Update the localhost part in the URL with your IP and assign a 'flaresolverr' tag.

### 9. Add Public Indexers
At this stage, integrate a few public indexers into Prowlarr and synchronize them with your applications.

### 10. Overseerr Configuration
Connect to Overseerr at `localhost:5055`:

- Sign in with your Plex account and link your Plex service.
- Synchronize libraries for movies, anime, and series.
- Connect Radarr and Sonarr:
  - Provide all necessary inputs and select the previously configured root folders.

**Note**: No need to add Sonarr twice for different root folders.

### 11. Tautulli Setup
Connect to Tautulli at `localhost:8181`:

- Set up a login, authenticate with Plex, and connect the Plex service.

### 12. Tdarr Setup
Connect to Tdarr at `localhost:8265`

#### Configure Node

- select `MyInternalNode` on the main page.
- Click the plus on Transcode GPU
- Click the plus on Health Check: CPU 
- Check `Auto accept successful transcodes`

#### Configure Library

- go to libraries and a new library named raw.
  - check folder watch and scan on start.
  - set the source to /media/raw
  - check the hourly scan or configure to your taste.
  - go to the transcode tab and set /temp (this is not mapped as a volume in the docker compose)
  - go to output folder and set /media/media
  - check output folder, copy to output if met and delete source file. don't record history. 

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

### 13. Recyclarr Setup

- Open the recyclarr.yml file
  - replace localhost with your IP.
  - Add your radarr and sonarr API keys.
- Restart the recyclarr container

---

### Wrapping Up

With all these configurations, you've now set up a robust media stack. You can now add all your indexers to Prowlarr and start selecting media on Overseerr. Use the flaresolverr tag if necessary. Should you encounter any issues or need further guidance, please refer to the individual documentation of each service or raise an issue on our [GitHub repository](https://github.com/xmaxcooking/composarr/issues).

Happy streaming! 🎬

### Credits

A special shoutout to [Dr Frankenstein](https://drfrankenstein.co.uk/) for his invaluable contribution and dedication to the Docker community. His in-depth tutorials on Synology Docker have paved the way for many enthusiasts and have been my guiding light for Composarr.

### License

Composarr is licensed under the MIT License. This means you can freely use, modify, distribute, and for private use. We are not liable for any damages or warranties. For the full license details, you can [view the MIT License here](https://opensource.org/licenses/MIT).
