---
weight: 4
title: "Markdown 基本語法"
date: 2019-12-01T21:57:40+08:00
lastmod: 2020-01-01T16:45:40+08:00
draft: false
author: "FeelIt"
authorLink: "https://feelit.khusika.com"
description: "這篇文章展示了基本的 Markdown 語法和格式."
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Markdown", "HTML"]
categories: ["documentation"]

lightgallery: true
hiddenFromHomePage: true

---

這篇文章提供了可以在 Hugo 的文章中使用的基本 Markdown 語法示例.

<!--more-->

{{< admonition >}}
這篇文章借鑒了一篇很棒的[來自 Grav 的文章](http://learn.getgrav.org/content/markdown).

如果你想了解 **FeelIt** 主題的擴展 Markdown 語法, 請閱讀[擴展 Markdown 語法頁面](../theme-documentation-content#extended-markdown-syntax).
{{< /admonition >}}

事實上, 編寫 Web 內容很麻煩. [WYSIWYG]^(所見即所得) 編輯器幫助減輕了這一任務. 但通常會導致代碼太糟, 或更糟糕的是, 網頁也會很醜.

沒有通常伴隨的所有覆雜和醜陋的問題, **Markdown** 是一種更好的生成 **HTML** 內容的方式.

一些主要好處是:

1. Markdown 簡單易學, 幾乎沒有多余的字符, 因此編寫內容也更快.
2. 用 Markdown 書寫時出錯的機會更少.
3. 可以產生有效的 XHTML 輸出.
4. 將內容和視覺顯示保持分開, 這樣就不會打亂網站的外觀.
5. 可以在你喜歡的任何文本編輯器或 Markdown 應用程序中編寫內容.
6. Markdown 使用起來很有趣!

John Gruber, Markdown 的作者如是說:

> Markdown 格式的首要設計目標是更具可讀性.
> 最初的想法是 Markdown 格式的文檔應當以純文本形式發布,
> 而不會看起來像被標簽或格式說明所標記.
> 雖然 Markdown 的語法受到幾種現有的文本到 HTML 轉換工具的影響,
> 但 Markdown 語法的最大靈感來源是純文本電子郵件的格式.
>
> {{< style "text-align: right;" >}}-- _John Gruber_{{< /style >}}

話不多說, 我們來回顧一下 Markdown 的主要語法以及生成的 HTML 樣式!

{{< admonition tip >}}
:(far fa-bookmark fa-fw): 將此頁保存為書簽，以備將來參考!
{{< /admonition >}}

## 1 標題

從 `h2` 到 `h6` 的標題在每個級別上都加上一個 `＃`:

```markdown
## h2 標題
### h3 標題
#### h4 標題
##### h5 標題
###### h6 標題
```

輸出的 HTML 看起來像這樣:

```html
<h2>h2 標題</h2>
<h3>h3 標題</h3>
<h4>h4 標題</h4>
<h5>h5 標題</h5>
<h6>h6 標題</h6>
```

{{< admonition note "標題 ID" >}}
要添加自定義標題 ID, 請在與標題相同的行中將自定義 ID 放在花括號中:

```markdown
### 一個很棒的標題 {#custom-id}
```

輸出的 HTML 看起來像這樣:

```html
<h3 id="custom-id">一個很棒的標題</h3>
```
{{< /admonition >}}

## 2 注釋

注釋是和 HTML 兼容的：

```html
<!--
這是一段注釋
-->
```

**不能**看到以下的注釋:

<!--
這是一段注釋
-->

## 3 水平線

HTML 中的 `<hr>` 標簽是用來在段落元素之間創建一個 "專題間隔" 的.
使用 Markdown, 你可以用以下方式創建一個 `<hr>` 標簽:

* `___`: 三個連續的下劃線
* `---`: 三個連續的破折號
* `***`: 三個連續的星號

呈現的輸出效果如下:

___
---
***

## 4 段落

按照純文本的方式書寫段落, 純文本在呈現的 HTML 中將用 `<p>`/`</p>` 標簽包裹.

如下段落:

```markdown
Lorem ipsum dolor sit amet, graecis denique ei vel, at duo primis mandamus. Et legere ocurreret pri,
animal tacimates complectitur ad cum. Cu eum inermis inimicus efficiendi. Labore officiis his ex,
soluta officiis concludaturque ei qui, vide sensibus vim ad.
```

輸出的 HTML 看起來像這樣:

```html
<p>Lorem ipsum dolor sit amet, graecis denique ei vel, at duo primis mandamus. Et legere ocurreret pri, animal tacimates complectitur ad cum. Cu eum inermis inimicus efficiendi. Labore officiis his ex, soluta officiis concludaturque ei qui, vide sensibus vim ad.</p>
```

可以使用一個空白行進行**換行**.

## 5 內聯 HTML 元素

如果你需要某個 HTML 標簽 (帶有一個類), 則可以簡單地像這樣使用:

```html
Markdown 格式的段落.

<div class="class">
    這是 <b>HTML</b>
</div>

Markdown 格式的段落.
```

## 6 強調

### 加粗

用於強調帶有較粗字體的文本片段.

以下文本片段會被 **渲染為粗體**.

```markdown
**渲染為粗體**
__渲染為粗體__
```

輸出的 HTML 看起來像這樣:

```html
<strong>渲染為粗體</strong>
```

### 斜體

用於強調帶有斜體的文本片段.

以下文本片段被 _渲染為斜體_.

```markdown
*渲染為斜體*
_渲染為斜體_
```

輸出的 HTML 看起來像這樣:

```html
<em>渲染為斜體</em>
```

### 刪除線

按照 [[GFM]^(GitHub flavored Markdown)](https://github.github.com/gfm/) 你可以使用刪除線.

```markdown
~~這段文本帶有刪除線.~~
```

呈現的輸出效果如下:

~~這段文本帶有刪除線.~~

輸出的 HTML 看起來像這樣:

```html
<del>這段文本帶有刪除線.</del>
```

### 組合

加粗, 斜體, 和刪除線可以 組合使用.

```markdown
***加粗和斜體***
~~**刪除線和加粗**~~
~~*刪除線和斜體*~~
~~***加粗, 斜體和刪除線***~~
```

呈現的輸出效果如下:

***加粗和斜體***

~~**刪除線和加粗**~~

~~*刪除線和斜體*~~

~~***加粗, 斜體和刪除線***~~

輸出的 HTML 看起來像這樣:

```html
<em><strong>加粗和斜體</strong></em>
<del><strong>刪除線和加粗</strong></del>
<del><em>刪除線和斜體</em></del>
<del><em><strong>加粗, 斜體和刪除線</strong></em></del>
```

## 7 引用

用於在文檔中引用其他來源的內容塊.

在要引用的任何文本之前添加 `>`:

```markdown
> **Fusion Drive** combines a hard drive with a flash storage (solid-state drive) and presents it as a single logical volume with the space of both drives combined.
```

呈現的輸出效果如下:

> **Fusion Drive** combines a hard drive with a flash storage (solid-state drive) and presents it as a single logical volume with the space of both drives combined.

輸出的 HTML 看起來像這樣:

```html
<blockquote>
  <p>
    <strong>Fusion Drive</strong> combines a hard drive with a flash storage (solid-state drive) and presents it as a single logical volume with the space of both drives combined.
  </p>
</blockquote>
```

引用也可以嵌套:

```markdown
> Donec massa lacus, ultricies a ullamcorper in, fermentum sed augue.
Nunc augue augue, aliquam non hendrerit ac, commodo vel nisi.
>> Sed adipiscing elit vitae augue consectetur a gravida nunc vehicula. Donec auctor
odio non est accumsan facilisis. Aliquam id turpis in dolor tincidunt mollis ac eu diam.
```

呈現的輸出效果如下:

> Donec massa lacus, ultricies a ullamcorper in, fermentum sed augue.
Nunc augue augue, aliquam non hendrerit ac, commodo vel nisi.
>> Sed adipiscing elit vitae augue consectetur a gravida nunc vehicula. Donec auctor
odio non est accumsan facilisis. Aliquam id turpis in dolor tincidunt mollis ac eu diam.

## 8 列表

### 無序列表

一系列項的列表, 其中項的順序沒有明顯關系.

你可以使用以下任何符號來表示無序列表中的項:

```markdown
* 一項內容
- 一項內容
+ 一項內容
```

例如:

```markdown
* Lorem ipsum dolor sit amet
* Consectetur adipiscing elit
* Integer molestie lorem at massa
* Facilisis in pretium nisl aliquet
* Nulla volutpat aliquam velit
  * Phasellus iaculis neque
  * Purus sodales ultricies
  * Vestibulum laoreet porttitor sem
  * Ac tristique libero volutpat at
* Faucibus porta lacus fringilla vel
* Aenean sit amet erat nunc
* Eget porttitor lorem
```

呈現的輸出效果如下:

* Lorem ipsum dolor sit amet
* Consectetur adipiscing elit
* Integer molestie lorem at massa
* Facilisis in pretium nisl aliquet
* Nulla volutpat aliquam velit
  * Phasellus iaculis neque
  * Purus sodales ultricies
  * Vestibulum laoreet porttitor sem
  * Ac tristique libero volutpat at
* Faucibus porta lacus fringilla vel
* Aenean sit amet erat nunc
* Eget porttitor lorem

輸出的 HTML 看起來像這樣:

```html
<ul>
  <li>Lorem ipsum dolor sit amet</li>
  <li>Consectetur adipiscing elit</li>
  <li>Integer molestie lorem at massa</li>
  <li>Facilisis in pretium nisl aliquet</li>
  <li>Nulla volutpat aliquam velit
    <ul>
      <li>Phasellus iaculis neque</li>
      <li>Purus sodales ultricies</li>
      <li>Vestibulum laoreet porttitor sem</li>
      <li>Ac tristique libero volutpat at</li>
    </ul>
  </li>
  <li>Faucibus porta lacus fringilla vel</li>
  <li>Aenean sit amet erat nunc</li>
  <li>Eget porttitor lorem</li>
</ul>
```

### 有序列表

一系列項的列表, 其中項的順序確實很重要.

```markdown
1. Lorem ipsum dolor sit amet
2. Consectetur adipiscing elit
3. Integer molestie lorem at massa
4. Facilisis in pretium nisl aliquet
5. Nulla volutpat aliquam velit
6. Faucibus porta lacus fringilla vel
7. Aenean sit amet erat nunc
8. Eget porttitor lorem
```

呈現的輸出效果如下:

1. Lorem ipsum dolor sit amet
2. Consectetur adipiscing elit
3. Integer molestie lorem at massa
4. Facilisis in pretium nisl aliquet
5. Nulla volutpat aliquam velit
6. Faucibus porta lacus fringilla vel
7. Aenean sit amet erat nunc
8. Eget porttitor lorem

輸出的 HTML 看起來像這樣:

```html
<ol>
  <li>Lorem ipsum dolor sit amet</li>
  <li>Consectetur adipiscing elit</li>
  <li>Integer molestie lorem at massa</li>
  <li>Facilisis in pretium nisl aliquet</li>
  <li>Nulla volutpat aliquam velit</li>
  <li>Faucibus porta lacus fringilla vel</li>
  <li>Aenean sit amet erat nunc</li>
  <li>Eget porttitor lorem</li>
</ol>
```

{{< admonition tip >}}
如果你對每一項使用 `1.`, Markdown 將自動為每一項編號. 例如:

```markdown
1. Lorem ipsum dolor sit amet
1. Consectetur adipiscing elit
1. Integer molestie lorem at massa
1. Facilisis in pretium nisl aliquet
1. Nulla volutpat aliquam velit
1. Faucibus porta lacus fringilla vel
1. Aenean sit amet erat nunc
1. Eget porttitor lorem
```

呈現的輸出效果如下:

1. Lorem ipsum dolor sit amet
1. Consectetur adipiscing elit
1. Integer molestie lorem at massa
1. Facilisis in pretium nisl aliquet
1. Nulla volutpat aliquam velit
1. Faucibus porta lacus fringilla vel
1. Aenean sit amet erat nunc
1. Eget porttitor lorem
{{< /admonition >}}

### 任務列表

任務列表使你可以創建帶有覆選框的列表.
要創建任務列表, 請在任務列表項之前添加破折號 (`-`) 和帶有空格的方括號 (`[ ]`). 要選擇一個覆選框，請在方括號之間添加 x (`[x]`).

```markdown
- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media
```

呈現的輸出效果如下:

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

## 9 代碼

### 行內代碼

用 <code>`</code> 包裝行內代碼段.

```markdown
在這個例子中, `<section></section>` 會被包裹成 **代碼**.
```

呈現的輸出效果如下:

在這個例子中, `<section></section>` 會被包裹成 **代碼**.

輸出的 HTML 看起來像這樣:

```html
<p>
  在這個例子中, <code>&lt;section&gt;&lt;/section&gt;</code> 會被包裹成 <strong>代碼</strong>.
</p>
```

### 縮進代碼

將幾行代碼縮進至少四個空格，例如:

```markdown
    // Some comments
    line 1 of code
    line 2 of code
    line 3 of code
```

呈現的輸出效果如下:

    // Some comments
    line 1 of code
    line 2 of code
    line 3 of code

輸出的 HTML 看起來像這樣:

```html
<pre>
  <code>
    // Some comments
    line 1 of code
    line 2 of code
    line 3 of code
  </code>
</pre>
```

### 圍欄代碼塊

使用 "圍欄" <code>```</code> 來生成一段帶有語言屬性的代碼塊.

{{< highlight markdown >}}
```markdown
Sample text here...
```
{{< / highlight >}}

輸出的 HTML 看起來像這樣:

```html
<pre language-html>
  <code>Sample text here...</code>
</pre>
```

### 語法高亮

[GFM]^(GitHub Flavored Markdown) 也支持語法高亮.

要激活它，只需在第一個代碼 "圍欄" 之後直接添加你要使用的語言的文件擴展名,
<code>```js</code>, 語法高亮顯示將自動應用於渲染的 HTML 中.

例如, 在以下 JavaScript 代碼中應用語法高亮:

{{< highlight markdown >}}
```js
grunt.initConfig({
  assemble: {
    options: {
      assets: 'docs/assets',
      data: 'src/data/*.{json,yml}',
      helpers: 'src/custom-helpers.js',
      partials: ['src/partials/**/*.{hbs,md}']
    },
    pages: {
      options: {
        layout: 'default.hbs'
      },
      files: {
        './': ['src/templates/pages/index.hbs']
      }
    }
  }
};
```
{{< / highlight >}}

呈現的輸出效果如下:

```js
grunt.initConfig({
  assemble: {
    options: {
      assets: 'docs/assets',
      data: 'src/data/*.{json,yml}',
      helpers: 'src/custom-helpers.js',
      partials: ['src/partials/**/*.{hbs,md}']
    },
    pages: {
      options: {
        layout: 'default.hbs'
      },
      files: {
        './': ['src/templates/pages/index.hbs']
      }
    }
  }
};
```

{{< admonition >}}
**Hugo** 文檔中的 [語法高亮頁面](https://gohugo.io/content-management/syntax-highlighting/) 介紹了有關語法高亮的更多信息,
包括語法高亮的 shortcode.
{{< /admonition >}}

## 10 表格

通過在每個單元格之間添加豎線作為分隔線, 並在標題下添加一行破折號 (也由豎線分隔) 來創建表格. 注意, 豎線不需要垂直對齊.

```markdown
| Option | Description |
| ------ | ----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |
```

呈現的輸出效果如下:

| Option | Description |
| ------ | ----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |

輸出的 HTML 看起來像這樣:

```html
<table>
  <thead>
    <tr>
      <th>Option</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>data</td>
      <td>path to data files to supply the data that will be passed into templates.</td>
    </tr>
    <tr>
      <td>engine</td>
      <td>engine to be used for processing templates. Handlebars is the default.</td>
    </tr>
    <tr>
      <td>ext</td>
      <td>extension to be used for dest files.</td>
    </tr>
  </tbody>
</table>
```

{{< admonition note "文本右對齊或居中對齊" >}}
在任何標題下方的破折號右側添加冒號將使該列的文本右對齊.

在任何標題下方的破折號兩邊添加冒號將使該列的對齊文本居中.

```markdown
| Option | Description |
|:------:| -----------:|
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |
```

呈現的輸出效果如下:

| Option | Description |
|:------:| -----------:|
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |
{{< /admonition >}}

## 11 鏈接 {#links}

### 基本鏈接

```markdown
<https://assemble.io>
<contact@revolunet.com>
[Assemble](https://assemble.io)
```

呈現的輸出效果如下 (將鼠標懸停在鏈接上，沒有提示):

<https://assemble.io>

<contact@revolunet.com>

[Assemble](https://assemble.io)

輸出的 HTML 看起來像這樣:

```html
<a href="https://assemble.io">https://assemble.io</a>
<a href="mailto:contact@revolunet.com">contact@revolunet.com</a>
<a href="https://assemble.io">Assemble</a>
```

### 添加一個標題

```markdown
[Upstage](https://github.com/upstage/ "Visit Upstage!")
```

呈現的輸出效果如下 (將鼠標懸停在鏈接上，會有一行提示):

[Upstage](https://github.com/upstage/ "Visit Upstage!")

輸出的 HTML 看起來像這樣:

```html
<a href="https://github.com/upstage/" title="Visit Upstage!">Upstage</a>
```

### 定位標記

定位標記使你可以跳至同一頁面上的指定錨點. 例如, 每個章節:

```markdown
## Table of Contents
  * [Chapter 1](#chapter-1)
  * [Chapter 2](#chapter-2)
  * [Chapter 3](#chapter-3)
```

將跳轉到這些部分:

```markdown
## Chapter 1 <a id="chapter-1"></a>
Content for chapter one.

## Chapter 2 <a id="chapter-2"></a>
Content for chapter one.

## Chapter 3 <a id="chapter-3"></a>
Content for chapter one.
```

{{< admonition >}}
定位標記的位置幾乎是任意的. 因為它們並不引人注目, 所以它們通常被放在同一行了.
{{< /admonition >}}

## 12 腳注

腳注使你可以添加注釋和參考, 而不會使文檔正文混亂.
當你創建腳注時, 會在添加腳注引用的位置出現帶有鏈接的上標編號.
讀者可以單擊鏈接以跳至頁面底部的腳注內容.

要創建腳注引用, 請在方括號中添加插入符號和標識符 (`[^1]`).
標識符可以是數字或單詞, 但不能包含空格或制表符.
標識符僅將腳注引用與腳注本身相關聯 - 在腳注輸出中, 腳注按順序編號.

在中括號內使用插入符號和數字以及用冒號和文本來添加腳注內容 (`[^1]：這是一段腳注`).
你不一定要在文檔末尾添加腳注. 可以將它們放在除列表, 引用和表格等元素之外的任何位置.

```markdown
這是一個數字腳注[^1].
這是一個帶標簽的腳注[^label]

[^1]: 這是一個數字腳注
[^label]: 這是一個帶標簽的腳注
```

這是一個數字腳注[^1].

這是一個帶標簽的腳注[^label]

[^1]: 這是一個數字腳注
[^label]: 這是一個帶標簽的腳注

## 13 圖片

圖片的語法與鏈接相似, 但包含一個在前面的感嘆號.

```markdown
![Minion](https://octodex.github.com/images/minion.png)
```

![Minion](https://octodex.github.com/images/minion.png)

或者:

```markdown
![Alt text](https://octodex.github.com/images/stormtroopocat.jpg "The Stormtroopocat")
```

![Alt text](https://octodex.github.com/images/stormtroopocat.jpg "The Stormtroopocat")

像鏈接一樣, 圖片也具有腳注樣式的語法:

```markdown
![Alt text][id]
```

![Alt text][id]

稍後在文檔中提供參考內容, 用來定義 URL 的位置:

```markdown
[id]: https://octodex.github.com/images/dojocat.jpg  "The Dojocat"
```

[id]: https://octodex.github.com/images/dojocat.jpg  "The Dojocat"

{{< admonition tip >}}
**FeelIt** 主題提供了一個包含更多功能的 [圖片的 shortcode](../theme-documentation-extended-shortcodes#image).
{{< /admonition >}}
 