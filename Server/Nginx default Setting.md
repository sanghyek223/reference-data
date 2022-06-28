## Nginx Default Setting
#
###### Nginx

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