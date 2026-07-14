# RLPrompt: Optimizing Discrete Text Prompts with Reinforcement Learning

## 原文資訊

- 論文：RLPrompt: Optimizing Discrete Text Prompts with Reinforcement Learning
- 作者：Mingkai Deng; Jianyu Wang; Cheng-Ping Hsieh; Yihan Wang; Han Guo; Tianmin Shu; Meng Song; Eric P. Xing; Zhiting Hu
- arXiv ID：2205.12548v3
- 分類：cs.CL, cs.LG
- 發表 / 更新：2022-05-25 / 2022-10-22
- 連結：[abs](https://arxiv.org/abs/2205.12548) / [pdf](https://arxiv.org/pdf/2205.12548)
- 本次閱讀範圍：全文 PDF 與 arXiv source；包含 Introduction、方法、實驗、分析、限制與附錄重點，不只讀 Introduction。
- 擷取日期：2026-07-14
- 收錄性質：使用者指定補充閱讀；不計入每日排程選文上限。

## 為什麼選這篇

這篇有趣的地方，不只是「用 RL 找 prompt」。更關鍵的是它把 prompt 從人類語言指令，改寫成一種可以被搜尋、被獎勵函數塑形、甚至可以跨模型轉移的離散控制碼。它碰到一個很早但到現在仍然重要的問題：語言模型到底是在理解我們寫的自然語言 prompt，還是在對某些 token pattern 產生可利用的內部反應？

它也站在 soft prompt 與 manual prompt 之間。Soft prompt 可用梯度調，但不可讀、難跨模型、需要白箱梯度；manual / paraphrase prompt 可讀，但搜尋空間很小。RLPrompt 試圖用強化學習去搜尋「離散 token prompt」，讓 prompt 保持文字形式，同時不需要 downstream LM 的梯度。

從 2026 的視角回看，這篇像是早期「黑箱模型控制」與「prompt 作為可最佳化介面」的前身。它提醒我們：prompt engineering 不是只有寫好英文指令；也可能是設計 reward、policy、搜尋空間與 evaluation loop。

## 一句話理解

RLPrompt 把離散 prompt 搜尋視為強化學習問題：訓練一個小 policy network 逐 token 產生 prompt，用下游任務表現作 reward，最後得到可直接丟給黑箱語言模型使用的文字 prompt。

## 這篇在解什麼基本問題

### 1. Prompt 很有用，但怎麼找到好 prompt？

Prompting 的吸引力在於不用改大模型權重，就能讓預訓練模型做分類、生成、風格轉換等任務。但 prompt 本身通常很難設計：

```text
同一個任務
→ 不同 prompt wording
→ 模型輸出差很多
```

所以問題不是「prompt 能不能用」，而是：

> 在只有少量資料、甚至只有弱 reward 的情況下，如何系統性找到能提升任務表現的 prompt？

### 2. Soft prompt 有效但不透明

Soft prompt / prefix tuning 會學一串連續向量。好處是可微分、可用 gradient descent；缺點是：

- 人看不懂；
- 通常綁定特定模型的 embedding space；
- 需要模型內部梯度；
- 對只有 inference API 的模型不適用。

如果模型只提供黑箱 API，soft prompt 的優勢會被削弱。

### 3. Discrete prompt 可讀、可轉移，但很難最佳化

離散 prompt 是實際 token，可以被複製、檢查、跨模型使用。但搜尋空間巨大。若 vocabulary 大小為 $|V|$、prompt 長度為 $T$，暴力搜尋規模約為：

$$
O(|V|^T)
$$

也就是說，長度只要稍微增加，就不可能枚舉。過去方法多半靠人工改寫、paraphrase 後挑選，或像 AutoPrompt 那樣用梯度近似去改 token；但這些方法要嘛不系統，要嘛仍依賴模型梯度。

## 方法核心：把 prompt 搜尋變成 RL

### 1. 狀態、動作、獎勵怎麼對應？

RLPrompt 的 formulation 很直覺：

- agent 要產生一段 prompt：$z = [z_1, z_2, \dots, z_T]$；
- 每一步選下一個 token $z_t$；
- 選完完整 prompt 後，把 prompt 和輸入 $x$ 丟給任務 LM；
- 下游任務表現變成 reward。

用比較白話的方式表示：

```text
policy network 產生 prompt
→ prompt + input 丟進 frozen / black-box LM
→ LM 做分類或生成
→ 用任務指標算 reward
→ reward 回頭更新 policy network
```

這裡 downstream LM 被當成環境，不需要開放梯度。policy network 自己是可訓練的；它學的是「如何產生能讓 LM 表現好的離散 token」。

### 2. 為什麼不是直接改 LM？

因為這篇的目標不是微調模型，而是找 prompt。作者想保留幾個特性：

- 下游 LM 可以 frozen；
- 下游 LM 可以是黑箱；
- 訓練後只留下文字 prompt，推論時不需要 policy network；
- prompt 仍是離散 token，有機會跨模型轉移。

這點很重要：policy network 是搜尋器，不是最終部署的一部分。訓練完以後，可以丟掉 MLP / policy，只拿學到的 prompt 去用。

### 3. Policy network 怎麼做？

作者用一個小型 policy LM 作為 prompt generator，例如 distilGPT-2。做法不是全模型微調，而是在 frozen compact LM 上加一層 MLP。

附錄給的設定是：

- policy LM：distilGPT-2，約 82M parameters；
- 加上的 MLP：1 hidden layer、2048 hidden states；
- 新增參數約 3.1M，約為 distilGPT-2 的 3.8%。

這個設計有一個微妙好處：

```text
task LM 可以很大、很貴、甚至黑箱；
policy LM 可以較小、可訓練、只負責搜尋 prompt。
```

所以 RLPrompt 的實際架構不是「用 RL 訓練大 LM」，而是「用小模型 + RL 去找能控制大模型的離散 prompt」。

## Reward engineering：這篇真正的工程重點

作者很清楚指出：用 RL 做 prompt optimization 的難點不只是 action space 離散，而是 reward 很吵。

原因是 reward 不是直接從 token 本身來，而是經過一長串黑箱流程：

```text
prompt token
→ 與 input 拼接
→ LM 內部推理
→ 輸出 label / text
→ 外部 metric 評分
```

每一層都會引入不穩定。為了讓 RL 可訓練，作者提出兩個重點。

### 1. Input-specific z-score reward

不同輸入本來難度不同。比如 style transfer 裡，有些句子只要改一兩個字就能轉成正面，有些句子很難保留內容又改風格。如果把所有 input 的 reward 放在同一尺度比較，policy 可能偏向容易拿分的樣本。

所以作者對每個 input 單獨做 reward normalization。概念上是：對同一個輸入 $x$，抽多個 prompt $Z(x)$，算它們的 reward 平均與標準差，再把某個 prompt 的 reward 轉成 z-score：

$$
\tilde{R}_x(z) = \frac{R_x(z) - \mu_x}{\sigma_x}
$$

白話解釋：

> 不問這個 prompt 在全班拿幾分，而是問它在「同一題、同一個 input」的候選 prompt 裡表現多好。

這讓 reward 更像「相對於該輸入難度的進步」，而不是被樣本難度混淆。

### 2. Piecewise reward

如果 reward 設計太單一，policy 可能找到 adversarial prompt。例如分類任務若只最大化某個 label probability，prompt 可能學到讓模型一律偏向某一類的奇怪 token，而不是真正分類。

作者用 piecewise reward，把 dense signal 和 sparse qualitative signal 混在一起：

- dense：例如正確 label probability 與其他 label probability 的 gap；
- sparse / bonus：例如真的分類正確後給大幅獎勵。

分類裡，作者看的是正確類別機率與其他類別最高機率的差距：

$$
Gap(c) = P(c) - \max_{c' \ne c} P(c')
$$

若 gap 大於 0，代表預測正確；若小於 0，代表錯。這個設計比單純最大化正確類別機率更能防止「偏一類」的投機行為。

## 實驗一：Few-shot text classification

### 設定

作者在少樣本分類上測試 RLPrompt。典型流程是把分類問題改成 prompt-based prediction。例如 sentiment classification：

```text
Input: food is delicious
Template: [Input] [Prompt] [MASK]
Verbalizer: positive → great, negative → terrible
```

模型看 `[MASK]` 位置比較像哪個 verbalizer token，就判斷類別。

實驗使用 RoBERTa-large 作為 backbone，訓練資料是每類 16 個 examples，驗證資料也是每類 16 個。主表包含 SST-2、Yelp Polarity、MR、CR、SST-5、Yelp、AG's News 等資料集。

### 對比方法

作者比較了幾類方法：

- full fine-tuning；
- manual prompt；
- instructional prompt；
- in-context demonstration；
- soft prompt tuning；
- black-box tuning；
- GrIPS 這類 discrete prompt enumeration；
- AutoPrompt；
- RLPrompt 2-token / 5-token。

### 主要結果

主表中，RLPrompt 5 discrete tokens 在 7 個資料集平均 accuracy 達到 75.8，是表中最高；2 tokens 版本平均 74.4，也接近混合 soft prompt 的 Black-Box Tuning 50 soft tokens 版本 74.7。

幾個具體數字：

- SST-2：RLPrompt 5 tokens 92.5，高於 manual prompt 82.8、fine-tuning 80.6、BB Tuning mixed 89.1；
- Yelp Polarity：RLPrompt 5 tokens 95.1，是表中最高；
- MR：RLPrompt 5 tokens 87.1，是表中最高；
- CR：RLPrompt 5 tokens 89.5，是表中最高；
- 7 個主表資料集平均：RLPrompt 5 tokens 75.8。

但也不是全面勝出。例如：

- Yelp 5-class 上 fine-tuning 51.0，高於 RLPrompt 44.8；
- AG's News 上 fine-tuning 84.9，高於 RLPrompt 80.2。

附錄也說，多類別任務如 Yahoo、DBPedia 在資料量相對變多時，fine-tuning / soft prompt 類方法仍有優勢。這提示 RLPrompt 更強的場景不是「資料很多、可全模型訓練」，而是「低資源、黑箱、想保留離散 prompt」的場景。

### 訓練效率

作者把 RLPrompt 和 BB Tuning 比較，說它在不需要 LM 梯度的情況下，收斂步數和 soft prompt black-box tuning 接近，但表現較好。這裡的重點不是 RL 一定便宜，而是：

```text
不拿 task LM 梯度
仍可找到有效離散 prompt
```

附錄訓練成本也不低：分類實驗在 RTX 3090 上，視模型大小約 1.5 到 4 小時。也就是說，RLPrompt 不是「免費 prompt engineering」，而是把成本從微調大模型，轉成訓練小 policy + 多次查詢 task LM。

## 實驗二：Unsupervised text style transfer

### 為什麼選 style transfer？

Style transfer 的任務是：給一句話，把風格改掉，但內容要保留。例如：

```text
The food is disgusting.
→ The food is delicious.
```

而且通常沒有成對監督資料。這讓它適合測試 RLPrompt 的另一個能力：不用 gold label，也能靠 reward function 學 prompt。

### Reward 設計

作者把 reward 設成內容保留與目標風格強度的加總：

$$
R(x, y, s) = Content(x, y) + Style(y, s)
$$

其中：

- Content 用 model-based alignment / CTC 類指標衡量輸入輸出是否保留內容；
- Style 用訓練好的 style classifier 判斷是否達成目標風格；
- Fluency 不直接在公式中，但評估時會獨立看。

因為每個句子的 reward 尺度不同，這裡特別使用 input-specific z-score normalization。

### 結果

主表在 Yelp sentiment transfer 上比較 training baselines、prompting baselines 與 RLPrompt。最強的 RLPrompt + GPT-2-xl 結果為：

- Content：72.1；
- Style：94.2；
- Fluency：89.5；
- joint score $J(C,S,F)$：61.4；
- GM：84.7。

相比之下：

- DiRR：Content 78.8、Style 97.7、Fluency 75.6、$J=59.6$、GM 83.5；
- Manual Prompt：Content 64.2、Style 91.5、Fluency 93.2、$J=53.4$、GM 81.8；
- Null / Random Prompt 的 $J$ 大約 33–35。

作者的解讀是：DiRR 這種 full-model fine-tuning 在內容與風格上較強，但 fluency 較低；RLPrompt 不改 LM 權重，保留了生成流暢性，因此 joint score 和 GM 反而更高或具競爭力。

人評也呈現類似方向：DiRR 的 Content / Style 分數最高，RLPrompt 稍低，但 Fluency 接近 manual prompt，整體 GM 次佳。這比較像一種 trade-off：

```text
fine-tune model：更能逼近任務目標，但可能傷害語言流暢性；
prompt-only control：控制力較弱，但較保留原模型生成能力。
```

### 模型大小效應

在 style transfer 上，作者從 distilGPT-2 到 GPT-2-xl 都測。RLPrompt 的 performance 隨 task LM 變大大致提升：

- distilGPT-2：$J=46.0$；
- GPT-2-small：$J=50.7$；
- GPT-2-medium：$J=56.1$；
- GPT-2-large：$J=56.5$；
- GPT-2-xl：$J=61.4$。

這符合直覺：prompt 本身只是控制訊號，真正生成能力仍取決於 task LM 的能力。

## 最有意思的分析：好的 prompt 不一定像人話

這篇最值得記的發現，是「gibberish prompt」現象。

作者發現很多表現強的 prompt 看起來不像自然語言。例如 sentiment transfer 的 prompt 可能是：

```text
Parameters Comparison )=( Compare either
```

人看起來沒意義，但模型反應很好。作者也做了 fluency constraint：限制 prompt token 必須比較像 GPT-2 會自然生成的文字。結果 fluent prompt 的 perplexity 大幅下降，例如 prompt 像：

```text
I love my life (
```

但任務表現下降：

- 無 fluency constraint：Prompt PPL 約 254K，$J=61.4$，GM 84.7；
- 加 fluency constraint：Prompt PPL 約 82.1，$J=46.7$，GM 78.1。

這個結果很反直覺：

> prompt 對人越像一句話，不一定對模型越有效。

更精確地說，語言模型的 prompt interface 同時有兩層：

1. 人類語義層：我們寫自然語言指令；
2. 模型內部激活層：某些 token pattern 會觸發模型學到的分布結構。

RLPrompt 找到的可能是第二層，而不是第一層。

## 跨模型轉移：gibberish 但可轉移

離散 prompt 的另一個重要優點是可跨模型使用。作者測試：在某個模型上學到 prompt，拿到另一個模型上用。

主要觀察：

- prompt 可以在不同模型間保留部分效果；
- 從小模型學到的 prompt，轉到大模型常常仍有不錯表現，甚至更好；
- 從大模型學到的 prompt，轉回小模型時表現常下降；
- RoBERTa 這類 masked LM 與 GPT-2 這類 left-to-right LM 之間也有一定轉移。

這個現象很有意思，因為它暗示不同預訓練 LM 之間可能共享某些「promptable structure」。即使 prompt 看起來像亂碼，它仍可能觸發相似的內部分布。

用更一般的語言說：

```text
prompt 不是只在某個模型內部 embedding space 的軟向量；
離散 token prompt 可能是一種跨模型可攜的控制介面。
```

這也是它相對 soft prompt 的核心差異。

## Verbalizer robustness

Prompt-based classification 常常很依賴 verbalizer。比如 positive / negative 可以映射成 great / terrible，也可以映射成 good / bad。不同 verbalizer 會影響模型結果。

作者測試了 SST-2 和 AG's News 等情境，發現 RLPrompt 在多種 verbalizer choice 下都比 manual prompt 穩定，變異也比較小。

這個結果可以這樣理解：

> 若 label token 是固定的，prompt optimization 可以部分補償 verbalizer choice 的脆弱性。

但這不代表 verbalizer 不重要，而是 RLPrompt 能在給定 verbalizer 下找到比較適配的上下文 token。

## 附錄裡值得注意的實作細節

### 分類任務

- prompt 搜尋時使用 top-256 sampling；
- policy network 的輸入可只是一個 placeholder word，例如 `classification`；
- optimizer 用 Adam，learning rate $5 \times 10^{-5}$；
- 2-token prompt 訓練 6K steps，5-token prompt 訓練 12K steps；
- 每 10 steps 計算 validation performance；
- 評估時平均 validation accuracy 最高的 3 個 prompts；
- 多個 few-shot split、多個 random seed，以減少低資源設定的不穩定。

### Style transfer

- 每個 input sample 4 個 prompts；
- prompt sampling 用 top-50；
- generation 用 top-10 sampling；
- 每個方法 sample 32 個候選輸出，再選 reward 最高者；
- reward 被 shape 到較大的範圍，例如 [-20, 80]；
- distilGPT-2 到 GPT-2-xl 的訓練成本可從 10 小時到 1 天。

這些細節提醒：RLPrompt 的結果不只是 formulation 漂亮，背後也倚賴許多 sampling、reward shaping、candidate selection 的工程選擇。

## 可能的貢獻

### 論文自稱

1. 提出用 RL 做 discrete prompt optimization 的框架。
2. 不需要 downstream LM 的梯度，能把 LM 當成黑箱環境。
3. 用小 policy network 搜尋 prompt，訓練後只留下離散 prompt。
4. 在 few-shot classification 與 unsupervised style transfer 上優於多種 prompt baseline。
5. 發現高效 prompt 常常是 gibberish，但仍可跨模型轉移。

### 我的保守判讀

這篇真正的價值不是證明 RL 是 prompt optimization 的最終答案，而是把「prompt」從人工撰寫物件，轉成可用黑箱 reward 搜尋的控制介面。它讓我們看到一個方向：

```text
prompt engineering
→ prompt optimization
→ reward-shaped interface search
```

但它也暴露幾個限制：

- reward design 仍高度依賴人類工程；
- 訓練成本不低，尤其 style transfer；
- 對大型 API 模型的實驗沒有真正完成；
- gibberish prompt 雖然有效，但可解釋性反而變弱；
- 如果 reward 有漏洞，policy 可能學到投機 prompt。

## 限制與風險

### 1. Reward hacking 風險

作者自己也提到，如果 reward 設計錯，policy 可能最大化 reward 而不是完成任務。這點在今天看更重要：只要有自動 prompt optimization，就會有 prompt overfitting 與 reward hacking。

### 2. Gibberish prompt 的解釋性矛盾

離散 prompt 原本的優點是「人看得懂」。但 RLPrompt 學到的最佳 prompt 常常不像人話。這使它介於 soft prompt 與 manual prompt 之間：

```text
形式上是文字；
功能上像模型內部控制碼；
人類解釋性不一定成立。
```

### 3. 跨模型轉移不是保證

雖然作者觀察到 transfer，但效果取決於 source / target model。尤其大模型學到的 prompt 轉回小模型可能下降，表示 prompt 可能依賴模型能力與內部 representation。

### 4. 大型商用 API 的成本與可行性

論文說方法可用在 GPT-3 這類 API-only 模型，但限制章節也承認沒有實驗於 GPT-3。若真的用在大型 API，可能遇到：

- 查詢成本；
- rate limit；
- API 隨版本改變；
- reward evaluation 成本；
- prompt overfitting 到特定模型版本。

## 可以放進資料庫的筆記

1. **Prompt 可以被視為控制碼，不只是自然語言指令。** 人類可讀 prompt 與模型有效 prompt 不必重合。
2. **離散 prompt 的價值在於可攜性。** Soft prompt 綁定 embedding space；文字 prompt 至少有跨模型試驗的可能。
3. **Prompt optimization 的核心不是搜尋本身，而是 reward 設計。** Reward 一錯，搜尋越強，錯得越徹底。
4. **小模型可以當 prompt 搜尋器，大模型可以當黑箱環境。** 這是一種「小模型探索、大模型執行」的分工。
5. **Gibberish prompt 是 interpretability 的警訊。** 看似離散、可讀的 token，不代表語義上可解釋。
6. **跨模型 prompt transfer 暗示預訓練模型共享某些 promptable structures。** 這可能是研究 model family、scaling、pretraining corpus 共性的切入口。
7. **Prompt fluency 與 task performance 可能衝突。** 對人像話的 prompt，不一定最能激活模型想要的行為。
8. **少樣本任務中，prompt 方法常是資料效率與穩定性的 trade-off。** 資料更多、多類別更複雜時，全模型或連續參數調整仍可能更強。

## 這篇對今天 LLM / agent 的啟發

### 1. Agent prompt 也可以被 reward 搜尋

今天很多 agent prompt 還是人工寫 system prompt、tool instruction、planner template。RLPrompt 的思路可以抽象成：

```text
agent prompt / tool instruction
→ agent 執行任務
→ 外部評分：成功率、成本、步數、錯誤率
→ 搜尋 prompt / policy
```

只是今天的 action space 可能不只是 token，而是：

- prompt 段落順序；
- tool description；
- memory format；
- reflection template；
- verifier prompt；
- routing rule。

### 2. 但自然語言 prompt 的可維護性很重要

RLPrompt 的 gibberish prompt 很有效，但在產品與長期系統裡可能難維護。這提示一個設計張力：

```text
performance-optimized prompt
vs.
human-maintainable prompt
```

對研究 benchmark，可以接受 gibberish；對可審計、可維護的 agent 系統，可能需要限制 prompt 必須可讀，或把不可讀部分隔離為 learned controller。

### 3. Reward loop 比 prompt wording 更重要

這篇把 prompt engineering 的重點從「怎麼寫一句神奇咒語」轉成「如何定義任務成功」。這對現在的 LLM evaluation 也很有啟發：沒有好的 evaluator / reward，prompt optimization 只是自動化試錯。

## 後續想追的問題

1. 如果把 RLPrompt 用在 instruction-tuned LLM 上，gibberish prompt 還會有效嗎？還是 instruction tuning 會讓模型更偏向人類語義層？
2. 跨模型可轉移的 prompt token 是否能揭露不同模型共享的 pretraining artifact？
3. 若 reward 由 LLM-as-judge 給出，RLPrompt 會不會學到 judge-specific exploit？
4. 可否設計「可讀性約束 + task reward」的 Pareto frontier，而不是只比較 fluent / non-fluent？
5. 在 agent tool-use 任務中，prompt optimization 應該搜尋 token、自然語言段落，還是 workflow graph？

## 我的總結

RLPrompt 不是一篇單純「用 RL 做 prompt」的論文。它更像是在說：語言模型的 prompt 介面有一個人類看得見的語義層，也有一個模型內部可被 token pattern 操控的行為層。RL 可以在後者上做搜尋，而離散 prompt 讓搜尋結果仍可被複製、部署、跨模型測試。

這篇的美感在於它保留了 prompt 的文字形式，卻揭露了 prompt 不一定是人類語言。這對後來所有 prompt optimization、black-box LLM control、agent prompt evaluation 都是一個很好的早期樣本。
