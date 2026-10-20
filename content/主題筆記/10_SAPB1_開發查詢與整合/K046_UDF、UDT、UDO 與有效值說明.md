---
title: "UDF、UDT、UDO 與有效值說明"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S037","S057","S086"]
---

# UDF、UDT、UDO 與有效值說明

[回總覽](../../00_閱讀入口.md)

## 整合摘要

UDF 是欄位、UDT 是資料表、UDO 是業務物件。整理主資料／文件型選擇、以月份保留原料價格資料，以及 CUFD／UFD1 有效值的代碼與說明映射；另含 WinRAR 解壓縮的插入提問。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S037｜確認使用者定義欄位驗證](<../../原始問答/S037_確認使用者定義欄位驗證.md>)：2026-07-30，4 段問答。
- [S057｜釐清 SAP B1 自定義表格物件類型](<../../原始問答/S057_釐清 SAP B1 自定義表格物件類型.md>)：2026-08-07，3 段問答。
- [S086｜解釋 UDO UDF UDT](<../../原始問答/S086_解釋 UDO UDF UDT.md>)：2026-08-19，3 段問答。

## 問題索引

- [01｜這裡是 使用者定義欄位管理的欄位資料。 如果我的驗證是用有效值](#qa1)
- [02｜這要我報表怎麼拉說明](#qa2)
- [03｜沒有UFD1](#qa3)
- [04｜沒有其他方式嗎](#qa4)
- [05｜如果我想在sapb1做自定義表格 我可以做到類似的嗎 物件類型要選甚麼](#qa5)
- [06｜但我目前就是固定這四個資料 我想要直接加進去欄位](#qa6)
- [07｜但我每個月都會變](#qa7)
- [08｜UDO UDF UDT 分別是甚麼意思](#qa8)
- [09｜圖片／附件提問](#qa9)
- [10｜解壓縮是哪一個](#qa10)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 這裡是 使用者定義欄位管理的欄位資料。 如果我的驗證是用有效值

來源：[S037-Q001](<../../原始問答/S037_確認使用者定義欄位驗證.md#q1>)；2026-07-30

**問題**

# Files mentioned by the user:

## codex-clipboard-f7b89d41-5e2d-44d8-ae50-bfe4e9c928f8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f7b89d41-5e2d-44d8-ae50-bfe4e9c928f8.png

## My request for Codex:
這裡是 使用者定義欄位管理的欄位資料。

如果我的驗證是用有效值

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

了解，這個使用者定義欄位的驗證方式是「有效值」：

- 欄位：`PlannedOrder`
- 類型：字母與數字
- 長度：5
- `Y`：計劃採購
- `N`：非計劃採購
- 未設定預設值，也不是必要欄位

您接下來想確認哪種用法？例如 SQL 查詢／更新、SAP Business One SDK 讀取、畫面顯示說明，或依有效值做條件判斷？

<a id="qa2"></a>

### 2. 這要我報表怎麼拉說明

來源：[S037-Q002](<../../原始問答/S037_確認使用者定義欄位驗證.md#q2>)；2026-07-30

**問題**

這要我報表怎麼拉說明

**歷史回答**

報表若要把 `Y / N` 顯示成中文說明，最簡單可以直接用 `CASE`：

```sql
SELECT
    T0.U_PlannedOrder AS [計劃訂購代碼],
    CASE T0.U_PlannedOrder
        WHEN 'Y' THEN N'計劃採購'
        WHEN 'N' THEN N'非計劃採購'
        ELSE N''
    END AS [計劃訂購說明]
FROM 你的資料表 T0
```

如果只需要顯示說明，不需要顯示 `Y / N`：

```sql
SELECT
    CASE T0.U_PlannedOrder
        WHEN 'Y' THEN N'計劃採購'
        WHEN 'N' THEN N'非計劃採購'
        ELSE N''
    END AS [計劃訂購]
FROM 你的資料表 T0
```

如果希望報表自動讀取 SAP Business One「使用者定義欄位管理」中的有效值說明，則可查 `UFD1`：

```sql
SELECT
    T0.U_PlannedOrder,
    T1.Descr AS [計劃訂購說明]
FROM 你的資料表 T0
LEFT JOIN UFD1 T1
    ON T1.TableID = '實際資料表代碼'
   AND T1.FieldID = (
        SELECT FieldID
        FROM CUFD
        WHERE TableID = '實際資料表代碼'
          AND AliasID = 'PlannedOrder'
   )
   AND T1.FldValue = T0.U_PlannedOrder
```

重點是：

- 資料欄位實際名稱為 `U_PlannedOrder`
- `Y`、`N` 的說明存放在 `UFD1.Descr`
- `CUFD` 用來找該欄位的 `FieldID`
- `TableID` 要換成這個 UDF 所屬的資料表，例如：
  - 銷售訂單表頭：`ORDR`
  - 銷售訂單明細：`RDR1`
  - 採購訂單表頭：`OPOR`
  - 採購訂單明細：`POR1`
  - 料品主檔：`OITM`

如果有效值說明未來可能修改，建議用 `CUFD + UFD1`；如果只有固定的 `Y/N`，用 `CASE` 最簡單、報表執行也較穩定。

<a id="qa3"></a>

### 3. 沒有UFD1

來源：[S037-Q003](<../../原始問答/S037_確認使用者定義欄位驗證.md#q3>)；2026-07-30

**問題**

沒有UFD1

**歷史回答**

如果報表資料來源沒有 `UFD1`，就直接在報表查詢用 `CASE` 轉換：

```sql
CASE
    WHEN T0.U_PlannedOrder = 'Y' THEN N'計劃採購'
    WHEN T0.U_PlannedOrder = 'N' THEN N'非計劃採購'
    ELSE N''
END AS [計劃訂購說明]
```

完整範例：

```sql
SELECT
    T0.DocNum AS [單號],
    T0.U_PlannedOrder AS [計劃訂購代碼],
    CASE
        WHEN T0.U_PlannedOrder = 'Y' THEN N'計劃採購'
        WHEN T0.U_PlannedOrder = 'N' THEN N'非計劃採購'
        ELSE N''
    END AS [計劃訂購說明]
FROM ORDR T0
```

如果使用 Crystal Reports，也能在公式欄位中寫：

```crystal
If {ORDR.U_PlannedOrder} = "Y" Then
    "計劃採購"
Else If {ORDR.U_PlannedOrder} = "N" Then
    "非計劃採購"
Else
    ""
```

這樣報表會把資料庫中的 `Y` 顯示成「計劃採購」，把 `N` 顯示成「非計劃採購」。缺點是以後若在使用者定義欄位管理中修改說明，報表公式也需要同步修改。

<a id="qa4"></a>

### 4. 沒有其他方式嗎

來源：[S037-Q004](<../../原始問答/S037_確認使用者定義欄位驗證.md#q4>)；2026-07-30

**問題**

沒有其他方式嗎

**歷史回答**

有其他方式，但要看報表是透過什麼資料來源取得資料。

SAP Business One 官方定義中，「有效值」的說明實際仍儲存在 `UFD1`。如果你看不到，通常是資料庫 Schema、權限或報表連線來源的問題，不代表表不存在。[SAP 官方文件](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/550b9326da1247838e14d816d4290d13.html?locale=en-US)

可先測試：

SQL Server：

```sql
SELECT *
FROM dbo.UFD1
```

SAP HANA：

```sql
SELECT *
FROM "公司資料庫名稱"."UFD1"
```

如果純 SQL 報表確實無權限讀取 `UFD1`，剩下幾種方式：

1. 使用 SAP DI API

```vb
Set rs = oCompany.GetBusinessObject(BoRecordset)
Set rs = oCompany.GetBusinessObject(BoBridge).GetFieldValidValues( _
    "ORDR", "U_PlannedOrder")
```

更標準的呼叫概念是：

```text
SBObob.GetFieldValidValues("資料表", "U_PlannedOrder")
```

它會直接回傳：

```text
Value | Description
Y     | 計劃採購
N     | 非計劃採購
```

SAP 官方也說明這個方法會取得欄位的有效值及說明。[GetFieldValidValues](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~SBObob~GetFieldValidValues.html)

2. 使用 Service Layer

讀取欄位中繼資料：

```http
GET /b1s/v1/UserFieldsMD?$filter=TableName eq 'ORDR' and Name eq 'PlannedOrder'
```

回傳的 `ValidValuesMD` 會包含 Value 與 Description。注意 `Name` 通常不加 `U_`。[Service Layer 官方指南](https://help.sap.com/doc/fc2f5477516c404c8bf9ad1315a17238/10.0/en-US/Working_with_SAP_Business_One_Service_Layer.pdf)

3. Crystal Reports 公式

如果報表只有資料庫連線、無法呼叫 SDK 或 Service Layer，就只能：

```crystal
Select {ORDR.U_PlannedOrder}
Case "Y": "計劃採購"
Case "N": "非計劃採購"
Default: ""
```

結論是：若要「自動跟著使用者定義欄位管理的說明變動」，只能透過 `UFD1`、DI API 或 Service Layer；單靠文件資料表中的 `U_PlannedOrder`，只會取得 `Y/N`，不會包含說明。

<a id="qa5"></a>

### 5. 如果我想在sapb1做自定義表格 我可以做到類似的嗎 物件類型要選甚麼

來源：[S057-Q001](<../../原始問答/S057_釐清 SAP B1 自定義表格物件類型.md#q1>)；2026-08-07

**問題**

# Files mentioned by the user:

## codex-clipboard-b5994c49-4533-465b-961e-f336a70acb38.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b5994c49-4533-465b-961e-f336a70acb38.png

## My request for Codex:

如果我想在sapb1做自定義表格 我可以做到類似的嗎 物件類型要選甚麼

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

可以做到。依你圖片的用途，建議把它設計成「每個年月一筆的原物料價格主檔」，物件類型優先選：

## 建議選擇：主檔資料（Master Data）

因為這些資料是可以反覆查詢、修改的設定值，不是像發票、採購單那種一次產生後便形成交易歷程的單據。

建議結構：

### 主表：Master Data

例如 `@Z_MKT_PRICE`

| 欄位 | 用途 |
|---|---|
| `Code` | 年月，例如 `202512`，必須唯一 |
| `Name` | 可同樣填 `202512` |
| `U_YearMonth` | 年月 |
| `U_CreateUser` | 建立人員 |
| `U_CreateDate` | 建立日期 |
| `U_UpdateUser` | 修改人員 |
| `U_UpdateDate` | 修改日期 |

### 明細表：Master Data Rows

例如 `@Z_MKT_PRICE1`

| 欄位 | 用途 |
|---|---|
| `U_Material` | 原物料代碼 |
| `U_MaterialName` | 例如 2.6mm 銅桿、PVC 粉 |
| `U_LastPrice` | 上月市場均價 |
| `U_StdCost` | 年度標準成本 |
| `U_Currency` | USD |
| `U_Uom` | 公噸 |

也就是：

```text
主表：Master Data
  └─ 明細表：Master Data Rows
```

這種方式比較有彈性，未來增加第五、六種原料時，只要增加明細列，不需要一直增加欄位。SAP 官方也把 Master Data Rows 定義為 Master Data 的子表，而 UDO 可以自動提供新增、更新、刪除及查找功能。[SAP User-Defined Objects](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/4510031acf465d7ae10000000a11466f.html)

如果四種原料永遠固定，也可以只建立一張 `Master Data` 表，把銅桿、PVC、DPHP、TOTM 各做成獨立欄位，但後續擴充會比較麻煩。

## 其他物件類型何時使用

- `No Object`：只是單純存資料，透過查詢或 Add-on 自己讀寫，不需要 UDO 的新增、查找、瀏覽功能。
- `No Object with Auto Increment`：單純資料表，Code 由系統遞增；不適合直接把 `202512` 當 Code。
- `Master Data`：最適合你這種年月價格設定。
- `Master Data Rows`：搭配 Master Data，存放各原物料價格。
- `Document / Document Rows`：適合每月都要「過帳」、不可隨意修改、需要單號及交易歷程的情況。

要注意：表格的物件類型建立後不能更改，所以建表前要先確定。[SAP 自訂表說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_WEB_CLIENT/2554bf7e9aa347729b0547a737e123ac/ef5a3b4a8f394f1396fa7b76f47b8e4b.html)

另外，建立 UDO 時可以讓 SAP B1 自動產生預設表單，但若要做到圖片中完全相同的排版——固定標題、左右欄位、建立／修改資訊位置——通常還需要用 UI API/Add-on 製作自訂表單；單靠 UDT/UDO 預設畫面通常會是標準欄位加矩陣格式。[SAP UDO 建立說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/740b0ed4858447aa9cb7398046fd9f3b.html)

<a id="qa6"></a>

### 6. 但我目前就是固定這四個資料 我想要直接加進去欄位

來源：[S057-Q002](<../../原始問答/S057_釐清 SAP B1 自定義表格物件類型.md#q2>)；2026-08-07

**問題**

但我目前就是固定這四個資料 我想要直接加進去欄位

**歷史回答**

可以，如果確定永遠固定這四種原料，就不需要明細表。

物件類型選「主檔資料（Master Data）」即可，一個年月對應一筆資料。

建議欄位如下：

| 欄位名稱 | 說明 | 類型 |
|---|---|---|
| `Code` | 年月，例如 `202512` | 系統欄位 |
| `Name` | 可填 `2025年12月` | 系統欄位 |
| `U_CuMkt` | 銅桿上月市場均價 | 金額／價格 |
| `U_CuStd` | 銅桿年度標準成本 | 金額／價格 |
| `U_PVCMkt` | PVC粉上月市場均價 | 金額／價格 |
| `U_PVCStd` | PVC粉年度標準成本 | 金額／價格 |
| `U_DPHPMkt` | DPHP油上月市場均價 | 金額／價格 |
| `U_DPHPStd` | DPHP油年度標準成本 | 金額／價格 |
| `U_TOTMMkt` | TOTM油上月市場均價 | 金額／價格 |
| `U_TOTMStd` | TOTM油年度標準成本 | 金額／價格 |
| `U_Currency` | 幣別，例如 USD | 英數 |
| `U_Uom` | 單位，例如公噸 | 英數 |

建議直接把 `Code` 當年月鍵值：

```text
Code = 202512
Name = 2025年12月
```

這樣可以避免同一個年月建立兩筆資料，因此通常不必再增加 `U_YearMonth`。

如果四種原料的幣別與單位始終都是 `USD／公噸`，甚至可以不建立 `U_Currency`、`U_Uom`，直接顯示固定文字。

建立流程為：

1. 建立一張自訂表，物件類型選「主檔資料」。
2. 在這張表新增上述八個價格欄位。
3. 到「工具 → 自訂工具 → 物件註冊精靈」。
4. 註冊成 UDO，類型選「主檔資料」。
5. 勾選產生預設表單、尋找、刪除及紀錄等需要的服務。
6. 將 `Code`、`Name` 與八個價格欄位加入表單。

建立／修改人員和日期，建議不要讓使用者自行輸入；若要呈現成圖片底部那樣，透過 Add-on 在新增、更新時自動填入會比較可靠。

結論：**一張 Master Data 自訂表、沒有 Rows 子表，四種原料各建立「市場均價＋標準成本」兩個欄位。**

<a id="qa7"></a>

### 7. 但我每個月都會變

來源：[S057-Q003](<../../原始問答/S057_釐清 SAP B1 自定義表格物件類型.md#q3>)；2026-08-07

**問題**

但我每個月都會變

**歷史回答**

正因為每個月都會變，所以應該是「每個月份新增一筆資料」，不是每個月覆蓋同一筆。

物件類型仍然選：

> 主檔資料（Master Data）

資料會像這樣保存：

| Code | Name | 銅桿市場價 | 銅桿標準成本 | PVC市場價 | PVC標準成本 | DPHP市場價 | DPHP標準成本 | TOTM市場價 | TOTM標準成本 |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 202511 | 2025年11月 | 10,800 | 10,000 | 680 | 1,000 | 970 | 1,300 | 1,500 | 2,900 |
| 202512 | 2025年12月 | 11,051 | 10,000 | 692 | 1,000 | 993 | 1,300 | 1,528 | 2,900 |
| 202601 | 2026年1月 | 11,200 | 10,500 | 710 | 1,050 | 1,010 | 1,350 | 1,550 | 3,000 |

操作方式：

- 2025 年 12 月：新增 `Code = 202512`
- 2026 年 1 月：新增 `Code = 202601`
- 2026 年 2 月：新增 `Code = 202602`

因此可以保留每個月的歷史價格，也能用 SAP B1 的「上一筆／下一筆」瀏覽不同月份。

八個價格欄位固定放在同一張表內：

```text
U_CuMkt       銅桿市場均價
U_CuStd       銅桿標準成本
U_PVCMkt      PVC市場均價
U_PVCStd      PVC標準成本
U_DPHPMkt     DPHP市場均價
U_DPHPStd     DPHP標準成本
U_TOTMMkt     TOTM市場均價
U_TOTMStd     TOTM標準成本
```

其中：

- 「上月市場均價」通常每月輸入新值。
- 「年度標準成本」即使一年只改一次，也可以每月一併保存當時適用的數值。
- 查詢某月份時，直接用 `Code = YYYYMM` 找到該筆。

如果你的意思是畫面永遠只保留最新月份，完全不需要查歷史資料，才適合只保留一筆並每月覆蓋。但以成本追溯、報表重算和稽核來說，**建議每月新增一筆，不要覆蓋舊月份。**

<a id="qa8"></a>

### 8. UDO UDF UDT 分別是甚麼意思

來源：[S086-Q001](<../../原始問答/S086_解釋 UDO UDF UDT.md#q1>)；2026-08-19

**問題**

UDO UDF UDT 分別是甚麼意思

**歷史回答**

在 **SAP Business One（SAP B1）** 的語境中：

- **UDO（User-Defined Object，自訂物件）**  
  將自訂資料表包裝成一個完整的業務物件，可提供新增、修改、刪除、搜尋、表單與選單等功能。  
  例如：建立「設備維修單」這類 SAP 原本沒有的單據。

- **UDF（User-Defined Field，自訂欄位）**  
  在 SAP 原有資料表或自訂資料表中增加欄位。  
  例如：在客戶主檔新增「會員等級」欄位。

- **UDT（User-Defined Table，自訂資料表）**  
  建立一張用來儲存自訂資料的資料表。  
  例如：建立「會員等級設定表」。

三者的常見關係是：

**UDT 儲存資料 → UDF 定義欄位 → UDO 將資料表包裝成可操作的業務功能。**

如果你看到這些縮寫的情境不是 SAP B1，它們也可能有其他意思。

<a id="qa9"></a>

### 9. 圖片／附件提問

來源：[S086-Q002](<../../原始問答/S086_解釋 UDO UDF UDT.md#q2>)；2026-08-19

**問題**

# Files mentioned by the user:

## codex-clipboard-636f98ea-565f-4547-955e-0717e8d72a0e.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-636f98ea-565f-4547-955e-0717e8d72a0e.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

我看到圖片中是 WinRAR 開啟的 `InitialTool.zip`，壓縮檔內有一個 `InitialTool` 資料夾。

你希望我協助哪一項：解壓縮、檢查內容、判斷用途，還是處理錯誤？

<a id="qa10"></a>

### 10. 解壓縮是哪一個

來源：[S086-Q003](<../../原始問答/S086_解釋 UDO UDF UDT.md#q3>)；2026-08-19

**問題**

解壓縮是哪一個

**歷史回答**

點上方工具列的 **「Extract To」**（左起第二個藍色資料夾圖示），選擇存放位置後按「確定」即可解壓縮。

