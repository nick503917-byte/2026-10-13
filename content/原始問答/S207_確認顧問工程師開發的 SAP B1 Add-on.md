# 確認顧問工程師開發的 SAP B1 Add-on

來源編號：S207  
聊天 ID：`01a0ebec-2fb7-7d41-8980-8ebfc1336d2c`  
聊天起始日期（台北）：2026-09-29  
保存位置：一般本機紀錄  
問答段落：18

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S207-Q001｜這個是顧問工程師開發的 可以掛在sapb1的addon裡面

日期：2026-09-29

**你的提問**

# Files mentioned by the user:

## Add-On.b1s: C:/Users/nick_yeh/AppData/Local/Temp/Add-On.b1s

## Add-On.exe: C:/Users/nick_yeh/AppData/Local/Temp/Add-On.exe

## Add-On.exe.config: C:/Users/nick_yeh/AppData/Local/Temp/Add-On.exe.config

## Add-On.pdb: C:/Users/nick_yeh/AppData/Local/Temp/Add-On.pdb

## CrystalDecisions.CrystalReports.Engine.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.CrystalReports.Engine.dll

## CrystalDecisions.ReportAppServer.ClientDoc.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.ClientDoc.dll

## CrystalDecisions.ReportAppServer.CommLayer.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.CommLayer.dll

## CrystalDecisions.ReportAppServer.CommonControls.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.CommonControls.dll

## CrystalDecisions.ReportAppServer.CommonObjectModel.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.CommonObjectModel.dll

## CrystalDecisions.ReportAppServer.Controllers.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.Controllers.dll

## CrystalDecisions.ReportAppServer.CubeDefModel.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.CubeDefModel.dll

## CrystalDecisions.ReportAppServer.DataDefModel.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.DataDefModel.dll

## CrystalDecisions.ReportAppServer.DataSetConversion.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.DataSetConversion.dll

## CrystalDecisions.ReportAppServer.ObjectFactory.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.ObjectFactory.dll

## CrystalDecisions.ReportAppServer.Prompting.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.Prompting.dll

## CrystalDecisions.ReportAppServer.ReportDefModel.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.ReportDefModel.dll

## CrystalDecisions.ReportAppServer.XmlSerialize.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportAppServer.XmlSerialize.dll

## CrystalDecisions.ReportSource.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.ReportSource.dll

## CrystalDecisions.Shared.dll: C:/Users/nick_yeh/AppData/Local/Temp/CrystalDecisions.Shared.dll

## EInvoice.dll: C:/Users/nick_yeh/AppData/Local/Temp/EInvoice.dll

## EInvoice.pdb: C:/Users/nick_yeh/AppData/Local/Temp/EInvoice.pdb

## itextsharp.dll: C:/Users/nick_yeh/AppData/Local/Temp/itextsharp.dll

## JetwellLibrary.dll: C:/Users/nick_yeh/AppData/Local/Temp/JetwellLibrary.dll

## JetwellLibrary.pdb: C:/Users/nick_yeh/AppData/Local/Temp/JetwellLibrary.pdb

## Newtonsoft.Json.dll: C:/Users/nick_yeh/AppData/Local/Temp/Newtonsoft.Json.dll

## RestSharp.dll: C:/Users/nick_yeh/AppData/Local/Temp/RestSharp.dll

## SAPBusinessOneSDK.dll: C:/Users/nick_yeh/AppData/Local/Temp/SAPBusinessOneSDK.dll

## stdole.dll: C:/Users/nick_yeh/AppData/Local/Temp/stdole.dll

## Add-On (1).exe: C:/Users/nick_yeh/AppData/Local/Temp/Add-On (1).exe

## Jetwell GUI.ard: C:/Users/nick_yeh/AppData/Local/Temp/Jetwell GUI.ard

## codex-clipboard-f2444d07-4704-4abf-85c9-d8d682bd9667.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f2444d07-4704-4abf-85c9-d8d682bd9667.png
Image attachment: true

## codex-clipboard-7533d02c-e186-4b18-805f-c6eeabac74bf.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7533d02c-e186-4b18-805f-c6eeabac74bf.png
Image attachment: true



## My request:
這個是顧問工程師開發的 可以掛在sapb1的addon裡面

[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**當時回答**

了解，這是顧問工程師開發、可整合到 SAP Business One 的 Add-On。你提供的是程式與相關元件，截圖則是 **SAP Business One 延伸管理員（Extension Manager）**。

從檔名可以先辨識：

| 檔案 | 初步用途 |
|---|---|
| `Add-On.exe`、`.exe.config` | 主程式與設定 |
| `Jetwell GUI.ard` | Add-On 註冊描述檔，實際部署方式需看內容 |
| `SAPBusinessOneSDK.dll` | SAP Business One SDK 相關元件 |
| `EInvoice.dll`、`JetwellLibrary.dll` | 可能是電子發票與顧問自訂共用功能 |
| `CrystalDecisions.*`、`itextsharp.dll` | 報表與 PDF 相關元件 |
| `.pdb` | 偵錯符號，不是完整原始碼 |

**這組檔案可以作為我們後續開發 SAP B1 Add-On 的參考。** 但目前還沒檢查檔案內容，不能僅憑檔名確認它的功能、相容版本，或能否直接從畫面的「匯入」安裝。

你想先了解這套程式的功能與結構，還是要以它為參考，開發你自己的 Add-On？

---

<a id="q2"></a>

## S207-Q002｜對 你可以了解一下 因為我們目前的寫法 是只能在同一台主機 但顧問公司給的掛在裡面 只要啟用就會多一個模組的那種感覺

日期：2026-09-29

**你的提問**

對 你可以了解一下 因為我們目前的寫法 是只能在同一台主機 但顧問公司給的掛在裡面 只要啟用就會多一個模組的那種感覺

**當時回答**

我檢查過顧問的註冊檔，以及我們第 2、3 課的程式了。**我們目前的技術方向可以做成你說的「啟用後，SAP 裡就多一個模組」；主要還差選單整合、常駐運作與部署包裝。**

有一個關鍵要釐清：**顧問這種 Add-On，也是在執行 SAP B1 用戶端的電腦上運作。** 透過 SAP 管理部署與啟動，使用者就不需要自己找 EXE 執行。如果 SAP 是透過遠端桌面使用，程式實際上就在遠端主機的使用者工作階段執行。

你感受到的差異，大致是：

| 項目 | 我們目前的練習程式 | 顧問這類正式 Add-On |
|---|---|---|
| 啟動方式 | 手動執行 EXE | 由 SAP 啟動，可設定自動啟動 |
| 操作入口 | 啟動就直接開練習視窗 | SAP 裡有固定功能選單 |
| 關閉功能視窗 | 整支程式結束 | 程式繼續運作，可從選單再開 |
| 提供其他使用者使用 | 尚未整理部署包 | 經延伸管理員部署、指派公司 |

SAP 官方文件也確認：延伸指派給公司後，用戶端會依公司與使用者設定載入，並可設定自動啟動。[SAP 啟動與部署說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/0fddbddcb67c459984dd994275745197/fc1e8405bb4340de9321c7b225a2cd2c.html)

從顧問檔案中，我確認到：

- `Jetwell GUI.ard` 登記名稱為 **Jetwell GUI**、版本為 `0.33.12.66`。
- 指定啟動程式為 `Add-On.exe`，並列有 `X86Client`、`X64Client` 部署路徑與相依檔案。
- 主程式裡可以找到 `SAPbouiCOM`、`SAPbobsCOM`、`SAPbouiCOM.Framework`、選單建立相關符號。
- 設定使用 **.NET Framework 4.7.2**，與我們目前專案相同。

所以，這些檔案支持它採用 **SAP SDK 用戶端 Add-On，加上註冊部署機制** 的判斷；實際功能還不能只靠符號完整確認。

更重要的是，我們的 [第 3 課程式](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/Lesson03/Program.cs:31) 已經有這段：

```csharp
gui.Connect(args.Length > 0 ? args[0] : DevelopmentConnection);
```

**它已經預留接收 SAP 啟動參數的能力。** SAP 正式啟動 Add-On 時，就是把連線字串透過命令列參數交給程式。[SAP SDK 說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbouiCOM~SboGuiApi.html)

接下來適合把第 3 課整理成這種操作方式：

```text
登入 SAP B1
  → SAP 自動啟動我們的 Add-On
  → 選單出現「Nick 自訂功能」
      → 點「庫存查詢」才開啟視窗
      → 關閉視窗後，仍可從選單再次開啟
```

再製作自己的部署包，交由延伸管理員指派給測試公司。**這樣就能朝顧問那種使用體驗前進，現有的庫存查詢功能可以沿用。** 目前完成的是檔案與程式比對，尚未修改或安裝任何 Add-On。

---

<a id="q3"></a>

## S207-Q003｜了解 如果要在SAPB1 的原生單據多生一個按鈕 也可以嗎

日期：2026-09-29

**你的提問**

了解 如果要在SAPB1 的原生單據多生一個按鈕 也可以嗎

**當時回答**

**可以，能直接在 SAP B1 原生單據上增加自訂按鈕。** 我們目前使用的 Windows 用戶端 UI API 就能做到；SAP 官方也有「在原生銷售訂單加按鈕」的示範。[官方範例](https://help.sap.com/docs/SAP_BUSINESS_ONE/30bc77ba793647418a413851d474c4f2/c171ef467d624a2c93b5756d23534a27.html)

例如：

| 原生畫面 | 新增按鈕 | 點擊後的功能 |
|---|---|---|
| 銷售訂單 | 查詢庫存 | 查詢目前選取那一列料號的各倉庫庫存 |
| 採購訂單 | 歷史進價 | 顯示該料號過去的採購價格 |
| 應收發票 | 電子發票 | 執行自訂電子發票流程 |
| 交貨單 | 列印標籤 | 依目前單據資料產生標籤 |

運作流程是：

1. Add-On 隨 SAP 啟動，監聽單據開啟事件。
2. 開啟指定單據時，程式在原生畫面加入按鈕。
3. 點擊按鈕後，取得目前單據或選取列的資料，執行我們寫的功能。

**按鈕是 Add-On 在執行期間加上去的。** 停用 Add-On 後，再重新開啟單據，就不會加入這個按鈕；它不是永久修改 SAP 原生畫面。[SAP UI API 說明](https://help.sap.com/doc/saphelpiis_hc_b1_image_repository_development_training_basic_b1_90_tb1300_03.pdf)

以我們目前的進度，最適合的下一個練習就是：**在「銷售訂單」加一顆「查詢庫存」，直接帶入選取列的料號，沿用第 3 課的查詢功能。** 這樣就不必另外手動輸入料號了。

---

<a id="q4"></a>

## S207-Q004｜可以先寫一版在銷售訂單查詢庫存查詢目前選取那一列料號的各倉庫庫存 給我玩玩看嗎

日期：2026-09-29

**你的提問**

可以先寫一版在銷售訂單查詢庫存查詢目前選取那一列料號的各倉庫庫存   給我玩玩看嗎

**當時回答**

已做好第一版，**編譯成功，尚未在 SAP 裡實際試跑**。

[下載試玩包 ZIP](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/Lesson04/SAPB1Lesson04-試玩包.zip) · [操作說明](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/Lesson04/操作說明.md)

操作方式：

1. 先關閉第 2、3 課練習，SAP 登入 **WellShin_US_NewTest**。
2. 解壓縮後，在 SAP 所在的電腦與 Windows 工作階段執行 `bin\SAPB1Lesson04.exe`。
3. 開啟「銷售訂單」，**點明細左側列號，選取一列**。
4. 按明細表格上方靠右的 **「查詢庫存」**。

結果會顯示該料號各倉庫的名稱、現有量、已承諾量、已訂購量、可用量與合計。**只查詢，不會修改單據。**

控制視窗可以最小化；關閉它就會停止程式並隱藏按鈕。若按鈕位置重疊或出現錯誤，把畫面貼給我，我再依你的 SAP 版面調整。

---

<a id="q5"></a>

## S207-Q005｜圖片／附件提問

日期：2026-09-29

**你的提問**

# Files mentioned by the user:

## codex-clipboard-f76a51b4-b693-4ec2-a7f7-ada2f929357e.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f76a51b4-b693-4ec2-a7f7-ada2f929357e.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

按鈕已成功加上，但截圖中蓋到了「彙總類型」欄位。我已將它上移至頁籤右側，重新編譯完成，位置仍待實機確認。

[下載 v2 試玩包](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/Lesson04/SAPB1Lesson04-v2-試玩包.zip)

1. 關閉舊版「第 4 課」控制視窗。
2. 解壓新版，執行裡面的 `bin\SAPB1Lesson04.exe`。
3. 在訂單填入料號，**點左側列號選取該列**，再按「查詢庫存」。

目前截圖的料號是空白，需先輸入品項才能查詢。

---

<a id="q6"></a>

## S207-Q006｜為甚麼不是顯示SAPB1的畫面

日期：2026-09-29

**你的提問**

為甚麼不是顯示SAPB1的畫面

**當時回答**

因為我這一版把「查詢結果」做成了 **Windows 視窗（WinForms）**，所以看起來不是 SAP B1 原生畫面。只有單據上的「查詢庫存」按鈕是透過 SAP UI API 加入的。

這是我實作時的選擇，並不是 SAP 做不到；跟你希望整合在 SAP 裡的體驗有落差。

可以改成 **SAP B1 原生的查詢視窗與表格**，讓各倉庫庫存直接在 SAP 內顯示，外觀與操作也一致。原本讀取庫存的邏輯可以沿用，只需要替換結果畫面。

---

<a id="q7"></a>

## S207-Q007｜好像跑不動

日期：2026-09-29

**你的提問**

好像跑不動

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q8"></a>

## S207-Q008｜圖片／附件提問

日期：2026-09-29

**你的提問**

# Files mentioned by the user:

## codex-clipboard-cf6def1b-92c0-4008-bb2b-18dfeae3c041.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cf6def1b-92c0-4008-bb2b-18dfeae3c041.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

看到了，是結果視窗整片空白並顯示「沒有回應」，不是你操作錯誤。

我已做出 **v3 修正版**：
- 改用 **SAP B1 原生視窗與表格**。
- 調整查詢時機，避免直接在 SAP 按鈕事件中開啟結果。
- 加入錯誤紀錄。

[下載 v3 修正版](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/Lesson04/SAPB1Lesson04-v3-原生視窗.zip)

先關閉舊版；如果關不掉，在工作管理員**只結束 `SAPB1Lesson04.exe`**。接著解壓新版，執行 `bin\SAPB1Lesson04.exe`，再選取單據列查詢。

新版已編譯成功，尚未實機驗證。若仍失敗，請提供新版 `bin` 裡的 `diagnostic.log`，就能進一步定位。

---

<a id="q9"></a>

## S207-Q009｜可以了欸

日期：2026-09-29

**你的提問**

可以了欸

**當時回答**

太好了，這表示 **SAP 原生單據加按鈕 → 讀取選取列料號 → 在 SAP 內顯示各倉庫庫存** 已經跑通了。

你可以再換幾個料號，和「品項主檔 → 庫存資料」核對各倉庫數量。這版仍只讀取資料，不會修改銷售訂單。

後續就能以這個版本為基礎，包裝成由 SAP Add-On 管理員啟用的模組。

---

<a id="q10"></a>

## S207-Q010｜都可以用

日期：2026-09-29

**你的提問**

都可以用

**當時回答**

那就確認 **v3 的按鈕、選列與各倉庫庫存查詢都能正常使用**，後續以這版為基礎。

下一步可以把它做成正式部署包，讓 SAP 登入後自動啟動，不必每次手動開 EXE，就更接近顧問提供的模組體驗了。

---

<a id="q11"></a>

## S207-Q011｜好

日期：2026-09-29

**你的提問**

好

**當時回答**

已做好 **Nick Stock Query 1.0 部署包**。

- [下載部署 ZIP](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/NickStockAddon/NickStockQuery-1.0.0.0.zip)
- [部署操作說明](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/NickStockAddon/部署說明.md)

接下來：

1. 關閉目前的試玩版。
2. 在「延伸管理員 → 匯入」選擇這個 ZIP。
3. 指派給 **WellShin_US_NewTest**，啟動方式設為 **Automatic／自動**。
4. 重新登入測試公司，確認 Add-On 已連線，再開銷售訂單查詢。

由 SAP 啟動時，不會再跳出 Windows 控制視窗。

**已通過編譯與套件完整性檢查，尚未驗證實際匯入及自動啟動。** 如果匯入出現錯誤，把訊息貼給我；原本可用的 v3 試玩包仍保留。

---

<a id="q12"></a>

## S207-Q012｜我用其他電腦開 跑出這個

日期：2026-09-29

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e9fe63e8-4138-4192-ab3a-d7946a157982.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e9fe63e8-4138-4192-ab3a-d7946a157982.png
Image attachment: true



## My request:
我用其他電腦開 跑出這個

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這是我先前寫的**重複啟動限制**觸發了。

截圖顯示你透過遠端桌面使用 SAP。即使換另一台電腦連入，也可能仍是同一個遠端工作階段；裡面若已有試玩版或正式版執行，就會出現這個訊息。

先這樣處理：

1. 在**遠端桌面裡**關閉舊的第 4 課程式 `SAPB1Lesson04.exe`。
2. 到 SAP「Add-On 管理員」，查看 **Nick Stock Query**；若已連線，直接使用，不必再開 EXE。

我也已將限制改為「每個 SAP 用戶端各自判斷」，做好修正版：

[下載 1.0.0.1 部署包](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/NickStockAddon/NickStockQuery-1.0.0.1.zip)

升級時先停止舊版，再從延伸管理員匯入更新。新版已編譯通過，遠端多用戶端情境仍待實測。

---

<a id="q13"></a>

## S207-Q013｜圖片／附件提問

日期：2026-09-29

**你的提問**

# Files mentioned by the user:

## codex-clipboard-9d7dc387-28af-41ef-8195-b26a1141f2c1.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9d7dc387-28af-41ef-8195-b26a1141f2c1.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q14"></a>

## S207-Q014｜繼續

日期：2026-09-29

**你的提問**

繼續

**當時回答**

查到目前這台電腦的 **1.0.0.0 安裝成功，也有查詢成功紀錄**。但沒有截圖中 **1.0.0.1 部署失敗**的紀錄，因此還不能確定遠端主機的失敗原因。

我做好了[診斷工具](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/NickStockAddon/Collect-DeploymentDiagnostics.ps1)，只讀取紀錄，不修改設定。

請在**出現失敗畫面的遠端桌面內**：

1. 按一次「重試」，讓它留下最新錯誤。
2. 將診斷工具複製進去，右鍵選「使用 PowerShell 執行」。
3. 把桌面產生的 `NickStock-Diagnostics-日期時間.txt` 傳給我。

這樣才能分辨是舊程式占用、安裝服務、下載或套件問題，不必再盲目換版本。

---

<a id="q15"></a>

## S207-Q015｜但我在遠端的電腦用得 也可以診斷到嗎

日期：2026-09-29

**你的提問**

但我在遠端的電腦用得 也可以診斷到嗎

**當時回答**

可以，**只要在遠端桌面裡執行診斷工具，就會收集遠端那台電腦的資料**。

請把工具複製到遠端桌面，在那裡執行；產生的報告也會放在**遠端的桌面**，再傳回這個對話即可。

我目前直接檢查到的是你這邊的電腦，**無法直接讀到遠端主機的紀錄**。如果工具在本機執行，就診斷不到遠端的部署失敗。

---

<a id="q16"></a>

## S207-Q016｜放在哪裡

日期：2026-09-29

**你的提問**

放在哪裡

**當時回答**

放在**遠端電腦的桌面**就可以。

1. 先下載：[診斷工具](/C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/NickStockAddon/Collect-DeploymentDiagnostics.ps1)
2. 複製檔案，切到遠端桌面後貼上。
3. 在遠端對檔案按右鍵 → **使用 PowerShell 執行**。
4. 把遠端桌面產生的 `NickStock-Diagnostics-日期時間.txt` 傳給我。

---

<a id="q17"></a>

## S207-Q017｜我在這台開了 你看一下訊息

日期：2026-09-29

**你的提問**

我在這台開了 你看一下訊息

**當時回答**

找到原因了：**升級時，舊程式仍占用 DLL，導致安裝失敗。**

16:33:42 的紀錄明確寫著：
> 另一個處理序正在使用檔案 `Interop.SAPbobsCOM.dll`，所以無法存取。

這台電腦的舊版 **1.0.0.0** 也確實還在執行。

請在這台電腦：

1. 到「Add-On 管理員」停止 **Nick Stock Query**。
2. 若仍未結束，在工作管理員只結束 **`NickStockAddon.exe`**。
3. 回到「輕量型部署 Add-On 失敗」頁籤，選取 **1.0.0.1**，按「重試」。

**不用換部署包，也先不用重裝 SAP。** 這次已找到明確的檔案占用錯誤；遠端主機是否相同原因，仍需看它的紀錄。

---

<a id="q18"></a>

## S207-Q018｜在看一下

日期：2026-09-29

**你的提問**

在看一下

**當時回答**

最新 **16:40:50** 的重試仍失敗，原因相同：`Interop.SAPbobsCOM.dll` 被占用。

我確認舊版程序還沒結束：

- 程式：`NickStockAddon.exe`
- 版本：**1.0.0.0**
- PID：**33056**

請在**這台電腦**開啟工作管理員 →「詳細資料」，找到 **PID 33056** 的 `NickStockAddon.exe`，按「結束工作」，再回 SAP 按「重試」。

只結束這個 Add-On，不用關掉 SAP。

---

