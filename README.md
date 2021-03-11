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
