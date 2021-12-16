# TÀI LIỆU HƯỚNG DẪN CÀI ĐẶT VÀ CẤU HÌNH WEB SERVER BẰNG NGINX ROCKY LINUX TRÊN MÁY ẢO
***
## 1.	Cài đặt nginx
* Tắt SELinux phòng phát sinh lỗi
```php
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/sysconfig/selinux
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config
reboot
```
* Cài đặt nginx phiên bản mới nhất
```php
yum-config-manager --enable nginx-mainline

dnf install nginx
```
* Khởi động nginx
```php
systemctl start nginx

systemctl enable nginx
```
* Cấu hình nginx
```php
vi /etc/nginx/nginx.conf

 Server{
     ...
     server_name www.srv.world;
     ...
 }
 ```
* Kiểm tra version hiện tại
```php
nginx -v
```
* Tạo và di chuyên đến thư mục sau bằng câu lệnh
```php
mkdir /usr/local/src/nginx && cd /usr/local/src/nginx
```
* Tải xuống mã nguồn nginx và giải nén
```php
wget http://nginx.org/download/nginx-1.21.4.tar.gz

tar -xvzf nginx-1.21.4.tar.gz
```

* Thiết lập tường lửa:
```php
firewall-cmd --add-port=80/tcp

firewall-cmd --add-service=http

firewall-cmd --runtime-to-permanent
```
***
## 2. Cài đặt phpmyadmin
* Cài đặt php 7.4
```php
yum module -y enable php:7.4
yum module -y install php:7.4/common
Restart nginx và php-fpm
systemctl restart nginx
systemctl status php-fpm
```
* Cài đặt mariadb với câu lệnh sau
yum module -y install mariadb:10.3
```php
#Tạo 1 file mới và chỉnh sửa
vi /etc/my.cnf.d/charset.cnf

[mysqld] 
character-set-server = utf8mb4 

[client] 
default-character-set = utf8mb4

#Khởi động mariadb bằng câu lệnh

systemctl enable --now mariadb

```
* Tiến hành cài đặt ban đầu cho mariadb 
mysql_secure_installation
```php
# set root password
Set root password? [Y/n] y
New password:
Re-enter new password:

# remove anonymous users
Remove anonymous users? [Y/n] y

# disallow root login remotely
Disallow root login remotely? [Y/n] y

# remove test database
Remove test database and access to it? [Y/n] y

# reload privilege tables
Reload privilege tables now? [Y/n] y
```
* Cài đặt các gói cần thiết cho phpmyadmin
```php
# Cài đặt wget và unzip
    yum -y install wget
    yum -y install unzip
# Cài đặt các gói php
 	yum -y install php php-fpm php-gd php-mysqlnd php-cli php-curl php-zip php-common php-bcmath php-imap php-xmlrpc php-json php-mbstring php-xml php-opcache
# Khởi động lại nginx và php-fpm
    systemctl restart nginx

    systemctl start php-fpm

    systemctl enable php-fpm

```
* Cài đặt phpmyadmin
```php
cd /usr/share/nginx/html

wget https://files.phpmyadmin.net/phpMyAdmin/5.1.1/phpMyAdmin-5.1.1-all-languages.zip

unzip phpMyAdmin-5.1.1-all-languages.zip

mv phpMyAdmin-5.1.1-all-languages phpmyadmin

cd /usr/share/nginx/html/phpmyadmin

cp config.sample.inc.php config.inc.php

chown nginx:nginx config.inc.php

systemctl restart nginx

systemctl restart php-fpm

```
***
## 3. Cài đặt nukeviet
* Cài đặt nukeviet
```php
cd /usr/share/nginx/html

wget https://github.com/nukeviet/nukeviet/releases/download/4.5.01/nukeviet4.5.01setup.zip

unzip nukeviet4.5.01setup.zip

chown -R nginx:nginx /usr/share/nginx/html

chmod -R 777 /usr/share/nginx/html

systemctl restart nginx

systemctl restart php-fpm

```
***
## 4. Cài đặt modsecurity
* Chuẩn bị cài đặt các gói cần thiết và gỡ cài đặt nginx
```php
#Update lại rocky linux bằng câu lệnh
dnf upgrade --refresh -y
#Cài đặt gói Epel và remi

dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y

dnf install https://rpms.remirepo.net/enterprise/remi-release-8.rpm

#Tạo đoạn mã chỉ định kho lưu trữ nginx

vi /etc/yum.repos.d/nginx.repo

[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```

* Cài đặt libmodsecurity3 cho ModSecurity
```php
#Cài đặt git
dnf install git -y

#Clone lại mã nguồn
git clone --depth 1 -b v3/master --single-branch https://github.com/SpiderLabs/ModSecurity /usr/local/src/ModSecurity/

cd /usr/local/src/ModSecurity/

#Cài đặt các phụ thuộc và module con

dnf install gcc-c++ flex bison yajl curl-devel zlib-devel pcre-devel autoconf automake git curl make libxml2-devel pkgconfig libtool httpd-devel redhat-rpm-config wget openssl openssl-devel nano

dnf --enablerepo=powertools install doxygen yajl-devel

dnf --enablerepo=remi install GeoIP-devel

git submodule init

git submodule update

#Xây dựng môi trường chạy cấu hình và cài đặt
./build.sh

./configure

make

make install
```
* Cài đặt ModSecurity-nginx Connector
```php

git clone --depth 1 https://github.com/SpiderLabs/ModSecurity-nginx.git /usr/local/src/ModSecurity-nginx/

cd /usr/local/src/nginx/nginx-1.21.4

./configure --with-compat --add-dynamic-module=/usr/local/src/ModSecurity-nginx

make modules

cp objs/ngx_http_modsecurity_module.so /usr/lib64/nginx/modules/
```
* Tải và cấu hình ModSecurity-nginx Connector với nginx
```php
vi /etc/nginx/nginx.conf

#Thêm vào đầu file
load_module modules/ngx_http_modsecurity_module.so;

#Thêm vào thẻ http{} đoạn code
modsecurity on;
modsecurity_rules_file /etc/nginx/modsec/modsec-config.conf;

#Tạo thư mục mới

mkdir -p /etc/nginx/modsec/

#Di chuyên file cấu hình của modsecurity đến thư mục sau

cp /usr/local/src/ModSecurity/modsecurity.conf-recommended /etc/nginx/modsec/modsecurity.conf

#Tiến hành chỉnh sửa file sau

/etc/nginx/modsec/modsecurity.conf

#Tại dòng 7
SecRuleEngine On

#Tại dòng 224
SecAuditLogParts ABCEFHJKZ

#Tạo tệp sau và thêm đoạn code vào

/etc/nginx/modsec/modsec-config.conf

#Thêm vào đoạn code
Include /etc/nginx/modsec/modsecurity.conf

#Cuối cùng là sao chép file sau
cp /usr/local/src/ModSecurity/unicode.mapping /etc/nginx/modsec/

#Kiểm tra dịch vụ bằng câu lệnh
nginx -t

#Sau đó restart nginx
systemctl restart nginx

```

* Cài đặt OWASP CRS 3.3
```php
#Tải file về bằng câu lệnh
wget https://github.com/coreruleset/coreruleset/archive/refs/heads/v3.3/master.zip

#Cài đăt giải nén file tải về vào modsec
unzip master.zip -d /etc/nginx/modsec

#Tạo 1 bản sao lưu
cp /etc/nginx/modsec/coreruleset-3.3-master/crs-setup.conf.example /etc/nginx/modsec/coreruleset-3.3-master/crs-setup.conf

#Mở file sau lên và thêm vào nhưng dòng code sau để bật các quy tắc
Vi /etc/nginx/modsec/modsec-config.conf

#Thêm vào

Include /etc/nginx/modsec/coreruleset-3.3-master/crs-setup.conf
Include /etc/nginx/modsec/coreruleset-3.3-master/rules/*.conf*


```
* Kiểm tra và khởi động lại nginx
```php
nginx -t
systemctl restart nginx

```




* Sửa lại nginx.conf
```php
vi /etc/nginx/nginx.conf

#Thêm vào thẻ http

server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  www.srv.world;
        root         /usr/share/nginx/html;

        
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```
* Tùy chỉnh modscurity
```php
#Truy cập thư mục sau để tùy chỉnh các modsecurity
vi /etc/nginx/modsec/coreruleset-3.3-master/crs-setup.conf/
```
***
## 5. Hướng dẫn thiết lập Test một số các modsecurity
* Kiểm tra chặn xss bằng cách thêm dòng script sau vào sau tên miền 
```php
/index.html?exec=/bin/bash

#Hoặc

/?q=<Script>

#ví dụ:

http://192.168.1.8//index.html?exec=/bin/bash

```
* Mở bảo vệ Dos và kiểm tra
```php
#Sửa file sau
vi /etc/nginx/modsec/coreruleset-3.3-master/crs-setup.conf

#Tìm đến dòng khoảng 705 phần Dos proction

#Bỏ hết comment và chỉnh sửa như sau để có thể nhanh chóng kiểm tra
#tx.dos_burst_time_slice=30 
#tx.dos_counter_threshold=10
#setvar:'tx.dos_block_timeout=60
#khi reqquest 10 lần trong 30 giấy sẽ tính là 1 lần quá tải, khi quá tải 2 lần sẽ ban trong 60 giây(request 20 lần trong 30 giấy)

"id:900500,\
 phase:1,\
 nolog,\
 pass,\
 t:none,\
 setvar:'tx.dos_burst_time_slice=60',\  
 setvar:'tx.dos_counter_threshold=20',\ 
 setvar:'tx.dos_block_timeout=60'"


#Test Dos protection bằng cách request liên tục vào trang web đến khi nào đủ giới hạn như ví dụ trên
```

* Kiểm tra SQL Injection
```php
#Truy cập vào trang quản trị và nhập vào ô user và password câu lệnh sql injection cơ bản như sau


admin' or '1'='1

#Hoặc

admin' --


```
***
## 6. Những vấn đề còn tồn tại

* Sau khi chặn người dùng Dos đến máy chủ như đã thiết lập thì chưa thể bỏ chặn người dùng được, người dùng sẽ bị chặn vĩnh viễn cho đến khí restart lại nginx.

* Chưa thiết lập được rewrite cho nukeviet

