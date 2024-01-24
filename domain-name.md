
# #------------ Domain name Configuration ------------------ #/

# 1 Connect domain to serveur ip

/------ Example LWS VPS Debian server -------/

/--------- 1.1 Login to Lws

Go to the LWS client area or control panel and log in.

/--------- 1.2 Access Domain Managment section

/--------- 1.3 Go to Zone DNS section

Add or modify DNS records for your domain "domain.name.com."
Create an "A" record pointing to the IP address of your server. If you want to point a subdomain (e.g., "www.domain.name.com"), you can create a "CNAME" record pointing to the main domain.

/--------- 1.4 Saves changes

nb : can take time to be effective

/------ Example end -------/

# 2 create conf file in /etc/nginx/sites-avaliables

touch project.conf

<------------- edit file and add ----------->

server {
    server_name domain.name.com <www.domain.name.com>;

    charset utf-8;
    location / {
        proxy_set_header    Host $host;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto $scheme;
        # add_header Access-Control-Allow-Origin *;
        proxy_pass <http://0.0.0.0:8080>;                 (important nb : replace by project port in server)
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/domain.name.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/domain.name.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = domain.name.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    if ($host = www.domain.name.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

  listen 80;
  server_name domain.name.com;
    return 404; # managed by Certbot
}

<> --------------- end file edit ----------------->

# 3 save changes

sudo nginx -t
sudo systemctl reload nginx

# 4 allow https through the firewall

sudo ufw status
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'

# 5 Configure certificates (SSL)

/------ Example in lunix server -------/

/--------- install

sudo apt-get update
sudo apt-get install certbot python3-certbot-nginx

/--------- run

sudo certbot --nginx

/--------- generate

sudo certbot --nginx -d domain.name.com -d <www.domain.name.com>

<----------- output Succes Message start ----------->

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled
<https://domain.name.com> and
<https://www.domain.name.com>
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:

- Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/domain.name.com/fullchain.pem              (important)
   Your key file has been saved at:
   /etc/letsencrypt/live/domain.name.com/privkey.pem                (important)
   Your certificate will expire on 2024-04-22. To obtain a new or
   tweaked version of this certificate in the future, simply run
   certbot again with the "certonly" option. To non-interactively
   renew all of your certificates, run "certbot renew"
- If you like Certbot, please consider supporting our work by:

<----------------- Succes Message end -------------->

# 6 verify certbot auto renewal

sudo systemctl status certbot.time

<----------- test ------------->

sudo certbot renew --dry-run

/------ Example end -------/

# 7 Test

got to <www.domain.name.com> or domain.name.com

# 8 (If dont run ) restart nginx server ( nb : if problem , verify if apache server is started );

sudo systemctl restart nginx

# 9 (If persist) reboot server (reren docker containers after reboot)

sudo reboot

@byLutwrld
