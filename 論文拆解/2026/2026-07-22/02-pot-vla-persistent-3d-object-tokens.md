# Closing the Loop in Humanoid VLA: Persistent 3D Object Tokens for Verifiable Loco-Manipulation

## 原文資訊

- 論文：Closing the Loop in Humanoid VLA: Persistent 3D Object Tokens for Verifiable Loco-Manipulation
- 作者：Peng Ren, Haoyang Ge, Jiang Zhao, Cong Huang, Yukun Shi, Pei Chi, Kai Chen
- arXiv ID：2607.18016v1
- 分類：Robotics (cs.RO)
- 發表 / 更新：Submitted on 20 Jul 2026（arXiv abs 頁面顯示；本次未見後續版本）
- 連結：[abs](https://arxiv.org/abs/2607.18016) / [pdf](https://arxiv.org/pdf/2607.18016)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-22

## 為什麼選這篇

這篇切到 humanoid VLA 的一個核心問題：人形機器人不是只在桌面上做單步 manipulation，而是要移動、看見物件、靠近、抓取、搬運、放置，並在中途處理遮擋、失敗與恢復。這類任務裡，「同一個物件」必須在時間中保持可追蹤，而不能每一步都重新從畫面猜。

我選它，是因為它把 VLA 的問題從「下一個 action 怎麼產生」推到「action 依據的物件狀態，和 verification 使用的物件狀態，是否是同一套」。這對 Physical AI 很重要：如果 action module 和 monitor / predicate layer 各自看不同的 state representation，系統就可能出現 premature transition、抓錯物、或失敗後無法恢復。

它也和最近 VLA / robot foundation model 的趨勢相連：模型越通用，越需要一個能在真實 3D 世界中維持 identity、geometry、visibility、uncertainty 與 relation 的中間表示。這篇的 Persistent 3D Object Tokens 可以視為一種把「物件中心世界狀態」token 化後餵給 humanoid action expert 的嘗試。

## 一句話理解

POT-VLA 想讓人形 VLA 對同一個任務物件保持持久 3D 記憶，並用同一份物件狀態同時產生動作與檢查結果。

## Summary / Abstract 說了什麼

摘要說，Vision-Language-Action policies 是通用 robot control 的一條路，但長時程 humanoid loco-manipulation 需要把任務物件視為在移動、接觸、遮擋、恢復過程中持續存在的實體。作者把問題稱為 object-state divergence：用來 condition whole-body action 的 object state，可能和用來判斷 action 是否達成預期物理關係的 state 不一致。

論文提出 Persistent Object Tokenization（POT）。POT 從 RGB-D observations 維護 role-indexed 3D object records，再把這些 records 轉成 object tokens 給 whole-body action expert。可以用一個簡化式表示：

$$
r_i^t = \text{Update}(r_i^{t-1}, \text{RGBD}_t), \quad a_{t:t+k} = \pi(l, o_t, \{r_i^t\})
$$

其中 $r_i^t$ 是第 $i$ 個任務角色在時間 $t$ 的持久 3D 物件記錄，$l$ 是語言任務，$o_t$ 是當前觀測，$a_{t:t+k}$ 是接下來的 action chunk。重點是：同一組 $r_i^t$ 不只餵給 action expert，也用來做 geometric predicate checks。

摘要自稱在 Unitree G1 上，POT-VLA 在八類真實任務中把 matched direct GR00T-N1.7 baseline 從 39/80 提升到 71/80；在 Being-0-aligned reference task 上達到 44/50，而 Being-0 paper 對應服務任務報告為 37/50。這些數字我先視為摘要宣稱，尚未讀實驗設計、任務對齊細節與 baseline 條件。

## Introduction 的問題設定

Introduction 從自然語言任務開始，例如「整理桌面」、「拿飲料給我」。這些任務要求 humanoid robot grounding task objects、穿越場景、操作物件，並在多個短 horizon action chunks 之間驗證進度。

作者認為中心困難不是單純 perception 或 control，而是同一個杯子、籃子、支撐面、交接對象，必須在走路、伸手、抓取、放置、觀察失敗、恢復時保持可定位。這被定義成 humanoid VLA 的 closed-loop state-representation problem：物件狀態必須同時能指導 action generation，也能在事後驗證 action 的物理後果。

接著作者提出 object-state divergence 作為實務 failure mode。現代 VLA 往往用當前 observation 的 visual-language features 來 condition action；但任務進度可能由另一個 monitor、predicate layer 或 language-level state 來檢查。當「用來做動作的物件狀態」和「用來驗證的物件狀態」不同時，小的 grounding、grasping、placement error 可能導致過早切換 subtask、操作錯誤物件，或恢復策略失效。

作者強調，這個問題是物理的，不只是語義的。物件在 metric 3D 裡移動、和手或支撐物接觸、進入容器、部分遮擋。因此需要 object-centered state：時間上持久、可被 policy 使用、且每個 action chunk 後可驗證。

## 研究的第一性問題

- **基本問題**：長時程 humanoid VLA 如何在移動與操作中保持「同一個物件」的物理狀態一致？
- **約束**：物件會遮擋、移動、被抓起、放進容器、和支撐面形成關係；單幀 visual-language feature 不一定保留身份與 3D 關係。
- **既有方法卡點**：action generation 和 progress verification 若使用不同 state representation，就會出現 object-state divergence。
- **作者試圖移動的邊界**：建立 role-aware、持久更新的 3D object records，將它們 token 化給 action expert，同時作為 predicate verification 的共同狀態。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 POT，一種 role-aware object-level physical memory，能把任務物件綁定到持久 role slots。
- 將 Persistent 3D Object Tokens 插入 whole-body action-head sequence，使 action prediction 依賴明確的目標、目的地、支撐、交接等 3D 狀態。
- 建立 action expert 與 predicate verifier 共享同一份物件記憶的 closed-loop POT-VLA 系統。
- 在 Unitree G1 的八類真實任務上，摘要宣稱明顯優於 matched direct baseline。

### 我的保守判讀

- 這篇最值得放進資料庫的地方，是它把 humanoid VLA 的 bottleneck 定義為「物件狀態一致性」，而不是只說需要更強模型或更多資料。
- Persistent object tokens 很像在 VLA 和傳統 robotics state estimation 之間搭橋：既保留可微／token 化介面，又承認真實世界需要 3D identity 與 relation tracking。
- 但 Introduction 還不足以判斷 POT 對物件偵測、深度品質、角色綁定錯誤有多敏感，也無法判斷與 GR00T-N1.7 / Being-0 的比較是否完全公平。
- 若場景中物件很多、語言指令模糊、或物件外觀高度相似，role-indexed records 的初始化與維護可能會成為主要風險。

## 可放進資料庫的筆記

- **Object-state divergence**：動作用的物件狀態與驗證用的物件狀態不一致，是長時程 robot execution 的系統性失敗源。
- **共享狀態比強 monitor 更根本**：如果 monitor 和 policy 不看同一份 world state，監控再強也可能檢查錯對象。
- **Humanoid VLA 需要物件持久性**：移動、抓取、放置、遮擋與恢復都要求 object identity 在時間中延續。
- **3D object tokens 作為中介層**：不是把所有 3D state 寫成傳統規則，也不是只靠影像 latent，而是把物理物件記錄序列化成 action model 可讀 tokens。
- **Verification 應該回到幾何關係**：任務完成不是語言說完成，而是物件是否真的形成目標物理關係。
- **Physical AI 的閉環問題**：VLA 的下一步不只是 open-loop action generation，而是 action、observation、state refresh、predicate check、recovery 的循環。
- **角色索引的價值與風險**：role slots 讓任務物件可追蹤，但也把錯誤綁定變成關鍵 failure mode。

## 後續想追的問題

- POT 的 object records 如何初始化？語言角色和偵測到的 3D 物件如何綁定？
- 若 RGB-D 觀測錯誤、遮擋長時間持續、或物件被手遮住，record refresh 如何避免漂移？
- Persistent 3D Object Tokens 插入 action-head sequence 的位置與格式，對性能有多關鍵？
- predicate verifier 是手寫幾何規則、學習式判斷，還是混合？可擴充性如何？
- 與 GR00T-N1.7 / Being-0 的比較是否同 embodiment、同任務定義、同恢復策略與同觀測條件？
