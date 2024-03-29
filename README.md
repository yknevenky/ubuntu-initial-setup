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
