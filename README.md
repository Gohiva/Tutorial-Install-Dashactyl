#TUTORIAL INSTALAR DASHACTYL 

1. Instalar NodeJS 14
curl -sL https://deb.nodesource.com/setup_14.x | sudo bash -
sudo apt -y install nodejs

2. Clonar repositorios Dashactyl
apt install git
git clone https://github.com/Votion-Development/Dashactyl-0.4.git

3. Reverse Proxy
nano /etc/nginx/sites-available/dashactyl.conf
(Pegas lo siguiente)
server {
  listen 80;
  server_name dash-cloud.gohiva.es;
  return 301 https://%24server_name%24request_uri/;
}
server {
  listen 443 ssl http2;

  server_name dash-cloud.gohiva.es;
  ssl_certificate /etc/letsencrypt/live/dash-cloud.gohiva.es/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/dash-cloud.gohiva.es/privkey.pem;
  ssl_session_cache shared:SSL:10m;
  ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers  HIGH:!aNULL:!MD5;
  ssl_prefer_server_ciphers on;

  location / {
    proxy_pass http://localhost:9999/;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
    proxy_set_header Host $host;
    proxy_buffering off;
    proxy_set_header X-Real-IP $remote_addr;
  }
}
(Haces Cntr+O, Enter y después Cntrl+X)

sudo ln -s /etc/nginx/sites-available/dashactyl.conf /etc/nginx/sites-enabled/dashactyl.conf

(Cambias el puerto de Dashactyl en el settings.json)

4. Obtener certificado
sudo service nginx stop
sudo certbot certonly --standalone -d dash-cloud.gohiva.es
sudo service nginx start

5. Iniciar el Dash
sudo npm install pm2 -g
pm2 start Dashactyl-0.4/index.js
