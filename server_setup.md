## Instructions to configure an API host machine

> #### Note this is mainly note taking / reference, may or may not be up to date as we change deployment configs

---

# Ubuntu 18.10

On initial provision, signin with

```bash
ssh root@server_ip_or_hostname
```

## User Setup

1. Setup a non-root user with:

   ```bash
   adduser jim
   ```

2. Grant non-root user admin privileges with:

   ```bash
   usermod -aG sudo jim
   ```

3. Copy over existing ssh keys to the new user

   ```bash
   rsync --archive --chown=jim:jim ~/.ssh /home/jim
   ```

## Enable Firewall and allow OpenSSH

```bash
ufw app list
ufw allow OpenSSH
ufw enable
ufw status
```

## Install Nginx

```bash
sudo apt update
sudo apt install nginx
```

### Adjust firewall for Nginx

```bash
sudo ufw app list
sudo ufw allow 'Nginx Full'
sudo ufw status
```

#### Nginx is now running, you can check status and perform other operations with:

```bash
sudo systemctl stop nginx
sudo systemctl start nginx
# hard restart
sudo systemctl restart nginx
# soft restart (config changes w/o dropping connections)
sudo systemctl reload nginx
```

#### Nginx Config

Copy nginx config from this repo to `/etc/nginx/nginx.conf` (and any server subfolders)

## Get Certbot running for SSL Certs

```bash
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx
sudo certbot --nginx
```

_Note: may need to update the nginx conf with the location of the cert and key_
