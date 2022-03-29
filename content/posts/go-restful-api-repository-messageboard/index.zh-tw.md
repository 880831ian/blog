---
weight: 4
title: "用 Go 寫一個 Repository Restful API 的留言板 (gin、gorm 套件)"
date: 2022-03-27T21:27:19+08:00
lastmod: 2022-03-27T21:27:19+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Go", "RESTful API", "Mysql", "Repository", "實作", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

本文章是使用 Go 來寫一個 Repository Restful API 的留言板，並且會使用 gin 以及 gorm (使用 Mysql)套件。

建議可以先觀看 [Go 介紹](https://pin-yi.me/go/) 文章來簡單學習 Go 語言。

版本資訊

* macOS：11.6
* Go：go version go1.18 darwin/amd64
* Mysql：mysql  Ver 8.0.28 for macos11.6 on x86_64 (Homebrew)
<br>

## 實作

### 檔案結構

```
.
├── config
│   ├── config.go
│   └── connect.yaml
├── controller
│   └── Controller.go
├── go.mod
├── go.sum
├── main.go
├── model
│   └── Model.go
├── repository
│   └── Repository.go
└── routes
    └── Routers.go
```

<br>

我們來說明一下上面的資料夾個別功能與作用

* config：放置設定檔案，並讀取設定檔與連線資料庫。
* controller：商用邏輯控制。
* model：定義資料表資料型態。
* repository：處理與資料庫進行交握。
* routes：設定網站網址路由。

### go.mod

一開始我們創好資料夾後，要先來設定 go.mod 的 module

```sh
$ go mod init message
```

* go.mod 檔案
```sh
module message

go 1.18
```

<br>

接著使用 `go get` 來引入 `gin`、`gorm`、`mysql`、`yaml` 套件
```sh
$ go get -u github.com/gin-gonic/gin
$ go get -u gorm.io/gorm
$ go get -u gorm.io/driver/mysql
$ go get -u gopkg.in/yaml.v2
```
可以在查看一下 go.mod 檔案是否多了很多 indirect

<br>

### main.go

```go
package main

import (
	"message/config"
	"message/model"
	"message/routes"
	"fmt"
)

func main() {
	//連線資料庫
	if err := config.InitMySql() ;err != nil {
		panic(err)
	}
	//連結模型
	config.Sql.AutoMigrate(&model.Message{})
	//註冊路由
	r := routes.SetRouter()
	//啟動埠為8081的專案
	fmt.Println("開啟127.0.0.0.1:8081...")
	r.Run("127.0.0.1:8081")
}
```
引入我們 Repository 架構，將 config、model、routes 導入，先測試是否可以連線資料庫，使用 `AutoMigrate` 來新增資料表(如果沒有才新增)，註冊網址路由，最後啟動專案，我們將 Port 設定成 8081。

<br>

### config

我們剛剛有引入 `yaml` 套件，因為我們設定檔案會使用 yaml 來編輯

* connect.yaml
```yaml
host: 127.0.0.1
username: root
password: "密碼"
dbname: "資料庫名稱"
port: 3306
```
我們把 mysql 連線的資訊寫在此處。

<br>

* config.go (下面為一個檔案，但長度有點長，分開說明)

```go
package config

import (
	"io/ioutil"
	"fmt"
	"gopkg.in/yaml.v2"
	"gorm.io/gorm"
	"gorm.io/driver/mysql"
)
```
import 會使用到的套件。

<br>

```go
var Sql *gorm.DB

type conf struct {
	Host     string `yaml:"host"`
	UserName string `yaml:"username"`
	Password string `yaml:"password"`
	DbName   string `yaml:"dbname"`
	Port     string `yaml:"port"`
}

func (c *conf) getConf() *conf {
	//讀取config/connect.yaml檔案
	yamlFile, err := ioutil.ReadFile("config/connect.yaml")
	//若出現錯誤，列印錯誤訊息
	if err != nil {
		fmt.Println(err.Error())
	}
	//將讀取的字串轉換成結構體conf
	err = yaml.Unmarshal(yamlFile, c)
	if err != nil {
		fmt.Println(err.Error())
	}
	return c
}
```
設定資料庫連線的 conf ，設定連線的 config 讀取 yaml 檔案。

<br>

```go
//初始化連線資料庫
func InitMySql() (err error) {
	var c conf
	//獲取yaml配置引數
	conf := c.getConf()
	//將yaml配置引數拼接成連線資料庫的url
	dsn := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8mb4&parseTime=True&loc=Local",
		conf.UserName,
		conf.Password,
		conf.Host,
		conf.Port,
		conf.DbName,
	)
	//連線資料庫
	Sql, err = gorm.Open(mysql.New(mysql.Config{DSN: dsn}), &gorm.Config{})
	return
}
```
初始化資料庫，會把剛剛讀取 yaml 的 conf  串接成可以連接資料庫的 url ，最後連線資料庫。 

<br>

### Routers.go

```go
package routes

import (
	"message/controller"
	"github.com/gin-gonic/gin"
)

func SetRouter() *gin.Engine {
	//顯示 debug 模式
	gin.SetMode(gin.ReleaseMode)
	r := gin.Default()

	v1 := r.Group("api/v1")
	{
		//新增留言
		v1.POST("/message", controller.Create)
		//查詢全部留言
		v1.GET("/message", controller.GetAll)
		//查詢 {id} 留言
		v1.GET("/message/:id", controller.Get)
		//修改 {id} 留言
		v1.PATCH("/message/:id", controller.Update)
		//刪除 {id} 留言
		v1.DELETE("/message/:id", controller.Delete)
	}
	return r
}
```
設定路由，版本 v1 網址是 `api/v1` ，分別是新增留言、查詢全部留言、查詢 {id} 留言、修改 {id} 留言、刪除 {id} 留言，連接到不同的 `controller` function 。

<br>

### Model.go

```go
package model

import 	"gorm.io/gorm"

func (Message) TableName() string {
	return "message"
}

type Message struct {
	Id        int    `gorm:"primary_key,type:INT;not null;AUTO_INCREMENT"`
	User_Id   int    `json:"User_Id"  binding:"required"`
	Content   string `json:"Content"  binding:"required"`
	Version   int    `gorm:"default:0"`
	// 包含 CreatedAt 和 UpdatedAt 和 DeletedAt 欄位
    gorm.Model
}
```
設定資料表的結構，使用 gorm.Model 預設裡面會包含 CreatedAt 和 UpdatedAt 和 DeletedAt 欄位。

<br>

### Controller.go 

**(下面為一個檔案，但長度有點長，分開說明)**

```go
package controller

import (
	"net/http"
	"message/model"
	"message/repository"
	"github.com/gin-gonic/gin"
)
```
引入套件

```go
func GetAll(c *gin.Context) {
	message,err := repository.GetAllMessage()

	if err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}
	c.JSON(http.StatusOK, gin.H{"message": message})
}

func Get(c *gin.Context) {
	var message model.Message

	if err := repository.GetMessage(&message, c.Param("id")); err != nil {
		c.JSON(http.StatusNotFound, gin.H{"error": "找不到留言"})
		return
	}
	c.JSON(http.StatusOK, gin.H{"message": message})
}
```


<br>

## 參考資料

[基於Gin+Gorm框架搭建MVC模式的Go語言後端系統](https://iter01.com/609571.html)

