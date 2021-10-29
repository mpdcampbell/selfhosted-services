# selfhosted-services
A summary of my selfhosted services.<br />
All services are sitting behind authelia 2FA with routing via traefik.<br />
Services are hosted as docker containers on a linux home server.<br />
Only config file in repo is the docker-compose.yml.<br />

<p align="Left">
<img align="center" src="/images/heimdallScreenshot.png" alt="Hosted services" width="380"><br \>
</p>

## The Services
1. [Traefik](#traefik)
2. [Authelia](#authelia)
3. [Jitsi](#jitsi)
4. [Shinobi](#shinobi)
5. [Photoview](#photoview)
6. [Heimdall](#heimdall)

### Traefik
- [Traefik (V2)](https://github.com/traefik/traefik#readme) is a reverse proxy and is the backbone of the set up. <br />
- Traefik sits between the services and the outside world (specifically http and https ports 80 and 443).<br />
- Each service has a corresponding subdomain registered at the DNS and Traefik routes each subdomain request to the correct service.<br />
- This allows traffic to *jitsi.example.com* to access jitsi service without opening the jitsi external port on the network.<br />
- As well as convenience, Traefik handles security as all traffic is routed through middlewares where requests can be filtered and modified before the reach the service.<br />
- Main middlewares in this set up are http to https redirect, request rate limiter, a middleware applying range of http security headers and a middleware routing requests through authelia for two factor authentication.<br />
- A different http security header middleware is define for each service to allow future customisation.

### Authelia
- [Authelia](https://github.com/authelia/authelia#readme) is an authentication server providing two factor authentication for all the services.
- Allows users to be created and access level (no auth, single factor, two factor, no access) on a per service basis.
- Supports U2F and one time passwords via Google authenticator, but currently 2FA is set up via mobile push notifications using Duo.
<p align="Left">
<img align="center" src="/images/autheliaDemo.gif" alt="2FA demo of accessing heimdall" width="380"><br \>
</p>


### Jitsi
- [Jitsi](https://github.com/jitsi/jitsi-meet#readme) A full featured video conferencing platform. Essentially host your own Zoom, but with HD video streaming, no time limits, increased security and end to end encryption support.</br> 
- Setting this up during the pandemic has been huge quality of life upgrade over Zoom and I highly recommend it. </br>
- The bulk of my docker-compose yml, Jitsi consists of four services working together. </br>
    - **Jitsi Web:**
    - **Jitsi Videobridge (JVB):**
    - **Jitsi Conference Focus (Jicofo):**
    - **[Prosody](https://github.com/prosody):** An XMPP server, and linked as the only component not made by the Jitsi team.

### Shinobi
### Photoview
### Heimdall
- [Heimdall](https://github.com/linuxserver/Heimdall#readme) is an application dashboard, used here as a simple homepage.

