# Business Forensics AI / Imara：公開 repo 是否已經「leak」的專案稽核筆記

## 原始資訊

- 專案：[`bouwerruan10-boop/business-forensics-ai`](https://github.com/bouwerruan10-boop/business-forensics-ai)
- 本機副本：`~/tmp2/business-forensics-ai`
- 本次檢查日期：2026-06-29
- 遠端狀態：GitHub repo 為 **public**，預設分支 `main`。
- 最新遠端 commit：`e38239d7ad31f9f54bac6a4fc6a5ea8f524f93d9`，訊息為 `feat(verify): the 'prove it' contract now travels onto exported PDF + HTML`。
- 注意：本筆記只記錄「是否外洩」的判斷與風險結構；不收錄任何實際 secret 值。

## 一句話結論

如果「leak」指的是 **API key / password / token 等可直接濫用的秘密值**，這次掃描沒有看到明確外洩；但如果「leak」指的是 **私有商業產品的完整原始碼、商業計畫、稽核報告、部署線索與 go-to-market 材料公開在 GitHub**，那答案是：**已經是高風險公開曝露**。

最關鍵的矛盾是：`README.md` 開頭寫著「CONFIDENTIAL & PROPRIETARY — NOT OPEN SOURCE」與「trade-secret material」，但 GitHub repo metadata 顯示 `private=false` / `visibility=public`。這不是傳統 secret leak，而是 **repository exposure / source disclosure**。

## 我實際檢查了什麼

### 1. GitHub metadata

透過 `gh repo view` 與 GitHub API 讀回：

- `nameWithOwner`: `bouwerruan10-boop/business-forensics-ai`
- `isPrivate`: `false`
- `visibility`: `public`
- `defaultBranch`: `main`
- repo 建立時間：2026-06-18
- 最新更新：2026-06-28
- 語言面：主要為 Python、JavaScript，另有 Batchfile、PowerShell、HTML、CSS、Dockerfile。

### 2. 本機 repo 結構

本機 `~/tmp2/business-forensics-ai` 是一個正常 Git repo，remote 指向：

```text
https://github.com/bouwerruan10-boop/business-forensics-ai.git
```

排除 `.git`、`node_modules`、venv、cache、build 後，工作樹約：

- 358 個 tracked files
- 約 3.4 MB 非排除內容
- 主要目錄：`backend/`, `frontend/`, `docs/`, `legal/`, `scripts/`, `.github/`

### 3. Secret / sensitive filename 掃描

檢查 tracked 檔名後，與 secret / credential / data 相關的顯著項目：

- `backend/.env.example`
- `backend/.env - Copy.example`
- `TEST_DATA.csv`
- `backend/data/benchmarks.json`
- `docs/gtm/IMARA_Accountant_Shortlist.csv`
- `docs/reports/Imara_Investor_Report_Accessible.html`
- `docs/reports/assets/_ir_preview_p1.png`
- `docs/reports/assets/_ir_preview_p6.png`

沒有看到真正的 `.env`、`.db`、`.sqlite`、`.pdf`、`.xlsx` 被 tracked；本機工作樹也沒有找到未追蹤的 `.env` / DB / PDF / Excel 殘留。

### 4. Secret regex 掃描結果

我用多組模式掃描目前 tracked text files 與 Git history unique blobs，包括：

- Anthropic key 形狀：`sk-ant-...`
- OpenAI key 形狀：`sk-...` / `sk-proj-...`
- GitHub PAT：`ghp_...` 等
- AWS Access Key：`AKIA...`
- Private key block
- JWT-like token
- generic `api_key=`, `secret=`, `token=`, `password=` assignment

結果：

- 目前工作樹：5 個候選命中。
- Git history：15 個候選命中。

人工檢視後，這些命中看起來主要是 **false positive**：

1. `backend/agents/base_agent.py` 的 `api_key=` 是把 `ANTHROPIC_API_KEY` 變數傳入 SDK，不是硬編碼 key。
2. 幾個 markdown 文件中的 `sk-...` 命中，其實是英文句子或破折號造成的形狀誤判，例如 `sk--...`、`sk-by-...`、`sk-Dat...` 這類片段，不像有效 API key。
3. `.env.example` 內的 key 欄位是 placeholder，值已用 `***` / 空值形式處理。

所以：**我沒有看到可立即拿去使用的 API key / token / private key 外洩。**

## 真正的 leak 是哪一種？

這個 repo 比較像三層曝露：

### 第一層：不是 secret leak，而是 source / IP leak

`README.md` 自稱：

> CONFIDENTIAL & PROPRIETARY — NOT OPEN SOURCE.

同時 repo 是 public。這代表外部人可以直接取得：

- backend FastAPI 架構
- multi-agent pipeline 設計
- scoring / verification / report export 邏輯
- frontend UI 與資料呈現模式
- security audit / hardening plan
- product roadmap
- GTM 名單與 sales/research material
- legal draft / trademark risk / NDA / terms / privacy draft

這些不一定是「憑證」，但很可能是創業專案最有價值的 know-how。

### 第二層：部署與攻擊面線索公開

`docs/`、`scripts/`、`frontend` 與 `backend/main.py` 裡透露了足夠多的運維資訊，例如：

- Vercel frontend domain pattern：`business-forensics-ai.vercel.app` 與 preview deploy pattern。
- Railway backend URL 在 live e2e script 中出現。
- API route 結構：`/api/analyze`, `/api/status/{id}`, `/api/report/{id}`, `/api/shared/{token}`, `/api/admin/*`, `/api/v1/*` 等。
- auth model：operator password → bearer token；optional `API_SECRET_KEY` / `ADMIN_API_KEY`。
- rate limit、upload size、file type allowlist、CORS policy、health endpoint、share-token behavior。

這些不是「直接失竊」，但會讓攻擊者不用猜系統長什麼樣子。安全上通常叫 **reconnaissance advantage**：攻擊者可以從 source code 直接建立攻擊地圖。

### 第三層：商業與法律策略公開

`docs/gtm/IMARA_Accountant_Shortlist.csv` 看起來是 go-to-market shortlist；`legal/` 內有 NDA、operator agreement、terms、privacy、trademark risk 等草案；`docs/audits/` 有安全稽核結論與 remediation history。

這些資料不一定含個資或 secret，但會暴露：

- 目標客群與銷售路線。
- 產品尚未補強或曾經補強的安全問題。
- 法律風險與品牌命名風險。
- 產品定位：South African SMEs、funding / lender decision-support、POPIA / NCA governance。

所以這一層是 **business confidentiality leak**。

## 專案設計拆解：它到底在做什麼？

### 表面產品

Imara 是給南非 SME 的 AI business intelligence / funding-readiness 工具。使用者上傳財務、銀行、稅務、法律、HR、business plan 等文件後，backend 會跑多個 Claude specialist agents，再輸出 dashboard、PDF / HTML report、score、roadmap、credit memo 等。

### 內部模型

這個專案不是單純「把文件丟給 LLM 摘要」，而是明顯想做一個 deterministic-first 的 WAT system：

- **Workflow**：CEO pipeline 定義 phase、agent 順序與報告組裝。
- **Agents**：Claude agents 負責解釋、排序、敘事，不應該自己發明數字。
- **Tools**：financial ratios、distress score、tax optimizer、simulation、faithfulness verifier 等 deterministic services 負責計算。

這個設計方向是合理的：金融 / credit / lender-facing 場景裡，LLM 最危險的是「講得像真的但數字不受約束」。把數字、score、ratio、threshold 放在 deterministic services，LLM 只做 narrate / prioritize，能降低 hallucination 風險。

### 核心價值主張

它真正想賣的不是「AI 顧問報告」，而是：

> 把 SME 原本混亂、不可貸、不可投資的資料，整理成 lender / investor 可讀、可驗證、可追責的 evidence package。

所以後期 commit 訊息才會強調 `prove it contract`、verification evidence、PDF / HTML export。這是對的：如果最終買單者是 lender / accountant / government programme，可信度不是 UI 美觀，而是「每個數字能不能被追溯、每個結論能不能被 challenge」。

## 安全與外洩判斷

### 目前沒有看到的東西

我沒有看到以下明確外洩：

- 有效 Anthropic / OpenAI / GitHub / AWS key。
- private key block。
- 真實 `.env` 檔。
- SQLite production DB。
- 客戶上傳原始文件。
- 明顯的真實財報 / 銀行流水 / 稅務文件。

`TEST_DATA.csv` 看起來像示範月營收 / 成本 / 現金資料；`docs/gtm/IMARA_Accountant_Shortlist.csv` 是公開可查的公司名單整理，敏感度偏商業策略而非個資密碼。

### 仍然應該當成 leak 的東西

1. **完整原始碼公開**：如果作者本意是 proprietary，那 public repo 本身就是 leak。
2. **安全稽核文件公開**：即使漏洞已補，攻擊者仍能知道歷史弱點、架構假設與 auth 邊界。
3. **部署端點公開**：live e2e script 暴露 production API base，方便外部探測。
4. **GTM / legal / roadmap 公開**：對 competitor / opportunistic attacker 都有價值。
5. **license / visibility 矛盾**：repo public 但 README 說非 open source，會造成法律與治理混亂；公開不等於授權，但實務上已經降低保密主張強度。

## 風險矩陣

| 風險 | 等級 | 理由 |
|---|---:|---|
| API key / password 直接外洩 | 低 | 掃描未見有效 secret；`.env` 與 DB 沒有 tracked。 |
| 原始碼 / trade secret 外洩 | 高 | repo public，且 README 自稱 confidential / proprietary。 |
| 攻擊面 reconnaissance | 中高 | route、auth、部署、rate limit、share-token、admin 設計都可讀。 |
| 客戶資料外洩 | 低到未知 | repo 內未見 production DB / uploaded docs；但若 live endpoint 有 public share 或 auth 關閉，需另做線上驗證。 |
| 商業策略外洩 | 中高 | GTM shortlist、legal drafts、roadmap、research/audit docs 皆公開。 |
| 法律 / IP 主張受損 | 中 | public GitHub 與 proprietary 聲明並存，容易造成後續執行困難。 |

## 第一性原理拆解

### 問題的本質不是「有沒有一把 key」

很多人談 leak 只找 `.env`。但在 AI SaaS / fintech-ish 工具裡，真正值錢的常常不是 key，而是：

- agent workflow 怎麼拆；
- deterministic verification 怎麼接；
- scoring / model card / governance 怎麼包裝；
- 市場定位與客戶路線；
- 安全稽核與補強路徑。

也就是說，**secret leak 是 credential compromise；public proprietary repo 是 capability / strategy compromise。** 前者是「有人能不能登入你的帳號」，後者是「別人能不能複製你的產品邏輯與攻擊你的系統」。

### 這個 repo 的訊號是「把內部工作台當成公開展示櫃」

它看起來像一個由 AI coding agent 高速迭代的 repo：文件很多、稽核很多、handoff 很完整、commit message 很長。這對協作非常好，但如果 repo 是 public，就會出現反效果：

- 對自己：每個設計決策都可追溯。
- 對外人：每個設計決策也都可觀察。

這就是 AI-agent era 的新風險：代理人很擅長留下完整脈絡，但完整脈絡若放錯邊界，就等於把內部會議室直播出去。

## 建議處置順序

### P0：立刻決定 repo 邊界

如果這不是刻意 open-source demo，應立刻：

1. 將 GitHub repo 改成 private。
2. 檢查是否已有 forks / clones / GitHub code search 快取；若有，視為已外流，不要假設改 private 就消失。
3. 停止把 `docs/audits`, `docs/gtm`, `legal`, `HANDOFF.md`, `SESSION_STATE.md` 這類內部材料放在 public repo。
4. 分離 public demo repo 與 private product repo。

### P1：做正式 secret scan 與輪替判斷

雖然這次未見有效 secret，仍建議：

1. 用 `gitleaks` / GitHub secret scanning 掃全歷史。
2. 檢查 GitHub Security → Secret scanning alerts。
3. 若任何 key 曾經進入 public history，直接 rotate，不要只刪檔。
4. 把 `.gitleaks.toml` 放進 CI gate，push 前阻擋。

### P2：重整公開 / 私有資料分層

建議至少拆成三層：

- **Public marketing repo**：README、demo screenshots、high-level architecture、license 清楚的 public artifacts。
- **Private app repo**：backend/frontend/source/tests/deploy scripts。
- **Private operations vault**：GTM 名單、legal drafts、audit reports、handoff、session state、operator runbooks、production URLs。

### P3：線上面做一次黑箱驗證

本次主要是 repo / source audit，還沒有對 production endpoint 做 destructive 或付費操作。下一步可以只做安全的 GET / unauth 測試：

- `/api/health` 是否顯示 `auth_required=true`。
- `/api/admin/*` 未帶 token 是否 401。
- `/api/report/{random_uuid}` 是否 401 / 404，而不是 detail leak。
- `/api/shared/{random}` 是否只有 410 / 404。
- frontend JS bundle 是否含 `VITE_API_KEY` 或其他敏感 env。

不要在未授權下跑 `/api/analyze`，因為它可能花費 Anthropic / SERPER credits。

## 可抽象成的思考模型

### 「三種 leak」模型

1. **Credential leak**：key / token / password 外洩，後果是帳號或資源被濫用。
2. **Data leak**：客戶資料、DB、文件、報表外洩，後果是隱私、法規與信任損害。
3. **Capability leak**：原始碼、roadmap、prompt、agent workflow、security audit、GTM strategy 外洩，後果是競爭與攻擊面暴露。

這個 repo 目前不像第一種，暫時也沒看到第二種；最明顯是第三種。

### 「公開不等於開源，但公開會破壞保密」模型

GitHub public repo 可以同時寫著 proprietary license；法律上外人未必有使用權。但保密性的 practical value 已經受損，因為：

- 內容可被 clone。
- 搜尋引擎與 code search 可能索引。
- forks / caches / mirrors 可能存在。
- 攻擊者不需要 license 才能閱讀與學習。

所以要問的不是「授權了嗎」，而是「還能主張這是秘密嗎」。

## 放進資料庫的筆記

- `business-forensics-ai` public repo 沒看到明確有效 API key / token / private key 外洩，但 repo 本身公開了 proprietary source、security audits、GTM、legal drafts 與部署線索；應視為 source / business confidentiality leak。
- 對 AI-agent 快速迭代專案，`CLAUDE.md`, `HANDOFF.md`, `SESSION_STATE.md`, `docs/audits/`, `docs/gtm/`, `legal/` 往往比程式碼更像內部會議紀錄；public repo 要特別排除這些。
- Secret scanning 只能回答「鑰匙是否外洩」，不能回答「能力、策略與攻擊地圖是否外洩」。

## 仍不確定的地方

- 我沒有查 GitHub forks / clones / external mirrors，因此不能判斷外流範圍。
- 我沒有對 production API 做 live black-box 探測，也沒有使用任何付費 / side-effect endpoint。
- `AUTH_ENABLED`、`ADMIN_API_KEY`、`API_SECRET_KEY` 等 production env 是否正確設定，需要看 Railway / Vercel dashboard 或用只讀 endpoint 安全驗證。
- 若 repo 過去曾短暫 commit 真實 `.env` 後又刪除，這次 regex 未命中不代表 100% 不存在；正式流程仍應跑 gitleaks / GitHub Advanced Security secret scanning。
