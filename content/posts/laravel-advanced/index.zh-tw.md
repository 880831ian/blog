---
weight: 4
title: "Laravel 進階 (內建會員系統、驗證 RESTful API 是否登入、使用 Repository 設計模式)"
date: 2022-03-08T09:00:59+08:00
lastmod: 2022-03-08T14:27:53+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["PHP", "框架", "Laravel", "RESTful API", "Repository", "實作","介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

本篇是 [Laravel 介紹](https://pin-yi.me/laravel/) 的進階篇，由於有些說明介紹會沿用上一篇的內容，建議要先瀏覽過上一篇呦～

## Laravel 內建會員系統

Laravel 這個框架，很方便的地方就是，他可以將我們常用的帳號登入註冊等功能內建在 Laravel 框架內，那我們來看一下要怎麼使用吧。(本篇 Laravel Framework：5.4.36)

它的配置文件在 `config/auth.php` ，其中包含了用於調整認證服務行為或是標注選項配置等。

<br>

要怎麼開始呢!? 先使用 `artisan` 指令產生我們要的檔案以及路由吧！

```sh
$ php artisan make:auth
```

<br>

主要會產生

```sh
	new file:   app/Http/Controllers/HomeController.php
	new file:   resources/views/auth/login.blade.php
	new file:   resources/views/auth/passwords/email.blade.php
	new file:   resources/views/auth/passwords/reset.blade.php
	new file:   resources/views/auth/register.blade.php
	new file:   resources/views/home.blade.php
	new file:   resources/views/layouts/app.blade.php
	modified:   routes/web.php
```

<br>

其中 `routes/web.php` 多了：

```php
Auth::routes();

Route::get('/home', 'HomeController@index')->name('home'); 
```
<br>

views 底下放的就是顯示的畫面，所以現在可以瀏覽
 
登入頁面：`http://127.0.0.1:8000/login`

註冊頁面：`http://127.0.0.1:8000/register`

其他像是 `controller` 或是 `migration` 都已經內建在裡面了，稍後實作會講到！

<br>

### 實作

#### Migration
我們照上一篇的流程，由於 `route` 已經設定好，所以我們先來設定 `migration` ， 檔案就放在 `database/migrations` 底下的 `{日期時間}_create_users_table.php` ，我們來看一下預設的資料表有哪些欄位吧!

```php
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
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
```
簡單看一下，他會建立一個名叫 `users` 的資料表，欄位分別有自動累加(id)、字串(name)、唯一字串(email)、字串(密碼)、rememberToken()、timestamps()。

<br>

那我們這次想要做的事，不使用 email 做登入，改成使用 username來當作登入驗證，所以我們把 name、email的欄位改成
```php
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('username')->unique();
            $table->string('password');
            $table->string('api_token', 80)->unique();
            $table->rememberToken();
            $table->timestamps();
        });
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
```
設定 username 欄位為唯一值，移除掉 email ，再加入一個 api_token 用於後續的 RESTful API 驗證。

<br>

使用 `php artisan migrate` 將 `users` 這個 table 給建起來。
 
 因為我們修改 name、email 改成 username 這個欄位，所以我們也要修改一下 views 顯示的畫面，由於是簡單的 HTML 這邊就不再多描述，直接放上修改後的程式碼。
 
 #### View
 
 ##### Login.blade
 
 ```html
<div class="form-group{{ $errors->has('username') ? ' has-error' : '' }}">
 <label for="username" class="col-md-4 control-label">Username</label>

   <div class="col-md-6">
     <input id="username" type="text" class="form-control" name="username" value="{{ old('username') }}" required autofocus>
                                
     @if ($errors->has('username'))
            <span class="help-block">
                    <strong>{{ $errors->first('username') }}</strong>
            </span>
     @endif
  </div>
</div>
```   
<br>

##### Register.blade

```html
<div class="form-group{{ $errors->has('username') ? ' has-error' : '' }}">
 <label for="username" class="col-md-4 control-label">Username</label>

 <div class="col-md-6">
  	<input id="username" type="text" class="form-control" name="username" value="{{ old('username') }}" required>

     @if ($errors->has('username'))
            <span class="help-block">
                    <strong>{{ $errors->first('username') }}</strong>
            </span>
     @endif
  </div>
</div> 
```
 
 <br>
 
 #### Model
 
接下來到 `app` 底下找到 `User.php` 檔案，由於筆者習慣將 `model` 放到專屬的資料夾，不要讓他在 `app` 裡面流浪，所以會建立一個 `Models`  的資料夾，來存放所有的 `models` ，那移動原本的 `model` 有些有使用到它的路徑都要做修改，這邊以 `User` 檔案為示範。(因為後續會說到怎麼驗證登入 API ，所以上一篇的 Message model 也要記得修改歐！)
 
 <br>
 
 因為我們移動後，原本的路徑是 `App` 要改成 `App\Models`，會影響到的程式有以下幾個 (附上片段程式碼)
 
 * `config/auth.php` 約在70行左右
 
```php
    'providers' => [
        'users' => [
            'driver' => 'eloquent',
            'model' => App\Models\User::class, //修改片段
        ],
```
<br>
 
  * `config/services.php` 約在33行左右
 
```php
    'stripe' => [
        'model' => App\Models\User::class, //修改片段
        'key' => env('STRIPE_KEY'),
        'secret' => env('STRIPE_SECRET'),
    ],
```
<br>
 
   * `database/factories/UserFactory.php` 約在15行左右
 
```php
$factory->define(App\Models\User::class, function (Faker\Generator $faker) { //修改片段
    static $password;

    return [
        'name' => $faker->name,
        'email' => $faker->unique()->safeEmail,
        'password' => $password ?: $password = bcrypt('secret'),
        'remember_token' => str_random(10),
    ];
});
```
<br>
 
   * `app/Http/Controllers/Auth/RegisterController.php` 約在5行左右
 
```php
use App\Models\User;
```
<br>
 
 都修改好了，我們就繼續來修改 `User` 這個 model ，將原本的 name、email ，修改成以下
 ```php
 <?php

namespace App\Models;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'username', 'api_token', 'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'api_token', 'remember_token', 
    ];
}
 ```
 
 <br>
 
 裡面有一個是 `fillable` 跟 `hiddem` ，順便解釋一下這兩個是在做什麼
 
 * fillable：當使用者輸入這些 attribute 以外的參數(資料表的欄位)，就會發生錯誤。
 * hidden：想要限制能出現在陣列或是JSON 格式的屬性資料，比如密碼欄位等等，不想要顯示，只需要把該欄位加入 `hidden`。 
 
 再加碼一個
 
  * guarded：這個屬性與 `fillable` 相反，當使用者輸入該參數的值，就會被擋掉。以下這個例子是允許任何的 input 資料，但十分建議不要這樣。
 ```php
     protected $guarded = [];
 ```

<br>

#### Controller

接下來是要修改我們的 controller，主要需要修改的有兩個，檔案在 `app\Http\Auth` 底下的 `registerController` 、 `loginController` 兩個檔案，我們先來修改 `registerController` 註冊功能，看一下原本的程式碼在做什麼事情。

##### RegisterController

<br>
原本程式碼

```sh
    protected function validator(array $data)
    {
        return Validator::make($data, [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:6|confirmed',
        ]);
    }

    /**
     * Create a new user instance after a valid registration.
     *
     * @param  array  $data
     * @return \App\User
     */
    protected function create(array $data)
    {
        return User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => bcrypt($data['password']),
        ]);
    }
```
可以看到在 `validator()` 就是在驗證註冊時的欄位，以 email 來說明，他需要符合必填(required)、字串(string)、email格式(email)、最大長度(max:255)、在 users 資料表中唯一(unique:users) 才可以註冊，另外兩個也是如此。

那 `create()` 就是會建立註冊的資料到資料表中，就是這麼簡單XD，詳細可以參考 [Laravel 官網 Form Request Validation](https://laravel.com/docs/5.4/validation#form-request-validation)
 
<br>

那還記得我們把 name、email 給刪掉，改成 username 來做登入嗎，我們來看一下我們要修改哪些東西！

```sh
    protected function validator(array $data)
    {
        return Validator::make($data, [
            'username' => 'required|string|max:255|unique:users',
            'password' => 'required|string|min:6|confirmed',
        ]);
    }

    /**
     * Create a new user instance after a valid registration.
     *
     * @param  array  $data
     * @return \App\User
     */
    protected function create(array $data)
    {
        return User::create([
            'username' => $data['username'],
            'api_token' => Str::random(60),
            'password' => bcrypt($data['password']),
        ]);
    }
``` 
由於我們刪除 email ，所以要把 unique:users 設定在 username ，因為我們後續會需要用到一組 api_token 來驗證是誰在使用，所以我們在建立帳號時 `create()`，會先使用 `Str::random(60)` 產生一個60字元的隨機亂數，我們用到 str ，所以在上面還要先引用 `use Illuminate\Support\Str;`
 
<br>

##### LoginController

註冊頁面都已經修改好了，理論上連線到 `http://127.0.0.1:8000/register` 頁面，輸入 username 以及 密碼，會可以註冊成功囉，那接下來我們來修改登入的 controller。

```php
    use AuthenticatesUsers;

    /**
     * Where to redirect users after login.
     *
     * @var string
     */
    protected $redirectTo = '/home';

    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('guest')->except('logout');
    }
```
因為 Laravel 框架內建了驗證登入功能，所以只需要使用 `use AuthenticatesUsers;` 就可以達到驗證效果，登入成功就會使用 `redirectTo` 導向到 `/home`，那問題來了，因為 `AuthenticatesUsers` 預設是使用 email 來做登入驗證，但我們改成 username，我們要怎麼修改呢！？ 一起來看看吧 

```php
    //由於判斷是用 AuthenticatesUsers 這個內建的 trait 來實作，其中 username() 這個方法就是來指定登入要用的欄位
    public function username()
    {
        return 'username';
    }
```
上面有說因為使用了 `AuthenticatesUsers`  來做驗證，我們盡量不去修改框架內的檔案，當然 Laravel 也有配套措施，只要使用 `username()` 這個方法就可以指定登入要驗證的欄位，因為筆者在學習這段時也找了很久，這邊附上[ Laravel API](https://laravel.com/api/6.x/Illuminate/Foundation/Auth/AuthenticatesUsers.html) 可以去查看一下。

我們這時就可以使用 `http://127.0.0.1:8000/login` 來登入囉！試試看吧～
 
 
### 驗證 RESTful API 是否登入

本篇會使用到上一篇的 RESTful API 留言板來進行修改，所以有興趣的朋友，可以先去看玩上一篇 [Laravel 介紹]() 歐～

接下來也會同時使用 Repository 設計模式來修改程式碼，那 Repository 設計模式是什麼呢，讓我先來介紹。

#### Repository 設計模式

還記得我們上一次，把所有的處理都放在 `Controller` 裡面嗎！如果只是單一個小專案，還可以這樣做沒關係，但專案越來越大，會使用的功能也越來越多，會造成 `Controller` 檔案肥大且難以維護，基於 SOLID 原則，我們應該要使用 Repository 設計模式來補助  `Controller`，將相關的資料庫邏輯放在不同的 `Repository`，方便中大型的專案做維護。

<br> 
 

{{< admonition type=tip title="小知識" open=true >}}
SOLID：簡單來說就是物件導向設定上為了讓軟體維護、開發變得更容易的五個準則的縮寫。
* Single Responsibility Principle (SRP) 單一職責原則
* Open-Closed Principle (OCP) 開放封閉原則
* Liskov Substitution Principle (LSP) 里氏替換原則
* Interface Segregation Principle (ISP) 介面隔離原則
* Dependency Inversion Principle(DIP) 依賴反轉原則

SOLID目的也就是讓你程式碼達成低耦合、高內聚、降低程式碼壞味道，透過分離與clean code來提高可讀性會讓你的程式碼等同於設計文件，所以在修改或新增過程中降低產生Bug的機率，也可以較快的找到與解決出問題的地方，可以有效的減少技術債。

(資料來源：[我該學會SOLID嗎?](https://medium.com/@f40507777/%E6%88%91%E8%A9%B2%E5%AD%B8%E6%9C%83solid%E5%97%8E-4e73887c9156))
{{< /admonition >}} 
 
<br>

不太清楚沒關係，我們會慢慢介紹到！那我們先來看看這次要修改什麼呢！？ 我想要在使用 API 時，去檢查有沒有登入，才會進行動作，舉個例子，我們希望在新增留言、修改留言以及刪除留言都需要登入，且是由本人操作才算成功。

<br>

#### Migration

在此之前，我們先來修改一下上次的 `migration` {日期時間}_create_message_table.php 檔案吧

```php
    public function up()
    {
        Schema::create('message', function (Blueprint $table) {
            $table->bigIncrements('id'); //留言板編號
            $table->integer('user_id')->unsigned(); //留言者ID
            $table->foreign('user_id')->references('id')->on('users');
            $table->string('name', 20); //留言板姓名
            $table->string('content',20); //留言板內容
            $table->integer('like')->unsigned()->nullable(); //按讚者
            $table->foreign('like')->references('id')->on('users');
            $table->integer('version')->default(0); 
            $table->timestamps(); //留言板建立以及編輯的時間
        });
    }
```
可以看到我們將資料庫的名稱從 `messsages` 改為 `message` ，後續程式部分也都會修改，大家要在注意一下 ～ 

我們這次加入了留言者 ID (使用外鍵連接 `users` 的 `id`)、按讚者 ID (使用外鍵連接 `users` 的 `id`)、留言板樂觀鎖欄位。

我們希望每一個留言都可以對應到 `users` 的 帳號 `id` ，以及新增一個可以讓使用者按讚的功能，使用外鍵來可以存放按讚者的 ID ，還有判斷[樂觀鎖](https://pin-yi.me/mysql#)的欄位。

<br>

列出本次會使用的功能以及對應的方法、是否需要登入、登入後其他人是否可以操作
<br>
| 功能 | 方法 | 是否需要登入 | 登入後其他人是否可以操作 | 
| :---: | :---: | :---: | :---: |
| 查詢全部留言 | getAllMessage | 否 | 不需登入 | 
| 查詢{id}留言 | getMessage | 否 | 不需登入 | 
| 新增留言 | createMessage | 是 | 否 |
| 修改{id}留言 | updateMessage | 是 | 否 | 
| 按讚{id}留言 | likeMessage | 是 | 可以 |
| 刪除{id}留言 | deleteMessage | 是 | 否 |

<br>

我們先設定 Middleware ，什麼是 Middleware 呢！？

#### Middleware
Middleware 中文翻譯是中介軟體，是指從發出請求 (Request)之後，到接收回應(Response)這段來回的途徑上，

用來處理特定用途的程式，比較常用的 **Middleware** 有身份認證 (Identity) 、路由(Routing) 等，再舉個例子

```
某天早上你去圖書館看書，
下午去公園畫畫，
晚上去KTV 唱歌，
等到要準備回家的時候發現學生證不見了，
你會去哪裡找? (假設學生證就掉在這3個地方)
```

對於記憶不好的人來說，會按照 KTV > 公園 > 圖書館的路線去尋找。

假設在公園找到學生證，就不會再去圖書館了，由於這條路是死巷，所以只能返回走去KTV的路，這個就是 **Middleware** 的運作原理。

所以我們需要再請求時，先檢查是否有登入，才可以去執行需要權限的功能。
我們在建立 `user` 的時候，有多了一個 `api_token` 的欄位，就是來驗證是否登入以及操作的人是誰，那我們來看看要怎麼實作吧！

<br>

先下指令生成一個放置登入驗證權限的 `Middleware` ，我把它取名為 `TokenAuth`
```sh
$  php artisan make:middleware TokenAuth
Middleware created successfully.
```

<br>

接著要把剛剛生成的 `TokenAuth` 檔案放置在 `app/Http/Kernel.php` 檔案中

```php
    protected $routeMiddleware = [
        'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'token.auth' => \App\Http\Middleware\TokenAuth::class  //這邊
    ];
```

接下來我們就可以開始撰寫 `TokenAuth` 檔案的內容了

```php
    public function handle($request, Closure $next)
    {
        // 確認 Http Header 的 token 有無對應的使用者
        $api_token = request()->header('api_token');
        $auth_user = User::where('api_token', $api_token)->get();

        if(!count($auth_user)){
            return response(['message' => '用戶需要認證'], 401);
        }

        // 將通過驗證的使用者存放於 attribute 裡以便將變數傳給 controller
        $request->attributes->set('auth_user', $auth_user);
        return $next($request);
    }
```
這邊的意思是指，在 request 的時候，我們的 `api_token` 會夾在 header 中做請求，我們先將他存入 `$api_token` 的變數中，再來檢查 `users` 裡面是否有這個 token ，如果沒有就會回應用戶需要認證 401 的錯誤 status code，如果有這個 token ，我們就可以拿這個 token 對應的帳號名稱以及 ID 來做使用。

<br>

##### Route

接下來，我們要把我們設定好的 `TokenAuth` 設定在 `route\api.php` 的路由中，
```php
Route::get('message', 'MessageController@getAll');
Route::get('message/{id}', 'MessageController@get');
Route::post('message', 'MessageController@create')->middleware('token.auth');
Route::put('message/{id}', 'MessageController@update')->middleware('token.auth');
Route::patch('message/{id}', 'MessageController@like')->middleware('token.auth');
Route::delete('message/{id}', 'MessageController@delete')->middleware('token.auth');
```
可以看到跟我們上一篇的 route 設定的差不多，只是將 `MessageController` 後面的方法做了一點變化，簡化名稱(這與我們後面講到的 Repository 設計模式有關)，以及加上 `like` 這個方法來當作我們的按讚功能，後面將我們需要登入驗證才可以使用的功能，加入 `middleware('token.auth');`

<br>

##### Model

因為我們在取得資料時，不希望顯示 `user_id` 以及 `version` 給使用者，所以在 `app\Models\Message.php` 這個 model 裡面用 `hidden` 來做設定。

```php
    protected $table = 'Message';
    protected $fillable = [
        'content'
    ];
    protected $hidden = [
        'user_id','version'
    ];
```

<br>

##### Controller/Repository

接下來會連同 Repository 設計模式一起說明，所以會講得比較詳細。還記得我們上次把 RESTful API 要處理的邏輯都寫在 Controller 裡面嗎，我們光是一個小功能就讓整個 Controller 變得肥大，在後續維護時或是新增功能時，會導致十分不便利，因此我們要將 Repository 設計模式 給導入，那要怎麼實作呢～

<br>

我們要先在 `app` 底下新增一個 `Repositories` 目錄，在目錄底下再新增一個 `MessageRepository.php` 檔案來放我們處理邏輯以及與資料庫拿資料的程式，我們整個檔案分成幾段來看

<br>

先新增這個檔案的命名空間，並將我們原先放在 `MessageController` 的引用給拿進來。
```php
namespace App\Repositories;

use App\Models\Message;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Validator;
```

<br>

設定一個變數 `$message` 用 __construct 做初始化，把 Message model 可以使用 $tihs->message 來做代替
```php
    /** @var Message 注入的 Message model */

    private $message;

    public function __construct(Message $message)
    {
        $this->message = $message;
    }
```

<br>

由於我們會重複做一些功能，所以我們也把這些功能給拉出來，後續就不需要重覆做一樣的事情
```php
    // 回傳{id}是否存在
    public function is_exists($id){
        return $this->message->where('id',$id)->exists();
    }
    
    // 回傳content是否大於20
    public function max20_content(Request $request){
        $rules = ['content' => 'max:20'];
        $validator = Validator::make($request->all(), $rules);
        return $validator->fails();
    }
```
分別是 `is_exists()` 檢查輸入的 `$id` 是否存在，以及 `max20_content()` 是檢查 request 的 content 是否符合小於20的格式。

<br>

**查詢留言功能**
```php
    // 回傳全部的留言資料
    public function getAllMessage() {
        return  response($this->message->get()->toJson(JSON_PRETTY_PRINT), 200);   
    }

    // 回傳{id}留言資料
    public function getMessage($id){
        if ($this->is_exists($id)) {
            return response($this->message->where('id',$id)->get()->toJson(JSON_PRETTY_PRINT), 200);
        }
        return response()->json(["message" => "找不到訊息"], 404);
    }
```
回傳全部的留言資料 `getAllMessage()`，將 model 的資料表所有的資料 get()，再去做 tojson 並回應 200 的 status code ; 

回傳{id}留言資料 `getMessage($id)` 會先使用到我們的 `is_exist()` 來做檢查，如果有就搜尋這個 id ，並他的資料做  get() ，再去做 tojson 並回應 200 的 status code ; 如果沒有，就回應找不到訊息以及 404 的 status code 。

<br>

**新增留言功能**

```php
    // 回傳是否新增留言成功
    public function createMessage(Request $request) { 
        if ($this->max20_content($request)) {
            return response()->json(["message" => "內容長度超過20個字元"], 400);
        }
            $auth_user = request()->get('auth_user')->first();
            $message = new Message;
            $message->user_id = $auth_user['id'];
            $message->name = $auth_user['username'];
            $message->content = $request->content;
            $message->like = NULL;
            $message->updated_at = NULL;
            $message->save();
            return response()->json(["message" => "新增紀錄成功"], 201);
    }
```
先檢查 request 是否符合 `max20_content ()` ，如果符合就回應內容長度超過20個字元以及 400 的 status code ; 如果不符合就取我們 `TokenAuth` 的 `auth_user` 將他的 $auth_user['id'] 以及 $auth_user['username'] 寫入 $message->user_id 跟 $message->name ，以及其他的資料做儲存，最後回應新增紀錄成功 201 的 status code 。

<br>

**修改留言功能**

```php
    // 回傳是否修改留言成功
    public function updateMessage(Request $request,$id) {
        if ($this->is_exists($id) && Message::where('version',0)->lockForUpdate() ) { 
            if ($this->max20_content($request)) {
                return response()->json(["message" => "內容長度超過20個字元"], 400);
            }
                $auth_user = request()->get('auth_user')->first();
                $message = Message::find($id);
                if ($auth_user['id'] == $message->user_id) {
                    $message->user_id = $auth_user['id'];
                    $message->name = $auth_user['username'];
                    $message->content = is_null($request->content) ? $message->content : $request->content;
                    $message->like = NULL;
                    $message->version = '1';
                    $message->save();
                    return response()->json(["message" => "修改成功"],200);
                }
                return response()->json(["message" => "權限不正確"],403);
        }
            return response()->json(["message" => "找不到訊息"],404);
    }
```
先檢查 `is_exists($id)` 是否存在以及 version 做 lockForUpdate，再檢查 max20_content() 內容有沒有超過，如果都沒有才可以進行修改，修改時，也會檢查是否有 request content ，沒有就照舊使用原始資料，也會依照判斷回傳修改成功 200、權限不正確 403、找不到訊息 404 的 status code 。

<br>

**按讚留言功能**

```php
    // 回傳是否按讚留言成功
    public function likeMessage(Request $request,$id) {
        if ($this->is_exists($id)) {
            $auth_user = request()->get('auth_user')->first();
            $message = Message::find($id);
            $message->like = is_null($message->like) ? $auth_user['id'] : NULL;
            $message->save();
            return response()->json(["message" => "按讚成功"],200);
        }
            return response()->json(["message" => "找不到訊息"],404);
    }
```
由於按讚功能，只需要檢查按讚 id 是否存在即可，故使用 is_exists($id) 來做判斷，有 id 就回應按讚成功 200，沒有就回應找不到訊息 404。

<br>

**刪除功能**

```php
    // 回傳是否刪除留言成功
    public function deleteMessage($id) {
        if ($this->is_exists($id)) {
            $auth_user = request()->get('auth_user')->first();
            $message = Message::find($id);
            if ($auth_user['id'] == $message->user_id) {
                $message->delete();
                return response()->json(["message" => "刪除成功,沒有返回任何內容"],204);
            }
            return response()->json(["message" => "權限不正確"],403);
        } 
            return response()->json(["message" => "找不到訊息"],404);
    }
```
也一樣，先檢查 id 是否存在，再檢查刪除者是否與留言者為同一人。

<br>

到這裡我們講完 `MessageRepository.php` 的內容了，那原本的 Controller 剩下什麼呢 !?

```php
namespace App\Http\Controllers;

use App\Models\Message;
use Illuminate\Http\Request;
use App\Repositories\MessageRepository;
```
一樣基本的命名空間以及引用都沒變。

<br>

```php
   protected $messageRepository;

    public function __construct(MessageRepository $messageRepository)
    {
        $this->messageRepository = $messageRepository;

    }

    // 查詢全部留言
    public function getAll()
    {
        return $this->messageRepository->getAllMessage();  
    }
    // 查詢{id}留言
    public function get($id)
    {
       return $this->messageRepository->getMessage($id);
    }
    // 新增留言
    public function create(Request $request)
    {
        return $this->messageRepository->createMessage($request);
    }
    // 修改{id}留言
    public function update(Request $request,$id)
    {
        return $this->messageRepository->updateMessage($request,$id);
    }  
    // 按讚{id}留言
    public function like(Request $request,$id)
    {
        return $this->messageRepository->likeMessage($request,$id);
    }  
    // 刪除{id}留言
    public function delete($id)
    {
        return $this->messageRepository->deleteMessage($id);
    }  
``` 
我們將它改成這樣，可以讓我們的 Controller 變的更容易閱讀，以及更方便修改。

<br>


### Postman 測試

那我們一樣來看一下 Postman 的測試，這邊只顯示有加入 api_token 以及沒有的差別。


##### 新增留言 - 成功

{{< image src="/images/laravel-advanced/post-api-success.png"  width="600" caption="新增留言 成功" src_s="/images/laravel-advanced/post-api-success.png" src_l="/images/laravel-advanced/post-api-success.png" >}}

<br> 

新增留言成功，因為 **<font color='blue'>api 有輸入 api_token</font>** 會顯示新增紀錄成功以及回應 201 Created

<br>

##### 新增留言 - 失敗

{{< image src="/images/laravel-advanced/post-api-error.png"  width="600" caption="新增留言 失敗" src_s="/images/laravel-advanced/post-api-error.png" src_l="/images/laravel-advanced/post-api-error.png" >}}

<br> 

新增留言失敗，因為 **<font color='red'>api 沒有輸入 api_token</font>** ，所以會顯示用戶需要認證以及回應 401 Unauthorized

<br>

##### 修改留言 - 成功

{{< image src="/images/laravel-advanced/put-api-success"  width="600" caption="修改留言 成功" src_s="/images/laravel-advanced/put-api-success.png" src_l="/images/laravel-advanced/put-api-success.png" >}}

<br> 

修改留言成功，因為 **<font color='blue'>api 有輸入 api_token</font>** ，會顯示修改成功以及回應 200 OK

<br>

##### 修改留言 - 失敗 - 沒有Token

{{< image src="/images/laravel-advanced/put-api-error-1.png"  width="600" caption="修改留言 失敗" src_s="/images/laravel-advanced/put-api-error-1.png" src_l="/images/laravel-advanced/put-api-error-1.png" >}}

<br> 

修改留言失敗，因為 **<font color='red'>api 沒有輸入 api_token</font>** ，會顯示用戶需要認證以及回應 401 Unauthorized

<br>

##### 修改留言 - 失敗 - 權限不足

{{< image src="/images/laravel-advanced/put-api-error-2.png"  width="600" caption="修改留言 失敗" src_s="/images/laravel-advanced/put-api-error-2.png" src_l="/images/laravel-advanced/put-api-error-2.png" >}}

<br> 

修改留言失敗，雖然 **<font color='red'>有輸入正確的 token ，但不是當初的留言者</font>** ，會顯示權限不正確以及回應 403 Forbidden

<br>

##### 按讚留言 - 成功

{{< image src="/images/laravel-advanced/patch-api-success"  width="600" caption="修改留言 成功" src_s="/images/laravel-advanced/patch-api-success.png" src_l="/images/laravel-advanced/patch-api-success.png" >}}

<br> 

按讚留言成功，因為 **<font color='blue'>api 有輸入 api_token</font>** ，會顯示按讚成功以及回應 200 OK

<br>

##### 按讚留言 - 失敗 - 沒有Token

{{< image src="/images/laravel-advanced/patch-api-error.png"  width="600" caption="修改留言 失敗" src_s="/images/laravel-advanced/patch-api-error.png" src_l="/images/laravel-advanced/patch-api-error.png" >}}

<br> 

按讚留言失敗，因為 **<font color='red'>api 沒有輸入 api_token</font>** ，會顯示用戶需要認證以及回應 401 Unauthorized

<br>

##### 刪除留言 - 成功

{{< image src="/images/laravel-advanced/delete-api-success.png"  width="600" caption="刪除留言 成功" src_s="/images/laravel-advanced/delete-api-success.png" src_l="/images/laravel-advanced/delete-api-success.png" >}}

<br> 

刪除留言成功，因為 **<font color='blue'>api 有輸入 api_token</font>** ，不會顯示訊息但會回應 204 No Content

<br>

##### 刪除留言 - 失敗 - 沒有Token

{{< image src="/images/laravel-advanced/delete-api-error-1.png"  width="600" caption="刪除留言 失敗" src_s="/images/laravel-advanced/delete-api-error-1.png" src_l="/images/laravel-advanced/delete-api-error-1.png" >}}

<br> 

刪除留言失敗，因為 **<font color='red'>api 沒有輸入 api_token</font>** ，會顯示用戶需要認證以及回應 401 Unauthorized

<br>

##### 刪除留言 - 失敗 - 權限不足

{{< image src="/images/laravel-advanced/delete-api-error-2.png"  width="600" caption="刪除留言 失敗" src_s="/images/laravel-advanced/delete-api-error-2.png" src_l="/images/laravel-advanced/delete-api-error-2.png" >}}

<br> 

刪除留言失敗，雖然 **<font color='red'>有輸入正確的 token ，但不是當初的留言者</font>** ，會顯示權限不正確以及回應 403 Forbidden



<br>

## 參考資料
[Laravel Auth 自定義user 模型目錄結構](https://www.itread01.com/content/1545012913.html)

[user ( Model )](https://ithelp.ithome.com.tw/articles/10220381)

[Laravel Form Request Validation](https://laravel.com/docs/5.4/validation#form-request-validation)

[laravel Validation 驗證格式](https://ithelp.ithome.com.tw/articles/10250237)