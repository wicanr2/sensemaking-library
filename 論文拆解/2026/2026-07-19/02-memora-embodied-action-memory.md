# MEMORA: Embodied Action Memory from Egocentric Videos for Reasoning and Planning

## 原文資訊
- 論文：MEMORA: Embodied Action Memory from Egocentric Videos for Reasoning and Planning
- 作者：Zihao Yu, Xiu Yuan, Chongjie Zhang
- arXiv ID：2607.14252v1
- 分類：Robotics (cs.RO); Artificial Intelligence (cs.AI); Computation and Language (cs.CL)
- 發表 / 更新：Submitted on 15 Jul 2026
- 連結：[abs](https://arxiv.org/abs/2607.14252v1) / [pdf](https://arxiv.org/pdf/2607.14252v1)
- 本次閱讀範圍：Summary/Abstract + Introduction（arXiv HTML 可取得；Introduction 另以 PDF 文字抽取交叉確認；未讀 methods / experiments / results）
- 擷取日期：2026-07-19

## 為什麼選這篇

這篇是很明確的 LLM + Robotics / Embodied AI 交會題：它不是直接訓練一個低階 policy，而是問 long-horizon robot planning 需要什麼樣的「經驗記憶」。對機器人來說，未來目標常常不是只靠當下畫面就能解釋；它可能需要知道某個人平常把工具放哪裡、物件曾經怎麼變化、哪些程序反覆出現。

我選它的原因，是它把 egocentric video 的用途從「轉成示範資料或軌跡」拉到另一層：把第一人稱經驗整理成 persistent semantic / procedural memory，再供 planner 查詢。這對 embodied agents 很重要，因為許多 agent 架構其實缺少一個介於影片資料與即時控制之間的記憶層。

它也和近期 LLM agent memory 的討論互相呼應，但多了物理世界約束：地點會持續存在、物件會改變狀態但保留身份、動作有時間順序、慣例要跨事件累積才看得出來。這些不是一般聊天記憶或文件 RAG 可以直接處理好的問題。

## 一句話理解

MEMORA 想把連續第一人稱經驗轉成可編輯、可鞏固、可檢索的 embodied action memory，讓語言模型式 robot planner 在面對未來目標時能利用過去的地點、物件狀態、活動程序與習慣。

## Summary / Abstract 說了什麼

摘要指出，長程 robot planning 不只需要預測下一步動作，也需要能讓未來目標變得可解釋的 embodied experience memory。作者將這種能力定義為 Embodied Action Memory（EAM）：形成、維護並使用持久記憶狀態，以支援後續決策。

MEMORA 的設計包含 formation、consolidation、retrieval lifecycle，並維護四種 typed stores：Environment Memory、Entity Memory、Activity Memory、Inferred Knowledge。摘要說，online editing 負責在新觀測進來時維持物件身份與狀態歷史；offline consolidation 則把重複經驗抽象成可重用程序與參與者特定規律。

摘要還提出 MEMORA-Bench：使用 EPIC-KITCHENS-100 extension video 的 45 小時資料、18 位參與者，評估 memory-grounded planning 與 memory-assessment。作者宣稱完整 MEMORA 在四個 open-weight language models 上得到最強 aggregate results，並在 memory assessment 與 out-of-distribution Robot-Grounded Plan score 上有提升。本次筆記未讀實驗章節，因此這些結果只作為作者宣稱記錄。

## Introduction 的問題設定

Introduction 先指出 foundation-model robot planners 可以把語言目標、場景觀測與 retrieved demonstrations 轉成 action sequences；但在熟悉環境中的長程 planning 還依賴累積經驗形成的記憶。例如，合適的行動可能取決於某位參與者常把工具放在哪裡、物件狀態如何變化、過去出現過哪些程序、以及重複行為透露出什麼規律。

作者接著借用認知科學中 episodic traces 與 semantic regularities 的區分，說明 embodied agents 需要的不只是更大的 context window。事件證據、變化狀態與重複程序要能在經驗流中被維持，並在新目標提出時重新使用。這就是作者稱為 Embodied Action Memory 的能力。

Introduction 對 egocentric video 的定位也很清楚：第一人稱影片自然記錄了人在熟悉空間中移動、操作物件、重複日常程序的經驗。既有 egocentric-to-robot 工作常把它用於 imitation、dexterous manipulation 或 cross-embodiment robot learning；MEMORA 則走互補路線，不直接把影片轉成 policy 或軌跡，而是轉成 persistent semantic and procedural memory。

最後，作者把記憶設計成 lifecycle：perception stream 要被編碼為 structured observations，persistent state 要隨新證據修訂，重複經驗要鞏固成 reusable regularities，retrieval 則要把程序與 grounding evidence 交給 planner。Introduction 明確說，單純存 clips、把 observation flatten 成文字，或只維護一個 static entity graph，都難以滿足這些不同 continuity。

## 研究的第一性問題

- **基本問題**：機器人要如何把過去的 embodied experience 變成未來 planning 可用的記憶，而不是只保留原始影片或當下觀測？
- **約束**：記憶必須同時處理地點持續性、物件身份與狀態變化、活動時間順序，以及跨事件才浮現的慣例。
- **既有方法卡點**：直接把 egocentric video 當示範資料，可能學到「如何做」；但不一定學到「在這個人、這個地方、這個歷史脈絡下該做什麼」。
- **作者試圖移動的邊界**：把 embodied memory 從被動資料庫推向可在線編輯、離線鞏固、按類型檢索的 planning substrate。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- MEMORA 定義 Embodied Action Memory，強調記憶從經驗形成到未來行動的 lifecycle。
- 它維護 Environment、Entity、Activity、Inferred Knowledge 四類 memory stores，並用 online editing 與 offline consolidation 處理狀態變化與規律抽象。
- MEMORA-Bench 使用 45 小時 egocentric video，評估 retrospective memory faithfulness 與 prospective memory-grounded planning。
- 作者宣稱 MEMORA 在多個 open-weight language models 上改善 memory assessment 與 out-of-distribution planning 表現，並有定性實體機器人部署檢查。

### 我的保守判讀

- 這篇的價值在於把「機器人的記憶」拆成 typed, editable, consolidatable stores，而不是把所有歷史都塞成 RAG 文件。這種資訊架構問題可能比單一模型分數更可重用。
- 目前我只讀摘要與 Introduction，尚不能判斷 memory construction pipeline 對 perception errors 有多敏感，也不能判斷 benchmark 是否會讓語言模型靠文字規律取巧。
- MEMORA 位在 planning layer，不直接解決 low-level control；它的實用性要看下游 robot controller 能否可靠執行 memory-grounded language plans。

## 可放進資料庫的筆記

- Embodied memory 不只是長 context；它需要可維護的世界狀態、事件證據、程序記憶與習慣抽象。
- Egocentric video 可被用作 policy learning 資料，也可被用作 experience-to-memory 的來源；兩者是互補管線。
- 對 robot planning 而言，「使用者偏好」和「物件狀態歷史」可能是物理任務的一部分，不是額外 personalization 功能。
- Static entity graph 不足以描述 embodied experience，因為物件身份、狀態與活動序列會隨時間改變。
- Memory lifecycle 可拆成 formation、maintenance / editing、consolidation、retrieval；不同階段失敗會造成不同類型的 planning 錯誤。
- Planning layer 的記憶要能同時回答「曾經發生什麼」與「現在為了這個目標應該怎麼做」。
- Physical AI 的長程能力可能需要兩種互補記憶：低階 visuomotor history，以及高階 semantic / procedural memory。

## 後續想追的問題

- 四種 memory stores 的 schema 如何設計？哪些欄位是人工規則，哪些由模型抽取？
- Online editing 如何處理錯誤感知、物件身份混淆或狀態更新衝突？
- Offline consolidation 會不會過度抽象，把少數偶發行為誤當成習慣？
- MEMORA-Bench 的 planning metric 如何避免只評估語言合理性，而非真實可執行性？
- 與 RoboTTT 這類 long visuomotor context 方法結合時，哪些記憶應留在 symbolic / semantic layer，哪些應留在 sensorimotor layer？
