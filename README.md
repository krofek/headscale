## Headscale + Headscale-Admin + Caddy Proxy

> [!NOTE]
> This is a minimal pre-configured setup.
> If you need some advanced settings, like
> private dns addresses, creating a derp node, etc.
> edit the config files accordingly.

Caddy automatically creates a proxy,
gets a valid SSL certificate from Letsencrypt
and redirects http (```:80```) calls to https (```:443```).


### Setup

```sh
git clone https://github.com/Krofek/headscale
cd headscale
```


#### Copy and Edit Configs

Headscale config:

```sh
cp config/headscale/config.example.yaml config/headscale/config.yaml
nano config/headscale/config.yaml
```

Caddy config:

```sh
cp config/caddy/Caddyfile.example config/caddy/Caddyfile
nano config/caddy/Caddyfile
```


#### Start docker and check logs

```sh
docker compose up -d && docker compose logs -f
```


#### Create Api Key

```
docker exec -it headscale headscale apikey create
```

Copy and save the api key somewhere safe, you can't access the same api key later. 
You can still create new and delete the old ones in case you lose it.

Visit ```https://<SERVER_URL>/admin``` and enter the 
api key. Now you have access to headscale on the 
admin web dashboard.


### Tailscale client

I suggest creating user pre-auth keys for a smoother and faster client connection.


#### Rpi Dns fix

One of the links / symlinks works, haven't tested which one.

```
sudo apt install systemd-resolved

sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
sudo ln /usr/bin/resolvectl /usr/bin/systemd-resolve

echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.conf

sudo sysctl -p /etc/sysctl.conf

sudo systemctl restart systemd-resolved
sudo systemctl restart NetworkManage
sudo systemctl restart tailscaled
```


#### Node connection example

Replace the placeholder strings accordingly.


```
sudo tailscale up /
--login-server=<HEADSCALE_URL> /
--reset --force-reauth /
--accept-dns --accept-routes /
--advertise-tags=tag:<TAG_NAME> /
--advertise-routes=192.168.0.0/24  /
--advertise-exit-node  /
--auth-key=<PREAUTH_KEY>
```

The advertise routes and exit node oprions are optional,
you can accept or deny in admin page anyway.


## Important

At the moment headscale-admin doesn't fully support Headscale v0.26.
Mostly the problems arise while setting up ACL in the dashboard. Since Headscale v0.26 needa user names to include a @ in the ACL config.

I suggest using the ACL setup normally but instead of saving the config normally, go to the Config tab and add @ at the end of user names directly in the config text editor and then save.
