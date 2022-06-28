# 처음부터 제대로 배우는 라라벨

## 3. 라우팅 및 컨트롤러

### 3.3.3 모드 라우트 매칭 실패 시 대체 라우트 정의

```php
    // 라라벨 5.6 이상에서 사용 
    Route::fallback(function() {
        //
    });
```

### 3.6.1 사용자 입력 값 조회

```php
// 예제 3-27 
public function store(Request $request) 
{
    // $request->only([]) 선별적으로 request 값을 사용할 수 있다.
    Task::create($request->only(['title', 'description']));
    
    return redirect('tasks');
}
```

### 3.6.3 리소스 컨트롤러
 
```shell
$ php artisan make:controller TaskController --resource 
```

- route/web.php

```php
    Route::resource('tasks', TaskApiController::class); 
```

### 3.6.4 API 리소스 컨트롤러

- --resource 차이점 create edit 메소드를 생성하지 않는다.

```shell
$ php artisan make:controller ApiTaskController --api
```

- route/api.php
 
```php
    Route::apiResource('tasks', TaskApiController::class);
```

### 3.6.5 단일 액션 컨트롤러

- 컨트롤러가 단 하나의 라우트만 처리해야 할 때 사용

```php
public function __invoke(User $user)
{
    // 사용자의 아바타 이미지를 변경한다.
}

Route::post('user/{user}/update-avatar', UpdateUserAvatar::class);
```

### 3.7.1 묵시적 라우트 모델 바인딩

```php
Route::get('conferences/{conference}', function (Conference $conference) {
    return view('conferences.show')->with('conference', $conference);
});

Route::get('posts/{post:slug}', function (Post $post) {
    return $post;
});

Route::get('users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    // $user는 다음의 쿼리를 수행한 결과이다.
    // {user}는 URL 세크먼트 값이다.
    // User::find({user})->first();
    
    // $post는 다음의 쿼리를 수행한 결과이다.
    // {slug}는 URL 세크먼트 값이다.
    // User::find({user})->posts()->where('slug', {slug})->first();

    return $post;
});
```

### 3.8 라우트 캐싱

```shell
# 모든 라우트가 직렬화 되어 캐시로 저장 된다.
$ php artisan route:cache  

# 라우트 캐시 파일 삭제
$ php artisan route:clear
```

### 3.9.1 폼 메서드 스푸핑

```php
Route::get()
Route::post()
Route::any()
Route::match()
Route::patch()
Route::put()
Route::delete()
```

```html
<form action="/testk/5" method="post">
    <input type="hidden" name="_method" value="DELETE">
<!--    또는 -->
    @method('DELETE')
<!--    둘중 하나 선택-->
</form>
```

### 3.10 CSRF 보호

```html
<form action="/testk/5" method="post">
    <?php echo csrf_field(); ?>
<!--    또는 -->
    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">
<!--    또는 -->
    @csrf
<!--    둘중 하나 선택-->
</form>

<meta id="csrf-token" name="csrf-token" content="<?php echo csrf_field(); ?>">
```

```js
$.ajaxSetup({
    headers: {
        // 'X-CSRF-TOKEN' : $('#csrf-token').attr('content')
        'X-CSRF-TOKEN' : $('meta[name="csrf-token"]').attr('content')
    }
})
```

- 라라벨을 설치하고 Vue 기본 부트스트랩으로 작업하는 경우라면, CSRF가 이미 기능에 포함되어 적용할 필요가 없다. 

### 3.11 리다이렉트

```php
// 글로벌 핼퍼 함수를 사용하여 리다이렉트 응답 객체를 생성하는 방법
Route::get('redirect-with-helper', function() {
    return redirect()->to('login');
});

// 글로벌 핼퍼 함수를 짧게 활요하는 방법
Route::get('redirect-with-helper-shortcut', function() {
    return redirect('login');
});

// 퍼사드를 사용하여 리다이렉트 응답 객체를 생성하는 방법
Route::get('redirect-with-facade', function() {
    return Redirect::to('login');
});

// Route::redirect() 메서드를 사용하는 방법
Route::redirect('redirect-by-route', 'login');
```

### 3.11.2 redirect()->route() 메서드

```php
Route::get('redirect', function() {
    return redirect()->route('conference.index');
});

# 파라미터 전달하기
Route::get('redirect', function() {
    return redirect()->route('conference.index', ['conference' => 99]);
});
```

### 3.11.3 redirect()->back() 메서드
- 이전에 방문한 페이지가 어디인지 기억해둔다.

### 3.11.4 기타 리다이렉트 메서드

```php
home() // home으로 지정괸 라우트로 리다이렉트한다.
refresh() // 현재 페이지로 다시 접속하도록 리다이렉트한다. 
away() // 기본적인 URL 유효성 검사를 수행하지 않고 외부의 URL로 리다이렉트한다.
secure() // 보안 파리미터가 "true"로 설정된 to() 메서드와 같다
action() 
// redirect()->action('MyController@myMethod)
// 또는 redirect()->action([MyController::class, 'myMethod'])
guest() // 인증되지 않은 상태로 접근하면 인증페이지로 리다이렉트한다.
instended() // guest()와 비슷하다.
```

### 3.11.5 redirect()->with() 메서드

```php
Route::get('redirect-with-key-value', function() {
    return redirect('dashboard')
    ->with('error', true);
});

Route::get('redirect-with-key-array', function() {
    return redirect('dashboard')
    ->with(['error' => true, 'message' => 'Whoops!']);
});
```

- withInput()

```php
Route::post('form', function() {
    return redirect('form')
        ->withInput()
        ->with(['error' => true, 'message' => 'Whoops!']);
});

<input name="username" value="<?=old('username', '값이 없을떄 기본값')?>">
```

- withErrors()

```php
Route::post('form', function (Request $request) {
    $validator = Validator::make($request->all(), $this->validateionRules);
    
    if ($validator->fails()) {
        return back()
            -withErrors($validator)
            ->withInput();
    }
});
```

### 3.12 요청 중단하기

```php
Route::post('something-you-cart-do', function(Request $request) {
    abort(403, '접속 권한이 없습니다.');
    abort_unless($request->has('magicToken'), 403);
    abort_if($request->user()->isBanned, 403);
});
```

### 3.13 커스텀 응답

- response()->make() 메서드 
```php
return response()->make('Hello World!');
```

- response()->json(), response()->jsonp() 메서드
```php
return response()->json(User:all());
```

- response()->download(), response()->streamDownload(), response()->file() 메서드
```php
// file501751.pdf 파일을 myFile.pdf 이름으로 저장한다.
return response()->download('file501751.pdf', 'myFile.pdf');
// 브라우저에 바로 표시할 경우
return response()->file('file501751.pdf');
// 외부 서버로부터의 스트리밍 다운로드
return response()->streamDownload(function (){
    echo DocumentService::file('myFile')->getContent()
}, 'myFile.pdf');
```

### 3.14 CORS 처리

- config/cors.php

---

## 4. 블레이드 템플릿

### 4.1 블레이드 데이터 출력

```php
{{ $variable }} = <?=htmlentities($variable)?>
{!! $variable }} = <?=$variable?>
```
- 자바스크립트 핸들바 템플릿 {{ }} 사용하는데 블레이드 엔진은 @{{ }}로 시작하면 {{}} 무시된다.
- @verbatim 그대로 출력한다.
```php
<div class="container">
    Hello, @{{ name }}.
</div>

@verbatim
    <div class="container">
        Hello, {{ name }}.
    </div>
@endverbatim
```

### 4.2.1 조건문

- @if @elseif @else @endif
- @unless @endunless // if 반대조건문 if(!var)

### 4.2.2 반복문

- @for, @foreach, @while

- @forelse @empty @endforelse

#### @foreach와 @forelse  안에서 사용할 수 있는 @loop 변수

- index // 0부터 시작하는 인덱스
- iteration // 1부터 시작하는 인데스
- remaining // 반복문 안에 남아 있는 아이템 개수
- count // 반복문 안에 있는 전체 아이템의 개수
- first // 첫번째 아이템인지 아닌지 true/false
- last // 마지막 아이템인지 아닌지 true/false
- even // 짝수 true/false
- odd // 홀수 true/false
- depth // 반복문 중첩 첫번째 1, 두번째 2
- parent // 중첩되었을때 상위 @loop 없다면 null

```php
<ul>
@foreach ($pages as $page)
    <li>
        {{ $loop->iteration }}: {{ $page->title }}
        @if ($page->hasChildren()) 
        <ul>
        @foreach ($page->children() as $child)
            <li>
                {{ $loop->parent->iteration }}.{{ $loop->iteration }} : {{ $child->title }}
            </li>
        @endforeach
        </ul>        
        @endif
    </li>

</ul>
```

### 4.2.3 주석
- {{-- 여기에 작성한 코드는 HTML에 포합되지 않는다. --}}

#### @section/@show @yield 섹션 정의

- 브레이드 레이아웃
```php
<html>
    <head>
        <title>웹사이트 | @yield('title', 'Home Page')</title>
    </head>
    <body>
        <div class="container">
            @yield('content')
        </div>
        @section('footerScript')
            <script src="app.js"></script>
        @show
    </body>
</html>
```

- sign-up-button.blade.php @include page
```html
<button type="button">{{ text }}</button>
```

- 브레이드 레이아웃 확장
```php
@extends('layout.master')
@section('title', 'Dashboard')
@section('content')
    애플리케이션 대시보드에 오신 것을 환영합니다.
@endsection

<div>
@include('sign-up-button', ['text' => '여기를 클랙 하세요'])
</div>

@section('footerScript')
    @parent
    <script src="dashboard.js"></script>
@endsection

{{-- 해당 템플릿 파일이 있다면 포함 --}}
@includeIf('sidebar.admin', ['some' => 'data'])

{{-- 전달된 조건 값이 참인 경우에 해당 템플릿 파일 포함 --}}
@includeWhen($user->isAdmin(), 'sidebars.admin' , ['some' => 'data'])

{{-- 주어진 템플릿 배열 값에서 파일이 존재하는지 확인하고 첫 번째 템플릿 포함 --}}
@includeFirst(['custom.header', 'header'], ['some' => 'data'])
``` 

- @each

```php
// resource/views/sidebar.blade.php 
<div class="sidebar">
    // 첫번째파라미터는 파일명, 
    // 두번째 배열이나 컬렉션 변수, 
    // 반복문안에 변수명, 
    // 네번째 변수가 없을경우 출력해야할 템플릿 파일
    @each('partials.module', $modules, 'module', 'partials.empty-module)
</div>

// resource/views/partials/module.blade.php 
<div class="sidebar-module">
    <h1>{{ $module->title }}</h1>
</div>

// resource/views/partials/empty-module.blade.php 
<div class="sidebar-module">
    No modules :(
</div>
```

### 4.3.3 스택 사용하기

- @stack('name') @push('name')/@endpush @prepend('name')/@endprepend
- @component('partials.modal', ['class' => 'danger'])/@endcomponent

### 4.3.5 컴포넌트 테그 사용하기

```shell
$ php artisan make:component Alert

# app/View/Components/Alert.php
# resource/view/components/alert.blade.php
```

```php
<x-alert>
    주의 사항을 확인하십시오
</x-alert>
```

- 값 전달하기

```php
<x-alert type="error" :messae="$message" />
```

- app/View/Components/Alert.php
```php
class Alert extends Component
{  
    public $type;
    public $message;
    
    public function __construct($type, $message)
    {
        $this->type = $type,
        $this->message = $message,
    }
    
    public function render()
    {
        return view('components.alert');
    }
}
```

- resource/view/components/alert.blade.php
```php
<div class="alert alert-{{ $type }}" {{ $attriburtes }}>
    {{ $message }}
</div>

<!-- 다른 템플릿에서 사용 -->
<x-alert type="error" :message="$message" id="alertID" name="alertName"></x-alert>
```

- 인라인 뷰 컴포넌트 
  - blade 파일이 없고 컨트롤러만 있다. 간단한거 사용할때. 
```shell
$ php artisan make:component Inline/Alert --inline
```
 
- 동적 컴퓨턴트 
  - 라라벨 8부터 사용가능
```php
 <x-dynamic-component :component="$componentName" class="mt-4" />
```

### 4.4 뷰 컴포저와 서비스 주입 

- page 140 ~ 146 복잡하다 나중에 다음에 다시

### 4.5 커스텀 블레이드 지시어 
- 연산을 하는 코드들 넣으면 안된다.

- page 146 ~ 150 유용할 것 같다 다시보자