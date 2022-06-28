## Error 해결
#
#### # 서버접속시 404 Error
~~~bash
$ chmod 777 { directory } # 폴더에 권한 부여
~~~
#
#### - Failed to open stream: Permission denied
###### - ubuntu
~~~bash
$ chown -R www-data:www-data storage bootstrap/cache
~~~
###### - centos
~~~bash
$ chmod -R ugo+rw storage
~~~
#
#### - Group By Error
~~~php
// config\database.php
-> mysql -> strict -> false 로 수정
~~~
