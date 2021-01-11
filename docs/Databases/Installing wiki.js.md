Resources
https://www.howtoforge.com/how-to-install-wikijs-on-ubuntu-1804-lts/#stepnbsp-install-and-configure-nginx

## Download the source
wget https://github.com/Requarks/wiki/releases/download/2.4.107/wiki-js.tar.gz


Create a document root directory where Wiki.js should reside in:
sudo mkdir -p /var/www/wiki.js

Navigate to the document root directory:
cd /var/www/wiki.js

Create a user for wikijs
sudo useradd -d /var/www/wiki.js wikijs

Change ownership of the /var/www/wiki.js directory to wikijs user:
sudo chown -R wikijs:wikijs /var/www/wiki.js




# create a systemd service:
open a new file at /lib/systemd/system/wiki-js.service and add the following:

[Unit]
Description=Wiki.js
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/node server
Restart=always
# Consider creating a dedicated user for Wiki.js here:
User=wikijs
Environment=NODE_ENV=production
WorkingDirectory=/var/www/wiki.js

[Install]
WantedBy=multi-user.target


Reload systemd:
systemctl daemon-reload
Run the service:
systemctl start wiki-js.service
Enable the service on system boot.
systemctl enable wiki-js.service
Note: You can see the logs of the service using journalctl -u wiki-js


# Configuring nginx
sudo vi /etc/nginx/sites-available/wiki-js

server {
    listen 80;
    listen [::]:80;
    server_name  _;

    location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_next_upstream error timeout http_502 http_503 http_504;
    }
}


sudo ln -sf /etc/nginx/sites-available/wiki-js /etc/nginx/sites-enabled/wiki-js
rm /etc/nginx/sites-enabled/default



Admin:
username d.a.f.ayres@googlemail.com	
pass: test1234

