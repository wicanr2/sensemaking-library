# Prompt 不只是語意指令：從 RLPrompt、Nonsense Helps 到 J-space

- 日期：2026-07-14
- 類型：使用者討論整理 / 專題筆記
- 主題：LLM prompt optimization、gibberish prompt、nonsense perturbation、J-space、模型內部工作區
- 相關既有筆記：[RLPrompt 全文論文拆解](../../論文拆解/2026/2026-07-14/03-rlprompt-discrete-prompt-optimization.md)

## 一句話

LLM prompt 不能只理解成「人類語意指令」。它也可以是一種把模型內部狀態推到特定 activation basin、reasoning trajectory 或 silent workspace 的控制碼；因此，某些不合語法、不可讀、看似 gibberish 的 token sequence，可能比人類邏輯清楚的 prompt 更有效。

## 討論的起點

使用者在閱讀 RLPrompt 後提出一個問題：

> 2026 還有沒有類似的研究？也就是 ungrammatical / gibberish text 表現比 human logical text 更好。

後續又追問：

> `Nonsense Helps: Prompt Space Perturbation Broadens Reasoning Exploration` 跟目前提出的 LLM J-space 有關連嗎？

這份筆記把討論整理成一個可延伸的研究框架。

## 研究線索總覽

| 研究線 | 代表材料 | 觀察 |
|---|---|---|
| discrete prompt optimization | [RLPrompt](https://arxiv.org/abs/2205.12548) | 自動搜尋出的 discrete prompt 常不符合人類語法，但任務表現可超過人工 prompt。 |
| in-context gibberish pruning | [Evolving Prompts In-Context / PromptQuine](https://arxiv.org/abs/2506.17930) | 把 random demonstrations 剪到看似 incoherent gibberish，仍可提升 ICL 表現。 |
| reasoning exploration | [Nonsense Helps / LoPE](https://arxiv.org/abs/2605.05566) | Lorem Ipsum 類 pseudo-Latin perturbation 不提供答案，卻可打開不同 reasoning path。 |
| activation-level mechanism | [Meaningless Tokens, Meaningful Gains](https://arxiv.org/abs/2510.01032) | meaningless tokens 可能透過 MLP activation redistribution 改善 reasoning。 |
| adversarial gibberish | [Talking Nonsense](https://arxiv.org/abs/2404.17120)、[AmpleGCG-Plus](https://arxiv.org/abs/2410.22143) | 看似 nonsense 的 suffix / prompt 可讓模型產生 coherent target output 或繞過安全對齊。 |
| internal workspace | [Anthropic: A global workspace in language models](https://www.anthropic.com/research/global-workspace)、[Transformer Circuits paper](http://transformer-circuits.pub/2026/workspace/index.html) | J-lens 找到一組可 verbalize、可調控、可支援內部推理的 J-space representations。 |

## 第一層理解：prompt 是文字，也是控制向量

人類看到 prompt 是字串；模型看到的是 token embedding 序列，以及它們在 attention、MLP、residual stream 中造成的狀態變化。

因此，prompt 至少有兩種角色：

1. **語意指令**：用自然語言告訴模型要做什麼。
2. **狀態控制碼**：用 token pattern 改變模型進入哪個內部運算區域。

人類覺得清楚的 prompt，未必是模型內部最有效的控制碼。反過來，人類覺得奇怪的 token sequence，可能剛好把模型推到某個有利的 activation basin。

這也是 RLPrompt 類方法最反直覺的地方：最佳化器不是在找「好讀的說明文」，而是在找「讓目標模型分數最高的 token program」。

## LoPE：nonsense 不是提供資訊，而是改變探索路徑

`Nonsense Helps` 的設定是 reasoning RL，尤其是 GRPO / RLVR 中的 zero-advantage problem。

當同一題的多個 rollout 全部失敗，relative advantage 會塌成 0，這題就沒有有效訓練訊號。LoPE 的做法很簡單：

```text
原 prompt：
請一步一步解這題……

LoPE prompt：
Lorem ipsum dolor sit amet ... + 請一步一步解這題……
```

前面的 Lorem Ipsum：

- 不提供任務答案；
- 不提供語意線索；
- 但具有近自然語言的統計結構；
- 可以溫和改變模型的 output distribution。

作者的核心說法是：

> task-irrelevant prompt-space perturbation can shift the model’s output distribution enough to unlock orthogonal reasoning pathways.

也就是說，nonsense perturbation 的價值不是「增加知識」，而是「讓模型不要一直走同一條失敗推理路徑」。

## 為什麼不是越亂越好？

LoPE 的重要觀察是：有效 perturbation 通常不是完全亂碼。

比較粗略地說：

```text
太自然、語意太強：
可能把不相關概念塞進模型注意力與工作區，干擾任務。

太亂、太 OOD：
例如 random tokens / random ASCII，可能破壞模型對題目的理解。

低 perplexity、近自然語言但無強語意：
例如 Lorem Ipsum / pseudo-Latin，能擾動狀態，又不嚴重污染語意。
```

這讓 Lorem Ipsum 成為一種有趣的中間態：它像語言，所以模型處理穩定；但沒有明確任務語意，所以不太會把錯誤概念帶進來。

## J-space 提供什麼新視角？

Anthropic 的 J-space 研究不是在研究 prompt optimization，而是在問：模型內部是否存在一組功能上類似 global workspace 的 representations？

他們提出 Jacobian lens / J-lens：對每個 vocabulary token，估計某個內部 activation 對模型未來 verbalize 該 token 的一階影響。簡化說，就是：

> 這個內部狀態讓模型未來更可能說出、或能談到哪些概念？

J-space 的重要特徵包括：

1. **可報告**：問模型在想什麼，它比較會報告 J-space 裡的內容。
2. **可調控**：要求模型想某個概念，J-space 中相應 pattern 會亮起。
3. **支援內部推理**：多步推理的中間概念可能在 J-space 中出現，即使沒有輸出成 chain-of-thought。
4. **選擇性**：J-space 只是一小部分 activation，不代表模型全部運算。

這裡最重要的是：J-space 不是輸出文字，也不是顯式 CoT，而是 silent internal concept workspace。

## LoPE 和 J-space 的關係：目前是可檢驗假說，不是已證明因果鏈

時間上，LoPE 是 2026-05，Anthropic J-space 是 2026-07，所以 LoPE 並沒有用 J-space 作為理論基礎。

但兩者可以形成一個自然的機制假說：

```text
Lorem / nonsense perturbation
        ↓
改變早期 attention、MLP、residual stream 狀態
        ↓
改變模型載入哪些 latent concepts / intermediate steps
        ↓
J-space 中出現不同 silent concepts
        ↓
模型走出不同 reasoning trajectory
        ↓
解出原本卡住的題目
```

因此，最精準的說法不是「LoPE 證明了 J-space」，而是：

> LoPE 可能是一種外部 prompt-space intervention；J-space / J-lens 則提供未來觀測這種 intervention 如何改變內部推理工作區的工具。

## 更底層的橋：activation redistribution

`Meaningless Tokens, Meaningful Gains` 對這條線提供了更底層的機制說法。它研究 long sequences of meaningless tokens 為什麼能改善 reasoning performance，並提出：

- 影響主要出現在 early layers；
- 對 meaningful tokens 的 effect 可近似為 attention output 的 affine transformation；
- 接著造成 MLP activation redistribution；
- near-zero activations 變少；
- large-magnitude activations 變多；
- reasoning-critical tokens 的表示可能因此被強化。

這提醒我們：LoPE 的效果未必完全經由 J-space。它可能有兩層：

```text
底層：activation redistribution
- 改變 MLP / residual stream 的分布
- 強化或重排 reasoning-critical token representations

高層：workspace reconfiguration
- J-space 中載入不同中間概念
- 形成不同 silent reasoning trajectory
```

也就是說，J-space 可能是可讀的高階窗口，但不一定是全部因果來源。

## 可檢驗的實驗設計

### 實驗 1：比較原 prompt 與 Lorem prompt 的 J-space trajectory

對同一批 hard math questions：

1. 用原 prompt sample 多次。
2. 用 Lorem-perturbed prompt sample 多次。
3. 在各層與各 reasoning token 上讀 J-space contents。
4. 比較成功與失敗樣本中是否出現不同中間概念。

可觀察指標：

- 是否更早出現 `check`、`equation`、`constraint`、`case`、`ERROR` 類概念；
- 是否出現更多 alternative decomposition；
- 成功樣本與失敗樣本的 J-space path 是否可分；
- LoPE 獨有解題是否對應獨有 J-space trajectory。

### 實驗 2：J-space ablation 是否消除 LoPE gain？

若 LoPE 的改善主要經由 J-space，則 suppress / ablate top-k J-space contents 應該會讓 LoPE gain 下降。

若不下降，則可能代表主要原因在非 J-space activation，例如 MLP redistribution。

### 實驗 3：用 J-space steering 取代 Lorem perturbation

如果成功 LoPE rollout 常出現某些 J-space concepts，可以直接 steering 那些 J-lens vectors，例如：

```text
verify
case
constraint
substitution
alternative
```

然後看能否取代 Lorem perturbation。

可能結果：

1. J-space steering 幾乎等於 LoPE：LoPE 主要是 workspace steering。
2. J-space steering 有部分效果但不完整：LoPE 同時動了 workspace 與底層 activation。
3. J-space steering 無效：LoPE 主要是 non-J-space activation redistribution。

目前最合理的猜測是第二種。

## 對 prompt engineering 的重新定義

傳統直覺：

```text
好 prompt = 清楚、完整、合語法、符合人類邏輯
```

這條研究線給出的新定義：

```text
好 prompt = 能穩定把模型推到有利內部狀態的 token sequence
```

自然語言 prompt 是一種控制介面。  
Gibberish prompt、nonsense perturbation、optimized hard prompt 也是控制介面，只是人類不容易讀懂。

這個轉換很重要，因為它把 prompt engineering 從「寫作問題」改寫成「模型介面控制問題」。

## 可重用思考模型

判斷一個 weird prompt 為什麼有效，可以問四層問題：

1. **語意層**：它是否真的提供了任務資訊？還是只是表面看起來像資訊？
2. **分布層**：它是否讓輸入更接近 / 遠離模型熟悉的語言 manifold？perplexity 是否過高？
3. **activation 層**：它是否改變 early-layer attention、MLP activation sparsity、residual stream 分布？
4. **workspace 層**：它是否改變模型在 J-space 中載入的中間概念與推理路徑？

這四層能避免兩種誤解：

- 把所有 prompt 效果都解釋成語意；
- 把所有 gibberish 效果都神秘化成模型「密語」。

更穩的說法是：有些 weird prompt 是 token-level control code；它可能透過 activation redistribution 與 workspace steering 改變模型行為。

## 邊界與風險

1. **不是隨便亂碼都有效**  
   LoPE 的結果反而說明，太 OOD 的亂碼可能破壞理解。有效的是經過搜尋、剪枝、最佳化，或具有低 perplexity 的 perturbation。

2. **不要把 J-space 直接等同於意識**  
   Anthropic 明確把討論限制在 functional access / global workspace 類比，不主張模型有主觀經驗。

3. **jailbreak 與 reasoning improvement 要分開**  
   它們都顯示 weird token sequence 可控制模型，但目標不同：一個是繞過拒答 / alignment，一個是改善探索 / 解題。

4. **prompt optimization 可能 overfit validation set**  
   自動搜尋出的 prompt 分數高，不代表它有穩健、可轉移、可解釋的效果。

## 暫定結論

`Nonsense Helps / LoPE` 與 `J-space` 有關連，但目前是概念與機制上的關連，不是已完成的直接證明。

最好的工作假說是：

> Lorem Ipsum 這類 nonsense prompt 不是提供語意，而是溫和擾動模型 activation，使模型在內部工作區中載入不同的中間概念與推理路徑；其中一部分變化可能可被 J-lens / J-space 觀測，但另一部分可能發生在更底層的 MLP activation redistribution。

這條線的價值在於，它讓我們重新理解 prompt：prompt 不是單純的自然語言寫作，而是對模型內部計算狀態的介面設計。
