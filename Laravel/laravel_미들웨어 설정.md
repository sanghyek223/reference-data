## 미들웨어 
#
#### - 미들 웨어  생성
~~~bash
$ php artisan make:middleware {미들웨어 이름}
~~~
~~~php
// app\Http\Middleware -> {미들웨어파일}


// handel 함수에 조건달기

    public function handle(Request $request, Closure $next)
    {
        if (Auth::user() &&  Auth::user()->u_level == 10) {
            return $next($request);
        }
        //flash('회원님은 권한이 없습니다.');
        return redirect('/');
    }

// app\Http\Kernel.php 파일에 안에 조건 추가

    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
        //여기까지 가 원본

        // 추가된내용
        'exchange' => \App\Http\Middleware\Exchange::class,
        'admin' => \App\Http\Middleware\Admin::class, 

    ];
~~~