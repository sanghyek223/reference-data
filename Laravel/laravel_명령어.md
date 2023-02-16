## 명령어
#
#### - 프로젝트 생성
~~~bash
$ composer create-project --prefer-dist laravel/laravel {프로젝트명}
~~~
#
#### - 특정버전 프로젝트 생성
~~~bash
$ composer create-project laravel/laravel="{version}.*" {프로젝트명} --prefer-dist
~~~
#
#### - make:migration 커맨드를 이용하여 tasks 테이블을 구성
~~~bash
$ php artisan make:migration create_tasks_table --create=tasks
~~~
#
#### - 마이그레이션 실행
~~~bash
$ php artisan migrate
~~~
#
#### - 마이그레이션 실행시 INSERT
~~~php
// { root 경로 }\database\migrations\{ 마이그레이션 파일 } create 문 아래 insert 문 추가

<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->date('birthday');
            $table->string('phone');
            $table->string('email');
            $table->string('github');
            $table->string('blog');
        });

        DB::table('users')->insert([
            'name' => '{data}',
            'birthday' => '{data}',
            'phone' => '{data}',
            'email' => '{data}',
            'github' => '{data}',
            'blog' => '{data}',
        ]);
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('users');
    }
}

~~~
#
#### - 스케쥴러 실행
~~~bash
$ php artisan schedule:work
~~~
#
#### - 마이그레이션 롤백
~~~bash
$ php artisan migrate:rollback
~~~
#
#### - 컨트롤러 & 마이그레이션 생성
~~~bash
$ php artisan make:model {파일명} -c -m
~~~
#
#### - app Key 생성
~~~bash
$ php artisan key:generate
~~~
