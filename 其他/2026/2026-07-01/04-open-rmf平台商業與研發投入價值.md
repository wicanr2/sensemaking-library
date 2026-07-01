# Open-RMF 平台專欄｜從 interoperability 看未來商業與研發投入價值

## 原始問題

使用者希望把前一篇 ROS 2 / Open-RMF 商用產品線觀察中整理到的 Open-RMF 資料再往前推一步：不要只停在「誰用了 ROS 2、誰可能用了 Open-RMF」，而是融合成一篇分析平台未來商業價值與研發投入價值的專欄。

這篇筆記因此把 Open-RMF 當成一種「多機器人、多廠牌、多設施的互操作平台」來分析，而不是把它當成單一機器人的 navigation stack。

## 一句話結論

> Open-RMF 的短期商業價值不在於變成下一個通用 WMS / WCS，而在於成為「機器人與建築設施的互操作 adapter 層」：當現場有多廠牌 AMR、電梯、門禁、充電站、WMS / MES / ERP 時，平台價值會從單台 robot performance 轉向 integration cost、fleet congestion、site commissioning、vendor lock-in 與營運風險的下降。研發投入最值得放在 adapter 生態、traffic / task coordination、現場部署工具、web / API layer、simulation-to-production 驗證，而不是只追求再做一套封閉 fleet manager。

## 先定義問題：Open-RMF 到底解哪一層？

Open-RMF 官方定位是「A Common Language for Robot Interoperability」。它最早為 healthcare 場景發展，但官方也明確說可用於 offices、airports、seaports、shopping malls、factories、distribution centers 等。

這代表它不是單純的「讓某台機器人會走路」：

```text
ROS 2 / Nav2
→ 單台機器人的導航、控制、感測、行為樹

Open-RMF
→ 多機器人、多廠牌、多樓層、多設施資源的協調語言

WMS / WCS / MES / ERP
→ 訂單、庫存、產線、業務流程與營運 KPI
```

所以 Open-RMF 的第一性原理問題不是：

> 這台 robot 能不能更快走到終點？

而是：

> 當一個現場同時存在很多 robot、門、電梯、走廊、充電站、工作站與上層任務系統時，誰負責讓它們不互卡、可替換、可監控、可升級？

這個問題一旦成立，平台價值就會從「機器人硬體性能」轉移到「互操作摩擦成本」。

## 為什麼 Open-RMF 會有平台價值？

### 1. 多廠牌部署會自然增加，而不是減少

倉儲、醫院、商場、機場、工廠很少永遠只買同一家的 robot。原因很簡單：

```text
不同任務
→ 需要不同型態 robot
→ 每一類 robot 的最佳供應商不同
→ 現場逐步導入，採購時間也不同
→ 最後形成多廠牌、多世代、多 API 的機器人現場
```

InOrbit 的 warehouse automation 頁面也把這件事講得很直白：當初始 AMR 部署成功、規模開始擴大，物流公司會尋找更多可自動化任務；不同任務可能需要 goods-to-person、pallet movement、non-conveyables、depalletizing、picking 等不同 robot。它也提到支援 VDA-5050、Open-RMF、MassRobotics AMR Interop，以及 MiR、Omron、OTTO Motors 等 proprietary fleet management software 的整合。

這說明平台價值不是來自「市場突然喜歡開源」，而是來自現場異質性不可避免。

### 2. 真正昂貴的是現場整合，不是 adapter 程式碼本身

Open-RMF 的 adapter 生態已經可以看到幾種訊號：

- `awesome_adapters` 列出 fleet、lift、door 等 adapter 類型。
- Fleet adapter 包含 MiR、MiRFleet、OTTO / Clearpath、Gaussian、LionsBot、QuikSync、temi、TurtleBot 等。
- Lift / door adapter 包含 KONE、Octa Robotics、Dormakaba、QuikSync 等。
- InOrbit 公開 RMF Fleet Adapter，讓 RMF 的 traffic deconfliction、task execution 等能力能透過 InOrbit platform 驅動 robot。
- QuikSync adapter repo 把 fleet、door、lift 三種 adapter 放在同一個 Open-RMF integration surface 裡，透過 HTTP + WSS connector 把 QuikSync 管理的 robots、doors、lifts 暴露給客戶自己的 Open-RMF deployment。

表面上看，這些只是 GitHub repo；第一性原理看，它們其實是在降低以下成本：

```text
每個客戶現場都重寫一次 robot / door / lift integration
→ 工程成本高、測試成本高、交付時間長
→ 現場出問題時難以分責
→ 導致機器人部署規模卡在 PoC / 小規模 roll-out
```

adapter 生態的價值在於把「一次性專案工程」慢慢轉成「可複用的平台接口」。

### 3. Traffic deconfliction 是平台層問題，不是單台 robot 問題

單台 AMR 的 navigation 可以在自己的 map 裡規劃路徑；但多廠牌、多樓層、多通道的現場，問題會變成：

- 兩台不同廠牌 robot 是否會在窄通道互卡？
- 電梯一次只能服務一台或少數幾台 robot，誰先進？
- 清潔 robot、搬運 robot、巡檢 robot 的任務優先級如何協調？
- 臨時障礙、封路、維修、充電需求如何影響全域任務？
- WMS / WCS 下來的任務是否理解現場通行能力？

Open-RMF demos repo 對 RMF 的描述包含 heterogeneous robot fleets、shared resources such as space、lifts、doors，以及 task allocation、conflict resolution、traffic lane de-confliction。這些都是「現場資源協調」問題，不是單一 robot controller 可以單獨解掉的問題。

## 商業價值：Open-RMF 比較像「機器人物聯網的互操作層」

### 價值 A：降低多廠牌導入的 switching cost

如果每個 robot vendor 都只提供自己的 fleet manager，客戶會被迫在兩種壞選擇中取捨：

```text
全買同一家
→ 降低整合成本，但被供應商綁住

買不同家
→ 任務更適配，但整合成本與營運複雜度上升
```

Open-RMF 的平台價值，是提供第三條路：

```text
不同 vendor 保留自己的 robot / fleet 能力
+ Open-RMF 提供共同任務、路權、設施資源語言
→ 客戶能逐步導入，不必一次押注單一供應商
```

這對大型醫院、機場、商場、工廠、物流中心特別重要，因為這些場域的設備更新週期長，且往往已經有既有門禁、電梯、BMS、WMS / WCS / MES。

### 價值 B：讓 robot vendor 更容易進入複雜場域

對 robot vendor 來說，Open-RMF adapter 也可能是商業槓桿。沒有 adapter 時，vendor 要進入大型場域，需要自己回答：

- 我如何和別人的 robot 避讓？
- 我如何用客戶的電梯與門禁？
- 我如何被客戶既有 control room 監控？
- 我如何證明不會破壞現場 SLA？

如果 vendor 可以提供成熟 Open-RMF adapter，銷售話術會從「請相信我們可以客製整合」變成「我們可以接入你既有的 Open-RMF / interoperability layer」。這會降低採購方的 integration risk。

### 價值 C：讓平台商賣「控制平面」而不是只賣 robot

InOrbit 與 QuikSync 的例子很有代表性：

- InOrbit 把 Open-RMF adapter 放進 RobOps / warehouse automation / interoperability 敘事裡。
- QuikSync 的 Open-RMF adapters 把 QuikSync 管理的 fleet、door、lift 透過 Connector 暴露給客戶 Open-RMF deployment。

這暗示一條平台商業路徑：

```text
不是自己做所有 robot
而是做跨 robot、door、lift、facility、dashboard、API 的 control plane
```

這類平台可以賣：

- adapter / connector。
- 現場導入與 commissioning。
- dashboard / monitoring / alerting。
- workflow orchestration。
- SLA、support、upgrade、security。
- 與 WMS / WCS / ERP / BMS 的 enterprise integration。

Open-RMF 是開源底座；真正商業化的部分常在部署、整合、管理、可靠性與責任切割。

## 研發投入價值：哪些方向值得投？

### 1. Adapter 生態：最接近商業化的研發投資

最值得投的不是再做一個漂亮 demo，而是把常見 vendor / facility resource 的 adapter 做到 production-friendly：

- Fleet adapter：MiR、OTTO / Clearpath、Omron、Gaussian、LionsBot、QuikSync、InOrbit、MassRobotics / VDA-5050 bridge。
- Facility adapter：KONE、Dormakaba、Octa Robotics、BMS、charger、workcell。
- Enterprise adapter：WMS / WCS / MES / ERP 任務入口。

研發重點不是只有「能接起來」，而是：

```text
認證、錯誤回復、狀態同步、版本相容、資安、權限、觀測性、現場設定工具
```

這些才是 PoC 到 production 的差距。

### 2. Web / API layer：讓 Open-RMF 從工程系統變成營運系統

`rmf-web` 的定位是提供 web-based interface，用來 visualize and control Open-RMF deployments。它包含 API server、API client、dashboard framework，並且在 demos 中能讓使用者監看 task / robot states、發起 RMF task。

這一層的研發價值在於：

```text
ROS 2 / RMF 工程節點
→ API server / dashboard / role-based workflow
→ 現場營運人員、IT、維修、主管能使用
```

如果沒有這一層，Open-RMF 會停留在 robotics engineer 工具；有這一層，才有機會變成客戶可採購、可維運、可審計的平台。

### 3. Simulation-to-production：把場域導入成本往前移

`rmf_demos` 提供 hotel、office、airport terminal、clinic、campus、manufacturing & logistics 等環境示範。這些 demo 的商業意義不是展示動畫，而是提供場域建模與驗證流程的原型：

```text
先在 simulation / digital twin 裡驗證 traffic、lift、door、task flow
→ 降低現場 commissioning 風險
→ 再逐步接入真實 robot / facility adapter
```

對大型客戶，現場停機成本非常高；能把錯誤提前在 simulation 或 staging deployment 中發現，本身就是商業價值。

### 4. Reliability / observability / safety：真正的 enterprise moat

Open-RMF 若要變成商業平台，最終護城河不會只是「有 adapter」，而是：

- 任務失敗時如何回復。
- adapter 掛掉時如何降級。
- WSS / REST / ROS 2 topic 狀態不一致時如何修復。
- 電梯被人工佔用、門禁失敗、robot 低電量時如何重新規劃。
- 事件如何記錄，誰負責，如何稽核。
- 多租戶、多場域、多權限如何管理。

這些功能看起來不如 robot demo 酷，但更接近採購方願意付錢的地方。

## 投入價值矩陣

| 投入方向 | 商業價值 | 研發難度 | 為什麼值得 / 不值得 |
|---|---|---|---|
| Vendor / facility adapter | 高 | 中到高 | 最靠近導入收入；每多一個可靠 adapter，就降低一類場域整合成本 |
| rmf-web / API / dashboard | 高 | 中 | 把工程系統轉成營運系統，是 enterprise adoption 關鍵 |
| Traffic / task coordination 演算法 | 中到高 | 高 | 場域越複雜越重要，但短期商業化要靠具體痛點拉動 |
| Simulation / digital twin / commissioning tooling | 高 | 中到高 | 可降低現場停機與試錯成本，適合大型場域採購 |
| 再做一套封閉 fleet manager | 不一定 | 中 | 若只管自家 robot，容易變成 vendor lock-in；除非有明確 SaaS / RobOps 差異化 |
| 單純追逐更多 robot 硬體支援 | 中 | 中 | 必要但不充分；沒有 reliability / ops / security，仍然難進 production |

## 與前一篇 ROS 2 / Open-RMF 商用產品線觀察的融合

前一篇筆記的核心是：不要把「使用 ROS 2」直接等同於「使用 Open-RMF」。Dexory、KION、Fetch / Zebra 分別代表不同層次：

- Dexory：最清楚的 ROS 2 / Nav2 商用化案例；重點在 robot runtime 與倉儲 data platform。使用者補充的 Dexory × Neele-Vat 官方影片也提供商用場域訊號：Dexory 描述其 robot 自 2025 年初在 Rotterdam Pernis 倉儲場域運作，覆蓋約 40,000 個 pallet locations，每週自動掃描整倉 3 次，並把資料送進 DexoryView / Dexory 軟體平台形成 inventory visibility。這強化的是「robot runtime → data platform → warehouse operations」的商業路徑，而不是 Open-RMF 採用證據。
- KION：大型物流設備商吸收 ROS / ROS 2 能力；公開資料不足以證明具體 Open-RMF 產品化。
- Fetch / Zebra：ROS 商用化早期代表；ROS 2 / DDS 車隊說法要保守。

這篇則把焦點轉到 Open-RMF 平台本身：

```text
Dexory 類案例證明：ROS 2 / Nav2 可以支撐商用 robot runtime
InOrbit / QuikSync / MiR / OTTO adapter 類案例證明：Open-RMF 的平台價值在跨系統互操作
rmf-web / rmf_demos / free_fleet 類專案證明：Open-RMF 正在補齊 demo、web、adapter、fleet bridge、場域部署工具
```

因此，未來商業判斷可以分成兩條線：

1. **Robot company 的價值**：誰能做出可靠、低成本、可維修、可部署的 robot。
2. **Interoperability platform 的價值**：誰能讓多家 robot 與設施資源在同一現場共同運作。

Open-RMF 比較偏第二條線。

## 風險與限制

### 1. Open-RMF 不一定會成為唯一標準

互操作標準不只 Open-RMF，還有 VDA-5050、MassRobotics AMR Interop，以及各 robot vendor 自己的 API。Open-RMF 的優勢是開源、ROS / Gazebo / Open Robotics 生態、traffic / facility coordination；但它不一定在所有產業、所有地區、所有 vendor 中勝出。

### 2. WMS / WCS / ERP 不會被 Open-RMF 取代

倉儲與工廠客戶真正買的是吞吐量、準確率、SLA、庫存與履約能力。Open-RMF 若商業化成功，也多半是接在 WMS / WCS / MES / ERP 下方，成為 robot / facility execution layer，而不是取代上層業務系統。

### 3. 開源 repo 不等於實際部署規模

GitHub adapter、demo、官方文件只能證明技術路徑與生態方向；不等於已經有大規模付費部署。要判斷商業成熟度，還需要看：

- 客戶 case study。
- SI / integrator 導入案例。
- support contract。
- adapter 更新頻率。
- 現場安全認證。
- 與 enterprise systems 的正式 integration。

### 4. Production deployment 的難點在責任切割

多廠牌互操作會帶來一個現實問題：出事時誰負責？

```text
robot vendor 說是平台指令問題
platform 說是 robot API / firmware 問題
facility vendor 說是門禁 / 電梯策略問題
客戶只看到任務失敗與營運中斷
```

因此 Open-RMF 商業化若要成熟，除了技術 adapter，還需要事件追蹤、責任邊界、測試規範與 support 流程。

## 可放進資料庫的筆記

> Open-RMF 的平台價值不是讓單台 robot 更會走，而是降低多廠牌 robot、門、電梯、充電站、WMS / WCS / MES 之間的互操作摩擦。當機器人部署從單一 PoC 走向多任務、多場域、多 vendor，價值會從 robot runtime 轉到 integration cost、traffic deconfliction、site commissioning、observability、SLA 與 vendor lock-in 的下降。未來研發投入最值得放在 adapter 生態、web / API control plane、simulation-to-production、reliability / observability / safety，而不是只做封閉 fleet manager。Open-RMF 不必取代 WMS / WCS；它更像 robot / facility execution interoperability layer。

## 仍不確定的地方

1. Open-RMF 在真實 commercial deployment 的公開 case study 仍偏少；目前較多證據來自官方 repo、adapter repo、demo、平台商整合宣告。
2. VDA-5050、MassRobotics AMR Interop 與 Open-RMF 在不同產業、地區、vendor 的競合關係仍需追蹤。
3. QuikSync adapter repo 顯示完整 fleet / door / lift connector 設計，但公開 GitHub stars / forks 很低；它比較像一個早期但結構清楚的工程訊號，不宜過度解讀為大規模市場採用。
4. InOrbit 已明確把 Open-RMF 放進 interoperability / RobOps 敘事，但實際客戶採用比例與營收貢獻未公開。
5. Open-RMF 是否能從工程社群工具跨到 enterprise procurement，關鍵仍在 support、certification、security、observability 與 SI 生態。

## 來源

- [Open-RMF 官方網站：A Common Language for Robot Interoperability](https://www.open-rmf.org/)
- [GitHub｜open-rmf/rmf_demos](https://github.com/open-rmf/rmf_demos)
- [GitHub｜open-rmf/rmf-web](https://github.com/open-rmf/rmf-web)
- [GitHub｜open-rmf/free_fleet](https://github.com/open-rmf/free_fleet)
- [GitHub｜open-rmf/awesome_adapters](https://github.com/open-rmf/awesome_adapters)
- [GitHub｜open-rmf/fleet_adapter_mir](https://github.com/open-rmf/fleet_adapter_mir)
- [GitHub｜open-rmf/fleet_adapter_clearpath](https://github.com/open-rmf/fleet_adapter_clearpath)
- [InOrbit｜Open source InOrbit RMF Fleet Adapter now available](https://www.inorbit.ai/blog/inorbit-rmf-adapter)
- [InOrbit｜Warehouse Automation](https://www.inorbit.ai/warehouseautomation)
- [YouTube｜Dexory Robot x Neele Vat | Autonomous Robots Transform Inventory Management](https://www.youtube.com/watch?v=jgQ51fDQwRI)
- [GitHub｜quikbot/open-rmf-adapters-quiksync](https://github.com/quikbot/open-rmf-adapters-quiksync)
- [前篇筆記｜ROS 2 & Open-RMF：從商用產品線看機器人開源堆疊的落地邊界](./03-ros2-open-rmf商用產品線觀察.md)
