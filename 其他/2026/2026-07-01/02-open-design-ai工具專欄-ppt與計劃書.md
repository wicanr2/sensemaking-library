# 工具專欄｜Open Design：把 Agent 變成簡報與計劃書的設計工作台

## 工具資訊

- 工具名稱：Open Design
- 官方網站：[`https://open-design.ai/zh/`](https://open-design.ai/zh/)
- GitHub：[`nexu-io/open-design`](https://github.com/nexu-io/open-design)
- 產品定位：local-first、open-source 的 AI design workspace；官方自稱是 Figma / Claude Design 的 agent-native 替代。
- 授權與部署：官方頁面與 GitHub metadata 顯示採 Apache-2.0；桌面端、本地 daemon、Skill runtime 以本機執行為核心，也支援 BYOK。
- 本次閱讀範圍：官方中文首頁、Slides / PM / Templates / Skills / Pricing / Download / Hermes agent 頁面、GitHub repo metadata。未實機安裝測試，因此以下是產品概念與文件層級評估，不是實測報告。
- 收錄性質：使用者指定「工具專欄」。

## 一句話概述

> Open Design 不是單純「AI 幫你寫 PPT 文字」，而是把本地 coding agent、設計系統、模板、SKILL.md 工作流和 HTML / PDF / PPTX 匯出串成一個設計交付管線。

如果把 Word 計劃書與 PPT 提案都看成「說服型文件」，Open Design 比較像是在處理最後一公里：把內容骨架轉成可被閱讀、展示、評審的視覺 artifact。

## 產品結構

從官方文件看，Open Design 的核心不是單一模型，而是一個 workflow layer：

```text
使用者輸入：prompt / Markdown / CSV / JSON / repo / 截圖 / Figma 匯出
→ Agent：Claude Code、Codex、Cursor、Gemini CLI、OpenCode、Qwen、Hermes 等
→ Skill：SKILL.md 定義任務流程、輸出規格、模板規則
→ Design System：字體、色彩、版面、品牌規則
→ Artifact：HTML prototype / deck / image / video / PDF / PPTX
```

它的產品主張有幾個關鍵詞：

1. **Local-first**：桌面端、daemon、Skill runtime 在本機跑，artifact 落在專案目錄。
2. **BYOK**：用自己的模型供應商 key，官方頁面列出 OpenAI、Anthropic、Google、Mistral、xAI、Moonshot 等相容端點。
3. **Agent-native**：不把 AI 當聊天框，而是讓 coding agent 讀 Skill、寫檔、渲染、修正。
4. **模板 / 設計系統化**：官方模板頁標示有大量模板與設計系統，包含 deck、prototype、image、video、HyperFrames 等類型。
5. **可匯出**：Slides 頁面明確說 deck 可用瀏覽器全螢幕展示，也可匯出 PDF / PPTX；HTML Anything 則強調可把 Markdown、CSV、JSON 轉成可交付 HTML。

## 對 PPT 的幫助

### 1. 從「大綱」直接變成有設計感的 deck

官方 Slides 頁面的核心描述是：

> 貼上要點或粗略結構，agent 載入 deck skill，產出一連串排好版的幻燈片，而不是一篇長文。

這對 PPT 的幫助很直接。傳統做法常卡在：

```text
先寫內容 → 再開 PowerPoint → 選模板 → 調字級 → 對齊 → 補圖表 → 統一風格
```

Open Design 試圖把它改成：

```text
先給大綱 / 材料 / 語氣
→ 選 deck style / design system
→ agent 產生 HTML deck
→ 用對話修正
→ 匯出 PDF / PPTX
```

它的價值不只是節省時間，而是把「版面一致性」變成模板與 Skill 的責任。

### 2. 可做比普通 PPT 更像網頁 / 展示頁的簡報

官方列出的 deck 類模板很多，例如：

- 融資演示 pitch deck
- 產品發布 deck
- 投行路演材料
- 技術分享
- 學術論文風
- 年度信 / 週報 / QBR 類型
- Bento 洞見、知識架構藍圖、瑞士國際主義、暗色技術風等

這代表 Open Design 的簡報思路比較接近「HTML-based presentation」：用固定 16:9 畫布、CSS grid、SVG、動畫、資料卡片去組合，而不是 PowerPoint 內建物件的手工排列。

優點是視覺彈性高；缺點是若客戶最後要求完全可編輯的 `.pptx`，匯出保真度就會變成關鍵風險。

### 3. 對商務簡報最有用的場景

我會把適用場景分成三類：

1. **提案初稿 / 方向對齊**  
   例如產品提案、內部專案簡報、技術方案簡報。先快速做出可看的 deck，讓討論集中在主張與結構，而不是空白投影片。

2. **高視覺要求的 storytelling deck**  
   例如募資簡報、產品發布、年度策略、董事會簡報。Open Design 的模板庫比較能把敘事、數字、圖像與版面合成一個 coherent visual language。

3. **把研究筆記轉成可演示內容**  
   對這個 `thought_notebook` 很有啟發：Markdown 筆記可以先沉澱邏輯，再用 Open Design / HTML deck 模板轉成演講或簡報。

## 對 Word 計劃書 / 企劃書的幫助

Open Design 官方比較強調 HTML、prototype、slides，而不是 Word `.docx`。所以它對 Word 的幫助要拆開看。

### 1. 直接幫助：把計劃書變成「可讀的視覺文件」

官方 PM 頁面列出「產品規格文檔 / PRD」模板；Templates 頁也有：

- 產品規格文檔
- 會議紀要
- 週報更新
- 財務報告
- 工程運維手冊
- 數據可視化報告
- digital e-guide
- docs page

這些不一定是 Word，但它們覆蓋了 Word 計劃書常見的內容結構：問題定義、成功指標、範圍、用戶故事、設計筆記、上線計畫、待解問題、KPI、財務摘要、行動項。

換句話說，它可以幫你把「計劃書內容」整理成：

```text
單頁 HTML spec
可視化報告頁
可點擊原型
簡報版 pitch deck
PDF 交付物
```

這對提案前期很有價值，因為很多計劃書真正難的是：內容看起來像散文，無法讓主管或客戶快速抓到結構。

### 2. 間接幫助：先做 HTML / PDF，再回填 Word

如果最終交付必須是 Word，Open Design 目前看起來不是最直接的 `.docx` 編輯器。比較務實的工作流是：

```text
Open Design 產出結構化 HTML / PDF / deck
→ 人工或工具轉成 Word 章節
→ Word 裡處理正式格式、頁眉頁腳、公司範本、簽核欄位
```

也就是說，它適合做「計劃書的構思、視覺化、對齊」，不一定適合取代 Word 的正式交付流程。

### 3. 對計劃書最有用的部分不是寫字，而是結構化

計劃書通常要回答：

1. 為什麼要做？
2. 誰會受益？
3. 現在卡在哪？
4. 解法是什麼？
5. 里程碑與資源需求？
6. 風險與替代方案？
7. 成功指標？

Open Design 的 Skill / template 模式，可以把這些問題變成固定骨架。這對反覆寫 proposal、PRD、內部專案計畫的人很有幫助：不是每次從空白頁開始，而是讓 agent 按模板補足應有欄位。

## 第一性原理：它真正解的是什麼問題？

PPT 與 Word 計劃書的本質，不是文件格式，而是「降低決策者理解成本」。

```text
文件價值 = 內容可信度 × 結構清晰度 × 視覺可讀性 × 可交付性
```

傳統 AI 寫作工具多半只處理第一段：幫你生成文字。Open Design 想補的是後三段：

- 結構清晰度：Skill 把任務拆成固定輸出形式。
- 視覺可讀性：Design system / template 負責版面、字體、層次。
- 可交付性：HTML、PDF、PPTX、PNG 等 artifact 可被展示或分享。

所以它的核心不是「比較會寫」，而是「比較會把文字變成可看的交付物」。

## 我會怎麼用在 PPT / 計劃書工作流

### 工作流 A：企劃書先行，再轉 deck

```text
1. 先用一般文字工具整理問題、目標、範圍、預算、時程、風險。
2. 丟給 Open Design，要求生成 PRD / proposal-style HTML spec。
3. 檢查是否漏掉成功指標、風險、資源需求。
4. 再用 deck template 轉成 10-15 頁簡報。
5. 匯出 PDF 給審閱；若對方要求可編輯，再匯出 PPTX 並人工修。
```

適合：內部新專案、產品功能提案、導入 AI / 自動化工具的方案。

### 工作流 B：PPT 先行，用來逼出決策重點

```text
1. 先寫 8-10 頁 deck 大綱。
2. Open Design 產生投影片。
3. 看每一頁是否只有一個 decision point。
4. 把通過審查的 deck 反向展開成 Word 計劃書。
```

適合：董事會 / 客戶簡報、募資簡報、策略提案。

### 工作流 C：研究筆記變簡報

```text
thought_notebook Markdown
→ Open Design HTML deck
→ PDF / PPTX
→ 對外分享或內部 briefing
```

這可能是對目前筆記庫最實用的路徑：筆記負責深度，Open Design 負責把深度變成可展示的表面。

## 限制與風險

1. **PPTX 保真度仍要實測**  
   官方說可匯出 PPTX，且 Skill 目錄中有 PPTX HTML fidelity audit 類工具；這反而提醒我們，HTML deck 轉 PPTX 本來就可能有字體、裁切、斜體、間距、頁腳溢出等問題。

2. **Word `.docx` 不是核心能力**  
   目前公開頁面重心是 HTML / PDF / PPTX / prototype。若公司流程強制 Word 範本、簽核欄、頁碼、目錄、樣式表，Open Design 應定位為前期設計與轉譯工具，不是 Word 最終排版工具。

3. **內容正確性仍靠人審**  
   它可以把計劃書做漂亮，但不能自動保證市場數據、預算、法規、客戶需求正確。

4. **需要本地 agent / CLI 基礎**  
   官方 FAQ 說目前最少需要本地 daemon 加一個 agent；這對非技術使用者不是零摩擦。

5. **模板美感不等於品牌合規**  
   公司正式簡報常有品牌規範、法務揭露、保密標示、頁尾格式。Open Design 可以學 design system，但落地前仍要建立公司專用模板與審查清單。

## 對你的用途的判斷

如果目標是「更快生出能看的 PPT」，Open Design 很有用，尤其是：

- 把筆記 / 報告變成簡報；
- 產生多種視覺風格版本；
- 做 pitch deck、產品提案、研究 briefing；
- 先用 HTML / PDF 對齊，再決定要不要細修 PPTX。

如果目標是「正式 Word 計劃書自動完稿」，它不是最直接的工具。更合理的定位是：

> 用 Open Design 先把計劃書的邏輯、版面、圖表、簡報版跑出來；最後的 Word 正式版仍用既有文件工具或公司範本收尾。

## 可放進資料庫的筆記

> Open Design 的價值不在於取代 PowerPoint 或 Word，而在於把 agent、Skill、design system 和 HTML / PDF / PPTX artifact 串成「文件交付前的設計工作台」。對 PPT，它能把大綱快速變成有一致視覺語言的 deck；對 Word 計劃書，它更適合做 PRD / proposal 的結構化、視覺化與簡報化，而不是直接取代 `.docx` 最終排版。第一性原理上，它解的是決策文件的理解成本：文字只是輸入，真正的產物是讓人快速看懂、願意討論、可以拿去展示的 artifact。

## 後續可以實測的問題

1. 用同一份 `thought_notebook` 筆記，生成 HTML deck、PDF、PPTX，檢查三者版面保真度。
2. 建立一個「台灣企業計劃書」Skill：問題、背景、目標、資源、時程、風險、KPI、預算。
3. 建立一個「投資 / 董事會簡報」Design System：固定頁腳、資料來源、風險揭露、估值表格。
4. 測試 Word 工作流：HTML / Markdown → docx 是否能保留階層、表格、圖表與頁碼。
