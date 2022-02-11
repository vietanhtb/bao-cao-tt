# TÀI LIỆU HƯỚNG DẪN CÀI ĐẶT, CẤU HÌNH WEB SERVER BẰNG NGINX ROCKY LINUX TRÊN MÁY ẢO. TÌM HIỂU VỀ FIREWALL CSF
***
# Mục lục
[1.	Cài đặt nginx](https://github.com/vietanhtb/bao-cao-tt#1c%C3%A0i-%C4%91%E1%BA%B7t-nginx)

[2. Cài đặt phpmyadmin](https://github.com/vietanhtb/bao-cao-tt#2-c%C3%A0i-%C4%91%E1%BA%B7t-phpmyadmin)

[3. Cài đặt nukeviet và cấu hình rewrite cho nukeviet](https://github.com/vietanhtb/bao-cao-tt#3-c%C3%A0i-%C4%91%E1%BA%B7t-nukeviet-v%C3%A0-c%E1%BA%A5u-h%C3%ACnh-rewrite-cho-nukeviet)

[4. Cài đặt modsecurity](https://github.com/vietanhtb/bao-cao-tt#4-c%C3%A0i-%C4%91%E1%BA%B7t-modsecurity)

[5. Hướng dẫn thiết lập Test một số các modsecurity](https://github.com/vietanhtb/bao-cao-tt#5-h%C6%B0%E1%BB%9Bng-d%E1%BA%ABn-thi%E1%BA%BFt-l%E1%BA%ADp-test-m%E1%BB%99t-s%E1%BB%91-c%C3%A1c-modsecurity)

[6. Những vấn đề còn tồn tại](https://github.com/vietanhtb/bao-cao-tt#6-nh%E1%BB%AFng-v%E1%BA%A5n-%C4%91%E1%BB%81-c%C3%B2n-t%E1%BB%93n-t%E1%BA%A1i)

[7. Tạo server mới chứa mysql (mariadb) và thiết lập tường lửa](https://github.com/vietanhtb/bao-cao-tt#7-t%E1%BA%A1o-server-m%E1%BB%9Bi-ch%E1%BB%A9a-mysql-mariadb-v%C3%A0-thi%E1%BA%BFt-l%E1%BA%ADp-t%C6%B0%E1%BB%9Dng-l%E1%BB%ADa)

[8. Tìm hiểu về Firewall CSF](https://github.com/vietanhtb/bao-cao-tt#8-t%C3%ACm-hi%E1%BB%83u-v%E1%BB%81-firewall-csf)

[9. Xây dựng mô hình Replication Slave đồng bộ database từ máy master qua máy slave](https://github.com/vietanhtb/bao-cao-tt#9-x%C3%A2y-d%E1%BB%B1ng-m%C3%B4-h%C3%ACnh-replication-slave-%C4%91%E1%BB%93ng-b%E1%BB%99-database-t%E1%BB%AB-m%C3%A1y-master-qua-m%C3%A1y-slave)

[10. Mã hóa dữ liệu khi chuyển từ master qua slave](https://github.com/vietanhtb/bao-cao-tt#10-m%C3%A3-h%C3%B3a-d%E1%BB%AF-li%E1%BB%87u-khi-chuy%E1%BB%83n-t%E1%BB%AB-master-qua-slave)

[11. Xử lý chuyển slave thành master khi có sự cố](https://github.com/vietanhtb/bao-cao-tt#11-x%E1%BB%AD-l%C3%BD-chuy%E1%BB%83n-slave-th%C3%A0nh-master-khi-c%C3%B3-s%E1%BB%B1-c%E1%BB%91)

[12. Bài tập thiết lập cân bằng tải](https://github.com/vietanhtb/bao-cao-tt#12-b%C3%A0i-t%E1%BA%ADp-thi%E1%BA%BFt-l%E1%BA%ADp-c%C3%A2n-b%E1%BA%B1ng-t%E1%BA%A3i)

[13. Bài tập giữ session](https://github.com/vietanhtb/bao-cao-tt#13-b%C3%A0i-t%E1%BA%ADp-gi%E1%BB%AF-session)

[14. Bài tập NFS server](https://github.com/vietanhtb/bao-cao-tt#14-b%C3%A0i-t%E1%BA%ADp-nfs-server)

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
## 3. Cài đặt nukeviet và cấu hình rewrite cho nukeviet
* Cài đặt nukeviet
```php
cd /usr/share/nginx/html

wget https://github.com/nukeviet/nukeviet/releases/download/4.5.01/nukeviet4.5.01setup.zip

unzip nukeviet4.5.01setup.zip

mv ./nukeviet/* /usr/share/nginx/html/

rm -f /usr/share/nginx/html/index.html

rm -rf nukeviet/

chown -R nginx:nginx /usr/share/nginx/html

chmod -R 777 /usr/share/nginx/html

systemctl restart nginx

systemctl restart php-fpm

#Chạy câu lệnh sau đề phòng lỗi session

chown -R nginx:nginx /var/lib/php/session
service php-fpm restart
service nginx restart

#Chỉnh sửa lại user và group

vi /etc/php-fpm.d/www.conf

#Tìm đến 2 dòng

user = apache;

group = apache;

#sửa 

user=nginx; 

group=nginx;

service php-fpm restart
service nginx restart

```
* Cấu hình rewrite
```php
chỉnh sửa file sau

vi  /etc/nginx/conf.d/default.conf

#tìm đén đoạn code sau

    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
#Thêm vào phía trên đoạn code này đoạn code cấu hình sau

	if ($request_filename ~ /robots.txt$){
		rewrite ^(.*)$ /robots.php?action=$http_host break;
	}

        # Thiết lập rewrite chỉ có từ NukeViet 4.3.00
	rewrite ^/install/check\.rewrite$ /install/rewrite.php break;

	rewrite ^/(.*?)sitemap\.xml$ /index.php?nv=SitemapIndex break;
	rewrite "^/(.*?)sitemap\-([a-z]{2})\.xml$" /index.php?language=$2&nv=SitemapIndex break;
	rewrite "^/(.*?)sitemap\-([a-z]{2})\.([a-zA-Z0-9-]+)\.xml$" /index.php?language=$2&nv=$3&op=sitemap break;

	if (!-e $request_filename){
		rewrite (.*)(\/|\.html)$ /index.php;
		rewrite /(.*)tag/(.*)$ /index.php;
	}
	
	rewrite ^/seek\/q\=([^?]+)$ /index.php?nv=seek&q=$1 break;
	rewrite ^/search\/q\=([^?]+)$ /index.php?nv=news&op=search&q=$1 break;
	rewrite ^/([a-zA-Z0-9\-]+)\/search\/q\=([^?]+)$ /index.php?nv=$1&op=search&q=$2 break; 
	rewrite ^/([a-zA-Z0-9-\/]+)\/([a-zA-Z0-9-]+)$ /$1/$2/ break; 
	rewrite ^/([a-zA-Z0-9-]+)$ /$1/ break;		

	location ~ ^/admin/([a-z0-9]+)/(.*)$ {
		deny all;
	}

	location ~ ^/(config|includes|install|vendor)/(.*)$ {
		deny all;
	}

	location ~ ^/data/(cache|ip|ip6|logs)/(.*)$ {
		deny all;
	}

	location ~ ^/(assets|uploads|themes)/(.*).(php|ini|tpl|php3|php4|php5|phtml|shtml|inc|pl|py|jsp|sh|cgi)$ {
		deny all;
	}	
#Lưu lại và thoát
#Tiếp đến thiết lập file sau
vi /usr/share/nginx/html/data/config/config_global.php

#Thêm vào sau hàm if đoạn code sau 
$sys_info['supports_rewrite']='rewrite_mode_apache';

#Lưu lại và khởi động lại các dịch vụ
service php-fpm restart
service nginx restart
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
## 6. Những vấn đề còn tồn tại
***
* Sau khi chặn người dùng Dos đến máy chủ như đã thiết lập thì chưa thể bỏ chặn người dùng được, người dùng sẽ bị chặn vĩnh viễn cho đến khí restart lại nginx.


## 7. Tạo server mới chứa mysql (mariadb) và thiết lập tường lửa
***
* Setup truy cập mysql từ máy khác 
```php
#Cài đặt mariadb trên máy chủ mới như hướng dẫn đã có ở trên
#truy cập vào mysql
mysql -u root -p
#Tạo tài khoản mới để truy cập từ xa
create user 'va123'@'%' identified by 'vanh99tb';

GRANT ALL PRIVILEGES ON *.* TO 'va123'@'192.168.1.4' IDENTIFIED BY 'vanh99tb';

FLUSH PRIVILEGES;
#Hoặc sử dụng tài khoản root để truy cập từ xa

GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.1.4' IDENTIFIED BY 'vanh99tb' WITH GRANT OPTION;

FLUSH PRIVILEGES;



```
* Sau khi cài đặt máy chứa mysql(mariadb) thì thực hiện cấu hình tường lửa như sau
```php
#Tạo 1 zone riêng

firewall-cmd --permanent --new-zone=sqlzone

firewall-cmd --reload

#Kiểm tra lại các zone

firewall-cmd --get-zones

#Thêm những luật sau vào zone: Mở ssh, mysql, chỉ cho phép ip 192.168.1.15(ip của máy muốn truy cập) truy cập và mở cổng 3306(cổng mặc định của mysql)

firewall-cmd --zone=sqlzone --add-service=mysql --permanent  
firewall-cmd --zone=sqlzone --add-service=ssh --permanent   
firewall-cmd --zone=sqlzone --add-source='192.168.1.4' --permanent
firewall-cmd --zone=sqlzone --add-port=3306/tcp --permanent 
firewall-cmd --zone=sqlzone --change-interface=ens33

#Loại bỏ dịch vụ ssh trên zone public
firewall-cmd --zone=public --remove-service=ssh
firewall-cmd --reload

#kiểm tra lại

firewall-cmd --list-all-zones

#Kiểm tra đã giới hạn ssh và mysql như sau
#Sử dụng 1 máy có ip khác với ip đã thiết lập ở trên

ssh username@192.168.1.9

mysql -u va123 -p -h 192.168.1.9

```
* Đẩy database qua máy server mới chứa mysql(mariadb)
```php
#Cài đặt mariadb backup nếu chưa có

dnf -y install mariadb-backup

#Tạo 1 thư mục lưu trữ database backup

mkdir /home/mariadb_backup

#backup lại database(vietanh99tb là mật khẩu root của mariadb đã được thiết lập)

mariabackup --backup --target-dir /home/mariadb_backup -u root -p vietanh99tb

#Sau đó đẩy file qua máy server mysql mới lập(với ip của máy đó)

cd /home/

scp -r mariadb_backup/ root@192.168.1.9:/root/mariadb_backup

#Tiếp theo là thao tác trên máy server mysql(192.168.1.9)
#Import database vừa gửi qua vào database hiện có

mariabackup --prepare --target-dir /root/mariadb_backup

mariabackup --copy-back --target-dir /root/mariadb_backup

chown -R mysql. /var/lib/mysql

systemctl restart mariadb.service

#Hoàn thành việc chuyền dữ liệu

#Sau đó xóa mysql(mariadb) ở máy chủ cũ đi

systemctl stop mariadb
rm -rf /var/lib/mysql/*

#Backup xuất file .sql

mysqldump --all-databases --user=root --password > /root/testbackup.sql

#Đẩy file qua server mysql mới
cd /root/

scp -r testbackup.sql/ root@192.168.1.9:/root/testbackup.sql

#Import file sql vào trong mariadb

cd /root/
mysql -u root -p < /root/testbackup.sql
***
```
## 8. Tìm hiểu về Firewall CSF
***

 * Cách cài đặt Firewall CSF
 ```php
 #Dừng và vô hiệu hóa tường lửa.

 systemctl disable firewalld 
 systemctl stop firewalld

 #Sau khi đã dừng và vô hiệu hoá Firewalld chúng ta tiến hành cài đặt ibtables và tạo tập tin cần thiết bởi iptables

 yum -y install iptables-services
 touch /etc/sysconfig/iptables 
 touch /etc/sysconfig/iptables6

 #Khởi động iptacbles và Kích hoạt iptables mỗi khi khởi động lại VPS/Server.

 systemctl start iptables 
 systemctl start ip6tables
 systemctl enable iptables 
 systemctl enable ip6tables

 #Các bạn tiến hành cài đặt các thư viện cần thiết cho việc hoạt động của CSF

 yum -y install wget perl unzip net-tools perl-libwww-perl perl-LWP-Protocol-https perl-GDGraph

 #Cài đặt CSF Firewall

 cd /tmp
 wget https://download.configserver.com/csf.tgz 
 tar -xzf csf.tgz 
 cd csf 
 sh install.sh

 #Sau khi cài đặt hoàn tất, xóa các file cài đặt

 rm -rf /tmp/csf
 rm -f /tmp/csf.tgz 

#Sau khi cài đặt các bạn chỉnh sửa lại file cấu hình csf.conf thay đổi giá trị TESTING = "1" thành TESTING = "0"

vi /etc/csf/csf.conf

#Restart lại service CSF

csf -r

#Lưu ý:Nếu không thực hiện được lệnh csf -r các bạn cài thêm gói perl 

yum install perl -y
```

* CSF Open Port (Mở Port) và giới hạn kết nối
```php
#Cho phép user truy cập tới (incoming) các TCP port trên server

TCP_IN = "20,21,22,25,53,80,110,143,443,465,587,993,995"

#Cho phép server kết nối ra ngoài Internet các port chỉ định
TCP_OUT = "20,21,22,25,53,80,110,113,443" 

#Cho phép server kết nối ra tất cả các port ở bên ngoài 
TCP_OUT = "1:65535" Internet.

# Chỉ cho phép client kết nối vào các port UDP chỉ định trên server.
UDP_IN = "20,21,53" 

# Không cho phép client kết nối bất kỳ port UDP nào trên server
UDP_IN = "" 

# Cho phép Server kết nối ra ngoài Internet các port chỉ định
UDP_OUT = "20,21,53,113,123" 

# Cho phép server kết nối ra tất cả các port ở bên ngoài Internet
UDP_OUT = "1:65535" 

#Cho phép client bên ngoài Internet ping đến server:
ICMP_IN = 1

#Không cho phép ping đến server:
ICMP_IN = 0

#Sau đó restart lại csf 
csf -r

#Giới hạn số lượng kết nối
port;limit
```
 * Thay đổi port/IP của gói tin
 ```php
 nat tables  
ipt_DNAT iptables module
ipt_SNAT iptables module
ipt_REDIRECT iptables module
```
* Một số lệnh cơ bản trong trong tường lửa CSF
```php
#Stop CSF	
csf -s
#Disable csf
csf -f
#Restart CSF	
csf -r
#Check csf status
csf -l
#Allow an IP	
csf -a ip-address
#Deny IP	
csf -d ip-address
#Bỏ chặn ip
csf -dr ip-address
#Remove and unblock all entries	
csf -df
#Stop firewall	
csf -x
#Enable firewall	
csf -e

#Cho phép tcp kết nối inbound cổng 22 ip 192.168.1.2
tcp|in|d=22|s=192.168.1.2

#Cho phép tcp kết nối outbound cổng 22 ip 192.168.1.2
tcp|out|d=22|s=192.168.1.2

#Chặn ip truy cập
vi /etc/csf/csf.deny
#Chặn tcp kết nối inbound cổng 22 ip 192.168.1.12
tcp|in|d=22|s=192.168.1.12
```

## 9. Xây dựng mô hình Replication Slave đồng bộ database từ máy master qua máy slave
***
* Chỉnh sửa bên máy master
```php
#Chỉnh sửa file sau
vi /etc/my.cnf.d/mariadb-server.cnf

#add vào dưới [mysqld]
  server-id=10
  log-bin=master
  binlog-format=row
  binlog-do-db=baocaott

#restart lại mariadb
systemctl restart mariadb

#Đăng nhập vào mariadb
mysql -u root -p

#Tạo csdl có tên baocaott
create database baocaott;

#Tạo user và gán quyền cho user đó
create user 'repl_user'@'%' identified by 'vietanh99tb'; 
grant replication slave on *.* to repl_user@'%' identified by 'vietanh99tb'; 
stop slave;
flush privileges; 
exit;

#Tiến hành backup CSDL trên master server và chuyển nó đến slave server.
mysqldump --all-databases --user=root --password --master-data > reply.sql

#Chuyển file backup qua bên máy slave
scp reply.sql root@192.168.1.11:/root/reply.sql

#Đăng nhập vào MariaDB với root user và thực hiện unlock table bằng lệnh:
UNLOCK TABLES;

#Sử dụng câu lệnh dưới để kiểm tra trạng thái của master.
show master status;
```
* Chỉnh sửa bên máy slave
```php
#Chỉnh sửa file sau
vi /etc/my.cnf.d/mariadb-server.cnf

#Chỉnh sửa vào dưới [mysqld]

log-bin=master
binlog-do-db=baocaott
# define server ID (Set ID duy nhất không trùng với master và các slave khác nếu có)
server-id=11
# read only yes
read_only=1
# define own hostname
report-host=192.168.1.10

#Import CSDL master
mysql -u root -p < /root/reply.sql

#Restart MariaDB service để tiếp nhận thay đổi.
systemctl restart mariadb

#Sử dụng root user đăng nhập vào MariaDB Server.
mysql -u root -p

#Hướng dẫn Slave tìm file Master Log file và Start Slave.
STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.1.10', MASTER_USER='repl_user', MASTER_PASSWORD='vietanh99tb', MASTER_LOG_FILE='master.000002', MASTER_LOG_POS=943;
START SLAVE;
show slave status\G;
```

* Kiểm tra
```php
# Tạo bảng
create table test_database.test_table (id int, name varchar(50), address varchar(50), primary key (id)); 
Query OK, 0 rows affected (0.108 sec)

# Thêm dữ liệu
insert into test_database.test_table(id, name, address) values("001", "Rocky Linux", "Hiroshima"); 
Query OK, 1 row affected (0.036 sec)

# Kiểm tra bảng
select * from test_database.test_table; 
+----+-------------+-----------+
| id | name        | address   |
+----+-------------+-----------+
|  1 | Rocky Linux | Hiroshima |
+----+-------------+-----------+


#Sau đó qua bên máy slave kiểm tra 
select * from test_database.test_table; 
#Nếu hiện đúng dữ liệu bảng vừa tạo thì đã thành công
```
* Xử lý Promoting a Slave to Master. Giả lập Master bị hỏng, ví dụ: hỏng ổ cứng, rút dây mạng, dừng dịch vụ, ….Chuyển node slave thành node master tạm thời
```php
#Truy cập vào file
vi /etc/my.cnf.d/mariadb-server.cnf
bỏ dòng read-only=1

#Đăng nhập vào Slave Database và xóa cấu hình slave cũ bằng câu lệnh
stop slave;
reset slave all;
exit;
systemctl restart mysql

```
* Khi master đã được sửa chữa xong chúng ta sẽ thiết lập đưa slave về đúng vị trí của nó
```php
#Đầu tiên là backup lại dữ liệu và đẩy lại qua master do trong quá trình node slave làm master có thể là đã có dữ liệu được ghi thêm vào sau đó đẩy qua bên master rồi import lại rồi bắt đầu thiết lập lại vị trí của slave 

vi /etc/my.cnf.d/mariadb-server.cnf
#Thêm lại dòng read-only vào sau [mysqld]
read-only=1

#Đăng nhập lại vào mysql bên máy slave
mysql -u root -p

#Cấu hình lại slave
CHANGE MASTER TO MASTER_HOST='192.168.1.10', MASTER_USER='repl_user', MASTER_PASSWORD='vietanh99tb', MASTER_LOG_FILE='master.000002', MASTER_LOG_POS=943;
START SLAVE;

#Kiểm tra trạng thái
show slave status\G 
```
* Xử lý backup dữ liệu 1 tiếng 1 lần

```php
#Tạo thư mục lưu trữ các bản backup
mkdir /home/mariadb_backup/

#Đầu tiên tạo 1 file ở root 
vi mysql-backup.sh

#Sau đó ghi vào file đoạn code sau

#!/bin/bash

TODAY=`date +"%d:%b:%Y:%H:%M"`

################################################################
################## Update below values ########################

DB_BACKUP_PATH='/home/mariadb_backup/'

#################################################################

mkdir -p ${DB_BACKUP_PATH}/Time-Backup-File-${TODAY}
echo "Backup started for database "

mariabackup --backup --target-dir ${DB_BACKUP_PATH}/Time-Backup-File-${TODAY}/backup-mysql-on-${TODAY} -u root -p vietanh99tb

if [ $? -eq 0 ]; then
echo "Database backup successfully completed"
else
echo "Error found during backup"
exit 1
fi

### End of script ####

#Lưu lại lại rồi thoát ra

#Kiểm tra file có hoạt động không
bash mysql-backup.sh

#Truy cập vào file /home/mariadb_backup/ để kiểm tra
cd /home/mariadb_backup/
ls

#Sử dụng crontab để thiết lập 1 tiếng chạy tools 1 lần
crontab -e

#Backup 1 tiếng 1 lần
0 * * * * bash /root/mysql-backup.sh

#Sau đó lưu lại và thoát ra
#Kiểm tra crontab đang hoạt động

crontab -l

#Muốn xóa toàn bộ setup của crontab thì sử dụng câu lệnh crontab -r

#Kiểm tra thư mục /home/mariadb_backup/ sau 1 tiếng để thấy kết quả

cd /home/mariadb_backup/
```
***
## 10. Mã hóa dữ liệu khi chuyển từ master qua slave

* Đầu tiên tiến hành trên máy master cài đặt ssl
```php
yum -y install mod_sll
```
* Tạo chứng chỉ với OpenSSL
```php
#Tạo 1 thư mục lưu trữ
mkdir -p /etc/mysql/transit/
cd /etc/mysql/transit/

#Tạo key ca
openssl genrsa 2048 > ca-key.pem

openssl req -new -x509 -nodes -days 365000 \
      -key ca-key.pem -out ca.pem

Country Name (2 letter code) [XX]:VN
State or Province Name (full name) []:ThaiBinh
Locality Name (eg, city) [Default City]:ThaiBinh
Organization Name (eg, company) [Default Company Ltd]:Vinades
Organizational Unit Name (eg, section) []:Dau Thau
Common Name (eg, your name or your server's hostname) []:CA
Email Address []:webmaster@vinades.vn

#Tạo key cho server
openssl req -newkey rsa:2048 -days 365000 \
      -nodes -keyout server-key.pem -out server-req.pem

#Tạo chứng chỉ
openssl rsa -in server-key.pem -out server-key.pem

#Nhập thông tin
#Lưu ý Common Name không để trùng nhau nếu không sẽ dẫn đến lỗi
Country Name (2 letter code) [XX]:VN
State or Province Name (full name) []:ThaiBinh
Locality Name (eg, city) [Default City]:ThaiBinh
Organization Name (eg, company) [Default Company Ltd]:Vinades
Organizational Unit Name (eg, section) []:Dau Thau
Common Name (eg, your name or your server's hostname) []:Server
Email Address []:webmaster@vinades.vn

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

#Ký chứng chỉ
openssl x509 -req -in server-req.pem -days 365000 \
      -CA ca.pem -CAkey ca-key.pem -set_serial 01 \
      -out server-cert.pem

#Kiểm tra
openssl verify -CAfile ca.pem server-cert.pem
#Kết quả cài thành công sẽ hiển thị như sau
server-cert.pem: OK

```
* Tạo key và chứng chỉ cho client
```php
#Tạo khóa
openssl genrsa 2048 > client-key.pem

#Tạo chứng chỉ
openssl req -new -key client-key.pem -out client-req.pem

Country Name (2 letter code) [XX]:VN
State or Province Name (full name) []:ThaiBinh
Locality Name (eg, city) [Default City]:ThaiBinh
Organization Name (eg, company) [Default Company Ltd]:Vinades
Organizational Unit Name (eg, section) []:Dau Thau
Common Name (eg, your name or your server's hostname) []:Client
Email Address []:pcvietanh.99@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

#Ký chứng chỉ
openssl x509 -req -in client-req.pem -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out client-cert.pem -days 3650 -sha256

#Kiểm tra chứng chỉ
openssl verify -CAfile ca.pem server-cert.pem client-cert.pem
```
* Bên máy slave
```php
#Tạo thư mục lưu trữ các chứng chỉ
mkdir -p /etc/mysql/transit/
```
* Quay về master rồi chuyển các chứng chỉ qua bên slave
```php
scp -r /etc/mysql/transit/* root@192.168.1.11:/etc/mysql/transit/
```
* Tại cả máy master và máy slave thực hiện cấp quyền cho file vừa rồi như sau
```php
cd /etc/mysql/transit
$ chown -R mysql:mysql *
$ chmod 600 client-key.pem server-key.pem ca-key.pem
```
* Thực hiện chỉnh sửa file hệ thống của mariadb của cả master và slave
```php
#Chỉnh sửa file sau
vi /etc/my.cnf.d/mariadb-server.cnf

#Thêm vào dưới [mysqld]
ssl-ca=/etc/mysql/transit/ca.pem
ssl-cert=/etc/mysql/transit/server-cert.pem
ssl-key=/etc/mysql/transit/server-key.pem

#Khởi động lại mariadb
systemctl restart mariadb
```
* Quay trở lại máy master, đăng nhập vào maridb, yêu cầu người dùng sử dụng ssl 
```php
#Require ssl cho user va123
#Nếu chưa tạo user có thể tạo user riêng như sau:
create user 'va123'@'%' identified by 'vietanh99tb';
GRANT ALL PRIVILEGES ON *.* TO 'va123'@'192.168.1.11' IDENTIFIED BY 'vietanh99tb';
GRANT ALL PRIVILEGES ON *.* TO 'va123'@'192.168.1.11' IDENTIFIED BY 'vietanh99tb' REQUIRE SSL;

FLUSH PRIVILEGES;
#Khởi động lại mariadb
systemctl restart mariadb
```
* Máy slave kiểm tra
```php
mysql -u va123 -p -h 192.168.1.10 --ssl-cert client-cert.pem --ssl-key client-key.pem --ssl-ca ca.pem -e 'status'

#Chú ý 2 dòng trạng thái sau nếu như ssl hiển thị tương tự như vậy là 2 máy đã kết nối với nhau có mã hóa
Current user:           va123@192.168.1.11
SSL:                    Cipher in use is TLS_AES_256_GCM_SHA384

```
* Tiếp tục trên máy slave chính sửa file cấu hình mariadb
```php
vi /etc/my.cnf.d/mariadb-server.cnf
#Thêm vào file đoạn sau hoạc nếu đã có [client] trong file thì chỉ thêm đoạn mã ở dưới [client]
[client]
ssl-ca=/etc/mysql/transit/ca.pem
ssl-cert=/etc/mysql/transit/client-cert.pem
ssl-key=/etc/mysql/transit/client-key.pem

#Sau đó đăng nhập vào mariadb và dừng slave
STOP SLAVE;
#Khởi động lại mariadb
systemctl restart mariadb
```
* Quay lại máy master
```php
GRANT ALL PRIVILEGES ON *.* TO 'repl_user'@'192.168.1.11' IDENTIFIED BY 'vietanh99tb';

ALTER USER repl_user@'192.168.1.11' identified by 'vietanh99tb' REQUIRE SSL;

FLUSH PRIVILEGES;

#Khởi động lại mariadb

systemctl restart mariadb
```
* Quay lại máy slave
```php
#Kiểm tra
mysql -u repl_user -p -h 192.168.1.10 --ssl -e 'status'

#SSL hiển thị tương tự như sau là đã thành công
Current user:           repl_user@192.168.1.11
SSL:                    Cipher in use is TLS_AES_256_GCM_SHA384


#Đăng nhập vào mariadb sau đó chạy đoạn code sau

STOP SLAVE;
CHANGE MASTER TO MASTER_SSL = 1, MASTER_SSL_CA = '/etc/mysql/transit/ca.pem', MASTER_SSL_CERT = '/etc/mysql/transit/client-cert.pem', MASTER_SSL_KEY = '/etc/mysql/transit/client-key.pem';
START SLAVE;

#Kiểm tra slave
SHOW SLAVE STATUS\G

Slave_IO_State: Waiting for master to send event
    Master_Host: 192.168.1.10
    Master_User: repl_user
    Master_Port: 3306
    Connect_Retry: 60
    Master_Log_File: master.000007
    Read_Master_Log_Pos: 339
    Relay_Log_File: mariadb-relay-bin.000002
    Relay_Log_Pos: 552
    Relay_Master_Log_File:master.000007
    Slave_IO_Running: Yes
    Slave_SQL_Running: Yes
    Master_SSL_Allowed: Yes
    Master_SSL_CA_File: /etc/mysql/transit/ca.pem
    Master_SSL_Cert: /etc/mysql/transit/client-cert.pem
    Master_SSL_Key: /etc/mysql/transit/client-key.pem

#Đã thực hiện mã hóa thành công nếu như 3 dòng trạng thái sau hiển thị tương tự
#Slave_IO_Running: Yes
#Slave_SQL_Running: Yes
#Master_SSL_Allowed: Yes

```
* Tiến hành kiểm tra dữ liệu được truyền đi đã mã hóa hay chưa
```php
#Cài đặt gói tcpdump trên máy salve
yum install tcpdump 

#Chạy đoạn code sau trên máy salve để kiểm tra truyền dữ liệu ở cổng 3306
tcpdump -i ens33 -s 0 -l -w - 'src port 3306 or dst port 3306' | strings

#Ở máy Master insert thêm dữ liệu vào bảng test đã tạo ở bài thiết lập mô hình slave rồi sau đó quay ra kiểm tra bên máy salve

#Ta thấy được dữ liệu đã được mã hóa thành công

lN;0c
F&{$
9ho%
H>^]
%wa}
pe0o@53($X
6Xh+
Z2+9
(~:J
yO5i
:,#QUp
l~Z{
;HsLs
U}#+
:2B& c\?
\#ugM
]6=^
puYo


^C34 packets captured
34 packets received by filter
0 packets dropped by kernel
```
***

## 11. Xử lý chuyển slave thành master khi có sự cố
* Giả sử có 1 máy master 192.168.1.10 và 2 máy slave 192.168.1.11 và 192.168.1.12
* Khi máy 192.168.1.10 bị hỏng giải quyết để chuyển 192.168.1.11 thành master và máy còn lại vẫn là slave

* Thực hiện trên máy slave 192.168.1.11
```php
vi /etc/my.cnf.d/mariadb-server.cnf
# Bỏ dòng read-only =1
# Bỏ dòng report-host=192.168.1.10

#Sau đó đăng nhập vào mysql
mysql -u root -p
stop slave;
reset slave all;
exit;
systemctl restart mariadb

#backup lại sql
mariabackup --backup --target-dir /home/mariadb_backup_2 -u root -p vietanh99tb
#Chuyển file qua bên máy slave 192.168.1.12
rsync -avP /home/mariadb_backup_2 root@192.168.1.12:/root/

```

* Qua bên máy slave1 192.168.1.12
```php
#trước khi thiết lập hãy xóa toàn bộ cấu hình slave
mysql -u root -p
stop slave;
reset slave all;
exit;
systemctl restart mariadb

vi /etc/my.cnf.d/mariadb-server.cnf
# Thay đổi report-host=192.168.1.10
report-host=192.168.1.11

#Thực hiện lại các bước thiết lập slave
systemctl stop mariadb
rm -rf /var/lib/mysql/*

#Chạy câu lệnh sau
mariabackup --prepare --target-dir /root/mariadb_backup_2

#Restore lại database
mariabackup --copy-back --target-dir /root/mariadb_backup_2
chown -R mysql. /var/lib/mysql
systemctl start mariadb



#Chạy câu lệnh sau
cat /root/mariadb_backup_2/xtrabackup_binlog_info

master.000002    943

#Truy cập mysql

mysql -u root -p

#Thiết lập slave
stop slave;

change master to 
master_host='192.168.1.11',
master_user='repl_user',
master_password='vietanh99tb',
master_log_file='master.000002',
master_log_pos=943;

#Thiết lập ssl
CHANGE MASTER TO MASTER_SSL = 1, MASTER_SSL_CA = '/etc/mysql/transit/ca.pem', MASTER_SSL_CERT = '/etc/mysql/transit/client-cert.pem', MASTER_SSL_KEY = '/etc/mysql/transit/client-key.pem';

#Mở lại slave
start slave;
#Kiểm tra slave
show slave status/G

```

## 12. Bài tập thiết lập cân bằng tải
* Có 4 máy : 
    * Máy chủ nginx Master đóng vài trò là máy cân bằng tải: 192.168.1.17
    * Máy chủ nginx số 1: 192.168.1.18
    * Máy chủ nginx số 2: 192.168.1.19
    * Máy chủ nginx số 3: 192.168.1.20
* Kịch bản lựa chọn:
    * Ban đầu có 1 máy chủ web số 1: 192.168.1.18 và sau 1 thời gian đi vào hoạt động máy chủ đã không thể chịu nổi lượng truy cập lớn ngày càng tăng của người dùng từ đó.
    * Do nhu cầu truy cập cao và 1 máy chủ không thể tải hết được ta sẽ phát sinh ra phải tạo thêm 1 con máy chủ nữa và 1 con cân bằng tải. Từ đó sẽ thiết lập thêm 2 máy
        * Máy chủ nginx Master đóng vài trò là máy cân bằng tải: 192.168.1.17
        * Máy chủ nginx số 2: 192.168.1.19
    * Và sau 1 thời gian nữa khi lưu lượng truy cập của người dùng lại tăng lên và 2 máy chủ web không đủ để sử dụng nữa thì sẽ  phát sinh thêm máy chủ nginx số 3: 192.168.1.20
    * Master đóng vai trò cân bằng tải:
        * khi mà truy cập vào máy chủ mà bị đánh dấu không tốt đủ số lần thì sẽ không truy cập vào nó nữa

* Đầu tiên tiến hành cài đặt nginx trên tất cả các máy chủ 
```php
#Tắt Selinnux
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/sysconfig/selinux
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config
#Update và upgrade
dnf upgrade --refresh -y
dnf update -y
reboot
#Cài đặt nginx 
dnf -y install nginx

#config nginx
vi /etc/nginx/nginx.conf
#Dòng 41 : đổi hostname(Chỉ cần trên máy master)
server_name www.srv.world;

systemctl enable --now nginx

firewall-cmd --add-service=http

firewall-cmd --runtime-to-permanent

#Ở bên các server còn lại xóa file index.html đi và thay bằng file index html khác với nội dung như sau để dễ nhận biết các server
rm -f /usr/share/nginx/html/index.html

vi /usr/share/nginx/html/index.html

<html>
<body>
<div style="width: 100%; font-size: 40px; font-weight: bold; text-align: center;">
#(đặt stt theo server tương ứng)
Đây là NGINX SERVER 1
</div>
</body>
</html>
```
* Thiết lập cân bằng tải
```php

vi /etc/nginx/nginx.conf
#Comment lại toàn bộ thẻ server hoặc xóa đi
#sau đó thêm đoạn code sau vào trong thẻ http
http {
    ...
    ...
    ...
   upstream backend {
       server server_1;
       server server_2;
   }
   server {
        listen 80;
        
       location / {
           proxy_pass http://backend;
       }
   }



   .....
}

#Có các thuật toán sau
#Least connected nginx sẽ tính lưu lượng truy cập và kết nối thực tế mà server đó đang phải xử lý và sẽ phân bổ request mới đến server đang phải xử lý ít request hơn. Đây là 1 thuật toán khá thực tế trong cân bằng tải và khả đúng với cái tên của nó
#sử dụng bằng cách thêm vào trong thẻ upstream backend

   upstream backend {
       least_conn;
       server 192.168.1.18;
       server 192.168.1.19;
       server 192.168.1.20;
   }

#ip_hash sử dụng hash và nó hash ip address của client sử dụng bộ 3 octet của ipv4 hoặc toàn bộ địa chỉ ipv6 để làm key mã hóa Thuật toán này đảm bảo rằng, khi hoạt động bình thường , request từ 1 client luôn luôn chỉ đến 1 server điều này khá là giúp ích cho quá trình cân bằng tải
#sử dụng bằng cách thêm vào trong thẻ upstream backend

   upstream backend {
       ip_hash;
       server 192.168.1.18;
       server 192.168.1.19;
       server 192.168.1.20;
   }

#Ramdom sẽ ngẫu nhiên các server để lựa chọn server mà request sẽ đi tới nó có thể hỗ trợ cho thuật toán Least connected và 1 số thuật toán có trả phí

upstream backend {
       random two least_conn;
       server 192.168.1.18;
       server 192.168.1.19;
       server 192.168.1.20;
   }

#Như ở đây nginx chọn ra 2 server thỏa mãn thuật toán Least connected


#Health checks
#Sửa đổi thẻ location như sau
location / {
    proxy_next_upstream http_404;
    proxy_pass http://backend;
}

#Sau khi thiết lập xong hay thử tìm 1 page mà chỉ có 1 server có và request lúc đó ta thấy được những server không có page đó(lỗi 404) sẽ bị đánh dấu là không khỏe mạnh và sẽ request thẳng qua các server khác

#Fail timeout
upstream backend {
       random two least_conn;
       server 192.168.1.18 max_fails=3 fail_timeout=30;
       server 192.168.1.19 ;
       server 192.168.1.20 ;
   }
#nếu trong 30s mà server upstream có 3 lần bị đánh dấu là unhealthy thì server đó sẽ bị remove khỏi danh sách server active để phân phối traffic để kiểm tra điều đó ta có thể tắt server 192.168.1.18 đi và sau 3 lần request đến và nó hiện lỗi 502 thì sau nó ta thấy nó sẽ không request đến server đó nữa

#Down server
upstream backend {
       random two least_conn;
       server 192.168.1.18 max_fails=3 fail_timeout=30;
       server 192.168.1.19 down;
       server 192.168.1.20 ;
   }
#Server 192.168.1.19 sẽ bị ngừng request đến. Lý do không xóa hay comment vào để xóa cấu hình thì là do nếu muốn sử dụng thuật toán ip hash thì thiết lập 1 server down thay vì xóa cấu hình sẽ đảm bảo tính toàn vẹn   

#Backup server
upstream backend {
       random two least_conn;
       server 192.168.1.18 max_fails=3 fail_timeout=30;
       server 192.168.1.19 ;
       server 192.168.1.20 backup;
   }
#Server 192.168.1.20 sẽ được thiết lập làm server backup có nghĩa là khi 2 server còn lại đêu không hoạt động thì server này mới được request đến

#Weight Server
#Thiết lập để ưu tiên server nào nhận được nhiều băng thông, request hơn

upstream backend {
       random two least_conn;
       server 192.168.1.18 max_fails=3 fail_timeout=30 weight=4;
       server 192.168.1.19 weight=2;
       server 192.168.1.20 backup;
   }
#Như ở đây thì băng thông dành cho server 1 sẽ gấp đôi server 2. Nếu không thiết lập thì weight mặc định bằng 1

# config sẽ có những dòng code sau đây


http {
    ...
    ...
    ...
   upstream backend {
       least_conn;
       server 192.168.1.18 max_fails=3 fail_timeout=30 weight=4;
       server 192.168.1.19 max_fails=3 fail_timeout=30 weight=2;
       server 192.168.1.20 max_fails=3 fail_timeout=30 weight=2;
   }
   server {
        listen 80;
        
       location / {
           proxy_next_upstream http_404;
           proxy_pass http://backend;
       }
   }



   .....
}
```


## 13. Bài tập giữ session
* Giải pháp sử dụng ở đây là sử dụng Memcached
* Cài đặt Memcached lên trên máy chủ cân bằng tải
```php
#Update lại OS
dnf update -y
reboot

#Cài đặt memcached
dnf install memcached libmemcached -y
#Kiểm tra thông tin
rpm -qi memcached



#Cấu hình memcached như sau
vi /etc/sysconfig/memcached

PORT="11211"
USER="memcached"
MAXCONN="1024"
CACHESIZE="64"
OPTIONS=""

#Cài đặt và sử dụng tường lửa csf
dnf -y install @perl
cd /tmp
wget https://download.configserver.com/csf.tgz
tar -zxvf csf.tgz
cd csf
./install.sh
rm -rf /tmp/csf
rm -f /tmp/csf.tgz
#Tắt tường lửa mặc định và bật tường lửa CSF để sử dụng
systemctl stop firewalld
systemctl disable firewalld
systemctl enable csf
systemctl enable lfd
systemctl start csf

#Kiểm tra CSF đã hoạt động chưa
systemctl status csf

#Test iptable
/etc/csf/csftest.pl

#Kích hoạt
vi /etc/csf/csf.conf

#Tìm đến dòng có TESTING và FASTSTART và sửa giá trị như sau
TESTING = 0
FASTSTART = 1
#Thi thoảng tường lửa vẫn chặn ip trong csf.alow nên chỉnh về 1 để không bị chặn
IGNORE_ALLOW = "1"
#Comment toàn bộ 4 dòng sau lại hoặc là xóa đi và chỉ mở cổng 80 và 43
TCP_IN = "20,21,22,25,53,80,110,143,443,465,587,993,995"
TCP_IN = "20,21,22,25,53,80,110,143,443,465,587,993,995,3306"
UDP_OUT = "20,21,22,25,53,80,110,113,443"
UDP_OUT = "20,21,22,25,53,80,110,113,443,3306"

#Sửa thành
TCP_IN = "80,43"
TCP_IN = "80,43"
UDP_OUT = "80,43"
UDP_OUT = "80,43"

#Mở cổng cho memcache hoạt động
vi /etc/csf/csf.allow
tcp|in|d=11211|s=192.168.1.18
tcp|in|d=11211|s=192.168.1.19

csf -r

#Khởi động memcached
systemctl enable memcached.service
systemctl start memcached.service
#Kiểm tra trạng thái
systemctl status memcached

```
* Tại các máy server ta cài đặt như sau
```php
#Update lại OS
dnf update -y
reboot

#Cài đặt 2 gói epel và remi
dnf install epel-release -y
dnf install https://rpms.remirepo.net/enterprise/remi-release-8.rpm -y

#Cài đặt php
dnf module enable php:remi-7.4 -y
dnf install php-pecl-memcache php-pecl-memcached -y
dnf install nginx php php-cli -y

#Tạo file info.php để kiểm tra
echo '<?php phpinfo(); ?>' > /usr/share/nginx/html/info.php

#Cài đặt telnet
yum install -y telnet

#Kiểm tra kết nối đến với máy chủ chứa memcached
telnet 192.168.1.17 11211

tạo file test session


code test session
vi /usr/share/nginx/html/test.php
<?php
error_reporting(E_ALL | E_STRICT);
ini_set('display_startup_errors', 1);
ini_set('display_errors', 1);

ini_set('session.save_handler', 'memcached');
ini_set('session.save_path', '192.168.1.17:11211');
ini_set('session.gc_maxlifetime', 1800);

if (empty($_SESSION)) session_start();
#session_start();

echo 'Welcome to server 1<br />';

if (isset($_SESSION['count_hit'])) {
    $count_hit = $_SESSION['count_hit'];
} else {
    $count_hit = 0;
}
$_SESSION['count_hit'] = $count_hit + 1;

echo "count_hit = " . $count_hit . "<br>";

```
## 14. Bài tập NFS server
* Thiết lập bên phía máy chủ NFS
```php
#Cài đặt NFS
dnf -y install nfs-utils

#Set thư mục chia sẻ
vi /etc/exports
/home/nfsshare 192.168.1.0/24(rw,no_root_squash)

#Tạo thư mục và khỏi động nfs
mkdir /home/nfsshare
systemctl enable --now rpcbind nfs-server


#Mở cổng cho NFS hoạt động
vi /etc/csf/csf.allow
#Cổng cho NFS
tcp|in|d=2049|s=192.168.1.18
tcp|in|d=2049|s=192.168.1.19
#Cổng cho nfsv3
tcp|in|d=111|s=192.168.1.18
tcp|in|d=111|s=192.168.1.19
#Cổng cho mount
tcp|in|d=33333|s=192.168.1.18
tcp|in|d=33333|s=192.168.1.19
```
* Thiết lập bên client NFS
```php
#Cài đặt nfs
dnf -y install nfs-utils

#Mount nfs
mount -t nfs 192.168.1.17:/home/nfsshare /mnt
#Kiểm tra
df -hT

Filesystem                  Type      Size  Used Avail Use% Mounted on
devtmpfs                    devtmpfs  374M     0  374M   0% /dev
tmpfs                       tmpfs     392M     0  392M   0% /dev/shm
tmpfs                       tmpfs     392M  5.6M  386M   2% /run
tmpfs                       tmpfs     392M     0  392M   0% /sys/fs/cgroup
/dev/mapper/rl-root         xfs        47G  2.6G   45G   6% /
/dev/sda1                   xfs      1014M  260M  755M  26% /boot
192.168.1.17:/home/nfsshare nfs4       47G  2.6G   45G   6% /mnt
tmpfs                       tmpfs      79M     0   79M   0% /run/user/0

#Mount nfsv3
mount -t nfs -o vers=3 192.168.1.17:/home/nfsshare /mnt
#Kiểm tra
df -hT /mnt
Filesystem                  Type  Size  Used Avail Use% Mounted on
192.168.1.17:/home/nfsshare nfs4   47G  2.6G   45G   6% /mnt

#Thiết lập auto mount
vi /etc/fstab
192.168.1.17:/home/nfsshare /mnt               nfs     defaults        0 0

#Gán liên kết động
dnf -y install autofs
vi /etc/auto.master
/-    /etc/auto.mount

vi /etc/auto.mount
/mnt   -fstype=nfs,rw  192.168.1.17:/home/nfsshare

#Khởi động autofs
systemctl enable --now autofs
cd /mnt

#Kiểm tra
grep /mnt /proc/mounts

192.168.1.17:/home/nfsshare /mnt nfs4 rw,relatime,vers=4.2,rsize=131072,wsize=131072,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=192.168.1.18,local_lock=none,addr=192.168.1.17 0 0
/etc/auto.mount /mnt autofs rw,relatime,fd=17,pgrp=1186,timeout=300,minproto=5,maxproto=5,direct,pipe_ino=31680 0 0
192.168.1.17:/home/nfsshare /mnt nfs4 rw,relatime,vers=4.2,rsize=131072,wsize=131072,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=192.168.1.18,local_lock=none,addr=192.168.1.17 0 0


#Ta có thể tạo file ở trong mnt để kiểm tra


```
