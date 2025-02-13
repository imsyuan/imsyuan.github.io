+++
date = '2025-02-07T23:37:02+08:00'
draft = false
description = '完整的 Hugo 架設 Blog 教學'
author = 'Steven Chang'
title = '使用 Hugo 架設 Blog'
slug = 'hugo-deploy'
archive = ['2025']
tags = ['hugo', 'blog']
categories = ['Tech']
cover = { image = '/posts/2025/02/deploy_hugo/cover.png', alt = 'Cover Image'}
+++

## 架設 Hugo 起手式

- 環境： Mac OS Sequoia 15.3
- Terminal: iTerm2

### 安裝 Hugo

```bash
brew install hugo
```

### HUGO 指令測試
安裝完成後，在 Terminal 輸入以下指令，確認是否安裝成功。

```bash
hugo version
```
![carbon.png](/posts/2025/02/deploy_hugo/carbon.png)

### 建立新 Blog

```bash
hugo new site blog
```

### 建立新文章
為了方便管理，我會將文章放在 posts 資料夾中。
```bash 
hugo new posts/2025/2025.md
```

### 啟動 HUGO 伺服器
`-D` 參數可以讓草稿文章也顯示在網頁上。

```bash
hugo server -D
```

![hugo_server_start.png](/posts/2025/02/deploy_hugo/hugo_server_start.png)

開啟 `http://localhost:1313/` 即可看到 Hugo 預設的網頁。

## 安裝佈景主題
進入 [Hugo Themes](https://themes.gohugo.io/) 選擇一個喜歡的主題，將它加入到 Blog 中。 這邊選擇 [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 主題。
透過官方推薦的將 submodule 加入到專案中。
```bash
git init
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
```

### 修改 hugo.toml
```toml
# 新增 theme
theme = 'PaperMod'
```

## Local Build 出可以部署的檔案
```bash
## --gc: 執行完後清除 cache
## --minify: 壓縮檔案
hugo --gc --minify
```

## 部署到 Github Pages

在 Github 上建立一個新的 Repository，名稱為 `{你的 Github id 名稱}.github.io`。

![create_github_repo.png](/posts/2025/02/deploy_hugo/create_github_repo.png)

```bash
## Git init
git branch -M main

## 新增 remote
git remote add origin https://github.com/{你的Github id 名稱}/{你的 Github id 名稱}.github.io.git

## 將檔案加入到 git
git add .
git commit -m "Hugo 首次 commit"

## 將檔案推到 Github
git push -u origin main
```

接著進入 `https://{你的 Github id 名稱}.github.io` 即可看到你的 Blog。 
