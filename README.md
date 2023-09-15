# [<img alt="alt_text" width="50px" src="https://www.codeslikeaduck.com/img/codeDuck.svg" />](https://www.codeslikeaduck.com/) selfhosted-services
A summary of the services I host.<br />
All services are sitting behind Authelia 2FA with routing via Traefik.<br />
Services are hosted as Docker containers on a linux home server.<br />
Only config files in the repo are the docker-compose yaml files.<br />

<p align="Left">
<img align="center" src="/images/heimdallScreenshot.png" alt="Hosted services" width="380"><br \>
</p>

</br>

## The Services
1. [Traefik](#traefik)
2. [Authelia](#authelia)
3. [Docker-Socket-Proxy](#docker-socket-proxy)
4. [Traefik-Geoip-Filter](#traefik-geoip-filter)
5. [Jitsi](#jitsi)
6. [Photoview](#photoview)
7. [Wishlist (Christmas-Community)](#wishlist-christmas-community)
8. [DogCam (Go2rtc)](#dogcam-go2rtc)
9. [Heimdall](#heimdall)
10. [Dev Blog](#dev-blog)

### Traefik
- [Traefik (V2)](https://github.com/traefik/traefik#readme) is a reverse proxy and is the backbone of the set up. 
- Traefik sits between the services and the outside world (specifically http and https ports 80 and 443).
- Each service has a corresponding subdomain registered at the DNS and Traefik routes each subdomain request to the correct service.
- This allows traffic to *service.example.com* to access the service without opening the service external port on the network.
- As well as convenience, Traefik handles security as all traffic is routed through middlewares where requests can be filtered and modified before they reach the service.
- Main middlewares in this set up are http to https redirect, request rate limiter, a middleware applying range of http security headers and a middleware routing requests through Authelia for two factor authentication.
- A different http security header middleware is define for each service to allow future customisation.

### Authelia
- [Authelia](https://github.com/authelia/authelia#readme) is an authentication server providing two factor authentication for all the services.
- Allows users to be created and access level (no auth, single factor, two factor, no access) on a per service basis.
- Supports U2F and one time passwords via Google authenticator, but currently 2FA is set up via mobile push notifications using Duo.
<p align="Left">
<img align="center" src="/images/autheliaDemo.gif" alt="2FA demo of accessing heimdall" width="275"><br \>
</p>

### Docker-Socket-Proxy
- [Docker-Socket-Proxy](https://github.com/Tecnativa/docker-socket-proxy#readme) a proxy layer for between traefik and the docker socket.
- Traefik requires access to the docker socket to work and Traefik is exposed to the internet. This means an attack on traefik could grant the attacker root access on the underlying host. 
- [Recommended](https://doc.traefik.io/traefik/providers/docker/#docker-api-access) by traefik devs, docker-socket-proxy only allows access to the sections of the docker API that Traefik needs to function.

### Traefik-Geoip-Filter
- [Traefik-geoip-filter](https://github.com/mpdcampbell/traefik-geoip-filter) acts as geography based block/allowlist middleware for Traefik.
- It sets up a local Nginx webserver, that acts as an authserver for the [forwardAuth middleware](https://doc.traefik.io/traefik/middlewares/http/forwardauth/).
- You define the countries, counties, cities you want to allow/block traffic from, it grabs the IP addresses that match those locations from a database and makes an "IPchecklist".
- Assigning this middleware to a service's router, Traefik will route all requests to authserver first, and will only pass them on to the service if the authserver says the origin IP is/isn't in the allow/block list. Otherwise the request is returned with a 404 code.
- Useful for the services which can't be proxied through Cloudflare due to their no video/large files policy (i.e. Jitsi or CCTV).
- I think it's great, but I also made it.

### Jitsi
- [Jitsi](https://github.com/jitsi/jitsi-meet#readme) is a full featured video conferencing platform. Essentially host your own Zoom, but with HD video streaming, no time limits, increased security and end to end encryption support.
- Setting this up during the pandemic has been huge quality of life upgrade over Zoom, I highly recommend it. 
- Jitsi consists of four services working together. 
    - **Jitsi Meet (Jitsi_web):** The frontend interface for Jitsi. 
    - **Jitsi Videobridge (JVB):** The traffic controller, a server that routes video streams between participants
    - **Jitsi Conference Focus (Jicofo):** Conference manager, it opens and manages the sessions between participants and JVB.
    - **[Prosody](https://github.com/prosody):** An XMPP server where all the other components connect so they can communicate, and linked as the only component not made by the Jitsi team.

### Wishlist (Christmas-community)
- [Christmas-community](https://github.com/Wingysam/Christmas-Community#readme) is a private Amazon wishlist alternative.
- Supports separate user accounts with basic user & password auth, with all wishlists visible to all users.
- Users can add items to their wishlist with the product URL and for supported websites (30ish) the service grabs the product image, name and price.
- Otherwise image, name and price can be input manually.

### Photoview
- [Photoview](https://github.com/photoview/photoview#readme) is an actively developed photo & video gallery UI that maintains your directory structure.
- Features local facial recognition for photo sorting, tiered user account access, and supports RAW files and EXIF parsing.
- This is another service I highly recommend to anyone with years of family photos languishing on a hard drive.
- Consists of two services as it requires a SQL database for caching thumbnails, parsed photo data and smaller file versions for download.
- I have a [MariaDB](https://github.com/MariaDB/mariadb-docker#readme) container for this (phdb in the docker-compose yml).

### DogCam (Go2rtc)
- [Go2rtc](https://github.com/AlexxIT/go2rtc#readme) is a minimalist video feed streaming app.
- Supports a huge range of protocols and sources, even ffmpeg, and all with minimal configuration.
- I use it to capture the feed from an IP camera to act as a self hosted pet cam.
- For this use case, I highly recommend go2rtc over other popular CCTV services that are bloated with features you don't need.
- To make is as simple to use as possible, I added a [redirectregex middleware](https://doc.traefik.io/traefik/middlewares/http/redirectregex/) to redirect homepage traffic directly to the video feed.
<details> <summary> Expand for screenshot of go2rtc </summary>
    
- Rather than show my cameras, I set up two ffmpeg feeds playing Big Buck Bunny.
    <p align="Left">
    <img align="center" src="/images/go2rtcScreenshot.png" alt="Screenshot of go2rtc playing two video feeds" width="275"><br \>
    </p>
    </details>

### Heimdall
- [Heimdall](https://github.com/linuxserver/Heimdall#readme) is an application dashboard, used here as a simple homepage.

### Dev Blog
- Copy of [codeslikeaduck.com](https://codeslikeaduck.com) separately hosted to act as a dev environment.
- Allows me to tinker with the site without risking downtime.

</br>

## Art
<p float="left">
  <img src="/images/dogCamIcon.png" alt="DogCam Icon" width="80" />
  <img src="/images/photoviewIcon.png" alt="PhotoView Icon" width="80" /> 
  <img src="/images/jitsiIcon.png" alt="Jitsi Icon" width="80" />
  <img src="/images/wishlistIcon.png" alt="Wishlist Icon" width="80" /> </br>
  Icons made by <a href="https://www.freepik.com" title="Freepik">Freepik</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a>
</br>
  <img src="/images/traefikIcon.png" alt="DogCam Icon" width="80" /> </br>
  Road Icon made by <a href="https://www.flaticon.com/authors/smashicons" title="Smashicons">Smashicons</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a></br>
  <img alt="Duck Icon" width="80" src="/images/duckIcon.png" /> </br>
Duck Icon made by me.
