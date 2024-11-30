# VRChat streaming

Tool to stream anything (e.g. Emby/Plex/Jellyfin etc.) to VRChat without hustle

---

# Project description

[n.eko](https://neko.m1k1o.net/#/) is a virtual-shared Browser which allows multiple people viewing the exact same
stream of the virtual Browser in your local Browser.
One person has the control and can input/open any website they want, just like you would operate your local Browser.
The control can be passed on. Read more about it, on the [n.eko website](https://neko.m1k1o.net/).\
Taking advantage of that, n.eko brings functionality to also stream that
output [e.g. via RTMP](https://neko.m1k1o.net/#/getting-started/configuration?id=neko_broadcast_url) anywhere you like (
in this case to a self-hosted real-time media server called [MediaMTX](https://github.com/bluenviron/mediamtx)).\
So the general workflow would be:

- One Person operating n.eko in their local Browser e.g. starting a Video on their self-hosted Emby/Plex/Jellyfin Server
- n.eko must be set up to stream the output of n.eko (is configured that it happens automatically on n.eko startup in
  this case)
- The person puts in a link to the live stream into any VRChat video player where everyone can view the stream

# General infos

- This guide was written for Linux Servers. (Maybe I will investigate for Windows(WSL) use in the future)
- Use a Server with a fairly modern CPU (+4 threads) because this will live encode two 1080p streams which is resource
  intensive. Check out [Recommended Specs](https://neko.m1k1o.net/#/getting-started/quick-start?id=quick-start). I would
  absolutely recommend using an iGPU for encoding, pure CPU encoding mostly results in poor performance like low frame
  live streams.
- Use RTSP to Streams due to better stability. Use HLS (despite it having higher latency ~7s -> which should not matter
  much when just streaming a video anyway) if you need to stream to Quest Users due Bugs in Android's media codec.
- Use H.264 Codec for maximum compatibility, e.g. H.265 or VP9 might not work on some PC's or standalone VR-Headsets.
- I'd recommend a fixed Bitrate of minimum 4000kbps for decent video quality, especially when watching dark/hectic video
  scenes to prevent the video to look mushy. (I will include an example for variable Bitrate, but commented out. In case
  video quality is less important, or you want to save on Bandwidth. (`NEKO_BROADCAST_PIPELINE` parameter in the
  docker-compose file))
- Depending on your CPU's power you can play with
  the [encoding presets](https://gstreamer.freedesktop.org/documentation/x264/index.html?gi-language=c#GstX264EncPreset),
  currently it's set to `speed-preset=veryfast`
- **CHANGE THE PASSWORDS** in the docker-compose file for normal users (`NEKO_PASSWORD`) and admins (
  `NEKO_PASSWORD_ADMIN`) who can operate n.eko. In general i recommend reading through
  the [n.eko docs](https://neko.m1k1o.net/#/getting-started/configuration) and configure n.eko to your liking.

# Install instructions

Single Room Installation: This option allows for the installation and setup of a single room and stream at a time.

Multi-Room Installation: With this option, you can create multiple virtual rooms and streams, enabling several users to operate different virtual browsers simultaneously.

| Transcode Type        | Single Room (Linux)                                                                                  | Multi Room (Linux)                                                                                  | Single Room (Windows)     | Multi Room (Windows)      |
|-----------------------|------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|---------------------------|---------------------------|
| CPU (not recommended) | [docs](https://github.com/jameskitt616/vrchat_streaming/blob/master/docs/single-room-igpu/README.md) | not docs or supported yet                                                                           | not docs or supported yet | not docs or supported yet |
| iGPU Intel            | [docs](https://github.com/jameskitt616/vrchat_streaming/blob/master/docs/single-room-cpu/README.md)  | [docs](https://github.com/jameskitt616/vrchat_streaming/blob/master/docs/multi-room-igpu/README.md) | not docs or supported yet | not docs or supported yet |
| iGPU AMD              | not docs or supported yet                                                                            | not docs or supported yet                                                                           | not docs or supported yet | not docs or supported yet |
| dGPU Nvidia           | not docs or supported yet                                                                            | not docs or supported yet                                                                           | not docs or supported yet | not docs or supported yet |
| dGPU AMD              | not docs or supported yet                                                                            | not docs or supported yet                                                                           | not docs or supported yet | not docs or supported yet |

# Updating

1. Run `docker compose pull && docker compose up -d && docker image prune -f`

### Feel free to open an issue or [contact me](https://jameskitt616.one/contact/) for suggestions/questions/help.

### Thanks to [tenten8401](https://github.com/tenten8401)!
