# [Docker media server](https://github.com/PARC6502/docker-media-server)

## Containers

- Jellyfin - Media Server
- Sonarr - TV Downloader
- Radarr - Movie Downloader
- Deluge - Torrent Client
- Jackett - Indexer Proxy for Radarr and Sonarr

## Setup

All references to `localhost` can be replaced with your local IP address (e.g. `192.168.1.242` for me)

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
mkdir -p config/jellyfin/cache config/jellyfin/config \
         ${DLDIR}/completed ${DLDIR}/incomplete       \
         ${MOVIESDIR} ${TVDIR}
```

4. Edit Flemmarr

Edit `config/flemmarr/config.yml` and replace `192.168.1.242` with `localhost` or your local IP.


5. Create Docker containers

```bash
sudo docker-compose up -d
```

6. Configure Heimdal

Go to http://localhost:80 and add the following apps, replace localhost with your local IP if needed.
  - Transmission: `http://localhost:9091`
  - Radarr: `http://localhost:7878`
  - Sonarr: `http://localhost:8989`
  - Jackett: `http://localhost:9117`
  - Jellyfin: `http://localhost:8096`

7. Configure Transmission

```bash
SESSION_HEADER=$(curl --silent http://localhost:9091/transmission/rpc/ | sed 's/.*<code>//g;s/<\/code>.*//g')
curl --silent --header "$SESSION_HEADER" "http://localhost:9091/transmission/rpc" -d "{\"method\":\"session-set\",\"arguments\": {\"download-dir\":\"/downloads\"}}"
```

8. Configure Jackett, Radarr, and Sonarr

Go to https://localhost:9117, scroll to the bottom and set "FlareSolverr API URL:" to `http://localhost:8191`. Follow the instruction on Jackett to add indexers to Sonarr and Radarr. Most other Sonarr and Radarr settings will be preset through Flemmarr.

9. Configure Jellyfin

Go to https://localhost:8096 and run through the setup wizard, when prompted to add media locations add the following:
 - Movies: `/media/movies`
 - Shows: `/media/tv`

## Updating

If you've made changes to the docker compose file you'll need to stash them for the git pull to work

```bash
sudo docker-compose up -d --force-recreate --build
```
