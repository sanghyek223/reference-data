# Centos php7 httpd 삭제 후 php 8 nginx 설치
#
#### yum 으로 설치한 php 와 apache 확인
#
~~~bash
$ yum list installed | grep php # php 확인

$ yum list installed | grep httpd # apache 확인
~~~
#
#
#### php & 아파치 제거 (설치 유무 확인후 리스트 목록 전체 삭제)
#
~~~bash
$ yum -y remove {pakage_name} # 패키지 삭제 명령
~~~
~~~bash
$ yum -y remove php* # php 제거

$ yum -y remove httpd* # apache 제거
~~~
#
###### 설치된 것이 제대로 제거 되었는지 확인
~~~bash
$ rpm -qa httpd php # 메세지가 나오지 않으면 정상적으로 제거
~~~
#
#
#### yum update
#
~~~bash
$ yum -y update
~~~
#
#
#### Nginx install
#
~~~bash
cd /etc/yum.repos.d/ # 경로 이동
vi nginx.repo # 아래 내용 추가
~~~
#
###### repo 추가
~~~bash
[nginx] 
name=nginx repo 
baseurl=http://nginx.org/packages/centos/7/$basearch/ 
gpgcheck=0 
enabled=1
~~~
#
###### Nginx 설치 & System enable
~~~bash
$ yum -y install nginx # install

$ systemctl start nginx # start

$ systemctl enable nginx # enable
~~~
#
#
#### PHP 8.1 Install
#
~~~bash
$ yum -y install epel-release

$ yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

$ yum list installed | grep yum-utils # 없을경우 설치 : yum -y install yum-utils

$ yum-config-manager --enable remi-php81 # php 8.1 기본 설치 로 변경

$ yum -y install php php-cli php-fpm php-mysqlnd php-mysql php-mysqli php-devel php-zip php-gd php-curl php-xml php-json php-intl php-mbstring php-mcrypt php-posix php-shmop php-soap php-sysvmsg php-sysvsem php-sysvshm php-xmlrpc php-opcache

$ systemctl start php-fpm # start
$ systemctl enable php-fpm # enable
~~~
#
#
#### Nginx PHP setting
#
###### php.ini 파일 수정
~~~bash
$ vi /etc/php.ini # php.ini 파일 수정
~~~
#
###### www.conf 파일 수정
~~~bash
$ vi /etc/php-fpm.d/www.conf

// 수정내역
;user = apache # 변경전 
user = nginx 

;group = apache # 변경전 
group = nginx 

;listen.owner = nobody # 변경전 
listen.owner = nginx 

;listen.group = nobody # 변경전 
listen.owner = nginx 

;listen.mode = 0660 # 변경전 
listen.mode = 0660 

;listen = 127.0.0.1:9000 # 변경전 
isten = /var/run/php-fpm/php-fpm.sock 

;pm.max_children = 50 # 변경전 
pm.max_children = 130 

;pm.start_servers = 5 # 변경전 
pm.start_servers = 30 

;pm.min_spare_servers = 5 # 변경전 
pm.min_spare_servers = 30 

pm.max_spare_servers = 35 # 변경전 
pm.max_spare_servers = 100 

;pm.max_requests = 500 # 변경전 
pm.max_requests = 500
~~~
#
###### Nginx default.conf 파일 수정
~~~bash
vi /etc/nginx/conf.d/default.conf # default.conf 파일 수정
~~~
#
###### Nginx 기본 셋팅
~~~ini
server {
    listen 80 default;

    client_max_body_size 108M;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/nginx_error.log;

    root {프로젝트 경로};
    index index.php;

    if (!-e $request_filename) {
        rewrite ^.*$ /index.php last;
    }

    location ~ \.php$ {
        # fastcgi_pass 127.0.0.1:9000;
        fastcgi_pass   unix:/var/run/{php버젼-fpm}.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PHP_VALUE "error_log=/var/log/php-fpm/php_errors.log";
        fastcgi_buffers 16 16k;
        fastcgi_buffer_size 32k;
        include fastcgi_params;
    }
    
}
~~~
#
###### Nginx 재시작
~~~bash
$ systemctl restart nginx # nginx 재시작
~~~
#
#
## PHP JIT 활성화
#
~~~bash
$ vi /etc/php-zts.d/10-opcache.ini # 10-opcache 파일 수정

// 수정내역
opcache.enable=1 
opcache.jit_buffer_size=100M 
opcache.jit=tracing
~~~
#
###### php 재시작
~~~bash
$ systemctl restart php-fpm
~~~
