# Traefik + CrowdSec + GeoBlock Setup

## Prerequisites
1. Domain with Cloudflare DNS
2. Docker and Docker Compose installed
3. Basic understanding of Docker networking

## Setup Instructions

### 1. Environment Configuration
Copy the environment template and configure:
```bash
cp .env.example .env
```

Edit `.env` and add your Cloudflare API token

### 2. Manual Configuration Required

#### Edit `config/traefik.yml`:
```yaml
certificatesResolvers:
  cloudflare:
    acme:
      email: email@domain.com # <-- Change this to your email
```

#### Edit `config/dynamic.yml`:
```yaml
# Update geoblock configuration
ar-only:
  plugin:
    geoblock:
      allowedIPAddresses:
        - "YOUR.VPS.IP"  # Add your VPS IP if outside your country
      countries:  
        - AR  # your country code (https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)
        
# Update CrowdSec configuration  
crowdsec:
  plugin:
    bouncer:
      crowdsecLapiKey: ##REPLACE_API_KEY##  # Replace CrowdSec API key (docker exec crowdsec cscli bouncers add 
      forwardedHeadersTrustedIPs:
        - "YOUR.VPS.IP"  # Add your reverse proxy IP
```

### 3. Network and Directory Setup

```bash
docker network create proxy
docker network create crowd1
chmod 600 ./certs

cd crowdsec
docker compose up -d
docker exec crowdsec cscli bouncers add crowdsecBouncer
```
