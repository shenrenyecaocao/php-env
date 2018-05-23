一.安装php环境
    1.Ubuntu 16.04默认安装php7.0环境，但是php7目前兼容性并不是很好，如果自行安装php5需要清除php7的已安装包
        sudo dpkg -l | grep php| awk '{print $2}' |tr "\n" " "
        sudo apt-get install aptitude
        sudo aptitude purge `dpkg -l | grep php| awk '{print $2}' |tr "\n" " "`
    2.添加php源
        sudo add-apt-repository ppa:ondrej/php
    3.安装php5.6
        sudo apt-get update
        sudo apt-get install php5.6
        sudo apt-get install php5.6-gd
        sudo apt-get install php5.6-mysql
        sudo apt-get install php5.6-fpm php5.6-mysql php5.6-common php5.6-curl php5.6-cli php5.6-mcrypt php5.6-mbstring php5.6-dom
二.安装mysql
    sudo apt install mysql-server php5.6-mysql
    sudo apt-get install mysql-client
    mysql_secure_installation
    mysqladmin -p -u root version      测试安装
三.安装Nginx
    1、首先添加nginx_signing.key(必须，否则出错)
        wget http://nginx.org/keys/nginx_signing.key
        sudo apt-key add nginx_signing.key
    2、添加]Nginx](http://nginx.org/)官方提供的源
        echo "deb http://nginx.org/packages/ubuntu/ trusty nginx" >> /etc/apt/sources.list
        echo "deb-src http://nginx.org/packages/ubuntu/ trusty nginx" >> /etc/apt/sources.list
    3、更新源并安装Nginx
        sudo apt-get update
        sudo apt-get install nginx
        nginx -v
四.配置php
    vim /etc/php/5.6/fpm/php.ini
    cgi.fix_pathinfo = 0;
    display_errors  =  On

    sudo service nginx restart   重启
    /etc/init.d/nginx    重启
    /etc/init.d/php-fpm5.6 restart 重启fpm
    nginx -t   检测nginx配置
    nginx -c /etc/nginx/**.conf   指定配置文件启动nginx
    nginx -s reload 重新加载配置问价
    在/etc/nginx/sites-available下创建配置文件, ln -s 创建软连接到sites-enabled下
    server {
        listen       80;
        server_name  your_site_name.com;
        root /usr/share/nginx/html;
        index index.php index.html;

        location / {
                try_files $uri $uri/ =404;
        }

        error_page 404 /404.html;
        error_page 500 502 503 504 /50x.html;

        location = /50x.html {
                root /var/www/html;
        }

        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
}