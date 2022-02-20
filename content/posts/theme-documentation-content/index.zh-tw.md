---
weight: 2
title: "主題文檔 - 內容"
date: 2020-03-05T16:30:05+08:00
lastmod: 2020-03-05T16:30:05+08:00
draft: false
author: "FeelIt"
authorLink: "https://feelit.khusika.com"
description: "了解如何在 FeelIt 主題中快速, 直觀地創建和組織內容."
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["content", "Markdown"]
categories: ["documentation"]

lightgallery: true
hiddenFromHomePage: true


toc:
  auto: false
math:
  enable: true
---

了解如何在 **FeelIt** 主題中快速, 直觀地創建和組織內容.

<!--more-->

## 1 內容組織 {#contents-organization}

以下是一些方便你清晰管理和生成文章的目錄結構建議:

* 保持博客文章存放在 `content/posts` 目錄, 例如: `content/posts/我的第一篇文章.md`
* 保持簡單的靜態頁面存放在 `content` 目錄, 例如: `content/about.md`
* 本地資源組織

{{< admonition note "本地資源引用" >}}
{{< version 0.2.10 >}}

有三種方法來引用**圖片**和**音樂**等本地資源:

1. 使用[頁面包](https://gohugo.io/content-management/page-bundles/)中的[頁面資源](https://gohugo.io/content-management/page-resources/).
   你可以使用適用於 `Resources.GetMatch` 的值或者直接使用相對於當前頁面目錄的文件路徑來引用頁面資源.
2. 將本地資源放在 **assets** 目錄中, 默認路徑是 `/assets`.
   引用資源的文件路徑是相對於 assets 目錄的.
3. 將本地資源放在 **static** 目錄中, 默認路徑是 `/static`.
   引用資源的文件路徑是相對於 static 目錄的.

引用的**優先級**符合以上的順序.

在這個主題中的很多地方可以使用上面的本地資源引用,
例如 **鏈接**, **圖片**, `image` shortcode, `music` shortcode 和**前置參數**中的部分參數.

頁面資源或者 **assets** 目錄中的[圖片處理](https://gohugo.io/content-management/image-processing/)會在未來的版本中得到支持.
非常酷的功能! :(far fa-grin-squint fa-fw):
{{< /admonition >}}

## 2 前置參數 {#front-matter}

**Hugo** 允許你在文章內容前面添加 `yaml`, `toml` 或者 `json` 格式的前置參數.

{{< admonition >}}
**不是所有**的以下前置參數都必須在你的每篇文章中設置.
只有在文章的參數和你的 [網站設置](../theme-documentation-basics#site-configuration) 中的 `page` 部分不一致時才有必要這麽做.
{{< /admonition >}}

這是一個前置參數例子:

```yaml
---
title: "我的第一篇文章"
subtitle: ""
date: 2020-03-04T15:58:26+08:00
lastmod: 2020-03-04T15:58:26+08:00
draft: true
author: ""
authorLink: ""
description: ""
license: ""
images: []

tags: []
categories: []
featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
code:
  copy: true
  # ...
math:
  enable: true
  # ...
mapbox:
  accessToken: ""
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
library:
  css:
    # someCSS = "some.css"
    # 位於 "assets/"
    # 或者
    # someCSS = "https://cdn.example.com/some.css"
  js:
    # someJS = "some.js"
    # 位於 "assets/"
    # 或者
    # someJS = "https://cdn.example.com/some.js"
seo:
  images: []
  # ...
---
```

* **title**: 文章標題.
* **subtitle**: {{< version 0.2.0 >}} 文章副標題.
* **date**: 這篇文章創建的日期時間. 它通常是從文章的前置參數中的 `date` 字段獲取的, 但是也可以在 [網站配置](../theme-documentation-basics#site-configuration) 中設置.
* **lastmod**: 上次修改內容的日期時間.
* **draft**: 如果設為 `true`, 除非 `hugo` 命令使用了 `--buildDrafts`/`-D` 參數, 這篇文章不會被渲染.
* **author**: 文章作者.
* **authorLink**: 文章作者的鏈接.
* **description**: 文章內容的描述.
* **license**: 這篇文章特殊的許可.
* **images**: 頁面圖片, 用於 Open Graph 和 Twitter Cards.

* **tags**: 文章的標簽.
* **categories**: 文章所屬的類別.
* **featuredImage**: 文章的特色圖片.
* **featuredImagePreview**: 用在主頁預覽的文章特色圖片.

* **hiddenFromHomePage**: 如果設為 `true`, 這篇文章將不會顯示在主頁上.
* **hiddenFromSearch**: {{< version 0.2.0 >}} 如果設為 `true`, 這篇文章將不會顯示在搜索結果中.
* **twemoji**: {{< version 0.2.0 >}} 如果設為 `true`, 這篇文章會使用 twemoji.
* **lightgallery**: 如果設為 `true`, 文章中的圖片將可以按照畫廊形式呈現.
* **ruby**: {{< version 0.2.0 >}} 如果設為 `true`, 這篇文章會使用 [上標注釋擴展語法](#ruby).
* **fraction**: {{< version 0.2.0 >}} 如果設為 `true`, 這篇文章會使用 [分數擴展語法](#fraction).
* **fontawesome**: {{< version 0.2.0 >}} 如果設為 `true`, 這篇文章會使用 [Font Awesome 擴展語法](#fontawesome).
* **linkToMarkdown**: 如果設為 `true`, 內容的頁腳將顯示指向原始 Markdown 文件的鏈接.
* **rssFullText**: {{< version 0.2.4 >}} 如果設為 `true`, 在 RSS 中將會顯示全文內容.

* **toc**: {{< version 0.2.9 changed >}} 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.toc` 部分相同.
* **code**: {{< version 0.2.0 >}} 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.code` 部分相同.
* **math**: {{< version 0.2.0 changed >}} 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.math` 部分相同.
* **mapbox**: {{< version 0.2.0 >}} 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.mapbox` 部分相同.
* **share**: 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.share` 部分相同.
* **comment**: {{< version 0.2.0 changed >}} 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.comment` 部分相同.
* **library**: {{< version 0.2.7 >}} 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.library` 部分相同.
* **seo**: {{< version 0.2.10 >}} 和 [網站配置](../theme-documentation-basics#site-configuration) 中的 `params.page.seo` 部分相同.

{{< admonition tip >}}
{{< version 0.2.10 >}}

**featuredImage** 和 **featuredImagePreview** 支持[本地資源引用](#contents-organization)的完整用法.

如果帶有在前置參數中設置了 `name: featured-image` 或 `name: featured-image-preview` 屬性的頁面資源,
沒有必要在設置 `featuredImage` 或 `featuredImagePreview`:

```yaml
resources:
- name: featured-image
  src: featured-image.webp
- name: featured-image-preview
  src: featured-image-preview.jpg
```
{{< /admonition >}}

## 3 內容摘要

**FeelIt** 主題使用內容摘要在主頁中顯示大致文章信息。Hugo 支持生成文章的摘要.

![文章摘要預覽](summary.zh-cn.png "文章摘要預覽")

### 自動摘要拆分

默認情況下, Hugo 自動將內容的前 70 個單詞作為摘要.

你可以通過在 [網站配置](../theme-documentation-basics#site-configuration) 中設置 `summaryLength` 來自定義摘要長度.

如果您要使用 [CJK]^(中文/日語/韓語) 語言創建內容, 並且想使用 Hugo 的自動摘要拆分功能，請在 [網站配置](../theme-documentation-basics#site-configuration) 中將 `hasCJKLanguage` 設置為 `true`.

### 手動摘要拆分

另外, 你也可以添加 `<!--more-->` 摘要分割符來拆分文章生成摘要.

摘要分隔符之前的內容將用作該文章的摘要.

{{< admonition >}}
請小心輸入`<!--more-->` ; 即全部為小寫且沒有空格.
{{< /admonition >}}

### 前置參數摘要

你可能希望摘要不是文章開頭的文字. 在這種情況下, 你可以在文章前置參數的 `summary` 變量中設置單獨的摘要.

### 使用文章描述作為摘要

你可能希望將文章前置參數中的 `description` 變量的內容作為摘要.

你仍然需要在文章開頭添加 `<!--more-->` 摘要分割符. 將摘要分隔符之前的內容保留為空. 然後 **FeelIt** 主題會將你的文章描述作為摘要.

### 摘要選擇的優先級順序

由於可以通過多種方式指定摘要, 因此了解順序很有用. 如下:

1. 如果文章中有 `<!--more-->` 摘要分隔符, 但分隔符之前沒有內容, 則使用描述作為摘要.
2. 如果文章中有 `<!--more-->` 摘要分隔符, 則將按照手動摘要拆分的方法獲得摘要.
3. 如果文章前置參數中有摘要變量, 那麽將以該值作為摘要.
4. 按照自動摘要拆分方法.

{{< admonition >}}
不建議在摘要內容中包含富文本塊元素, 這會導致渲染錯誤. 例如代碼塊, 圖片, 表格等.
{{< /admonition >}}

## 4 Markdown 基本語法

這部分內容在 [Markdown 基本語法頁面](../basic-markdown-syntax/) 中介紹.

## 5 Markdown 擴展語法 {#extended-markdown-syntax}

**FeelIt** 主題提供了一些擴展的語法便於你撰寫文章.

### Emoji 支持

這部分內容在 [Emoji 支持頁面](../emoji-support/) 中介紹.

### 數學公式

**FeelIt** 基於 [$ \KaTeX $](https://katex.org/) 提供數學公式的支持.

在你的 [網站配置](../theme-documentation-basics#site-configuration) 中的 `[params.math]` 下面設置屬性 `enable = true`,
並在文章的前置參數中設置屬性 `math: true`來啟用數學公式的自動渲染.

{{< admonition tip >}}
有一份 [$ \KaTeX $ 中支持的 $ \TeX $ 函數](https://katex.org/docs/supported.html) 清單.
{{< /admonition >}}

#### 公式塊

默認的公式塊分割符是 `$$`/`$$` 和 `\\[`/`\\]`:

```markdown
$$ c = \pm\sqrt{a^2 + b^2} $$

\\[ f(x)=\int_{-\infty}^{\infty} \hat{f}(\xi) e^{2 \pi i \xi x} d \xi \\]
```

呈現的輸出效果如下:

$$ c = \pm\sqrt{a^2 + b^2} $$

\\[ f(x)=\int_{-\infty}^{\infty} \hat{f}(\xi) e^{2 \pi i \xi x} d \xi \\]

#### 行內公式

默認的行內公式分割符是  `$`/`$` 和 `\\(`/`\\)`:

```markdown
$ c = \pm\sqrt{a^2 + b^2} $ 和 \\( f(x)=\int_{-\infty}^{\infty} \hat{f}(\xi) e^{2 \pi i \xi x} d \xi \\)
```

呈現的輸出效果如下:

$ c = \pm\sqrt{a^2 + b^2} $ 和 \\( f(x)=\int_{-\infty}^{\infty} \hat{f}(\xi) e^{2 \pi i \xi x} d \xi \\)

{{< admonition tip >}}
你可以在 [網站配置](../theme-documentation-basics#site-configuration) 中自定義公式塊和行內公式的分割符.
{{< /admonition >}}

#### Copy-tex

**[Copy-tex](https://github.com/Khan/KaTeX/tree/master/contrib/copy-tex)** 是一個 **$ \KaTeX $** 的插件.

通過這個擴展, 在選擇並覆制 $ \KaTeX $ 渲染的公式時, 會將其 $ \LaTeX $ 源代碼覆制到剪貼板.

在你的 [網站配置](../theme-documentation-basics#site-configuration) 中的 `[params.math]` 下面設置屬性 `copyTex = true` 來啟用 Copy-tex.

選擇並覆制上一節中渲染的公式, 可以發現覆制的內容為 LaTeX 源代碼.

#### mhchem

**[mhchem](https://github.com/Khan/KaTeX/tree/master/contrib/mhchem)** 是一個 **$ \KaTeX $** 的插件.

通過這個擴展, 你可以在文章中輕松編寫漂亮的化學方程式.

在你的 [網站配置](../theme-documentation-basics#site-configuration) 中的 `[params.math]` 下面設置屬性 `mhchem = true` 來啟用 mhchem.

```markdown
$$ \ce{CO2 + C -> 2 CO} $$

$$ \ce{Hg^2+ ->[I-] HgI2 ->[I-] [Hg^{II}I4]^2-} $$
```

呈現的輸出效果如下:

$$ \ce{CO2 + C -> 2 CO} $$

$$ \ce{Hg^2+ ->[I-] HgI2 ->[I-] [Hg^{II}I4]^2-} $$

### 字符注音或者注釋 {#ruby}

**FeelIt** 主題支持一種 **字符注音或者注釋** Markdown 擴展語法:

```markdown
[Hugo]{?^}(一個開源的靜態網站生成工具)
```

呈現的輸出效果如下:

[Hugo]^(一個開源的靜態網站生成工具)

### 分數 {#fraction}

{{< version 0.2.0 >}}

**FeelIt** 主題支持一種 **分數** Markdown 擴展語法:

```markdown
[淺色]{?/}[深色]

[99]{?/}[100]
```

呈現的輸出效果如下:

[淺色]/[深色]

[90]/[100]

### Font Awesome {#fontawesome}

**FeelIt** 主題使用 [Font Awesome](https://fontawesome.com/) 作為圖標庫.
你同樣可以在文章中輕松使用這些圖標.

從 [Font Awesome 網站](https://fontawesome.com/icons?d=gallery) 上獲取所需的圖標 `class`.

```markdown
去露營啦! {?:}(fas fa-campground fa-fw): 很快就回來.

真開心! {?:}(far fa-grin-tears):
```

呈現的輸出效果如下:

去露營啦! :(fas fa-campground fa-fw): 很快就回來.

真開心! :(far fa-grin-tears):

### 轉義字符 {#escape-character}

在某些特殊情況下 (編寫這個主題文檔時 :(far fa-grin-squint-tears):),
你的文章內容會與 Markdown 的基本或者擴展語法沖突, 並且無法避免.

轉義字符語法可以幫助你渲染出想要的內容:

```markdown
{{??}X} -> X
```

例如, 兩個 `:` 會啟用 emoji 語法. 但有時候這不是你想要的結果. 可以像這樣使用轉義字符語法:

```markdown
{{??}:}joy:
```

呈現的輸出效果如下:

**{?:}joy{?:}** 而不是 **:joy:**

{{< admonition tip >}}
這個方法可以間接解決一個還未解決的 **[Hugo 的 issue](https://github.com/gohugoio/hugo/issues/4978)**.
{{< /admonition >}}

另一個例子是:

```markdown
[link{{??}]}(#escape-character)
```

呈現的輸出效果如下:

**[link{?]}(#escape-character)** 而不是 **[link](#escape-character)**.
