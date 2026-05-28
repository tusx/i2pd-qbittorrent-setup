# i2pd + Qbittorrent Setup in Podman
this is a simple guide to setup qbittorrent with i2pd to start Torrenting in i2p network

## Create Podman Network
to keep the i2p seperate from other podman containers its best to create a specific network for this setup.
```
podman network create i2p-net 
```
or you can name it whatever you want

## Create i2pd Container
```
podman run -d \
--name i2pd \
--net i2p-net \
--restart unless-stopped \
-v i2pd_data:/var/lib/i2pd:z \
-p 127.0.0.1:7070:7070 \
-p 127.0.0.1:4444:4444 \
-p 127.0.0.1:4447:4447 \
-p 4567:4567 \
-p 4567:4567/udp \
docker.io/purplei2p/i2pd:latest
```
all the other ports `-p ...` can be removed but leave the `4567` and `4567/udp` ports because those are the ones i2p uses to comunicate with others.
`-v i2pd_data:...` you can name the volum whatever you want and you can even mount it to your host if you wish.

## Create Qbittorrent Container
```
podman run -d \
-e PUID=0 \
-e PGID=0 \
--name i2p-qbittorrent \
--net i2p-net \
-e TZ=Etc/UTC \
-e WEBUI_PORT=8080 \
-p 8080:8080 \
-v i2p_qbit_conf:/config:z \
-v /home/user/Downloads:/downloads:Z \
--restart unless-stopped \
lscr.io/linuxserver/qbittorrent:latest
```
`-v i2p_qbit_conf:...` same, you can name it whatever or you can mount it on host.
`-v /home/user/Dow...` mount it where you want the torrents to be downloaded.
`-e PUID=0` and `-e PGID=0` leave them as is. because how the linuxserver container handles the permissions it needs to be set as `root` or in this case `0`. To the container it is running as "root" inside its isolated environment, to your host system, it possesses no more power than your standard user account.

The Setup isn't done yet you need to configure Qbittorrent now to make it work on i2p.

## Configure Qbittorrent
once you login to the Web UI go to `Tools` -> `Options...`
