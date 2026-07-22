# FM-VLA: Force-based Memory for Vision-Language-Action Models in Contact-Rich Manipulation

## 原文資訊

- 論文：FM-VLA: Force-based Memory for Vision-Language-Action Models in Contact-Rich Manipulation
- 作者：Ruicheng Li, Qixiu Li, Ruichun Ma, Yu Deng, Lin Luo, Zhiying Du, Jianfeng Xiang, Huizhi Liang, Ruicheng Wang, Jiaolong Yang, Baining Guo
- arXiv ID：2607.18231v1
- 分類：Robotics (cs.RO)
- 發表 / 更新：Submitted on 20 Jul 2026（arXiv abs 頁面顯示；本次未見後續版本）
- 連結：[abs](https://arxiv.org/abs/2607.18231) / [pdf](https://arxiv.org/pdf/2607.18231)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-22

## 為什麼選這篇

這篇很符合「LLM / VLM / VLA 走向 Physical AI」時會遇到的實體限制：光靠影像與語言並不一定能知道機器人和世界互動到哪一步。尤其在接觸豐富的操作任務裡，按鈕按了幾次、擦拭是否已經完成幾輪、物體是否被觸碰或卡住，常常不是目前這一幀影像可以可靠回答的問題。

它的切入點不是再堆更大的視覺記憶，而是把力／扭矩歷史當成一種任務記憶。這對 VLA 很有意思：如果 VLA 是把感知、語言和動作接起來的模型，那麼「哪些感測模態應該進入記憶」本身就是架構問題，不只是資料問題。

我選它，是因為它把非 Markovian manipulation 的問題說得相對具體：有些任務的正確下一步不只依賴當下觀測 $o_t$，而依賴歷史 $h_{1:t}$。力覺歷史可能提供比影像歷史更低成本、也更直接的互動進度訊號。

## 一句話理解

FM-VLA 想讓 VLA 在接觸型長時程操作中，不只看「現在畫面」，也記得「過去接觸發生了什麼」。

## Summary / Abstract 說了什麼

摘要指出，VLA 模型已經在機器人操作泛化上展現能力，但很多模型仍近似依賴當前觀測。已有記憶增強 VLA 會使用過去影像或語言摘要，但在視覺變化很小、遮擋嚴重、或事件本身主要體現在接觸訊號時，影像記憶可能又貴又不夠準。

FM-VLA 的做法是把力歷史編碼成 compact force memory tokens。論文自稱先用 VAE 對力時間序列做重建式預訓練，再把 force latent representation 與短期狀態歷史投影成額外 conditioning tokens，送進 action expert module。這裡的直覺可以簡化成：

$$
z = E_{\text{VAE}}(f_{1:t}), \quad a_{t:t+k} = \pi(o_t, l, z, s_{t-w:t})
$$

其中 $f_{1:t}$ 是到目前為止的力／扭矩歷史，$z$ 是壓縮後的力記憶，$o_t$ 是當前觀測，$l$ 是語言任務，$s_{t-w:t}$ 是短期 proprioceptive state。白話說，模型不是把所有歷史影像都塞回去，而是把「接觸過程」壓縮成幾個可被動作模型使用的記憶 token。

摘要也宣稱它在三種 memory-dependent tasks 上評估：找隱藏方塊、按按鈕指定次數、擦盤子指定次數；成功率超過 80%，且 inference overhead 很小。這些結果我只把它當作摘要與 introduction 中的宣稱，尚未讀實驗細節。

## Introduction 的問題設定

Introduction 先從 VLA 的通用操作能力談起：現有 VLA 借助大規模 vision-language pretraining，在多樣操作任務中有泛化能力。但作者認為，多數架構仍把決策視為從當前狀態到未來動作的 memoryless mapping。這種 Markovian assumption 在一些短任務可能夠用，但面對需要長期 temporal context 的真實場景就會不足。

作者舉的例子很清楚：重複動作計數、追蹤互動進度、部分可觀測狀態下尋找隱藏物。這些任務的正確動作常常取決於過去環境與 agent state，而不是當前影像。

接著作者把既有視覺記憶方法的限制拆成兩點。第一，當視覺變化很小或遮擋嚴重時，過去影像也不一定能顯示事件是否發生，例如小幅度重複按鈕。第二，保存並 attention 過去影像會增加 token 長度與延遲。換句話說，視覺記憶不是免費的，而且不一定是最好的事件記錄器。

最後，作者提出力訊號的角色：接觸事件、力大小、重複行為次數，本來就自然存在於 force sensor measurements 裡。既有 ForceVLA / TA-VLA 等工作雖然納入力或扭矩，但作者認為它們偏向改善當下或短期 action prediction，尚未把力當成長期互動進度記憶。

## 研究的第一性問題

- **基本問題**：在接觸型 manipulation 裡，機器人如何知道任務進度，而不是只知道當前影像？
- **約束**：歷史力訊號高頻、 noisy、未結構化；不能把所有歷史原樣塞進模型，否則計算與學習都會變重。
- **既有方法卡點**：視覺記憶對「看不出來但摸得出來」的事件不敏感；短期 force conditioning 又不足以累積長時程進度。
- **作者試圖移動的邊界**：把力歷史壓縮成可被 VLA action expert 使用的 memory tokens，讓 VLA 處理非 Markovian、contact-rich tasks。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出第一個 force-based memory 的 VLA 架構，用於非 Markovian、接觸豐富的操作任務。
- 用 VAE 將長時程力訊號壓縮成 compact latent representation，再轉成 conditioning tokens。
- 在三個需要記憶互動進度的雙臂操作任務中，明顯優於 memoryless 與 vision-based memory baselines，且推論開銷小。

### 我的保守判讀

- 這篇的概念價值在於提醒：Physical AI 的「記憶」不一定是語言記憶或影像記憶，也可能是接觸歷史、力覺事件與物理狀態變化。
- 但摘要與 Introduction 還不足以判斷方法是否能泛化到更長任務、更雜亂物體、不同力感測器配置，或無高品質 force sensor 的低成本平台。
- 它也可能高度依賴任務類型：若任務進度主要體現在力訊號，force memory 很合理；若進度主要是語義或幾何關係，可能仍需要物件記憶、3D 狀態或語言層規劃。

## 可放進資料庫的筆記

- **VLA 的非 Markovian 缺口**：不是所有 robot action 都能由當前 observation 決定；很多操作任務需要歷史。
- **記憶模態選擇**：模型記憶不等於影像幀堆疊；應根據任務事件的物理載體選感測模態。
- **力覺作為事件計數器**：接觸、按壓、擦拭、碰撞等事件，可能比影像更適合由 force history 表示。
- **壓縮歷史比保留歷史更重要**：長時程訊號若不壓縮，會造成 token 長度、延遲與學習困難。
- **Physical AI 的 token 化問題**：未來很多進展可能來自把物理狀態轉成 action model 可用的 tokens。
- **接觸任務的進度感**：擦了幾次、按了幾下、是否已接觸，都是任務 state，不只是低階控制細節。
- **保守評估原則**：abstract 中的 80% 成功率需等讀實驗設定、baseline 公平性與任務難度後才能判斷份量。

## 後續想追的問題

- VAE 的 force latent 是否跨任務共享，還是每類任務都要重新學？
- force memory tokens 與 visual tokens 的融合位置，對延遲與穩定性有多大影響？
- 如果力感測器 noisy、漂移或不同 robot embodiment，方法是否仍穩定？
- 它是否能處理「語義進度 + 物理接觸」混合的長任務，而不只是指定次數的接觸任務？
- baseline 的 vision memory 是否有足夠調參與相同推論預算？
