# 建立 SAP B1 SDK 每週練習

來源編號：S199  
聊天 ID：`01a0d262-1abf-7871-91a1-3b06e0bf09a6`  
聊天起始日期（台北）：2026-09-24  
保存位置：一般本機紀錄  
問答段落：8

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S199-Q001｜每週請給我一個 SAP Business One SDK／DI API 的短實作練習，聚焦 add-on 開發、生產與倉儲流程、批次管理等情境。先用簡短說明帶我理解核心概念，再給可…

日期：2026-09-24

**你的提問**

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

**當時回答**

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

---

<a id="q2"></a>

## S199-Q002｜b

日期：2026-09-24

**你的提問**

b

**當時回答**

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

---

<a id="q3"></a>

## S199-Q003｜我可以在這台寫完後 包起來成一個程式 在掛到另一台server 嗎

日期：2026-09-24

**你的提問**

我可以在這台寫完後 包起來成一個程式 在掛到另一台server 嗎

**當時回答**

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

---

<a id="q4"></a>

## S199-Q004｜應該是說 我的server 也就是我sapb1的主機 我應該會開在裡面 但我現在這台電腦 需要建設環境嗎?

日期：2026-09-24

**你的提問**

應該是說 我的server 也就是我sapb1的主機 我應該會開在裡面 但我現在這台電腦 需要建設環境嗎?

**當時回答**

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

---

<a id="q5"></a>

## S199-Q005｜OK 可以幫我開兩個專案嗎 後續會做 1. SERVICE LAYER 的串接 關於跟OA 泛微會把資料丟回來SAPB1 2. 我需要知道ADDON 怎麼做開發，不管是SAPB1要…

日期：2026-09-24

**你的提問**

OK 可以幫我開兩個專案嗎 後續會做

1. SERVICE LAYER 的串接 關於跟OA 泛微會把資料丟回來SAPB1
2. 我需要知道ADDON 怎麼做開發，不管是SAPB1要產出資料回傳給OA還是後續可能需要多個資料庫做拋轉資料的行為 我想應該都會用ADDON

**當時回答**

已幫你開好 **兩個獨立任務，各有自己的工作目錄**，並帶入目前的背景，方便之後接續：

1. **SAP B1 Service Layer｜泛微 OA 資料串接**  
   聚焦 OA 傳資料到 SAP B1，包含欄位對應、建立單據、結果回傳及失敗重試。



2. **SAP B1 Add-on｜開發入門與 OA、跨資料庫拋轉**  
   從環境設定、第一個 add-on 開始，逐步加入生產、倉儲、批次管理，以及資料回傳 OA、跨資料庫整合。



目前先建立需求起點，還沒有建立 Visual Studio 程式專案或安裝環境。第二個任務也會帶你判斷何時需要 add-on、何時適合背景整合程式。

---

<a id="q6"></a>

## S199-Q006｜幫我檢查我現在這台電腦有沒有相關的環境了

日期：2026-09-24

**你的提問**

幫我檢查我現在這台電腦有沒有相關的環境了

**當時回答**

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

---

<a id="q7"></a>

## S199-Q007｜NET Framework 4.7.2／x64 專案，加入 SAP API 參考。前面我建議選 4.8，現在依實際檢查，先選已有的 4.7.2 即可。 差在哪裡

日期：2026-09-24

**你的提問**

**NET Framework 4.7.2／x64** 專案，加入 SAP API 參考。前面我建議選 4.8，現在依實際檢查，先選已有的 4.7.2 即可。  差在哪裡

**當時回答**

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

---

<a id="q8"></a>

## S199-Q008｜跟我sapb1 的版本

日期：2026-09-24

**你的提問**

跟我sapb1 的版本

**當時回答**

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

---

