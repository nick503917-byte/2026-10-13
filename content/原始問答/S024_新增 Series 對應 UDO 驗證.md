# 新增 Series 對應 UDO 驗證

來源編號：S024  
聊天 ID：`019f9305-eb33-72c0-a9e8-4841bd43b3ca`  
聊天起始日期（台北）：2026-07-24  
保存位置：一般本機紀錄  
問答段落：16

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S024-Q001｜SQL／程式碼調整與說明

日期：2026-07-24

**你的提問**

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

**當時回答**

這段 SQL 是 SAP Business One 的 `SBO_SP_TransactionNotification` 驗證邏輯，用於新增或更新收貨採購單（`OPDN`，Object Type `20`）時，檢查：

- 單據系列 `Series`
- 類別 `U_TypeName`
- 異動區分 `U_ChangeType`
- 異動原因 `U_ChangeReason`

是否存在於 `[@CHANGECFG]` 的有效組合中。不符合時回傳錯誤 `2006`，阻止儲存。

您希望我協助哪一項：檢查邏輯、修改 SQL、處理 `NULL`、改善效能，還是套用到其他單據？

---

<a id="q2"></a>

## S024-Q002｜我在想 我每個單據都有相同欄位名稱的 我可不可以用OBJECT TYPE 來區分

日期：2026-07-24

**你的提問**

我在想 我每個單據都有相同欄位名稱的 我可不可以用OBJECT TYPE 來區分

**當時回答**

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

---

<a id="q3"></a>

## S024-Q003｜[@CHANGECFG]這個甚麼

日期：2026-07-24

**你的提問**

[@CHANGECFG]這個甚麼

**當時回答**

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

---

<a id="q4"></a>

## S024-Q004｜[dbo].[@FORM_NUM] T0 改這個

日期：2026-07-24

**你的提問**

[dbo].[@FORM_NUM]  T0 改這個

**當時回答**

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

---

<a id="q5"></a>

## S024-Q005｜給我完整的

日期：2026-07-24

**你的提問**

給我完整的

**當時回答**

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

---

<a id="q6"></a>

## S024-Q006｜[2026/7/24 下午 04:14] Nick: [dbo].[@FORM_NUM] T0 [2026/7/24 下午 04:18] Nick: Msg 207, Level …

日期：2026-07-24

**你的提問**

[2026/7/24 下午 04:14] Nick: [dbo].[@FORM_NUM]  T0
[2026/7/24 下午 04:18] Nick: Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1633 [Batch Start Line 7]
Invalid column name 'U_TypeName'.

**當時回答**

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

---

<a id="q7"></a>

## S024-Q007｜沒有卡住欸 可以先做小版本給我嗎 我只要收貨採購單

日期：2026-07-24

**你的提問**

沒有卡住欸 可以先做小版本給我嗎 我只要收貨採購單

**當時回答**

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

---

<a id="q8"></a>

## S024-Q008｜要有U_TYPENAME因為我主要就是要這個欄位要是對的

日期：2026-07-24

**你的提問**

要有U_TYPENAME因為我主要就是要這個欄位要是對的

**當時回答**

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

---

<a id="q9"></a>

## S024-Q009｜還是沒有卡住欸 可以成功新增

日期：2026-07-24

**你的提問**

還是沒有卡住欸 可以成功新增

**當時回答**

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

---

<a id="q10"></a>

## S024-Q010｜可以卡住

日期：2026-07-24

**你的提問**

可以卡住

**當時回答**

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

---

<a id="q11"></a>

## S024-Q011｜這個還是沒有

日期：2026-07-24

**你的提問**

這個還是沒有

**當時回答**

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

---

<a id="q12"></a>

## S024-Q012｜SQL／程式碼調整與說明

日期：2026-07-24

**你的提問**

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

**當時回答**

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

---

<a id="q13"></a>

## S024-Q013｜我再重新說明一下我的需求 採購單轉成收貨採購單時。 會把U_Typename的內容一起帶過來。 但我實際收貨採購單有自己的內容，但不會更新。 我有一個表格，有紀錄關於序列號不同 叫…

日期：2026-07-24

**你的提問**

我再重新說明一下我的需求

採購單轉成收貨採購單時。

會把U_Typename的內容一起帶過來。
但我實際收貨採購單有自己的內容，但不會更新。
我有一個表格，有紀錄關於序列號不同 叫做 U_SystemCode 他會記錄不同單據有不同的系列號。

所以我的想法是，既然會有的話 可以透過驗證後 利用屬於收貨採購單的series要等於 U_Typename的系統碼

**當時回答**

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

---

<a id="q14"></a>

## S024-Q014｜這個紀錄的表格叫 [@釐清收貨單自定義欄位_問題一](thread://019f92ca-e871-7292-b492-85cb3c1d1b42)

日期：2026-07-24

**你的提問**

〔此提問引用其他聊天，重複背景已省略；原聊天另見來源索引。〕

## My request for Codex:
這個紀錄的表格叫 [@釐清收貨單自定義欄位_問題一](thread://019f92ca-e871-7292-b492-85cb3c1d1b42)

**當時回答**

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

---

<a id="q15"></a>

## S024-Q015｜SERIES只會是數字

日期：2026-07-24

**你的提問**

SERIES只會是數字

**當時回答**

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

---

<a id="q16"></a>

## S024-Q016｜SQL／程式碼調整與說明

日期：2026-07-24

**你的提問**

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

**當時回答**

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

---

