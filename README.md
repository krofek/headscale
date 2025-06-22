## Headscale + Headscale-Admin + Caddy Proxy

Working setup for Headscale server with SSL.
Caddy automatically creates a proxy,
gets a valid TLS certificate from Letsencrypt
and redirects http calls to SSL encrypted https.

### Setup

'''sh
git clone https://github.com/Krofek/headscale
cd headscale
'''

#### Copy and Edit Configs

1.)

'''sh
cp config/headscale/config.example.yaml config/headscale/config.yaml
nano config/headscale/config.yaml
'''

2.)

'''sh
cp config/caddy/Caddyfile.example config/caddy/Caddyfile
nano config/caddy/Caddyfile
'''

#### Start docker and check logs

'''sh
docker compose up -d && docker compose logs -f
'''
