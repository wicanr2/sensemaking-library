# When Does Combining Language Models Help? A Co-Failure Ceiling on Routing, Voting, and Mixture-of-Agents Across 67 Frontier Models

## 原文資訊
- 論文：When Does Combining Language Models Help? A Co-Failure Ceiling on Routing, Voting, and Mixture-of-Agents Across 67 Frontier Models
- 作者：Josef Chen
- arXiv ID：2606.27288v1
- 分類：cs.AI, cs.LG
- 發表 / 更新：2026-06-25 / 2026-06-25
- 連結：[abs](https://arxiv.org/abs/2606.27288v1) / [pdf](https://arxiv.org/pdf/2606.27288v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-06-27

## 為什麼選這篇

這篇論文處理的是 LLM 系統設計裡一個很實際的問題：當我們把多個模型放在一起，用 routing、voting、cascade 或 mixture-of-agents 之類的方法，希望超過單一模型時，什麼情況下真的有空間變好？這不是單純的模型能力比較，而是推論編排（inference orchestration）的上限問題。

我選它的原因，是它把「多模型組合」從常見的多樣性直覺，拉回一個更硬的失敗結構問題。摘要和 introduction 都強調，業界常看 pairwise error correlation，也就是模型錯誤之間的兩兩相關；但作者認為真正限制上限的是所有模型在同一題上一起錯的比例 beta。這個觀點對 agent、router、model portfolio、LLM evaluation 都有可重用價值。

另一個值得放進資料庫的點是，作者沒有宣稱提出新的 routing 演算法，而是把既有的 ensemble / portfolio / finite-sample bound 工具，放到有價格、延遲、供應商風險的 LLM 推論市場裡。這種「不發明新模型，而是重新定義系統上限指標」的研究，對判斷多模型架構是否只是工程複雜度，也很有參考性。

## 一句話理解

多個 LLM 放在一起不一定能靠 routing 或 voting 變強，因為真正的天花板不是平均錯誤相關，而是「所有候選模型同時失敗」的共同失敗率。

## Summary / Abstract 說了什麼

摘要指出，routing、voting、cascade、fusion、mixture-of-agents 等多模型 LLM 系統，通常被用來提升單一模型的準確率。作者主張，任何最後輸出仍來自成員模型答案的策略，其準確率都不可能超過 `1 - beta`，其中 beta 是所有模型在同一個 query 上都答錯的比例。

作者進一步說，常用的平均 pairwise error correlation `ρ` 並不能辨識 `β`。這裡可以把每個模型在第 `q` 題是否答錯寫成一個 0/1 變數：

```text
E_i(q) = 1 代表第 i 個模型在第 q 題答錯
E_i(q) = 0 代表第 i 個模型答對
```

兩個模型 `i, j` 的錯誤相關係數大致是：

```text
ρ_ij = Corr(E_i, E_j)
     = Cov(E_i, E_j) / sqrt(Var(E_i) Var(E_j))
```

如果有 `m` 個模型，常見的平均 pairwise error correlation 是把所有模型兩兩配對後取平均：

```text
ρ̄ = 2 / (m(m - 1)) * Σ_{i<j} ρ_ij
```

直覺上，`ρ̄` 衡量的是「平均來看，兩個模型會不會一起錯」。但本文在意的 `β` 是更嚴格的「全部模型同時錯」：

```text
β = P(E_1 = 1, E_2 = 1, ..., E_m = 1)
```

所以問題在於：`ρ̄` 只看兩兩平均，`β` 看的是所有模型錯誤集合的交集。即使兩組模型有相同的邊際錯誤率與兩兩錯誤相關，它們也可能有不同的「全錯」比例。換句話說，`ρ̄` 看起來像多樣性的代理指標，但可能低估真正會一起出錯的尾端風險。

摘要也提到，作者用 Clopper-Pearson bound 給 `β` 一個有限樣本的信賴界。這可以先用最簡化的 binomial 觀點理解：假設測了 `n` 題，其中有 `k` 題是所有模型都答錯，那觀察到的共同失敗率是：

```text
β_hat = k / n
```

但 `β_hat` 只是樣本估計，不等於真實共同失敗率 `β`。Clopper-Pearson bound 會用 binomial distribution 的反函數，給出在信心水準 `1 - α` 下的保守區間。若只關心「最壞可能有多高」的上界，常見形式是：

```text
β_upper = BetaInv(1 - α; k + 1, n - k)   （k < n 時）
```

意思是：在這批樣本中看到 `k` 次共同失敗後，我們可以保守地說，真實 `β` 大約不會超過 `β_upper`（信心水準為 `1 - α`）。這讓研究者在訓練 router 之前，就可以估計任何 router、vote 或 cascade 最多還有多少可提升空間。實證部分涵蓋 21 個供應商的 67 個模型；在開放式數學任務上，觀察到的 `β` 高於單因子模型估出的 `β`，表示現有模型的共同失敗尾端可能比傳統相關結構想像得更重。

以上整理只根據摘要；我沒有檢查後文的實驗設計、資料集細節或統計檢定。

## Introduction 的問題設定

Introduction 的開場把問題放在「單一模型時代正在結束」這個實務背景中。企業不只選一個最佳模型，而是在成本、延遲、可靠性、能力、供應商風險之間，對許多模型做配置。因此，核心問題從「哪個模型最好」轉成「如何把 token 與 dollar budget 分配到一組異質、相關、快速折舊的模型池」。

作者接著指出，實務上常用 pairwise error correlation `ρ̄` 來判斷模型多樣性：`ρ̄` 低，好像代表模型錯誤比較不重疊，因此組合會有價值。但作者的中心主張是，`ρ̄` 不是正確的上限指標；真正限制 orchestration 的是 `β`，也就是所有模型同時在同一題失敗的比例。只要某些問題是整個模型池一起答錯，無論後面用 router、vote 或 cascade，都無法從成員模型中拿到正確答案。

Introduction 也明確降低新穎性宣稱。作者承認等相關變異下限、Gaussian-copula ensemble、oracle upper envelope、routing / cascading optimality、linear-programming duality、Clopper-Pearson interval 等工具都有既有來源。這篇的貢獻不是新 router，而是把這些工具專門化到 priced inference orchestration，並做市場尺度的量測。

作者列出的貢獻包括四塊：第一，提出 orchestration ceiling 和有限樣本 certificate；第二，說明為什麼 pairwise `ρ̄` 會低估共同失敗，尤其在 tail dependence 下 `β` 估計會偏低；第三，在 67 個模型、21 個 provider family 上做量測，並指出 open-ended math 這類任務可能是 ceiling-bound，而 science 任務可能是 realizability-bound；第四，把成本限制下的 routing、diversification limit、cascade calibration boundary 等經濟面放在 appendix 支撐。

## 研究的第一性問題

- 基本問題：如果多模型系統的輸出仍來自既有模型答案，什麼統計量決定它最多能比單一模型好多少？
- 約束：router、voting、cascade 並不能創造成員模型都沒有的正確答案；它們最多是在模型之間選對、合併或節省成本。
- 既有方法卡點：用 pairwise error correlation `ρ̄` 代表多樣性，可能只看到平均兩兩關係，卻看不到所有模型同時錯的共同尾端 `β`。
- 作者試圖移動的邊界：把「多模型組合是否有價值」從演算法事後比較，前移成可在訓練 router 前估計的上限 certificate。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 對任何輸出為成員模型答案的 routing、voting、cascade 策略，準確率上限是 `1 - β`。
- 單一最佳模型到 oracle routing 的可提升空間，可用「單一最佳模型錯誤率」與 `β` 的差來定位。
- pairwise `ρ̄` 不能可靠辨識 `β`；在共同失敗尾端較重時，會低估真正的全錯風險。
- Clopper-Pearson bound 可以把一組已評分 query 轉成有限樣本 certificate，用來估計多模型 orchestration 的最大可能收益。
- 在 67 個 frontier models 的市場尺度量測中，存在 oracle gain，但 learned router 未必能實現；不同任務可能分別受 ceiling 或 realizability 限制。

### 我的保守判讀

- 這篇的價值可能不在「證明多模型系統無效」，而在提醒：多模型系統的收益要先看共同失敗率，否則容易把工程複雜度誤認為能力提升。
- `β` 是一個很直觀但常被忽略的 diagnostic；它適合用來當作模型池採購、benchmark 設計、router 訓練前的 sanity check。
- 但我目前沒有讀 methods / experiments / appendix，因此不能判斷 67 個模型的樣本選擇、任務設計、grading 方法、router 訓練設定是否足以支撐所有經驗結論。
- 若任務允許模型產生新答案、外部工具查證、或人類審核介入，`1 - β` 這個上限如何延伸，需要讀全文確認。摘要裡的限定是「output is one member model answer」。

## 可放進資料庫的筆記

1. 多模型組合的核心不是「模型數量」，而是錯誤集合的交集大小。
2. Pairwise diversity 不等於 tail diversity；兩兩看起來分散，仍可能在難題上一起失敗。
3. Router 的上限要先估，再談訓練；否則可能是在低天花板任務上追逐不可實現的增益。
4. 「所有模型都錯」是模型市場的共同模式風險，類似金融投資組合裡的 tail co-movement。
5. 對 LLM 系統來說，evaluation 不只要報單模型 accuracy，也應該報 model pool 的 all-wrong rate。
6. Mixture-of-agents 若最後仍只是在既有答案間選擇，能力上限和「是否有人答對」高度綁定。
7. 一個好的 orchestration metric 應該能在部署前提供 stop/go signal，而不是只在實驗後解釋結果。
8. 這篇可作為「LLM portfolio management」的基本概念入口：能力、成本、延遲與共同失敗風險需要一起看。

## 後續想追的問題

1. 作者如何定義「正確」與「全錯」？不同任務的 grader 是否會影響 beta 估計？
2. 67 個模型的 provider family 與模型版本如何分布？是否有某些模型高度同源，導致共同失敗率偏高？
3. learned router 幾乎沒有實現 oracle gain 的原因，是特徵不足、訓練資料不足，還是任務本身不可辨識？
4. 如果系統允許外部工具、self-correction、或生成新答案，這個 co-failure ceiling 是否仍以相同形式成立？
5. beta 是否可以成為企業採購模型池時的標準報表，例如和成本、延遲、合規風險一起列入決策？
