# Deploying the Halal Info Site with Nginx

## Prerequisites
- Nginx installed on your server
- SSL certificate from Let's Encrypt (assuming you already have one for haritselfahmi.com)
- Root or sudo access to the server

## Deployment Steps

### 1. Copy the HTML file to your web server
```bash
# Create the directory for the halal site
sudo mkdir -p /var/www/halal

# Copy the index.html file to the server
sudo cp index.html /var/www/halal/

# Set proper permissions
sudo chown -R www-data:www-data /var/www/halal
sudo chmod -R 755 /var/www/halal
```

### 2. Install the nginx configuration
```bash
# Copy the nginx configuration
sudo cp nginx_halal.conf /etc/nginx/sites-available/halal

# Create a symbolic link to enable the site
sudo ln -s /etc/nginx/sites-available/halal /etc/nginx/sites-enabled/

# Test the nginx configuration
sudo nginx -t

# If the test passes, reload nginx
sudo systemctl reload nginx
```

### 3. Update DNS (if using a subdomain)
If you're using `halal.haritselfahmi.com`, add an A record in your DNS settings pointing to your server's IP address.

### 4. SSL Certificate
Since you're using the same domain certificate (haritselfahmi.com), the existing wildcard or multi-domain certificate should work. If you need to add the subdomain:

```bash
sudo certbot --nginx -d halal.haritselfahmi.com
```

## Configuration Details

The nginx configuration:
- Serves static files from `/var/www/halal`
- Redirects HTTP to HTTPS
- Enables gzip compression for better performance
- Sets cache headers for static assets
- Uses your existing SSL certificates from Let's Encrypt

## Customization

If you want to use a different domain name:
1. Edit `nginx_halal.conf` and change `halal.haritselfahmi.com` to your desired domain
2. Update the SSL certificate paths if using a different domain
3. Follow the deployment steps above

## Testing

After deployment, visit:
- http://halal.haritselfahmi.com (should redirect to HTTPS)
- https://halal.haritselfahmi.com (should display the site)

## Troubleshooting

If the site doesn't load:
- Check nginx error logs: `sudo tail -f /var/log/nginx/error.log`
- Verify file permissions: `ls -la /var/www/halal`
- Ensure nginx is running: `sudo systemctl status nginx`
- Check if port 443 is open: `sudo netstat -tlnp | grep :443`
