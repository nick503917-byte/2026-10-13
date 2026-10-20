---
title: "SDK、Add-on 入門與跨資料庫學習"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S199","S202"]
---

# SDK、Add-on 入門與跨資料庫學習

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 UI API／DI API、Visual Studio、.NET Framework、版本與位元架構、OA 及跨資料庫拋轉練習。版本號不同不代表不相容；以實際 SAP 支援矩陣與組件相容性為準，練習完成度看原對話回報。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S199｜建立 SAP B1 SDK 每週練習](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md>)：2026-09-24，8 段問答。
- [S202｜SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md>)：2026-09-24，44 段問答。

## 問題索引

- [01｜每週請給我一個 SAP Business One SDK／DI API 的短實作練習，聚焦 add-on 開發、生產與倉儲流程、批次管理等情境。先用簡短說明帶我理解核心概念，再給可…](#qa1)
- [02｜b](#qa2)
- [03｜我可以在這台寫完後 包起來成一個程式 在掛到另一台server 嗎](#qa3)
- [04｜應該是說 我的server 也就是我sapb1的主機 我應該會開在裡面 但我現在這台電腦 需要建設環境嗎?](#qa4)
- [05｜OK 可以幫我開兩個專案嗎 後續會做 1. SERVICE LAYER 的串接 關於跟OA 泛微會把資料丟回來SAPB1 2. 我需要知道ADDON 怎麼做開發，不管是SAPB1要…](#qa5)
- [06｜〔此紀錄未包含對應提問〕](#qa6)
- [07｜幫我檢查我現在這台電腦有沒有相關的環境了](#qa7)
- [08｜NET Framework 4.7.2／x64 專案，加入 SAP API 參考。前面我建議選 4.8，現在依實際檢查，先選已有的 4.7.2 即可。 差在哪裡](#qa8)
- [09｜跟我sapb1 的版本](#qa9)
- [10｜有裝sapb1 用戶端 但沒有sdk](#qa10)
- [11｜應該說 我會去另一台做測試 可以嗎](#qa11)
- [12｜所以我開發也需要SDK/API的元件嗎](#qa12)
- [13｜你可以幫我看 我目前這台有嗎](#qa13)
- [14｜所以不用SDK囉](#qa14)
- [15｜那我要先從哪裡開始](#qa15)
- [16｜圖片／附件提問](#qa16)
- [17｜圖片／附件提問](#qa17)
- [18｜這樣嗎](#qa18)
- [19｜DI UI 都要嘛](#qa19)
- [20｜好了](#qa20)
- [21｜沒有看到餒](#qa21)
- [22｜還是你可以幫我操作 我看著](#qa22)
- [23｜好](#qa23)
- [24｜甚麼意思阿 那台是透過遠端桌面登入的主機](#qa24)
- [25｜圖片／附件提問](#qa25)
- [26｜目前登的是正式 我有一個測試的](#qa26)
- [27｜圖片／附件提問](#qa27)
- [28｜對](#qa28)
- [29｜好](#qa29)
- [30｜如果我在我的電腦 sapb1 client 開 也會有嗎](#qa30)
- [31｜圖片／附件提問](#qa31)
- [32｜我有改訊息內容 為甚麼沒有改](#qa32)
- [33｜之前的exe](#qa33)
- [34｜還是一要欸](#qa34)
- [35｜C:\Users\nick_yeh\AppData\Local\Temp\c4481021-6160-4f83-b7f5-93c5ef8ab7aa_SAPB1-Hello-Test…](#qa35)
- [36｜我找不到 但我按exe 還是原本的 可以幫我找嗎](#qa36)
- [37｜再來還有甚麼下一個課程](#qa37)
- [38｜好](#qa38)
- [39｜圖片／附件提問](#qa39)
- [40｜圖片／附件提問](#qa40)
- [41｜有 成功的](#qa41)
- [42｜這個分別是甚麼意思](#qa42)
- [43｜那我可以拿這份來做練習嗎](#qa43)
- [44｜圖片／附件提問](#qa44)
- [45｜圖片／附件提問](#qa45)
- [46｜好](#qa46)
- [47｜圖片／附件提問](#qa47)
- [48｜隊的](#qa48)
- [49｜我要怎麼做](#qa49)
- [50｜可以跟我解釋 怎麼寫的嗎](#qa50)
- [51｜我現在發現 我們寫的這隻addon 我把Sapb1關掉後，他依然掛在我的工作管理員](#qa51)
- [52｜可以幫我把目前的學習到的以及課題分別整理出md檔跟pdf檔嗎 題目整理成pdf檔 寫的東西整理成md檔](#qa52)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 每週請給我一個 SAP Business One SDK／DI API 的短實作練習，聚焦 add-on 開發、生產與倉儲流程、批次管理等情境。先用簡短說明帶我理解核心概念，再給可…

來源：[S199-Q001](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q1>)；2026-09-24

**問題**

每週請給我一個 SAP Business One SDK／DI API 的短實作練習，聚焦 add-on 開發、生產與倉儲流程、批次管理等情境。先用簡短說明帶我理解核心概念，再給可動手做的小題目與參考解法，難度循序增加，避免一次塞太多內容。

已設定\*\*每週一上午（台灣時間）\*\*送你一題 SAP Business One SDK／DI API 短練習，從 add-on 基礎逐步進到生產、倉儲與批次管理；每題都會有簡短概念、小題目和參考解法。

### 第 1 題：在 add-on 中顯示訊息

**核心概念：** UI API 負責操作 SAP Business One 的畫面；`Application.MessageBox` 可以在用戶端顯示訊息。DI API 則用於處理業務資料，這題先練習 UI API。

**小題目：** 在已連線 SAP Business One 的 C# add-on 中，顯示「批次查詢功能準備中」。

**參考解法：** 假設 `oApplication` 是已取得的 `SAPbouiCOM.Application` 物件：
```
oApplication.MessageBox("批次查詢功能準備中");
```

執行後，確認訊息視窗有出現在 SAP Business One 中。下週會接著練習在表單上加入按鈕。

每週一SAP B1 SDK 每週練習

等等 我要在哪裡輸入這段語法

這段 C# 程式碼要寫在 **Visual Studio 的 SAP Business One add-on 專案**裡，通常放在按鈕點擊等事件的處理方法中；**不是輸入在 SAP B1 畫面、查詢管理員或 SQL 視窗**。

我上一題跳過了關鍵前提：`oApplication` 必須先透過 UI API 連上 SAP B1，單貼那一行無法執行。如果你目前還沒有 add-on 專案，先不用輸入它。你現在是已經在 Visual Studio 開了 SAP B1 專案，還是正在 SAP B1 用戶端裡找輸入位置？我可以照你目前的畫面帶你做。

**歷史回答**

這段程式要寫在 **Visual Studio 裡的 C# add-on 專案（`.cs` 檔案）**中。

可以先把操作流程理解成：

1. **Visual Studio**：撰寫、編譯與啟動 add-on。
2. **SAP Business One UI API**：讓 add-on 連上已開啟的 SAP B1 用戶端。
3. **SAP B1**：顯示程式呼叫的訊息視窗。[SAP 官方連線說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/400f0348d3f64a16bb03c38cc354eadb.html?version=10.0)

原本那一行：

```csharp
oApplication.MessageBox("批次查詢功能準備中");
```

必須放在 **UI API 連線成功、已取得 `oApplication` 之後**才能執行。前面的練習跳過了環境準備，我們先補這一步，暫時不用急著做按鈕。

**你目前是哪一種狀況？**

- **A：**只有 SAP B1，還沒安裝 Visual Studio。
- **B：**有 Visual Studio，但還沒有 SAP B1 add-on 專案。
- **C：**已經有 add-on 專案，只是不知道要貼在哪裡。

回覆 A、B 或 C 即可，我會從那一步開始帶你做。

<a id="qa2"></a>

### 2. b

來源：[S199-Q002](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q2>)；2026-09-24

**問題**

b

**歷史回答**

了解，你已有 **Visual Studio，但還沒有 add-on 專案**。我們先做一個小步驟：**建立專案，確認能加入 SAP UI API 參考**。

1. 開啟 Visual Studio，選 **「建立新專案」**。
2. 搜尋並選擇 C# 的 **「主控台應用程式（.NET Framework）」**。注意名稱要包含 `.NET Framework`。
3. 專案名稱輸入 `B1FirstAddon`，Framework 可先選 **.NET Framework 4.8**。
4. 建立後，在右側「方案總管」找到專案底下的 **「參考」**，按右鍵 → **「加入參考」**。
5. 選擇 **COM → 型別程式庫**，找 **SAP Business One UI API**，勾選後按「確定」。這是 SAP 官方連線流程所需的參考。[官方說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/400f0348d3f64a16bb03c38cc354eadb.html?version=10.0)

**程式碼之後會寫在專案中的 `Program.cs`。** 先完成以上步驟，下一步再加上連線和顯示訊息的程式。

請回覆你做到哪裡：

- **完成：**已加入 SAP Business One UI API。
- **找不到範本：**沒有「主控台應用程式（.NET Framework）」。
- **找不到元件：**COM 清單沒有 SAP Business One UI API。

如果 SAP B1 是透過**遠端桌面**開啟，也請順便告訴我，這會影響後續程式在哪台電腦執行。

<a id="qa3"></a>

### 3. 我可以在這台寫完後 包起來成一個程式 在掛到另一台server 嗎

來源：[S199-Q003](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q3>)；2026-09-24

**問題**

我可以在這台寫完後 包起來成一個程式 在掛到另一台server 嗎

**歷史回答**

**可以，你可以在這台用 Visual Studio 開發，編譯、打包後，部署到另一台 Windows Server；執行的那台通常不用安裝 Visual Studio。**

但要先區分你想做的功能：

| 想做的功能 | 使用方式 | 程式在哪裡執行 |
|---|---|---|
| SAP B1 裡增加按鈕、表單、跳出訊息 | UI API add-on，可搭配 DI API | 跟 SAP B1 用戶端一起執行 |
| 定時處理生產單、庫存移轉、批次資料 | DI API 背景程式 | 可以在 Server 上獨立執行 |

UI API 需要連到正在執行的 SAP B1 用戶端；DI API 則負責存取業務物件與資料。[SAP 元件說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_SQL/f6fb230cc90949d8b66586a39189992b/dd462399ff8d48ebac823ac1e272ba64.html?locale=en-US)

**以我們現在的「跳出訊息」練習來說：**

- 如果你是遠端登入 Server，並在那裡開 SAP B1，就可以把 add-on 部署到那台 Server，在同一個使用者工作階段執行。
- 如果 SAP B1 開在每位同事自己的電腦，add-on 也要在各自的用戶端執行。只把程式放到資料庫 Server，不會讓大家的 SAP B1 自動出現訊息。

**打包時也通常不只是帶一個 `.exe`。** 還要包含相依 DLL、設定檔，並確認目標機器有對應的 .NET Framework、SAP 元件，以及相符的版本與 x86／x64 架構。

你說的那台 Server，是 **「你會遠端登入並開 SAP B1 的主機」**，還是 **「想讓程式在背景自動跑的主機」**？

<a id="qa4"></a>

### 4. 應該是說 我的server 也就是我sapb1的主機 我應該會開在裡面 但我現在這台電腦 需要建設環境嗎?

來源：[S199-Q004](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q4>)；2026-09-24

**問題**

應該是說 我的server 也就是我sapb1的主機 我應該會開在裡面 但我現在這台電腦 需要建設環境嗎?

**歷史回答**

**需要，但開發電腦不用安裝整套 SAP B1 伺服器或資料庫。** 要裝多少，取決於你要不要在這台直接測試。

依你描述，可以分成這兩種方式：

| 方式 | 你現在這台電腦 | SAP B1 主機 |
|---|---|---|
| **在本機寫程式，到主機測試** | Visual Studio、.NET Framework 開發工具、SAP API 參考組件 | SAP B1 用戶端、程式所需的執行環境及 API 元件 |
| **在本機寫程式，也在本機測試** | 上述工具，加上 SAP B1 用戶端及所需 API 元件 | 提供 SAP B1 後端服務 |

我們現在練習的 **UI API 訊息視窗，必須連到正在執行的 SAP B1 用戶端**。如果用戶端只開在主機的遠端桌面裡，練習程式也要在那個遠端桌面的使用者工作階段執行。[SAP 官方說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_SQL/f6fb230cc90949d8b66586a39189992b/dd462399ff8d48ebac823ac1e272ba64.html?locale=en-US)

**你可以先採用第一種方式：**

1. 在目前電腦用 Visual Studio 寫程式、編譯。
2. 把編譯結果及相依檔案複製到 SAP B1 主機。
3. 遠端登入主機，開啟 SAP B1，再執行程式測試。

另外，先修正上一個步驟：**若你這台沒有安裝 SAP UI API，Visual Studio 的 COM 清單可能找不到它，這不代表你操作錯誤。** 開發時也能透過「瀏覽」加入對應的 SAP API 參考組件。

下一步先確認一件事：**你目前這台電腦，有安裝 SAP Business One 用戶端嗎？還是完全透過遠端桌面操作 SAP？**

<a id="qa5"></a>

### 5. OK 可以幫我開兩個專案嗎 後續會做 1. SERVICE LAYER 的串接 關於跟OA 泛微會把資料丟回來SAPB1 2. 我需要知道ADDON 怎麼做開發，不管是SAPB1要…

來源：[S199-Q005](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q5>)；2026-09-24

**問題**

OK 可以幫我開兩個專案嗎 後續會做

1. SERVICE LAYER 的串接 關於跟OA 泛微會把資料丟回來SAPB1
2. 我需要知道ADDON 怎麼做開發，不管是SAPB1要產出資料回傳給OA還是後續可能需要多個資料庫做拋轉資料的行為 我想應該都會用ADDON

**歷史回答**

已幫你開好 **兩個獨立任務，各有自己的工作目錄**，並帶入目前的背景，方便之後接續：

1. **SAP B1 Service Layer｜泛微 OA 資料串接**  
   聚焦 OA 傳資料到 SAP B1，包含欄位對應、建立單據、結果回傳及失敗重試。



2. **SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉**  
   從環境設定、第一個 add-on 開始，逐步加入生產、倉儲、批次管理，以及資料回傳 OA、跨資料庫整合。



目前先建立需求起點，還沒有建立 Visual Studio 程式專案或安裝環境。第二個任務也會帶你判斷何時需要 add-on、何時適合背景整合程式。

<a id="qa6"></a>

### 6. 〔此紀錄未包含對應提問〕

來源：[S202-Q001](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q1>)；2026-09-24

**問題**

〔此紀錄未包含對應提問〕

**歷史回答**

這個任務會循序學習 **SAP Business One 開發與系統整合**，採用「簡短概念 → 小實作 → 參考解法」的方式。

預計範圍：

- 釐清 UI API、DI API、Service Layer 與背景整合服務的用途，再依情境選擇架構；資料拋轉不預設一定要做成 add-on。
- 從基本訊息視窗、UI 按鈕，逐步進入業務資料讀寫、生產、倉儲及批次管理。
- 後續處理 SAP B1 資料回傳泛微 OA、跨公司或跨系統拋轉，以及記錄與重試。
- SAP 業務資料寫入使用受支援的 API，不直接修改 SAP 系統資料表。

**下次開始時，先確認：**

1. 開發電腦是否已安裝 SAP B1 用戶端及 SDK。
2. SAP B1 的版本、修補版本與用戶端位元架構。
3. 資料庫使用 SQL Server 還是 SAP HANA。
4. Visual Studio 的版本與已安裝的 .NET 開發工具。
5. 未來程式在哪裡執行：目前電腦，或透過遠端桌面登入的 SAP B1 Windows 主機。
6. 是否有可用的測試公司資料庫與測試帳號。

目前先停在範圍整理，不進行安裝、實作或正式系統連線。等你繼續時，就從環境確認開始。

<a id="qa7"></a>

### 7. 幫我檢查我現在這台電腦有沒有相關的環境了

來源：[S199-Q006](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q6>)；2026-09-24

**問題**

幫我檢查我現在這台電腦有沒有相關的環境了

**歷史回答**

**檢查好了，你這台已經有大部分開發環境，可以開始做 SAP B1 add-on，不用從零安裝。**

| 項目 | 檢查結果 |
|---|---|
| Visual Studio | ✅ Enterprise 2019，版本 16.11.58 |
| C#／.NET 桌面開發工具 | ✅ 已安裝，含編譯器與 MSBuild |
| SAP B1 用戶端 | ✅ 64 位元，檔案版本 `10.00.291` |
| UI API | ✅ 已找到，COM 已註冊 |
| DI API | ✅ 已安裝 64 位元，10.0 元件版本 `10.00.291` |
| .NET Framework 4.7.2 開發參考組件 | ✅ 已安裝 |
| .NET Framework 4.8 Targeting Pack | ⚠️ 未找到；有執行環境及 SDK，不代表有這個開發套件 |
| 現代 .NET SDK | ✅ 已安裝 `10.0.303` |
| SAP SDK 文件與範例套件 | 未在標準安裝位置找到 |

**對你兩個開發方向的影響：**

- **Add-on：**可以先建立 **C#／.NET Framework 4.7.2／x64** 專案，加入 SAP API 參考。前面我建議選 4.8，現在依實際檢查，先選已有的 4.7.2 即可。
- **Service Layer：**已有撰寫串接程式的基本工具。本機不需要安裝 Service Layer，但仍要確認 SAP 主機上的服務網址、版本及連線是否可用。

另外，你這台**確實已安裝 SAP B1 用戶端**，所以若能從本機登入 SAP B1，就有條件直接在本機測試 UI add-on，不必每次搬到主機。

這次確認的是安裝檔案、工具與註冊狀態；**尚未編譯測試或登入 SAP 驗證連線，也沒有更動設定。**

<a id="qa8"></a>

### 8. NET Framework 4.7.2／x64 專案，加入 SAP API 參考。前面我建議選 4.8，現在依實際檢查，先選已有的 4.7.2 即可。 差在哪裡

來源：[S199-Q007](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q7>)；2026-09-24

**問題**

**NET Framework 4.7.2／x64** 專案，加入 SAP API 參考。前面我建議選 4.8，現在依實際檢查，先選已有的 4.7.2 即可。  差在哪裡

**歷史回答**

**對你目前第一個 SAP B1 add-on 練習，選 4.7.2 或 4.8，程式寫法幾乎沒有差別。** 主要差在「開發時需要的工具」和「部署主機需要的版本」。

| 比較 | .NET Framework 4.7.2 | .NET Framework 4.8 |
|---|---|---|
| 版本 | 較早 | 較新，包含框架改進 |
| 你的電腦能否直接以它為目標編譯 | ✅ 已有對應開發參考組件 | 需要補裝 4.8 Targeting Pack |
| 部署主機所需執行環境 | 4.7.2 或相容的較新版本 | 4.8 或相容的較新版本 |

一般而言，**以 4.7.2 編譯的程式可以在 4.8 環境執行；以 4.8 編譯的程式，不能假設在只有 4.7.2 的環境正常運作。** [Microsoft 版本說明](https://learn.microsoft.com/en-us/dotnet/framework/migration-guide/versions-and-dependencies)

你這台的狀況，關鍵是兩個不同東西：

- **Runtime（執行環境）**：讓電腦「執行」程式。
- **Targeting Pack（目標版本套件）**：讓 Visual Studio 按指定版本的功能「編譯」程式。

所以，有較新的執行環境，仍可能缺少以 4.8 為目標的編譯組件。[Microsoft 開發套件說明](https://learn.microsoft.com/en-us/dotnet/framework/install/guide-for-developers)

另外，**`x64` 是另一個獨立設定，代表程式以 64 位元執行**。你安裝的是 64 位元 SAP B1／DI API，因此先用 x64 配合。

我前面建議 4.7.2，是因為**你現在就能開始練習**，不是因為 SAP B1 一定要用它。正式專案會先確認主機的 .NET 與 SAP 元件支援版本，再決定；若確認適合 4.8，就補上開發套件並使用 4.8。

<a id="qa9"></a>

### 9. 跟我sapb1 的版本

來源：[S199-Q008](<../../原始問答/S199_建立 SAP B1 SDK 每週練習.md#q8>)；2026-09-24

**問題**

跟我sapb1 的版本

**歷史回答**

**有關，但 .NET Framework 的版本號不需要跟 SAP B1 一樣。** 它們是不同軟體，各自有自己的版本。

你這台目前查到：

| 元件 | 版本 | 用途 |
|---|---|---|
| SAP B1 用戶端 | `10.00.291` | 你操作的 SAP 系統畫面 |
| SAP UI API／DI API 10.0 元件 | `10.00.291` | 讓程式操作 SAP 畫面與業務資料 |
| .NET Framework 專案目標 | 可先用 `4.7.2` | 編譯、執行 C# 程式的框架 |

**真正要確認的是：SAP B1 該版本支援哪些 .NET Framework，以及開發機與部署主機的 SAP API 元件是否相容。**

目前只能確認你這台的 SAP 用戶端與 API 10.0 元件版本一致，**還沒確認另一台 SAP 主機的版本與官方支援條件**。所以我前面說「可以先用 4.7.2」，是依本機開發工具判斷，還不能當作正式部署的相容性結論。

下一步可以在 SAP B1 的 **「說明 → 關於 SAP Business One」**查看完整版本，包含 FP／PL；你把版本文字貼給我，就能再核對適合用 4.7.2 還是 4.8。

<a id="qa10"></a>

### 10. 有裝sapb1 用戶端 但沒有sdk

來源：[S202-Q002](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q2>)；2026-09-24

**問題**

有裝sapb1 用戶端 但沒有sdk

**歷史回答**

了解，目前確認：**開發電腦已安裝 SAP B1 用戶端，但尚未安裝 SDK**。

我們先確認版本，再找對應的 SDK 安裝來源。

請在 SAP B1 用戶端開啟 **「說明 → 關於 SAP Business One」**，把顯示的版本資訊貼給我，或提供截圖；若有顯示 FP、PL 或 32／64 位元，也一起提供。

另外，你目前使用的 Visual Studio 是哪個版本？

<a id="qa11"></a>

### 11. 應該說 我會去另一台做測試 可以嗎

來源：[S202-Q003](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q3>)；2026-09-24

**問題**

應該說 我會去另一台做測試 可以嗎

**歷史回答**

可以，**在目前電腦寫程式、編譯，再拿到另一台電腦測試**，可以這樣安排。

- **開發機**：安裝 Visual Studio，以及開發所需的 SAP B1 SDK／API 元件。
- **測試機**：準備 SAP B1 用戶端、程式需要的 API 執行元件，並能登入測試公司資料庫。

如果做的是 **UI API add-on**，測試時程式需在執行 SAP B1 用戶端的同一個 Windows 登入工作階段中啟動。例如透過遠端桌面測試，就在那個遠端桌面裡同時開啟 SAP B1 與 add-on。

接下來以**測試機的 SAP B1 版本與位元架構**為準，安排開發環境。你說的另一台，是透過遠端桌面登入的 SAP 主機，還是另一台一般電腦？

<a id="qa12"></a>

### 12. 所以我開發也需要SDK/API的元件嗎

來源：[S202-Q004](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q4>)；2026-09-24

**問題**

所以我開發也需要SDK/API的元件嗎

**歷史回答**

**對，如果要開發我們前面提到的 UI API add-on，開發機需要有對應的 API 參考元件，Visual Studio 才能編譯。** 即使拿到另一台測試，這點也一樣。[SAP 官方說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/400f0348d3f64a16bb03c38cc354eadb.html?version=10.0)

不過，我前面把「SDK」與「API 元件」一起說，容易讓你以為必須全部安裝，這裡修正：

| 項目 | 用途 |
|---|---|
| API 元件 | 讓程式參考、呼叫 SAP 功能 |
| SDK 安裝項目 | 主要提供開發文件與範例 |

你已經裝了 SAP B1 用戶端，**可能已有 UI API 元件，不代表沒另外裝 SDK 就不能開發**。要先檢查現有元件。[SAP 元件說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_HANA/1a2fc202f7f64336abf9fbc957d9b9ba/faab71bd33ce45408d97fe71509d60cd.html)

另外，未來若採用 **Service Layer 做背景資料拋轉**，程式透過 HTTP 呼叫服務，開發機就不需要安裝 UI API／DI API 元件。[SAP Service Layer 說明](https://learning.sap.com/courses/leveraging-the-sap-business-one-service-layer/exploring-the-service-layer-api-architecture-and-capabilities)

所以目前**先不用急著安裝**，下一步先確認你的開發機是否已有可用的 UI API。

<a id="qa13"></a>

### 13. 你可以幫我看 我目前這台有嗎

來源：[S202-Q005](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q5>)；2026-09-24

**問題**

你可以幫我看 我目前這台有嗎

**歷史回答**

有，已確認你這台電腦有以下元件：

| 元件 | 檢查結果 |
|---|---|
| SAP B1 用戶端 | 已安裝，64 位元 |
| UI API | 已找到程式檔，且有 COM 註冊 |
| DI API | 已安裝，64 位元，且有 COM 註冊 |
| 主要元件檔案版本 | `10.00.291` |

**所以你已經有開發 UI API／DI API 程式的基本元件，不需要先另外安裝完整 SDK 才能開始。**

下一步可以在 Visual Studio 建立小專案，確認能加入 SAP API 參考並編譯；實際執行測試前，再核對另一台測試機的版本與位元架構。

這次只有讀取安裝資訊與檔案，沒有變更設定或連線 SAP 資料庫。

<a id="qa14"></a>

### 14. 所以不用SDK囉

來源：[S202-Q006](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q6>)；2026-09-24

**問題**

所以不用SDK囉

**歷史回答**

對，**目前不用另外安裝 SDK，就可以開始做我們的小實作**，因為你這台已經有 UI API 與 DI API 元件，能供 Visual Studio 參考使用。

另外安裝 SDK 主要是取得開發文件與範例，之後有需要再補即可。接下來先確認 Visual Studio 能加入 API 參考並成功編譯。

<a id="qa15"></a>

### 15. 那我要先從哪裡開始

來源：[S202-Q007](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q7>)；2026-09-24

**問題**

那我要先從哪裡開始

**歷史回答**

先從 **建立一個能編譯的 C# 專案** 開始，確認 Visual Studio 能使用你電腦上的 UI API。

這次只做環境確認，還不用登入 SAP 或連線資料庫。

1. 開啟 Visual Studio，選擇「建立新專案」。
2. 搜尋並選擇 **「主控台應用程式（.NET Framework）」**，語言選 **C#**。注意範本名稱要有 `.NET Framework`。
3. 專案名稱輸入 `SapB1Hello`，Framework 選 **.NET Framework 4.8**。
4. 建立後，在右側「方案總管」找到「參考」，按右鍵 →「加入參考」。
5. 選「COM」，尋找並勾選 **SAP Business One UI API**，按「確定」。
6. 開啟「專案 → 屬性 → 建置」，將「平台目標」設定為 **x64**，配合你已安裝的 64 位元 SAP 元件。
7. 按 **Ctrl + Shift + B** 建置專案。

**這一步的成功標準：參考已加入，而且建置成功。** 下一步才加入連接 SAP B1 與顯示訊息視窗的程式碼。

如果找不到 `.NET Framework` 專案範本或 UI API，告訴我停在哪一步，以及畫面顯示的內容，我們就從那裡處理。

<a id="qa16"></a>

### 16. 圖片／附件提問

來源：[S202-Q008](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q8>)；2026-09-24

**問題**

# Files mentioned by the user:

## codex-clipboard-bad0e6ae-2200-49aa-bb9b-df359d168bf9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-bad0e6ae-2200-49aa-bb9b-df359d168bf9.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你目前選到的 **Console App** 沒有標示 `.NET Framework`，先不要按下一步。

請在這個畫面：

1. 上方搜尋框輸入 **`主控台`**。
2. 「所有語言」改成 **C#**。
3. 找到並選擇 **「主控台應用程式 (.NET Framework)」**。

如果搜尋不到，再把搜尋文字改成 **`.NET Framework`**，看看有哪些範本。你可以貼搜尋結果，我幫你確認要選哪個。

<a id="qa17"></a>

### 17. 圖片／附件提問

來源：[S202-Q009](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q9>)；2026-09-24

**問題**

# Files mentioned by the user:

## codex-clipboard-03895b30-a028-41ad-bfe3-a7c8d3447d8d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-03895b30-a028-41ad-bfe3-a7c8d3447d8d.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

專案 **SAPB1ADDON** 已建立成功。接下來先加入 UI API 參考，程式碼暫時不用改。

1. 在右側「方案總管」的 **「參考」** 上按右鍵。
2. 選 **「加入參考」**。
3. 左側選 **「COM」→「類型程式庫」**。
4. 找到並勾選 **SAP Business One UI API**。
5. 按「確定」。

這一步是讓專案認得 SAP 的畫面操作功能，例如之後要用的 `MessageBox`。

如果清單裡找不到，貼出「加入參考」的畫面，我幫你確認。

<a id="qa18"></a>

### 18. 這樣嗎

來源：[S202-Q010](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q10>)；2026-09-24

**問題**

這樣嗎

**歷史回答**

這則訊息沒有看到新的截圖，所以還無法確認。

如果你已勾選 **SAP Business One UI API**，就可以按「確定」。接著展開右側的「參考」，確認裡面有 **`SAPbouiCOM`**；有出現就表示加入成功。

<a id="qa19"></a>

### 19. DI UI 都要嘛

來源：[S202-Q011](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q11>)；2026-09-24

**問題**

DI UI 都要嘛

**歷史回答**

**目前只需要 UI API，DI API 之後用到再加。**

- **UI API（`SAPbouiCOM`）**：操作 SAP 畫面，例如訊息視窗、按鈕、選單。
- **DI API（`SAPbobsCOM`）**：讀寫業務資料，例如建立訂單、更新料號。

我們第一個練習是顯示訊息視窗，所以先加 **SAP Business One UI API**。如果你已經兩個都加了，也可以保留，不影響這次練習。

<a id="qa20"></a>

### 20. 好了

來源：[S202-Q012](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q12>)；2026-09-24

**問題**

好了

**歷史回答**

接下來設定 **64 位元**，再確認專案能編譯：

1. 在右側專案 **SAPB1ADDON** 上按右鍵 →「屬性」。
2. 左側選「建置」。
3. 將「平台目標」改成 **x64**。
4. 按 **Ctrl + S** 儲存。
5. 按 **Ctrl + Shift + B** 建置。

這是為了配合你電腦上的 **64 位元 SAP API**。

下方若顯示 **「建置成功」**，這一步就完成了；若有錯誤，把錯誤訊息貼給我。先不用按「開始」執行。

<a id="qa21"></a>

### 21. 沒有看到餒

來源：[S202-Q013](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q13>)；2026-09-24

**問題**

# Files mentioned by the user:

## codex-clipboard-574f0859-7253-43bd-9a2e-3acf1c7393fb.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-574f0859-7253-43bd-9a2e-3acf1c7393fb.png



## My request:
沒有看到餒

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你目前還在程式碼畫面，**專案屬性頁還沒打開**。

看右上角「方案總管」：

1. 找到 **C# 圖示旁的 `SAPB1ADDON`** 那一列。
2. 在那一列按滑鼠右鍵 → 選 **「屬性」**。
3. 開啟後，左側選 **「建置」**，就能找到 **「平台目標」**，改成 `x64`。

也可以直接**雙擊專案底下、有扳手圖示的 `Properties`**，開啟專案屬性頁。

右下角現在空白的「屬性」小視窗，不是我們要找的設定頁。

<a id="qa22"></a>

### 22. 還是你可以幫我操作 我看著

來源：[S202-Q014](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q14>)；2026-09-24

**問題**

還是你可以幫我操作 我看著

**歷史回答**

已幫你操作完成：

- 開啟 **Properties → 建置**。
- 將目前 Debug 的「平台目標」設成 **x64** 並儲存。
- 建置結果：**1 成功、0 失敗**。

上方仍顯示 `Any CPU` 是方案平台名稱；這個專案的實際平台目標已是 **x64**。

現在畫面保留在設定與建置結果。下一步就可以加入第一段 UI API 程式碼了。

<a id="qa23"></a>

### 23. 好

來源：[S202-Q015](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q15>)；2026-09-24

**問題**

好

**歷史回答**

已加入第一段程式，並確認 **建置成功，0 失敗**。

核心是這一行：

```csharp
Type sapUiType = typeof(SAPbouiCOM.Application);
```

它讓編譯器確認：**專案已能辨識 SAP UI API**。目前尚未執行，也沒有連接 SAP。

下一步才是連接 SAP、顯示訊息視窗。你之前說要在另一台測試：**那台是透過遠端桌面登入的主機，還是另一台一般電腦？**

<a id="qa24"></a>

### 24. 甚麼意思阿 那台是透過遠端桌面登入的主機

來源：[S202-Q016](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q16>)；2026-09-24

**問題**

甚麼意思阿

那台是透過遠端桌面登入的主機

**歷史回答**

意思是：**剛才只確認程式「認得 SAP 的工具」，還沒有真的連上 SAP。**

就像電話已經準備好，但還沒撥出去。畫面上的「建置成功」，代表程式能順利編譯，不代表已連線成功。

你要在**遠端桌面主機**測試，所以流程會是：

1. 在目前電腦寫程式、編譯。
2. 把編譯好的程式與需要的檔案複製到遠端主機。
3. 在遠端桌面內開啟 SAP B1，登入測試公司。
4. 在**同一個遠端桌面工作階段**執行我們的程式，讓它在 SAP 裡跳出訊息視窗。

**下一步先確認遠端主機上的 SAP B1 版本。** 你可以在那台 SAP 的「說明 → 關於 SAP Business One」查看，貼給我後，我們再繼續準備訊息視窗的小實作。

<a id="qa25"></a>

### 25. 圖片／附件提問

來源：[S202-Q017](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q17>)；2026-09-24

**問題**

# Files mentioned by the user:

## codex-clipboard-92884083-0d2d-445b-b9e9-9d472eee29b6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-92884083-0d2d-445b-b9e9-9d472eee29b6.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

確認了，遠端主機顯示：

- **SAP Business One 10.0**
- **版本：10.00.291，SP 2505 (Security) HOTFIX1**
- **64 位元**

這與你開發機查到的主要元件版本 `10.00.291`、64 位元一致，目前的 **x64 設定合適**。

下一步可以準備「連接已開啟的 SAP B1，跳出一個訊息視窗」的小程式，再放到遠端主機測試。

先確認一下：**遠端 SAP 現在登入的是測試公司，還是正式公司？**

<a id="qa26"></a>

### 26. 目前登的是正式 我有一個測試的

來源：[S202-Q018](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q18>)；2026-09-24

**問題**

目前登的是正式 我有一個測試的

**歷史回答**

那我們就用你有的**測試公司**做練習。

請先把正式公司正在編輯的工作儲存好，再於遠端 SAP B1：

1. 選「管理 → 選擇公司」。
2. 切換並登入你的**測試公司**。
3. 確認 SAP 畫面顯示的公司名稱是測試公司。

UI API 會操作它連上的 SAP 用戶端，所以要先確認公司。這次小實作只會跳出訊息視窗，不新增或修改單據。

切換完成後告訴我，我們再繼續。

<a id="qa27"></a>

### 27. 圖片／附件提問

來源：[S202-Q019](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q19>)；2026-09-24

**問題**

# Files mentioned by the user:

## codex-clipboard-d223efa1-f0e4-407f-b753-4c1e1fb0abed.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d223efa1-f0e4-407f-b753-4c1e1fb0abed.png



## My request:



[原對話含圖片，本文未嵌入圖片]

**歷史回答**

目前你選取的是 **`WellShin_US_NewTest`**，畫面也確認了資料庫使用 **SQL Server 2022**。

如果這就是你準備的測試公司，請按左下角 **「確定」**。等登入完成，再確認 SAP 主畫面的公司名稱。

目前這張圖是「選擇公司」畫面，還不能確認已切換成功。

<a id="qa28"></a>

### 28. 對

來源：[S202-Q020](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q20>)；2026-09-24

**問題**

對

**歷史回答**

好，就使用 **`WellShin_US_NewTest`** 作為這次的測試公司。

請按「確定」完成登入，並讓遠端 SAP B1 保持開啟。接下來會在目前電腦準備小程式，再複製到遠端主機執行，讓測試公司的 SAP 跳出「Hello SAP B1」訊息。

<a id="qa29"></a>

### 29. 好

來源：[S202-Q021](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q21>)；2026-09-24

**問題**

好

**歷史回答**

小程式已完成並**編譯成功**，還沒在遠端主機執行。

下載：[SAP B1 測試程式 ZIP](outputs/SAPB1-Hello-Test.zip)

接下來請：

1. 把 ZIP 複製到**遠端桌面主機**並解壓縮。
2. 在那個遠端桌面中，讓 SAP 保持登入 **`WellShin_US_NewTest`**。
3. 雙擊解壓縮後的 **`SAPB1ADDON.exe`**。

預期 SAP 會跳出 **「Hello SAP B1！」**。如果連到其他公司，程式會停止。

執行後把結果貼給我；若出現錯誤，先保留錯誤畫面。

<a id="qa30"></a>

### 30. 如果我在我的電腦 sapb1 client 開 也會有嗎

來源：[S202-Q022](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q22>)；2026-09-24

**問題**

如果我在我的電腦 sapb1 client 開 也會有嗎

**歷史回答**

**可以。** 如果你在自己的電腦開啟 SAP B1 Client，並登入 **`WellShin_US_NewTest`**，再於自己的電腦執行 `SAPB1ADDON.exe`，訊息就會出現在本機的 SAP 裡。

重點是：**SAP Client 和小程式，要在同一台電腦、同一個 Windows 登入工作階段執行。** 資料庫放在遠端主機也沒關係。

這次測試建議只開一個 SAP Client，避免程式連到另一個視窗。

<a id="qa31"></a>

### 31. 圖片／附件提問

來源：[S202-Q023](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q23>)；2026-09-24

**問題**

# Files mentioned by the user:

## codex-clipboard-84c5edcf-60b7-491d-83d7-c4a0198fa284.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-84c5edcf-60b7-491d-83d7-c4a0198fa284.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

成功了！畫面確認程式已經：

- 連接到正在執行的 SAP B1。
- 確認公司是 **`WellShin_US_NewTest`**。
- 透過 **UI API** 顯示「Hello SAP B1！」。

這次沒有新增或修改業務資料。按「確定」後，可以回到程式的黑色視窗，按 Enter 結束。

你已完成第一個 SAP B1 小實作。接下來可以先看懂這段程式的連線流程，再練習新增自己的按鈕。

<a id="qa32"></a>

### 32. 我有改訊息內容 為甚麼沒有改

來源：[S202-Q024](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q24>)；2026-09-29

**問題**

我有改訊息內容 為甚麼沒有改

**歷史回答**

最常見的原因是：**你改了 `Program.cs`，但執行的仍是之前的 EXE。** 修改程式碼後，要重新建置，EXE 才會更新。

請依序確認：

1. 改的是 `sap.MessageBox(...)` 裡的文字。`Console.WriteLine(...)` 只會改黑色視窗的內容。
2. 關掉上一次執行的小程式，再按 **Ctrl＋Shift＋B**，確認「建置成功」。
3. 執行專案 **`bin\Debug\SAPB1ADDON.exe`**。之前解壓縮的 ZIP 或複製到遠端的 EXE 不會自動更新，需要重新複製。

**你現在是按 Visual Studio 的「開始」，還是雙擊之前解壓縮的 EXE？**

<a id="qa33"></a>

### 33. 之前的exe

來源：[S202-Q025](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q25>)；2026-09-29

**問題**

之前的exe

**歷史回答**

那就對了，**之前的 EXE 還是舊版，不會隨程式碼自動更新。**

1. 在 Visual Studio 按 **Ctrl＋Shift＋B**，確認建置成功。
2. 到這個資料夾找新版：
   ```text
   C:\Users\nick_yeh\source\repos\SAPB1ADDON\SAPB1ADDON\bin\Debug
   ```
3. 執行裡面的 **`SAPB1ADDON.exe`**。

如果要在遠端主機測試，先關閉舊程式，再把這個資料夾內的執行檔與設定檔複製過去，取代舊版即可。

<a id="qa34"></a>

### 34. 還是一要欸

來源：[S202-Q026](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q26>)；2026-09-29

**問題**

還是一要欸

**歷史回答**

我查到：**原本專案裡的訊息仍是 `Hello SAP B1！`，EXE 更新時間也停在 9 月 24 日。** 所以目前查到的這份沒有套用你的修改。

可能你改的是另一份 `Program.cs`，或修改還沒存檔。

請在 Visual Studio 的 **`Program.cs` 分頁上按右鍵 →「複製完整路徑」**，把路徑貼給我，再告訴我你把訊息改成什麼。我就能對照你實際修改的那份。

<a id="qa35"></a>

### 35. C:\Users\nick_yeh\AppData\Local\Temp\c4481021-6160-4f83-b7f5-93c5ef8ab7aa_SAPB1-Hello-Test…

來源：[S202-Q027](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q27>)；2026-09-29

**問題**

C:\Users\nick_yeh\AppData\Local\Temp\c4481021-6160-4f83-b7f5-93c5ef8ab7aa_SAPB1-Hello-Test.zip.7aa\SAPB1-Hello-Test\Program.cs

**歷史回答**

找到原因了：**你改的是 ZIP 暫存資料夾裡的範例原始碼，不是原本專案的程式碼。** 它不會更新旁邊的 EXE。

請這樣做：

1. 先把你修改的訊息文字複製起來。
2. 在 Visual Studio 選「檔案 → 開啟 → 專案／方案」，開啟：
   ```text
   C:\Users\nick_yeh\source\repos\SAPB1ADDON\SAPB1ADDON.sln
   ```
3. 從右側「方案總管」開啟 **Program.cs**，修改 `sap.MessageBox(...)` 裡的文字。
4. 按 **Ctrl＋Shift＋B**，確認建置成功。
5. 執行新版：
   ```text
   C:\Users\nick_yeh\source\repos\SAPB1ADDON\SAPB1ADDON\bin\Debug\SAPB1ADDON.exe
   ```

以後都從這個 **`.sln` 方案檔**開始開啟，就比較不會改到另一份。

<a id="qa36"></a>

### 36. 我找不到 但我按exe 還是原本的 可以幫我找嗎

來源：[S202-Q028](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q28>)；2026-09-29

**問題**

我找不到 但我按exe 還是原本的 可以幫我找嗎

**歷史回答**

找到了！你原本專案已改成 **「Hello Nick！」**，我也已重新編譯成功。

新版放在這裡：

```text
C:\Users\nick_yeh\Documents\ChatGPT\SAPB1_Addon開發\outputs\Hello-Nick
```

把上面路徑貼到**檔案總管的網址列**，按 Enter，再雙擊裡面的 **SAPB1ADDON.exe**。

先關閉舊的小程式，SAP 保持登入測試公司。這份新版預期會顯示 **「Hello Nick！」**，我尚未替你執行。

<a id="qa37"></a>

### 37. 再來還有甚麼下一個課程

來源：[S202-Q029](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q29>)；2026-09-29

**問題**

再來還有甚麼下一個課程

**歷史回答**

下一課建議做：**在 SAP 裡新增自己的視窗與按鈕。**

例如做一個「整合練習」視窗，裡面有：

- 一個姓名輸入框。
- 一個「打招呼」按鈕。
- 輸入 `Nick` 後按按鈕，跳出「你好，Nick！」。

這會學到三件事：**建立畫面、讀取輸入值、處理按鈕點擊事件**。也會學到為什麼程式要保持執行，才能持續回應操作。

後續可以依這個順序進行：

| 課程 | 小實作 |
|---|---|
| 第 2 課：視窗與按鈕 | 輸入姓名、按鈕打招呼 |
| 第 3 課：查詢業務資料 | 輸入料號，查出品名與庫存 |
| 第 4 課：寫入測試資料 | 透過受支援 API 建立一筆測試資料 |
| 第 5 課：回傳泛微 OA | 整理 SAP 資料，先送到模擬接收端 |
| 第 6 課：背景自動拋轉 | 加入排程、記錄、重試與避免重複傳送 |

先完成第 2 課，之後做「查庫存」或「傳送 OA」按鈕，就有基礎了。

<a id="qa38"></a>

### 38. 好

來源：[S202-Q030](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q30>)；2026-09-29

**問題**

好

**歷史回答**

**第 2 課已準備好，編譯成功**，接著實際測試視窗與按鈕。

1. SAP 保持登入 **`WellShin_US_NewTest`**。
2. 在檔案總管開啟：
   ```text
   C:\Users\nick_yeh\Documents\ChatGPT\SAPB1_Addon開發\outputs\Lesson02\bin\Debug
   ```
3. 雙擊 **`SAPB1Lesson02.exe`**。
4. 預期 SAP 出現「整合練習」視窗，輸入姓名、按「打招呼」，就會顯示問候訊息。

這課的流程是：**建立視窗 → 等待按鈕點擊 → 讀取姓名 → 顯示訊息**。

我尚未在 SAP 裡執行驗證。你先試開，貼畫面給我，我們再對照程式碼學習。

<a id="qa39"></a>

### 39. 圖片／附件提問

來源：[S202-Q031](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q31>)；2026-09-29

**問題**

# Files mentioned by the user:

## codex-clipboard-e332e3b0-bec6-4b2b-acb7-8183386d694f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e332e3b0-bec6-4b2b-acb7-8183386d694f.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

畫面已成功建立！目前確認**輸入框和兩個按鈕都顯示正常**。

接著試兩個操作：

1. 保留 `Nick`，按 **「打招呼」**，預期跳出「你好，Nick！」。
2. 清空姓名，再按一次，預期提示「請先輸入姓名。」。

按鈕背後主要做這兩件事：

```csharp
string name = nameInput.Value.Trim(); // 讀取姓名，去掉前後空白
sap.MessageBox("你好，" + name + "！", 1, "確定", "", "");
```

先試按「打招呼」，確認按鈕事件也能正常運作。

<a id="qa40"></a>

### 40. 圖片／附件提問

來源：[S202-Q032](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q32>)；2026-09-29

**問題**

# Files mentioned by the user:

## codex-clipboard-784a956d-1200-4660-bc14-656bb5e7bfad.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-784a956d-1200-4660-bc14-656bb5e7bfad.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

看到你已把姓名改成 `jason'`，最後多了一個單引號 `'`。

現在按 **「打招呼」**，預期會顯示：

```text
你好，jason'！
```

程式會保留你輸入的文字。這張圖還沒顯示訊息視窗；按下後有跳出來嗎？

<a id="qa41"></a>

### 41. 有 成功的

來源：[S202-Q033](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q33>)；2026-09-29

**問題**

有 成功的

**歷史回答**

成功，這代表**輸入姓名 → 按鈕觸發事件 → 讀取文字 → 顯示訊息**整個流程已經通了。

程式主要分成三個部分：

| 程式位置 | 用途 |
|---|---|
| `CreatePracticeForm()` | 建立視窗、輸入框和按鈕 |
| `OnItemEvent(...)` | 收到按鈕事件，讀取姓名並打招呼 |
| `WinApp.Run()` | 讓程式持續等待操作 |

接著做一個小練習：把問候改成 **「歡迎，Jason，開始今天的練習！」**。

在第 2 課專案的 `Program.cs` 找到：

```csharp
sap.MessageBox("你好，" + name + "！", 1, "確定", "", "");
```

改成：

```csharp
sap.MessageBox("歡迎，" + name + "，開始今天的練習！", 1, "確定", "", "");
```

**先關閉 SAP 裡的練習視窗，再建置並執行新版 EXE。** 這次就由你完成一次「修改 → 編譯 → 驗證」。

<a id="qa42"></a>

### 42. 這個分別是甚麼意思

來源：[S202-Q034](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q34>)；2026-09-29

**問題**

# Files mentioned by the user:

## codex-clipboard-2bae317a-1ab4-49c6-8d89-8d16c7380371.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2bae317a-1ab4-49c6-8d89-8d16c7380371.png
Image attachment: true



## My request:
這個分別是甚麼意思

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這四個檔案是「建置」後產生的，各有不同用途：

| 檔案 | 意思 | 你需要做什麼 |
|---|---|---|
| **`SAPB1Lesson02.exe`** | 編譯好的主程式，負責連接 SAP、建立視窗、處理按鈕 | **雙擊它執行** |
| **`Interop.SAPbouiCOM.dll`** | C# 與 SAP UI API 之間的橋梁，讓程式能呼叫 SAP 功能 | 和 EXE 放在一起，不用打開 |
| **`SAPB1Lesson02.exe.config`** | 程式的設定檔，這次主要指定使用的 .NET Framework 版本 | 和 EXE 放在一起，目前不用修改 |
| **`SAPB1Lesson02.pdb`** | 偵錯資訊，協助 Visual Studio 對照原始碼與行號、追查錯誤 | 一般執行不需要，開發時保留 |

**要搬到另一台測試，直接複製這四個檔案最方便。** 那台仍需要有對應的 SAP 用戶端／UI API；這個 DLL 並不是完整的 SAP 安裝元件。

另外，**要改訊息或功能，改的是專案裡的 `Program.cs`**，再建置產生新版 EXE，不是直接修改這四個檔案。

<a id="qa43"></a>

### 43. 那我可以拿這份來做練習嗎

來源：[S202-Q035](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q35>)；2026-09-29

**問題**

那我可以拿這份來做練習嗎

**歷史回答**

可以，**第 2 課就是給你修改、練習用的**。不過截圖那四個是執行用檔案，要改功能需開啟完整專案。

在 Visual Studio 選「檔案 → 開啟 → 專案／方案」，開啟：

```text
C:\Users\nick_yeh\Documents\ChatGPT\SAPB1_Addon開發\outputs\Lesson02\SAPB1Lesson02.csproj
```

接著從「方案總管」開啟 **Program.cs**，可以先練習改：

- 視窗標題：「第 2 課：整合練習」。
- 按鈕文字：「打招呼」。
- 預設姓名：`Nick`。
- 跳出的問候內容。

每次修改後，**關閉舊的練習視窗 → 儲存 → Ctrl＋Shift＋B 建置 → 執行 `bin\Debug` 裡的新版 EXE**。

先保留連線字串與測試公司檢查，專心練習畫面和訊息即可。

<a id="qa44"></a>

### 44. 圖片／附件提問

來源：[S202-Q036](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q36>)；2026-09-29

**問題**

# Files mentioned by the user:

## codex-clipboard-2eb0c251-1511-441b-8719-bd5ff37a3e35.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2eb0c251-1511-441b-8719-bd5ff37a3e35.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

目前畫面沒有顯示「方案總管」，所以還看不到程式檔案。

1. 按 **Ctrl＋Alt＋L**，開啟「方案總管」。
2. 展開 **SAPB1Lesson02**。
3. 雙擊 **Program.cs**，就能看到程式碼。

另外，上方目前顯示 **x86**。先不用按「開始」；等打開程式後，再確認「專案屬性 → 建置 → 平台目標」是 **x64**，配合你的 SAP 版本。

<a id="qa45"></a>

### 45. 圖片／附件提問

來源：[S202-Q037](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q37>)；2026-09-29

**問題**

# Files mentioned by the user:

## codex-clipboard-f891c361-f392-45b0-a411-c579fd504744.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f891c361-f392-45b0-a411-c579fd504744.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

成功了！畫面已顯示你修改的 **「歡迎，Nick，開始今天的練習！」**，代表你已完成：

**修改原始碼 → 建置 → 執行新版 EXE → 驗證結果。**

這段程式：

```csharp
"歡迎，" + name + "，開始今天的練習！"
```

是把三段文字接起來，其中 `name` 來自輸入框。換個姓名再按按鈕，訊息就會跟著改變，不需要重新編譯。

下一課就可以把「輸入姓名」換成 **「輸入料號，查詢品名與庫存」**，開始讀取 SAP 業務資料。

<a id="qa46"></a>

### 46. 好

來源：[S202-Q038](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q38>)；2026-09-29

**問題**

好

**歷史回答**

**第 3 課已準備好，編譯成功：輸入料號，查詢品名與庫存。**

請先試操作：

1. 關閉第 2 課視窗，SAP 保持登入 **`WellShin_US_NewTest`**。
2. 在檔案總管開啟：
   ```text
   C:\Users\nick_yeh\Documents\ChatGPT\SAPB1_Addon開發\outputs\Lesson03\bin\Debug
   ```
3. 雙擊 **`SAPB1Lesson03.exe`**。
4. 輸入測試公司中一個已知的完整料號，按「查詢庫存」。

預期會顯示**品名、庫存單位，以及所有倉庫合計的現有量、已承諾量、已訂購量**。

這課的新重點是：**UI API 負責畫面，DI API 負責讀取業務資料**。程式只查詢，不修改資料；尚未在 SAP 實際驗證，你試完後貼結果給我。

<a id="qa47"></a>

### 47. 圖片／附件提問

來源：[S202-Q039](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q39>)；2026-09-29

**問題**

# Files mentioned by the user:

## codex-clipboard-45514b03-22e6-435e-a039-1d9e16a6310b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-45514b03-22e6-435e-a039-1d9e16a6310b.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

成功！這次已透過 **DI API 讀到料號 `001590` 的品名與庫存資料**。

畫面顯示：

| 項目 | 數量（PCS） |
|---|---:|
| 現有量 | 0 |
| 已承諾量 | 0 |
| 已訂購量 | 20 |
| 計算可用量 | 0 − 0 ＋ 20 ＝ **20** |

注意：**目前現有庫存是 0**，可用量的 20 包含尚未入庫的訂購量，不能當成現在能出貨 20 件。

這次流程就是：

**輸入料號 → UI API 接收按鈕事件 → DI API 查詢品項 → UI API 顯示結果。**

下一步先到 SAP 的「品項主檔 → 庫存資料」，查同一料號，核對所有倉庫的合計是否一致。這樣才能確認程式不只查得到，也查得正確。

<a id="qa48"></a>

### 48. 隊的

來源：[S202-Q040](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q40>)；2026-09-29

**問題**

隊的

**歷史回答**

了解，數量核對一致，第 3 課的基本查詢就驗證成功了。

你現在已經做到 **建立畫面、處理按鈕、讀取 SAP 業務資料**。

下一個練習建議是：**把庫存按倉庫列成表格**，例如：

| 倉庫代碼 | 現有量 | 已承諾量 | 已訂購量 |
|---|---:|---:|---:|
| 倉庫 A | … | … | … |
| 倉庫 B | … | … | … |

這樣就能看出庫存分布，也會學到 SAP 的 **Grid 表格**，比一直跳訊息視窗更適合呈現多筆資料。

<a id="qa49"></a>

### 49. 我要怎麼做

來源：[S202-Q041](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q41>)；2026-09-29

**問題**

我要怎麼做

**歷史回答**

我們分小步驟做，先在第 3 課的視窗加上 **Grid 表格**，下一步再把各倉庫數量填進去。

**1. 關閉正在執行的練習視窗**，在 Visual Studio 開啟第 3 課專案的 `Program.cs`。

**2. 找到 `CreateQueryForm()`**，把視窗高度：

```csharp
form.ClientHeight = 155;
```

改成：

```csharp
form.ClientHeight = 350;
```

**3. 在 `form.Visible = true;` 前面，加入：**

```csharp
// 建立一份暫存在畫面中的表格資料，不是資料庫資料表。
Ui.DataTable table = form.DataSources.DataTables.Add("Stock");
table.Columns.Add("倉庫代碼", Ui.BoFieldsType.ft_AlphaNumeric, 20);
table.Columns.Add("現有量", Ui.BoFieldsType.ft_Quantity);
table.Columns.Add("已承諾量", Ui.BoFieldsType.ft_Quantity);
table.Columns.Add("已訂購量", Ui.BoFieldsType.ft_Quantity);

// 在 SAP 視窗中加入 Grid，並綁定上面的資料。
Ui.Item gridItem = AddItem(
    "grdStock", Ui.BoFormItemTypes.it_GRID,
    20, 115, 490, 200);

Ui.Grid grid = (Ui.Grid)gridItem.Specific;
grid.DataTable = table;

// 本課只供查詢，不讓使用者編輯表格內容。
for (int i = 0; i < grid.Columns.Count; i++)
{
    grid.Columns.Item(i).Editable = false;
}

grid.AutoResizeColumns();
```

**4. 按 Ctrl＋Shift＋B 建置，再執行新版 EXE。**

這一步預期會看到**有四個欄位、還沒有資料列的表格**。原本的查詢仍會跳出訊息，因為我們還沒改查詢邏輯。

先完成這一步，我們再把倉庫資料放進表格。

<a id="qa50"></a>

### 50. 可以跟我解釋 怎麼寫的嗎

來源：[S202-Q042](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q42>)；2026-09-29

**問題**

可以跟我解釋 怎麼寫的嗎

**歷史回答**

可以。這段程式分成兩部分：**準備資料的表格，再建立畫面上看得到的表格。**

你可以想成：`DataTable` 裝資料，`Grid` 把資料顯示出來。

### 1. 先讓視窗變高

```csharp
form.ClientHeight = 350;
```

- `form`：我們建立的 SAP 視窗。
- `ClientHeight`：視窗內部可使用的高度。
- `350`：設定的高度，單位是像素。

因為要在按鈕下方放表格，所以把高度從 `155` 增加到 `350`。

### 2. 建立裝資料的 DataTable

```csharp
Ui.DataTable table = form.DataSources.DataTables.Add("Stock");
```

拆開看：

| 寫法 | 意思 |
|---|---|
| `Ui.DataTable` | 這個變數的型別是 SAP UI API 的資料表 |
| `table` | 我們替變數取的名字 |
| `form.DataSources.DataTables` | 這個視窗所擁有的資料表集合 |
| `.Add("Stock")` | 新增一份內部名稱叫 `Stock` 的資料表 |
| `=` | 把新增的資料表交給 `table` 變數保存 |

**這份表格只存在於畫面使用的記憶體中，不會新增 SAP 資料庫的資料表。**

另外，前面程式有：

```csharp
using Ui = SAPbouiCOM;
```

所以 `Ui.DataTable` 就是 `SAPbouiCOM.DataTable` 的簡寫。

### 3. 定義表格有哪些欄位

```csharp
table.Columns.Add("倉庫代碼", Ui.BoFieldsType.ft_AlphaNumeric, 20);
```

意思是新增一個欄位：

- 名稱：`倉庫代碼`
- 型別：`ft_AlphaNumeric`，用來放文字。
- 長度：`20`

倉庫代碼可能是 `01`、`WH-A`，所以用文字，不用數字型別，才能保留前面的 `0`。

另外三欄放數量：

```csharp
table.Columns.Add("現有量", Ui.BoFieldsType.ft_Quantity);
table.Columns.Add("已承諾量", Ui.BoFieldsType.ft_Quantity);
table.Columns.Add("已訂購量", Ui.BoFieldsType.ft_Quantity);
```

`ft_Quantity` 表示數量欄位，可以呈現小數。

到這裡只有**欄位定義**，還沒有加入任何資料列。

### 4. 在視窗裡放一個 Grid

```csharp
Ui.Item gridItem = AddItem(
    "grdStock", Ui.BoFormItemTypes.it_GRID,
    20, 115, 490, 200);
```

`AddItem` 是我們前面自己寫好的輔助方法，參數依序是：

```csharp
AddItem(內部代碼, 元件種類, 左邊位置, 上方位置, 寬度, 高度);
```

所以這段意思是：

| 參數 | 用途 |
|---|---|
| `"grdStock"` | 這個元件的內部識別代碼 |
| `it_GRID` | 建立表格元件 |
| `20` | 距離視窗內容區左邊 20 |
| `115` | 距離內容區上方 115 |
| `490` | 寬度 |
| `200` | 高度 |

### 5. 取得 Grid，並接上資料

```csharp
Ui.Grid grid = (Ui.Grid)gridItem.Specific;
grid.DataTable = table;
```

SAP 的 `Item` 是通用元件，可以代表按鈕、輸入框或表格。

- `gridItem.Specific`：取得這個元件的具體操作物件。
- `(Ui.Grid)`：告訴 C#，把它當成 Grid 使用。
- `grid.DataTable = table`：讓 Grid 顯示剛才建立的資料表。

關係就是：

```text
table：欄位與資料 → grid：畫面上的表格
```

### 6. 逐欄設定成不能修改

```csharp
for (int i = 0; i < grid.Columns.Count; i++)
{
    grid.Columns.Item(i).Editable = false;
}
```

這是 `for` 迴圈，用來重複處理每一欄：

- `int i = 0`：從第 `0` 欄開始，程式的索引通常從 0 起算。
- `i < grid.Columns.Count`：只要還沒超過欄位數，就繼續。
- `i++`：每次處理完，往下一欄。
- `Editable = false`：該欄不允許使用者編輯。

四欄的索引分別是 `0、1、2、3`。

### 7. 自動調整欄寬

```csharp
grid.AutoResizeColumns();
```

讓 Grid 自動調整各欄寬度。之後填入資料，也可以再呼叫一次。

目前這段完成的是**建立表格外觀**；下一步才會從 DI API 讀取各倉庫資料，一列一列填進 `table`。

<a id="qa51"></a>

### 51. 我現在發現 我們寫的這隻addon 我把Sapb1關掉後，他依然掛在我的工作管理員

來源：[S202-Q043](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q43>)；2026-10-01

**問題**

我現在發現 我們寫的這隻addon 我把Sapb1關掉後，他依然掛在我的工作管理員

**歷史回答**

**這是我們程式的退出處理不完整，不是你操作錯誤。**

舊版的 `ExitThread()` 只結束呼叫執行緒的訊息迴圈，不保證整支 EXE 結束；退出時還會嘗試操作正在關閉的 SAP。[Microsoft 說明](https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.application.exitthread)

我已做成 **1.0.0.2 修正版**，收到 SAP 關閉通知後會結束自己的程序，已通過編譯與模擬事件測試。

[下載修正版 ZIP](C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/NickStockAddon/NickStockQuery-1.0.0.2.zip)

需要透過原本的 **Extension Manager 更新既有延伸**，目前安裝的舊版不會自動改變。更新後，再正常關閉 SAP，確認 `NickStockAddon.exe` 是否消失。

**實際 SAP 關閉測試尚未完成**；若是 SAP 當機或被強制結束、沒有送出關閉通知，還需要另外處理。

<a id="qa52"></a>

### 52. 可以幫我把目前的學習到的以及課題分別整理出md檔跟pdf檔嗎 題目整理成pdf檔 寫的東西整理成md檔

來源：[S202-Q044](<../../原始問答/S202_SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉.md#q44>)；2026-10-06

**問題**

可以幫我把目前的學習到的以及課題分別整理出md檔跟pdf檔嗎

題目整理成pdf檔
寫的東西整理成md檔

**歷史回答**

已整理完成：

- **PDF 題本**：8 頁，包含七組練習、驗收標準與提示。[原成果：SAPB1_課題與練習.pdf](<C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/學習整理/SAPB1_課題與練習.pdf>)
- **[Markdown 學習與程式筆記](C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_Addon開發/outputs/學習整理/SAPB1_學習與程式筆記.md)**：概念解說、修改與建置流程、問題修正，以及六份程式原始碼快照。

兩份都有區分**已實測成功**與**仍待驗收**的內容。

