+++
date = '2025-06-18T22:41:02+08:00'
draft = false
title = '如何在 Hugo 中安裝 Disqus 系統，以 PaperMod 主題為例'
description = ''
author = 'Steven Chang'
slug = 'hugo_comments_disqus_install'
archive = ['2025']
tags = ['hugo', 'disqus', 'comments']
categories = ['hugo']
cover = { image = '/posts/2025/06/hugo_comments_disqus_install/cover.png', alt = 'Cover Image'}
+++

這邊記錄一下如何在 Hugo 中安裝 Disqus 評論系統，佈景主題為 PaperMod。

## 安裝步驟

1. 在 [Disqus](https://disqus.com/) 註冊帳號並創建一個新的網站。
2. 在 Disqus 的網站設定中，找到 `Shortname`，這是用來識別你的網站的。
3. 在你的 Hugo 網站的 `config.toml` 或 `config.yaml` 中添加以下配置：

   ```toml
   [params]
     disqusShortname = "your_disqus_shortname"
     comments = true
   ```

   或者如果你使用的是 YAML 格式：

   ```yaml
   params:
     disqusShortname: your_disqus_shortname
     comments: true
   ```
4. 確保你的 Hugo 佈景主題支持 Disqus。PaperMod 佈景主題已經內建支持 Disqus，所以只需要確保上述配置正確即可。
5. 複製 `themes/PaperMod/layouts/partials/comments.html` 到 `layouts/partials/comments.html` 確認 Disqus 的代碼已經包含在內。

   如果沒有，你可以手動添加以下代碼到你的 `layouts/partials/comments.html`：

```html
    {{- /* Comments area start */ -}}
    <div class="disqus markdown">
        {{ partial "disqus.html" . }}
    </div>
    {{- /* Comments area end */ -}}
```

6. 建立 `layouts/partials/disqus.html` 檔案，並添加以下內容：

```html
    {{ if .Site.Params.disqusShortname }}
        <div id="disqus_thread"></div>
        <script>
            var disqus_config = function () {
                this.page.url = '{{ .Permalink }}'; // Replace PAGE_URL with your page's canonical URL variable
                this.page.identifier = '{{ .File.Path }}'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
            };
            (function() { // DON'T EDIT BELOW THIS LINE
                var d = document, s = d.createElement('script');
                s.src = 'https://{{ .Site.Params.disqusShortname }}.disqus.com/embed.js';
                s.setAttribute('data-timestamp', +new Date());
                (d.head || d.body).appendChild(s);
            })();
        </script>
        <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
    {{ end }}
```

7. 在 local 確認是否有完成