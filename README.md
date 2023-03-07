# AWS_PUSH_CENTOS
Documentation to push react and node js website on AWS CENTOS

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-centos-7

LINK ===================================================================

sudo vi /etc/yum.repos.d/CentOS-Base.repo
exclude=postgresql*
sudo yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
sudo yum install postgresql11-contrib
yum list postgresql*
sudo yum install postgresql11-server
sudo /usr/pgsql-11/bin/postgresql-11-setup initdb
sudo systemctl start postgresql-11
sudo systemctl enable postgresql-11
sudo -i -u postgres
createuser --interactive
type name of user(centos) press enter
than type y and press enter
psql
ALTER USER centos PASSWORD '12345';
now copy and paste db.sql here in terminal
sudo vi /var/lib/pgsql/11/data/pg_hba.conf  
change to md5
sudo systemctl restart postgresql-11

====================== INSTALL NODE JS =========================
sudo yum remove node npm nodesource-release-el7-1
curl -fsSL https://rpm.nodesource.com/setup_14.x | sudo bash -
sudo yum clean all
sudo yum update
sudo yum install nodejs npm

sudo yum remove epel-release nginx
====================INSTALL NGINX =========================================
sudo yum install epel-release
sudo yum install nginx
sudo systemctl start nginx
sudo systemctl status nginx

cd /etc/nginx/
sudo vi nginx.conf

setsebool -P httpd_enable_homedirs 1
setenforce 0
systemctl restart nginx
systemctl daemon-reload

===================================================================================
user ubuntu;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    client_body_buffer_size 100k;
    client_header_buffer_size 1k;
    client_max_body_size 100k;
    large_client_header_buffers 2 1k;
    client_body_timeout 10;
    client_header_timeout 10;
    keepalive_timeout 5 5;
    send_timeout 10;
    server_tokens off;
    #gzip  on; on;

    include /etc/nginx/conf.d/*.conf;
}
==================================================================================

cd /etc/nginx/conf.d/
sudo touch default.conf
sudo vi default.conf


CHANGE SERVER PORT AND FILE NAME ACCORDING TO REQUIREMENT
=======================================================================
server {
    listen       3000;
    listen [::]:3000 default_server;
    server_name http://3.224.114.43:5005/;

    access_log /home/centos/admin/server_logs/host.access.log main;

    location / {
        root   /home/centos/admin/deploy;
        index  index.html index.htm;
        try_files $uri /index.html;
        add_header X-Frame-Options SAMEORIGIN;
        add_header X-Content-Type-Options nosniff;
        add_header X-XSS-Protection "1; mode=block";
        add_header Strict-Transport-Security "max-age=31536000; includeSubdomains;";
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    server_tokens off;

    location ~ /\.ht {
        deny  all;
    }

}
=======================================================================
