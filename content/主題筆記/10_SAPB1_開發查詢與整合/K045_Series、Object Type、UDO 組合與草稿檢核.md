---
title: "Series、Object Type、UDO 組合與草稿檢核"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S023","S024","S085","S133"]
---

# Series、Object Type、UDO 組合與草稿檢核

[回總覽](../../00_閱讀入口.md)

## 整合摘要

Series 是編號系列，Object Type 是單據類型，兩者不可因為都是數字就互相比對。合併 @FORM_NUM 組合驗證、跨單據 UNION ALL、草稿 112 與 ODRF.ObjType 分流；擴充時保持 SELECT 欄位數與型別一致。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S023｜釐清收貨單自定義欄位_問題一](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md>)：2026-07-24，13 段問答。
- [S024｜新增 Series 對應 UDO 驗證](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md>)：2026-07-24，16 段問答。
- [S085｜擴充 UDO 單據驗證範圍](<../../原始問答/S085_擴充 UDO 單據驗證範圍.md>)：2026-08-18，6 段問答。
- [S133｜新增草稿單據檢核](<../../原始問答/S133_新增草稿單據檢核.md>)：2026-09-04，3 段問答。

## 問題索引

- [01｜我想問 我從採購單轉到收貨採購單 我的自定義欄位目前是掛FMS 但因為單據不同 自定義欄位的內容也會不同。 但我目前收貨採購單的自定義欄位的內容會延用帶出採購單的內容 我該怎麼解比…](#qa1)
- [02｜SQL SERVER 欄位在表頭 UDF 的名稱是 U_typename SELECT TOP 1 T1.U_CategoryName FROM [@FORM_NUM] T1 WH…](#qa2)
- [03｜我傳錯了 是這個 SELECT TOP 1 T1.U_CategoryName FROM [@FORM_NUM] T1 WHERE T1.U_SystemCode = $[OPDN…](#qa3)
- [04｜有沒有其他的方式](#qa4)
- [05｜用SP去做操作也可能嗎](#qa5)
- [06｜SQL／程式碼調整與說明](#qa6)
- [07｜SERIES是甚麼意思](#qa7)
- [08｜SQL／程式碼調整與說明](#qa8)
- [09｜OPDN的SERIES多少](#qa9)
- [10｜11 91 91 91 21 21 21 92 92 92 92 19 93 93 93 93 93 93 93 93 5 5 91 20 98 98 98 98 98 98 98…](#qa10)
- [11｜上面的數字 幫我去釣重複後 給我個別對應甚麼單據](#qa11)
- [12｜這個應該是對應不同的單據](#qa12)
- [13｜不關OPDN 那些應該是OBJECT TYPE](#qa13)
- [14｜SQL／程式碼調整與說明](#qa14)
- [15｜我在想 我每個單據都有相同欄位名稱的 我可不可以用OBJECT TYPE 來區分](#qa15)
- [16｜[@CHANGECFG]這個甚麼](#qa16)
- [17｜[dbo].[@FORM_NUM] T0 改這個](#qa17)
- [18｜給我完整的](#qa18)
- [19｜[2026/7/24 下午 04:14] Nick: [dbo].[@FORM_NUM] T0 [2026/7/24 下午 04:18] Nick: Msg 207, Level …](#qa19)
- [20｜沒有卡住欸 可以先做小版本給我嗎 我只要收貨採購單](#qa20)
- [21｜要有U_TYPENAME因為我主要就是要這個欄位要是對的](#qa21)
- [22｜還是沒有卡住欸 可以成功新增](#qa22)
- [23｜可以卡住](#qa23)
- [24｜這個還是沒有](#qa24)
- [25｜SQL／程式碼調整與說明](#qa25)
- [26｜我再重新說明一下我的需求 採購單轉成收貨採購單時。 會把U_Typename的內容一起帶過來。 但我實際收貨採購單有自己的內容，但不會更新。 我有一個表格，有紀錄關於序列號不同 叫…](#qa26)
- [27｜這個紀錄的表格叫 [@釐清收貨單自定義欄位_問題一](thread://019f92ca-e871-7292-b492-85cb3c1d1b42)](#qa27)
- [28｜SERIES只會是數字](#qa28)
- [29｜SQL／程式碼調整與說明](#qa29)
- [30｜SQL／程式碼調整與說明](#qa30)
- [31｜UNION ALL 的 用法](#qa31)
- [32｜Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 2108 [Batch Sta…](#qa32)
- [33｜Msg 205, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1949 [Batch Sta…](#qa33)
- [34｜生產收貨 生產發貨 庫存調撥 AR貸項 採購退貨 交貨單 採購單 AR發票 銷售訂單 採購請求 草稿也要卡控](#qa34)
- [35｜好 給我一個完整的](#qa35)
- [36｜SQL／程式碼調整與說明](#qa36)
- [37｜我只要 TYPENAME](#qa37)
- [38｜給我完整的](#qa38)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 我想問 我從採購單轉到收貨採購單 我的自定義欄位目前是掛FMS 但因為單據不同 自定義欄位的內容也會不同。 但我目前收貨採購單的自定義欄位的內容會延用帶出採購單的內容 我該怎麼解比…

來源：[S023-Q001](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q1>)；2026-07-24

**問題**

我想問 我從採購單轉到收貨採購單 我的自定義欄位目前是掛FMS 但因為單據不同 自定義欄位的內容也會不同。

但我目前收貨採購單的自定義欄位的內容會延用帶出採購單的內容 我該怎麼解比較好

**歷史回答**

這看起來是 SAP Business One 的 FMS（格式化搜尋）情境。原因通常是：從「採購單」複製到「收貨採購單」時，SAP B1 會先把同一個 UDF 的值一併複製過去，而收貨採購單上的 FMS 沒有再次自動執行，所以保留了採購單的內容。

我會依欄位用途選擇以下做法：

1. 如果兩張單據的欄位意義不同：建立不同的 UDF

這是最穩定的方案，例如：

- 採購單：`U_PO_xxx`
- 收貨採購單：`U_GRPO_xxx`

不要讓兩張單據共用相同的 UDF 欄位代碼。這樣 SAP 在複製單據時，就不會把採購單的值直接帶入收貨採購單。

2. 如果必須使用同一個 UDF：替收貨採購單設定自己的 FMS

在收貨採購單的 UDF 上設定專用查詢，並啟用：

- 自動重新整理
- 當某個欄位變更時重新整理
- 建議觸發欄位選擇「供應商代碼」、「料號」、「數量」或「基礎文件編號」

例如 SQL Server 的概念可能是：

```sql
SELECT
    CASE
        WHEN $[OPDN.CardCode] = 'V0001' THEN '收貨單內容A'
        ELSE '收貨單內容B'
    END
```

但要注意：FMS 若只設定在欄位上、沒有設定自動重新整理，從採購單複製進來的原值通常不會被覆蓋。

3. 如果收貨內容需要根據原採購單計算

可以在 FMS 中利用收貨採購單明細的基礎文件欄位：

- `PDN1.BaseType = 22`：來源是採購單
- `PDN1.BaseEntry`：採購單 `DocEntry`
- `PDN1.BaseLine`：採購單明細行號

例如：

```sql
SELECT T0."U_GRPO_Value"
FROM "POR1" T0
WHERE T0."DocEntry" = $[$38.45.NUMBER]
  AND T0."LineNum"  = $[$38.46.NUMBER]
```

實際 `$[$欄位編號]` 會依 SAP B1 表單與版本而不同，需要開啟「檢視 → 系統資訊」確認 Item UID／Column UID。

我的建議是：

- 如果採購單與收貨採購單的內容本質不同：使用不同 UDF，最乾淨。
- 如果只是計算規則不同：保留同一 UDF，但替收貨採購單建立獨立 FMS，並設定自動重新整理。
- 如果新增收貨單時一定要強制改寫、不能依賴使用者觸發：考慮用 SDK／B1UP，或在加入前處理；不建議用 `SBO_SP_TransactionNotification` 直接更新單據資料。

如果你提供以下資料，我可以直接幫你寫出 FMS SQL：

- SAP B1 使用 SQL Server 還是 HANA
- 欄位在表頭還是明細列
- UDF 名稱
- 收貨採購單應該根據什麼條件產生內容

<a id="qa2"></a>

### 2. SQL SERVER 欄位在表頭 UDF 的名稱是 U_typename SELECT TOP 1 T1.U_CategoryName FROM [@FORM_NUM] T1 WH…

來源：[S023-Q002](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q2>)；2026-07-24

**問題**

SQL SERVER 
欄位在表頭
UDF 的名稱是 U_typename

SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPOR.Series]
目前我寫的FMS

**歷史回答**

你目前的 FMS 是採購單專用的，因為使用了：

```sql
$[OPOR.Series]
```

`OPOR` 是採購單表頭；收貨採購單表頭是 `OPDN`。因此收貨採購單應該另外建立一支 FMS：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPDN.Series]
```

然後在「收貨採購單」的 `U_typename` 欄位上，重新指定這支 FMS。

設定時建議：

1. 開啟收貨採購單。
2. 游標停在 `U_typename`。
3. 按 `Shift + Alt + F2` 設定格式化搜尋。
4. 指定收貨採購單專用的查詢。
5. 勾選「欄位變更時自動重新整理」。
6. 觸發欄位建議先選「供應商代碼 `CardCode`」。

之所以建議用 `CardCode` 觸發，而不只用 `Series`，是因為建立收貨採購單時，文件系列通常已經是預設值，不一定發生「系列變更」事件；如此 FMS 可能不會執行，`U_typename` 就繼續保留從採購單複製來的值。

如果 `U_SystemCode` 是文字欄位，而 `Series` 是數字欄位，建議明確轉型：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode =
      CONVERT(NVARCHAR(20), $[OPDN.Series])
```

採購單則保留原本的獨立查詢：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode =
      CONVERT(NVARCHAR(20), $[OPOR.Series])
```

也就是：

- 採購單 `U_typename` → 使用 `OPOR.Series`
- 收貨採購單 `U_typename` → 使用 `OPDN.Series`
- 兩張單據可以是同一個 UDF 名稱，但必須分別綁定不同的 FMS 查詢。

要特別留意：即使查詢改成 `OPDN.Series`，如果 FMS 沒有在複製後被觸發，原採購單內容還是可能留著。可以先測試用 `CardCode` 自動重新整理；若複製流程仍沒有觸發，代表標準 FMS 的事件時機不夠穩定，屆時比較可靠的方式是使用 B1UP／SDK，在收貨採購單載入或新增前強制重算 `U_typename`。

<a id="qa3"></a>

### 3. 我傳錯了 是這個 SELECT TOP 1 T1.U_CategoryName FROM [@FORM_NUM] T1 WHERE T1.U_SystemCode = $[OPDN…

來源：[S023-Q003](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q3>)；2026-07-24

**問題**

我傳錯了 是這個 SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPDN.Series]

**歷史回答**

了解，你的 SQL 已經正確使用收貨採購單表頭 `OPDN`：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPDN.Series]
```

真正的問題不是 SQL，而是執行時機：從採購單複製時，SAP B1 會把 `U_typename` 的舊值帶過來，但 FMS 不一定會重新執行。

建議在收貨採購單的 `U_typename` 按 `Alt + Shift + F2`，確認：

- 勾選「欄位變更時自動重新整理」
- 觸發欄位選表頭欄位
- 可先選「供應商代碼」
- 選擇「顯示已儲存的使用者定義值」

如果是 SAP B1 10.0，可以設定多個觸發欄位，建議加入：

- 供應商代碼
- 文件系列
- 過帳日期

雖然查詢只使用 `Series`，觸發欄位不一定要是 `Series`；觸發欄位的用途只是要求 SAP 再執行一次查詢。

你可以先做這個測試：

1. 從採購單複製到收貨採購單。
2. 查看 `U_typename` 是否仍是採購單內容。
3. 游標停在 `U_typename`，按 `Shift + F2` 手動執行 FMS。
4. 如果按完後內容正確，便能確定 SQL 沒問題，單純是自動觸發時機問題。

如果設定觸發欄位後仍被採購單內容覆蓋，代表複製流程中 `U_typename` 是在 FMS 執行之後才被帶入。標準 FMS 沒有一個完全可靠的「文件複製完成」觸發事件，此時有三種選擇：

- 最簡單：複製後手動按 `Shift + F2`。
- 最穩定且不用程式：另外建立收貨採購單專用 UDF，例如 `U_GRPO_typename`，避免採購單同名欄位被複製。
- 完全自動：透過 B1UP 或 SAP B1 SDK，在收貨採購單載入完成後重新計算欄位。

另外，請確認 `[@FORM_NUM]` 中每個 `U_SystemCode` 只有一筆資料。因為 `TOP 1` 沒有 `ORDER BY`，若同一個系列有多筆，SQL Server 可能取到不固定的內容。

SAP 官方也說明，自動重新整理只有在指定的觸發欄位發生變更時才會執行；「Refresh Regularly」則可能在開啟既有文件時也重新計算，造成既有文件進入 Update 狀態，因此不建議直接用在正式環境。[SAP Business One：Search by Saved Query](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/451002cccf465d7ae10000000a11466f.html)

<a id="qa4"></a>

### 4. 有沒有其他的方式

來源：[S023-Q004](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q4>)；2026-07-24

**問題**

有沒有其他的方式

**歷史回答**

有。除了調整 FMS 觸發條件外，主要有以下幾種方式。就你的情境，我最推薦「收貨採購單使用另一個 UDF」，若一定要沿用 `U_typename` 且必須全自動，再考慮 B1UP／SDK。

### 方式一：收貨採購單使用專用 UDF

例如新增：

```text
U_GRPO_typename
```

收貨採購單的 FMS 改掛在這個欄位：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPDN.Series]
```

接著：

- 採購單顯示 `U_typename`
- 收貨採購單顯示 `U_GRPO_typename`
- 在表單設定中隱藏不需要的欄位

優點是採購單轉收貨採購單時，不會因同名 UDF 而帶入錯誤內容，也不用處理 FMS 執行順序。這是不用寫程式最穩定的方式。

### 方式二：保留同一欄位，增加人工刷新動作

收貨完成複製後，讓使用者在 `U_typename` 按：

```text
Shift + F2
```

或者設定一個使用者一定會操作的欄位作為 FMS 觸發來源，例如：

- 備註
- 過帳日期
- 文件日期
- 自訂的「重新計算」欄位

可以新增一個 Yes/No UDF：

```text
U_Recalculate
```

讓使用者切換為 `Y` 時觸發 `U_typename` 的 FMS。這比依賴 `Series` 或 `CardCode` 更可控，但仍需要一次人工操作。

### 方式三：使用 B1UP

如果公司有 Boyum B1 Usability Package，可以設定事件：

```text
開啟收貨採購單
    → 從採購單複製完成
    → 執行 SQL
    → 將結果寫入 U_typename
```

查詢概念：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPDN.Series]
```

優點：

- 可以保留同一個 `U_typename`
- 不需要使用者手動刷新
- 可以準確選在複製完成後覆寫
- 通常比自行開發 SAP Add-on 快

### 方式四：使用 SAP B1 UI API／SDK Add-on

攔截收貨採購單的表單事件，例如：

- 收貨採購單載入完成
- Copy From／Copy To 完成
- 按下新增按鈕之前

程式取得目前的 `Series`，再查詢 `[@FORM_NUM]`，最後寫入：

```text
OPDN.U_typename
```

這是控制力最完整的方案，也能避免 FMS 執行順序不固定，但需要開發及部署 Add-on。

### 不建議：直接在資料庫 Stored Procedure 更新 OPDN

不建議在以下程序中直接 `UPDATE OPDN`：

```sql
SBO_SP_TransactionNotification
SBO_SP_PostTransactionNotice
```

`SBO_SP_TransactionNotification` 適合檢核及阻擋交易，不適合替單據欄位賦值；直接更新 SAP B1 系統表，也可能造成資料一致性、升級或 SAP 支援問題。

如果只想在新增前檢查，可以用 `SBO_SP_TransactionNotification` 阻擋錯誤內容，例如確認 `U_typename` 與系列設定一致，但它不能安全地替你修改欄位。

結論：

- 不想開發：新增 `U_GRPO_typename`，最穩定。
- 可以接受操作一步：新增「重新計算」觸發欄位。
- 必須保留 `U_typename` 且全自動：使用 B1UP 或 UI API Add-on。
- Transaction Notification：只拿來檢查，不拿來更新。

<a id="qa5"></a>

### 5. 用SP去做操作也可能嗎

來源：[S023-Q005](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q5>)；2026-07-24

**問題**

用SP去做操作也可能嗎

**歷史回答**

技術上可以，但要分成「SP 驗證」與「SP 直接更新」兩種。SAP B1 比較安全、受支援的做法是用 SP 驗證；不建議在 SP 裡直接 `UPDATE OPDN`。

### 1. 用 Transaction Notification 驗證

可以在新增收貨採購單時檢查 `U_typename` 是否符合目前的 `Series`；不符合就阻擋新增，讓使用者重新執行 FMS。

收貨採購單的 Object Type 是 `20`：

```sql
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN
    IF EXISTS
    (
        SELECT 1
        FROM OPDN T0
        LEFT JOIN [@FORM_NUM] T1
               ON T1.U_SystemCode =
                  CONVERT(NVARCHAR(20), T0.Series)
        WHERE T0.DocEntry =
              CONVERT(INT, @list_of_cols_val_tab_del)
          AND ISNULL(T0.U_typename, '') <>
              ISNULL(T1.U_CategoryName, '')
    )
    BEGIN
        SET @error = -5001
        SET @error_message =
            N'收貨採購單的類別名稱與文件系列不符，請重新執行格式化搜尋。'
    END
END
```

優點是安全，不會直接修改 SAP 系統表；缺點是只能阻擋，不能替使用者改成正確值。

實作前也要處理以下情況：

- `[@FORM_NUM]` 找不到對應系列
- 同一個 `U_SystemCode` 有多筆資料
- `U_CategoryName` 為 NULL
- 手動建立及複製建立是否都需要檢查

### 2. 用 PostTransactionNotice 直接更新

技術上可寫成：

```sql
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN
    UPDATE T0
       SET T0.U_typename = T1.U_CategoryName
    FROM OPDN T0
    INNER JOIN [@FORM_NUM] T1
            ON T1.U_SystemCode =
               CONVERT(NVARCHAR(20), T0.Series)
    WHERE T0.DocEntry =
          CONVERT(INT, @list_of_cols_val_tab_del);
END
```

但我不建議正式環境這樣做，原因包括：

- 直接更新 SAP 系統表 `OPDN`
- SAP Client 畫面不一定立即顯示新值，可能需要重新開啟單據
- 可能沒有正常留下 SAP 的修改紀錄
- 可能繞過業務物件驗證、Add-on 事件及整合事件
- 升級、稽核或向 SAP 報修時可能遇到支援問題
- 如果其他系統在新增完成後立即讀取單據，可能產生時序問題

尤其不要在 `SBO_SP_TransactionNotification` 中直接更新當下正在新增的 `OPDN`，可能造成鎖定、交易異常或不可預期結果。

### 3. 比較安全的 SP 架構

可以讓 SP 只負責「算出正確值」：

```sql
CREATE PROCEDURE dbo.SP_GetGRPOTypeName
    @Series INT
AS
BEGIN
    SET NOCOUNT ON;

    SELECT TOP 1 U_CategoryName
    FROM [@FORM_NUM]
    WHERE U_SystemCode = CONVERT(NVARCHAR(20), @Series);
END
```

然後由 B1UP／UI API／DI API：

1. 取得收貨採購單 `Series`
2. 呼叫 SP
3. 取得 `U_CategoryName`
4. 透過 SAP API 寫入 `U_typename`
5. 再新增或更新文件

這樣查詢邏輯集中在 SP，但實際寫入仍走 SAP B1 API，是較安全的全自動方案。

另外，單純把 FMS 查詢改為呼叫 SP，仍然無法解決你的核心問題；因為問題是「複製完成後 FMS 沒有再次觸發」，不是查詢如何取得資料。

建議選擇：

- 只能使用 SQL：Transaction Notification 驗證並阻擋。
- 接受非標準做法：PostTransactionNotice 更新，但需承擔風險。
- 正式且全自動：SP 負責計算，B1UP／SDK 負責寫入。

<a id="qa6"></a>

### 6. SQL／程式碼調整與說明

來源：[S023-Q006](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q6>)；2026-07-24

**問題**

CREATE PROCEDURE dbo.SP_GetGRPOTypeName
    @Series INT
AS
BEGIN
    SET NOCOUNT ON;

    SELECT TOP 1 U_CategoryName
    FROM [@FORM_NUM]
    WHERE U_SystemCode = CONVERT(NVARCHAR(20), @Series);
END

我需要給你甚麼才可以改

**歷史回答**

可以，我可以幫你改成可直接部署的 SQL。不過先確認你希望做到哪一種：

1. 新增收貨採購單時，欄位不正確就阻擋新增（較安全）。
2. 新增完成後，SP 自動更新 `OPDN.U_typename`（可行但不建議直接更新 SAP 系統表）。
3. 由 B1UP／Add-on 呼叫 SP，取得正確值後寫入（最完整）。

請提供以下資料：

- SAP Business One 版本，例如 `10.0 FP 2305`
- `U_SystemCode` 的實際資料型別
- `U_typename` 與 `U_CategoryName` 的資料型別及長度
- `[@FORM_NUM]` 是否可能同一個 `U_SystemCode` 有多筆
- 找不到系列設定時，要填空白、保留原值，還是阻擋新增
- 你選擇上面第 1、2 或第 3 種方式
- 如果選第 1 種：貼出目前完整的 `SBO_SP_TransactionNotification`
- 如果選第 2 種：貼出目前完整的 `SBO_SP_PostTransactionNotice`

可以執行下面這支唯讀 SQL，把結果貼給我：

```sql
SELECT
    T0.TABLE_NAME,
    T0.COLUMN_NAME,
    T0.DATA_TYPE,
    T0.CHARACTER_MAXIMUM_LENGTH,
    T0.NUMERIC_PRECISION,
    T0.NUMERIC_SCALE
FROM INFORMATION_SCHEMA.COLUMNS T0
WHERE
       (T0.TABLE_NAME = '@FORM_NUM'
        AND T0.COLUMN_NAME IN ('U_SystemCode', 'U_CategoryName'))
    OR (T0.TABLE_NAME = 'OPDN'
        AND T0.COLUMN_NAME IN ('Series', 'U_typename'))
ORDER BY T0.TABLE_NAME, T0.COLUMN_NAME;
```

再提供几筆範例資料：

```sql
SELECT TOP 10
    Code,
    Name,
    U_SystemCode,
    U_CategoryName
FROM [@FORM_NUM]
ORDER BY Code;
```

如果 `U_SystemCode` 理論上必須唯一，也請執行：

```sql
SELECT
    U_SystemCode,
    COUNT(*) AS RecordCount
FROM [@FORM_NUM]
GROUP BY U_SystemCode
HAVING COUNT(*) > 1;
```

請勿提供 SQL Server 帳號、密碼或連線字串。

最重要的是：你目前貼的 `SP_GetGRPOTypeName` 只會「查出名稱」，本身不會在採購單轉收貨採購單時自動執行，也不會寫入 `U_typename`。所以還需要決定由 Transaction Notification、PostTransactionNotice，還是 Add-on/B1UP 來呼叫它。

<a id="qa7"></a>

### 7. SERIES是甚麼意思

來源：[S023-Q007](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q7>)；2026-07-24

**問題**

SERIES是甚麼意思

**歷史回答**

`Series` 是 SAP Business One 的「文件編號系列」，也就是決定單據編號如何產生的系列代碼。

例如收貨採購單畫面可能顯示：

```text
系列名稱：2026收貨採購單
文件號碼：260000123
```

資料庫裡通常是：

```text
OPDN.Series = 18
OPDN.DocNum = 260000123
```

其中：

- `OPDN.Series`：系列的內部代碼，例如 `18`
- `OPDN.DocNum`：使用者看到的文件號碼，例如 `260000123`
- `NNM1.SeriesName`：系列名稱，例如「2026收貨採購單」

可以用下面的 SQL 查看收貨採購單目前使用的系列：

```sql
SELECT
    T0.DocEntry,
    T0.DocNum,
    T0.Series,
    T1.SeriesName,
    T0.U_typename
FROM OPDN T0
LEFT JOIN NNM1 T1
       ON T1.Series = T0.Series
      AND T1.ObjectCode = '20'
ORDER BY T0.DocEntry DESC;
```

`ObjectCode = '20'` 代表收貨採購單。

你的 FMS：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPDN.Series]
```

意思是：

1. 取得目前收貨採購單的文件系列內部代碼。
2. 到 `[@FORM_NUM]` 尋找相同的 `U_SystemCode`。
3. 把對應的 `U_CategoryName` 填入 `U_typename`。

例如：

```text
OPDN.Series = 18

@FORM_NUM：
U_SystemCode = 18
U_CategoryName = 原料收貨
```

查詢結果就是：

```text
U_typename = 原料收貨
```

你可以執行下面的 SQL，確認 `[@FORM_NUM]` 存的是「系列內部代碼」還是「系列名稱」：

```sql
SELECT
    T0.U_SystemCode,
    T0.U_CategoryName,
    T1.Series,
    T1.SeriesName,
    T1.ObjectCode
FROM [@FORM_NUM] T0
LEFT JOIN NNM1 T1
       ON CONVERT(NVARCHAR(20), T1.Series) = T0.U_SystemCode
ORDER BY T0.U_SystemCode;
```

如果能正常對應到 `NNM1.Series`，表示你現在使用 `$[OPDN.Series]` 是正確的；如果 `U_SystemCode` 存的是「2026收貨採購單」這類文字，就應該改用 `NNM1.SeriesName` 進行比對。

<a id="qa8"></a>

### 8. SQL／程式碼調整與說明

來源：[S023-Q008](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q8>)；2026-07-24

**問題**

SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPOR.Series]

這個呢

**歷史回答**

這一支是用在「採購單」上的 FMS：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPOR.Series]
```

各部分意思如下：

- `OPOR`：採購單表頭資料表
- `OPOR.Series`：目前採購單的文件編號系列內碼
- `[@FORM_NUM]`：你的自訂資料表
- `U_SystemCode`：用來保存系列內碼
- `U_CategoryName`：查詢後要帶回 `U_typename` 的內容

例如目前採購單是：

```text
OPOR.Series = 12
```

而自訂表資料是：

```text
U_SystemCode = 12
U_CategoryName = 採購單
```

FMS 就會回傳：

```text
採購單
```

兩支查詢的差別只有目前所在的單據：

採購單使用：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode =
      CONVERT(NVARCHAR(20), $[OPOR.Series])
```

收貨採購單使用：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode =
      CONVERT(NVARCHAR(20), $[OPDN.Series])
```

對照如下：

| 單據 | 表頭資料表 | Object Type |
|---|---|---:|
| 採購單 | `OPOR` | `22` |
| 收貨採購單 | `OPDN` | `20` |

所以：

- 在採購單畫面掛 `OPOR.Series`
- 在收貨採購單畫面掛 `OPDN.Series`

不過，這只能決定「FMS 執行時查哪張單據的系列」。它仍無法保證從採購單轉收貨採購單後自動覆蓋舊的 `U_typename`，因為複製後 FMS 是否重新執行，取決於自動重新整理的觸發設定。

<a id="qa9"></a>

### 9. OPDN的SERIES多少

來源：[S023-Q009](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q9>)；2026-07-24

**問題**

OPDN的SERIES多少

**歷史回答**

`OPDN.Series` 沒有固定值，它是每張收貨採購單所選「編號系列」的內部代碼，例如可能是 `3`、`18`、`25`，依你們 SAP B1 的設定而定。

查詢收貨採購單可用的全部系列：

```sql
SELECT
    Series,
    SeriesName,
    BeginStr,
    NextNumber,
    EndStr,
    Locked
FROM NNM1
WHERE ObjectCode = '20'
ORDER BY Series;
```

其中：

- `ObjectCode = '20'`：收貨採購單
- `Series`：你需要拿來和 `OPDN.Series` 比對的值
- `SeriesName`：畫面顯示的系列名稱

查看最近收貨採購單實際使用的 Series：

```sql
SELECT TOP 20
    T0.DocEntry,
    T0.DocNum,
    T0.Series,
    T1.SeriesName,
    T0.U_typename
FROM OPDN T0
LEFT JOIN NNM1 T1
       ON T1.Series = T0.Series
      AND T1.ObjectCode = '20'
ORDER BY T0.DocEntry DESC;
```

查看某一張收貨採購單，例如文件號碼 `12345`：

```sql
SELECT
    DocEntry,
    DocNum,
    Series,
    U_typename
FROM OPDN
WHERE DocNum = 12345;
```

所以 `$[OPDN.Series]` 代表「目前畫面上這張收貨採購單所使用的 Series」，不是固定寫死一個數字。

<a id="qa10"></a>

### 10. 11 91 91 91 21 21 21 92 92 92 92 19 93 93 93 93 93 93 93 93 5 5 91 20 98 98 98 98 98 98 98…

來源：[S023-Q010](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q10>)；2026-07-24

**問題**

11 
91 
91 
91 
21 
21 
21 
92 
92 
92 
92 
19 
93 
93 
93 
93 
93 
93 
93 
93 
5 
5 
91 
20 
98 
98 
98 
98 
98 
98 
98 
98 
98 
91 
98 
98 
99 
99 
99 
99 
99 
99 
99 
99 
91 
99 
99 
102 
102 
102 
12 
100 
100 
100 
100 
91 
101 
13 
88 
89 
90 
56 
103 
104 
105 
91 
91 
91

**歷史回答**

這些是多張收貨採購單使用過的 `Series`，所以會重複。整理後，你目前出現的系列代碼共有：

```text
5, 11, 12, 13, 19, 20, 21, 56,
88, 89, 90, 91, 92, 93,
98, 99, 100, 101, 102, 103, 104, 105
```

要確認每個代碼對應的系列名稱，執行：

```sql
SELECT
    Series,
    SeriesName,
    BeginStr,
    NextNumber,
    EndStr,
    Locked
FROM NNM1
WHERE ObjectCode = '20'
  AND Series IN
  (
      5, 11, 12, 13, 19, 20, 21, 56,
      88, 89, 90, 91, 92, 93,
      98, 99, 100, 101, 102, 103, 104, 105
  )
ORDER BY Series;
```

如果只想查看 `OPDN` 實際使用過哪些 Series，不要顯示重複值：

```sql
SELECT DISTINCT
    T0.Series,
    T1.SeriesName
FROM OPDN T0
LEFT JOIN NNM1 T1
       ON T1.Series = T0.Series
      AND T1.ObjectCode = '20'
ORDER BY T0.Series;
```

而你的 FMS：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPDN.Series]
```

會依「目前那一張收貨採購單」取得其中一個 Series。例如目前單據的 `Series = 91`，查詢實際上相當於：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = 91;
```

接下來要確認的是 `[@FORM_NUM].U_SystemCode` 裡是否真的有 `91、92、93、98、99……` 等對應資料：

```sql
SELECT
    U_SystemCode,
    U_CategoryName
FROM [@FORM_NUM]
ORDER BY U_SystemCode;
```

<a id="qa11"></a>

### 11. 上面的數字 幫我去釣重複後 給我個別對應甚麼單據

來源：[S023-Q011](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q11>)；2026-07-24

**問題**

上面的數字 幫我去釣重複後 給我個別對應甚麼單據

**歷史回答**

去除重複後共有 22 個：

```text
5, 11, 12, 13, 19, 20, 21, 56,
88, 89, 90, 91, 92, 93,
98, 99, 100, 101, 102, 103, 104, 105
```

如果這些數字是從 `OPDN.Series` 查出來的，那它們全部都是「收貨採購單」使用過的編號系列，只是系列名稱不同。`Series` 數字本身不是單據種類。

請執行下面 SQL，即可列出每個 Series 對應的系列名稱及單據：

```sql
SELECT
    T0.Series,
    T0.SeriesName,
    T0.ObjectCode,
    CASE T0.ObjectCode
        WHEN '18' THEN N'應付發票'
        WHEN '19' THEN N'應付貸項通知單'
        WHEN '20' THEN N'收貨採購單'
        WHEN '21' THEN N'退貨'
        WHEN '22' THEN N'採購單'
        WHEN '1470000065' THEN N'採購報價單'
        WHEN '1470000113' THEN N'採購申請'
        ELSE N'其他單據'
    END AS DocumentType,
    T0.BeginStr,
    T0.NextNumber,
    T0.EndStr,
    CASE T0.Locked
        WHEN 'Y' THEN N'已鎖定'
        ELSE N'使用中'
    END AS SeriesStatus
FROM NNM1 T0
WHERE T0.Series IN
(
    5, 11, 12, 13, 19, 20, 21, 56,
    88, 89, 90, 91, 92, 93,
    98, 99, 100, 101, 102, 103, 104, 105
)
ORDER BY T0.Series;
```

如果只確認它們在 `OPDN` 中的對應，使用：

```sql
SELECT DISTINCT
    T0.Series,
    T1.SeriesName,
    N'收貨採購單' AS DocumentType
FROM OPDN T0
LEFT JOIN NNM1 T1
       ON T1.Series = T0.Series
      AND T1.ObjectCode = '20'
WHERE T0.Series IN
(
    5, 11, 12, 13, 19, 20, 21, 56,
    88, 89, 90, 91, 92, 93,
    98, 99, 100, 101, 102, 103, 104, 105
)
ORDER BY T0.Series;
```

預期結果格式會是：

| Series | SeriesName | 單據 |
|---:|---|---|
| 91 | 實際系列名稱 | 收貨採購單 |
| 92 | 實際系列名稱 | 收貨採購單 |
| 93 | 實際系列名稱 | 收貨採購單 |

實際的 `SeriesName` 必須從你們公司的 `NNM1` 查詢，單靠 Series 數字無法判斷系列名稱。

<a id="qa12"></a>

### 12. 這個應該是對應不同的單據

來源：[S023-Q012](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q12>)；2026-07-24

**問題**

這個應該是對應不同的單據

**歷史回答**

你說得對：如果這些數字是從 `NNM1.Series` 取得，而不是單獨從 `OPDN.Series` 取得，它們確實可能分別屬於不同單據。

直接執行下面 SQL，就能查出每個 Series 對應哪種單據：

```sql
SELECT
    T0.Series,
    T0.SeriesName,
    T0.ObjectCode,
    CASE T0.ObjectCode
        WHEN '13' THEN N'應收發票'
        WHEN '14' THEN N'應收貸項通知單'
        WHEN '15' THEN N'交貨'
        WHEN '16' THEN N'銷售退貨'
        WHEN '17' THEN N'銷售訂單'
        WHEN '18' THEN N'應付發票'
        WHEN '19' THEN N'應付貸項通知單'
        WHEN '20' THEN N'收貨採購單'
        WHEN '21' THEN N'採購退貨'
        WHEN '22' THEN N'採購單'
        WHEN '23' THEN N'銷售報價單'
        WHEN '24' THEN N'收款'
        WHEN '25' THEN N'存款'
        WHEN '30' THEN N'日記帳分錄'
        WHEN '46' THEN N'付款'
        WHEN '59' THEN N'收貨'
        WHEN '60' THEN N'發貨'
        WHEN '67' THEN N'庫存轉儲'
        WHEN '112' THEN N'草稿'
        WHEN '1250000001' THEN N'庫存轉儲請求'
        WHEN '1470000065' THEN N'採購報價單'
        WHEN '1470000113' THEN N'採購申請'
        ELSE N'其他單據，請依 ObjectCode 確認'
    END AS DocumentType
FROM NNM1 T0
WHERE T0.Series IN
(
    5, 11, 12, 13, 19, 20, 21, 56,
    88, 89, 90, 91, 92, 93,
    98, 99, 100, 101, 102, 103, 104, 105
)
ORDER BY T0.Series;
```

重點是：

- `Series`：編號系列的內部代碼
- `SeriesName`：你們公司設定的系列名稱
- `ObjectCode`：真正代表單據種類的代碼
- 例如 `ObjectCode = 20` 才是收貨採購單

如果查詢結果顯示：

```text
Series 91 → ObjectCode 22
Series 92 → ObjectCode 20
Series 93 → ObjectCode 18
```

則代表：

```text
91 → 採購單
92 → 收貨採購單
93 → 應付發票
```

但如果這些數字確實是透過：

```sql
SELECT Series FROM OPDN
```

查出來的，那它們全部都是收貨採購單所使用的系列；因為 `OPDN` 本身就是收貨採購單表。是否對應不同單據，要以 `NNM1.ObjectCode` 的查詢結果為準。

<a id="qa13"></a>

### 13. 不關OPDN 那些應該是OBJECT TYPE

來源：[S023-Q013](<../../原始問答/S023_釐清收貨單自定義欄位_問題一.md#q13>)；2026-07-24

**問題**

不關OPDN 那些應該是OBJECT TYPE

**歷史回答**

對，這串是 `Object Type`，不是 `Series`。前面的查詢邏輯因此要修正：你現在拿「文件系列」去比對「單據類型」，欄位用錯了。

去除重複後的對照如下：

| Object Type | SAP B1 物件／單據 |
|---:|---|
| 5 | 稅碼／營業稅群組 |
| 11 | 聯絡人 |
| 12 | 使用者 |
| 13 | 應收發票 |
| 19 | 應付貸項通知單 |
| 20 | 收貨採購單 |
| 21 | 採購退貨 |
| 56 | 關稅群組 |
| 88 | Panama – Enterada，巴拿馬本地化物件 |
| 89 | Panama – Salida，巴拿馬本地化物件 |
| 90 | Panama – Traspaso，巴拿馬本地化物件 |
| 91 | 預算情境 |
| 92 | 利率 |
| 93 | 使用者預設群組 |
| 98 | Interest／利息設定 |
| 99 | Interest Level／利息層級 |
| 100 | 資訊來源 |
| 101 | 銷售階段 |
| 102 | 缺陷原因 |
| 103 | 活動／聯絡日誌類型 |
| 104 | 活動／聯絡日誌地點 |
| 105 | Internet Service Request |

這些定義可在 SAP 官方的 [Document Types and Source Tables](https://help.sap.com/docs/SAP_BUSINESS_ONE/8cd3db2a2ab94882b22503cba89c0063/86772ca485d847489acd69a99fa2f100.html) 查到。

你的 FMS 目前寫成：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE T1.U_SystemCode = $[OPOR.Series]
```

如果 `U_SystemCode` 存的是 Object Type，這裡不能使用 `Series`，應改為 `ObjType`。

採購單的 Object Type 是 `22`：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE CONVERT(NVARCHAR(20), T1.U_SystemCode) =
      CONVERT(NVARCHAR(20), $[OPOR.ObjType])
```

也可以直接寫死：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE CONVERT(NVARCHAR(20), T1.U_SystemCode) = '22'
```

收貨採購單的 Object Type 是 `20`：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE CONVERT(NVARCHAR(20), T1.U_SystemCode) =
      CONVERT(NVARCHAR(20), $[OPDN.ObjType])
```

或直接寫：

```sql
SELECT TOP 1 T1.U_CategoryName
FROM [@FORM_NUM] T1
WHERE CONVERT(NVARCHAR(20), T1.U_SystemCode) = '20'
```

因此正確對照是：

```text
採購單 OPOR       → ObjType 22
收貨採購單 OPDN   → ObjType 20
```

你貼出的 Object Type 清單裡面有 `20`，但沒有 `22`。請先確認 `[@FORM_NUM]` 是否缺少採購單設定：

```sql
SELECT
    U_SystemCode,
    U_CategoryName
FROM [@FORM_NUM]
WHERE CONVERT(NVARCHAR(20), U_SystemCode) IN ('20', '22');
```

理想結果應該至少有：

```text
20 → 收貨採購單對應內容
22 → 採購單對應內容
```

這也很可能正是原問題的核心：原本用 `$[OPOR.Series]`／`$[OPDN.Series]`，取得的是編號系列；但 `U_SystemCode` 實際存的是 Object Type。改成 `ObjType` 後，採購單和收貨採購單才會查到各自正確的內容。只是複製後仍要確保 FMS 有重新觸發，才能覆蓋帶過來的舊值。

<a id="qa14"></a>

### 14. SQL／程式碼調整與說明

來源：[S024-Q001](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q1>)；2026-07-24

**問題**

----------------------------------------------------------------
-- Series 對應 UDO 組合驗證
-- 類別 / 異動區分 / 異動原因 必須符合 UDO 設定
----------------------------------------------------------------
IF @object_type = '20' AND @transaction_type IN ('A','U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPDN T0
        WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [@CHANGECFG] T1
                WHERE T1.U_SystemCode = CAST(T0.Series AS NVARCHAR(20))
                  AND T1.U_TypeName = T0.U_TypeName
                  AND T1.U_ChangeType = T0.U_ChangeType
                  AND T1.U_ChangeReason = T0.U_ChangeReason
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message = N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END

**歷史回答**

這段 SQL 是 SAP Business One 的 `SBO_SP_TransactionNotification` 驗證邏輯，用於新增或更新收貨採購單（`OPDN`，Object Type `20`）時，檢查：

- 單據系列 `Series`
- 類別 `U_TypeName`
- 異動區分 `U_ChangeType`
- 異動原因 `U_ChangeReason`

是否存在於 `[@CHANGECFG]` 的有效組合中。不符合時回傳錯誤 `2006`，阻止儲存。

您希望我協助哪一項：檢查邏輯、修改 SQL、處理 `NULL`、改善效能，還是套用到其他單據？

<a id="qa15"></a>

### 15. 我在想 我每個單據都有相同欄位名稱的 我可不可以用OBJECT TYPE 來區分

來源：[S024-Q002](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q2>)；2026-07-24

**問題**

我在想 我每個單據都有相同欄位名稱的 我可不可以用OBJECT TYPE 來區分

**歷史回答**

可以用 `@object_type` 區分單據，但 **不能直接用 `@object_type` 動態代表資料表名稱**。因為每個 Object Type 對應不同表，例如：

| Object Type | 單據 | 表頭資料表 |
|---|---|---|
| `18` | 應付發票 | `OPCH` |
| `20` | 收貨採購單 | `OPDN` |
| `21` | 採購退貨 | `ORPD` |
| `59` | 收貨單 | `OIGN` |
| `60` | 發貨單 | `OIGE` |

即使 UDF 欄位名稱都相同，SQL Server 的靜態 SQL 仍然必須指定實際資料表。建議在 `SBO_SP_TransactionNotification` 中依 Object Type 分流，將結果先存入變數，再共用同一段驗證。

```sql
DECLARE @Series       NVARCHAR(20)
DECLARE @TypeName     NVARCHAR(100)
DECLARE @ChangeType   NVARCHAR(100)
DECLARE @ChangeReason NVARCHAR(100)
DECLARE @DocEntry     INT

SET @DocEntry = TRY_CAST(@list_of_cols_val_tab_del AS INT)

IF @transaction_type IN ('A', 'U')
BEGIN

    -- 收貨採購單
    IF @object_type = '20'
    BEGIN
        SELECT
            @Series       = CAST(T0.Series AS NVARCHAR(20)),
            @TypeName     = T0.U_TypeName,
            @ChangeType   = T0.U_ChangeType,
            @ChangeReason = T0.U_ChangeReason
        FROM OPDN T0
        WHERE T0.DocEntry = @DocEntry
    END

    -- 採購退貨
    ELSE IF @object_type = '21'
    BEGIN
        SELECT
            @Series       = CAST(T0.Series AS NVARCHAR(20)),
            @TypeName     = T0.U_TypeName,
            @ChangeType   = T0.U_ChangeType,
            @ChangeReason = T0.U_ChangeReason
        FROM ORPD T0
        WHERE T0.DocEntry = @DocEntry
    END

    -- 收貨單
    ELSE IF @object_type = '59'
    BEGIN
        SELECT
            @Series       = CAST(T0.Series AS NVARCHAR(20)),
            @TypeName     = T0.U_TypeName,
            @ChangeType   = T0.U_ChangeType,
            @ChangeReason = T0.U_ChangeReason
        FROM OIGN T0
        WHERE T0.DocEntry = @DocEntry
    END

    -- 發貨單
    ELSE IF @object_type = '60'
    BEGIN
        SELECT
            @Series       = CAST(T0.Series AS NVARCHAR(20)),
            @TypeName     = T0.U_TypeName,
            @ChangeType   = T0.U_ChangeType,
            @ChangeReason = T0.U_ChangeReason
        FROM OIGE T0
        WHERE T0.DocEntry = @DocEntry
    END

    ------------------------------------------------------------
    -- 共用驗證
    ------------------------------------------------------------
    IF @object_type IN ('20', '21', '59', '60')
       AND NOT EXISTS (
            SELECT 1
            FROM [@CHANGECFG] T1
            WHERE T1.U_SystemCode  = @Series
              AND T1.U_TypeName    = @TypeName
              AND T1.U_ChangeType  = @ChangeType
              AND T1.U_ChangeReason = @ChangeReason
       )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END
```

這種寫法的優點是：

- 每張單據只負責從自己的資料表取值。
- UDO 組合驗證只寫一次。
- 之後新增單據，只要新增一個 `ELSE IF @object_type = ...`。
- 避免在 `TransactionNotification` 使用動態 SQL。

有一點要特別注意：SQL 的 `NULL = NULL` 不會成立。如果這三個欄位允許空值，而且 UDO 內的空值也應視為相符，需要改成：

```sql
AND ISNULL(T1.U_TypeName, '') =
    ISNULL(@TypeName, '')
AND ISNULL(T1.U_ChangeType, '') =
    ISNULL(@ChangeType, '')
AND ISNULL(T1.U_ChangeReason, '') =
    ISNULL(@ChangeReason, '')
```

若三個欄位都是必填，則維持原本的直接比較即可。

<a id="qa16"></a>

### 16. [@CHANGECFG]這個甚麼

來源：[S024-Q003](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q3>)；2026-07-24

**問題**

[@CHANGECFG]這個甚麼

**歷史回答**

`[@CHANGECFG]` 是 SAP Business One 裡的「使用者自訂資料表（UDT）」名稱。

在 SAP B1 的 SQL Server 資料庫中：

- 自訂資料表的實際 SQL 名稱會以 `@` 開頭。
- 因為名稱包含特殊字元 `@`，查詢時通常用中括號包起來。
- 因此 SQL 寫成 `[@CHANGECFG]`。
- 在 SAP B1 設定畫面看到的表格代碼通常是 `CHANGECFG`。

例如：

```sql
SELECT *
FROM [@CHANGECFG]
```

你原本的驗證是假設這張自訂表已經存在，並包含以下 UDF：

```text
U_SystemCode
U_TypeName
U_ChangeType
U_ChangeReason
```

它的用途是保存「允許的組合」，例如：

| U_SystemCode | U_TypeName | U_ChangeType | U_ChangeReason |
|---|---|---|---|
| 83 | 原料 | 入庫 | 採購入庫 |
| 83 | 成品 | 入庫 | 生產入庫 |
| 91 | 原料 | 出庫 | 退料 |

驗證的意思就是：

> 目前單據的 Series＋類別＋異動區分＋異動原因，必須在這張設定表中找到完全相同的一筆。

不過有個關鍵點：`[@CHANGECFG]` 是範例或自訂名稱，**不是 SAP B1 的標準資料表**。如果你的系統沒有建立這張表，這段 SQL 會出現「Invalid object name」錯誤。

你必須把它換成你實際存放設定資料的 UDT 表名，例如：

```sql
FROM [@你的UDT代碼]
```

可以用下面的 SQL 查看目前公司資料庫內有哪些自訂表：

```sql
SELECT
    TableName,
    Descr,
    ObjectType
FROM OUTB
ORDER BY TableName
```

找到真正的表格代碼後，例如 `Z_CHANGE_CFG`，SQL 實際名稱就是：

```sql
[@Z_CHANGE_CFG]
```

<a id="qa17"></a>

### 17. [dbo].[@FORM_NUM] T0 改這個

來源：[S024-Q004](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q4>)；2026-07-24

**問題**

[dbo].[@FORM_NUM]  T0 改這個

**歷史回答**

可以，將原本的：

```sql
FROM [@CHANGECFG] T1
```

改成：

```sql
FROM [dbo].[@FORM_NUM] T1
```

完整的共用驗證如下：

```sql
IF @object_type IN ('20', '21', '59', '60')
   AND NOT EXISTS (
        SELECT 1
        FROM [dbo].[@FORM_NUM] T1
        WHERE T1.U_SystemCode   = @Series
          AND T1.U_TypeName     = @TypeName
          AND T1.U_ChangeType   = @ChangeType
          AND T1.U_ChangeReason = @ChangeReason
   )
BEGIN
    SET @error = 2006
    SET @error_message =
        N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
END
```

如果套用到最初的收貨採購單版本：

```sql
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [dbo].[@FORM_NUM] T1
                WHERE T1.U_SystemCode =
                      CAST(T0.Series AS NVARCHAR(20))
                  AND T1.U_TypeName =
                      T0.U_TypeName
                  AND T1.U_ChangeType =
                      T0.U_ChangeType
                  AND T1.U_ChangeReason =
                      T0.U_ChangeReason
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END
```

前提是 `[@FORM_NUM]` 內確實存在以下欄位：

```text
U_SystemCode
U_TypeName
U_ChangeType
U_ChangeReason
```

若實際欄位名稱不同，也需要一併替換。

<a id="qa18"></a>

### 18. 給我完整的

來源：[S024-Q005](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q5>)；2026-07-24

**問題**

給我完整的

**歷史回答**

以下是完整版本，支援：

- `20`：收貨採購單 `OPDN`
- `21`：採購退貨 `ORPD`
- `59`：收貨單 `OIGN`
- `60`：發貨單 `OIGE`
- 設定表改為 `[dbo].[@FORM_NUM]`

請放入 `SBO_SP_TransactionNotification` 中，並置於程序最後的 `SELECT @error, @error_message` 之前。

```sql
----------------------------------------------------------------
-- Series 對應 UDO 組合驗證
-- 類別 / 異動區分 / 異動原因 必須符合 @FORM_NUM 設定
----------------------------------------------------------------
IF @transaction_type IN ('A', 'U')
   AND @object_type IN ('20', '21', '59', '60')
BEGIN

    DECLARE @CheckDocEntry     INT
    DECLARE @CheckSeries       NVARCHAR(20)
    DECLARE @CheckTypeName     NVARCHAR(254)
    DECLARE @CheckChangeType   NVARCHAR(254)
    DECLARE @CheckChangeReason NVARCHAR(254)

    SET @CheckDocEntry =
        TRY_CAST(@list_of_cols_val_tab_del AS INT)

    ------------------------------------------------------------
    -- Object Type 20：收貨採購單
    ------------------------------------------------------------
    IF @object_type = '20'
    BEGIN
        SELECT
            @CheckSeries       = CAST(T0.Series AS NVARCHAR(20)),
            @CheckTypeName     = T0.U_TypeName,
            @CheckChangeType   = T0.U_ChangeType,
            @CheckChangeReason = T0.U_ChangeReason
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry = @CheckDocEntry
    END

    ------------------------------------------------------------
    -- Object Type 21：採購退貨
    ------------------------------------------------------------
    ELSE IF @object_type = '21'
    BEGIN
        SELECT
            @CheckSeries       = CAST(T0.Series AS NVARCHAR(20)),
            @CheckTypeName     = T0.U_TypeName,
            @CheckChangeType   = T0.U_ChangeType,
            @CheckChangeReason = T0.U_ChangeReason
        FROM [dbo].[ORPD] T0
        WHERE T0.DocEntry = @CheckDocEntry
    END

    ------------------------------------------------------------
    -- Object Type 59：收貨單
    ------------------------------------------------------------
    ELSE IF @object_type = '59'
    BEGIN
        SELECT
            @CheckSeries       = CAST(T0.Series AS NVARCHAR(20)),
            @CheckTypeName     = T0.U_TypeName,
            @CheckChangeType   = T0.U_ChangeType,
            @CheckChangeReason = T0.U_ChangeReason
        FROM [dbo].[OIGN] T0
        WHERE T0.DocEntry = @CheckDocEntry
    END

    ------------------------------------------------------------
    -- Object Type 60：發貨單
    ------------------------------------------------------------
    ELSE IF @object_type = '60'
    BEGIN
        SELECT
            @CheckSeries       = CAST(T0.Series AS NVARCHAR(20)),
            @CheckTypeName     = T0.U_TypeName,
            @CheckChangeType   = T0.U_ChangeType,
            @CheckChangeReason = T0.U_ChangeReason
        FROM [dbo].[OIGE] T0
        WHERE T0.DocEntry = @CheckDocEntry
    END

    ------------------------------------------------------------
    -- 檢查 Series + 類別 + 異動區分 + 異動原因
    -- 是否存在於 @FORM_NUM
    ------------------------------------------------------------
    IF @CheckDocEntry IS NOT NULL
       AND NOT EXISTS (
            SELECT 1
            FROM [dbo].[@FORM_NUM] T1
            WHERE ISNULL(T1.U_SystemCode, '') =
                  ISNULL(@CheckSeries, '')
              AND ISNULL(T1.U_TypeName, '') =
                  ISNULL(@CheckTypeName, '')
              AND ISNULL(T1.U_ChangeType, '') =
                  ISNULL(@CheckChangeType, '')
              AND ISNULL(T1.U_ChangeReason, '') =
                  ISNULL(@CheckChangeReason, '')
       )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END
```

程序結尾仍須保留 SAP B1 原本的回傳：

```sql
SELECT @error, @error_message
```

此版本假設所有單據與 `[@FORM_NUM]` 都使用以下欄位：

```text
U_TypeName
U_ChangeType
U_ChangeReason
```

而 `[@FORM_NUM]` 的系列欄位是：

```text
U_SystemCode
```

另外要確認 `U_SystemCode` 存放的是單據的 `Series` 数字代碼，而不是系列名稱。如果它存的是系列名稱，就要再連接 `NNM1` 取得 `SeriesName` 後比對。

<a id="qa19"></a>

### 19. [2026/7/24 下午 04:14] Nick: [dbo].[@FORM_NUM] T0 [2026/7/24 下午 04:18] Nick: Msg 207, Level …

來源：[S024-Q006](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q6>)；2026-07-24

**問題**

[2026/7/24 下午 04:14] Nick: [dbo].[@FORM_NUM]  T0
[2026/7/24 下午 04:18] Nick: Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1633 [Batch Start Line 7]
Invalid column name 'U_TypeName'.

**歷史回答**

這個錯誤代表：程式中某一張表沒有 `U_TypeName` 欄位。最可能是 `[dbo].[@FORM_NUM]` 的實際 UDF 欄位名稱不同。

先執行以下 SQL，查出 `[@FORM_NUM]` 的實際欄位：

```sql
SELECT
    C.COLUMN_NAME,
    C.DATA_TYPE,
    C.CHARACTER_MAXIMUM_LENGTH
FROM INFORMATION_SCHEMA.COLUMNS C
WHERE C.TABLE_SCHEMA = 'dbo'
  AND C.TABLE_NAME = '@FORM_NUM'
ORDER BY C.ORDINAL_POSITION
```

也可以查看 SAP B1 登記的 UDF：

```sql
SELECT
    T0.TableID,
    T0.AliasID,
    'U_' + T0.AliasID AS SQLColumnName,
    T0.Descr
FROM CUFD T0
WHERE T0.TableID = '@FORM_NUM'
ORDER BY T0.FieldID
```

同時檢查四張單據表是否都有相同欄位：

```sql
SELECT
    C.TABLE_NAME,
    C.COLUMN_NAME
FROM INFORMATION_SCHEMA.COLUMNS C
WHERE C.TABLE_SCHEMA = 'dbo'
  AND C.TABLE_NAME IN ('@FORM_NUM', 'OPDN', 'ORPD', 'OIGN', 'OIGE')
  AND (
        C.COLUMN_NAME LIKE '%TypeName%'
     OR C.COLUMN_NAME LIKE '%ChangeType%'
     OR C.COLUMN_NAME LIKE '%ChangeReason%'
     OR C.COLUMN_NAME LIKE '%SystemCode%'
  )
ORDER BY C.TABLE_NAME, C.ORDINAL_POSITION
```

錯誤原因不是資料內容，而是欄位名稱不存在。舉例來說，如果查詢結果是：

```text
U_TYPE
U_CHANGE
U_REASON
U_SYSTEM
```

原本程式就必須改成：

```sql
FROM [dbo].[@FORM_NUM] T1
WHERE ISNULL(T1.U_SYSTEM, '') =
      ISNULL(@CheckSeries, '')
  AND ISNULL(T1.U_TYPE, '') =
      ISNULL(@CheckTypeName, '')
  AND ISNULL(T1.U_CHANGE, '') =
      ISNULL(@CheckChangeType, '')
  AND ISNULL(T1.U_REASON, '') =
      ISNULL(@CheckChangeReason, '')
```

另外，`Line 1633` 可以幫你確認是哪一個 `U_TypeName` 出錯；可能是：

```sql
T1.U_TypeName
```

也可能是：

```sql
T0.U_TypeName
```

請把第一段查詢得到的 `[@FORM_NUM]` 欄位清單，以及四張單據表的實際 UDF 名稱貼給我，我就能將完整程式改成可直接執行的版本。

<a id="qa20"></a>

### 20. 沒有卡住欸 可以先做小版本給我嗎 我只要收貨採購單

來源：[S024-Q007](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q7>)；2026-07-24

**問題**

沒有卡住欸 可以先做小版本給我嗎 我只要收貨採購單

**歷史回答**

可以，先做「收貨採購單（Object Type `20`）」的小版本，只驗證單據的 `Series` 是否存在 `[dbo].[@FORM_NUM]` 的 `U_SystemCode`，先確認卡控機制能正常阻擋。

把下面程式放在 `SBO_SP_TransactionNotification` 最後的：

```sql
SELECT @error, @error_message
```

之前。

```sql
----------------------------------------------------------------
-- 收貨採購單：Series 對應 @FORM_NUM 驗證
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [dbo].[@FORM_NUM] T1
                WHERE CAST(T1.U_SystemCode AS NVARCHAR(20)) =
                      CAST(T0.Series AS NVARCHAR(20))
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'此收貨採購單的系列別未設定於 FORM_NUM，請重新確認。'
    END

END
```

先用下面 SQL 確認設定表裡有沒有目前的系列：

```sql
SELECT
    T0.Code,
    T0.Name,
    T0.U_SystemCode
FROM [dbo].[@FORM_NUM] T0
```

測試方式：

1. 找一個沒有出現在 `U_SystemCode` 的收貨採購單系列。
2. 新增或更新收貨採購單。
3. 系統應顯示錯誤 `2006`，並阻止儲存。

這個版本暫時不使用 `U_TypeName`，因此不會再出現：

```text
Invalid column name 'U_TypeName'
```

確認 Series 卡控有效後，再依 `[@FORM_NUM]` 真正存在的欄位名稱，逐一加入類別、異動區分和異動原因。

<a id="qa21"></a>

### 21. 要有U_TYPENAME因為我主要就是要這個欄位要是對的

來源：[S024-Q008](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q8>)；2026-07-24

**問題**

要有U_TYPENAME因為我主要就是要這個欄位要是對的

**歷史回答**

可以保留收貨採購單的 `OPDN.U_TypeName`。目前錯誤代表 `[@FORM_NUM]` 沒有 `U_TypeName`，因此先假設設定表是用標準欄位 `Name` 保存類別名稱。

```sql
----------------------------------------------------------------
-- 收貨採購單：Series + 類別名稱驗證
-- OPDN.U_TypeName 對應 @FORM_NUM.Name
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [dbo].[@FORM_NUM] T1
                WHERE CAST(T1.U_SystemCode AS NVARCHAR(20)) =
                      CAST(T0.Series AS NVARCHAR(20))
                  AND ISNULL(T1.Name, '') =
                      ISNULL(T0.U_TypeName, '')
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'類別名稱與該收貨採購單系列別的設定不符，請重新確認。'
    END

END
```

對應關係為：

```text
OPDN.Series       → @FORM_NUM.U_SystemCode
OPDN.U_TypeName   → @FORM_NUM.Name
```

請先執行這段確認 `Name` 是否真的存放類別名稱：

```sql
SELECT
    T0.Code,
    T0.Name,
    T0.U_SystemCode
FROM [dbo].[@FORM_NUM] T0
ORDER BY T0.Code
```

如果類別名稱不是放在 `Name`，就要把程式中的：

```sql
T1.Name
```

換成 `[@FORM_NUM]` 實際存放類別名稱的欄位。`OPDN` 這邊仍然保留：

```sql
T0.U_TypeName
```

<a id="qa22"></a>

### 22. 還是沒有卡住欸 可以成功新增

來源：[S024-Q009](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q9>)；2026-07-24

**問題**

還是沒有卡住欸 可以成功新增

**歷史回答**

那目前問題很可能不是比對條件，而是：

1. 程式放錯公司資料庫。
2. 程式沒有放在最後的 `SELECT @error, @error_message` 前面。
3. 後方程式又把 `@error` 重設成 `0`。
4. 修改後沒有成功執行 `ALTER PROCEDURE`。

先用「強制阻擋」確認 `Object Type 20` 是否有進入。

```sql
----------------------------------------------------------------
-- 測試：強制阻擋所有收貨採購單新增
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type = 'A'
BEGIN
    SET @error = 2999
    SET @error_message = N'測試成功：已進入收貨採購單卡控。'
END
```

程序最下面必須是：

```sql
SELECT @error, @error_message
```

而且在卡控程式後面，不能再出現：

```sql
SET @error = 0
SET @error_message = N'Ok'
```

正確位置如下：

```sql
ALTER PROCEDURE [dbo].[SBO_SP_TransactionNotification]
    @object_type NVARCHAR(30),
    @transaction_type NCHAR(1),
    @num_of_cols_in_key INT,
    @list_of_key_cols_tab_del NVARCHAR(255),
    @list_of_cols_val_tab_del NVARCHAR(255)
AS
BEGIN

    DECLARE @error INT
    DECLARE @error_message NVARCHAR(200)

    SET @error = 0
    SET @error_message = N'Ok'

    -- 其他既有卡控……

    ------------------------------------------------------------
    -- 測試：強制阻擋收貨採購單
    ------------------------------------------------------------
    IF @object_type = '20'
       AND @transaction_type = 'A'
    BEGIN
        SET @error = 2999
        SET @error_message = N'測試成功：已進入收貨採購單卡控。'
    END

    -- 這兩行一定要在最下面
    SELECT @error, @error_message

END
```

如果強制測試可以擋住，再換成正式版本：

```sql
----------------------------------------------------------------
-- 收貨採購單：Series + U_TypeName 組合驗證
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF NOT EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        INNER JOIN [dbo].[@FORM_NUM] T1
            ON CAST(T1.U_SystemCode AS NVARCHAR(20)) =
               CAST(T0.Series AS NVARCHAR(20))
           AND ISNULL(LTRIM(RTRIM(T1.Name)), '') =
               ISNULL(LTRIM(RTRIM(T0.U_TypeName)), '')
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'類別名稱與該收貨採購單系列別設定不符，請重新確認。'
    END

END
```

如果連 `2999` 強制測試都擋不住，就能確定不是 SQL 比對問題，而是程序未套用、資料庫錯誤，或錯誤值在後面被覆蓋。先測這一段最準。

<a id="qa23"></a>

### 23. 可以卡住

來源：[S024-Q010](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q10>)；2026-07-24

**問題**

可以卡住

**歷史回答**

很好，代表 `Object Type = 20`、程序位置和错误回传都正常。先移除 `2999` 强制测试，换成下面正式版本。

这个版本会分别检查：

1. `U_TypeName` 不得为空。
2. `Series + U_TypeName` 必须存在于 `[@FORM_NUM]`。
3. `OPDN.U_TypeName` 对应 `[@FORM_NUM].Name`。

```sql
----------------------------------------------------------------
-- 收货采购单：Series + U_TypeName 验证
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    ------------------------------------------------------------
    -- 1. 类别名称不得为空
    ------------------------------------------------------------
    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)
          AND NULLIF(LTRIM(RTRIM(T0.U_TypeName)), '') IS NULL
    )
    BEGIN
        SET @error = 2005
        SET @error_message = N'类别名称不可为空白，请重新选择。'
    END

    ------------------------------------------------------------
    -- 2. Series + 类别名称必须符合 FORM_NUM 设置
    ------------------------------------------------------------
    ELSE IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [dbo].[@FORM_NUM] T1
                WHERE LTRIM(RTRIM(
                          CAST(T1.U_SystemCode AS NVARCHAR(20))
                      )) =
                      LTRIM(RTRIM(
                          CAST(T0.Series AS NVARCHAR(20))
                      ))
                  AND ISNULL(LTRIM(RTRIM(T1.Name)), '') =
                      ISNULL(LTRIM(RTRIM(T0.U_TypeName)), '')
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'类别名称与该收货采购单系列别设置不符，请重新确认。'
    END

END
```

程序最后保留：

```sql
SELECT @error, @error_message
```

如果故意选择错误的 `U_TypeName` 仍能新增，表示 `[@FORM_NUM].Name` 不是类别名称，或者表内确实存在这个组合。可以用这段查询确认实际匹配结果，把 `123` 换成测试单据的 `DocEntry`：

```sql
DECLARE @DocEntry INT = 123

SELECT
    T0.DocEntry,
    T0.Series,
    T0.U_TypeName,
    T1.Code       AS ConfigCode,
    T1.Name       AS ConfigTypeName,
    T1.U_SystemCode,
    CASE
        WHEN T1.Code IS NULL THEN N'不符合'
        ELSE N'符合'
    END AS CheckResult
FROM [dbo].[OPDN] T0
LEFT JOIN [dbo].[@FORM_NUM] T1
    ON LTRIM(RTRIM(CAST(T1.U_SystemCode AS NVARCHAR(20)))) =
       LTRIM(RTRIM(CAST(T0.Series AS NVARCHAR(20))))
   AND ISNULL(LTRIM(RTRIM(T1.Name)), '') =
       ISNULL(LTRIM(RTRIM(T0.U_TypeName)), '')
WHERE T0.DocEntry = @DocEntry
```

关键是确认这个对应关系是否正确：

```text
OPDN.U_TypeName → @FORM_NUM.Name
```

如果 `[@FORM_NUM]` 的类别其实放在另一个 `U_` 栏位，需要把 `T1.Name` 换成那个实际栏位。

<a id="qa24"></a>

### 24. 這個還是沒有

來源：[S024-Q011](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q11>)；2026-07-24

**問題**

這個還是沒有

**歷史回答**

既然强制 `2999` 可以挡住，但加入 `OPDN` 查询后没有挡住，最可能是：

> `@list_of_cols_val_tab_del` 没有成功找到对应的 `OPDN.DocEntry`。

先用下面的诊断版，它会强制挡住，并显示系统传入的 DocEntry，以及从 `OPDN` 读取到的 `Series`、`U_TypeName`。

请暂时替换正式卡控：

```sql
----------------------------------------------------------------
-- 诊断：读取收货采购单资料
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    DECLARE @TestDocEntry INT
    DECLARE @TestSeries NVARCHAR(20)
    DECLARE @TestTypeName NVARCHAR(100)

    SET @TestDocEntry =
        TRY_CAST(
            LTRIM(RTRIM(@list_of_cols_val_tab_del))
            AS INT
        )

    SELECT
        @TestSeries =
            CAST(T0.Series AS NVARCHAR(20)),
        @TestTypeName =
            T0.U_TypeName
    FROM [dbo].[OPDN] T0
    WHERE T0.DocEntry = @TestDocEntry

    SET @error = 2998
    SET @error_message =
        N'DocEntry=' + ISNULL(CAST(@TestDocEntry AS NVARCHAR(20)), N'NULL')
        + N'，Series=' + ISNULL(@TestSeries, N'NULL')
        + N'，TypeName=' + ISNULL(@TestTypeName, N'NULL')

END
```

新增收货采购单时，应该出现类似：

```text
DocEntry=123，Series=83，TypeName=原料
```

如果显示：

```text
DocEntry=123，Series=NULL，TypeName=NULL
```

表示没有从 `OPDN` 找到单据。若能正确显示资料，则可直接改成以下正式版：

```sql
----------------------------------------------------------------
-- 收货采购单：Series + U_TypeName 验证
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    DECLARE @DocEntry20 INT
    DECLARE @Series20 NVARCHAR(20)
    DECLARE @TypeName20 NVARCHAR(100)

    SET @DocEntry20 =
        TRY_CAST(
            LTRIM(RTRIM(@list_of_cols_val_tab_del))
            AS INT
        )

    SELECT
        @Series20 =
            CAST(T0.Series AS NVARCHAR(20)),
        @TypeName20 =
            LTRIM(RTRIM(T0.U_TypeName))
    FROM [dbo].[OPDN] T0
    WHERE T0.DocEntry = @DocEntry20

    -- 类别名称不可为空
    IF NULLIF(@TypeName20, '') IS NULL
    BEGIN
        SET @error = 2005
        SET @error_message = N'类别名称不可为空白，请重新选择。'
    END

    -- Series + 类别名称必须存在于 FORM_NUM
    ELSE IF NOT EXISTS (
        SELECT 1
        FROM [dbo].[@FORM_NUM] T1
        WHERE LTRIM(RTRIM(
                  CAST(T1.U_SystemCode AS NVARCHAR(20))
              )) = @Series20
          AND LTRIM(RTRIM(T1.Name)) = @TypeName20
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'类别名称与该收货采购单系列别设置不符。'
    END

END
```

先跑诊断版；画面显示的 `DocEntry、Series、TypeName` 三个值，就是判断问题所在最直接的依据。

<a id="qa25"></a>

### 25. SQL／程式碼調整與說明

來源：[S024-Q012](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q12>)；2026-07-24

**問題**

----------------------------------------------------------------
-- Series 對應 UDO 組合驗證
-- 類別 / 異動區分 / 異動原因 必須符合 UDO 設定
----------------------------------------------------------------
IF @object_type = '20' AND @transaction_type IN ('A','U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPDN T0
        WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [@CHANGECFG] T1
                WHERE T1.U_SystemCode = CAST(T0.Series AS NVARCHAR(20))
                  AND T1.U_TypeName = T0.U_TypeName
                  AND T1.U_ChangeType = T0.U_ChangeType
                  AND T1.U_ChangeReason = T0.U_ChangeReason
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message = N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END

這個也卡不住

**歷史回答**

这段程式只有在「找不到对应组合」时才会挡。如果 `[@CHANGECFG]` 已存在完全相同的组合，成功新增是正常的。

不过你实际使用的是 `[@FORM_NUM]`，而且它没有 `U_TypeName`，所以不能直接照旧版写。现在先做一个诊断版本：它会强制挡住，并显示实际读取值及符合笔数。

假设 `[@FORM_NUM].Name` 是类别名称：

```sql
----------------------------------------------------------------
-- 诊断：检查 Series + U_TypeName 的匹配结果
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    DECLARE @DbgDocEntry INT
    DECLARE @DbgSeries NVARCHAR(20)
    DECLARE @DbgTypeName NVARCHAR(100)
    DECLARE @DbgMatchCount INT

    SET @DbgDocEntry =
        TRY_CAST(LTRIM(RTRIM(@list_of_cols_val_tab_del)) AS INT)

    SELECT
        @DbgSeries =
            LTRIM(RTRIM(CAST(T0.Series AS NVARCHAR(20)))),
        @DbgTypeName =
            LTRIM(RTRIM(T0.U_TypeName))
    FROM [dbo].[OPDN] T0
    WHERE T0.DocEntry = @DbgDocEntry

    SELECT
        @DbgMatchCount = COUNT(*)
    FROM [dbo].[@FORM_NUM] T1
    WHERE LTRIM(RTRIM(
              CAST(T1.U_SystemCode AS NVARCHAR(20))
          )) = @DbgSeries
      AND LTRIM(RTRIM(T1.Name)) = @DbgTypeName

    SET @error = 2997
    SET @error_message =
        N'Series=' + ISNULL(@DbgSeries, N'NULL')
        + N'，TypeName=' + ISNULL(@DbgTypeName, N'NULL')
        + N'，符合笔数='
        + CAST(ISNULL(@DbgMatchCount, 0) AS NVARCHAR(10))

END
```

新增时可能看到：

```text
Series=83，TypeName=原料，符合笔数=1
```

这表示设定表确实有相同组合，所以原本逻辑不会挡。

如果显示：

```text
Series=83，TypeName=原料，符合笔数=0
```

表示应该阻挡，正式版本如下：

```sql
----------------------------------------------------------------
-- 收货采购单：Series + U_TypeName 验证
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    DECLARE @DocEntry20 INT
    DECLARE @Series20 NVARCHAR(20)
    DECLARE @TypeName20 NVARCHAR(100)

    SET @DocEntry20 =
        TRY_CAST(LTRIM(RTRIM(@list_of_cols_val_tab_del)) AS INT)

    SELECT
        @Series20 =
            LTRIM(RTRIM(CAST(T0.Series AS NVARCHAR(20)))),
        @TypeName20 =
            LTRIM(RTRIM(T0.U_TypeName))
    FROM [dbo].[OPDN] T0
    WHERE T0.DocEntry = @DocEntry20

    IF NULLIF(@TypeName20, '') IS NULL
    BEGIN
        SET @error = 2005
        SET @error_message = N'类别名称不可为空白，请重新选择。'
    END

    ELSE IF NOT EXISTS (
        SELECT 1
        FROM [dbo].[@FORM_NUM] T1
        WHERE LTRIM(RTRIM(
                  CAST(T1.U_SystemCode AS NVARCHAR(20))
              )) = @Series20
          AND LTRIM(RTRIM(T1.Name)) = @TypeName20
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'类别名称与该收货采购单系列别设置不符，请重新确认。'
    END

END
```

先使用诊断版，画面上的「符合笔数」最重要：

- `0`：设定不符合，正式版应阻挡。
- `1` 以上：设定表内已有相同组合，因此允许新增是正确结果。
- `Series=NULL` 或 `TypeName=NULL`：没有正确读取 `OPDN` 栏位，需要检查字段位置或栏位名称。

<a id="qa26"></a>

### 26. 我再重新說明一下我的需求 採購單轉成收貨採購單時。 會把U_Typename的內容一起帶過來。 但我實際收貨採購單有自己的內容，但不會更新。 我有一個表格，有紀錄關於序列號不同 叫…

來源：[S024-Q013](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q13>)；2026-07-24

**問題**

我再重新說明一下我的需求

採購單轉成收貨採購單時。

會把U_Typename的內容一起帶過來。
但我實際收貨採購單有自己的內容，但不會更新。
我有一個表格，有紀錄關於序列號不同 叫做 U_SystemCode 他會記錄不同單據有不同的系列號。

所以我的想法是，既然會有的話 可以透過驗證後 利用屬於收貨採購單的series要等於 U_Typename的系統碼

**歷史回答**

了解，你要验证的核心其实只有两个值：

```text
收货采购单 OPDN.Series
=
该 U_TypeName 在 @FORM_NUM 中对应的 U_SystemCode
```

采购订单转成收货采购单时，虽然 `U_TypeName` 会沿用，但如果收货采购单选择的系列不同，就必须检查该类别对应的系统码是否与收货采购单 `Series` 相同；不相同就阻止新增。

假设：

```text
OPDN.U_TypeName → @FORM_NUM.Name
OPDN.Series     → @FORM_NUM.U_SystemCode
```

完整卡控如下：

```sql
----------------------------------------------------------------
-- 收货采购单 Series 与 U_TypeName 对应系统码验证
--
-- OPDN.U_TypeName 对应 @FORM_NUM.Name
-- OPDN.Series     对应 @FORM_NUM.U_SystemCode
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    DECLARE @GRPODocEntry INT
    DECLARE @GRPOSeries NVARCHAR(20)
    DECLARE @GRPOTypeName NVARCHAR(100)
    DECLARE @TypeSystemCode NVARCHAR(20)

    SET @GRPODocEntry =
        TRY_CAST(
            LTRIM(RTRIM(@list_of_cols_val_tab_del))
            AS INT
        )

    ------------------------------------------------------------
    -- 取得收货采购单资料
    ------------------------------------------------------------
    SELECT
        @GRPOSeries =
            LTRIM(RTRIM(CAST(T0.Series AS NVARCHAR(20)))),
        @GRPOTypeName =
            LTRIM(RTRIM(T0.U_TypeName))
    FROM [dbo].[OPDN] T0
    WHERE T0.DocEntry = @GRPODocEntry

    ------------------------------------------------------------
    -- 根据 U_TypeName 取得该类别设置的系统码
    ------------------------------------------------------------
    SELECT TOP 1
        @TypeSystemCode =
            LTRIM(RTRIM(CAST(T1.U_SystemCode AS NVARCHAR(20))))
    FROM [dbo].[@FORM_NUM] T1
    WHERE LTRIM(RTRIM(T1.Name)) = @GRPOTypeName

    ------------------------------------------------------------
    -- U_TypeName 不得为空
    ------------------------------------------------------------
    IF NULLIF(@GRPOTypeName, '') IS NULL
    BEGIN
        SET @error = 2005
        SET @error_message =
            N'收货采购单的类别名称不可为空，请重新选择。'
    END

    ------------------------------------------------------------
    -- U_TypeName 必须存在于 FORM_NUM
    ------------------------------------------------------------
    ELSE IF @TypeSystemCode IS NULL
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'类别名称「' + @GRPOTypeName
            + N'」未设置对应的收货采购单系列号。'
    END

    ------------------------------------------------------------
    -- 收货采购单 Series 必须等于类别设置的 U_SystemCode
    ------------------------------------------------------------
    ELSE IF @GRPOSeries <> @TypeSystemCode
    BEGIN
        SET @error = 2007
        SET @error_message =
            N'类别「' + @GRPOTypeName
            + N'」应使用系列号 ' + @TypeSystemCode
            + N'，目前系列号为 ' + @GRPOSeries
            + N'，请重新确认。'
    END

END
```

例如设定表内容：

| Code | Name | U_SystemCode |
|---|---|---:|
| 001 | 原料采购 | 83 |
| 002 | 设备采购 | 91 |

收货采购单如果是：

```text
U_TypeName = 原料采购
Series     = 83
```

验证通过。

如果采购订单带过来：

```text
U_TypeName = 原料采购
```

但是收货采购单是：

```text
Series = 91
```

系统就会阻止新增，并提示应使用系列号 `83`。

有一个关键点需要确认：`OPDN.U_TypeName` 实际保存的是 `[@FORM_NUM].Name` 还是 `Code`。执行：

```sql
SELECT TOP 20
    T0.DocEntry,
    T0.U_TypeName
FROM [dbo].[OPDN] T0
WHERE ISNULL(T0.U_TypeName, '') <> ''
ORDER BY T0.DocEntry DESC
```

再对照：

```sql
SELECT
    T0.Code,
    T0.Name,
    T0.U_SystemCode
FROM [dbo].[@FORM_NUM] T0
ORDER BY T0.Code
```

如果 `OPDN.U_TypeName` 保存的是 `Code`，只要把正式程式中的：

```sql
WHERE LTRIM(RTRIM(T1.Name)) = @GRPOTypeName
```

改成：

```sql
WHERE LTRIM(RTRIM(T1.Code)) = @GRPOTypeName
```

即可。這段只負責驗證，不會自動修改 `U_TypeName`；若不符合，使用者需要在收貨採購單重新選擇正確類別。

<a id="qa27"></a>

### 27. 這個紀錄的表格叫 [@釐清收貨單自定義欄位_問題一](thread://019f92ca-e871-7292-b492-85cb3c1d1b42)

來源：[S024-Q014](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q14>)；2026-07-24

**問題**

〔此提問引用其他聊天，重複背景已省略；原聊天另見來源索引。〕

## My request for Codex:
這個紀錄的表格叫 [@釐清收貨單自定義欄位_問題一](thread://019f92ca-e871-7292-b492-85cb3c1d1b42)

**歷史回答**

了解，结合这份记录后，问题已经明确了：

- `[@FORM_NUM].U_SystemCode` 存的是 **Object Type**
- 不是文件的 `Series`
- 收货采购单的 Object Type 是 `20`
- 正确类别存于 `[@FORM_NUM].U_CategoryName`
- 需要验证 `OPDN.U_typename` 是否等于 Object Type `20` 对应的类别

所以之前拿 `OPDN.Series` 比较 `U_SystemCode` 的逻辑不正确。

请使用这个小版本：

```sql
----------------------------------------------------------------
-- 收货采购单 U_typename 验证
--
-- 收货采购单 Object Type = 20
-- @FORM_NUM.U_SystemCode = Object Type
-- @FORM_NUM.U_CategoryName = 正确类别名称
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)

          -- 类别为空，或者类别与收货采购单设置不符
          AND (
                 NULLIF(LTRIM(RTRIM(T0.U_typename)), '') IS NULL

                 OR NOT EXISTS (
                    SELECT 1
                    FROM [dbo].[@FORM_NUM] T1
                    WHERE CONVERT(NVARCHAR(20), T1.U_SystemCode) =
                          @object_type
                      AND ISNULL(LTRIM(RTRIM(T1.U_CategoryName)), '') =
                          ISNULL(LTRIM(RTRIM(T0.U_typename)), '')
                 )
              )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'收货采购单的类别名称不正确，请重新执行格式化搜索。'
    END

END
```

实际验证关系是：

```text
@object_type = 20
        ↓
@FORM_NUM.U_SystemCode = 20
        ↓
取得 @FORM_NUM.U_CategoryName
        ↓
检查是否等于 OPDN.U_typename
```

例如设定表为：

```text
U_SystemCode   = 20
U_CategoryName = 收货采购单
```

如果从采购单带过来的内容是：

```text
OPDN.U_typename = 采购单
```

因为：

```text
采购单 <> 收货采购单
```

就会被阻止新增。

也可以先执行下面查询确认 `20` 的设置：

```sql
SELECT
    T0.Code,
    T0.Name,
    T0.U_SystemCode,
    T0.U_CategoryName
FROM [dbo].[@FORM_NUM] T0
WHERE CONVERT(NVARCHAR(20), T0.U_SystemCode) = '20'
```

应该只查到一笔收货采购单设置。如果查到多笔不同的 `U_CategoryName`，由于验证会接受其中任意一个符合值，建议先清理重复设置。

<a id="qa28"></a>

### 28. SERIES只會是數字

來源：[S024-Q015](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q15>)；2026-07-24

**問題**

SERIES只會是數字

**歷史回答**

對，`OPDN.Series` 本身是數字欄位，但「數字」不代表它就是 Object Type。兩者都是數字，意義不同：

```text
OPDN.Series  = 編號系列，例如 91、92、93
OPDN.ObjType = 單據類型，收貨採購單固定為 20
```

依你提供的紀錄，`[@FORM_NUM].U_SystemCode` 存的是 Object Type，所以應比對 `20`，不是 `OPDN.Series`。

若 `U_SystemCode` 也是數字型別，可以簡化成：

```sql
----------------------------------------------------------------
-- 收貨採購單 U_typename 驗證
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                NULLIF(LTRIM(RTRIM(T0.U_typename)), '') IS NULL

                OR NOT EXISTS (
                    SELECT 1
                    FROM [dbo].[@FORM_NUM] T1
                    WHERE T1.U_SystemCode = 20
                      AND ISNULL(
                              LTRIM(RTRIM(T1.U_CategoryName)), ''
                          ) =
                          ISNULL(
                              LTRIM(RTRIM(T0.U_typename)), ''
                          )
                )
              )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'收貨採購單的類別名稱不正確，請重新執行格式化搜尋。'
    END

END
```

如果真正需求是用「文件編號系列」决定类别，例如：

```text
Series 91 → 类别 A
Series 92 → 类别 B
```

才应该写：

```sql
WHERE T1.U_SystemCode = T0.Series
```

但如果需求是：

```text
采购订单（Object Type 22）→ 采购类别
收货采购单（Object Type 20）→ 收货类别
```

就应该写：

```sql
WHERE T1.U_SystemCode = 20
```

根据你前面说明的「采购订单转换后，收货采购单应使用自己的内容」，应该采用第二种，也就是使用 Object Type `20`。

<a id="qa29"></a>

### 29. SQL／程式碼調整與說明

來源：[S024-Q016](<../../原始問答/S024_新增 Series 對應 UDO 驗證.md#q16>)；2026-07-24

**問題**

----------------------------------------------------------------
-- 收货采购单 U_typename 验证
--
-- 收货采购单 Object Type = 20
-- @FORM_NUM.U_SystemCode = Object Type
-- @FORM_NUM.U_CategoryName = 正确类别名称
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)

          -- 类别为空，或者类别与收货采购单设置不符
          AND (
                 NULLIF(LTRIM(RTRIM(T0.U_typename)), '') IS NULL

                 OR NOT EXISTS (
                    SELECT 1
                    FROM [dbo].[@FORM_NUM] T1
                    WHERE CONVERT(NVARCHAR(20), T1.U_SystemCode) =
                          @object_type
                      AND ISNULL(LTRIM(RTRIM(T1.U_CategoryName)), '') =
                          ISNULL(LTRIM(RTRIM(T0.U_typename)), '')
                 )
              )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'收货采购单的类别名称不正确，请重新执行格式化搜索。'
    END

END


T1.U_SystemCode) =
                          @object_type 這兩個是不會一樣的，應該是T1.U_SystemCode) = 等於收貨採購單的系統碼 如果不一樣就跳ERROR

**歷史回答**

對，你說得對。这里：

```sql
T1.U_SystemCode = @object_type
```

不符合你的实际设计。

正确关系应该是：

```text
@object_type = 20
```

只用来判断目前是不是收货采购单。

真正的数据验证是：

```text
@FORM_NUM.U_SystemCode = OPDN.Series
@FORM_NUM.U_CategoryName = OPDN.U_typename
```

也就是根据收货采购单自己的 `Series`，检查 `U_typename` 是否为该系列对应的类别。

完整修正版：

```sql
----------------------------------------------------------------
-- 收货采购单 U_typename 验证
--
-- @object_type = 20：判断目前是收货采购单
-- @FORM_NUM.U_SystemCode = OPDN.Series
-- @FORM_NUM.U_CategoryName = OPDN.U_typename
----------------------------------------------------------------
IF @object_type = '20'
   AND @transaction_type IN ('A', 'U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM [dbo].[OPDN] T0
        WHERE T0.DocEntry =
              TRY_CAST(@list_of_cols_val_tab_del AS INT)

          AND (
                ------------------------------------------------
                -- U_typename 不得为空
                ------------------------------------------------
                NULLIF(LTRIM(RTRIM(T0.U_typename)), '') IS NULL

                OR

                ------------------------------------------------
                -- Series + U_typename 必须符合 FORM_NUM 设置
                ------------------------------------------------
                NOT EXISTS (
                    SELECT 1
                    FROM [dbo].[@FORM_NUM] T1
                    WHERE LTRIM(RTRIM(
                              CONVERT(NVARCHAR(20), T1.U_SystemCode)
                          )) =
                          LTRIM(RTRIM(
                              CONVERT(NVARCHAR(20), T0.Series)
                          ))
                      AND ISNULL(
                              LTRIM(RTRIM(T1.U_CategoryName)), ''
                          ) =
                          ISNULL(
                              LTRIM(RTRIM(T0.U_typename)), ''
                          )
                )
              )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'收货采购单的类别名称与文件系列设置不符，请重新执行格式化搜索。'
    END

END
```

验证关系如下：

```text
OPDN.Series
    ↓
@FORM_NUM.U_SystemCode
    ↓
取得对应的 U_CategoryName
    ↓
检查是否等于 OPDN.U_typename
```

例如：

```text
收货采购单：
OPDN.Series     = 91
OPDN.U_typename = 收货采购
```

设定表：

```text
U_SystemCode   = 91
U_CategoryName = 收货采购
```

结果：允许新增。

如果从采购单带过来的是：

```text
OPDN.Series     = 91
OPDN.U_typename = 一般采购
```

但设定表中 `91` 对应的是「收货采购」，结果就会跳出错误并阻止新增。

如果 `U_SystemCode` 确定是数值型别，条件也可以简化为：

```sql
WHERE T1.U_SystemCode = T0.Series
  AND ISNULL(LTRIM(RTRIM(T1.U_CategoryName)), '') =
      ISNULL(LTRIM(RTRIM(T0.U_typename)), '')
```

不过保留 `CONVERT` 版本可以避免 `U_SystemCode` 实际是文字型 UDF 时发生型别转换问题。

<a id="qa30"></a>

### 30. SQL／程式碼調整與說明

來源：[S085-Q001](<../../原始問答/S085_擴充 UDO 單據驗證範圍.md#q1>)；2026-08-18

**問題**

----------------------------------------------------------------
-- Series 對應 UDO 組合驗證
-- 類別 / 異動區分 / 異動原因 必須符合 UDO 設定
----------------------------------------------------------------
IF @object_type = '20' AND @transaction_type IN ('A','U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPDN T0
        WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [@FORM_NUM] T1
                WHERE T1.U_SystemCode = CAST(T0.Series AS NVARCHAR(20))
                  AND T1.U_CategoryName = T0.U_TypeName
                  AND T1.U_ChangeType = T0.U_ChangeType
                  AND T1.U_ChangeReason = T0.U_ChangeReason
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message = N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END


目前是收貨採購單 我想再增加 生產收貨 生產發貨 庫存調撥 AR貸項 採購退貨 交貨單 採購單 AR發票 生產訂單 銷售訂單 採購請求

**歷史回答**

可以將各單據表頭用 `UNION ALL` 統一驗證。以下包含原本的「收貨採購單」，並新增你列出的 11 種單據：

```sql
----------------------------------------------------------------
-- Series 對應 UDO 組合驗證
-- 類別 / 異動區分 / 異動原因 必須符合 UDO 設定
----------------------------------------------------------------
IF @object_type IN (
       '20',          -- 收貨採購單
       '59',          -- 生產收貨
       '60',          -- 生產發貨
       '67',          -- 庫存調撥
       '14',          -- A/R 貸項通知單
       '21',          -- 採購退貨
       '15',          -- 交貨單
       '22',          -- 採購單
       '13',          -- A/R 發票
       '202',         -- 生產訂單
       '17',          -- 銷售訂單
       '1470000113'   -- 採購請求
   )
   AND @transaction_type IN ('A', 'U')
   AND @error = 0
BEGIN

    IF EXISTS (
        SELECT 1
        FROM (
            ----------------------------------------------------
            -- 收貨採購單
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OPDN T0
            WHERE @object_type = '20'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 生產收貨
            -- Object Type 59 同時包含一般庫存收貨，
            -- 透過 IGN1.BaseType = 202 限定為生產收貨
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OIGN T0
            WHERE @object_type = '59'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)
              AND EXISTS (
                    SELECT 1
                    FROM IGN1 L0
                    WHERE L0.DocEntry = T0.DocEntry
                      AND L0.BaseType = 202
              )

            UNION ALL

            ----------------------------------------------------
            -- 生產發貨
            -- Object Type 60 同時包含一般庫存發貨，
            -- 透過 IGE1.BaseType = 202 限定為生產發貨
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OIGE T0
            WHERE @object_type = '60'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)
              AND EXISTS (
                    SELECT 1
                    FROM IGE1 L0
                    WHERE L0.DocEntry = T0.DocEntry
                      AND L0.BaseType = 202
              )

            UNION ALL

            ----------------------------------------------------
            -- 庫存調撥
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OWTR T0
            WHERE @object_type = '67'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- A/R 貸項通知單
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ORIN T0
            WHERE @object_type = '14'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 採購退貨
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ORPD T0
            WHERE @object_type = '21'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 交貨單
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ODLN T0
            WHERE @object_type = '15'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 採購單
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OPOR T0
            WHERE @object_type = '22'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- A/R 發票
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OINV T0
            WHERE @object_type = '13'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 生產訂單
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OWOR T0
            WHERE @object_type = '202'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 銷售訂單
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ORDR T0
            WHERE @object_type = '17'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 採購請求
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OPRQ T0
            WHERE @object_type = '1470000113'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

        ) DOC
        WHERE NOT EXISTS (
            SELECT 1
            FROM [@FORM_NUM] UDO
            WHERE UDO.U_SystemCode =
                  CAST(DOC.Series AS NVARCHAR(20))
              AND UDO.U_CategoryName = DOC.U_TypeName
              AND UDO.U_ChangeType = DOC.U_ChangeType
              AND UDO.U_ChangeReason = DOC.U_ChangeReason
        )
    )
    BEGIN
        SET @error = 2006
        SET @error_message =
            N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END
```

特別注意：

- `59` 和 `60` 分別是庫存收貨、庫存發貨的 Object Type，不只用於生產作業。因此程式使用明細的 `BaseType = 202`，只驗證以生產訂單為基礎的生產收貨／發貨。
- 若也要驗證「一般庫存收貨／一般庫存發貨」，將兩段中的 `EXISTS (...) BaseType = 202` 移除即可。
- 上述所有表頭都必須已建立 `U_TypeName`、`U_ChangeType`、`U_ChangeReason` 三個 UDF，否則 SQL 會無法編譯。
- `AND @error = 0` 可避免覆蓋前面其他卡控已經產生的錯誤。

Object Type 與表名可在 SAP 官方的 [BoObjectTypes Enumeration](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKhelp/SAPbobsCOM~Enumerations~BoObjectTypes_EN.html)及[單據來源資料表說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/8cd3db2a2ab94882b22503cba89c0063/a543aed2372243fcb7d723534dd482c9.html)核對。

<a id="qa31"></a>

### 31. UNION ALL 的 用法

來源：[S085-Q002](<../../原始問答/S085_擴充 UDO 單據驗證範圍.md#q2>)；2026-08-18

**問題**

UNION ALL 的 用法

**歷史回答**

`UNION ALL` 是把多個 `SELECT` 的查詢結果「上下合併」成同一個結果集，而且保留重複資料。

基本語法：

```sql
SELECT 欄位1, 欄位2
FROM 資料表A

UNION ALL

SELECT 欄位1, 欄位2
FROM 資料表B;
```

例如：

```sql
SELECT DocEntry, Series
FROM OPDN

UNION ALL

SELECT DocEntry, Series
FROM OINV;
```

結果會把 `OPDN` 與 `OINV` 的資料接在一起：

```text
DocEntry  Series
--------  ------
1         100
2         100
1         200
3         200
```

即使兩邊有完全相同的資料，也會全部保留。

### UNION 與 UNION ALL 的差異

```sql
SELECT Col1 FROM TableA
UNION
SELECT Col1 FROM TableB;
```

`UNION` 會移除重複資料。

```sql
SELECT Col1 FROM TableA
UNION ALL
SELECT Col1 FROM TableB;
```

`UNION ALL` 會保留重複資料，通常效能也比較好，因為資料庫不需要額外排序、比對及去除重複值。

### 使用條件

每個 `SELECT` 必須符合：

1. 欄位數量相同。
2. 欄位排列順序相同。
3. 對應欄位的資料型態必須相容。

正確：

```sql
SELECT
    Series,
    U_TypeName,
    U_ChangeType
FROM OPDN

UNION ALL

SELECT
    Series,
    U_TypeName,
    U_ChangeType
FROM OINV;
```

錯誤：

```sql
SELECT
    Series,
    U_TypeName
FROM OPDN

UNION ALL

SELECT
    Series,
    U_TypeName,
    U_ChangeType
FROM OINV;
```

因為第一段是兩個欄位，第二段是三個欄位。

### 在你的卡控程式中的作用

你的不同單據資料放在不同資料表：

- 收貨採購單：`OPDN`
- A/R 發票：`OINV`
- 交貨單：`ODLN`
- 採購單：`OPOR`

但它們都有相同的驗證欄位：

```sql
Series
U_TypeName
U_ChangeType
U_ChangeReason
```

所以先透過 `UNION ALL` 將目前正在處理的單據整理成相同格式：

```sql
SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM OPDN T0
WHERE @object_type = '20'
  AND T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)

UNION ALL

SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM OINV T0
WHERE @object_type = '13'
  AND T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
```

當 `@object_type = '20'` 時：

- `OPDN` 查得到資料。
- `OINV` 因為 `@object_type = '13'` 不成立，查不到資料。

當 `@object_type = '13'` 時則相反。

因此雖然寫了很多段 `SELECT`，實際執行時通常只有符合目前 `@object_type` 的那一段會回傳資料。

合併後再給它一個臨時名稱：

```sql
FROM (
    SELECT ...
    FROM OPDN

    UNION ALL

    SELECT ...
    FROM OINV
) DOC
```

後面就能統一用 `DOC` 來驗證：

```sql
WHERE NOT EXISTS (
    SELECT 1
    FROM [@FORM_NUM] UDO
    WHERE UDO.U_SystemCode =
          CAST(DOC.Series AS NVARCHAR(20))
      AND UDO.U_CategoryName = DOC.U_TypeName
      AND UDO.U_ChangeType = DOC.U_ChangeType
      AND UDO.U_ChangeReason = DOC.U_ChangeReason
)
```

也就是：

```text
不同單據表
   ↓ UNION ALL
統一成 DOC
   ↓
只寫一次 UDO 驗證條件
```

這裡適合使用 `UNION ALL`，因為各段已透過 `@object_type` 限制，沒有去除重複資料的需求。

<a id="qa32"></a>

### 32. Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 2108 [Batch Sta…

來源：[S085-Q003](<../../原始問答/S085_擴充 UDO 單據驗證範圍.md#q3>)；2026-08-19

**問題**

Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 2108 [Batch Start Line 7]
Invalid column name 'U_ChangeType'.
Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 2109 [Batch Start Line 7]
Invalid column name 'U_ChangeReason'.

**歷史回答**

這個錯誤表示：`UNION ALL` 中至少一個 SAP 表頭沒有建立以下欄位：

```sql
U_ChangeType
U_ChangeReason
```

即使目前只新增收貨採購單，SQL Server 在儲存預存程序時仍會編譯所有 `UNION ALL` 分支，因此任何一張表缺欄位，整個程序都無法更新。

先執行以下查詢，找出哪些資料表缺少 UDF：

```sql
SELECT
    V.DocName AS N'單據名稱',
    V.TableName AS N'資料表',
    CASE
        WHEN COL_LENGTH(V.TableName, 'U_TypeName') IS NULL
            THEN N'缺少'
        ELSE N'存在'
    END AS U_TypeName,
    CASE
        WHEN COL_LENGTH(V.TableName, 'U_ChangeType') IS NULL
            THEN N'缺少'
        ELSE N'存在'
    END AS U_ChangeType,
    CASE
        WHEN COL_LENGTH(V.TableName, 'U_ChangeReason') IS NULL
            THEN N'缺少'
        ELSE N'存在'
    END AS U_ChangeReason
FROM (
    VALUES
        (N'收貨採購單',     'OPDN'),
        (N'生產收貨',       'OIGN'),
        (N'生產發貨',       'OIGE'),
        (N'庫存調撥',       'OWTR'),
        (N'A/R 貸項通知單', 'ORIN'),
        (N'採購退貨',       'ORPD'),
        (N'交貨單',         'ODLN'),
        (N'採購單',         'OPOR'),
        (N'A/R 發票',       'OINV'),
        (N'生產訂單',       'OWOR'),
        (N'銷售訂單',       'ORDR'),
        (N'採購請求',       'OPRQ')
) V (DocName, TableName)
ORDER BY V.TableName;
```

結果顯示「缺少」的資料表，就是造成錯誤的來源。

### 解決方式一：在所有單據建立相同 UDF

如果所有單據都需要驗證這三個欄位，應透過 SAP Business One 建立 UDF：

```text
工具
→ 自訂工具
→ 使用者定義欄位－管理
→ 文件
→ 對應的單據類型
```

確認每種單據都有：

```text
TypeName
ChangeType
ChangeReason
```

實際資料庫欄位會是：

```sql
U_TypeName
U_ChangeType
U_ChangeReason
```

不要直接用 `ALTER TABLE` 修改 SAP 系統表，應透過 SAP B1 的 UDF 管理功能新增。

### 解決方式二：暫時移除尚未建立 UDF 的單據

例如檢查結果顯示 `OWOR` 沒有：

```sql
U_ChangeType
U_ChangeReason
```

就先移除生產訂單這一段：

```sql
UNION ALL

SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM OWOR T0
WHERE @object_type = '202'
  AND T0.DocEntry =
      CAST(@list_of_cols_val_tab_del AS INT)
```

同時從最前面的清單移除 `'202'`：

```sql
IF @object_type IN (
    '20',
    '59',
    '60',
    '67',
    '14',
    '21',
    '15',
    '22',
    '13',
    -- '202' 暫時移除
    '17',
    '1470000113'
)
```

### 檢查欄位實際名稱

如果已經建立 UDF，但名稱可能不同，可以查：

```sql
SELECT
    T0.TableID,
    T0.AliasID,
    T0.Descr
FROM CUFD T0
WHERE T0.TableID IN (
    'OPDN', 'OIGN', 'OIGE', 'OWTR',
    'ORIN', 'ORPD', 'ODLN', 'OPOR',
    'OINV', 'OWOR', 'ORDR', 'OPRQ'
)
ORDER BY
    T0.TableID,
    T0.FieldID;
```

例如查到：

```text
AliasID = Change_Type
```

SQL 欄位名稱就應該寫成：

```sql
T0.U_Change_Type
```

而不是：

```sql
T0.U_ChangeType
```

重點是：`@object_type` 的條件不能避免編譯錯誤；`UNION ALL` 裡出現的每個實體欄位，都必須確實存在。

<a id="qa33"></a>

### 33. Msg 205, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1949 [Batch Sta…

來源：[S085-Q004](<../../原始問答/S085_擴充 UDO 單據驗證範圍.md#q4>)；2026-08-19

**問題**

Msg 205, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1949 [Batch Start Line 7]
All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.

**歷史回答**

這個錯誤表示 `UNION ALL` 串接的某一段 `SELECT`，回傳欄位數量和其他段不同。

你的每一段都必須固定回傳這 4 個欄位：

```sql
SELECT
    T0.Series,          -- 第 1 欄
    T0.U_TypeName,      -- 第 2 欄
    T0.U_ChangeType,    -- 第 3 欄
    T0.U_ChangeReason   -- 第 4 欄
```

正確格式如下：

```sql
SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM OPDN T0
WHERE @object_type = '20'
  AND T0.DocEntry =
      CAST(@list_of_cols_val_tab_del AS INT)

UNION ALL

SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM OINV T0
WHERE @object_type = '13'
  AND T0.DocEntry =
      CAST(@list_of_cols_val_tab_del AS INT);
```

## 常見錯誤

例如某一段漏掉 `U_ChangeReason`：

```sql
-- 錯誤：只有 3 個欄位
SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType
FROM OWOR T0
```

其他段有 4 個欄位：

```sql
-- 其他段有 4 個欄位
SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM OPDN T0
```

這樣就會出現 `Msg 205`。

另一種情況是誤將額外欄位放進某一段：

```sql
-- 錯誤：這段有 5 個欄位
SELECT
    T0.DocEntry,
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM OPDN T0
```

## 如果某張表沒有該 UDF

如果該單據暫時沒有 `U_ChangeType` 或 `U_ChangeReason`，不能直接少寫欄位，仍要用 `NULL` 補齊相同欄位數量：

```sql
SELECT
    T0.Series,
    T0.U_TypeName,
    CAST(NULL AS NVARCHAR(100)) AS U_ChangeType,
    CAST(NULL AS NVARCHAR(100)) AS U_ChangeReason
FROM OWOR T0
WHERE @object_type = '202'
  AND T0.DocEntry =
      CAST(@list_of_cols_val_tab_del AS INT)
```

但要注意，這樣會導致後續 UDO 比對不成功，因此只適合表示「該單據的欄位目前不存在」。若該單據本來就不需要驗證，應把整段移除。

## 建議統一加上欄位別名

第一段可明確定義衍生資料表的欄位名稱：

```sql
SELECT
    DOC.Series,
    DOC.TypeName,
    DOC.ChangeType,
    DOC.ChangeReason
FROM (
    SELECT
        T0.Series          AS Series,
        T0.U_TypeName      AS TypeName,
        T0.U_ChangeType    AS ChangeType,
        T0.U_ChangeReason  AS ChangeReason
    FROM OPDN T0
    WHERE @object_type = '20'
      AND T0.DocEntry =
          CAST(@list_of_cols_val_tab_del AS INT)

    UNION ALL

    SELECT
        T0.Series,
        T0.U_TypeName,
        T0.U_ChangeType,
        T0.U_ChangeReason
    FROM OINV T0
    WHERE @object_type = '13'
      AND T0.DocEntry =
          CAST(@list_of_cols_val_tab_del AS INT)
) DOC;
```

你目前需要檢查 `Line 1949` 前後的每個 `SELECT`。逐段數逗號最容易判斷：

```text
Series,
U_TypeName,
U_ChangeType,
U_ChangeReason
```

每一段都只能有相同的 4 個輸出欄位。`WHERE` 裡有多少條件不影響，只有 `SELECT` 和 `FROM` 之間的輸出欄位數需要一致。

<a id="qa34"></a>

### 34. 生產收貨 生產發貨 庫存調撥 AR貸項 採購退貨 交貨單 採購單 AR發票 銷售訂單 採購請求 草稿也要卡控

來源：[S085-Q005](<../../原始問答/S085_擴充 UDO 單據驗證範圍.md#q5>)；2026-08-19

**問題**

生產收貨 生產發貨 庫存調撥 AR貸項 採購退貨 交貨單 採購單 AR發票  銷售訂單 採購請求 草稿也要卡控

**歷史回答**

草稿在 `SBO_SP_TransactionNotification` 中的：

- `@object_type = '112'`
- 表頭資料表：`ODRF`
- `ODRF.ObjType`：草稿實際的單據類型
- 草稿明細：`DRF1`

因此除了原本正式單據的 Object Type，最外層必須加入 `'112'`：

```sql
IF @object_type IN (
       '20',          -- 收貨採購單
       '59',          -- 生產收貨
       '60',          -- 生產發貨
       '67',          -- 庫存調撥
       '14',          -- A/R 貸項通知單
       '21',          -- 採購退貨
       '15',          -- 交貨單
       '22',          -- 採購單
       '13',          -- A/R 發票
       '17',          -- 銷售訂單
       '1470000113',  -- 採購請求
       '112'          -- 草稿
   )
   AND @transaction_type IN ('A', 'U')
   AND @error = 0
BEGIN
```

然後在原本 `UNION ALL` 查詢的最後一段，加入以下草稿查詢。注意仍然維持相同的 4 個輸出欄位：

```sql
UNION ALL

----------------------------------------------------
-- 草稿
-- @object_type = 112
-- ODRF.ObjType 為草稿實際單據類型
----------------------------------------------------
SELECT
    T0.Series,
    T0.U_TypeName,
    T0.U_ChangeType,
    T0.U_ChangeReason
FROM ODRF T0
WHERE @object_type = '112'
  AND T0.DocEntry =
      CAST(@list_of_cols_val_tab_del AS INT)

  -- 只驗證指定的草稿單據類型
  AND T0.ObjType IN (
        '59',          -- 庫存收貨／生產收貨
        '60',          -- 庫存發貨／生產發貨
        '67',          -- 庫存調撥
        '14',          -- A/R 貸項通知單
        '21',          -- 採購退貨
        '15',          -- 交貨單
        '22',          -- 採購單
        '13',          -- A/R 發票
        '17',          -- 銷售訂單
        '1470000113'   -- 採購請求
  )

  -- 59、60 只限定生產收貨／生產發貨
  AND (
        T0.ObjType NOT IN ('59', '60')
        OR EXISTS (
            SELECT 1
            FROM DRF1 T1
            WHERE T1.DocEntry = T0.DocEntry
              AND T1.ObjType = T0.ObjType
              AND T1.BaseType = 202
        )
  )
```

完整的草稿卡控邏輯相當於：

```text
@object_type = 112
        ↓
查詢 ODRF
        ↓
使用 ODRF.ObjType 判斷真正單據
        ↓
59、60 再使用 DRF1.BaseType = 202
判斷是否為生產收貨／生產發貨
        ↓
以 Series + 三個 UDF 比對 @FORM_NUM
```

如果 `DRF1` 沒有 `ObjType` 或你的版本編譯時出現欄位錯誤，可以改成：

```sql
AND (
      T0.ObjType NOT IN ('59', '60')
      OR EXISTS (
          SELECT 1
          FROM DRF1 T1
          WHERE T1.DocEntry = T0.DocEntry
            AND T1.BaseType = 202
      )
)
```

另外請先確認 `ODRF` 是否有三個 UDF：

```sql
SELECT
    COL_LENGTH('ODRF', 'U_TypeName')     AS U_TypeName,
    COL_LENGTH('ODRF', 'U_ChangeType')   AS U_ChangeType,
    COL_LENGTH('ODRF', 'U_ChangeReason') AS U_ChangeReason;
```

如果欄位存在，結果會回傳欄位長度；如果不存在，會回傳 `NULL`。草稿卡控要正常編譯，`ODRF` 必須具備這三個欄位。

<a id="qa35"></a>

### 35. 好 給我一個完整的

來源：[S085-Q006](<../../原始問答/S085_擴充 UDO 單據驗證範圍.md#q6>)；2026-08-19

**問題**

好 給我一個完整的

**歷史回答**

以下是完整版本，包含：

- 正式單據卡控
- 草稿卡控（`ODRF`）
- 生產收貨／生產發貨限定 `BaseType = 202`
- 所有 `UNION ALL` 均固定回傳 4 個欄位
- 避免覆蓋前面其他卡控錯誤

```sql
----------------------------------------------------------------
-- Series 對應 UDO 組合驗證
--
-- 驗證條件：
-- 1. 單據 Series
-- 2. 類別 U_TypeName
-- 3. 異動區分 U_ChangeType
-- 4. 異動原因 U_ChangeReason
--
-- 必須符合 [@FORM_NUM] 的設定
----------------------------------------------------------------
IF @object_type IN (
       '20',          -- 收貨採購單
       '59',          -- 生產收貨
       '60',          -- 生產發貨
       '67',          -- 庫存調撥
       '14',          -- A/R 貸項通知單
       '21',          -- 採購退貨
       '15',          -- 交貨單
       '22',          -- 採購單
       '13',          -- A/R 發票
       '202',         -- 生產訂單
       '17',          -- 銷售訂單
       '1470000113',  -- 採購請求
       '112'          -- 草稿
   )
   AND @transaction_type IN ('A', 'U')
   AND @error = 0
BEGIN

    IF EXISTS (
        SELECT 1
        FROM (
            ----------------------------------------------------
            -- 1. 收貨採購單
            -- Object Type：20
            -- Table：OPDN
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OPDN T0
            WHERE @object_type = '20'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 2. 生產收貨
            -- Object Type：59
            -- Header：OIGN
            -- Detail：IGN1
            --
            -- Object Type 59 也包含一般庫存收貨，
            -- 使用 BaseType = 202 限定生產收貨
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OIGN T0
            WHERE @object_type = '59'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)
              AND EXISTS (
                    SELECT 1
                    FROM IGN1 T1
                    WHERE T1.DocEntry = T0.DocEntry
                      AND T1.BaseType = 202
              )

            UNION ALL

            ----------------------------------------------------
            -- 3. 生產發貨
            -- Object Type：60
            -- Header：OIGE
            -- Detail：IGE1
            --
            -- Object Type 60 也包含一般庫存發貨，
            -- 使用 BaseType = 202 限定生產發貨
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OIGE T0
            WHERE @object_type = '60'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)
              AND EXISTS (
                    SELECT 1
                    FROM IGE1 T1
                    WHERE T1.DocEntry = T0.DocEntry
                      AND T1.BaseType = 202
              )

            UNION ALL

            ----------------------------------------------------
            -- 4. 庫存調撥
            -- Object Type：67
            -- Table：OWTR
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OWTR T0
            WHERE @object_type = '67'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 5. A/R 貸項通知單
            -- Object Type：14
            -- Table：ORIN
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ORIN T0
            WHERE @object_type = '14'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 6. 採購退貨
            -- Object Type：21
            -- Table：ORPD
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ORPD T0
            WHERE @object_type = '21'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 7. 交貨單
            -- Object Type：15
            -- Table：ODLN
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ODLN T0
            WHERE @object_type = '15'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 8. 採購單
            -- Object Type：22
            -- Table：OPOR
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OPOR T0
            WHERE @object_type = '22'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 9. A/R 發票
            -- Object Type：13
            -- Table：OINV
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OINV T0
            WHERE @object_type = '13'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 10. 生產訂單
            -- Object Type：202
            -- Table：OWOR
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OWOR T0
            WHERE @object_type = '202'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 11. 銷售訂單
            -- Object Type：17
            -- Table：ORDR
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ORDR T0
            WHERE @object_type = '17'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 12. 採購請求
            -- Object Type：1470000113
            -- Table：OPRQ
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OPRQ T0
            WHERE @object_type = '1470000113'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            ----------------------------------------------------
            -- 13. 草稿
            -- Transaction Notification Object Type：112
            -- Header：ODRF
            -- Detail：DRF1
            --
            -- ODRF.ObjType 為草稿實際單據類型
            ----------------------------------------------------
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ODRF T0
            WHERE @object_type = '112'
              AND T0.DocEntry =
                  CAST(@list_of_cols_val_tab_del AS INT)

              --------------------------------------------------
              -- 只驗證下列草稿單據
              --------------------------------------------------
              AND T0.ObjType IN (
                    '20',          -- 收貨採購單
                    '59',          -- 生產收貨
                    '60',          -- 生產發貨
                    '67',          -- 庫存調撥
                    '14',          -- A/R 貸項通知單
                    '21',          -- 採購退貨
                    '15',          -- 交貨單
                    '22',          -- 採購單
                    '13',          -- A/R 發票
                    '17',          -- 銷售訂單
                    '1470000113'   -- 採購請求
              )

              --------------------------------------------------
              -- 草稿 59、60 只卡生產收貨／生產發貨
              -- 其他草稿單據直接進入驗證
              --------------------------------------------------
              AND (
                    T0.ObjType NOT IN ('59', '60')
                    OR EXISTS (
                        SELECT 1
                        FROM DRF1 T1
                        WHERE T1.DocEntry = T0.DocEntry
                          AND T1.BaseType = 202
                    )
              )

        ) DOC

        --------------------------------------------------------
        -- 驗證該組合是否存在於 UDO 設定
        --------------------------------------------------------
        WHERE NOT EXISTS (
            SELECT 1
            FROM [@FORM_NUM] UDO
            WHERE UDO.U_SystemCode =
                  CAST(DOC.Series AS NVARCHAR(20))
              AND UDO.U_CategoryName =
                  DOC.U_TypeName
              AND UDO.U_ChangeType =
                  DOC.U_ChangeType
              AND UDO.U_ChangeReason =
                  DOC.U_ChangeReason
        )
    )
    BEGIN
        SET @error = 2006

        SET @error_message =
            N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END
```

使用前先執行以下檢查，確保所有正式單據表及草稿表都有三個 UDF：

```sql
SELECT
    V.DocName AS N'單據名稱',
    V.TableName AS N'資料表',

    CASE
        WHEN COL_LENGTH(V.TableName, 'U_TypeName') IS NULL
            THEN N'缺少'
        ELSE N'存在'
    END AS U_TypeName,

    CASE
        WHEN COL_LENGTH(V.TableName, 'U_ChangeType') IS NULL
            THEN N'缺少'
        ELSE N'存在'
    END AS U_ChangeType,

    CASE
        WHEN COL_LENGTH(V.TableName, 'U_ChangeReason') IS NULL
            THEN N'缺少'
        ELSE N'存在'
    END AS U_ChangeReason

FROM (
    VALUES
        (N'收貨採購單',     'OPDN'),
        (N'生產收貨',       'OIGN'),
        (N'生產發貨',       'OIGE'),
        (N'庫存調撥',       'OWTR'),
        (N'A/R 貸項通知單', 'ORIN'),
        (N'採購退貨',       'ORPD'),
        (N'交貨單',         'ODLN'),
        (N'採購單',         'OPOR'),
        (N'A/R 發票',       'OINV'),
        (N'生產訂單',       'OWOR'),
        (N'銷售訂單',       'ORDR'),
        (N'採購請求',       'OPRQ'),
        (N'單據草稿',       'ODRF')
) V (DocName, TableName)
ORDER BY V.TableName;
```

只要任何一張表顯示「缺少」，完整 SQL 就會出現 `Invalid column name`。需要先在 SAP Business One 的使用者定義欄位管理中補上，或暫時移除該單據的 `UNION ALL` 分支。

<a id="qa36"></a>

### 36. SQL／程式碼調整與說明

來源：[S133-Q001](<../../原始問答/S133_新增草稿單據檢核.md#q1>)；2026-09-04

**問題**

---

---

\-- 8. 採購單\
 \-- Object Type：22\
 \-- Table：OPOR  

---

SELECT\
 T0.Series,\
 T0.U\_TypeName,\
 T0.U\_ChangeType,\
 T0.U\_ChangeReason\
 FROM OPOR T0\
 WHERE @object\_type = '22'\
 AND T0.DocEntry =\
 CAST(@list\_of\_cols\_val\_tab\_del AS INT)  

UNION ALL  

---

\-- 9. A/R 發票\
 \-- Object Type：13\
 \-- Table：OINV  

---

SELECT\
 T0.Series,\
 T0.U\_TypeName,\
 T0.U\_ChangeType,\
 T0.U\_ChangeReason\
 FROM OINV T0\
 WHERE @object\_type = '13'\
 AND T0.DocEntry =\
 CAST(@list\_of\_cols\_val\_tab\_del AS INT)  

UNION ALL  

---

\-- 11. 銷售訂單\
 \-- Object Type：17\
 \-- Table：ORDR  

---

SELECT\
 T0.Series,\
 T0.U\_TypeName,\
 T0.U\_ChangeType,\
 T0.U\_ChangeReason\
 FROM ORDR T0\
 WHERE @object\_type = '17'\
 AND T0.DocEntry =\
 CAST(@list\_of\_cols\_val\_tab\_del AS INT)  

UNION ALL  





我寫SP  我現在只要卡 



WHERE NOT EXISTS (\
 SELECT 1\
 FROM [@FORM\_NUM] UDO\
 WHERE UDO.U\_SystemCode =\
 CAST(DOC.Series AS NVARCHAR(20))\
 AND UDO.U\_CategoryName =\
 DOC.U\_TypeName\
 AND UDO.U\_ChangeType =\
 DOC.U\_ChangeType\
 AND UDO.U\_ChangeReason =\
 DOC.U\_ChangeReason\
 )\
 )\
 BEGIN\
 SET @error = 2008

SET @error\_message =\
 N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'\
 END  

END  



以及草稿也要

**歷史回答**

草稿在 SAP Business One 的 `Object Type` 是 `112`，資料表是 `ODRF`；再透過 `ODRF.ObjType` 判斷草稿原本是採購單、A/R 發票或銷售訂單。

可以把正式單據與草稿一起整理成 `DOC`，再統一驗證：

```sql
/* 採購單、A/R 發票、銷售訂單，以及其草稿 */
IF @transaction_type IN ('A', 'U')
AND @object_type IN ('22', '13', '17', '112')
BEGIN

    IF EXISTS
    (
        SELECT 1
        FROM
        (
            /* 8. 採購單 */
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OPOR T0
            WHERE @object_type = '22'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 9. A/R 發票 */
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM OINV T0
            WHERE @object_type = '13'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 11. 銷售訂單 */
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ORDR T0
            WHERE @object_type = '17'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 草稿：採購單、A/R 發票、銷售訂單 */
            SELECT
                T0.Series,
                T0.U_TypeName,
                T0.U_ChangeType,
                T0.U_ChangeReason
            FROM ODRF T0
            WHERE @object_type = '112'
              AND CONVERT(NVARCHAR(20), T0.ObjType)
                  IN ('22', '13', '17')
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)
        ) DOC
        WHERE NOT EXISTS
        (
            SELECT 1
            FROM [@FORM_NUM] UDO
            WHERE UDO.U_SystemCode =
                  CAST(DOC.Series AS NVARCHAR(20))
              AND ISNULL(UDO.U_CategoryName, '') =
                  ISNULL(DOC.U_TypeName, '')
              AND ISNULL(UDO.U_ChangeType, '') =
                  ISNULL(DOC.U_ChangeType, '')
              AND ISNULL(UDO.U_ChangeReason, '') =
                  ISNULL(DOC.U_ChangeReason, '')
        )
    )
    BEGIN
        SET @error = 2008;
        SET @error_message =
            N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。';
    END;

END;
```

重點是：

- 正式單據：
  - `22` → `OPOR`
  - `13` → `OINV`
  - `17` → `ORDR`
- 草稿一律是：
  - `@object_type = '112'`
  - Table：`ODRF`
  - `ODRF.ObjType` 才是草稿預計建立的正式單據類型。
- `ISNULL(..., '')` 可讓空值也能正常比對；否則 SQL Server 中 `NULL = NULL` 不會成立。
- 使用 `TRY_CAST` 可避免傳入值無法轉成整數時直接讓 Transaction Notification 發生轉型錯誤。

<a id="qa37"></a>

### 37. 我只要 TYPENAME

來源：[S133-Q002](<../../原始問答/S133_新增草稿單據檢核.md#q2>)；2026-09-04

**問題**

我只要 TYPENAME

**歷史回答**

如果只要卡 `U_TypeName`，把 `U_ChangeType`、`U_ChangeReason` 的比對拿掉即可：

```sql
IF @transaction_type IN ('A', 'U')
AND @object_type IN ('22', '13', '17', '112')
BEGIN

    IF EXISTS
    (
        SELECT 1
        FROM
        (
            /* 採購單 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM OPOR T0
            WHERE @object_type = '22'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* A/R 發票 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM OINV T0
            WHERE @object_type = '13'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 銷售訂單 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM ORDR T0
            WHERE @object_type = '17'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 草稿 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM ODRF T0
            WHERE @object_type = '112'
              AND CONVERT(NVARCHAR(20), T0.ObjType)
                  IN ('22', '13', '17')
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)
        ) DOC
        WHERE NOT EXISTS
        (
            SELECT 1
            FROM [@FORM_NUM] UDO
            WHERE UDO.U_SystemCode =
                  CAST(DOC.Series AS NVARCHAR(20))
              AND ISNULL(UDO.U_CategoryName, '') =
                  ISNULL(DOC.U_TypeName, '')
        )
    )
    BEGIN
        SET @error = 2008;
        SET @error_message =
            N'類別名稱與該單據系列別設定不符，請重新確認。';
    END;

END;
```

核心條件只剩：

```sql
WHERE UDO.U_SystemCode = CAST(DOC.Series AS NVARCHAR(20))
  AND ISNULL(UDO.U_CategoryName, '') =
      ISNULL(DOC.U_TypeName, '')
```

<a id="qa38"></a>

### 38. 給我完整的

來源：[S133-Q003](<../../原始問答/S133_新增草稿單據檢核.md#q3>)；2026-09-04

**問題**

給我完整的

**歷史回答**

以下是可直接放進 `SBO_SP_TransactionNotification` 的完整卡控，只檢查 `U_TypeName`，並包含採購單、A/R 發票、銷售訂單及草稿：

```sql
/* =========================================================
   單據系列別與類別名稱卡控
   正式單據：
       22  採購單      OPOR
       13  A/R 發票    OINV
       17  銷售訂單    ORDR

   草稿：
       112 草稿        ODRF
   ========================================================= */

IF @error = 0
   AND @transaction_type IN ('A', 'U')
   AND @object_type IN ('22', '13', '17', '112')
BEGIN

    IF EXISTS
    (
        SELECT 1
        FROM
        (
            /* 1. 採購單 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM OPOR T0
            WHERE @object_type = '22'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 2. A/R 發票 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM OINV T0
            WHERE @object_type = '13'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 3. 銷售訂單 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM ORDR T0
            WHERE @object_type = '17'
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

            UNION ALL

            /* 4. 採購單、A/R 發票、銷售訂單草稿 */
            SELECT
                T0.Series,
                T0.U_TypeName
            FROM ODRF T0
            WHERE @object_type = '112'
              AND CONVERT(NVARCHAR(20), T0.ObjType)
                  IN ('22', '13', '17')
              AND T0.DocEntry =
                  TRY_CAST(@list_of_cols_val_tab_del AS INT)

        ) DOC
        WHERE NOT EXISTS
        (
            SELECT 1
            FROM [@FORM_NUM] UDO
            WHERE UDO.U_SystemCode =
                  CONVERT(NVARCHAR(20), DOC.Series)
              AND ISNULL(UDO.U_CategoryName, '') =
                  ISNULL(DOC.U_TypeName, '')
        )
    )
    BEGIN
        SET @error = 2008;
        SET @error_message =
            N'類別名稱與該單據系列別設定不符，請重新確認。';
    END;

END;
```

放置位置是在 `SBO_SP_TransactionNotification` 最後這段之前：

```sql
SELECT @error, @error_message;
```

這版只會比對：

```sql
UDO.U_SystemCode = 單據 Series
UDO.U_CategoryName = 單據 U_TypeName
```

不會再檢查 `U_ChangeType` 和 `U_ChangeReason`。

