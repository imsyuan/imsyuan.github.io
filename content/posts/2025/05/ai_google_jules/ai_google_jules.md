+++
date = '2025-05-21T00:47:43+08:00'
draft = false
title = 'Google 重磅推出 Jules AI 對決 OpenAI 的 Codex'
description = ''
author = 'Steven Chang'
slug = 'ai_google_jules'
archive = ['2025']
tags = ['AI', 'Google', 'Jules', 'OpenAI', 'Codex', 'Filament']
categories = ['AI']
cover = { image = '/posts/2025/05/ai_google_jules/cover.png', alt = 'Cover Image'}
+++

## Google 重磅推出 Jules AI 對決 OpenAI 的 Codex

Google 在 2025 年 5 月 21 日宣布推出其最新的 AI 編程助手 Jules，旨在與 OpenAI 的 Codex 競爭。Jules 是一個強大的 AI 編程助手，專為開發者設計，能夠理解自然語言指令並生成高質量的程式碼。

以下就來試試看，如何透過 Jules 來幫我的一個 Filament 專案，加上我想要的功能。

我已經安裝 `spatie-laravel-settings-plugin` Plugin，我想讓 `EmployeeResource.php` 這個檔案裡面的其中一個欄位，能夠透過設定檔來控制。

### 開啟 Jules 並且連結 Github
- 開啟 [Google Jules](https://jules.google.com/task)
![jules_auth_github.png](/posts/2025/05/ai_google_jules/jules_auth_github.png)

- 選擇 Repo
![select-repo.png](/posts/2025/05/ai_google_jules/select-repo.png)


### 根據我的 prompt 生成我需要的程式碼
```prompt
feat: assign config value $hr_base_salary to Employee resource's base_salary
```

- 過程中會出現一些 error，Jules 嘗試自動修復環境問題。
![debug.png](/posts/2025/05/ai_google_jules/debug.png)

- 最後成功生成了我需要的程式碼，並且詢問要不要開新分支
![scuess.png](/posts/2025/05/ai_google_jules/scuess.png)

- 在我的 Github Repo 中可以看到 Jules 自動生成的分支
![create-branch.png](/posts/2025/05/ai_google_jules/create-branch.png)

### 總結
整體來說算是一個不錯的體驗，Jules 能夠快速理解我的需求並生成相應的程式碼。雖然在過程中遇到了一些錯誤，但它能夠自動修復並繼續進行。這對於開發者來說是一個很大的幫助，特別是在處理複雜的專案時。
我相信一個好的且明確的 prompt 可能會影響到 AI 執行的結果。這或許是我們需要多加學習如何當一個好的 prompt engineerㄅㄠ。
