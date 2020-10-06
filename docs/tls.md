# TLS (https)

TODO: the below guide doesn't work...

## How to install SSL certs for Nginx server on Amazon Linux

### Certbot

[Guide](https://levelup.gitconnected.com/how-to-install-ssl-certificate-for-nginx-server-in-amazon-linux-2986f51371fb)

note: this guide is written partly from memory... may not fully work as written

```bash
cd /home/ec2-user

# get certbot
sudo yum install certbot

# generate certificate
sudo certbot certonly --standalone --debug -d stateofjavascript.com -d www.stateofjavascript.com -d api.stateofjavascript.com
# fills in...
# Certificate:
# /etc/letsencrypt/live/stateofjavascript.com/cert.pem
# Full Chain:
# /etc/letsencrypt/live/stateofjavascript.com/fullchain.pem
# Private Key:
# /etc/letsencrypt/live/stateofjavascript.com/privkey.pem
```

### Nginx 

Modify config at /etc/nginx/nginx.conf

```
... http {
  listen 80;
  server_name stateofjavascript.com;
  ...
  server {
    ...
    location / {
      # auto route http to https
      returns 301 https://$server_name$request_uri;
    }
  }
}
server {
  listen 443 ssl;
  server_name stateofjavascript.com;
  ssl_certificate "/etc/letsencrypt/live/stateofjavascript.com/fullchain.pem";
  ssl_certificate_key "/etc/letsencrypt/live/stateofjavascript.com/privkey.pem";

  add_header Strict-Transport-Security "max-age=315360000";
  # other headers

  location / {
    autoindex on;
    root /stateofjavascript.com/; # root path of your domain's index file
    index index.html;
    try_files $uri $uri/ /index.html;
  }
}
```


### Cron job to renew cert

Add a cron job to renew certificate like

If renew fails, then stop nginx and do the renew process again

```sh
# Runs at 8AM on the 28th of every third month
0 8 28 */3 * /home/ec2-user/certbot-auto renew
10 8 28 */3 * service nginx restart
```
