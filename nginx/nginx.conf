# Bloco para redirecionar HTTP para HTTPS e configurar o desafio do Certbot
server {
    listen 80;
    server_name let-me-ask.faraujo.com api-let-me-ask.faraujo.com;

    # Configuração para o desafio do Certbot
    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    # Redireciona HTTP para HTTPS
    location / {
        return 301 https://$host$request_uri;
    }
}

# Bloco para o let-me-ask-webapp - let-me-ask.faraujo.com
server {
    listen 443 ssl;
    server_name let-me-ask.faraujo.com;

    ssl_certificate /etc/letsencrypt/live/let-me-ask.faraujo.com/fullchain.pem; # Certificado para let-me-ask.faraujo.com
    ssl_certificate_key /etc/letsencrypt/live/let-me-ask.faraujo.com/privkey.pem; # Chave para let-me-ask.faraujo.com

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout 5m;
    ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
    ssl_prefer_server_ciphers on;

    # Proxy para a aplicação Frontend (Vite)
    location / {
        proxy_pass http://let-me-ask-webapp:80; # Nome do serviço no docker-compose.yml e porta que o contêiner expõe
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

# Bloco para o Backend (Node.js) - api-let-me-ask.faraujo.com
server {
    listen 443 ssl;
    server_name api-let-me-ask.faraujo.com; # Substitua pelo seu subdomínio do Node.js

    ssl_certificate /etc/letsencrypt/live/api-let-me-ask.faraujo.com/fullchain.pem; # Certificado para api-let-me-ask.faraujo.com
    ssl_certificate_key /etc/letsencrypt/live/api-let-me-ask.faraujo.com/privkey.pem; # Chave para api-let-me-ask.faraujo.com

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout 5m;
    ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
    ssl_prefer_server_ciphers on;

    # Proxy para a aplicação let-me-ask-service (Node.js)
    location / { # A raiz do subdomínio api.dominio.com
        proxy_pass http://let-me-ask-service:3000; # Nome do serviço no docker-compose.yml e porta que o contêiner expõe
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}