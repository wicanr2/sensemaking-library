# ROS 2 & Open-RMF 相關專欄｜從商用產品線看機器人開源堆疊的落地邊界

## 原始問題

使用者提供了一組「ROS 2 深度整合至商用產品線」的指標性廠商：Dexory、KION Group、Fetch Robotics，並希望整理到 ROS 2 & Open-RMF 相關專欄。

這篇筆記的重點不是把三家公司都硬歸類為 Open-RMF 使用者，而是先把層次拆開：

```text
ROS 2 / Nav2：單台機器人的感知、定位、導航、控制與節點通訊
Open-RMF：多機器人、多廠牌、多樓層 / 電梯 / 門禁 / 任務調度的互操作協調層
WCS / WMS / MES：倉儲、製造、物流營運系統的業務調度層
```

商用產品通常不會公開完整架構，因此這裡把「公開證據」與「合理推論」分開寫。

## 一句話結論

> Dexory 是目前公開資料中最強的 ROS 2 / Nav2 商用落地案例；KION 顯示大型物流設備商正在把 ROS / ROS 2 能力納入自動化產品線，但公開產品級細節較少；Fetch Robotics 則更像 ROS 商用化的早期代表，公開資料主要指向 ROS 1 / Fetch & Freight 生態，而不是明確的 ROS 2 + DDS 車隊案例。Open-RMF 應放在「跨廠牌協調層」理解，不能把所有 ROS 2 商用 AMR 都直接等同於 Open-RMF 產品。

## 分層模型：不要把 ROS 2 與 Open-RMF 混成同一層

### 1. Robot runtime 層：ROS 2 / Nav2

這層處理單台機器人能不能可靠移動：

- 感測器資料流：LiDAR、camera、IMU、wheel odometry。
- Localization / mapping。
- Path planning / control / obstacle avoidance。
- Lifecycle、action、topic、service。
- Nav2 的 planner、controller、behavior tree、costmap、recovery behavior。

商用 AMR 如果說「採用 ROS 2 / Nav2」，通常是這一層。

### 2. Fleet adapter 層：把機器人接到調度語言

這層把不同機器人的私有 API 轉成共同任務語言：

- dispatch task
- report robot state
- pause / resume / cancel
- battery / charging
- traffic negotiation
- map / lane / waypoint

Open-RMF 的 fleet adapter 就在這一層發揮作用。

### 3. Facility interoperability 層：Open-RMF 的核心價值

Open-RMF 官方定位是「A Common Language for Robot Interoperability」。它最早為 healthcare 場景發展，但官方也說可用於 offices、airports、seaports、shopping malls、factories、distribution centers 等。

這層處理的是：

- 多廠牌機器人不要在走廊互卡。
- 機器人要和電梯、門禁、充電站、建築設備互動。
- 任務不是只派給某台 robot，而是需要跨系統協調。

### 4. Business orchestration 層：WCS / WMS / MES

倉儲物流公司真正賣給客戶的通常不是 ROS 2，而是：

- 盤點準確率。
- 揀貨效率。
- 庫位最佳化。
- 訂單履約速度。
- 與 WMS / WCS / ERP 串接。

因此 ROS 2 / Open-RMF 在商用敘事中常被藏在底層，公開資料不一定會主動揭露。

## 指標廠商整理

| 廠商 | 核心產品 / 應用領域 | 公開 ROS 2 / ROS 證據 | 與 Open-RMF 的關係判斷 | 筆記 |
|---|---|---|---|---|
| Dexory | 高層貨架庫存盤點機器人、倉儲 digital twin / DexoryView | 官方 ROSCon 2024 文表示 ROS 2 是 autonomous mobile robots 與 DexoryView 的核心；ROSCon 2023 談 Nav2 bidirectional navigation；Nav2 / ROS Index 提到 Dexory 是 Nav2 sponsor | 目前比較像 ROS 2 / Nav2 深度商用案例；未看到官方明確說使用 Open-RMF | 最強案例 |
| KION Group | 叉車、無人叉車、廠內物流、自動化倉儲設備 | ROS-Industrial current members 列 KION；Open Robotics Discourse / 招募資料顯示 KION India 尋找 ROS / ROS 2 / C++ robotics 能力 | 公開資料只能支持「ROS 能力進入產品研發組織」，不能直接證明 Open-RMF 產品化 | 中等證據 |
| Fetch Robotics / Zebra | Fetch / Freight AMR、物流與揀貨車隊；後被 Zebra 收購 | GitHub `fetchrobotics/fetch_ros` 是 Fetch Robotics 的 open ROS components；公開 manual 是 Fetch & Freight Research Edition Noetic；repo README 提過未來 ROS 2 support planning / forthcoming | 更像 ROS 商用化早期代表；公開資料不足以支持「ROS 2 DDS 跨 AP 車隊」這個具體說法 | 要謹慎標註 |

## Dexory：最清楚的 ROS 2 / Nav2 商用化案例

### 公開證據

Dexory 官方在 ROSCon 2024 回顧中寫到：

- 團隊分享「deploying and scaling robotics solutions powered by ROS 2」。
- Guillaume Doisy 參與「Successfully deploying ROS 2 into production」panel。
- Dexory 從小型 robotics start-up 擴展到部署近 100 台 autonomous mobile robots。
- 其 production software stack 使用最新版本 ROS 2。
- 官方明確寫：「With ROS 2 at the core of our autonomous mobile robots and the DexoryView platform」。

Dexory 官方 ROSCon 2023 文章則談 Guillaume Doisy 在 ROSCon 分享「Bidirectional Navigation with Nav2」，重點是非圓形、對稱 footprint 的機器人如何支援雙向移動。

Nav2 / ROS Index 頁面也列出 Dexory：

> Dexory develops robotics and AI logistics solutions to drive better business decisions using a digital twin of warehouses to provide inventory insights.

Open Robotics Discourse 的 Nav2 討論還提到，Nav2 MPPI controller 對「大型、高慣性工業車輛」的 low / asymmetric acceleration 支援，是受 Dexory 的大型車輛需求推動。

### 第一性原理解讀

Dexory 的場景不是普通辦公室小 AMR，而是高貨架倉儲盤點：

```text
高貨架 + 密集通道 + 24/7 運轉 + 大型車體 + 高慣性
→ 需要穩定 localization / planning / control
→ 通用 Nav2 必須能處理非圓形 footprint、雙向行駛、低加速度限制
→ 商用需求反過來推動開源 navigation stack 成熟
```

這裡值得注意的是：Dexory 不是只「拿 ROS 2 來用」，而是透過 ROSCon、Nav2 sponsorship、具體 controller 改進需求，進入開源 stack 的共演關係。

## KION Group：大型物流設備商的 ROS 能力吸收

### 公開證據

KION 是全球大型 industrial trucks / warehouse equipment / intralogistics 集團。公開可查到的 ROS 證據主要是：

- ROS-Industrial current members 頁面列出 KION Group，描述其在 100 多個國家提供 factory、warehouse、distribution center 的 material / information flow 解決方案。
- Open Robotics Discourse 上出現 KION India 的 robotics ROS / C++ developer 招募內容；搜尋結果摘要明確提到 KION Group 是 industrial truck 與 intralogistic automation 的大型組織。
- CareersInRobotics 上也有 KION Group Senior Robotics Engineer 職缺，技能列 C++、ROS 2、Python。

### 證據邊界

這些資料支持：

```text
KION 組織內需要 ROS / ROS 2 robotics software 能力
KION 在 ROS-Industrial 生態中可見
KION 的產品場景天然對應 AGV / AMR / forklift autonomy / intralogistics
```

但目前公開資料不足以嚴格證明：

```text
某一款 KION 商用無人叉車 = 以 ROS 2 作為正式 production runtime
某一套 KION fleet system = 已使用 Open-RMF
```

所以這裡要把 KION 放在「大型 incumbent 正在吸收 ROS 2 能力」而不是「已公開 Open-RMF 產品」那一類。

### 第一性原理解讀

KION 這類公司真正的架構挑戰通常是：

```text
既有叉車 / AGV / WCS / 客戶現場安全規範
+ 新一代 autonomous stack
+ 與 WMS / MES / ERP 串接
+ 車隊、路權、充電、維修、SLA
```

ROS 2 可能位於 vehicle autonomy 層，但上層很可能仍由 KION / Dematic 自己的 WCS、fleet manager 或客戶既有系統主導。Open-RMF 若進入，通常會是跨廠牌、多設備互通的 adapter layer，而不是取代 WCS。

## Fetch Robotics / Zebra：ROS 商用化的早期代表，但 ROS 2 證據要保守

### 公開證據

Fetch Robotics 公開資料顯示：

- GitHub `fetchrobotics/fetch_ros` 是「Open ROS Components for Robots from Fetch Robotics」。
- Fetch & Freight Research Edition manual 是 ROS Noetic + Ubuntu 20.04；文件保留作 existing users / historical reference。
- `fetch_ros` README 的 buildfarm development branches 表格曾列 ROS 2 Dashing planning / forthcoming 類描述。
- Zebra 於 2021 年宣布收購 Fetch Robotics，使其 AMR 能力納入 Zebra 的 enterprise asset intelligence / automation portfolio。

### 對使用者原句的修正

使用者提供的說法中提到：

> Fetch Robotics 利用 ROS 2 的 DDS 確保車隊在龐大物流中心內，於 Wi-Fi 跨 AP 切換時的通訊可靠度。

這個描述在概念上合理，因為 ROS 2 的 DDS 確實與 distributed communication / QoS / discovery 有關；但本次公開查證沒有找到 Fetch 官方明確說「商用車隊以 ROS 2 DDS 解決 Wi-Fi roaming」的來源。

因此在筆記中應標註為：

```text
概念相容，但公開證據不足；Fetch 更穩健的定位是 ROS 商用化早期代表，而非已公開 ROS 2 DDS 車隊案例。
```

### 第一性原理解讀

Fetch 的重要性在於它證明過一件事：ROS 不只可以存在於研究機器人，也可以包裝成可銷售、可維護、可部署的 AMR 產品。

但 ROS 1 到 ROS 2 的商用遷移不是自然發生；中間有很多 production concern：

- 網路品質與 fleet scale。
- QoS / discovery / namespace / lifecycle。
- OTA update。
- 客戶現場 support。
- 與 WMS / WCS 的 SLA。
- 安全與停機責任。

Fetch 是「ROS 商用化」的好案例；若要把它列為「ROS 2 深度整合」，還需要更強來源。

## ROS 2 與 Open-RMF 的商用產品化路徑

從這三個案例可以抽象出三種商用化路徑：

### 路徑 A：Robot-first

代表：Dexory。

```text
先做出一台能在真實環境可靠運作的 robot
→ ROS 2 / Nav2 成為 autonomy runtime
→ 商用需求推動 navigation stack 客製化
→ 再包成 data platform / digital twin / warehouse intelligence
```

這條路徑的核心是「robot 本身就是資料採集與執行端」。

### 路徑 B：Fleet / facility-first

代表：Open-RMF 的典型目標場景。

```text
現場已有多廠牌 robot / elevator / door / charger
→ 問題不是單台 robot 會不會走
→ 問題是多系統如何避免互卡、共用路權、完成任務
→ Open-RMF 作為 interoperability layer
```

這條路徑最適合醫院、機場、商場、辦公樓、港口、工廠、distribution center。

### 路徑 C：Incumbent integration

代表：KION / Zebra 類公司。

```text
原本已有叉車、倉儲系統、客戶渠道、維修網路
→ 吸收 ROS / ROS 2 開源能力
→ 但產品包裝仍以 WCS / WMS / fleet platform / service contract 呈現
```

這條路徑下，ROS 2 往往不會是銷售頁主角，而是工程組織與底層軟體架構的一部分。

## 對 Open-RMF 的判斷框架

判斷某商用系統是否「接近 Open-RMF 產品化」，不能只看它是否用 ROS 2。應該看四個訊號：

1. **是否有 fleet adapter / connector**  
   是否公開提到把自家 fleet manager 接到 RMF。

2. **是否處理 facility resources**  
   電梯、門、閘機、充電站、走廊路權。

3. **是否跨廠牌**  
   只調度自家 robot 的 fleet manager，不一定需要 Open-RMF；跨廠牌才更接近 RMF 的價值。

4. **是否把任務抽象成共通語言**  
   例如 delivery、cleaning、patrol、inventory scan，而不是只下發自家 API 的 waypoint。

用這個框架看：

- Dexory：強 ROS 2 / Nav2；Open-RMF 訊號未明確。
- KION：可能有 fleet / WCS 深度；Open-RMF 訊號未明確。
- Fetch：強 ROS 商用歷史；ROS 2 / Open-RMF 訊號需保守。

## 可放進資料庫的筆記

> ROS 2、Nav2 與 Open-RMF 是同一個商用 robotics stack 的不同層。ROS 2 / Nav2 解的是單台機器人能否穩定導航與控制；Open-RMF 解的是多機器人、多廠牌與建築設備之間的互操作；WCS / WMS / MES 解的是業務任務與倉儲營運。Dexory 是公開資料中最清楚的 ROS 2 / Nav2 商用化案例，且商用需求反向推動 Nav2；KION 代表大型物流設備商吸收 ROS / ROS 2 能力；Fetch 代表 ROS 商用化早期路徑，但公開證據不足以直接支持 ROS 2 DDS 車隊說法。不要把「用了 ROS 2」直接等同於「用了 Open-RMF」。

## 仍不確定的地方

1. KION 是否有公開產品明確宣稱 ROS 2 runtime 或 Open-RMF adapter？本次只看到會員 / 招募 / 生態訊號。
2. Fetch / Zebra 的現行 AMR 產品線是否已從 ROS 1 遷移到 ROS 2？公開文件較舊或偏 research edition。
3. Dexory 是否有使用 Open-RMF 或只是 ROS 2 / Nav2 + 自家 DexoryView fleet / data platform？公開頁面目前支持後者，不支持前者。
4. Open-RMF 在 distribution center 的實際 commercial deployment 名單仍需另做來源盤點，尤其要找 adapter repository、case study、或客戶場域證據。

## 來源

- [Open-RMF 官方網站：A Common Language for Robot Interoperability](https://www.open-rmf.org/)
- [Dexory｜Dexory at ROSCon 2024: Scaling Success with ROS 2](https://www.dexory.com/insights/dexory-at-roscon-2024-scaling-success-with-ros-2)
- [Dexory｜ROSCon'23: Bidirectional navigation with Nav2](https://www.dexory.com/insights/roscon23-bidirectional-navigation-with-nav2-guillaume-doisy)
- [ROS Index｜navigation2 repository overview](https://index.ros.org/r/navigation2/)
- [Open Robotics Discourse｜Nav2 Blog: Improving MPPI for High-Inertia Industrial Vehicles](https://discourse.openrobotics.org/t/nav2-blog-improving-mppi-for-high-interia-industrial-vehicles/54403)
- [ROS-Industrial｜Current Members](https://rosindustrial.org/current-members/)
- [Open Robotics Discourse｜Robotics ROS, C++ Developer @ KION India](https://discourse.openrobotics.org/t/robotics-ros-c-developer-kion-india/51497)
- [GitHub｜fetchrobotics/fetch_ros](https://github.com/fetchrobotics/fetch_ros)
- [Fetch & Freight Research Edition Manual](https://fetchrobotics.github.io/docs/)
- [Zebra press release｜Zebra Technologies to Acquire Fetch Robotics](https://www.zebra.com/us/en/about-zebra/newsroom/press-releases/2021/zebra-technologies-to-acquire-fetch-robotics.html)
