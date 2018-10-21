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
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt update
sudo apt install python-certbot-nginx
sudo certbot --nginx
```

_Note: may need to update the nginx conf with the location of the cert and key_

## Install Postgres

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

#### Create a superuser role for yourself

```bash
sudo -u postgres createuser --interactive
```

#### Create a role and db for expensus

```bash
createuser --interactive
 # expensus
 # superuser? no
createdb expensus_production
```

Then open a console using `psql expensus_production` and set owner to the expensus role.

```sql
alter database expensus_production OWNER TO expensus;
```

#### Connecting to DB from local Datagrip

Under general:

```
host: localhost  |  port: 5432
database: expensus_production
user: expensus
```

Under ssh/ssl:

```
proxy host: expensus.app
proxy user: <your_name>
auth type: Key pai (OpenSSH or PuTTY)
private key file: /Users/<your_user>/.ssh/id_rsa (or wherever your ssh key is)
passphrase: (if you have a key passphrase)
```

## Installing Node via NVM

```bash
sudo apt update
sudo apt install build-essential libssl-dev
# get from https://github.com/creationix/nvm#install-script
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

Reload your shell and install the desired version (and yarn)

```bash
nvm install 10.12.0
npm i -g yarn
```

You may need to fix the ownership of the project directory:

```bash
sudo chmod 0777 expensus
```

## Installing Foreman

```bash
sudo apt install ruby-foreman
```
