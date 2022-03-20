# selfhosted-services
A summary of the services I host.<br />
All services are sitting behind authelia 2FA with routing via traefik.<br />
Services are hosted as docker containers on a linux home server.<br />
Only config file in repo is the docker-compose.yml.<br />

<p align="Left">
<img align="center" src="/images/heimdallScreenshot.png" alt="Hosted services" width="380"><br \>
</p>

</br>

## The Services
1. [Traefik](#traefik)
2. [Docker-Socket-Proxy](#docker-socket-proxy)
3. [Authelia](#authelia)
4. [Jitsi](#jitsi)
5. [Photoview](#photoview)
6. [Wishlist (Christmas-Community)](#wishlist-christmas-community)
7. [DogCam (Shinobi)](#dogcam-shinobi)
8. [Heimdall](#heimdall)

### Traefik
- [Traefik (V2)](https://github.com/traefik/traefik#readme) is a reverse proxy and is the backbone of the set up. 
- Traefik sits between the services and the outside world (specifically http and https ports 80 and 443).
- Each service has a corresponding subdomain registered at the DNS and Traefik routes each subdomain request to the correct service.
- This allows traffic to *service.example.com* to access the service without opening the service external port on the network.
- As well as convenience, Traefik handles security as all traffic is routed through middlewares where requests can be filtered and modified before they reach the service.
- Main middlewares in this set up are http to https redirect, request rate limiter, a middleware applying range of http security headers and a middleware routing requests through authelia for two factor authentication.
- A different http security header middleware is define for each service to allow future customisation.

### Docker-Socket-Proxy
- [Docker-Socket-Proxy](https://github.com/Tecnativa/docker-socket-proxy#readme) a proxy layer for between traefik and the docker socket.
- Traefik requires access to the docker socket to work and Traefik is exposed to the internet. This means an attack on traefik could grant the attacker root access on the underlying host. 
- [Recommended](https://doc.traefik.io/traefik/providers/docker/#docker-api-access) by traefik devs, docker-socket-proxy only allows access to the sections of the docker API that Traefik needs to function.

### Authelia
- [Authelia](https://github.com/authelia/authelia#readme) is an authentication server providing two factor authentication for all the services.
- Allows users to be created and access level (no auth, single factor, two factor, no access) on a per service basis.
- Supports U2F and one time passwords via Google authenticator, but currently 2FA is set up via mobile push notifications using Duo.
<p align="Left">
<img align="center" src="/images/autheliaDemo.gif" alt="2FA demo of accessing heimdall" width="275"><br \>
</p>

### Jitsi
- [Jitsi](https://github.com/jitsi/jitsi-meet#readme) is a full featured video conferencing platform. Essentially host your own Zoom, but with HD video streaming, no time limits, increased security and end to end encryption support.
- Setting this up during the pandemic has been huge quality of life upgrade over Zoom, I highly recommend it. 
- The bulk of my docker-compose yml, Jitsi consists of four services working together. 
    - **Jitsi Meet (Jitsi_web):** The frontend interface for Jitsi. 
    - **Jitsi Videobridge (JVB):** The traffic controller, a server that routes video streams between participants
    - **Jitsi Conference Focus (Jicofo):** Conference manager, it opens and manages the sessions between participants and JVB.
    - **[Prosody](https://github.com/prosody):** An XMPP server where all the other components connect so they can communicate, and linked as the only component not made by the Jitsi team.

### Wishlist (Christmas-community)
- [Christmas-community](https://github.com/Wingysam/Christmas-Community#readme) is a private Amazon wishlist alternative.
- Supports seperate user accounts with basic user & password auth, with all wishlists visible to all users.
- Users can add items to their wishlist with the product url and for supported websites (30ish) the service grabs the product image, name and price.
- Otherwise image, name and price can be input manually.

### Photoview
- [Photoview](https://github.com/photoview/photoview#readme) is an actively developed photo & video gallery UI that mantains your directory structure.
- Features local facial recognition for photo sorting, tiered user account access, and supports RAW files and EXIF parsing.
- This is another service I highly recommend to anyone with years of family photos languishing on a harddrive.
- Consists of two services as it requires a SQL database for caching thumbnails, parsed photo data and smaller file versions for download.
- I have a [MariaDB](https://github.com/MariaDB/mariadb-docker#readme) container for this (phdb in the docker-compose yml).

### DogCam (Shinobi)
- [Shinobi](https://gitlab.com/Shinobi-Systems/Shinobi/-/blob/master/README.md) is a CCTV service for viewing and managing an array of camera streams.
- I use it as a web UI to remotely view and record an IP camera for checking on the dog.
- Provides security and privacy peace of mind as I can keep the cheap IP camera blocked from external network while Shinobi grabs the feed from local network to expose it externally.

### Heimdall
- [Heimdall](https://github.com/linuxserver/Heimdall#readme) is an application dashboard, used here as a simple homepage.

</br>

## Art
<p float="left">
  <img src="/images/dogCamIcon.png" alt="DogCam Icon" width="80" />
  <img src="/images/photoviewIcon.png" alt="PhotoView Icon" width="80" /> 
  <img src="/images/jitsiIcon.png" alt="Jitsi Icon" width="80" />
  <img src="/images/wishlistIcon.png" alt="Wishlist Icon" width="80" /> Icons made by <a href="https://www.freepik.com" title="Freepik">Freepik</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a>
</br>
  <img src="/images/traefikIcon.png" alt="DogCam Icon" width="80" /> Road Icon made by <a href="https://www.flaticon.com/authors/smashicons" title="Smashicons">Smashicons</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a>
