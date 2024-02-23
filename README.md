# Media-Aggregation-Stack
This is a general media aggregation stack that I created using the *arr services for downloading and sorting media for PLEX

This stack uses various *arr Docker projects alongside SabNZBD as a downloader.

Doplarr is used as my discord x overseer integration for media requests so I can request Media on my phone
Overseer manages requests and sends the downloads to Sonarr for TV and Radarr for Movies
Sonarr and Radarr use my indexer Prowlarr to download the NZB data and send it to SabNZBD for unpacking.
These final files get sorted and automatically sent to SMB shares from my plex server so they get automatically added without any input from myself.

If you want to use the Media Stack, please download the docker-compose.yml or copy and paste the code into your own docker-compose.yml.  The only things you would need to change are the file paths, or make sure that the file paths are already created.  Then run docker compose up -d.

~~~yml
version: "2.1"
services:
  prowlarr:
    image: lscr.io/linuxserver/prowlarr:latest
    container_name: prowlarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /path/to/data:/config
    ports:
      - 9696:9696
    restart: unless-stopped
  sabnzbd:
    image: lscr.io/linuxserver/sabnzbd:latest
    container_name: sabnzbd
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /etc/sabnzbd:/config
      - /etc/sabnzbdmedia/successful:/Downloads/complete
      - /etc/sabnzbdmedia/unsuccessful:/Downloads/incomplete
    ports:
      - 8080:8080
    restart: unless-stopped
  radarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: radarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /etc/radarr:/config
      ##- /path/to/movies:/movies #optional
      - /etc/sabnzbdmedia/successful:/Downloads/complete
      - /etc/sabnzbdmedia/Movies:/Downloads/Movies
    ports:
      - 7878:7878
    restart: unless-stopped
  sonarr:
    image: lscr.io/linuxserver/sonarr:latest
    container_name: sonarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /etc/sonarr:/config
      ##- /path/to/tvseries:/tv #optional
      - /etc/sabnzbdmedia/successful:/Downloads/complete
      - /etc/sabnzbdmedia/tvshows:/Downloads/tvshows
    ports:
      - 8989:8989
    restart: unless-stopped
  overseerr:
    image: lscr.io/linuxserver/overseerr:latest
    container_name: overseerr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /path/to/overseerr/config:/config
      - /etc/sabnzbdmedia/successful:/Downloads/complete
      - /etc/sabnzbdmedia/Movies:/Downloads/Movies
      - /etc/sabnzbdmedia/tvshows:/Downloads/tvshows
    ports:
      - 5055:5055
    restart: unless-stopped
  doplarr:
    image: lscr.io/linuxserver/doplarr:latest
    container_name: doplarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - DISCORD__TOKEN=[token]
      - OVERSEERR__API=
      - OVERSEERR__URL=
      - RADARR__API=
      - RADARR__URL=
      - SONARR__API=
      - SONARR__URL=
    #  - DISCORD__MAX_RESULTS=25 #optional
     # - DISCORD__REQUESTED_MSG_STYLE=:plain #optional
     # - SONARR__QUALITY_PROFILE= #optional
     # - RADARR__QUALITY_PROFILE= #optional
     # - SONARR__ROOTFOLDER= #optional
    #  - RADARR__ROOTFOLDER= #optional
     # - SONARR__LANGUAGE_PROFILE= #optional
     # - OVERSEERR__DEFAULT_ID= #optional
      #- PARTIAL_SEASONS=true #optional
      #- LOG_LEVEL=:info #optional
      #- JAVA_OPTS= #optional
    volumes:
      - /path/to/doplarr/config:/config
    restart: unless-stopped
~~~
