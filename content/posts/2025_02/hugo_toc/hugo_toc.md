+++
date = '2025-02-12T11:01:51+08:00'
draft = false
title = '如何將 Hugo 文章目錄移到側邊'
description = "如何將 Hugo 文章目錄移到側邊"
author = "Steven Chang"
slug = "hugo_toc"
archive = ['2025']
tags = ['hugo', 'blog', 'toc']
categories = ['Tech']
cover = { image = '/posts/2025_02/hugo_toc/cover.png', alt = 'Cover Image for toc'}
+++

## 如何將 Hugo 文章目錄移到側邊

### 產生 custom 的 css

產生一個 `blank.css`
```bash
touch assets/css/extended/blank.css
```

產生的檔案位置會如下

```
assets
└── css
    └── extended
        └── blank.css
```

貼上以下的 css 內容

```css
:root {
    --nav-width: 1380px;
    --article-width: 650px;
    --toc-width: 300px;
}

.toc {
    margin: 0 2px 40px 2px;
    border: 1px solid var(--border);
    background: var(--entry);
    border-radius: var(--radius);
    padding: 0.4em;
}

.toc-container.wide {
    position: absolute;
    height: 100%;
    border-right: 1px solid var(--border);
    left: calc((var(--toc-width) + var(--gap)) * -1);
    top: calc(var(--gap) * 2);
    width: var(--toc-width);
}

.wide .toc {
    position: sticky;
    top: var(--gap);
    border: unset;
    background: unset;
    border-radius: unset;
    width: 100%;
    margin: 0 2px 40px 2px;
}

.toc details summary {
    cursor: zoom-in;
    margin-inline-start: 20px;
    padding: 12px 0;
}

.toc details[open] summary {
    font-weight: 500;
}

.toc-container.wide .toc .inner {
    margin: 0;
}

.active {
    font-size: 110%;
    font-weight: 600;
}

.toc ul {
    list-style-type: circle;
}

.toc .inner {
    margin: 0 0 0 20px;
    padding: 0px 15px 15px 20px;
    font-size: 16px;

    /*目录显示高度*/
    max-height: 83vh;
    overflow-y: auto;
}

.toc .inner::-webkit-scrollbar-thumb {  /*滚动条*/
    background: var(--border);
    border: 7px solid var(--theme);
    border-radius: var(--radius);
}

.toc li ul {
    margin-inline-start: calc(var(--gap) * 0.5);
    list-style-type: none;
}

.toc li {
    list-style: none;
    font-size: 0.95rem;
    padding-bottom: 5px;
}

.toc li a:hover {
    color: var(--secondary);
}

```

### 產生 layouts 的 html

產生一個 `toc.html`
```bash
touch layouts/partials/toc.html
```

產生的檔案位置會如下

```
layouts
└── partials
    └── toc.html
```

貼上以下的 html 內容

```html
{{- $headers := findRE "<h[1-6].*?>(.|\n])+?</h[1-6]>" .Content -}}
{{- $has_headers := ge (len $headers) 1 -}}
{{- if $has_headers -}}
<aside id="toc-container" class="toc-container wide">
    <div class="toc">
        <details {{if (.Param "TocOpen") }} open{{ end }}>
        <summary accesskey="c" title="(Alt + C)">
            <span class="details">{{- i18n "toc" | default "Table of Contents" }}</span>
        </summary>

        <div class="inner">
            {{- $largest := 6 -}}
            {{- range $headers -}}
            {{- $headerLevel := index (findRE "[1-6]" . 1) 0 -}}
            {{- $headerLevel := len (seq $headerLevel) -}}
            {{- if lt $headerLevel $largest -}}
            {{- $largest = $headerLevel -}}
            {{- end -}}
            {{- end -}}

            {{- $firstHeaderLevel := len (seq (index (findRE "[1-6]" (index $headers 0) 1) 0)) -}}

            {{- $.Scratch.Set "bareul" slice -}}
            <ul>
                {{- range seq (sub $firstHeaderLevel $largest) -}}
                <ul>
                    {{- $.Scratch.Add "bareul" (sub (add $largest .) 1) -}}
                    {{- end -}}
                    {{- range $i, $header := $headers -}}
                    {{- $headerLevel := index (findRE "[1-6]" . 1) 0 -}}
                    {{- $headerLevel := len (seq $headerLevel) -}}

                    {{/* get id="xyz" */}}
                    {{- $id := index (findRE "(id=\"(.*?)\")" $header 9) 0 }}

                    {{- /* strip id="" to leave xyz, no way to get regex capturing groups in hugo */ -}}
                    {{- $cleanedID := replace (replace $id "id=\"" "") "\"" "" }}
                    {{- $header := replaceRE "<h[1-6].*?>((.|\n])+?)</h[1-6]>" "$1" $header -}}

                    {{- if ne $i 0 -}}
                    {{- $prevHeaderLevel := index (findRE "[1-6]" (index $headers (sub $i 1)) 1) 0 -}}
                    {{- $prevHeaderLevel := len (seq $prevHeaderLevel) -}}
                    {{- if gt $headerLevel $prevHeaderLevel -}}
                    {{- range seq $prevHeaderLevel (sub $headerLevel 1) -}}
                    <ul>
                        {{/* the first should not be recorded */}}
                        {{- if ne $prevHeaderLevel . -}}
                        {{- $.Scratch.Add "bareul" . -}}
                        {{- end -}}
                        {{- end -}}
                        {{- else -}}
                        </li>
                        {{- if lt $headerLevel $prevHeaderLevel -}}
                        {{- range seq (sub $prevHeaderLevel 1) -1 $headerLevel -}}
                        {{- if in ($.Scratch.Get "bareul") . -}}
                    </ul>
                    {{/* manually do pop item */}}
                    {{- $tmp := $.Scratch.Get "bareul" -}}
                    {{- $.Scratch.Delete "bareul" -}}
                    {{- $.Scratch.Set "bareul" slice}}
                    {{- range seq (sub (len $tmp) 1) -}}
                    {{- $.Scratch.Add "bareul" (index $tmp (sub . 1)) -}}
                    {{- end -}}
                    {{- else -}}
                </ul>
                </li>
                {{- end -}}
                {{- end -}}
                {{- end -}}
                {{- end }}
                <li>
                    <a href="#{{- $cleanedID -}}" aria-label="{{- $header | plainify -}}">{{- $header | safeHTML -}}</a>
                    {{- else }}
                <li>
                    <a href="#{{- $cleanedID -}}" aria-label="{{- $header | plainify -}}">{{- $header | safeHTML -}}</a>
                    {{- end -}}
                    {{- end -}}
                    <!-- {{- $firstHeaderLevel := len (seq (index (findRE "[1-6]" (index $headers 0) 1) 0)) -}} -->
                    {{- $firstHeaderLevel := $largest }}
                    {{- $lastHeaderLevel := len (seq (index (findRE "[1-6]" (index $headers (sub (len $headers) 1)) 1) 0)) }}
                </li>
                {{- range seq (sub $lastHeaderLevel $firstHeaderLevel) -}}
                {{- if in ($.Scratch.Get "bareul") (add . $firstHeaderLevel) }}
            </ul>
            {{- else }}
            </ul>
            </li>
            {{- end -}}
            {{- end }}
            </ul>
        </div>
        </details>
    </div>
</aside>
<script>
    let activeElement;
    let elements;
    window.addEventListener('DOMContentLoaded', function (event) {
        checkTocPosition();

        elements = document.querySelectorAll('h1[id],h2[id],h3[id],h4[id],h5[id],h6[id]');
        // Make the 2025_02 header active
        activeElement = elements[0];
        const id = encodeURI(activeElement.getAttribute('id')).toLowerCase();
        document.querySelector(`.inner ul li a[href="#${id}"]`).classList.add('active');
    }, false);

    window.addEventListener('resize', function(event) {
        checkTocPosition();
    }, false);

    window.addEventListener('scroll', () => {
        // Check if there is an object in the top half of the screen or keep the last item active
        activeElement = Array.from(elements).find((element) => {
            if ((getOffsetTop(element) - window.pageYOffset) > 0 &&
                (getOffsetTop(element) - window.pageYOffset) < window.innerHeight/2) {
                return element;
            }
        }) || activeElement

        elements.forEach(element => {
            const id = encodeURI(element.getAttribute('id')).toLowerCase();
            if (element === activeElement){
                document.querySelector(`.inner ul li a[href="#${id}"]`).classList.add('active');
            } else {
                document.querySelector(`.inner ul li a[href="#${id}"]`).classList.remove('active');
            }
        })
    }, false);

    const main = parseInt(getComputedStyle(document.body).getPropertyValue('--article-width'), 10);
    const toc = parseInt(getComputedStyle(document.body).getPropertyValue('--toc-width'), 10);
    const gap = parseInt(getComputedStyle(document.body).getPropertyValue('--gap'), 10);

    function checkTocPosition() {
        const width = document.body.scrollWidth;

        if (width - main - (toc * 2) - (gap * 4) > 0) {
            document.getElementById("toc-container").classList.add("wide");
        } else {
            document.getElementById("toc-container").classList.remove("wide");
        }
    }

    function getOffsetTop(element) {
        if (!element.getClientRects().length) {
            return 0;
        }
        let rect = element.getBoundingClientRect();
        let win = element.ownerDocument.defaultView;
        return rect.top + win.pageYOffset;
    }
</script>
{{- end }}

```

結果如下

![hugo_toc_exampleF.png](/posts/2025_02/hugo_toc/hugo_toc_example.png)