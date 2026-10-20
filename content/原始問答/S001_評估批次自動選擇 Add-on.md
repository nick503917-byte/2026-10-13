# 評估批次自動選擇 Add-on

來源編號：S001  
聊天 ID：`019f82ad-e19f-7d30-bb11-f245a52d77b3`  
聊天起始日期（台北）：2026-07-21  
保存位置：一般本機紀錄  
問答段落：6

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S001-Q001｜OBTN 本身是批次的管理 我想了解 SAPB1沒辦法自動選擇批次 只能一個物料一個物料慢慢選。想了解 有沒有可能用add on 做出自動選擇批號

日期：2026-07-21

**你的提問**

OBTN 本身是批次的管理 我想了解 SAPB1沒辦法自動選擇批次 只能一個物料一個物料慢慢選。想了解 有沒有可能用add on 做出自動選擇批號

**當時回答**

可以，而且這正是 SAP B1 Add-on 很適合處理的情境。

OBTN 是「批號主檔」；真正要判斷某批號在某倉庫可用多少、是否可配置，還必須搭配批號庫存與交易配置資料。Add-on 可以在單據儲存前，自動依規則為每一個批次管理物料配置批號與數量，使用者就不必逐行開啟批號選擇視窗。

常見的自動選批規則：

- FEFO：優先選最快到期的批號（食品、藥品最常用）
- FIFO：優先選最早入庫的批號
- 指定倉庫／儲位優先
- 排除已過期、凍結、品檢未放行的批號
- 客戶、訂單、製造日期或自訂欄位指定批號
- 庫存不足時，自動跨多個批號拆分配置，或提示使用者處理

實作方式通常是：

1. Add-on 監聽銷售訂單、交貨、A/R 發票、庫存發貨等單據的新增／更新事件。
2. 讀取每筆物料需求數量與倉庫。
3. 查詢可用批號庫存，按既定規則排序與分配。
4. 將選出的批號、數量寫回該單據列的批號配置。
5. 若倉庫啟用儲位管理，還要同步配置「批號 × 儲位」；否則單據會無法過帳。
6. 保留「自動選批」按鈕或自動執行開關，讓使用者在必要時覆寫結果。

SAP 官方 SDK 的 `BatchNumbers` 物件就是用來把批號與數量附加到單據列；一個物料列可配置多個批號，並可指定對應單據列。官方文件也明確列出其資料來源包含 `OBTN`、`OBTW`、`OBTQ`、`OITL`、`ITL1`。 [SAP BatchNumbers SDK 文件](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~BatchNumbers.html)

要注意兩點：

- 不能只看 OBTN：它是批號資料；選批必須以「倉庫中的實際可用量」為準，並避開已被其他文件配置的量。
- 若使用 Web Client，標準功能本身已有「手動或自動」配置批號的操作選項；若您使用的是傳統 SAP B1 Client，或標準自動規則不符合公司規則，才特別值得做 Add-on。 [SAP B1 Web Client 使用手冊](https://help.sap.com/doc/ceedc4fe720a4fc19a8e1fa75bdf02f7/10.0_SP_2411/en-US/ab9cf61542fb4359ae1ee7a972f920d5.pdf)

結論：可行，而且可以做到「按一下按鈕，整張單所有批次物料自動配完」，甚至在新增列或按儲存時自動執行。若您告訴我目前要自動選批的是哪一種單據（銷售訂單、交貨、領料、庫存發貨等）、是否有儲位管理，以及想用 FIFO 還是效期優先，我可以幫您整理一份具體的 Add-on 規格與資料表／SDK 設計。

---

<a id="q2"></a>

## S001-Q002｜我需要您寫給我程式 會是如何寫

日期：2026-07-21

**你的提問**

我需要您寫給我程式 會是如何寫

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q3"></a>

## S001-Q003｜如果我想要的是，從生產發貨的批次選擇 自動選擇

日期：2026-07-21

**你的提問**

如果我想要的是，從生產發貨的批次選擇 自動選擇

**當時回答**

已改成「生產發貨（Issue for Production）」自動選批版本：

- 讀取已釋放生產訂單的手動發料元件
- 依 FEFO 自動拆分、配置批號
- 建立 `oInventoryGenExit`，並以 `BaseType = 202` 連回生產訂單
- 略過倒沖（Backflush）元件與已完全發料列

程式與使用說明在：

- [AutoBatchAllocator.cs](C:\Users\nick_yeh\Documents\Codex\2026-07-21\obtn-sapb1-add-on\outputs\SapB1AutoBatch\AutoBatchAllocator.cs)
- [README.zh-TW.md](C:\Users\nick_yeh\Documents\Codex\2026-07-21\obtn-sapb1-add-on\outputs\SapB1AutoBatch\README.zh-TW.md)

呼叫方式：

```csharp
int issueDocEntry = allocator.CreateIssueForProduction(
    productionOrderDocEntry: 123,
    postingDate: DateTime.Today);
```

目前會發出每個元件「尚未發料」的完整數量。若您要做部分領料，我可以下一步替您加上輸入數量／掃碼批號覆寫功能。

注意：此版尚未包含「儲位管理」的批號×儲位配置；若倉庫有啟用 Bin Location，需再補 `DocumentLinesBinAllocations`。SAP 對手動發料與倒沖發料的定義可參考 [ProductionOrderIssueType SDK 文件](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~ProductionOrders_Lines~ProductionOrderIssueType.html)。

---

<a id="q4"></a>

## S001-Q004｜所以我需要複製哪裡呢

日期：2026-07-21

**你的提問**

所以我需要複製哪裡呢

**當時回答**

請先複製整個這支檔案到您的 SAP B1 Add-on C# 專案：

[AutoBatchAllocator.cs](C:\Users\nick_yeh\Documents\Codex\2026-07-21\obtn-sapb1-add-on\outputs\SapB1AutoBatch\AutoBatchAllocator.cs)

然後在您「自動選批並發料」按鈕的事件中加入：

```csharp
var allocator = new AutoBatchAllocator(company);

int issueDocEntry = allocator.CreateIssueForProduction(
    productionOrderDocEntry: 123,  // 生產訂單的 DocEntry
    postingDate: DateTime.Today);
```

其中：

- `company`：您 Add-on 目前已連線的 `SAPbobsCOM.Company`
- `123`：要改成使用者目前開啟的生產訂單 `DocEntry`，不是畫面顯示的生產單號 `DocNum`
- 專案需加入 COM 參考 `SAPbobsCOM`

如果您目前還沒有 Add-on 專案，而是只有 SAP B1 系統，不能只貼這段就執行；需要先建立 C# Add-on。我也可以接著幫您寫「按鈕事件取得目前生產訂單 DocEntry」的那一段。

---

<a id="q5"></a>

## S001-Q005｜而且我想要做按鈕

日期：2026-07-21

**你的提問**

而且我想要做按鈕

**當時回答**

按鈕已補好了。您需要複製兩個檔案到 Add-on C# 專案：

1. [AutoBatchAllocator.cs](C:\Users\nick_yeh\Documents\Codex\2026-07-21\obtn-sapb1-add-on\outputs\SapB1AutoBatch\AutoBatchAllocator.cs)
2. [ProductionOrderAutoBatchButton.cs](C:\Users\nick_yeh\Documents\Codex\2026-07-21\obtn-sapb1-add-on\outputs\SapB1AutoBatch\ProductionOrderAutoBatchButton.cs)

再在您既有的 Add-on 啟動程式加入：

```csharp
_autoBatchButton = new ProductionOrderAutoBatchButton(application, company);
application.ItemEvent += OnItemEvent;

private void OnItemEvent(
    string formUid,
    ref SAPbouiCOM.ItemEvent itemEvent,
    out bool bubbleEvent)
{
    _autoBatchButton.HandleItemEvent(formUid, ref itemEvent, out bubbleEvent);
}
```

並在類別中宣告：

```csharp
private ProductionOrderAutoBatchButton _autoBatchButton;
```

專案必須有這兩個 COM 參考：

- `SAPbobsCOM`
- `SAPbouiCOM`

完成後，重新啟動 Add-on，開啟「已儲存、Released 狀態」的生產訂單，右下角會看到「自動選批發料」按鈕。按下後程式會自動建立生產發貨單並選好批號。手動發料與倒沖元件的差異可見 [SAP SDK 文件](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~ProductionOrders_Lines~ProductionOrderIssueType.html)。

---

<a id="q6"></a>

## S001-Q006｜所以我需要填入資料庫名稱以及使用者帳號密碼嗎

日期：2026-07-21

**你的提問**

所以我需要填入資料庫名稱以及使用者帳號密碼嗎

**當時回答**

不用手動填資料庫名稱、資料庫帳密。

因為這是 SAP B1 Add-on 按鈕，應直接沿用使用者目前登入 SAP B1 的連線；這樣權限、公司資料庫、授權都會自動帶入，也不必把密碼寫在程式裡。

Add-on 啟動時要先建立 `application` 與 `company`：

```csharp
var guiApi = new SAPbouiCOM.SboGuiApi();
guiApi.Connect(Environment.GetCommandLineArgs()[1]);

var application = guiApi.GetApplication(-1);

var company = new SAPbobsCOM.Company();
int result = company.SetSboLoginContext(
    application.Company.GetConnectionContext(company.GetContextCookie()));

if (result != 0 || company.Connect() != 0)
{
    company.GetLastError(out int code, out string message);
    throw new Exception($"SAP B1 連線失敗：{code} - {message}");
}
```

後面的按鈕程式使用的就是這兩個物件：

```csharp
_autoBatchButton = new ProductionOrderAutoBatchButton(application, company);
```

只有當程式是「獨立執行」、不是由 SAP B1 Client 啟動的 Add-on 時，才需要另外處理公司資料庫與登入資訊；您現在要做的按鈕不需要。

---

