#
###### 설치
~~~bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update

sudo apt install php8.2-fpm -y
sudo apt-get install php8.2 php8.2-mbstring php8.2-gettext php8.2-zip php8.2-fpm php8.2-curl php8.2-mysql php8.2-gd php8.2-cgi php8.2-soap php8.2-sqlite3 php8.2-xml php8.2-redis php8.2-bcmath php8.2-imagick php8.2-intl -y
systemctl restart php8.2-fpm
systemctl enable php8.2-fpm

curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer

sudo apt install nginx -y
sudo nginx -t
sudo systemctl enable nginx

sudo apt-get install mysql-server -y
sudo ufw allow mysql
sudo systemctl start mysql
sudo systemctl enable mysql

sudo apt install git
git config --global user.name "sanghyek223"
git config --global user.email "jsbxiw819224@gmail.com"
git config --list

ls -l ~/.ssh/id_*.pub # ssh 키 있는지 확인
ssh-keygen # ssh 키 생성

TIMEZONE="Asia/Seoul"
timedatectl set-timezone $TIMEZONE

php artisan key:generate
chown -R www-data:www-data storage bootstrap/cache
~~~