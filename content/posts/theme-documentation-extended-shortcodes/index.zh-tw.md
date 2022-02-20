---
weight: 4
title: "主題文檔 - 擴展 Shortcodes"
date: 2020-03-03T16:29:59+08:00
lastmod: 2020-03-03T16:29:59+08:00
draft: false
author: "FeelIt"
authorLink: "https://feelit.khusika.com"
description: "FeelIt 主題在 Hugo 內置的 shortcode 的基礎上提供多個擴展的 shortcode."
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["shortcodes"]
categories: ["documentation"]

lightgallery: true
hiddenFromHomePage: true

mapbox:
  lightStyle: mapbox://styles/mapbox/light-zh-v1?optimize=true
  darkStyle: mapbox://styles/mapbox/dark-zh-v1?optimize=true
---

**FeelIt** 主題在 Hugo 內置的 shortcode 的基礎上提供多個擴展的 shortcode.

<!--more-->

## 1 style

{{< version 0.2.0 changed >}}

{{< admonition >}}
Hugo **extended** 版本對於 `style` shortcode 是必需的.
{{< /admonition >}}

`style` shortcode 用來在你的文章中插入自定義樣式.

`style` shortcode 有兩個位置參數.

第一個參數是自定義樣式的內容. 它支持 [:(fab fa-sass fa-fw): SASS](https://sass-lang.com/documentation/style-rules/declarations#nesting) 中的嵌套語法,
並且 `&` 指代這個父元素.

第二個參數是包裹你要更改樣式的內容的 HTML 標簽, 默認值是 `div`.

一個 `style` 示例:

```markdown
{{</* style "text-align:right; strong{color:#00b1ff;}" */>}}
This is a **right-aligned** paragraph.
{{</* /style */>}}
```

呈現的輸出效果如下:

{{< style "text-align:right; strong{color:#00b1ff;}" >}}
This is a **right-aligned** paragraph.
{{< /style >}}

## 2 link

{{< version 0.2.0 >}}

`link` shortcode 是 [Markdown 鏈接語法](../basic-markdown-syntax#links) 的替代.
`link` shortcode 可以提供一些其它的功能並且可以在代碼塊中使用.

{{< version 0.2.10 >}} 支持[本地資源引用](../theme-documentation-content#contents-organization)的完整用法.

`link` shortcode 有以下命名參數:

* **href** *[必需]* (**第一個**位置參數)

    鏈接的目標.

* **content** *[可選]* (**第二個**位置參數)

    鏈接的內容, 默認值是 **href** 參數的值.

    *支持 Markdown 或者 HTML 格式.*

* **title** *[可選]* (**第三個**位置參數)

    HTML `a` 標簽 的 `title` 屬性, 當懸停在鏈接上會顯示的提示.

* **rel** *[可選]*

    HTML `a` 標簽 的 `rel` 補充屬性.

* **class** *[可選]*

    HTML `a` 標簽 的 `class` 屬性.

一個 `link` 示例:

```markdown
{{</* link "https://assemble.io" */>}}
或者
{{</* link href="https://assemble.io" */>}}

{{</* link "mailto:contact@revolunet.com" */>}}
或者
{{</* link href="mailto:contact@revolunet.com" */>}}

{{</* link "https://assemble.io" Assemble */>}}
或者
{{</* link href="https://assemble.io" content=Assemble */>}}
```

呈現的輸出效果如下:

* {{< link "https://assemble.io" >}}
* {{< link "mailto:contact@revolunet.com" >}}
* {{< link "https://assemble.io" Assemble >}}

一個帶有標題的 `link` 示例:

```markdown
{{</* link "https://github.com/upstage/" Upstage "Visit Upstage!" */>}}
或者
{{</* link href="https://github.com/upstage/" content=Upstage title="Visit Upstage!" */>}}
```

呈現的輸出效果如下 (將鼠標懸停在鏈接上，會有一行提示):

{{< link "https://github.com/upstage/" Upstage "Visit Upstage!" >}}

## 3 image {#image}

{{< version 0.2.0 changed >}}

`image` shortcode 是 [`figure` shortcode](../theme-documentation-built-in-shortcodes#figure) 的替代. `image` shortcode 可以充分利用 [lazysizes](https://github.com/aFarkas/lazysizes) 和 [lightgallery.js](https://github.com/sachinchoolur/lightgallery.js) 兩個依賴庫.

{{< version 0.2.10 >}} 支持[本地資源引用](../theme-documentation-content#contents-organization)的完整用法.

`image` shortcode 有以下命名參數:

* **src** *[必需]* (**第一個**位置參數)

    圖片的 URL.

* **alt** *[可選]* (**第二個**位置參數)

    圖片無法顯示時的替代文本, 默認值是 **src** 參數的值.

    *支持 Markdown 或者 HTML 格式.*

* **caption** *[可選]* (**第三個**位置參數)

    圖片標題.

    *支持 Markdown 或者 HTML 格式.*

* **title** *[可選]*

    當懸停在圖片上會顯示的提示.

* **class** *[可選]*

    HTML `figure` 標簽的 `class` 屬性.

* **src_s** *[可選]*

    圖片縮略圖的 URL, 用在畫廊模式中, 默認值是 **src** 參數的值.

* **src_l** *[可選]*

    高清圖片的 URL, 用在畫廊模式中, 默認值是 **src** 參數的值.

* **height** *[可選]*

    圖片的 `height` 屬性.

* **width** *[可選]*

    圖片的 `width` 屬性.

* **linked** *[可選]*

    圖片是否需要被鏈接, 默認值是 `true`.

* **rel** *[可選]*

    HTML `a` 標簽 的 `rel` 補充屬性, 僅在 **linked** 屬性設置成 `true` 時有效.

一個 `image` 示例:

```markdown
{{</* image src="/images/lighthouse.jpg" caption="Lighthouse (`image`)" src_s="/images/lighthouse-small.jpg" src_l="/images/lighthouse-large.jpg" */>}}
```

呈現的輸出效果如下:

{{< image src="/images/lighthouse.jpg" caption="Lighthouse (`image`)" src_s="/images/lighthouse-small.jpg" src_l="/images/lighthouse-large.jpg" >}}

## 4 admonition

`admonition` shortcode 支持 **12** 種 幫助你在頁面中插入提示的橫幅.

*支持 Markdown 或者 HTML 格式.*

{{< admonition >}}
一個 **注意** 橫幅
{{< /admonition >}}

{{< admonition abstract >}}
一個 **摘要** 橫幅
{{< /admonition >}}

{{< admonition info >}}
一個 **信息** 橫幅
{{< /admonition >}}

{{< admonition tip >}}
一個 **技巧** 橫幅
{{< /admonition >}}

{{< admonition success >}}
一個 **成功** 橫幅
{{< /admonition >}}

{{< admonition question >}}
一個 **問題** 橫幅
{{< /admonition >}}

{{< admonition warning >}}
一個 **警告** 橫幅
{{< /admonition >}}

{{< admonition failure >}}
一個 **失敗** 橫幅
{{< /admonition >}}

{{< admonition danger >}}
一個 **危險** 橫幅
{{< /admonition >}}

{{< admonition bug >}}
一個 **Bug** 橫幅
{{< /admonition >}}

{{< admonition example >}}
一個 **示例** 橫幅
{{< /admonition >}}

{{< admonition quote >}}
一個 **引用** 橫幅
{{< /admonition >}}

`admonition` shortcode 有以下命名參數:

* **type** *[必需]* (**第一個**位置參數)

    `admonition` 橫幅的類型, 默認值是 `note`.

* **title** *[可選]* (**第二個**位置參數)

    `admonition` 橫幅的標題, 默認值是 **type** 參數的值.

* **open** *[可選]* (**第三個**位置參數) {{< version 0.2.0 changed >}}

    橫幅內容是否默認展開, 默認值是 `true`.

一個 `admonition` 示例:

```markdown
{{</* admonition type=tip title="This is a tip" open=false */>}}
一個 **技巧** 橫幅
{{</* /admonition */>}}
或者
{{</* admonition tip "This is a tip" false */>}}
一個 **技巧** 橫幅
{{</* /admonition */>}}
```

呈現的輸出效果如下:

{{< admonition tip "This is a tip" false >}}
一個 **技巧** 橫幅
{{< /admonition >}}

## 5 mermaid

[mermaid](https://mermaidjs.github.io/) 是一個可以幫助你在文章中生成圖表和流程圖的庫, 類似 Markdown 的語法.

只需將你的 mermaid 代碼插入 `mermaid` shortcode 中即可.

### 5.1 流程圖 {#flowchart}

一個 **流程圖** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
graph LR;
    A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
graph LR;
    A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
{{< /mermaid >}}

### 5.2 時序圖 {#sequence-diagram}

一個 **時序圖** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
sequenceDiagram
    participant Alice
    participant Bob
    Alice->>John: Hello John, how are you?
    loop Healthcheck
        John->John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail...
    John-->Alice: Great!
    John->Bob: How about you?
    Bob-->John: Jolly good!
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
sequenceDiagram
    participant Alice
    participant Bob
    Alice->>John: Hello John, how are you?
    loop Healthcheck
        John->John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail...
    John-->Alice: Great!
    John->Bob: How about you?
    Bob-->John: Jolly good!
{{< /mermaid >}}

### 5.3 甘特圖 {#gantt}

一個 **甘特圖** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
gantt
    dateFormat  YYYY-MM-DD
    title Adding GANTT diagram functionality to mermaid
    section A section
    Completed task            :done,    des1, 2014-01-06,2014-01-08
    Active task               :active,  des2, 2014-01-09, 3d
    Future task               :         des3, after des2, 5d
    Future task2               :         des4, after des3, 5d
    section Critical tasks
    Completed task in the critical line :crit, done, 2014-01-06,24h
    Implement parser and jison          :crit, done, after des1, 2d
    Create tests for parser             :crit, active, 3d
    Future task in critical line        :crit, 5d
    Create tests for renderer           :2d
    Add to mermaid                      :1d
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
gantt
    dateFormat  YYYY-MM-DD
    title Adding GANTT diagram functionality to mermaid
    section A section
    Completed task            :done,    des1, 2014-01-06,2014-01-08
    Active task               :active,  des2, 2014-01-09, 3d
    Future task               :         des3, after des2, 5d
    Future task2               :         des4, after des3, 5d
    section Critical tasks
    Completed task in the critical line :crit, done, 2014-01-06,24h
    Implement parser and jison          :crit, done, after des1, 2d
    Create tests for parser             :crit, active, 3d
    Future task in critical line        :crit, 5d
    Create tests for renderer           :2d
    Add to mermaid                      :1d
{{< /mermaid >}}

### 5.4 類圖 {#class-diagram}

一個 **類圖** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
classDiagram
    Class01 <|-- AveryLongClass : Cool
    Class03 *-- Class04
    Class05 o-- Class06
    Class07 .. Class08
    Class09 --> C2 : Where am i?
    Class09 --* C3
    Class09 --|> Class07
    Class07 : equals()
    Class07 : Object[] elementData
    Class01 : size()
    Class01 : int chimp
    Class01 : int gorilla
    Class08 <--> C2: Cool label
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
classDiagram
    Class01 <|-- AveryLongClass : Cool
    Class03 *-- Class04
    Class05 o-- Class06
    Class07 .. Class08
    Class09 --> C2 : Where am i?
    Class09 --* C3
    Class09 --|> Class07
    Class07 : equals()
    Class07 : Object[] elementData
    Class01 : size()
    Class01 : int chimp
    Class01 : int gorilla
    Class08 <--> C2: Cool label
{{< /mermaid >}}

### 5.5 狀態圖 {#state-diagram}

一個 **狀態圖** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
stateDiagram
    [*] --> Still
    Still --> [*]
    Still --> Moving
    Moving --> Still
    Moving --> Crash
    Crash --> [*]
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
stateDiagram
    [*] --> Still
    Still --> [*]
    Still --> Moving
    Moving --> Still
    Moving --> Crash
    Crash --> [*]
{{< /mermaid >}}

### 5.6 Git 圖 {#git-graph}

一個 **Git 圖** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
gitGraph:
options
{
    "nodeSpacing": 100,
    "nodeRadius": 10
}
end
    commit
    branch newbranch
    checkout newbranch
    commit
    commit
    checkout master
    commit
    commit
    merge newbranch
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
gitGraph:
options
{
    "nodeSpacing": 100,
    "nodeRadius": 10
}
end
    commit
    branch newbranch
    checkout newbranch
    commit
    commit
    checkout master
    commit
    commit
    merge newbranch
{{< /mermaid >}}

### 5.7 實體關系圖 {#erd}

一個 **erDiagram** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE-ITEM : contains
    CUSTOMER }|..|{ DELIVERY-ADDRESS : uses
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE-ITEM : contains
    CUSTOMER }|..|{ DELIVERY-ADDRESS : uses
{{< /mermaid >}}

### 5.8 用戶旅程圖 {#ujd}

一個 **旅行** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
journey
    title My working day
    section Go to work
      Make tea: 5: Me
      Go upstairs: 3: Me
      Do work: 1: Me, Cat
    section Go home
      Go downstairs: 5: Me
      Sit down: 5: Me

{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
journey
    title My working day
    section Go to work
      Make tea: 5: Me
      Go upstairs: 3: Me
      Do work: 1: Me, Cat
    section Go home
      Go downstairs: 5: Me
      Sit down: 5: Me

{{< /mermaid >}}

### 5.9 餅圖 {#pie}

一個 **餅圖** `mermaid` 示例:

```markdown
{{</* mermaid */>}}
pie
    "Dogs" : 386
    "Cats" : 85
    "Rats" : 15
{{</* /mermaid */>}}
```

呈現的輸出效果如下:

{{< mermaid >}}
pie
    "Dogs" : 386
    "Cats" : 85
    "Rats" : 15
{{< /mermaid >}}

## 6 echarts

[ECharts](https://echarts.apache.org/) 是一個幫助你生成交互式數據可視化的庫.

ECharts 提供了常規的 [折線圖](https://echarts.apache.org/zh/option.html#series-line), [柱狀圖](https://echarts.apache.org/zh/option.html#series-line), [散點圖](https://echarts.apache.org/zh/option.html#series-scatter), [餅圖](https://echarts.apache.org/zh/option.html#series-pie), [K線圖](https://echarts.apache.org/zh/option.html#series-candlestick), 用於統計的 [盒形圖](https://echarts.apache.org/zh/option.html#series-boxplot), 用於地理數據可視化的 [地圖](https://echarts.apache.org/zh/option.html#series-map), [熱力圖](https://echarts.apache.org/zh/option.html#series-heatmap), [線圖](https://echarts.apache.org/zh/option.html#series-lines), 用於關系數據可視化的 [關系圖](https://echarts.apache.org/zh/option.html#series-graph), [treemap](https://echarts.apache.org/zh/option.html#series-treemap), [旭日圖](https://echarts.apache.org/zh/option.html#series-sunburst), 多維數據可視化的 [平行坐標](https://echarts.apache.org/zh/option.html#series-parallel), 還有用於 BI 的 [漏鬥圖](https://echarts.apache.org/zh/option.html#series-funnel), [儀表盤](https://echarts.apache.org/zh/option.html#series-gauge), 並且支持圖與圖之間的混搭.

只需在 `echarts` shortcode 中以 `JSON`/`YAML`/`TOML`格式插入 ECharts 選項即可.

一個 `JSON` 格式的 `echarts` 示例:

```json
{{</* echarts */>}}
{
  "title": {
    "text": "折線統計圖",
    "top": "2%",
    "left": "center"
  },
  "tooltip": {
    "trigger": "axis"
  },
  "legend": {
    "data": ["郵件營銷", "聯盟廣告", "視頻廣告", "直接訪問", "搜索引擎"],
    "top": "10%"
  },
  "grid": {
    "left": "5%",
    "right": "5%",
    "bottom": "5%",
    "top": "20%",
    "containLabel": true
  },
  "toolbox": {
    "feature": {
      "saveAsImage": {
        "title": "保存為圖片"
      }
    }
  },
  "xAxis": {
    "type": "category",
    "boundaryGap": false,
    "data": ["周一", "周二", "周三", "周四", "周五", "周六", "周日"]
  },
  "yAxis": {
    "type": "value"
  },
  "series": [
    {
      "name": "郵件營銷",
      "type": "line",
      "stack": "總量",
      "data": [120, 132, 101, 134, 90, 230, 210]
    },
    {
      "name": "聯盟廣告",
      "type": "line",
      "stack": "總量",
      "data": [220, 182, 191, 234, 290, 330, 310]
    },
    {
      "name": "視頻廣告",
      "type": "line",
      "stack": "總量",
      "data": [150, 232, 201, 154, 190, 330, 410]
    },
    {
      "name": "直接訪問",
      "type": "line",
      "stack": "總量",
      "data": [320, 332, 301, 334, 390, 330, 320]
    },
    {
      "name": "搜索引擎",
      "type": "line",
      "stack": "總量",
      "data": [820, 932, 901, 934, 1290, 1330, 1320]
    }
  ]
}
{{</* /echarts */>}}
```

一個 `YAML` 格式的 `echarts` 示例:

```yaml
{{</* echarts */>}}
title:
    text: 折線統計圖
    top: 2%
    left: center
tooltip:
    trigger: axis
legend:
    data:
        - 郵件營銷
        - 聯盟廣告
        - 視頻廣告
        - 直接訪問
        - 搜索引擎
    top: 10%
grid:
    left: 5%
    right: 5%
    bottom: 5%
    top: 20%
    containLabel: true
toolbox:
    feature:
        saveAsImage:
            title: 保存為圖片
xAxis:
    type: category
    boundaryGap: false
    data:
        - 周一
        - 周二
        - 周三
        - 周四
        - 周五
        - 周六
        - 周日
yAxis:
    type: value
series:
    - name: 郵件營銷
      type: line
      stack: 總量
      data:
          - 120
          - 132
          - 101
          - 134
          - 90
          - 230
          - 210
    - name: 聯盟廣告
      type: line
      stack: 總量
      data:
          - 220
          - 182
          - 191
          - 234
          - 290
          - 330
          - 310
    - name: 視頻廣告
      type: line
      stack: 總量
      data:
          - 150
          - 232
          - 201
          - 154
          - 190
          - 330
          - 410
    - name: 直接訪問
      type: line
      stack: 總量
      data:
          - 320
          - 332
          - 301
          - 334
          - 390
          - 330
          - 320
    - name: 搜索引擎
      type: line
      stack: 總量
      data:
          - 820
          - 932
          - 901
          - 934
          - 1290
          - 1330
          - 1320
{{</* /echarts */>}}
```

一個 `TOML` 格式的 `echarts` 示例:

```toml
{{</* echarts */>}}
[title]
text = "折線統計圖"
top = "2%"
left = "center"

[tooltip]
trigger = "axis"

[legend]
data = [
  "郵件營銷",
  "聯盟廣告",
  "視頻廣告",
  "直接訪問",
  "搜索引擎"
]
top = "10%"

[grid]
left = "5%"
right = "5%"
bottom = "5%"
top = "20%"
containLabel = true

[toolbox]
[toolbox.feature]
[toolbox.feature.saveAsImage]
title = "保存為圖片"

[xAxis]
type = "category"
boundaryGap = false
data = [
  "周一",
  "周二",
  "周三",
  "周四",
  "周五",
  "周六",
  "周日"
]

[yAxis]
type = "value"

[[series]]
name = "郵件營銷"
type = "line"
stack = "總量"
data = [
  120.0,
  132.0,
  101.0,
  134.0,
  90.0,
  230.0,
  210.0
]

[[series]]
name = "聯盟廣告"
type = "line"
stack = "總量"
data = [
  220.0,
  182.0,
  191.0,
  234.0,
  290.0,
  330.0,
  310.0
]

[[series]]
name = "視頻廣告"
type = "line"
stack = "總量"
data = [
  150.0,
  232.0,
  201.0,
  154.0,
  190.0,
  330.0,
  410.0
]

[[series]]
name = "直接訪問"
type = "line"
stack = "總量"
data = [
  320.0,
  332.0,
  301.0,
  334.0,
  390.0,
  330.0,
  320.0
]

[[series]]
name = "搜索引擎"
type = "line"
stack = "總量"
data = [
  820.0,
  932.0,
  901.0,
  934.0,
  1290.0,
  1330.0,
  1320.0
]
{{</* /echarts */>}}
```

呈現的輸出效果如下:

{{< echarts >}}
{
  "title": {
    "text": "折線統計圖",
    "top": "2%",
    "left": "center"
  },
  "tooltip": {
    "trigger": "axis"
  },
  "legend": {
    "data": ["郵件營銷", "聯盟廣告", "視頻廣告", "直接訪問", "搜索引擎"],
    "top": "10%"
  },
  "grid": {
    "left": "5%",
    "right": "5%",
    "bottom": "5%",
    "top": "20%",
    "containLabel": true
  },
  "toolbox": {
    "feature": {
      "saveAsImage": {
        "title": "保存為圖片"
      }
    }
  },
  "xAxis": {
    "type": "category",
    "boundaryGap": false,
    "data": ["周一", "周二", "周三", "周四", "周五", "周六", "周日"]
  },
  "yAxis": {
    "type": "value"
  },
  "series": [
    {
      "name": "郵件營銷",
      "type": "line",
      "stack": "總量",
      "data": [120, 132, 101, 134, 90, 230, 210]
    },
    {
      "name": "聯盟廣告",
      "type": "line",
      "stack": "總量",
      "data": [220, 182, 191, 234, 290, 330, 310]
    },
    {
      "name": "視頻廣告",
      "type": "line",
      "stack": "總量",
      "data": [150, 232, 201, 154, 190, 330, 410]
    },
    {
      "name": "直接訪問",
      "type": "line",
      "stack": "總量",
      "data": [320, 332, 301, 334, 390, 330, 320]
    },
    {
      "name": "搜索引擎",
      "type": "line",
      "stack": "總量",
      "data": [820, 932, 901, 934, 1290, 1330, 1320]
    }
  ]
}
{{< /echarts >}}

`echarts` shortcode 還有以下命名參數:

* **width** *[可選]* (**第一個**位置參數)

    {{< version 0.2.0 >}} 數據可視化的寬度, 默認值是 `100%`.

* **height** *[可選]* (**第二個**位置參數)

    {{< version 0.2.0 >}} 數據可視化的高度, 默認值是 `30rem`.

## 7 mapbox

{{< version 0.2.0 >}}

[Mapbox GL JS](https://docs.mapbox.com/mapbox-gl-js) 是一個 JavaScript 庫，它使用 WebGL, 以 [vector tiles](https://docs.mapbox.com/help/glossary/vector-tiles/) 和 [Mapbox styles](https://docs.mapbox.com/mapbox-gl-js/style-spec/) 為來源, 將它們渲染成互動式地圖.

`mapbox` shortcode 有以下命名參數來使用 Mapbox GL JS:

* **lng** *[必需]* (**第一個**位置參數)

    地圖初始中心點的經度, 以度為單位.

* **lat** *[必需]* (**第二個**位置參數)

    地圖初始中心點的緯度, 以度為單位.

* **zoom** *[可選]* (**第三個**位置參數)

    地圖的初始縮放級別, 默認值是 `10`.

* **marked** *[可選]* (**第四個**位置參數)

    是否在地圖的初始中心點添加圖釘, 默認值是 `true`.

* **light-style** *[可選]* (**第五個**位置參數)

    淺色主題的地圖樣式, 默認值是[前置參數](../theme-documentation-content#front-matter)或者[網站配置](../theme-documentation-basics#site-configuration)中設置的值.

* **dark-style** *[可選]* (**第六個**位置參數)

    深色主題的地圖樣式, 默認值是[前置參數](../theme-documentation-content#front-matter)或者[網站配置](../theme-documentation-basics#site-configuration)中設置的值.

* **navigation** *[可選]*

    是否添加 [NavigationControl](https://docs.mapbox.com/mapbox-gl-js/api#navigationcontrol), 默認值是[前置參數](../theme-documentation-content#front-matter)或者[網站配置](../theme-documentation-basics#site-configuration)中設置的值.

* **geolocate** *[可選]*

    是否添加 [GeolocateControl](https://docs.mapbox.com/mapbox-gl-js/api#geolocatecontrol), 默認值是[前置參數](../theme-documentation-content#front-matter)或者[網站配置](../theme-documentation-basics#site-configuration)中設置的值.

* **scale** *[可選]*

    是否添加 [ScaleControl](https://docs.mapbox.com/mapbox-gl-js/api#scalecontrol), 默認值是[前置參數](../theme-documentation-content#front-matter)或者[網站配置](../theme-documentation-basics#site-configuration)中設置的值.

* **fullscreen** *[可選]*

   是否添加 [FullscreenControl](https://docs.mapbox.com/mapbox-gl-js/api#fullscreencontrol), 默認值是[前置參數](../theme-documentation-content#front-matter)或者[網站配置](../theme-documentation-basics#site-configuration)中設置的值.

* **width** *[可選]*

    地圖的寬度, 默認值是 `100%`.

* **height** *[可選]*

    地圖的高度, 默認值是 `20rem`.

一個簡單的 `mapbox` 示例:

```markdown
{{</* mapbox 121.485 31.233 12 */>}}
或者
{{</* mapbox lng=121.485 lat=31.233 zoom=12 */>}}
```

呈現的輸出效果如下:

{{< mapbox 121.485 31.233 12 >}}

一個帶有自定義樣式的 `mapbox` 示例:

```markdown
{{</* mapbox -122.252 37.453 10 false "mapbox://styles/mapbox/streets-zh-v1" */>}}
或者
{{</* mapbox lng=-122.252 lat=37.453 zoom=10 marked=false light-style="mapbox://styles/mapbox/streets-zh-v1" */>}}
```

呈現的輸出效果如下:

{{< mapbox -122.252 37.453 10 false "mapbox://styles/mapbox/streets-zh-v1?optimize=true" >}}

## 8 music

`music` shortcode 基於 [APlayer](https://github.com/MoePlayer/APlayer) 和 [MetingJS](https://github.com/metowolf/MetingJS) 提供了一個內嵌的響應式音樂播放器.

有三種方式使用 `music` shortcode.

### 8.1 自定義音樂 URL {#custom-music-url}

{{< version 0.2.10 >}} 支持[本地資源引用](../theme-documentation-content#contents-organization)的完整用法.

`music` shortcode 有以下命名參數來使用自定義音樂 URL:

* **server** *[必需]*

    音樂的鏈接.

* **type** *[可選]*

    音樂的名稱.

* **artist** *[可選]*

    音樂的創作者.

* **cover** *[可選]*

    音樂的封面鏈接.

一個使用自定義音樂 URL 的 `music` 示例:

```markdown
{{</* music url="/music/Wavelength.mp3" name=Wavelength artist=oldmanyoung cover="/images/Wavelength.jpg" */>}}
```

呈現的輸出效果如下:

{{< music url="/music/Wavelength.mp3" name=Wavelength artist=oldmanyoung cover="/images/Wavelength.jpg" >}}

### 8.2 音樂平台 URL 的自動識別 {#automatic-identification}

`music` shortcode 有一個命名參數來使用音樂平台 URL 的自動識別:

* **auto** *[必需]]* (**第一個**位置參數)

    用來自動識別的音樂平台 URL, 支持 `netease`, `tencent` 和 `xiami` 平台.

一個使用音樂平台 URL 的自動識別的 `music` 示例:

```markdown
{{</* music auto="https://music.163.com/#/playlist?id=60198" */>}}
或者
{{</* music "https://music.163.com/#/playlist?id=60198" */>}}
```

呈現的輸出效果如下:

{{< music auto="https://music.163.com/#/playlist?id=60198" >}}

### 8.3 自定義音樂平台, 類型和 ID {#custom-server}

`music` shortcode 有以下命名參數來使用自定義音樂平台:

* **server** *[必需]* (**第一個**位置參數)

    [`netease`, `tencent`, `kugou`, `xiami`, `baidu`]

    音樂平台.

* **type** *[必需]* (**第二個**位置參數)

    [`song`, `playlist`, `album`, `search`, `artist`]

    音樂類型.

* **id** *[必需]* (**第三個**位置參數)

    歌曲 ID, 或者播放列表 ID, 或者專輯 ID, 或者搜索關鍵詞, 或者創作者 ID.

一個使用自定義音樂平台的 `music` 示例:

```markdown
{{</* music server="netease" type="song" id="1868553" */>}}
或者
{{</* music netease song 1868553 */>}}
```

呈現的輸出效果如下:

{{< music netease song 1868553 >}}

### 8.4 其它參數 {#other-parameters}

`music` shortcode 有一些可以應用於以上三種方式的其它命名參數:

* **theme** *[可選]*

    {{< version 0.2.0 changed >}} 音樂播放器的主題色, 默認值是 `#448aff`.

* **fixed** *[可選]*

    是否開啟固定模式, 默認值是 `false`.

* **mini** *[可選]*

    是否開啟迷你模式, 默認值是 `false`.

* **autoplay** *[可選]*

    是否自動播放音樂, 默認值是 `false`.

* **volume** *[可選]*

    第一次打開播放器時的默認音量, 會被保存在瀏覽器緩存中, 默認值是 `0.7`.

* **mutex** *[可選]*

    是否自動暫停其它播放器, 默認值是 `true`.

`music` shortcode 還有一些只適用於音樂列表方式的其它命名參數:

* **loop** *[可選]*

    [`all`, `one`, `none`]

    音樂列表的循環模式, 默認值是 `none`.

* **order** *[可選]*

    [`list`, `random`]

    音樂列表的播放順序, 默認值是 `list`.

* **list-folded** *[可選]*

    初次打開的時候音樂列表是否折疊, 默認值是 `false`.

* **list-max-height** *[可選]*

    音樂列表的最大高度, 默認值是 `340px`.

## 9 bilibili

{{< version 0.2.0 changed >}}

`bilibili` shortcode 提供了一個內嵌的用來播放 bilibili 視頻的響應式播放器.

如果視頻只有一個部分, 則僅需要視頻的 BV `id`, 例如:

```code
https://www.bilibili.com/video/BV1Sx411T7QQ
```

一個 `bilibili` 示例:

```markdown
{{</* bilibili BV1Sx411T7QQ */>}}
或者
{{</* bilibili id=BV1Sx411T7QQ */>}}
```

呈現的輸出效果如下:

{{< bilibili id=BV1Sx411T7QQ >}}

如果視頻包含多個部分, 則除了視頻的 BV `id` 之外, 還需要 `p`, 默認值為 `1`, 例如:

```code
https://www.bilibili.com/video/BV1TJ411C7An?p=3
```

一個帶有 `p` 參數的 `bilibili` 示例:

```markdown
{{</* bilibili BV1TJ411C7An 3 */>}}
或者
{{</* bilibili id=BV1TJ411C7An p=3 */>}}
```

呈現的輸出效果如下:

{{< bilibili id=BV1TJ411C7An p=3 >}}

## 10 typeit

`typeit` shortcode 基於 [TypeIt](https://typeitjs.com/) 提供了打字動畫.

只需將你需要打字動畫的內容插入 `typeit` shortcode 中即可.

### 10.1 簡單內容 {#simple-content}

允許使用 `Markdown` 格式的簡單內容, 並且 **不包含** 富文本的塊內容, 例如圖像等等...

一個 `typeit` 示例:

```markdown
{{</* typeit */>}}
這一個帶有基於 [TypeIt](https://typeitjs.com/) 的 **打字動畫** 的 *段落*...
{{</* /typeit */>}}
```

呈現的輸出效果如下:

{{< typeit >}}
這一個帶有基於 [TypeIt](https://typeitjs.com/) 的 **打字動畫** 的 *段落*...
{{< /typeit >}}

另外, 你也可以自定義 **HTML 標簽**.

一個帶有 `h4` 標簽的 `typeit` 示例:

```markdown
{{</* typeit tag=h4 */>}}
這一個帶有基於 [TypeIt](https://typeitjs.com/) 的 **打字動畫** 的 *段落*...
{{</* /typeit */>}}
```

呈現的輸出效果如下:

{{< typeit tag=h4 >}}
這一個帶有基於 [TypeIt](https://typeitjs.com/) 的 **打字動畫** 的 *段落*...
{{< /typeit >}}

### 10.2 代碼內容 {#code-content}

代碼內容也是允許的, 並且通過使用參數 `code` 指定語言類型可以實習語法高亮.

一個帶有 `code` 參數的 `typeit` 示例:

```markdown
{{</* typeit code=java */>}}
public class HelloWorld {
    public static void main(String []args) {
        System.out.println("Hello World");
    }
}
{{</* /typeit */>}}
```

呈現的輸出效果如下:

{{< typeit code=java >}}
public class HelloWorld {
    public static void main(String []args) {
        System.out.println("Hello World");
    }
}
{{< /typeit >}}

### 10.3 分組內容 {#group-content}

默認情況下, 所有打字動畫都是同時開始的.
但是有時你可能需要按順序開始一組 `typeit` 內容的打字動畫.

一組具有相同 `group` 參數值的 `typeit` 內容將按順序開始打字動畫.

一個帶有 `group` 參數的 `typeit` 示例:

```markdown
{{</* typeit group=paragraph */>}}
**首先**, 這個段落開始
{{</* /typeit */>}}

{{</* typeit group=paragraph */>}}
**然後**, 這個段落開始
{{</* /typeit */>}}
```

呈現的輸出效果如下:

{{< typeit group=paragraph >}}
**首先**, 這個段落開始
{{< /typeit >}}

{{< typeit group=paragraph >}}
**然後**, 這個段落開始
{{< /typeit >}}

## 11 script

{{< version 0.2.8 >}}

`script` shortcode 用來在你的文章中插入 **:(fab fa-js fa-fw): Javascript** 腳本.

{{< admonition >}}
腳本內容可以保證在所有的第三方庫加載之後按順序執行.
所以你可以自由地使用第三方庫.
{{< /admonition >}}

一個 `script` 示例:

```markdown
{{</* script */>}}
console.log('Hello FeelIt!');
{{</* /script */>}}
```

你可以在開發者工具的控制台中看到輸出.

{{< script >}}
console.log('Hello FeelIt!');
{{< /script >}}

## 12 oEmbed

{{< version 1.0.1 changed >}}

oEmbed endpoints allow you to get embed HTML and basic metadata for pages, posts, and videos in order to display them in another website or app. The oEmbed endpoints require either an App Access Token or Client Access Token.

### 12.1 oEmbed Facebook
{{< version 1.0.1 >}}

**a. oEmbed Facebook Pages**

Sample input of Facebook Pages
```markdown
{{</* oembed "fb" "page" "https://www.facebook.com/FacebookforDevelopers" */>}}
```

Sample output of Facebook Pages

{{< oembed "fb" "page" "https://www.facebook.com/FacebookforDevelopers" >}}

URL Formats

```html
https://www.facebook.com/{page-name}
https://www.facebook.com/{page-id}
```

**b. oEmbed Facebook Posts**

Sample input of Facebook Posts

```markdown
{{</* oembed "fb" "post" "https://www.facebook.com/FacebookforDevelopers/photos/a.441861428552/10151617410093553" */>}}
```

Sample output of Facebook Posts

{{< oembed "fb" "post" "https://www.facebook.com/FacebookforDevelopers/photos/a.441861428552/10151617410093553" >}}

URL Formats

```html
https://www.facebook.com/{page-name}/posts/{post-id}
https://www.facebook.com/{username}/posts/{post-id}
https://www.facebook.com/{username}/activity/{activity-id}
https://www.facebook.com/photo.php?fbid={photo-id}
https://www.facebook.com/photos/{photo-id}
https://www.facebook.com/permalink.php?story_fbid={post-id}&id={page-or-user-id}
https://www.facebook.com/media/set?set={set-id}
https://www.facebook.com/questions/{question-id}
https://www.facebook.com/notes/{username}/{note-url}/{note-id}
```

**c. oEmbed Facebook Videos**

Sample input of Facebook Videos

```markdown
{{</* oembed "fb" "video" "https://www.facebook.com/FacebookforDevelopers/videos/2201055573317594" */>}}
```

Sample output of Facebook Videos

{{< oembed "fb" "video" "https://www.facebook.com/FacebookforDevelopers/videos/2201055573317594" >}}

URL Formats

```html
https://www.facebook.com/{page-name}/videos/{video-id}/
https://www.facebook.com/{username}/videos/{video-id}/
https://www.facebook.com/video.php?id={video-id}
https://www.facebook.com/video.php?v={video-id}
```

### 12.2 oEmbed Instagram
{{< version 1.0.0 >}}

**a. oEmbed Instagram Post**

Sample input of Instagram Post

```markdown
{{</* oembed "ig" "p" "BWNjjyYFxVx" "hidecaption" */>}}
```

Sample output of Instagram Post

{{< oembed "ig" "p" "BWNjjyYFxVx" "hidecaption" >}}

**b. oEmbed Instagram TV**

Sample input of Instagram TV

```markdown
{{</* oembed "ig" "tv" "BkQUbR8h1sp" "hidecaption" */>}}
```

Sample output of Instagram TV

{{< oembed "ig" "tv" "BkQUbR8h1sp" "hidecaption" >}}

### 12.3 oEmbed Twitter
{{< version 1.0.1 >}}

Sample input of 'oembed tweet'

```markdown
{{</* oembed "tweet" "https://twitter.com/GoHugoIO/status/877500564405444608" */>}}
```

Sample output of 'oembed tweet'

{{< oembed "tweet" "https://twitter.com/GoHugoIO/status/877500564405444608" >}}
