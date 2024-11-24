# VRChat streaming
Tool to stream anything (e.g. Emby/Plex/Jellyfin etc.) to VRChat without hustle

---

# Project description
[n.eko](https://neko.m1k1o.net/#/) is a virtual-shared Browser which allows multiple people viewing the exact same stream of the virtual Browser in your local Browser.
One person has the control and can input/open any website they want, just like you would operate your local Browser.
The control can be passed on. Read more about it, on the [n.eko website](https://neko.m1k1o.net/).\
Taking advantage of that, n.eko brings functionality to also stream that output [e.g. via RTMP](https://neko.m1k1o.net/#/getting-started/configuration?id=neko_broadcast_url) anywhere you like (in this case to a self-hosted real-time media server called [MediaMTX](https://github.com/bluenviron/mediamtx)).\
So the general workflow would be:
- One Person operating n.eko in their local Browser e.g. starting a Video on their self-hosted Emby/Plex/Jellyfin Server
- n.eko must be set up to stream the output of n.eko (is configured that it happens automatically on n.eko startup in this case)
- The person puts in a link to the live stream into any VRChat video player where everyone can view the stream

# General stuff
- Use a Server with a fairly new CPU (+4 threads) because this will live encode two 1080p streams which is resource intensive. Check out [Recommended Specs](https://neko.m1k1o.net/#/getting-started/quick-start?id=quick-start). I would absolutely recommend using an iGPU for encoding, pure CPU encoding mostly results in poor performance like low frame live streams.
- Use HLS as streaming method to VRChat (despite it having higher latency ~7s -> which should not matter much when just streaming a video anyway). It also supports RTSP (and more), which has much lower latency, but apparently due Bugs in Android's media codec it might not work for e.g. Quest Users or other standalone VR-Headsets.
- Use H.264 Codec for maximum compatibility, e.g. H.265 or VP9 might not work on some PC's or standalone VR-Headsets.
- I'd recommend a fixed Bitrate of minimum 4000kbps for decent video quality, especially when watching dark/hectic video scenes to prevent the video to look mushy. (I will include an example for variable Bitrate, but commented out. In case video quality is less important, or you want to save on Bandwidth. (`NEKO_BROADCAST_PIPELINE` parameter in the docker-compose file))
- Depending on your CPU's power you can play with the [encoding presets](https://gstreamer.freedesktop.org/documentation/x264/index.html?gi-language=c#GstX264EncPreset), currently it's set to `speed-preset=veryfast`
- CHANGE THE PASSWORDS in the docker-compose file for normal users (`NEKO_PASSWORD`) and admins (`NEKO_PASSWORD_ADMIN`) who can operate n.eko. In general i recommend reading through the [n.eko docs](https://neko.m1k1o.net/#/getting-started/configuration) and configure n.eko to your liking.

# Networking
- Open Ports `52000-52100` for WebRTC to view n.eko in your Browser (you can lower the port range ofc, if you expect less people to operate/view n.eko in a browser. This port range does not matter for viewing in VRChat)
- The docker-compose i provided is meant to be run behind a reverse proxy. So create a new subdomain e.g. `neko.yourdomain.tld`. Read more at [n.eko reverse proxy](https://neko.m1k1o.net/#/getting-started/reverse-proxy), [n.eko config](https://neko.m1k1o.net/#/getting-started/configuration?id=neko_proxy)
- For HLS you do not need to open any extra ports, i also suggest to reverse-proxy with a dedicated subdomain to mediamtx e.g. `mtx.yourdomain.tld` -> `localip:8888`. If you do not reverse proxy HLS, you need to opern port `8888`.
- If you want to use RTSP, you need to open Port `8554`.
- I'd absolutely suggest you to use a reverse-proxy AND use SSL/TLS for a secure connection, setting this up goes beyond this guide.

# Single stream instance iGPU (recommended)
1. If not already done, [install Docker and Docker Compose (v2.10+)](https://docs.docker.com/engine/install/)
2. Copy and configure your docker compose
   file `curl -L -o docker-compose.yml https://raw.githubusercontent.com/jameskitt616/vrchat_streaming/refs/head/master/docker-compose.single-room-igpu.yml`
3. Update the passwords in the docker compose file
4. Run `docker compose up -d` to run the Docker containers in detached daemon mode
5. If you set up your reverse-proxy properly you can now open n.eko in your local Browser and the HLS live stream in VRChat.

You will now find your n.eko at `https://neko.yourdomain.tld` \
You can now open the live stream in VRChat (or VLC) at `https://mtx.youdomain.tld/live/index.m3u8` \
(Optionally you can open the live stream in your local Browser as well at `https://mtx.youdomain.tld/live`)

# Single stream instance (not recommended)
> Can lead to serious lags, video artifacts etc.
1. If not already done, [install Docker and Docker Compose (v2.10+)](https://docs.docker.com/engine/install/)
2. Copy and configure your docker compose
   file `curl -L -o docker-compose.yml https://raw.githubusercontent.com/jameskitt616/vrchat_streaming/refs/head/master/docker-compose.single-room.yml`
3. Update the passwords in the docker compose file
4. Run `docker compose up -d` to run the Docker containers in detached daemon mode
5. If you set up your reverse-proxy properly you can now open n.eko in your local Browser and the HLS live stream in VRChat.

You will now find your n.eko at `https://neko.yourdomain.tld` \
You can now open the live stream in VRChat (or VLC) at `https://mtx.youdomain.tld/live/index.m3u8` \
(Optionally you can open the live stream in your local Browser as well at `https://mtx.youdomain.tld/live`)

# Multi stream instance
1. If not already done, [install Docker and Docker Compose (v2.10+)](https://docs.docker.com/engine/install/)
2. Copy and configure your docker compose
   file `curl -L -o docker-compose.yml https://raw.githubusercontent.com/jameskitt616/vrchat_streaming/refs/head/master/docker-compose.multi-room.yml`
3. Update the passwords in the docker compose file
4. Create a users file to protect the neko-rooms admin panel `htpasswd -bc usersfile admin 'your-secure-password'`
5. Run `docker compose up -d` to run the Docker containers in detached daemon mode
6. If you set up your reverse-proxy properly you can now open n.eko in your local Browser.

You will now find your n.eko at `https://neko.yourdomain.tld`

Once in n.eko-rooms, you create a new room. Under Environment variables add:
- `NEKO_BROADCAST_URL` => `rtmp://rtsp_server:1935/live`, `rtmp://rtsp_server:1935/party`, `rtmp://rtsp_server:1935/movie` (stream key "live" can be changed to anything, use different stream keys for the different streams, see `rtsp_server` section of compose file.)
- (fixed bitrate exmaple. INFO: update the port for each room you configure in the pipeline, to match it to the correct port you set for the `MTX_PATHS_XXXXX_SOURCE` variable at the `rtsp_server` section in the docker compose file) `NEKO_BROADCAST_PIPELINE` => `mpegtsmux name=mux alignment=7 ! queue max-size-time=2000000000 ! udpsink host=rtsp_server port=1234 buffer-size=2097152 ximagesrc display-name={display} show-pointer=true use-damage=false ! video/x-raw,framerate=30/1 ! queue max-size-buffers=300 leaky=downstream ! videoconvert ! video/x-raw,format=NV12 ! vaapih264enc rate-control=cbr bitrate=6144 keyframe-period=10 quality-level=2 ! h264parse config-interval=1 ! video/x-h264,stream-format=byte-stream,profile=main ! queue max-size-buffers=300 ! mux. pulsesrc device={device} ! audio/x-raw,channels=2 ! queue max-size-buffers=300 leaky=downstream ! audioconvert ! voaacenc bitrate=320000 ! audio/mpeg,rate=48000 ! aacparse ! queue max-size-buffers=300 ! mux.`

You can now open the live stream in VRChat (or VLC) at `https://mtx.youdomain.tld/live/index.m3u8`, `https://mtx.youdomain.tld/party/index.m3u8`, `https://mtx.youdomain.tld/live/movie.m3u8` \
(Optionally you can open the live stream in your local Browser as well at e.g. `https://mtx.youdomain.tld/live`, `https://mtx.youdomain.tld/party`, `https://mtx.youdomain.tld/movie`)

# Updating
1. Run `docker compose pull && docker compose up -d && docker image prune -f`

### Feel free to open an issue or [contact me](https://jameskitt616.one/contact/) for suggestions/questions/help.

### Thanks to [tenten8401](https://github.com/tenten8401)!
