## Letsencrypt Certbot 설치
#
###### Certbot PPA 추가
~~~bash
 $ sudo apt install software-properties-common
 $ sudo add-apt-repository universe
 $ sudo add-apt-repository ppa:certbot/certbot
 $ sudo apt update
~~~
#
###### Certbot 설치
~~~bash
 $ sudo apt install certbot python3-certbot-nginx
~~~
#
###### Certbot 실행
~~~bash
 $ sudo certbot --nginx
~~~
#
###### Certbot 자동갱신
~~~bash
$ sudo crontab -e

// crontab setting ( 매월1일 12시 갱신 )
0 12 1 * * sudo certbot renew --renew-hook="sudo service restart nginx"
~~~
#
###### Certbot 등록확인
~~~bash
$ sudo certbot certificates
~~~

###### letsencrypt 버전 확인
~~~bash
$ letsencrypt --version
~~~

###### 우분투 버전 확인
~~~bash
$ cat /etc/issue
~~~
###### 어떤 인증서가 있는지 확인하는 명령어
~~~bash
$ sudo certbot certificates
~~~
###### 인증서 자동갱신 테스트
~~~bash
$ sudo certbot renew --dry-run 
~~~