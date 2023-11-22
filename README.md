# [Docker media server](https://github.com/PARC6502/docker-media-server)

## Containers

- Sonarr - Anime Downloader
- Transmission - Torrent Client
- Prowlarr - Indexer Proxy for Sonarr
- Jellyfin - Media Server
- Heimdall - Application dashboard
- Shoko - Anime organization

## Setup

All references to `localhost` may need to be replaced with your local IP address (e.g. `192.168.1.242`)

1. Clone repo

```bash
git clone https://github.com/nouun/media-server
cd media-server
```

2. Setup and source environment variables
```
cp example.env .env
vi .env
source .env
```

3. Create directories
```bash
mkdir -p ./config                   \
         ./config/jellyfin          \
         ./config/jellyfin/cache    \
         ./config/jellyfin/confi    \
         ./config/shoko             \
         ./config/deluge            \
         ./config/heimdall          \
         ./config/sonarr            \
         ./config/prowlarr          \
         ${DOWNLOAD_DIR}/complete   \
         ${DOWNLOAD_DIR}/incomplete \
         ${DOWNLOAD_DIR}/torrents   \
         ${ANIME_DIR}    
```

4. Create Docker containers

```bash
sudo docker-compose up -d
```

5. Configure Heimdal

Go to http://localhost:8888 and add the following apps, replace localhost with your local IP if needed.
  - Transmission: `http://localhost:9091`
  - Radarr: `http://localhost:7878`
  - Sonarr: `http://localhost:8989`
  - Jackett: `http://localhost:9117`
  - Jellyfin: `http://localhost:8096`
  - Shoko: `http://localhost:8111`

6. Configure Transmission

```bash
SESSION_HEADER=$(curl --silent http://localhost:9091/transmission/rpc/ | sed 's/.*<code>//g;s/<\/code>.*//g')
curl --silent --header "$SESSION_HEADER" "http://localhost:9091/transmission/rpc" -d "{\"method\":\"session-set\",\"arguments\": {\"download-dir\":\"/downloads\"}}"
```

7. Configure Prowlarr and Sonarr

Follow the instruction on Prowlarr to add indexers to Sonarr.

8. Configure Jellyfin

Go to https://localhost:8096 and run through the setup wizard, when prompted to add media locations add the following:
 - Shows: `/media/anime`

## Updating

If you've made changes to the docker compose file you'll need to stash them for the git pull to work

```bash
sudo docker-compose up -d --force-recreate --build
```
