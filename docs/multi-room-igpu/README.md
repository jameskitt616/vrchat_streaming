# Multi stream instance

### Prerequisites

1. If not already done, [install Docker and Docker Compose](https://docs.docker.com/engine/install/)
2. Configure [networking](https://github.com/jameskitt616/vrchat_streaming/blob/master/docs/networking.md)

### Install

1. Copy and configure your docker compose
   file `curl -L -o compose.yaml https://raw.githubusercontent.com/jameskitt616/vrchat_streaming/refs/heads/master/docs/multi-room-igpu/compose.yaml`
2. Update the passwords in the docker compose file
3. Create a users file to protect the neko-rooms admin panel `htpasswd -bc usersfile admin 'your-secure-password'` (you need to have `apache2-utils` for `htpasswd` installed)
4. Run `docker compose up -d` to run the Docker containers in detached daemon mode
5. If you set up your reverse-proxy properly you can now open n.eko in your local Browser.

You will now find your n.eko at `https://neko.yourdomain.tld`

Once in n.eko-rooms, you create a new room. Under Environment variables add:
- `NEKO_BROADCAST_URL` => `rtmp://rtsp_server:1935/live`, `rtmp://rtsp_server:1935/party`, `rtmp://rtsp_server:1935/movie` (stream key "live" can be changed to anything, use different stream keys for the different streams, see `rtsp_server` section of compose file.)
- (fixed bitrate exmaple. INFO: update the port for each room you configure in the pipeline, to match it to the correct port you set for the `MTX_PATHS_XXXXX_SOURCE` variable at the `rtsp_server` section in the docker compose file) `NEKO_BROADCAST_PIPELINE` => `mpegtsmux name=mux alignment=7 ! queue max-size-time=2000000000 ! udpsink host=rtsp_server port=1234 buffer-size=2097152 ximagesrc display-name={display} show-pointer=true use-damage=false ! video/x-raw,framerate=30/1 ! queue max-size-buffers=300 leaky=downstream ! videoconvert ! video/x-raw,format=NV12 ! vaapih264enc rate-control=cbr bitrate=6144 keyframe-period=10 quality-level=2 ! h264parse config-interval=1 ! video/x-h264,stream-format=byte-stream,profile=main ! queue max-size-buffers=300 ! mux. pulsesrc device={device} ! audio/x-raw,channels=2 ! queue max-size-buffers=300 leaky=downstream ! audioconvert ! voaacenc bitrate=320000 ! audio/mpeg,rate=48000 ! aacparse ! queue max-size-buffers=300 ! mux.`

You can now open the live stream in VRChat (or VLC) at `https://mtx.youdomain.tld/live/index.m3u8`, `https://mtx.youdomain.tld/party/index.m3u8`, `https://mtx.youdomain.tld/live/movie.m3u8` \
(Optionally you can open the live stream in your local Browser as well at e.g. `https://mtx.youdomain.tld/live`, `https://mtx.youdomain.tld/party`, `https://mtx.youdomain.tld/movie`)
