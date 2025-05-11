+++
title = 'Nextcloud Installation & Setup with Database'
date = 2025-05-10T22:01:04+05:30
draft = true
summary = "Hosting Nextcloud in Raspberry Pi 5 with database setup, allows HTTPS access and Mobile Synchronization"
+++


# Generate `SSL` Certificate

# Install Nextcloud with `SSL`

```sh
version: "2.1"
services:
  nextcloud:
    image: lscr.io/linuxserver/nextcloud:latest
    container_name: nextcloud
    environment:
      - PUID=995
      - PGID=100
      - TZ=Asia/Kolkata
    volumes:
      - /srv/dev-disk-by-uuid-3df56cd5-3c81-4180-90db-58d530d81ad2/nextcloud/config:/config
      - /srv/dev-disk-by-uuid-3df56cd5-3c81-4180-90db-58d530d81ad2/data:/data
      - /srv/dev-disk-by-uuid-3df56cd5-3c81-4180-90db-58d530d81ad2/nextcloud/ssl/selfsigned.crt:/config/ssl/server.crt:ro
      - /srv/dev-disk-by-uuid-3df56cd5-3c81-4180-90db-58d530d81ad2/nextcloud/ssl/selfsigned.key:/config/ssl/server.key:ro
    ports:
      - 8080:80
      - 8443:443
    restart: unless-stopped
```

# Database Setup

```sh
-- Login to MariaDB (from container or host)
mysql -u root -p

-- Create the database
CREATE DATABASE nextcloud;

-- Create a user and grant privileges
CREATE USER 'nextclouduser'@'%' IDENTIFIED BY 'strongpassword';

-- Grant all privileges on the new database to the new user
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'%';

-- Save changes
FLUSH PRIVILEGES;
```

This warning means that Nextcloud is blocking access because your current IP (e.g., `192.168.31.136`) isn't listed as a trusted domain in `config.php`.

---

### ✅ To fix this:

Edit the `config.php` file located in your Nextcloud config volume:

```bash
nano /srv/dev-disk-by-uuid-3df56cd5-3c81-4180-90db-58d530d81ad2/nextcloud/config/www/nextcloud/config/config.php
```

Find the line that looks like:

```php
'trusted_domains' => 
array (
  0 => 'localhost',
),
```

And update it to include your IP address and any domain/port you’re using:

```php
'trusted_domains' => 
array (
  0 => 'localhost',
  1 => '192.168.31.136',
  2 => '192.168.31.136:8443',
),
```

Save and exit.

---

### 🔄 Then restart the container:

```bash
docker restart nextcloud
```

Now you should be able to access Nextcloud without the “untrusted domain” error.

Would you like to set a local DNS name (like `nextcloud.local`) instead of using the IP?

