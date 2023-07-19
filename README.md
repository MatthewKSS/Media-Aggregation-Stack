# Media-Aggregation-Stack
This is a general media aggregation stack that I created using the *arr services for downloading and sorting media for PLEX

This stack uses various *arr Docker projects alongside SabNZBD as a downloader.

I use Prowlarr to do most of the indexing connected to NZBGeek, and NewsHosting for my usenet providers.
I have Prowlarr, Sonarr, Lidarr, and Radarr connected in the same stack to talk to each other to keep my media accurate and download new media.
I use SabNZBD as my download client and, at the moment, use an FTP server and client to automatically detect when something get's downloaded
Downloaded files automatically get transferred into my Plex server and sorted into TV shows and Movies.  
Music Files go to a different folder where the storage is for Navidrome.

If you want to use the Media Stack, please download the docker-compose.yml or copy and paste the code into your docker machine. 

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
      ##- /path/to/downloads:/downloads #optional
      ##- /path/to/incomplete/downloads:/incomplete-downloads #optional
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
      ##- /path/to/downloadclient-downloads:/downloads #optional
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
      ##- /path/to/downloadclient-downloads:/downloads #optional
    ports:
      - 8989:8989
    restart: unless-stopped
  lidarr:
    image: lscr.io/linuxserver/lidarr:latest
    container_name: lidarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /path/to/appdata/config:/config
      - /path/to/music:/music #optional
      - /path/to/downloads:/downloads #optional
    ports:
      - 8686:8686
    restart: unless-stopped
~~~
