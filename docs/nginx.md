# Nginx

## Resources

[How to install Nginx](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04)

[How to secure Nginx with Let's Encrypt](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)

[How to set up a Node.JS application for production](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-16-04)

[How to set up nginx server blocks (virtual hosts)](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-virtual-hosts-on-ubuntu-16-04)

[How to install linux, nginx, mysql, php](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-in-ubuntu-16-04)

## Configuration

### Content

- `/var/www/html`: Nginx config directory

### Configuration

#### /etc/nginx

The Nginx configuration directory.
All of the Nginx configuration files reside here.

#### /etc/nginx/nginx.conf

The main Nginx configuration file.
This can be modified to make changes to the Nginx global configuration.

#### /etc/nginx/sites-available/

The directory where per-site “server blocks” can be stored.

Nginx will not use the configuration files found in this directory
unless they are linked to the sites-enabled directory (see below).

Typically, all server block configuration is done in this directory,
and then enabled by linking to the other directory.

#### /etc/nginx/sites-enabled/

The directory where enabled per-site “server blocks” are stored.  Typically,
these are created by linking to configuration files found
in the sites-available directory.

#### /etc/nginx/snippets

This directory contains configuration fragments that can be included
elsewhere in the Nginx configuration. Potentially repeatable configuration
segments are good candidates for refactoring into snippets.

### Server Logs

#### /var/log/nginx/access.log

Every request to your web server is recorded in this
log file unless Nginx is configured to do otherwise.

#### /var/log/nginx/error.log

Any Nginx errors will be recorded in this log.

