Here are several examples of how to use the `location` directive in Nginx for different scenarios:

### 1. **Basic Location Block**
This basic `location` block matches all requests to the root (i.e., `/`) of the server. It can be used to serve static files, like HTML, CSS, or JavaScript.

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

- **Explanation**: All requests to the root URL (e.g., `http://example.com/`) will serve files from the `/var/www/html` directory, with `index.html` being the default file if no specific file is requested.

### 2. **Exact Match**
Use the `=` symbol to specify an **exact match** for a location. This example returns a specific response when `/about` is requested.

```nginx
server {
    listen 80;
    server_name example.com;

    location = /about {
        return 200 "About Us Page";
    }
}
```

- **Explanation**: This will only match the exact path `/about`. If you try accessing `/about/` or `/about?query=1`, it won't match this block.

### 3. **Prefix Match**
This example demonstrates a **prefix match**. Any request starting with `/images/` will match this location.

```nginx
server {
    listen 80;
    server_name example.com;

    location /images/ {
        root /var/www/example;
    }
}
```

- **Explanation**: If a client requests `http://example.com/images/picture.jpg`, Nginx will serve the file located at `/var/www/example/images/picture.jpg`.

### 4. **Location with Regex Matching**
You can use regular expressions in the `location` directive. In this example, a regular expression is used to match URLs ending in `.php`.

```nginx
server {
    listen 80;
    server_name example.com;

    location ~ \.php$ {
        root /var/www/html;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}
```

- **Explanation**: This block matches any URL that ends with `.php` (e.g., `/index.php`). It passes the request to the PHP FastCGI process for handling.

### 5. **Serve Static Content from a Different Directory**
In this example, requests for static content like images and stylesheets are served from a separate directory.

```nginx
server {
    listen 80;
    server_name example.com;

    location /static/ {
        alias /var/www/static_files/;
        expires 30d;
        access_log off;
    }
}
```

- **Explanation**: Requests like `http://example.com/static/logo.png` will be served from `/var/www/static_files/logo.png`. The `alias` directive is used when the request path and the file system path differ.

### 6. **Proxy Pass Example**
This example shows how to proxy requests to another server, such as an API backend.

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

- **Explanation**: All requests to `api.example.com` will be forwarded to the backend service running on `http://127.0.0.1:8080`.

### 7. **Custom 404 Page**
You can use a `location` block to serve a custom 404 error page.

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        root /var/www/html;
        try_files $uri $uri/ =404;
    }

    error_page 404 /custom_404.html;
    location = /custom_404.html {
        root /var/www/errors;
    }
}
```

- **Explanation**: If a requested file is not found, Nginx will serve `/var/www/errors/custom_404.html` as a custom error page.

### 8. **Restrict Access by IP Address**
In this example, access to `/admin` is restricted to specific IP addresses.

```nginx
server {
    listen 80;
    server_name example.com;

    location /admin {
        allow 192.168.1.1;
        allow 192.168.2.0/24;
        deny all;
    }
}
```

- **Explanation**: Only the IP address `192.168.1.1` and the `192.168.2.0/24` subnet are allowed to access `/admin`. All other IP addresses will be denied.

### 9. **Redirect to Another URL**
You can use a `location` block to perform a redirect.

```nginx
server {
    listen 80;
    server_name example.com;

    location /old-page {
        return 301 /new-page;
    }
}
```

- **Explanation**: Requests to `/old-page` will be redirected to `/new-page` with a `301 Moved Permanently` status code.

### 10. **Enforce HTTPS**
This example forces all HTTP requests to be redirected to HTTPS.

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        return 301 https://$host$request_uri;
    }
}
```

- **Explanation**: Any HTTP request will be redirected to the same URL but with `https://`, ensuring that users always access the site securely.

### 11. **Caching Static Files**
You can cache static files for a specified period using the `expires` directive.

```nginx
server {
    listen 80;
    server_name example.com;

    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform";
    }
}
```

- **Explanation**: All image, CSS, and JavaScript files will be cached by the browser for 30 days.

### 12. **Serve Precompressed Files (gzip)**
Nginx can serve precompressed `.gz` files if they exist, to improve loading times for static assets.

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        gzip_static on;
        root /var/www/html;
    }
}
```

- **Explanation**: If there is a file like `style.css.gz`, Nginx will serve the `.gz` version if the client supports it, saving bandwidth.

---

These examples cover a wide range of use cases for the `location` directive in Nginx, from serving static files to proxying requests, handling errors, and implementing security policies.
