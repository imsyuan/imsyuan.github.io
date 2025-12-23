+++
date = '2025-12-22T11:09:33+08:00'
draft = false
title = '如何使用 N8N 搭配 Google Drive + RAG 建立屬於企業自己的 Line 客服 AI 機器人'
description = ''
author = 'Steven Chang'
slug = 'n8n-rag-linaioa'
archive = ['2025']
tags = ['N8N', 'RAG', 'Line', 'AI', '企業客服機器人']
categories = ['N8N', 'RAG', 'AI', 'Line']
cover = { image = '/posts/2025/12/n8n-rag-lineaioa/cover.png', alt = 'Cover Image'}
+++

最近看了許多文章、影片，都在探討 RAG 的應用，所以研究了一下 N8N 如何建立自己的 RAG 系統，
並且結合 Line 來打造一個客服 AI 機器人，這篇文章就來分享一下整個流程和步驟。

由於 AI 領域的更新，可能是以天或者甚至以小時為單位在變化，參考了很多影片後，會發現不一定時做得出來你想要的需求

總共會使用到的服務有 `N8N`、`Line OA`、`Gemini AI`、`Pinecone`、`Google Drive`，這些服務大部分都有免費額度可以使用

## 建立 Line 帳號

首先進入到 Line OA 管理後台，建立自己一個新的官方帳號 [Line OA 管理](https://manager.line.biz/)

![create-line-oa.png](/posts/2025/12/n8n-rag-lineaioa/create-line-oa.png)

取得 `Channel Access Token` 和 `Webhook` 以及點擊下方的 LINE Developers Console 可以找到 `Channel Secret`
這 3 個資訊在後續 N8N 的設定中會用到。

![line-access-secret-token.png](/posts/2025/12/n8n-rag-lineaioa/line-access-secret-token.png)

## RAG 介紹

![rag.png](/posts/2025/12/n8n-rag-lineaioa/rag.png)

RAG (Retrieval-Augmented Generation) 是一種結合檢索和生成的技術，主要用於提升自然語言處理模型的性能。
在大語言模型中經常會出現「幻覺」(Hallucination) 的問題，RAG 透過引入外部知識庫來減少這種現象。

主要分成三個部分：
1. **檢索 (Retrieval)**：從外部知識庫中檢索相關資訊，這些資訊可以是文件、文章、數據等。
2. **增強 (Augmentation)**：將檢索到的資訊與輸 入的查詢結合起來，形成一個更豐富的上下文。
3. **生成 (Generation)**：使用自然語言生成模型來產生回答，

RAG 與 生成式 AI (Generative AI) 的主要區別於 RAG 透過資料庫中的資訊來輔助回答問題，
而生成式 AI (例如 GPT 等)則主要依賴於模型本身的訓練數據來產生內容，但不一定保證內容正確性

## N8N 設定

這次總共會使用到兩個 Flow，一個是用來建立 RAG 的流程，另外一個是用來處理 Line 的訊息，

### RAG Flow 將資料切片放入 Pinecone
![n8n-flow1.png](/posts/2025/12/n8n-rag-lineaioa/n8n-flow1.png)

主要是將資料放於 Google Drive 透過 N8N 裡面的 Google drive 節點來讀取檔案內容，接下來就要使用 Pinecone 來建立向量資料庫
1. 申請 [Pinecone](https://app.pinecone.io) 帳號
2. 建立一個 Index，選擇一個 model 作為向量模型
   ![pinecone-create-index.png](/posts/2025/12/n8n-rag-lineaioa/pinecone-create-index.png)
3. 根據圖中拉取 `Search files and folders` 節點來讀取 Google Drive 的檔案
4. `Download file` 節點來下載檔案內容
5. `Pinecone Vector Store` 節點來將檔案內容上傳至 Pinecone 向量資料庫
    - 建立 Pinecone Credential，輸入 API Key 和 Environment
    - `Pinecone Index` 選擇剛剛在 Pinecone 建立的 Index
   ![n8n-pinecone-setting.37.31.png](/posts/2025/12/n8n-rag-lineaioa/n8n-pinecone-setting.png)
6. `Embedding Model` 選擇 `Google Gemini Embeddings`
   ![n8n-gemini-embedding-setting.png](/posts/2025/12/n8n-rag-lineaioa/embedding-google-gemini.png)
7. `Data Loader`
   ![n8n-embedding-data-loader.png](/posts/2025/12/n8n-rag-lineaioa/n8n-embedding-data-loader.png)
8. 下方要加上一個 `Recursive Character Text Splitter`
   這邊就是要選擇要切割的方式，可以根據文件的特性來選擇適合的切割器，以及大小
   ![n8n-embedding-Splitter.png](/posts/2025/12/n8n-rag-lineaioa/n8n-embedding-Splitter.png)
9. 執行後就會將 Google Drive 裡面的檔案內容上傳至 Pinecone 向量資料庫
   ![pinecone-index-result.png](/posts/2025/12/n8n-rag-lineaioa/pinecone-index-result.png)

### 整合 Line & AI & RAG Flow

![n8n-flow2.png](/posts/2025/12/n8n-rag-lineaioa/n8n-flow2.png)

1. Webhook 節點，設定 Method 為 POST，這邊的網址就是要放在 Line OA 裡的 Messaging API `Webhook URL` 裡面
2. AI Agent 節點，設定主要的 `System Prompt`，這邊可以根據自己的需求來設定
   ![flow2-ai-agent-setting.png](/posts/2025/12/n8n-rag-lineaioa/flow2-ai-agent-setting.png)
3. AI Agent 下方的 Chat model 我是選用 OpenRouter Chat Model 中的 `Gemini-2.0-flash-001`
4. AI Agent 的 Tools 下要新增一個 `Pinecone Vector Store`，這邊要設定剛剛建立的 Pinecone Credential 以及 Index
![n8n-flow2-pinecone-setting.png](/posts/2025/12/n8n-rag-lineaioa/n8n-flow2-pinecone-setting.png)
5. 下方的 Embedding Model 也是選用 `Google Gemini Embeddings`
6. 最後一個節點是 `Line` 節點，這邊要設定剛剛取得的 `Channel Access Token` 和 `Channel Secret`
   - `URL` : `https://api.line.me/v2/bot/message/reply`
   - `Method` : `POST`
   - `Generic Auth Type` : `Bearer Token`
   - `Send Body`
   - `Body Content Type` : `JSON`
   - `Body Parameters` :
   - `replyToken` : 
    ```text
    {
      "replyToken":"{{ $('Webhook').item.json.body.events[0].replyToken }}",
      "messages":[
        {
          "type":"text",
          "text": {{ JSON.stringify($json.output) }}
        }
      ]
    }
     ```
   
## 結果
![result-line1.png](/posts/2025/12/n8n-rag-lineaioa/result-line1.png)
![result-line2.png](/posts/2025/12/n8n-rag-lineaioa/result-line2.png)
