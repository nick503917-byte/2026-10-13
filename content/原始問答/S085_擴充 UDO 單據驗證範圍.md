# 擴充 UDO 單據驗證範圍

來源編號：S085  
聊天 ID：`01a01434-7dcb-71a0-ad90-acb0368fd961`  
聊天起始日期（台北）：2026-08-18  
保存位置：一般本機紀錄  
問答段落：6

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S085-Q001｜SQL／程式碼調整與說明

日期：2026-08-18

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

**當時回答**

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

---

<a id="q2"></a>

## S085-Q002｜UNION ALL 的 用法

日期：2026-08-18

**你的提問**

UNION ALL 的 用法

**當時回答**

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

---

<a id="q3"></a>

## S085-Q003｜Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 2108 [Batch Sta…

日期：2026-08-19

**你的提問**

Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 2108 [Batch Start Line 7]
Invalid column name 'U_ChangeType'.
Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 2109 [Batch Start Line 7]
Invalid column name 'U_ChangeReason'.

**當時回答**

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

---

<a id="q4"></a>

## S085-Q004｜Msg 205, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1949 [Batch Sta…

日期：2026-08-19

**你的提問**

Msg 205, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1949 [Batch Start Line 7]
All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.

**當時回答**

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

---

<a id="q5"></a>

## S085-Q005｜生產收貨 生產發貨 庫存調撥 AR貸項 採購退貨 交貨單 採購單 AR發票 銷售訂單 採購請求 草稿也要卡控

日期：2026-08-19

**你的提問**

生產收貨 生產發貨 庫存調撥 AR貸項 採購退貨 交貨單 採購單 AR發票  銷售訂單 採購請求 草稿也要卡控

**當時回答**

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

---

<a id="q6"></a>

## S085-Q006｜好 給我一個完整的

日期：2026-08-19

**你的提問**

好 給我一個完整的

**當時回答**

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

---

