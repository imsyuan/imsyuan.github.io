+++
date = '2025-05-08T00:37:52+08:00'
draft = false
title = '如何讓 Obsidian Canva 的文字置中'
description = ''
author = 'Steven Chang'
slug = 'obsidian_canva_center_text'
archive = ['2025']
tags = ['obsidian', 'canva', 'center text']
categories = ['obsidian']
cover = { image = '/posts/2025/05/obsidian_canva_center_text/cover.png', alt = 'Cover Image'}
+++

### 透過新增 CSS snippets

1. 進入 **Obsidian Settings**

2. 在彈出的視窗中，前往「外觀」(**Appearance**)，然後向下捲動直到你看到「CSS 程式碼片段」(**CSS snippets**)。

3. 你應該會在右側看到一個資料夾圖示，點擊它以開啟程式碼片段資料夾。

4. 這時 Windows 資料夾應該會跳出，在空白處點右鍵，選擇「新增」->「文字文件」。

5. 將這個文字文件重新命名為 Canvas-Snippets.css（系統可能會詢問你是否確定要重新命名，請確認）。

6. 雙擊這個檔案，然後將以下提供的 CSS 程式碼貼上並儲存（關閉檔案並確認儲存變更）。

```css
.canvas-node-content:not(.is-loaded):not(:has(.has-list-bullet)) {
    text-align: center;
}
```

7. 關閉資料夾視窗，別忘了啟用剛剛建立的 Canvas-Snippets（此時你的 Obsidian 設定應該還是開著的）。

![setting_css_snippets.png](/posts/2025/05/obsidian_canva_center_text/setting_css_snippets.png)


[Ref](https://www.reddit.com/r/ObsidianMD/comments/179un8s/comment/l8ulmgq/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)