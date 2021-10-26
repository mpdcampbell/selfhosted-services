# selfhosted-services
A summary of my selfhosted services.<br />
All services as docker containers, sitting behind authelia 2FA with routing via traefik.<br />
Only config file in repo is the docker-compose.yml.<br />

<p align="Left">
<img align="center" src="/images/heimdallScreenshot.png" alt="Hosted services" width="380"><br \>
</p>

## The Services
1. [Authelia](#authelia)
2. [Traefik](#traefik)
3. [Jitsi](#jitsi)
4. [Shinobi](#shinobi)
5. [Photoview](#photoview)
6. [Heimdall](#heimdall)

### Authelia


### Traefik
Traefik (V2) is a reverse proxy and is the backbone of the set up. <br />
Traefik sits between the services and the outside world (specifically http and https ports 80 and 443).<br />
Each service has a corresponding subdomain registered at the DNS and Traefik routes each subdomain request to the correct service.<br />
This allows traffic to *jitsi.example.com* to access jitsi service without opening the jitsi external port on the network.<br />
As well as convenience, Traefik increases security as all traffic is routed through middlewares where requests can be filtered and modified before the reach the service.<br />


### Heimdall
### Jitsi
### Shinobi
### Photoview
