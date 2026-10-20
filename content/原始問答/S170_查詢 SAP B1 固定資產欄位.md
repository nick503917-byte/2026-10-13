# 查詢 SAP B1 固定資產欄位

來源編號：S170  
聊天 ID：`01a09f21-ab04-7d02-88f4-3e3abe930b8a`  
聊天起始日期（台北）：2026-09-14  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S170-Q001｜SAPB1 我想查 關於固定資產相關的欄位 歷史取得阿等等

日期：2026-09-14

**你的提問**

SAPB1 我想查 關於固定資產相關的欄位 歷史取得阿等等

**當時回答**

你說的「**歷史取得成本**」，在 SAP B1 標準固定資產模組中，最直接對應的是 **`ITM8.APCHist`（Historical APC）**。APC 指「取得及生產成本」。以下依 SAP B1 10.0 的欄位文件整理。

| 想查的內容 | 資料表／欄位 |
|---|---|
| 資產編號 | `ITM8.ItemCode` |
| 期間類別 | `ITM8.PeriodCat` |
| 折舊範圍 | `ITM8.DprArea` |
| **歷史取得及生產成本** | **`ITM8.APCHist`** |
| 取得及生產成本 | `ITM8.APC` |
| 累計一般折舊 | `ITM8.OrDpAcc` |
| 累計非計劃折舊 | `ITM8.UnDpAcc` |
| 累計特殊折舊 | `ITM8.SpDpAcc1`～`SpDpAcc3` |
| 殘值 | `ITM8.SalvageVal` |
| 資產數量 | `ITM8.Quantity` |

來源：[SAP 官方 ITM8 欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/ITM8.html)。

另外，固定資產常會一起查這幾張表：

| 資料表 | 用途 | 常用欄位 |
|---|---|---|
| `OITM` | 資產編號、名稱等主檔資料 | `ItemCode`、`ItemName` |
| `ITM7` | 折舊設定 | `DprStart` 開始日期、`DprEnd` 結束日期、`UsefulLife` 使用年限、`RemainLife` 剩餘年限、`DprType` 折舊類型 |
| `FIX1` | 固定資產交易明細 | `RefDate` 過帳日期、`TransType` 交易類型、`APC` 成本、`OrdDpr` 一般折舊、`HistOrdDpr` 歷史一般折舊 |

來源：[OITM](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/OITM.html)、[ITM7](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/ITM7.html)、[FIX1](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/FIX1.html)。

**「歷史取得成本」和「歷次取得交易」是兩種查法**：前者找 `ITM8.APCHist`；後者可查 `FIX1`，其中 `TransType = '110'` 是取得、`'115'` 是後續取得。[SAP 交易類型定義](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/FIX1.html)

串接時也要注意：`ITM7`、`ITM8` 的鍵值是 **資產編號＋期間類別＋折舊範圍**，不能只用資產編號互接，否則可能產生重複資料。

你目前用的是 **SQL Server 還是 HANA**？你要查的是資產主檔上的成本、累計折舊等欄位，還是每筆取得／異動歷史？我可以依你的環境直接整理查詢 SQL。

---

<a id="q2"></a>

## S170-Q002｜如果我是固定資產 SAPB1 通常怎麼設定

日期：2026-09-14

**你的提問**

# Files mentioned by the user:

## codex-clipboard-b9edf2f8-bb61-4449-8027-bc7854bc1920.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b9edf2f8-bb61-4449-8027-bc7854bc1920.png



## My request:


如果我是固定資產 SAPB1 通常怎麼設定

[原對話含圖片，本文未嵌入圖片]

**當時回答**

**如果你要用 SAP B1 的固定資產模組管理取得成本、折舊與報廢，主要要設定的是「固定資產科目決定＋資產類別」。** 你截圖的「項目群組 → 會計」頁面，並不是完整的固定資產科目設定位置。

以下以 SAP B1 內建固定資產模組為例。

**1. 先建立固定資產的科目決定**

路徑通常是：

**管理 → 設定 → 財務 → 固定資產 → 科目決定**

可以依資產種類建立，例如「電腦設備」「機器設備」「運輸設備」。每組指定相應的會計科目。[SAP 官方設定路徑](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~FAAccountDeterminationsService.html)

常見配置如下，科目名稱是示意，實際代碼要對應你公司的科目表：

| 固定資產科目設定 | 通常對應的科目示例 |
|---|---|
| 資產負債表科目 | 電腦設備／機器設備 |
| 取得清算科目 | 固定資產取得清算／購置過渡科目 |
| 一般折舊科目 | 折舊費用－電腦設備 |
| 累計一般折舊科目 | 累計折舊－電腦設備 |
| 非計劃／特殊折舊相關科目 | 依實際採用的折舊處理設定 |
| 處分、報廢相關科目 | 資產處分收入、處分利益／損失等 |

這組科目決定會連結至資產類別，再套用到個別資產。[SAP 固定資產設定教材](https://help.sap.com/doc/download_multimedia_ebooks_businessone90_fainitialsettings_story_html/9.0/en-US/story_content/external_files/90_FA_InitialSettings.pdf)

**2. 建立資產類別，設定折舊方式**

路徑：

**管理 → 設定 → 財務 → 固定資產 → 資產類別**

例如建立「電腦設備」：

| 設定項目 | 示意內容 |
|---|---|
| 資產類別 | 電腦設備 |
| 科目決定 | 電腦設備科目組 |
| 折舊範圍 | 公司使用的帳務折舊範圍 |
| 折舊類型 | 例如直線法 |
| 使用年限 | 依公司會計政策設定 |

接著到 **財務 → 固定資產 → 資產主檔**，替每項資產指定資產類別，相關折舊參數就會帶入。[SAP 資產類別設定說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/2656e37c26124e1bbb33af8878e81ecf.html)

**3. 你截圖的這頁怎麼處理？**

**先不要把整頁的「存貨科目、銷貨成本、差異科目」全部填成固定資產科目。** 要先確認你建立的是內建模組的「資產主檔」，還是一般項目主檔中的固定資產項目；兩者的設定情境不能直接混用。

你前面提到的「歷史取得成本、累計折舊」，則屬於資產金額資料，也不是填在這張項目群組的科目設定頁。

**你現在是從「財務 → 固定資產 → 資產主檔」建立資產，還是從「存貨 → 項目主檔」建立？** 這能確認你截圖上的「費用科目」應如何處理。

---

