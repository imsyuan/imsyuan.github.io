+++
date = '2025-03-19T15:09:06+08:00'
draft = false
title = 'Mermaid 流程圖轉換到 draw.io 使用'
description = ''
author = 'Steven Chang'
slug = 'mermaid_to_drawio'
archive = ['2025']
tags = ['drawio', 'mermaid', 'ermode', 'AI', 'GPT']
categories = ['drawio']
cover = { image = '/posts/2025/03/mermaid_to_drawio/cover.png', alt = 'Cover Image'}
+++

## 如何將 Mermaid 轉換到 draw.io

### 先談談什麼是 Mermaid
Mermaid 是 [Mermaid.js](https://github.com/mermaid-js/mermaid)，它是一個 JavaScript-based 用來畫流程圖、甘特圖、時序圖等的開源圖表語言。可以透過簡單的文字描述來產生圖表。
很常使用在 Markdown 文件中，例如 Github 中的 readme.md 文件。

### 為什麼要這樣做呢？
因為我慣用的是 draw.io，有時候後透過 GPT 產生的流程圖，可以直接複製貼上到 draw.io。

 
### 插入 Mermaid 流程圖

![drawio_insert.png](/posts/2025/03/mermaid_to_drawio/drawio_insert.png)

![insert_syntax.png](/posts/2025/03/mermaid_to_drawio/insert_syntax.png)

![result.png](/posts/2025/03/mermaid_to_drawio/result.png)

[Reference](https://mermaid-js.github.io/mermaid/#/flowchart)

```php
echo "Hello, Hugo!";
```