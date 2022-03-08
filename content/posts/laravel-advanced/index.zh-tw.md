---
weight: 4
title: "Laravel 進階 (內建會員系統、驗證 RESTful API 是否登入、使用 repository設計模式)"
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

tags: ["PHP", "框架", "Laravel", "RESTful API", "實作","介紹"]
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
 
 因為我們修改 name、email 改成 username 這個欄位，所以我們也要修改一下 views 顯示的畫面，由於是簡單的 HTML 這邊就不再多描述，有問題可以下方留言><
 
 <br>
 
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

## 參考資料
[Laravel Auth 自定義user 模型目錄結構](https://www.itread01.com/content/1545012913.html)

[user ( Model )](https://ithelp.ithome.com.tw/articles/10220381)