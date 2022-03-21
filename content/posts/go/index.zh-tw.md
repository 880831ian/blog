---
weight: 4
title: "Go 介紹"
date: 2022-03-21T11:33:25+08:00
lastmod: 2022-03-21T11:33:25+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Go", "實作", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

## 什麼是 Go ?

Go 全名是 go programming language 又被稱為 Golang，是因為 go 這個詞太廣泛，不易搜尋，所以習慣叫 Golang。

Go 是由 Google 開發並維護的編譯程式語言，支援垃圾回收與併發，由於創辦人之一也是 C 語言前身 B 語言的作者，所以 Go 也繼承了許多 C 的風格
其特點有以下幾點：

* 開源：Go 為開放原始碼，可以直接到[專案的 GitHub](https://github.com/golang/go) 來查看 Go 底層的原始碼。
* 靜態型別：因爲靜態型別的特性，可以在編譯期間就進行完整的型別檢查，來找出大部分的型別錯誤。
* 編譯速度：因為 Go 語言先天優勢是架構設計非常單純，並不像物件導向語言龐大，取在編譯時不用相依其他的 library，因此讓他有更好的執行效率。
* 語法簡潔：Go 關鍵字不多，不到30幾個，因為其關鍵字不少也與 C 的關鍵字重複，學習更容易上手。
* 原生支援併發：在目前電腦都是多顆核心的時代下， Go 在先天設計上就支援了併發，也就是所謂的 `goroutine` ，這是 Go 最大特色之一，thread 的使用是非常消耗系統資源的，當使用越多管理上也越困難，而 goroutine 有輕巧低消耗資源的特性，而這個優點在於系統支援的消耗也會比較少。

<br>

{{< admonition tip "靜態型別/動態型別" ture >}}
靜態型別的意思是指當宣告一個變數時，你必須同時宣告此變數所存放的資料型態為何

```
var age int // int
var name string // string
```
<br>

動態型別指的是程式執行時，系統才可以看見的型別，什麼型別都可以

```
var i interface {}
i = 18
i = "Golang 程式設計"
```

{{< /admonition >}}

{{< admonition tip "編譯式語言/直譯式語言" ture >}}

* 編譯式：當我們寫完程式時，我們需要將程式 compile (編譯) 成電腦看得懂的程式，再將程式拿去執行。 
* 直譯式：當我們寫完程式時，直接使用直譯器一行一行翻譯成電腦語言並執行。

比較：
1. 編譯式執行效率較佳
2. 直譯式相對容易 Debug
3. 編譯式語言編譯完後，可以直接在各類 OS 系統中執行，因為編譯完的程式，就是電腦看得懂的。
{{< /admonition >}}

那我們大致了解 Go 後就來安裝 Go吧！

## 安裝 Go

本次作業系統為 macOS，所以後續都以 macOS 為主，如果是使用其他的作業系統，可以直接到[官網來下載](https://go.dev/doc/install)。

macOS 可以用 brew 等工具來下載，但這次我使用官網直接下載 pkg 來安裝。

{{< image src="/images/go/download.png"  width="700" caption="Go 官網下載位置 " src_s="/images/go/download.png" src_l="/images/go/download.png" >}}

下載完後，使用 `go version` 來檢查是否安裝成功

```sh
$ go version
go version go1.18 darwin/amd64
```

<br>

接著我們來看一下他的環境變數，使用 `go env`，來查看

```sh
$ go env
GO111MODULE=""
GOARCH="amd64"
GOBIN=""
GOCACHE="/Users/ian_zhuang/Library/Caches/go-build"
GOENV="/Users/ian_zhuang/Library/Application Support/go/env"
GOEXE=""
GOEXPERIMENT=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GOINSECURE=""
GOMODCACHE="/Users/ian_zhuang/go/pkg/mod"
GONOPROXY=""
GONOSUMDB=""
GOOS="darwin"
GOPATH="/Users/ian_zhuang/go"
GOPRIVATE=""
GOPROXY="https://proxy.golang.org,direct"
GOROOT="/usr/local/go"
```
這邊簡單的列出來，比較重要的是

* GOPATH：他是有關管理程式碼和套件執行檔到地方，網路上有人說一開始是空白，但我安裝後已經幫我帶入好。

說一下這個路徑的內容，我在依照設定的路徑，像我是在 Users/使用者/go，在 go 底下新增三個資料夾：

* src：主要放置專案的地方
* pkg：套件主要儲存的資料夾
* bin：存放編譯好的執行檔案

<br>

## 第一隻 Go 程式

我們依照官網的示範教學，先建立一個資料夾，來放我們第一個 Go 程式 "印出 Hello world"

```
mkdir hello
cd hello
```

<br>

當我們程式要導入其他模組 (module) 時，可以通過  go.mod 來管理這些模組，那什麼是 go.mod？

go.mod 是用來定義 module 的文件，用來標示此 module 的 module path、使用的 go 版本、管理 module 的依賴。

我們在添加時，不需要去修改 mod 檔案，只需要使用 `go mod init [module-path]` 就可以生成 go.mod 檔案。

如果後續有用到外部依賴的 module ，可以使用 `go get` 來更新 go.mod 中依賴的 module。 

當我們在編輯 `go` 檔案，導入了尚未加入 mod 的 module ，可以使用 `go mod tidy` 來自動加入 go 裡面尚未加入的 module ，或刪除不需要的 module。
```
$ go mod init example/hello
go: creating new go.mod: module example/hello

$cat go.mod
module example/hello

go 1.18
```

<br>

最後來新增一個 `hello.go` 檔案來放我們的程式

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

* Package：package 主要分成兩種，一個是可執行 (executable)，另一個則是可重複使用的 (reusable)，而 `package main` 就是可執行的檔案，像我們上面這個有包含 package main 的檔案，在編譯時，就會產生一個 hello 的執行檔，電腦就是依照此檔案執行的。
* Import：當我們寫程式時，一定會引入其他人寫的套件。而 Go 語言的標準函式庫為開發團隊先寫好，提供一些常用的功能，當然也可以使用其他第三方套件，還滿足內建以及標準函式庫的不足。
* Main Function：每個 Go 語言的專案基本上都會有一個主程式，主程式裡的程式通常都為最核心的部分。

<br>

解說一下這個程式碼主要運作， import fmt 就是我們使用 go 內建標準的 package `fmt` ，然後再 main 這邊透過它來印出 Hello World!

<br>

最後使用 `go run hello.go` 來將此程式執行

```sh
$ go run hello.go
Hello, World!
```
就可以看到我們程式將 Hello,World! 給印出來拉！

接下來要簡單介紹一下常用的另外3個指令，分別是 `go build`、`go install`、`go clean`：

<br>

`go build`：我們剛剛使用 run 來執行，但還記得我們前面說 Go 是編譯式程式，所以我們可以將程式用 `go build` 來編譯成電腦看得懂的執行檔歐 ~

```
$ ls
go.mod   hello.go
$ go build
go.mod   hello    hello.go
```
多的這個 hello 就是編譯後的檔案。

<br>

`go install`：如果編譯沒有錯誤，一樣跟 build 會產生執行檔，不同的是，會將執行檔，產生於 $GOPATH/bin 內。

```
$ ls /Users/ian_zhuang/go/bin
dlv          go-outline   gomodifytags goplay       gopls        gotests      impl         staticcheck

$ go install

$ ls /Users/ian_zhuang/go/bin 
dlv          go-outline   gomodifytags goplay       gopls        gotests      hello        impl         staticcheck
```

<br>

`go clean`：執行後會將 build 產生的檔案都刪除 (install 的不會)

```
$  ls
go.mod   hello    hello.go

$ go clean

$ ls 
go.mod   hello.go
```

<br>

## Go 變數型態

我們在學習 Go 語言之前，要先了解一下基本的變數型態，可以簡單分為 字串、整數、浮點數、布林值

### 字串

在 Go 語言中，字串必須用雙引號給匡起來，也可以使用反引號來宣告。用雙引號刮起來的字串不能包含換行，但可以包含跳脫字元，例如 `\n` 、`\t` 。由於反引號內包含的是原始字串，可以跨越多行，所以跳脫符號在原始字串中沒有任何含義。

```go
func main() {
   var name = "ian"
   fmt.Printf("資料型態 name : %v(%T)\n", name,name)
   var address = `台中市太平區`
   fmt.Printf("資料型態 address : %v(%T)\n", address,address)
}
```      

```sh
$ go run hello.go
資料型態 name : ian(string)
資料型態 address : 台中市太平區(string)
```

<br>

### 整數

整數用於儲存整數。 Go 具有多種大小不一的內建整數型別，用來儲存有號數和無號數。

* 有號數

| 型別 | 大小 | 範圍 | 
| :---: | :---: | :---: |
| int | 取決平台 | 取決平台 |
| int 8 | 8 bits | -128 到 127 |
| int 16 | 16 bits | -2^15 到 2^15-1 |
| int 32 | 32 bits | -2^31 到 2^31-1 | 
| int 64 | 64 bits | -2^63 到 -2^63-1 |

* 無號數

| 型別 | 大小 | 範圍 | 
| :---: | :---: | :---: |
| uint | 取決平台 | 取決平台 |
| uint 8 | 8 bits | 0 到 255 |
| uint 16 | 16 bits | 0 到 2^16-1 |
| uint 32 | 32 bits | 0 到 2^32-1 | 
| uint 64 | 64 bits | 0 到 -2^64-1 |

`int` 、 `uint` 的型別大小取決於平台。在 32 位元系統上，它大小為 32 位元，64 位元系統上，它的大小為 64 位元。

使用整數值時，除非確定會用到的大小跟範圍才使用有號數及無號數，否則應該都使用 `int` 資料型別。

```go
func main() {
	var myInt8 int8 = 97
	var myInt = 1200
	var myUint uint = 500
	var myOctalNumber = 034
	var myHexNumber = 0xFF  
	fmt.Printf("%d, %d, %d, %#o, %#x\n", myInt8, myInt, myUint, myOctalNumber, myHexNumber)
}
```

```sh
$ go run hello.go
97, 1200, 500, 034, 0xff
```

在 Go 中，也可以使用前綴 `0` 來宣告八進制數字，或是使用 `0x` 來宣告十六進制數字。

<br>

### 浮點數

浮點數型別用於儲存袋小數部分的數字。Go 有兩種浮點數型別：

* float32：在記憶體中佔用32位元，並以單精度浮點數格式儲存。
* float64：在記憶體中佔用64位元，並以雙精度浮點數格式儲存。

浮點數的預設是 `float64`，除非初始化有為浮點數變數指定型別，否則編譯器將判定為 `float64`。

```go
func main() {
	var a = 245.4664
	var b float32 = 1452.34
	fmt.Printf("%f(%T)\n%f(%T)\n", a,a,b,b)
}
```

```sh
$ go run hello.go
245.466400(float64)
1452.339966(float32)
```

<br>

### 布林值

Go 提供了一種稱為 `bool` 的資料型別來儲存布林值。它有兩個可能的值：`true` 和 `false`。

```go
func main() {
	var a = true
	var b bool = false
	fmt.Printf("%v(%T)\n%v(%T)\n", a,a,b,b)
}
```

```sh
$  go run hello.go 
true(bool)
false(bool)
```

<br>

布林型別也可以使用運算子 `&&` (與,and)、`||` (和,or)、`!` (否定)

```go
func main() {
	var a = 4 <= 7 
	var b = 10 != 10
	var c = 10 > 20 && 5 == 5 
	var d = 2*2 == 4 || 10/3 == 3
	fmt.Printf("%v\n%v\n%v\n%v\n", a,b,c,d)
}
```

```sh
$  go run hello.go 
true
false
false
true
```

<br>

### 數字型別的運算

Go 提供了多種用於數字、浮點數型別執行運算的運算子

* 算術運算子：`+`、`-`、`*`、`/`、`%`
* 比較運算子：`==`、`!=`、`<`、`>`、`<=`、`>=`
* 位元運算子：`&`、 `|`、 `^`、 `<<`、`>>`
* 遞增和遞減運算子：`++`、`--`
* 賦值運算子：`+=`、`-=`、`*=`、`/=`、`%=`、`<<=`、`>>=`、`&=`、`|=`、`^=`

```go
import (
	"fmt"
	"math"
)
 
func main() {
	var a, b = 4, 5
	var res1 = (a + b) * (a + b) / 2 
	a++ 
	b += 10 
	var res2 = a ^ b 
	var r = 3.5
	var res3 = math.Pi * r * r 
	fmt.Printf("res1 : %v, res2 : %v, res3 : %v\n", res1, res2, res3)
}
```
<br>

### 型別轉換

Go 有一個強型別系統，它不允許混合型別。舉例來說，你不能把 `int` 變數加到 `float64` 變數中，也不能將 `int` 變數加到 `int64` 變數中。

```go
func main() {
	var a int64 = 4
	var b int = int(a)
	var c int = 500
	fmt.Println(a, b,a+c)
}
```

```sh
$ go run hello.go 
# command-line-arguments
./hello.go:11:19: invalid operation: a + c (mismatched types int64 and int)
```
就會跳出錯誤說明別不同，無法直接做運算，那要怎麼辦呢！？

<br>

```go
func main() {
	var a int64 = 4
	var b int = int(a)
	var c int = 500
	fmt.Println(a, b,int(a)+c)
}
```

```sh
$ go run hello.go 
4 4 504
```
一般將值 `v` 轉換為型別 `T` 的語法是 `T(V)` 。

<br>

## 參考資料

[Go 官網](https://go.dev/doc/tutorial/getting-started)

[golang後端入門分享](https://ithelp.ithome.com.tw/users/20137500/ironman/4184)

[30天就Go(3)：操作指令及Hello World!](https://ithelp.ithome.com.tw/articles/10186546)

[Golang 基本型別、運算子和型別轉換](https://calvertyang.github.io/2019/11/05/golang-basic-types-operators-type-conversion/)