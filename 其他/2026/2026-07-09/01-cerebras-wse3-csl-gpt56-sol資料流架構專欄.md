# 技術專欄｜Cerebras WSE-3 與 GPT-5.6 Sol：不是更大的 GPU，而是把記憶體牆拆成 90 萬個小房間

## 原文資訊

- 使用者提供材料：關於 OpenAI GPT-5.6 Sol 在 Cerebras 上推出、CUDA 與 CSL 程式模型差異、WSE-3 資料流架構、SRAM 頻寬與 stencil computation 加速的論述。
- 補充查核日期：2026-07-09
- 主要來源：
  - OpenAI｜[Previewing GPT-5.6 Sol: a next-generation model](https://openai.com/index/previewing-gpt-5-6-sol/)
  - Cerebras｜[Product - Chip / The Future of AI is Wafer Scale](https://www.cerebras.ai/product-chip)
  - NVIDIA｜[NVIDIA H100 Tensor Core GPU](https://www.nvidia.com/en-us/data-center/h100/)
  - arXiv｜Elia Belli and Daniele De Sensi, [Stencil Computations on Cerebras Wafer-Scale Engine](https://arxiv.org/abs/2605.07954v1), 2026-05-08

## 一句話拆解

GPU 是「很多執行緒共享一個龐大而分層的記憶體系統」；Cerebras WSE-3 比較像「把 90 萬個小處理器鋪在一整片晶圓上，每個處理器只管自己的 48 KB SRAM，資料像水流一樣在相鄰格子之間被明確送過去」。

這不是單純的「Cerebras 比 NVIDIA 快」，而是兩種計算哲學的差異：

- GPU 解決的是：如何讓大量 thread / warp 共同餵飽高吞吐的 SM，並盡量把 HBM、cache、shared memory 用到極致。
- WSE 解決的是：如果把記憶體直接放在每個 PE 旁邊，並讓資料移動變成程式明確描述的一部分，哪些工作負載可以避開傳統記憶體牆？

## 先釐清：使用者論述中哪些是已查到的，哪些要保留不確定

### 較有來源支持的部分

1. **OpenAI 確實提到 GPT-5.6 Sol 將在 Cerebras 上提供。**  
   OpenAI 的 GPT-5.6 Sol 預覽頁寫到：將在 7 月於 Cerebras 上推出 GPT-5.6 Sol，速度最高可到 **750 tokens/s**，初期提供給 select customers。這裡我查到的是 750 tokens/s，不是 3,000 tokens/s。

2. **Cerebras 官方宣稱 WSE-3 是晶圓級 AI 晶片。**  
   Cerebras 產品頁稱 WSE-3 面積 46,225 mm²、4 兆電晶體、125 petaflops AI compute、900,000 AI-optimized cores。

3. **Cerebras 官方與論文都支持「大量分散式 SRAM」這個核心敘事。**  
   Cerebras 產品頁與 Belli / De Sensi 的 stencil 論文都把 WSE-3 的優勢放在 on-chip SRAM、mesh interconnect、資料流架構與避免 off-chip memory bottleneck 上。

4. **H100 的 80GB 與約 3TB/s 等級頻寬是合理比較基準。**  
   NVIDIA H100 頁面列出 SXM 版本 GPU memory 80GB、memory bandwidth 3.35TB/s；使用者說的「每秒 3 TB」可視為約略值。

### 需要標示不確定或修正的部分

1. **「gpt-oss-120B 每秒 3,000 tokens」不等同於 GPT-5.6 Sol。**  
   Cerebras 產品頁可見其推論服務有「超過 3,000 tokens/s」的敘事，但 OpenAI GPT-5.6 Sol 官方頁面寫的是在 Cerebras 上最高 750 tokens/s。兩者可能是不同模型、不同批次、不同量測條件，不能直接混在一起。

2. **「stencil computation 較 H100 有 95–210 倍」我沒有在本次查核中找到直接來源。**  
   我查到的 2026 arXiv 論文是以 WSE-3 對 NVIDIA A100 baseline 比較，摘要與正文提到最高 **342×** speedup；若要主張 H100 的 95–210×，需要另找該數字的原始 benchmark 或報告。

3. **「完全沒有 kernel 啟動」需要更細緻地說。**  
   從程式模型角度，CSL 的確不是 CUDA 那種 host launch GPU kernel、再由 thread blocks / warps 執行的模型；但 WSE 仍有 host 與 SDK 負責載入程式、配置 PE、串流資料與啟動任務。比較安全的說法是：它沒有 CUDA 式 thread/warp/kernel mental model，而不是整個系統沒有任何啟動或排程概念。

## 為什麼 CUDA 使用者會看不懂 CSL

如果熟悉 CUDA，你腦中的計算單位大概是這樣：

```text
一個 kernel
  → 很多 block
    → 每個 block 很多 thread
      → 32 個 thread 組成 warp
        → 存取 global memory / shared memory / registers / cache
```

所以 CUDA 程式設計常常在想：

- thread index 怎麼映射到資料？
- memory access 能不能 coalescing？
- shared memory tile 怎麼設計？
- warp divergence 會不會嚴重？
- global memory bandwidth 是否被浪費？
- occupancy 是否夠高？

Cerebras CSL 的思考方向幾乎反過來：你不是先想「我要開多少 thread」，而是想「每個 PE 是一個小島，它手上有哪一小塊資料？它什麼時候要把 wavelet 送給東西南北的鄰居？收到資料後要觸發什麼計算？」

比較像這樣：

```text
整片晶圓是一張 2D 網格
  → 每個 PE 有自己的程式、自己的 SRAM、自己的 router
    → PE 只能直接碰自己的本地 SRAM
      → 要別人的資料，就把訊息沿著 mesh 傳過去
        → 計算與通訊被設計成資料流
```

換句話說，CUDA 的核心抽象是「大量執行緒如何共同使用記憶體階層」；CSL / WSE 的核心抽象是「大量處理單元如何把資料移動本身變成計算圖的一部分」。

## WSE-3 的關鍵：不是 44GB 比 80GB 大，而是 44GB 的位置不同

使用者材料裡最重要的一句其實不是「WSE-3 有 44GB SRAM」，而是「這 44GB 不是一個所有核心共享的大池子」。

H100 的 80GB HBM 很大，也很快，但它仍然是 off-chip HBM。GPU 的 SM 要從 HBM 拿資料，中間還要經過 cache、記憶體控制器、coalescing 規則與各種排程。GPU 很擅長把這套系統榨乾，但程式寫不好或工作負載不適合時，仍會撞到 memory wall。

WSE-3 的 44GB on-chip SRAM 則被切成 90 萬份，放在 PE 旁邊。直覺上你可以把它想成：

```text
GPU：很多工人共用一個巨大倉庫，倉庫很快，但大家都要排路線、排車道、排規則。
WSE：每個工人桌上都有一個小抽屜，抽屜超快；但別人的抽屜你不能直接打開，只能傳紙條請隔壁轉交。
```

這帶來兩個同時存在的結果：

1. **本地存取極快。**  
   SRAM 在 PE 旁邊，沒有傳統 GPU 那種 global memory access 形態；很多 memory-bound 工作如果能被切成本地小問題，就會非常吃香。

2. **遠端資料變成顯式通訊成本。**  
   PE 不能隨便讀別人的 SRAM。你必須把資料透過 mesh / wavelet routing 傳到該去的地方。這使得演算法設計更像在做「空間部署」：資料放哪裡、往哪裡流、何時同步，會直接決定效率。

所以 WSE-3 的優勢不是把 GPU 的所有功能都做得更大，而是把「記憶體靠近計算」這件事推到極端；代價是程式設計者要接受更強的空間性與通訊顯式性。

## 為什麼 stencil computation 特別適合 WSE

Stencil computation 是科學計算裡很常見的模式，例如流體、熱傳、天氣、波動方程。它的基本形式是：每個格點的新值，取決於自己與鄰近格點。

例如一個簡化的 2D stencil：

```text
new[x, y] = f(
  old[x, y],
  old[x-1, y], old[x+1, y],
  old[x, y-1], old[x, y+1]
)
```

這種工作負載的特性是：

- 計算量不一定很大；
- 資料移動很多；
- 每個點主要需要附近鄰居，不需要全域亂抓資料；
- 網格結構天然對應到 2D PE mesh。

這正好吻合 WSE 的長處。每個 PE 管一小塊網格，鄰居資料就從相鄰 PE 傳來。你不需要反覆從 HBM 拉資料，也不需要把所有資料塞進 shared memory tile 再小心處理 bank conflict。Belli / De Sensi 的論文因此把 WSE-3 描述成能有效消除 GPU stencil 實作常見的 off-chip memory bottleneck，並在其測試設定中對 A100 baseline 達到最高 342× speedup。

但這也提醒我們：這種加速不是通用魔法。它最適合「空間局部性強、通訊圖規律、資料可以穩定鋪在網格上」的問題。若工作負載需要大量不規則查表、動態分支、全域 attention、或頻繁跨很遠的 PE 搬資料，WSE 的優勢就要重新驗證。

## 那 LLM 推論為什麼也可以放到 Cerebras？

LLM 推論不像 stencil 那麼簡單，尤其 transformer 會涉及矩陣乘法、KV cache、attention、MoE routing、batching、prefill / decode 不同階段等問題。這裡不能天真地說「LLM 就是 stencil」。比較合理的理解是：Cerebras 想把 LLM 推論也改寫成更適合 wafer-scale dataflow 的部署問題。

對模型服務而言，使用者真正感受到的不是 FLOPS，而是：

- 首 token latency；
- decode tokens/s；
- 長上下文與 cache 行為；
- batch 與即時互動之間的取捨；
- API 是否能穩定供應；
- 成本與配額。

OpenAI 說 GPT-5.6 Sol 在 Cerebras 上最高 750 tokens/s，這個訊號的意義不只是「有一個更快晶片」。更深的意義是：前沿模型服務商開始把模型能力分層成不同部署路線。有些路線追求最大模型能力；有些路線追求互動速度；有些路線追求成本；有些路線追求資料主權或供應多元化。

Cerebras 在這裡扮演的是「非 GPU 推論供應路線」。它不一定取代 H100 / B200，而是提供一個不同的 latency / throughput / memory locality trade-off。

## 思考模型：把硬體差異拆成三個問題

下次看到「某某 AI 晶片比 GPU 快幾倍」時，不要先問倍數，而是先問三件事。

### 1. 資料在哪裡？

- 是在 off-chip HBM？
- 是在 on-chip SRAM？
- 是每個核心私有？
- 還是共享 cache / shared memory？

計算速度常常不是第一瓶頸；資料在哪裡、搬多遠、能不能重用，才是底層瓶頸。

### 2. 平行性由誰負責描述？

- CUDA：程式員描述 thread/block/grid，硬體負責 warp scheduling 與記憶體階層。
- WSE / CSL：程式員更直接描述 PE 之間的資料流、通訊與本地計算。

抽象越高，開發越容易，但硬體特色可能被隱藏；抽象越貼近硬體，效能上限可能更高，但開發成本與演算法重寫成本也更高。

### 3. 工作負載的通訊圖長什麼樣？

- 如果資料主要跟鄰居互動，mesh 很自然。
- 如果資料需要全域聚合，mesh 可能變成通訊瓶頸。
- 如果資料流規律可預測，dataflow 架構很強。
- 如果資料流高度動態、不規則，GPU 的成熟軟體生態可能更有彈性。

## 可放進資料庫的筆記

- WSE-3 不是「更大 GPU」，而是「晶圓級 2D PE mesh + 分散式 SRAM + 顯式資料流」的架構路線。
- H100 的優勢在成熟 CUDA 生態、HBM 容量/頻寬、通用 accelerator software stack；WSE-3 的優勢在把記憶體極度靠近計算，對 memory-bound、空間局部性強的問題特別有吸引力。
- 看到 tokens/s 數字時，要先確認模型、batch、上下文長度、量化/精度、是否 streaming、是否單使用者互動、是否供應商最佳條件；不同模型的 tokens/s 不能直接互比。
- OpenAI GPT-5.6 Sol on Cerebras 的公開頁面寫的是最高 750 tokens/s；Cerebras 產品頁上的 3,000+ tokens/s 敘事應視為其他模型或其他條件，不能直接當成 GPT-5.6 Sol 的實測數字。
- Stencil 類問題之所以能在 WSE 上特別快，是因為問題本身的鄰接通訊圖與 WSE 的 2D mesh 很像；這不自動代表所有 LLM 或所有 HPC workloads 都會有同等倍數。

## 仍不確定的地方

- GPT-5.6 Sol 在 Cerebras 上的實際可用 API 條件、價格、模型限制、上下文長度、批次策略與穩定供應量，仍需等 OpenAI / Cerebras 更完整公開。
- 使用者材料中的「gpt-oss-120B 每秒 3,000 tokens」與「stencil 較 H100 95–210×」可能來自其他 benchmark 或社群報導；本筆記目前只把它們作為待查線索，不當作已驗證結論。
- CSL 的實際開發體驗、debug 成本、編譯器成熟度與可移植性，不能只從硬體規格判斷；需要看實際 SDK 文件、範例與開發者案例。

## 小結

如果用一句話讓 CUDA 使用者理解 Cerebras：不要把 WSE-3 想成「有更多核心的 GPU」，而要想成「把一個大問題鋪到晶圓上的 90 萬個小房間，每個房間都有自己的小記憶體與門口，資料必須沿著走廊傳」。

它的強項來自把記憶體牆切碎：每個 PE 的本地資料非常近、非常快；它的限制也來自同一件事：遠端資料不是你想讀就讀，必須被設計成通訊。真正的問題不是「Cerebras 會不會取代 GPU」，而是「哪些 AI / HPC 工作負載，值得為這種資料流機器重寫？」
