# Networking
- Open Ports `52000-52100` for WebRTC to view n.eko in your Browser (you can lower the port range ofc, if you expect fewer people to operate/view n.eko in a browser. This port range does not matter for viewing in VRChat)
- The docker-compose I provided is meant to be run behind a reverse proxy. So create a new subdomain e.g. `neko.yourdomain.tld`. Read more at [n.eko reverse proxy](https://neko.m1k1o.net/docs/v3/reverse-proxy-setup)
- For HLS you do not need to open any extra ports, I also suggest to reverse-proxy with a dedicated subdomain to mediamtx e.g. `mtx.yourdomain.tld` -> `localip:8888`. If you do not reverse proxy HLS, you need to opern port `8888`.
- If you want to use RTSP, you need to open Port `8554`.
- I'd absolutely suggest you to use a reverse-proxy AND use SSL/TLS for a secure connection, setting this up goes beyond this guide.
