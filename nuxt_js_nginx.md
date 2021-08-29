server {
    listen 80;
    listen [::]:80;
    index index.html;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

## SSL CONFIG
server {                                                                                                         
        listen 80;
        listen 443 ssl;
        server_name domainname.com;

        gzip on;
        gzip_types	text/plain application/xml;
        gzip_proxied    no-cache no-store private expired auth;
        gzip_min_length 1000;

        ssl_certificate     /etc/nginx/ssl/cert.crt;
        ssl_certificate_key /etc/nginx/ssl/cert.key;

        ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers HIGH:!aNULL:!MD5;

        # This is a cache for SSL connections
        ssl_session_cache shared:le_nginx_SSL:1m;
        ssl_session_timeout 1440m;

        rewrite ^/(.*)/$ /$1 permanent;


        location / {
                proxy_set_header        Host $host;
                proxy_set_header        X-Real-IP $remote_addr;
                proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header        X-Forwarded-Proto $scheme;
                proxy_redirect          off;
                proxy_buffering         on;
                proxy_cache             STATIC;
                proxy_cache_valid	200 1d;
                proxy_cache_use_stale   error timeout invalid_header updating http_500 http_502 http_503 http_504;

                proxy_pass              http://localhost:3006;
                proxy_read_timeout	1m;
                proxy_connect_timeout   1m;
        }
}
