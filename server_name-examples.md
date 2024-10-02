The `server_name` directive in Nginx is used to define which domain(s) or subdomain(s) a server block should respond to. You can configure Nginx to handle specific domain names, wildcard domains, or even catch-all configurations. Here are several examples to help illustrate how `server_name` works in different scenarios.

### 1. **Single Domain**
This is the most common use case, where Nginx responds to a specific domain name.

#### Example:
```nginx
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
- **`server_name example.com;`**: Nginx will handle requests for `example.com`.
- Requests for `http://example.com` will serve files from `/var/www/example`.

### 2. **Multiple Domain Names (Aliases)**
You can set up Nginx to handle multiple domain names for a single server block.

#### Example:
```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/example;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- **`server_name example.com www.example.com;`**: Nginx will handle requests for both `example.com` and `www.example.com`.
- This is useful when you want the same content served for both the base domain and its `www` version.

### 3. **Wildcard Subdomains**
You can use a wildcard `*` to match all subdomains of a domain.

#### Example:
```nginx
server {
    listen 80;
    server_name *.example.com;

    root /var/www/example;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- **`server_name *.example.com;`**: Nginx will handle any subdomain of `example.com` (e.g., `app.example.com`, `blog.example.com`, `dev.example.com`).
- Requests for `subdomain.example.com` will be served from the same root directory.

### 4. **Exact Domain Name (Prevent Subdomain Matching)**
If you want to strictly match a single domain without allowing subdomains, use `=` before the domain name.

#### Example:
```nginx
server {
    listen 80;
    server_name =example.com;

    root /var/www/example;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- **`server_name =example.com;`**: Nginx will handle requests **only** for `example.com` and **not** for any subdomains like `www.example.com` or `app.example.com`.

### 5. **Catching All Domains (Catch-All)**
You can use an underscore (`_`) as a wildcard to match any domain name that doesn't match any other `server_name` directive.

#### Example:
```nginx
server {
    listen 80 default_server;
    server_name _;

    root /var/www/default;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- **`server_name _;`**: This acts as a **catch-all** for any requests that don’t match any other `server_name` configurations. It will catch requests for unknown or mismatched domain names.
- This is useful as a fallback when you want to serve a default page or handle requests to any undefined domains.

### 6. **Multiple Server Blocks for Different Domains**
You can have different server blocks for handling various domains with different configurations.

#### Example:
```nginx
server {
    listen 80;
    server_name example.com;

    root /var/www/example;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

server {
    listen 80;
    server_name api.example.com;

    root /var/www/api;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- **`server_name example.com;`**: This block handles requests for `example.com`.
- **`server_name api.example.com;`**: This block handles requests for `api.example.com`.
- Each domain can serve different content from different root directories (`/var/www/example` and `/var/www/api`).

### 7. **Using Regex for Advanced Domain Matching**
You can use regular expressions (regex) to match more complex domain patterns. Note that you need to prefix the `server_name` with `~` for regex.

#### Example:
```nginx
server {
    listen 80;
    server_name ~^www\.(.+)\.com$;

    root /var/www/regex;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- **`server_name ~^www\.(.+)\.com$;`**: Nginx will match any domain that starts with `www.` and ends with `.com`, such as `www.example.com` or `www.test.com`.
- This can be useful for complex domain matching scenarios.

### 8. **Redirecting `www` to Non-`www`**
You can use `server_name` in combination with a redirect to ensure that requests to `www.example.com` are redirected to `example.com` (non-`www` version).

#### Example:
```nginx
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
- The first server block redirects `www.example.com` to `example.com`.
- The second server block serves the actual content for `example.com`.

### 9. **Redirecting HTTP to HTTPS**
If you want to redirect all HTTP traffic to HTTPS for a specific domain, you can use the following configuration:

#### Example:
```nginx
server {
    listen 80;
    server_name example.com;

    return 301 https://example.com$request_uri;
}
```
- This block ensures that all HTTP requests are redirected to the HTTPS version of `example.com`.

### Summary:
- **`server_name`** defines which domains a particular server block will handle.
- You can specify one or more domains, use wildcards, regular expressions, or set up catch-all blocks.
- This allows you to configure different behaviors for different domains or subdomains on the same Nginx server.

These examples provide flexibility to manage multiple domains and requests efficiently within your Nginx configuration.
