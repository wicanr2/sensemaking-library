# Business Forensics AI / Imara：用在美股與台股分析的價值評估

## 原始資訊

- 專案：[`bouwerruan10-boop/business-forensics-ai`](https://github.com/bouwerruan10-boop/business-forensics-ai)
- 本機副本：`~/tmp2/business-forensics-ai`
- 本次評估日期：2026-06-29
- 關聯筆記：[Business Forensics AI / Imara：公開 repo 是否已經「leak」的專案稽核筆記](./01-business-forensics-ai-leak-audit.md)
- 本筆記問題：這個專案若拿來做 **美股 / 台股投資研究、基本面分析、財報拆解與風險評估**，有多少可用價值？哪些部分可直接借用？哪些部分需要重建？

## 一句話結論

這個專案對股票分析的價值不在於「可以直接拿來預測股價」，而在於它已經做出一套很接近 **投資研究底層工作流** 的東西：把公司資料丟進系統，先用 deterministic arithmetic 產生可追溯的財務比率、壓力測試、現金流與困境訊號，再讓多個 agent 從財務、營運、法律、策略、市場與風險角度寫成報告。

但它原本是為 **南非 SME 授信 / bankability / business-forensics** 設計，不是為上市公司二級市場投資設計。因此若直接用在美股或台股，價值大概是：

- **作為單家公司「財務體檢 / 盡調 / 風險雷達」：中高價值。**
- **作為股票買賣訊號 / alpha model：低價值，除非大改資料管線與回測框架。**
- **作為美股 / 台股研究助理的架構參考：高價值。**
- **作為可直接部署的股票分析產品：中低價值，因為 benchmark、法規、資料來源、估值方法與時間序列比較都要重建。**

我的判斷：它最值得移植的是 **「數字歸數字、敘事歸敘事」** 的架構，而不是它目前的南非 SME prompt 或 Imara Score 本身。

---

## 一、專案裡已經有用於股票研究的核心能力

### 1. Deterministic financial-ratio engine：最可移植的部分

`backend/services/financial_ratios.py` 的設計很重要。它明確把財務數字從 LLM 敘事中拆出來：

- 從 P&L / balance sheet 文字中抽取：revenue、COGS、gross profit、operating profit、net profit、current assets、inventory、receivables、current liabilities、payables、total debt、equity、interest、total assets、total liabilities、retained earnings 等。
- 用純 Python 計算比率：gross margin、net margin、operating margin、current ratio、quick ratio、debtor days、creditor days、inventory days、debt-to-equity、interest coverage。
- 每個 ratio 都保留 source figures，讓報告可以追溯「這個數字是怎麼來的」。
- `fundamentals_score()` 再把若干可用比率合成 0–100 分的 fundamentals score。

這對股票研究很有價值，因為投資研究最怕的是：LLM 看起來講得很像，但 EPS、毛利率、現金流、負債比其實算錯。這個專案的好習慣是：

> LLM 不負責算數，LLM 只負責解釋已經算好的數字。

這點可直接用在美股 / 台股財報分析。只要把輸入資料從 SME 上傳檔案，換成 SEC 10-K / 10-Q、台灣公開資訊觀測站財報、TEJ / FinMind / yfinance / Financial Modeling Prep / Polygon 等資料源，就能形成一個比較可靠的基本面研究底座。

### 2. Altman Z'' distress score：可作為財務風險訊號，但不能當股票評價核心

`backend/services/distress_score.py` 實作 Altman Z'' emerging-markets variant：

\[
Z'' = 3.25 + 6.56X_1 + 3.26X_2 + 6.72X_3 + 1.05X_4
\]

其中：

- \(X_1\)：Working Capital / Total Assets
- \(X_2\)：Retained Earnings / Total Assets
- \(X_3\)：EBIT / Total Assets
- \(X_4\)：Book Value of Equity / Total Liabilities

專案用它當作 Imara Score 的 external convergence anchor：若 Z'' 與 Imara band 同方向，代表風險判斷有外部模型支撐；若分歧，就提醒使用者重看財務資料。

對股票分析來說，這個模組可用於：

- 小型股、景氣循環股、資產負債表壓力股的「財務困境雷達」。
- 台股中小型電子零組件、傳產、營建、航運等公司遇到庫存、負債、景氣反轉時的第一層風險篩選。
- 美股 small cap / micro cap / high leverage company 的破產風險檢查。

但它不適合：

- 當成買賣訊號。
- 評估軟體平台、金融股、生技研發型公司、早期 AI / SaaS 成長股的核心價值。
- 替代 sector-specific 指標，例如銀行要看 NIM / NPL / CET1，保險要看 combined ratio / RBC，半導體要看 capex cycle / utilization / inventory days / gross margin normalization。

所以它是「風險下限檢查」，不是「投資上行判斷」。

### 3. Valuation Agent：有估值骨架，但目前是 SME 估值，不是上市股估值

`backend/agents/specialist_agents.py` 的 `ValuationAgent` 已經包含一個估值框架：

- normalised EBITDA
- EBITDA multiple
- DCF sanity check
- asset-based floor
- low / mid / high valuation range
- value drivers / detractors

這些概念和股票估值有交集，但目前 prompt 明確寫的是 **owner-managed SMEs**、南非 SME 交易常見 2.5x–5x normalised EBITDA、ZAR range。這不能直接搬到美股 / 台股。

若要改成股票研究，需要至少重建：

- 市值 / 企業價值：market cap、net debt、minority interest、preferred stock。
- per-share 指標：EPS、book value per share、free cash flow per share、dividend per share。
- listed-company multiples：P/E、forward P/E、EV/EBITDA、EV/Sales、P/B、P/FCF、PEG。
- sector-specific multiples：半導體 EV/Sales + gross margin + capex intensity；銀行 P/B + ROE；保險 P/B + underwriting；SaaS EV/ARR + Rule of 40；生技 probability-adjusted pipeline。
- 歷史區間比較：5 年 / 10 年 valuation percentile。
- 同業比較：美股同業、台股同業、跨市場 ADR / local listing 比較。
- 匯率與會計準則差異：USD / TWD、IFRS / US GAAP、ADR ratio。

因此，`ValuationAgent` 的價值是「報告結構與估值敘事骨架」，不是現成估值模型。

### 4. Action Simulator / 13-week cashflow：適合做營運情境，不適合直接做股價情境

`backend/services/simulation.py` 已經有 driver-based scenario model：

- revenue growth
- price increase
- gross margin improvement
- opex reduction
- debtor days reduction
- inventory days reduction
- supplier saving
- optimistic / expected / pessimistic capture rate

`backend/services/cashflow_13week.py` 也有 13 週 direct-method cash-flow projection，用 annual run-rate 估短期 liquidity。

這對股票研究的價值是：

- 可以把「毛利率改善 100 bps」、「營收成長 10%」、「庫存天數下降」、「營業費用率下降」這種驅動因子做成可重算模型。
- 對台股製造業、通路、零售、航運、營建這種營運週期明顯的公司，能幫忙把敘事轉成財務敏感度。
- 對美股消費、工業、SaaS，也可做 margin / growth / opex leverage 的情境分析。

但目前它缺少上市股分析最需要的三件事：

1. **股本與稀釋**：shares outstanding、diluted shares、SBC、buyback、現增 / CB / ADR。
2. **市場價格連結**：目前沒有股價、market cap、EV、估值倍數與 expected return 的連動。
3. **歷史時間序列**：目前像是 single-period operating model，不是多季 / 多年面板資料模型。

所以它適合拿來做「公司營運體質模擬」，但還不是「股票報酬模擬」。

### 5. Market Research Agent：方向對，但搜尋與資料源要換成金融資訊源

`backend/agents/market_research_agent.py` 目前用 Serper.dev 做 Google Search / News，搜尋公司名稱、產業趨勢、競爭者、新聞、評論與 public reputation。

這對股票研究有用，但需要換資料源與查詢策略：

- 美股：SEC filings、company IR、earnings call transcript、8-K、10-K、10-Q、investor presentation、analyst estimates、short interest、insider transactions、FRED / BEA / BLS macro。
- 台股：公開資訊觀測站、證交所 / 櫃買中心、公司法說會、月營收、財報、重大訊息、股利政策、投信 / 外資 / 自營商買賣超、集保股權分散、產業新聞。
- 新聞只應當補充 catalyst，不應當作財務事實來源。

目前 Market Research Agent 比較像「公司外部存在感 / 競品 / reputation」工具；若用於股票，需要變成「證券研究資料擷取器」。

---

## 二、用在美股分析：價值與限制

### 可直接產生價值的場景

1. **10-K / 10-Q 財報體檢**
   - 抽取收入、毛利、營業利益、淨利、資產、負債、現金、應收帳款、庫存、利息費用。
   - 自動算 margin、current ratio、debt-to-equity、interest coverage、inventory days、receivable days。
   - 生成「財務風險 / 營運效率 / 利潤品質」摘要。

2. **小型股或高槓桿公司的風險掃描**
   - Altman Z''、interest coverage、current ratio、cash runway、working capital deterioration。
   - 對 small cap、turnaround、meme stock、資金壓力股有用。

3. **財報電話會議後的敘事驗證**
   - 管理層說「margin pressure temporary」，系統可對照毛利率、營業費用率、庫存、應收款變化。
   - 管理層說「demand strong」，系統可對照 revenue growth、deferred revenue、bookings proxy、inventory / backlog。

4. **同業比較前的單家公司拆解**
   - 先把每家公司拆成一組可追溯 ratio / finding，再進行 peer comparison。

### 主要限制

- 美國上市公司財報結構比 SME 複雜，segment、non-GAAP、stock-based compensation、deferred revenue、lease liabilities、tax effects 都需要專門處理。
- 金融、保險、REIT、生技、資源股不能用同一套通用 SME ratio。
- 沒有 analyst estimate / forward guidance / consensus revision，就很難做股價反應分析。
- 沒有 price history / factor exposure / backtest，就不能宣稱 alpha。

### 對美股的實用定位

比較合理的定位是：

> 「10-K / 10-Q + earnings call 的 forensic fundamental analyst」，不是「自動選股機」。

---

## 三、用在台股分析：價值與限制

### 台股反而有幾個很適合的地方

台股很多公司是製造、零組件、通路、營建、航運、傳產與電子供應鏈，這些公司非常吃：

- 毛利率變化
- 庫存天數
- 應收帳款天數
- 營業利益率
- 匯率與原物料成本
- 資本支出與折舊
- 客戶集中度
- 產業循環

這和專案現有的 business-forensics / operations / working-capital / margin leakage 思路其實很接近。特別是：

- `financial_ratios.py` 的 inventory / receivable / payable days 可改造成台股供應鏈風險雷達。
- `simulation.py` 的 margin / revenue / inventory drivers 可改造成景氣循環敏感度模型。
- `macro_data.py` 的 firm macro-sensitivity 概念可改成 TWD / USD 匯率、台灣利率、電價、原物料價格、出口需求。
- multi-agent 報告可對「財務、營運、策略、法務、競爭、景氣」做一家公司綜合拆解。

### 台股需要補的關鍵資料管線

若要真的做台股，需要至少補：

1. **公開資訊觀測站資料擷取**
   - 年報、季報、月營收、重大訊息、法說會簡報、股利政策。

2. **交易與籌碼資料**
   - 股價、成交量、三大法人、融資融券、借券、集保股權分散。

3. **台股產業 taxonomy**
   - 半導體設計、晶圓代工、封測、PCB、被動元件、伺服器、散熱、光通訊、電源、工具機、營建、金融、航運等。

4. **會計與揭露差異**
   - 台股多使用 IFRS，月營收是重要訊號；美股沒有完全對應的月營收資料。

5. **供應鏈關係圖**
   - 台股常常不是看單家公司，而是看在 NVIDIA / Apple / AMD / AWS / Meta capex 鏈條中的位置。

### 對台股的實用定位

更合理的定位是：

> 「台股公司基本面與營運週期拆解器」，尤其適合製造 / 供應鏈 / 傳產 / 營建 / 通路；不適合直接當短線籌碼或技術分析工具。

---

## 四、哪些部分不該直接使用

### 1. 不該直接使用 Imara Score 當股票評分

Imara Score 是為 SME bankability / lender decision-support 設計。它回答的是：「這家公司是否可被放款、是否具備營運與信用體質？」

股票投資問的是另一組問題：

- 現價是否已反映風險？
- 未來 earnings revision 往上或往下？
- 市場共識錯在哪裡？
- 估值倍數相對歷史與同業是便宜還是貴？
- 催化劑與時間尺度是什麼？
- 下檔風險與上行空間是否不對稱？

所以 Imara Score 最多可改造成「quality / balance-sheet / operating-risk subscore」，不能直接當 buy/sell/hold。

### 2. 不該直接使用南非 SME benchmark

`benchmark_service.py` 明確偏向 African market profiles，包括 South Africa、Nigeria、Kenya、Zimbabwe，且許多 benchmark 是 SME / industry median。這和美股 / 台股上市公司不可直接對照。

需要重建：

- 美股 sector benchmark：GICS / NAICS / FactSet RBICS / Damodaran industry data。
- 台股 benchmark：TEJ 產業、證交所產業、供應鏈自訂分類。
- 同業分組：不能只靠產業名稱，還要看商業模式與客戶結構。

### 3. 不該用 Google Search 取代正式金融資料

Search / News 可以用於輔助 catalyst，但不能當財報數字、股價、股本、估值倍數、法人籌碼的主資料源。

股票系統要有資料層級：

1. 官方 filings / exchange disclosure
2. 公司 IR / 法說會
3. 財務資料供應商
4. 新聞 / transcript / research commentary
5. 社群與市場噪音

目前專案只做到第 4 層附近，還沒建立第 1–3 層的可靠管線。

---

## 五、若要改造成美股 / 台股研究系統，建議架構

### Layer 1：資料 ingestion

- 美股：ticker → SEC CIK → 10-K / 10-Q / 8-K / earnings transcript / price history / estimates。
- 台股：股票代號 → 公開資訊觀測站 / 月營收 / 季報 / 年報 / 重大訊息 / 法說會 / 股價籌碼。
- 統一成 company facts store：income statement、balance sheet、cash flow、segment、shares、market price、events。

### Layer 2：deterministic metrics

把目前 `financial_ratios.py` 擴成：

- growth：YoY / QoQ revenue, gross profit, operating income, EPS, FCF。
- quality：gross margin, operating margin, ROIC, ROE, accruals, cash conversion。
- balance sheet：net cash / net debt, debt maturity, interest coverage, current ratio。
- working capital：inventory days, receivable days, payable days。
- valuation：market cap, EV, P/E, EV/EBITDA, EV/Sales, P/B, P/FCF, dividend yield。
- shareholder return：buyback, dividend payout, dilution。
- sector-specific metrics：bank NIM / NPL / CET1；SaaS ARR / NRR / Rule of 40；semiconductor capex / utilization / inventory。

原則仍然是：**所有數字由 deterministic code 計算，agent 只引用與解釋。**

### Layer 3：agent roles 重設

把原本 SME 11 agents 改成投資研究 agents：

1. Financial Statement Agent：財報品質、三表勾稽。
2. Business Model Agent：收入結構、護城河、客戶與供應鏈。
3. Industry Cycle Agent：景氣循環、同業比較、產能與價格。
4. Valuation Agent：估值區間、歷史分位、同業 multiples。
5. Risk Agent：資產負債表、法律、治理、會計紅旗。
6. Catalyst Agent：財報、法說、產品、政策、訂單、庫存循環。
7. Taiwan Microstructure Agent：若是台股，加入月營收、法人、籌碼、集保、融資融券。
8. US Market Agent：若是美股，加入 SEC filing、analyst revision、guidance、macro factor。
9. Thesis Synthesizer：形成 bull/base/bear、關鍵假設、需追蹤指標。

### Layer 4：投資輸出格式

不要輸出「Imara Score」，而是輸出：

- 投資問題：市場目前可能錯估什麼？
- Bull / Base / Bear thesis。
- 核心數字表：成長、利潤率、現金流、估值、資產負債表。
- 估值區間與 implied return。
- 風險清單與反證條件。
- 下一季 / 下一月要追蹤的 3–5 個 leading indicators。
- 「不構成投資建議」與資料來源界線。

---

## 六、我會怎麼評分它對股票分析的價值

| 面向 | 目前價值 | 原因 |
|---|---:|---|
| 財報數字抽取與 ratio 計算 | 7/10 | 已有 deterministic arithmetic 與 source trace；但需支援上市公司三表與多期資料。 |
| 財務風險 / 困境掃描 | 7/10 | Altman Z''、流動性、槓桿、利息保障倍數可用；但產業適配不足。 |
| 營運改善 / 情境模擬 | 6/10 | driver-based simulation 很適合拆 margin / working capital；但未連到股本、EV、股價。 |
| 估值分析 | 4/10 | 有 EBITDA / DCF 骨架；但目前是 SME valuation，不是 listed equity valuation。 |
| 美股適配度 | 4/10 | 需要 SEC / price / estimates / sector metrics 管線。 |
| 台股適配度 | 5/10 | 台股製造與供應鏈分析很契合 working-capital 思路；但需要公開資訊觀測站、月營收、籌碼資料。 |
| 直接產生交易訊號 | 2/10 | 沒有價格時間序列、回測、factor model、portfolio construction。 |
| 作為投資研究系統架構參考 | 8/10 | multi-agent + deterministic metrics + evidence trail 的方向非常對。 |

## 七、最值得借走的思維模型

### 模型 A：先做「會計真相層」，再做「投資敘事層」

很多 AI 股票分析容易直接摘要新聞與財報，然後產生一段看似合理的投資建議。這個專案更好的地方是，它先把財務資料轉成可驗證的 ratio / score / scenario，再讓 agent 寫解釋。

可重用思維：

> 投資研究不是讓 LLM 猜答案，而是讓 LLM 解釋一組可重算的證據。

### 模型 B：把公司當成「運轉中的現金機器」，不是只看 EPS

專案很重視 working capital、cash conversion、debtor days、inventory days、13-week cashflow。這對台股尤其有用，因為很多公司股價轉折不是 EPS 當下，而是：

- 庫存開始堆高或去化。
- 應收帳款變慢。
- 毛利率先於營收轉折。
- 客戶拉貨 / 砍單在月營收與庫存裡先反映。

這是比「新聞利多利空」更接近基本面轉折的觀察方式。

### 模型 C：不要把單一分數神化

Imara Score 在 SME 授信裡已經需要 Altman Z'' 交叉驗證；股票研究更不能只看單一總分。比較好的設計是：

- quality score
- growth score
- valuation score
- balance-sheet risk score
- earnings revision / catalyst score
- governance / accounting risk score

最後不是給一個神秘總分，而是給「這檔股票目前靠哪一種假設成立」。

---

## 八、實作優先順序

若要最小成本改造成美股 / 台股研究工具，我會這樣排：

### Phase 1：不要碰股價，先做財報拆解器

- 輸入 ticker + 財報。
- 產出 deterministic ratios、三表摘要、風險紅旗、bull/base/bear business drivers。
- 對台股先支援月營收與季報；對美股先支援 10-K / 10-Q。

### Phase 2：加入同業與歷史比較

- 同公司 5 年趨勢。
- 同業群 benchmark。
- 毛利率、營益率、ROE、庫存天數、應收天數、負債比的歷史分位。

### Phase 3：加入估值與 implied return

- market cap / EV / share count / net debt。
- multiples 與 DCF。
- bull/base/bear 目標價，不輸出單點預測。

### Phase 4：加入 catalyst tracker

- 美股：earnings date、guidance、analyst revision、8-K、insider。
- 台股：月營收、法說會、重大訊息、法人籌碼、供應鏈新聞。

### Phase 5：才考慮回測與策略

- 定義可量化 signal。
- 避免 look-ahead bias。
- 做 sector-neutral / market-neutral / long-only 回測。
- 評估 turnover、交易成本、容量。

---

## 最終判斷

如果目標是「做一個幫我看美股 / 台股公司的研究助理」，這個專案很有參考價值，尤其是 deterministic ratio engine、財務風險掃描、multi-agent 報告架構、情境模擬與 evidence trail。

如果目標是「拿它直接選股或預測股價」，目前價值很低，因為它沒有正式金融資料管線、沒有上市公司估值模型、沒有股價 / 股本 / EV、沒有多期資料、沒有同業分位、沒有回測。

最好的改造方向不是把 Imara 原樣拿來套股票，而是抽出它的工程哲學：

1. 所有財務數字 deterministic 計算。
2. 每個數字保留來源。
3. agent 負責解釋、質疑、串成 thesis。
4. 報告永遠區分「已驗證數字」、「模型假設」、「投資敘事」。

這樣改，才會變成一個可靠的美股 / 台股基本面研究系統，而不是另一個會講漂亮話的股票聊天機器人。
