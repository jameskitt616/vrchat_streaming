# Single stream instance iGPU (recommended)
1. If not already done, [install Docker and Docker Compose (v2.10+)](https://docs.docker.com/engine/install/)
2. Copy and configure your docker compose
   file `curl -L -o docker-compose.yml https://raw.githubusercontent.com/jameskitt616/vrchat_streaming/refs/heads/master/single-room-igpu/compose.yaml`
3. Update the passwords in the docker compose file
4. Run `docker compose up -d` to run the Docker containers in detached daemon mode
5. If you set up your reverse-proxy properly you can now open n.eko in your local Browser and the HLS/RTSP live stream in VRChat.

You will now find your n.eko at `https://neko.yourdomain.tld` \
You can now open the live stream in VRChat (or VLC) at `https://mtx.youdomain.tld/live/index.m3u8` \
(Optionally you can open the live stream in your local Browser as well at `https://mtx.youdomain.tld/live`)
