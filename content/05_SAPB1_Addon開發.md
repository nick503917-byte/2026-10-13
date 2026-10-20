# SAPB1_Addon開發｜學習、庫存查詢與預估 PK

整理日期：2026-10-13。

## 1. 開發目標與環境

從簡單訊息視窗學習 SAP B1 Add-on，逐步做到欄位事件、DI 查詢、庫存 Grid、銷售訂單整合、可安裝封裝與預估 PK。OA 及跨公司傳送仍是後續方向。

既有筆記記錄的環境：Visual Studio 2019、.NET Framework 4.7.2、x64、SAP B1 10.0（10.00.291，SP 2505 Security HOTFIX1）、SQL Server 2022。練習公司為 WellShin_US_NewTest；這些是開發專案的記錄，不直接推定其他公司環境完全相同。

## 2. 進度與證據

| 階段 | 已做到 | 狀態 |
|---|---|---|
| 第 1 課 | UI API 連線、MessageBox、Hello Nick | 歷史截圖回覆確認成功 |
| 第 2 課 | 自訂視窗、輸入姓名、按鈕與問候文字 | 使用者操作與畫面有成功紀錄；個別異常案例未全數驗收 |
| 第 3 課 | DI API 查料號、品名與品項合計庫存 | 001590 查詢成功，使用者確認庫存比對一致 |
| 第 4 課 | 原生 Grid、銷售訂單明細選列、庫存查詢按鈕 | 已有多版程式及文件；完整驗收未確認 |
| NickStockAddon 1.0.0.2 | 封裝、版本修正、退出事件模擬 | 已編譯與模擬測試；SAP 更新後實機退出結果待確認 |
| 預估 PK 1.0.0.0 | 自訂模組、訂單帶入、棧板列、保存快照與讀回 | 使用者回報第一步測試沒問題；未說明所有功能逐項通過 |
| OA／跨資料庫 | 技術討論與後續方向 | 沒有完成端到端資料傳送證據 |

**本次修正既有筆記的狀態：** 2026-10-06 的學習筆記把預估 PK 寫為待實測；另一聊天「規劃 SAP ADDON 棧板畫面」已有使用者「目前第一步測試沒有問題」的回報。應更新成「初步測試已回報正常、完整驗收待補」，不能仍寫完全沒測，也不能提升為全部驗收完成。

## 3. API 與程式觀念

| 元件／概念 | 學習重點 |
|---|---|
| SAPbouiCOM／UI API | 視窗、按鈕、選單、Grid、畫面事件 |
| SAPbobsCOM／DI API | 業務物件、品項資料與自訂資料操作 |
| GetDICompany | 沿用已登入 SAP Client 的 DI 連線；需再確認公司 |
| FormUID／ItemUID | 辨識自己的視窗及元件，避免其他表單事件誤觸發 |
| BeforeAction／BubbleEvent | 區分前後事件、是否讓 SAP 繼續處理 |
| Application.Run | 維持 UI 訊息迴圈；Console.ReadLine 不是替代方案 |
| COM 物件 | 需正確管理生命週期；共享連線不任意 Disconnect |

UI Add-on 應與 SAP Client 位於同一台機器與 Windows 工作階段。資料庫可以在其他主機；可在開發機編譯後，把對應輸出搬到遠端 SAP Client 的環境。

## 4. 從修改到執行的固定流程

```text
關閉舊練習程式
→ 開啟真正的 .sln／.csproj
→ 修改 Program.cs
→ 儲存並建置
→ 核對成功、版本、輸出時間與目錄
→ 執行該次產生的 EXE
→ 在指定測試公司驗證
```

曾發生修改 ZIP 暫存資料夾裡的 Program.cs，實際卻執行舊 EXE。修改原始碼不會自動更新別處的執行檔；安裝版也要走對應更新流程。

| 檔案 | 用途 |
|---|---|
| .sln／.csproj | 開發與建置入口 |
| Program.cs | 原始碼 |
| EXE | 編譯後主程式 |
| Interop DLL | .NET 與 SAP COM 溝通用，不取代 SAP API 安裝 |
| exe.config | 執行時設定 |
| ARD／ZIP | 延伸部署資訊與套件 |
| PDB | 偵錯資訊，通常非執行必要 |

方案平台顯示名稱不一定就是實際 PlatformTarget，需查看專案屬性；本專案已記錄 x64。

## 5. 庫存查詢

基本流程：從畫面取得完整料號 → DI Items.GetByKey → 讀取品名與數量 → UI 顯示。料號是字串，要保留前導零；查無資料或權限錯誤時不沿用上一次結果。

| 顯示量 | 既有程式屬性 |
|---|---|
| 現有量 | QuantityOnStock |
| 已承諾量 | QuantityOrderedByCustomers |
| 已訂購量 | QuantityOrderedFromVendors |

既有練習計算：可用量＝現有量－已承諾量＋已訂購量。料號 001590 的歷史結果為 0－0＋20＝20 PCS，使用者確認一致；因包含尚未入庫數量，不能直接稱為可立即出貨 20 件。

後續 Grid 以 UI DataTable 建欄、加列、SetValue、綁定顯示；倉庫資料按 WhsInfo 列讀取。需清空上次資料、處理零筆及非庫存品項。

銷售訂單整合的既有識別為表單 139、Matrix 38、料號欄 1；只適用已確認的標準畫面。點列號選列、讀取未儲存畫面料號，以及查詢已儲存庫存，是不同動作。

## 6. NickStockAddon 版本與部署故障

| 版本 | 紀錄 |
|---|---|
| 1.0.0.0 | 正式封裝與隱藏控制視窗 |
| 1.0.0.1 | 以 SAP AppId 區分同工作階段不同 Client；後續回報關閉 SAP 仍殘留 |
| 1.0.0.2 | 改善收到生命週期事件後未結束的行為，已編譯與封裝 |

歷史更新失敗原因之一是舊版 NickStockAddon 程序仍占用 Interop.SAPbobsCOM.dll。處理方向是先確認占用的程式與版本，結束指定的舊 Add-on，再重試更新；不能只看新 ZIP 已產出就認定安裝完成。

### 退出修正的適用界線

舊版 ExitThread 只退出目前執行緒的訊息迴圈；新版本在指定 SAP 生命週期事件中直接 Environment.Exit(0)。模擬測試有四種停止事件退出碼 0、FontChanged 不退出的紀錄。

該做法會略過 finally，原本針對唯讀查詢程式。不能直接套用到寫入交易、保存快照或 OA 佇列而不驗證中斷後果。SAP 崩潰且沒有事件的情況，也不等於已覆蓋。

## 7. 預估 PK 第一版

入口：SAP 模組 → 預估PK。

需求與現有原型：

- PK 號碼在儲存後產生，作為保存資料的識別。
- 選取銷售訂單並帶入 ORDR／RDR1。
- 品料號為 RDR1.ItemCode，數量為 Quantity。
- 棧板暫定固定 P007，箱數與層數可輸入、可增加列。
- 可保存後按 PK 號碼開啟；保存為快照，既有內容不覆寫。

| 資料 | 對應／限制 |
|---|---|
| 訂單鍵／顯示號 | DocEntry／DocNum，不可混用 |
| 客戶 | CardCode、CardName |
| 預計交貨日 | DocDueDate，不是實際出貨日期 |
| 客戶參考與地址 | NumAtCard、Address2 |
| 明細數量 | Quantity，不是剩餘未交 OpenQty |
| 可選訂單 | 原型列最近 100 張未結案品項訂單 |
| 棧板 | P007 為暫定，箱數／層數為正整數 |
| 儲存 | @NK_EPK／U_Snapshot，XML 快照 |

既有程式透過 DI API 建自訂表／欄位及寫入 UserTable。流水號採 MAX＋1 與主鍵限制；同時儲存仍可能撞號，不能因有交易就認定一定不會衝突。

### 下一階段需求

使用者已表示後續要改邏輯及輸出相對應 Excel。尚缺：

- 棧板主檔與可選尺寸／型號。
- 料號分配到棧板的方式，一張訂單或多張訂單的界線。
- 箱容量、箱數、層數、重量、材積的計算規則。
- 採訂購量、未交量或本次出貨量。
- Excel 範本、欄位對應、版面與計算範例。
- 保存後變更、版本、重印或重新匯出的規則。

沒有找到 Excel 匯出功能已完成的證據。

## 8. 待辦與驗收

- [ ] 庫存查詢：空料號、不存在、非庫存、不同倉庫、單位、選錯公司。
- [ ] Lesson04：未選列、切換單據、雙擊／連點、關閉視窗與查詢重入。
- [ ] 1.0.0.2：確認安裝的就是新版，再測 SAP 關閉、切公司、語言變更與異常退出。
- [ ] 預估 PK：逐項記錄選單、帶入、欄位、保存、重開及多使用者取號。
- [ ] 預估 PK：寫入中退出是否回復、是否會產生不完整快照。
- [ ] 取得 Excel 範本及一筆預期結果，再實作匯出。
- [ ] OA／跨公司另訂介面規格，不把本機 UI 查詢成功當成整合成功。

## 9. 現有文件與程式位置

專案內主要產物：

| 目錄／文件 | 內容 |
|---|---|
| outputs/Lesson02、Lesson03、Lesson04 | 課程專案與說明 |
| outputs/NickStockAddon | Program.cs、部署說明、各版 ZIP、1.0.0.2 修正說明 |
| outputs/EstimatedPK | 原型程式、Build.ps1、使用與部署說明、1.0.0.0 ZIP |
| outputs/學習整理/SAPB1_學習與程式筆記.md | 詳細概念與歷史原始碼快照 |
| outputs/學習整理/SAPB1_課題與練習.pdf | 既有練習題本，本次未重新製作 |

主要聊天：「SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉」「確認顧問工程師開發的 SAP B1 Add-on」「規劃 SAP ADDON 棧板畫面」。完整識別資料見來源索引。


## 現有 Obsidian 筆記對照

以下依名稱與原資料夾歸類，作為原文入口；不代表已逐條驗證內容或消除版本差異。

- [20250723_程式開發類型](file:///C:/Users/nick_yeh/Desktop/Obsidian/SAP-B1/20250723_%E7%A8%8B%E5%BC%8F%E9%96%8B%E7%99%BC%E9%A1%9E%E5%9E%8B.md)
- [20250814_B1UP_WorkShop](file:///C:/Users/nick_yeh/Desktop/Obsidian/SAP-B1/20250814_B1UP_WorkShop.md)
- [B1 UP](file:///C:/Users/nick_yeh/Desktop/Obsidian/SAP-B1/B1%20UP.md)
- [[SAPB1技術問題/SAP B1 Object ID|SAP B1 Object ID]]
- [[SAPB1技術問題/SAP Business One Dynamic Syntax|SAP Business One Dynamic Syntax]]
- [[SAPB1技術問題/SAP Business One 變更Form上的欄位說明(Changing Description)|SAP Business One 變更Form上的欄位說明(Changing Description)]]
- [用一個UDT 或是多個UDT來實現，請比較優缺點](file:///C:/Users/nick_yeh/Desktop/Obsidian/SAP-B1/%E7%94%A8%E4%B8%80%E5%80%8BUDT%20%E6%88%96%E6%98%AF%E5%A4%9A%E5%80%8BUDT%E4%BE%86%E5%AF%A6%E7%8F%BE%EF%BC%8C%E8%AB%8B%E6%AF%94%E8%BC%83%E5%84%AA%E7%BC%BA%E9%BB%9E.md)
- [網銀開發](file:///C:/Users/nick_yeh/Desktop/Obsidian/SAP-B1/%E7%B6%B2%E9%8A%80%E9%96%8B%E7%99%BC.md)
- [[SAPB1技術問題/20250723_程式開發類型|20250723_程式開發類型]]
- [[SAPB1技術問題/20250814_B1UP_WorkShop|20250814_B1UP_WorkShop]]
- [[SAPB1技術問題/用一個UDT 或是多個UDT來實現，請比較優缺點|用一個UDT 或是多個UDT來實現，請比較優缺點]]
- [[SAPB1技術問題/網銀開發|網銀開發]]
- [[地端筆記/地端筆記/ABAP/ABAP|ABAP]]
- [[地端筆記/地端筆記/ABAP/Untitled Kanban|Untitled Kanban]]
