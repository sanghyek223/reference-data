## Laravel & Naver Sens Sms 연동
#
###### 네이버 sens 연결
[seungmun/laravel-sens 설치](https://github.com/seungmun/laravel-sens)

~~~bash
$ composer require seungmun/laravel-sens # 컴포저로 sens 설치
~~~

~~~bash
$ php artisan vendor:publish --provider="Seungmun\Sens\SensServiceProvider" --tag="config" # vendor 에 서비스 추가
~~~
#
###### Notification 생성
~~~bash
$ php artisan make:notification SendPurchaseReceipt # notification(알림) 생성 (sms)
~~~
~~~bash
$ php artisan make:notification SendPurchaseInvoice # notification(알림) 생성 (mms)
~~~
#
###### .env 파일 설정 추가
~~~ini
SENS_ACCESS_KEY={ACCESS_KEY}
SENS_SECRET_KEY={SECRET_KEY}
SENS_SERVICE_ID={SERVICE_ID}
SENS_ALIMTALK_SERVICE_ID=your-alimtalk-service-id
SENS_PlUS_FRIEND_ID=your-plus-friend-id
~~~
#
###### Notifications 수정
~~~php 
// app\Notifications\SendPurchaseReceipt 파일에 toMail 함수 -> 수정
public function toSms($notifiable) 
{
    return (new SmsMessage)
        ->to($notifiable->phone_num) // 수신 번호
        ->from($notifiable->company_num) // 발신 번호
        ->content($notifiable->sms_content." ".$notifiable->confirm_num) // 내용
        ->contentType('COMM')// You can ignore it (default: COMM)
        ->type('SMS');  // You can ignore it (default: SMS)
}
~~~
#
###### Notifications 호출
~~~php 
$sms_result = sms_setting::find(1); // find 로 값을 찾아야함 다른조건 error남
$sms_result["company_num"] = company_info::where('category','tel')->value('content');
$sms_result["phone_num"] = $request->input('u_phone');
$sms_result["confirm_num"] = $randomNum;
$sms_result->notify(new SendPurchaseReceipt); // sms호출
~~~
