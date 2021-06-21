# TLS (https)

TODO: the below guide doesn't work...

## How to install SSL certs for Nginx server on Amazon Linux

### Certbot

[Partially from Guide](https://levelup.gitconnected.com/how-to-install-ssl-certificate-for-nginx-server-in-amazon-linux-2986f51371fb)
[Mostly from guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html)

```bash
cd /home/ec2-user
mkdir certbot-dl
cd ./certbot-dl

# Can't install certbot using `sudo yum install certbot` on amazon linux 2
# instead, add "epel" to yum's repo list
sudo wget -r --no-parent -A 'epel-release-*.rpm' http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/
sudo rpm -Uvh dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-*.rpm
sudo yum-config-manager --enable "epel*"
sudo yum repolist all
# install certbot using epel
sudo yum install certbot python2-certbox-nginx

# generate certificate for all domains it will be used for
# use --debug because certbot is experimental on Amazon Linux 2
sudo certbot \
	certonly \
	--standalone\
	--debug \
	-d nickkelly.dev \
	-d planka.nickkelly.dev \
	-d linkace.nickkelly.dev \
	-d firefly.nickkelly.dev \
	-d cms.nickkelly.dev \
	-d api.nickkelly.dev \
	-d http-icons.nickkelly.dev \
	-d gql-ts.nickkelly.dev \
	-d www.planka.nickkelly.dev \
	-d www.nickkelly.dev \
	-d www.cms.nickkelly.dev \
	-d www.api.nickkelly.dev \
	-d www.http-icons.nickkelly.dev \
	-d www.gql-ts.nickkelly.dev \
	-d www.firefly.nickkelly.dev \
  -d docker.nickkelly.dev

# Remove the downloaded files & folders
cd ..
rm -rf certbot-dl
   
# This will create keys in
# /etc/letsencrypt/live/stateofjavascript.com/cert.pem
# /etc/letsencrypt/live/stateofjavascript.com/fullchain.pem
# /etc/letsencrypt/live/stateofjavascript.com/privkey.pem

# Add these private keys to tls server blocks
```

### Nginx 

Modify config at /etc/nginx/nginx.conf

```nginx.conf
... http {
 # https://linuxize.com/post/redirect-http-to-https-in-nginx/#:~:text=The%20preferred%20method%20to%20redirect
 # https://regbrain.com/article/node-nginx-ec2
 # Permanent redirect all http traffic to https
 server {
   listen 80 default_server;
   listen [::]:80 default_server;
   server_name _;
   return 301 https://$host$request_uri;
 }

 # https://levelup.gitconnected.com/how-to-install-ssl-certificate-for-nginx-server-in-amazon-linux-2986f51371
 # tls proxy server block
 server {
   listen                  443 ssl;
   server_name             stateofjavascript.com www.stateofjavascript.com;
   ssl_certificate         "/etc/letsencrypt/live/stateofjavascript.com/fullchain.pem";
   ssl_certificate_key     "/etc/letsencrypt/live/stateofjavascript.com/privkey.pem";
   # add_header Strict-Transport-Security "max-age=31536000";
   # other headers...
   location / {
     proxy_pass http://localhost:3000;
   }
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
