# Linux: Installation Let Enscrypt's on Debian 9 with Nginx

Tags: debian, linux, nginx

Sau khi setup nginx va config a virtualhost, vidu [domain.com.conf](http://domain.com.conf/)

Cai dat certbot

```
apt-get install certbot python-certbot-nginx
```

Cai ssl cho domain

```
domain.com www.domain.com
```

```
nano /etc/nginx/sites.d/domain.conf

#Add below lines after server_name
.com/fullchain.pem;
.com/privkey.pem;
include /etc/nginx/conf.d/ssl.conf
```

\#Content of in /etc/nginx/conf.d/ssl.conf

```
ssl_session_cache           shared:SSL:20m; #1m luu dc 4000 session
ssl_session_timeout         1440m; #expired in 10 minutes
ssl_protocols               TLSv1 TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers   on;
ssl_ciphers                 "ECDHE-ECDSA-AES128-GCM-SHA256 ECDHE-ECDSA-AES256-GCM-SHA384 ECDHE-ECDSA-AES128-SHA ECDHE-ECDSA-AES256-SHA ECDHE-ECDSA-AES128-SHA256 ECDHE-ECDSA-AES256-SHA384 ECDHE-RSA-AES128-GCM-SHA256 ECDHE-R
SA-AES256-GCM-SHA384 ECDHE-RSA-AES128-SHA ECDHE-RSA-AES128-SHA256 ECDHE-RSA-AES256-SHA384 DHE-RSA-AES128-GCM-SHA256 DHE-RSA-AES256-GCM-SHA384 DHE-RSA-AES128-SHA DHE-RSA-AES256-SHA DHE-RSA-AES128-SHA256 DHE-RSA-AES256-SHA25
6 EDH-RSA-DES-CBC3-SHA";
ssl_dhparam                 /etc/nginx/cert/dhparam.pem;
ssl_stapling                on;
ssl_stapling_verify         on;
resolver                    8.8.4.4 8.8.8.8;
add_header Strict-Transport-Security "max-age=31557600; includeSubDomains";
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-Xss-Protection "1";
.google-analytics.com.googleapis.com.gstatic.com
add_header Referrer-Policy no-referrer-when-downgrade;
```

Default /etc/nginx/sites.d/[example.com.conf](http://example.com.conf/)

```
server
{
listen          80;
example.com.com www.example.com
access_log      off;
log_not_found   off;
https://example
}
server
{
listen          443 ssl http2;
www.example
access_log      off;
log_not_found   off;
example.com/fullchain.pem;
example.com/privkey.pem;
example.com/chain.pem;
include /etc/nginx/conf.d/ssl.conf;
https://example
}
server
{
listen 443 ssl http2;
.com
example.com/fullchain.pem;
example.com/privkey.pem;
example.com/chain.pem;
include /etc/nginx/conf.d/ssl.conf;
xemotomini.com/public;
index index.html index.php;
charset utf-8;

location / {
try_files $uri $uri/ /index.php$is_args$args;
}
location ~ \.php$ {
try_files $uri =404;
fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
include /etc/nginx/fastcgi_params;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
include /etc/nginx/conf.d/static.conf;
include /etc/nginx/conf.d/deny.conf;
}
```

Auto renew certbot

Test renew with cmd:

```
certbot renew --dry-run
```

```
crontab -e
#setup for cron auto run on 2h AM in daily
0 2 * * * /usr/bin/certbot renew --post-hook "systemctl reload nginx"
```
