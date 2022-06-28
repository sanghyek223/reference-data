## Ec2 Ubuntu 에 nginx php Laravel 설치
#
###### 최신화 
~~~bash
$ sudo apt update

$ sudo apt upgrade
~~~

#
###### root 권한
~~~bash
$ sudo passwd root # root 비밀번호를 먼저 변경

$ sudo vi /etc/ssh/sshd_config # sshd_config 설정정보 변경

$ :set nu # 보기 좋게 라인수 표시 (생략 해도 상관 없음)


"PermitRootLogin yes" 부분의 주석을 해제하고 저장 (:wq)


루트 경로에 .ssh 경로를 생성하여 준다.
$ sudo mkdir /root/.ssh # 이미 디렉토리가 있을 경우 생성되지 않는다


ec2 유저의 인증키를 root로 복사 한다
$ sudo cp /home/{ec2-user}/.ssh/authorized_keys /root/.ssh


sshd를 리스타트 한다.
$ sudo systemctl restart sshd


ecs-user 계정으로 접속 했던대로 root로 접속하여 확인
~~~
#
###### Nginx 설치
~~~bash
$ sudo apt-get install nginx
~~~
###### Nginx 참고
~~~bash
$ service nginx start # 시작
$ service nginx stop # 정지
$ service nginx restart # 재시작
$ service nginx reload # 설정파일을 재로드
$ service nginx status # 현재 상태

or

$ systemctl start nginx # 시작
$ systemctl stop nginx # 정지
$ systemctl restart nginx # 재시작
$ systemctl reload nginx # 설정파일을 재로드
$ systemctl status nginx # 현재 상태
~~~
#
#
## PHP 설치
~~~bash
$ sudo apt-get install php

$ sudo apt install -y php7.4-fpm php7.4-gd php7.4-json php7.4-mysql php7.4-curl php7.4-mbstring php7.4-intl php-imagick php7.4-xml php7.4-zip


// 관련 모듈 설치

$ sudo apt-get install php-mbstring
$ sudo apt-get install php-curl
$ sudo apt-get install php-mysql
$ sudo apt-get install php-xml
$ sudo apt-get install php-xmlrpc
$ sudo apt-get install php-gd
$ sudo apt-get install php-imagick
$ sudo apt-get install php-dev libmcrypt-dev gcc make autoconf libc-dev pkg-config
$ sudo pecl install mcrypt-1.0.1
~~~
#
#
## 컴포져 설치
~~~bash
$ sudo apt-get install curl​

$ sudo curl -s https://getcomposer.org/installer | php

$ sudo mv composer.phar /usr/local/bin/composer # composer 란 명령어를 전역에서 사용할 수 있게 하기위해
~~~
#
#
## Lravel 설치
###### root 디렉토리에서 라라벨 설치 명령 실행
~~~bash
$ composer create-project --prefer-dist laravel/laravel {프로젝트명}
~~~
#
#
## Nxigx + Laravel  연동
###### nginx conf 수정
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
###### Nginx restart
~~~bash
$ sudo service nginx restart
~~~






