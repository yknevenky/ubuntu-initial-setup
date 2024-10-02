# Ubuntu-initial-setup
A note on setup instructions for ubuntu

### Update
```bash
sudo apt update
sudo apt upgrade
```

### Docker setup
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
##### To install the latest version, run:
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

##### Docker compose installation
```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin
```
##### To find a file in the entire system
```bash
sudo find / -type d -name "sites-enabled"
```

# Nginx Configuration Directories

The `/etc/nginx/conf.d/`, `/etc/nginx/sites-available/`, and `/etc/nginx/sites-enabled/` directories serve different purposes in the Nginx configuration:

## `/etc/nginx/conf.d/`
This directory is used to store additional configuration files that are included in the main Nginx configuration file (`/etc/nginx/nginx.conf`). The main configuration file typically contains a line like `include /etc/nginx/conf.d/*.conf;` or `include /etc/nginx/conf.d/*.vhost;`, which tells Nginx to load and process all configuration files in the `conf.d` directory.

The `conf.d` directory is useful for organizing configuration snippets or separate configurations for different applications, virtual hosts, or modules. This way, you can keep the main configuration file clean and modular.

## `/etc/nginx/sites-available/`
This directory contains configuration files for different virtual hosts or server blocks. Each file in this directory represents a separate website or application configuration.

The files in the `sites-available` directory are not loaded by Nginx by default. Instead, you need to create symbolic links from the `sites-enabled` directory to the desired configuration files in the `sites-available` directory to enable them.

## `/etc/nginx/sites-enabled/`
This directory is where the symbolic links to the enabled virtual host configuration files are stored. Nginx reads and processes all the configuration files linked from the `sites-enabled` directory.

To enable a virtual host configuration, you create a symbolic link from the `sites-enabled` directory to the corresponding configuration file in the `sites-available` directory. To disable a virtual host, you simply remove the symbolic link from the `sites-enabled` directory.

The `sites-available` and `sites-enabled` directories provide a way to manage and enable/disable virtual host configurations without modifying the main Nginx configuration file. This separation of concerns makes it easier to maintain and control which virtual hosts are active or inactive.

**In summary:**
- `/etc/nginx/conf.d/` is for additional configuration snippets or modules.
- `/etc/nginx/sites-available/` contains all available virtual host configurations.
- `/etc/nginx/sites-enabled/` contains symbolic links to the enabled virtual host configurations from `sites-available`.

This structure helps in organizing and managing Nginx configurations, especially in environments with multiple websites or applications running on the same server.

## Some nginx notes

`listen [::]:80;`: The [::] refers to the IPv6 wildcard address. This tells Nginx to listen for requests on port 80 but over IPv6.

The `server_name` directive in Nginx is used to define which domain(s) or subdomain(s) a server block should respond to. 

```
server_name example.com www.example.com;
```

If you want to strictly match a single domain without allowing subdomains, use = before the domain name.

```
 server_name =example.com;
```

You can use an underscore (_) as a wildcard to match any domain name that doesn't match any other server_name directive.
```
server_name _;
```

Redirecting www to Non-www
You can use server_name in combination with a redirect to ensure that requests to www.example.com are redirected to example.com (non-www version).

```
server {
    listen 80;
    server_name www.example.com;

    return 301 http://example.com$request_uri;
}

server {
    listen 80;
    server_name example.com;

    root /var/www/example;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

# `$request_uri`
The $request_uri variable contains the full original request from the client, including the path and query string (everything after the domain). This value is exactly what the client requested, without any modifications or decoding.

Includes: The full URI including the query string.
Unmodified: It is not altered by Nginx during internal redirects or rewrites.
Encoded: URL-encoded characters (like %20 for spaces) remain encoded.

Typical Usage:
$request_uri is often used in rewrite rules, logging, and when you want to capture or redirect the exact request as sent by the client. For example, it is common to use it when redirecting from HTTP to HTTPS: 
```
server {
    listen 80;
    server_name example.com;

    return 301 https://example.com$request_uri;
}
```
# `$uri`
The $uri variable contains the normalized URI that Nginx works with internally. It represents the path part of the request, without the query string. Additionally, $uri is decoded by Nginx, meaning that any encoded characters (like %20) are translated into their real character equivalents (in this case, a space).

Includes: The URI path only (no query string).
Modified: It can be altered by Nginx, especially after internal redirects or rewrite rules.
Decoded: Any URL-encoded characters (e.g., %20) are decoded.
Typical Usage:
$uri is commonly used in Nginx location blocks or rewrite rules to match specific paths and handle requests accordingly. For example:

```
server {
    listen 80;
    server_name example.com;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
| Variable	| Description	| Example Value |
| --------- | ----------- | ------------- |
| $request_uri	| Full URI as requested by the client, including the query string and encoded characters. |	/page?user=1 (unchanged from the original request) |
| $uri	| Normalized URI used internally by Nginx, without the query string and with decoded characters.	| /page (decoded and modified if needed) |

```
http://example.com/page?user=1
```
