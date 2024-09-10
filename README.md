# Install HTTPS Web Proxy Using Nginx

## I. Install Nginx (No need to install if you've already had it)

### 1. Update Package Index
#### >>> Before installing any new packages, it's a good practice to update the package index:
```
apt-get update
```

### 2. Install Nginx
#### >>> To install Nginx on Ubuntu, run the following command:
```
apt-get install nginx
```

### 3. Start Nginx Service
#### >>> After the installation is complete, start the Nginx service with the following command:
```
systemctl start nginx
```

### 4. Enable Nginx to Start on Boot
#### >>> To ensure that Nginx starts automatically when the system boots up, run:
```
systemctl enable nginx
```

### 5. Enable Nginx to Start on Boot
#### >>> You can check the status of Nginx to ensure it is running properly:
```
systemctl status nginx
```

## II. Configure HTTPS Proxy with Internal Service

### 1. Add Configuration File for Internal Service
#### >>> Create a `cr-web-backend.conf` on path `/etc/nginx/conf.d` with the following content:
```
server {
    listen 7002 ssl;
    server_name crossroadscambodia.church;

    ssl_certificate /opt/https-httpd/fullchain.pem;
    ssl_certificate_key /opt/https-httpd/privkey.pem;

    location / {
        proxy_pass http://192.168.10.111:7001;  # URL of cr-web-backend service
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
#### >>> Replace `7002` with your desired https port.
#### >>> Replace `crossroadscambodia.church` with your actual domain name.
#### >>> Replace `http://192.168.10.111:7001` with your actual service url.
#### >>> Update `/opt/https-httpd/fullchain.pem` and `/opt/https-httpd/privkey.pem` with the paths to your SSL certificate and key files.
#### >>> If you don't have SSL certificates, you may generate using this guideline: `https://github.com/rongroeung/apache-httpd-https?tab=readme-ov-file#i-generate-ssltls-certificate`

### 2. Test Nginx Configuration
#### >>> Before restarting Nginx, it's a good practice to test the configuration for syntax errors:
```
nginx -t
```
#### >>> If the test is successful, you should see: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok.

### 3. Restart Nginx
#### >>> After verifying the configuration, restart Nginx for the changes to take effect:
```
systemctl restart nginx
```

### 4. Access Your Internal Service via HTTPS Web Proxy (Nginx)
#### >>> You should now be able to access your internal service through Nginx using HTTPS. Visit `https://crossroadscambodia.church:7002` in a web browser, and Nginx will proxy the requests to your internal service running at `http://192.168.10.111:7001`.
#### >>> Ensure your internal service is running and accessible at `http://192.168.10.111:7001`, and your SSL certificates are correctly configured in Nginx.
#### >>> By following these steps, you can configure Nginx to proxy HTTPS requests to your internal service running on `https://crossroadscambodia.church:7002`.
