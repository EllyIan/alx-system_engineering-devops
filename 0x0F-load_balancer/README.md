# Load Balancer Setup

This project sets up a load-balanced environment using HAProxy, Nginx, and custom HTTP headers to track server responses. It includes scripts to configure web servers (`web-01` and `web-02`) and the load balancer (`lb-01`).

## Requirements

- Ubuntu 20.04 or later
- SSH access to `web-01`, `web-02`, and `lb-01`

## Project Structure

- `0-custom_http_response_header`: Script to configure Nginx on `web-01` and `web-02`
- `1-install_load_balancer`: Script to install and configure HAProxy on `lb-01`

## Configuration Steps

### Step 1: Configure Nginx on Web Servers

1. **Update and install Nginx**:
   - Run the `0-custom_http_response_header` script on both `web-01` and `web-02`.

```bash
#!/bin/bash

# Update package lists and install Nginx
sudo apt-get update
sudo apt-get install -y nginx

# Add the custom header to the Nginx configuration
sudo bash -c 'cat > /etc/nginx/conf.d/custom_header.conf << EOF
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
    
    server_name _;
    
    location / {
        try_files \$uri \$uri/ =404;
        add_header X-Served-By \$hostname;
    }
}
EOF'

# Restart Nginx to apply the configuration changes
sudo systemctl restart nginx

