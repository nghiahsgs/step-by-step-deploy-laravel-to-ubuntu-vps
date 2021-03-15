# step-by-step-deploy-laravel-to-ubuntu-vps
step by step deploy laravel to ubuntu vps


# Cách deploy laravel lên server ubuntu 


## Bước 1: Cài php và phpmyadmin
Chi tiết xem ở bài này http://nghiahsgs.com/cach-cai-wordpress-tren-ubuntu-server-ubuntu-20-su-dung-lamp/

+ Cài mysql và đổi mật khẩu cho root
```
sudo apt-get update
sudo apt-get install mysql-server
```
+ Đổi mật khẩu cho root trong mysql
```
sudo mysql
mysql > SELECT user,authentication_string,plugin,host FROM mysql.user;

mysql > ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

mysql > FLUSH PRIVILEGES;

mysql > SELECT user,authentication_string,plugin,host FROM mysql.user;
```

+ Xem status của mysql
```
systemctl status mysql.service
```

+ Cài php myadmin
```
apt update && upgrade

sudo add-apt-repository universe

apt install phpmyadmin
```

## Bước 2: Cài composer
```
sudo apt update
sudo apt install php-cli unzip
cd ~
curl -sS https://getcomposer.org/installer -o composer-setup.php
```

```
HASH=`curl -sS https://composer.github.io/installer.sig`
echo $HASH
```

```
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
```

```
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

# A: cách 1: serve trực triếp laravel trên server
## Bước 3: Cài laravel 
+ Tạo project mới laravel
```
cd ~
composer create-project --prefer-dist laravel/laravel example-app
```

+ Mở quyền cho các thư mục (www-data là viết cho apache2)
```
sudo chown -R www-data.www-data ~/example-app/storage
sudo chown -R www-data.www-data ~/example-app/bootstrap/cache
```

+ Serve serve laravel  (giả sử vps của ip là 45.32.106.247)
```
php artisan serve --host 45.32.106.247 --port 3000
```

## Bước 4: Vào trình duyệt và gõ 
my_ip:myport là dùng bt

## Bước 5: Cài pm2 rồi run ẩn
pm2 start "php artisan serve --host 45.32.106.247 --port 3000"

# B: Cách 2 dùng apache
## Bước 3 Cài laravel:
+ Tạo project mới laravel
```
cd ~
composer create-project --prefer-dist laravel/laravel example-app
```
+ Di chuyển thư mục code vào /var/www
```
mv /example-app /var/www
```
+ Mở quyền cho các thư mục (www-data là viết cho apache2)
```
sudo chown -R www-data.www-data /var/www/example-app/storage
sudo chown -R www-data.www-data /var/www/example-app/bootstrap/cache
```
## Bước 4: cấu hình apache
Tạo mới file config
```
vi /etc/apache2/sites-available/test.conf
```
Dán nội dung sau vào
```
<VirtualHost *:80>
        DocumentRoot "/var/www/example-app/public"
        ServerName nghiadeptrai.com
        <Directory /var/www/example-app/public>
              AllowOverride All
        </Directory>


</VirtualHost>
```
+ enable cấu hình lên
```
sudo a2ensite test.conf

sudo service apache2 restart

sudo a2enmod rewrite

sudo service apache2 restart
```
## Bước 5: Chỉnh file host
mở file host
```
vi /etc/hosts
```
```
ip_vps nghiadeptrai.com
```
Vaof trình duyệt vào gõ nghiadeptrai.com và tận hưởng
