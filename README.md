# VRChat streaming

Tool to stream anything (e.g. Emby/Plex/Jellyfin/YouTube etc.) to VRChat without hustle

---

> [!NOTE]
> The default configuration of this project utilizes the integrated graphics provided by an Intel CPU.

# Project description

[n.eko](https://neko.m1k1o.net/#/) is a virtual-shared Browser which allows multiple people viewing the exact same
stream of the virtual Browser in your local Browser.
One person has the control and can open any website they want, just like you would operate your local Browser.
The control can be passed on. Read more about it, on the [n.eko website](https://neko.m1k1o.net/).\
Taking advantage of that, n.eko brings functionality to also stream that
output [e.g. via RTMP](https://neko.m1k1o.net/docs/v3/configuration/capture#broadcast) anywhere you like (
in this case to a self-hosted real-time media server called [MediaMTX](https://github.com/bluenviron/mediamtx)).\
So the general workflow would be:

- One Person operating n.eko in their local Browser e.g. starting a Video on their self-hosted Emby/Plex/Jellyfin Server
- n.eko must be set up to stream the output of n.eko (is configured that it happens automatically on n.eko startup in
  this case)
- The person puts in a HLS/RTSP link to the live stream into any VRChat video player where everyone can view the stream

# General infos

- Use a Server with a fairly modern Intel CPU to also make usage of the iGPU. Check [Recommended Specs](https://neko.m1k1o.net/docs/v3/quick-start).
- RTSP Streams don't work for Standalone VR-Headsets like Quest 3 due Bugs in Android's media codec, use HLS for this.
- Use H.264 Codec for maximum compatibility, e.g. H.265 or VP9 might not work on some PC's or standalone VR-Headsets.
- I'd recommend a fixed Bitrate of minimum 6000kbps for decent video quality.
- **CHANGE THE PASSWORDS** in the docker-compose file for normal users (`NEKO_MEMBER_MULTIUSER_USER_PASSWORD`) and admins (
  `NEKO_MEMBER_MULTIUSER_ADMIN_PASSWORD`) who can operate n.eko. In general i recommend reading through
  the [n.eko docs](https://neko.m1k1o.net/#/getting-started/configuration) and configure n.eko to your liking.

# Install instructions

> [!NOTE]
> The default configuration of this project utilizes the integrated graphics provided by an Intel CPU.

### Prerequisites

1. If not already done, [install Docker and Docker Compose](https://docs.docker.com/engine/install/)
2. Configure [networking](https://github.com/jameskitt616/vrchat_streaming/blob/master/docs/networking.md)
3. Install Intel Media Driver (I think `apt install intel-media-va-driver` should be enough. Change according to your Linux Distribution)

### Install

1. Copy and configure your docker compose
   file `curl -L -o compose.yaml https://raw.githubusercontent.com/jameskitt616/vrchat_streaming/refs/heads/master/compose.yaml`
2. Update the passwords in the docker compose file
3. Run `docker compose up -d` to run the Docker containers in detached daemon mode
4. If you set up your reverse-proxy properly you can now open n.eko in your local Browser and the HLS/RTSP live stream in VRChat.
5. For persistent Browser storage follow https://neko.m1k1o.net/docs/v3/customization/browsers#persistent-profile

You will now find your n.eko at `https://neko.yourdomain.tld` \
You can now open the live stream in VRChat (or VLC) at `https://mtx.youdomain.tld/live/index.m3u8` \

# Updating

1. Run `docker compose pull && docker compose up -d && docker image prune -f`

# Closing words
### Feel free to open an issue or [contact me](https://jameskitt616.one/contact/) for suggestions/questions/help.

### Thanks to [tenten8401](https://github.com/tenten8401)!
