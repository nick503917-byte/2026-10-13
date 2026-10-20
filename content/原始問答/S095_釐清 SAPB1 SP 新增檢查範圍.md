# 釐清 SAPB1 SP 新增檢查範圍

來源編號：S095  
聊天 ID：`01a01e09-f090-7163-aab5-5c6c4401482c`  
聊天起始日期（台北）：2026-08-20  
保存位置：一般本機紀錄  
問答段落：9

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S095-Q001｜我的SAPB1 SP 如果我的SP是寫 我把欄位名稱寫進去 項目名稱裡面， 但如果我寫新增 他會檢查我所有的項目主檔嗎?

日期：2026-08-20

**你的提問**

我的SAPB1 SP 如果我的SP是寫 我把欄位名稱寫進去 項目名稱裡面， 但如果我寫新增 他會檢查我所有的項目主檔嗎?

**當時回答**

如果你指的是 SAP B1 的 `SBO_SP_TransactionNotification`：

- `@transaction_type = 'A'`（新增）只表示「目前正在新增一筆資料」。
- 它不會自動逐筆檢查所有項目主檔。
- 新增項目主檔時，SP 通常只被觸發一次，並可用 `@list_of_cols_val_tab_del` 取得目前新增項目的 `ItemCode`。

例如只檢查本次新增項目：

```sql
IF @object_type = '4' AND @transaction_type = 'A'
BEGIN
    IF EXISTS (
        SELECT 1
        FROM OITM
        WHERE ItemCode = @list_of_cols_val_tab_del
          AND ItemName LIKE '%欄位名稱%'
    )
    BEGIN
        SET @error = 1
        SET @error_message = N'項目名稱不可包含欄位名稱'
    END
END
```

但如果寫成：

```sql
IF EXISTS (
    SELECT 1
    FROM OITM
    WHERE ItemName LIKE '%欄位名稱%'
)
```

因為沒有用 `ItemCode = @list_of_cols_val_tab_del` 限定目前項目，SQL 就會搜尋整張 `OITM`；只要歷史資料中任何一筆符合，每次新增都可能被擋住。

所以結論是：**「新增」不會讓 SAP 自動檢查所有項目主檔；是否掃描全部資料，取決於你的查詢有沒有鎖定本次新增的 `ItemCode`。**

---

<a id="q2"></a>

## S095-Q002｜SQL／程式碼調整與說明

日期：2026-08-20

**你的提問**

IF @object_type = '4' AND @transaction_type IN ('A','U')
BEGIN
    UPDATE OITM
    SET
        OITM.ItemName =
            ISNULL(U_Seg1Desc,'') + ' '
          + ISNULL(U_Seg2Desc,'') + ' '
          + ISNULL(U_Seg3Desc,'') + ' '
          + ISNULL(U_Seg4Desc,'') + ' '
          + ISNULL(U_Seg5Desc,'') + ' '
          + ISNULL(U_Seg6Desc,'') + ' '
          + ISNULL(U_Seg7Desc,'') + ' '
          + ISNULL(U_Seg8Desc,'') + ' '
          + ISNULL(U_Seg9Desc,'') + ' '
          + ISNULL(U_Seg10Desc,'') + ' '
          + ISNULL(U_Seg11Desc,'') + ' '
          + ISNULL(U_Seg12Desc,'') + ' '
          + ISNULL(U_Seg13Desc,'')
    FROM OITM
    WHERE OITM.ItmsGrpCod = 103
END

這樣寫法 是不是會跑全部

**當時回答**

對，這樣會更新全部符合條件的項目主檔：

```sql
WHERE OITM.ItmsGrpCod = 103
```

每次新增或更新任何項目主檔時，都會重算 `OITM` 中所有 `ItmsGrpCod = 103` 的項目名稱。

若只想更新目前正在新增／修改的項目，應限定 `ItemCode`：

```sql
IF @object_type = '4' AND @transaction_type IN ('A','U')
BEGIN
    UPDATE OITM
    SET ItemName =
        RTRIM(
              ISNULL(U_Seg1Desc, '')  + ' '
            + ISNULL(U_Seg2Desc, '')  + ' '
            + ISNULL(U_Seg3Desc, '')  + ' '
            + ISNULL(U_Seg4Desc, '')  + ' '
            + ISNULL(U_Seg5Desc, '')  + ' '
            + ISNULL(U_Seg6Desc, '')  + ' '
            + ISNULL(U_Seg7Desc, '')  + ' '
            + ISNULL(U_Seg8Desc, '')  + ' '
            + ISNULL(U_Seg9Desc, '')  + ' '
            + ISNULL(U_Seg10Desc, '') + ' '
            + ISNULL(U_Seg11Desc, '') + ' '
            + ISNULL(U_Seg12Desc, '') + ' '
            + ISNULL(U_Seg13Desc, '')
        )
    WHERE ItmsGrpCod = 103
      AND ItemCode = @list_of_cols_val_tab_del;
END
```

這樣每次只會處理本次新增或更新的那個 `ItemCode`。

不過要注意：在 `SBO_SP_TransactionNotification` 裡直接 `UPDATE OITM`，可能帶來 SAP 支援性、鎖定或遞迴觸發方面的風險。這支 SP 一般較適合做檢查並回傳錯誤，而不是修改 SAP 系統表。若仍採用這種方式，至少務必加上 `ItemCode` 條件。

---

<a id="q3"></a>

## S095-Q003｜他還是會有處理到其他的

日期：2026-08-20

**你的提問**

他還是會有處理到其他的

**當時回答**

如果已經加上：

```sql
AND ItemCode = @list_of_cols_val_tab_del
```

這一段 SQL 理論上只可能更新一個 `ItemCode`。若其他項目仍被改到，通常代表 SP 裡還有舊的全表更新、另一支 Trigger／SP，或 `@list_of_cols_val_tab_del` 的使用位置不正確。

建議寫得更明確：

```sql
IF @object_type = '4'
   AND @transaction_type IN ('A', 'U')
BEGIN
    DECLARE @CurrentItemCode NVARCHAR(50);

    SET @CurrentItemCode =
        LTRIM(RTRIM(@list_of_cols_val_tab_del));

    UPDATE T0
       SET T0.ItemName =
           LTRIM(RTRIM(
               CONCAT(
                   NULLIF(T0.U_Seg1Desc,  ''), ' ',
                   NULLIF(T0.U_Seg2Desc,  ''), ' ',
                   NULLIF(T0.U_Seg3Desc,  ''), ' ',
                   NULLIF(T0.U_Seg4Desc,  ''), ' ',
                   NULLIF(T0.U_Seg5Desc,  ''), ' ',
                   NULLIF(T0.U_Seg6Desc,  ''), ' ',
                   NULLIF(T0.U_Seg7Desc,  ''), ' ',
                   NULLIF(T0.U_Seg8Desc,  ''), ' ',
                   NULLIF(T0.U_Seg9Desc,  ''), ' ',
                   NULLIF(T0.U_Seg10Desc, ''), ' ',
                   NULLIF(T0.U_Seg11Desc, ''), ' ',
                   NULLIF(T0.U_Seg12Desc, ''), ' ',
                   NULLIF(T0.U_Seg13Desc, '')
               )
           ))
    FROM OITM AS T0
    WHERE T0.ItemCode = @CurrentItemCode
      AND T0.ItmsGrpCod = 103;
END
```

先確認目前傳入的是哪個品號，可以暫時阻擋交易並顯示：

```sql
IF @object_type = '4'
   AND @transaction_type IN ('A', 'U')
BEGIN
    SET @error = -1;
    SET @error_message =
        N'目前處理 ItemCode：['
        + ISNULL(@list_of_cols_val_tab_del, N'NULL')
        + N']';
END
```

如果顯示的確實只有目前品號，其他項目仍被更新，請檢查：

```sql
-- 檢查資料庫中是否另有 OITM Trigger
SELECT
    tr.name,
    OBJECT_DEFINITION(tr.object_id) AS TriggerScript
FROM sys.triggers AS tr
WHERE tr.parent_id = OBJECT_ID('OITM');
```

也要在下列位置搜尋所有 `UPDATE OITM`：

- `SBO_SP_TransactionNotification`
- `SBO_SP_PostTransactionNotice`
- 其他自訂 Stored Procedure
- SQL Agent Job
- SAP Add-on 或排程程式

尤其確認原本這段沒有殘留在 SP 的其他位置：

```sql
UPDATE OITM
...
WHERE ItmsGrpCod = 103
```

只要還有一段未限制 `ItemCode`，每次執行仍然會處理群組 103 的全部項目。最直接的判斷方式是把你目前完整的 `SBO_SP_TransactionNotification` 貼出來；只看片段無法排除同一支 SP 其他位置又更新了一次。

---

<a id="q4"></a>

## S095-Q004｜這個已經是全部

日期：2026-08-20

**你的提問**

這個已經是全部

**當時回答**

那原因就確定了：你完整程式的條件只有：

```sql
WHERE OITM.ItmsGrpCod = 103
```

所以每次新增或修改一個項目時，都會更新群組 103 裡面的所有項目。必須再加上目前操作的品號：

```sql
IF @object_type = '4'
   AND @transaction_type IN ('A', 'U')
BEGIN
    UPDATE T0
    SET T0.ItemName =
        RTRIM(
              ISNULL(T0.U_Seg1Desc, '')  + ' '
            + ISNULL(T0.U_Seg2Desc, '')  + ' '
            + ISNULL(T0.U_Seg3Desc, '')  + ' '
            + ISNULL(T0.U_Seg4Desc, '')  + ' '
            + ISNULL(T0.U_Seg5Desc, '')  + ' '
            + ISNULL(T0.U_Seg6Desc, '')  + ' '
            + ISNULL(T0.U_Seg7Desc, '')  + ' '
            + ISNULL(T0.U_Seg8Desc, '')  + ' '
            + ISNULL(T0.U_Seg9Desc, '')  + ' '
            + ISNULL(T0.U_Seg10Desc, '') + ' '
            + ISNULL(T0.U_Seg11Desc, '') + ' '
            + ISNULL(T0.U_Seg12Desc, '') + ' '
            + ISNULL(T0.U_Seg13Desc, '')
        )
    FROM OITM AS T0
    WHERE T0.ItmsGrpCod = 103
      AND T0.ItemCode = @list_of_cols_val_tab_del;
END
```

關鍵就是這一行：

```sql
AND T0.ItemCode = @list_of_cols_val_tab_del
```

以項目主檔 `object_type = '4'` 而言，`@list_of_cols_val_tab_del` 就是這次新增或修改的 `ItemCode`。

因此：

- 原始版本：處理群組 103 的全部項目。
- 加上 `ItemCode` 條件後：只處理目前操作的項目。
- 如果目前項目不屬於群組 103：不會更新任何資料。

另外，你用空格串接時，中間欄位若是空白，項目名稱會出現連續空格；這不影響更新範圍，但可能讓名稱格式看起來不整齊。

---

<a id="q5"></a>

## S095-Q005｜一樣 會刷新這個項目群組的所有主黨

日期：2026-08-20

**你的提問**

一樣 會刷新這個項目群組的所有主黨

**當時回答**

如果已加上：

```sql
AND T0.ItemCode = @list_of_cols_val_tab_del
```

SQL Server 不可能由這一個 `UPDATE` 更新同群組的多個品號，因為 `ItemCode` 是唯一鍵。你看到的「全部刷新」，很可能只是 SAP B1 畫面重新載入整個項目群組，而不是資料庫把所有項目都 `UPDATE`。

要分清兩件事：

- 畫面刷新：SAP 重新查詢並顯示整個群組，屬正常現象。
- 資料被修改：其他品號的 `ItemName` 確實發生變化。

可以先直接測試 SQL 會命中幾筆：

```sql
DECLARE @ItemCode NVARCHAR(50) = N'你的測試品號';

SELECT ItemCode, ItemName
FROM OITM
WHERE ItmsGrpCod = 103
  AND ItemCode = @ItemCode;
```

結果最多只能一筆。

但核心問題是：不建議在 `SBO_SP_TransactionNotification` 中直接：

```sql
UPDATE OITM
```

這支 SP 是交易檢核用途。SAP 正在儲存項目主檔時，你又直接修改同一張 `OITM`，可能造成 SAP 用戶端重新整理、快取不同步或非預期行為。

比較穩定的做法是：

1. 在 SAP 畫面端用格式化搜尋或 Add-on，在儲存前組合 `ItemName`。
2. `SBO_SP_TransactionNotification` 只負責檢查項目名稱是否正確，不直接更新 `OITM`。
3. 若一定要資料庫端處理，可考慮儲存完成後的機制，但直接修改 SAP 系統表仍有支援風險。

例如 Transaction Notification 改成只檢查：

```sql
IF @object_type = '4'
   AND @transaction_type IN ('A', 'U')
BEGIN
    IF EXISTS
    (
        SELECT 1
        FROM OITM AS T0
        WHERE T0.ItemCode = @list_of_cols_val_tab_del
          AND T0.ItmsGrpCod = 103
          AND ISNULL(T0.ItemName, '') <>
              RTRIM(
                    ISNULL(T0.U_Seg1Desc, '')  + ' '
                  + ISNULL(T0.U_Seg2Desc, '')  + ' '
                  + ISNULL(T0.U_Seg3Desc, '')  + ' '
                  + ISNULL(T0.U_Seg4Desc, '')  + ' '
                  + ISNULL(T0.U_Seg5Desc, '')  + ' '
                  + ISNULL(T0.U_Seg6Desc, '')  + ' '
                  + ISNULL(T0.U_Seg7Desc, '')  + ' '
                  + ISNULL(T0.U_Seg8Desc, '')  + ' '
                  + ISNULL(T0.U_Seg9Desc, '')  + ' '
                  + ISNULL(T0.U_Seg10Desc, '') + ' '
                  + ISNULL(T0.U_Seg11Desc, '') + ' '
                  + ISNULL(T0.U_Seg12Desc, '') + ' '
                  + ISNULL(T0.U_Seg13Desc, '')
              )
    )
    BEGIN
        SET @error = -1;
        SET @error_message = N'項目名稱與規格欄位組合不一致';
    END
END
```

所以，如果你所說的是「SAP 畫面整群重新整理」，加 `ItemCode` 也不一定能阻止畫面刷新；但若是「其他品號名稱也實際變更」，那一定還有批次操作、Add-on、格式化搜尋或其他資料庫程序介入，並非上述限定 `ItemCode` 的 SQL 一次更新多筆。

---

<a id="q6"></a>

## S095-Q006｜資料被修改：其他品號的 ItemName 確實發生變化。

日期：2026-08-20

**你的提問**

資料被修改：其他品號的 ItemName 確實發生變化。

**當時回答**

那就不是「畫面刷新」，而是確實有多次更新發生。只要 SQL 已經有：

```sql
AND T0.ItemCode = @list_of_cols_val_tab_del
```

單次執行最多更新一個品號；最可能的情況是 SAP／Add-on 在同一次操作中，對群組內每個品號分別呼叫一次 `transaction_type = 'U'`。

可以用紀錄表確認 SP 到底被呼叫幾次。

先建立暫時的除錯表：

```sql
CREATE TABLE dbo.Z_TN_ItemDebug
(
    LogID            INT IDENTITY(1,1) PRIMARY KEY,
    LogTime          DATETIME2 DEFAULT SYSDATETIME(),
    ObjectType       NVARCHAR(30),
    TransactionType  NCHAR(1),
    ItemCode         NVARCHAR(255),
    LoginName        NVARCHAR(128) DEFAULT ORIGINAL_LOGIN(),
    HostName         NVARCHAR(128) DEFAULT HOST_NAME(),
    AppName          NVARCHAR(128) DEFAULT APP_NAME()
);
```

然後在項目主檔判斷內暫時加入：

```sql
IF @object_type = '4'
   AND @transaction_type IN ('A', 'U')
BEGIN
    INSERT INTO dbo.Z_TN_ItemDebug
    (
        ObjectType,
        TransactionType,
        ItemCode
    )
    VALUES
    (
        @object_type,
        @transaction_type,
        @list_of_cols_val_tab_del
    );

    UPDATE T0
       SET T0.ItemName =
           RTRIM(
                 ISNULL(T0.U_Seg1Desc, '')  + ' '
               + ISNULL(T0.U_Seg2Desc, '')  + ' '
               + ISNULL(T0.U_Seg3Desc, '')  + ' '
               + ISNULL(T0.U_Seg4Desc, '')  + ' '
               + ISNULL(T0.U_Seg5Desc, '')  + ' '
               + ISNULL(T0.U_Seg6Desc, '')  + ' '
               + ISNULL(T0.U_Seg7Desc, '')  + ' '
               + ISNULL(T0.U_Seg8Desc, '')  + ' '
               + ISNULL(T0.U_Seg9Desc, '')  + ' '
               + ISNULL(T0.U_Seg10Desc, '') + ' '
               + ISNULL(T0.U_Seg11Desc, '') + ' '
               + ISNULL(T0.U_Seg12Desc, '') + ' '
               + ISNULL(T0.U_Seg13Desc, '')
           )
    FROM OITM AS T0
    WHERE T0.ItemCode = @list_of_cols_val_tab_del
      AND T0.ItmsGrpCod = 103;
END
```

操作一次後查詢：

```sql
SELECT *
FROM dbo.Z_TN_ItemDebug
ORDER BY LogID;
```

判讀方式：

- 出現很多不同 `ItemCode`：SAP、Add-on、批次更新或格式化搜尋正在逐筆更新群組內的品號，因此 SP 也逐筆執行。
- 只出現一個 `ItemCode`，其他品號名稱卻仍被改：資料庫內還有 Trigger、其他程序或 SQL Job。

可再搜尋資料庫內所有涉及 `OITM` 的程式：

```sql
SELECT
    SCHEMA_NAME(O.schema_id) AS SchemaName,
    O.name AS ObjectName,
    O.type_desc,
    M.definition
FROM sys.sql_modules AS M
JOIN sys.objects AS O
  ON O.object_id = M.object_id
WHERE M.definition LIKE '%OITM%'
ORDER BY O.type_desc, O.name;
```

也檢查 `OITM` Trigger：

```sql
SELECT
    T.name,
    OBJECT_DEFINITION(T.object_id) AS TriggerDefinition
FROM sys.triggers AS T
WHERE T.parent_id = OBJECT_ID('dbo.OITM');
```

重點是：如果除錯表記錄了多個品號，問題不是 `WHERE` 失效，而是外部操作對多個項目各自送出了一次更新。這種情況下，應找出是哪個 Add-on、格式化搜尋或批次功能在逐筆儲存。除錯完成後再移除紀錄程式及 `Z_TN_ItemDebug` 表。

---

<a id="q7"></a>

## S095-Q007｜我是在postTransactionNotification

日期：2026-08-20

**你的提問**

我是在postTransactionNotification

**當時回答**

了解，你是在 `SBO_SP_PostTransactionNotice`。這支 SP 是 SAP 完成交易後才執行，因此可以看到已儲存的 `OITM` 資料；但你原本的條件仍然會更新整個群組：

```sql
WHERE OITM.ItmsGrpCod = 103
```

在 `PostTransactionNotice` 中，正確限定本次品號可這樣寫：

```sql
IF @object_type = '4'
   AND @transaction_type IN ('A', 'U')
BEGIN
    DECLARE @ItemCode NVARCHAR(50);

    SET @ItemCode =
        CONVERT(NVARCHAR(50), @list_of_cols_val_tab_del);

    UPDATE T0
       SET T0.ItemName =
           RTRIM(
                 ISNULL(T0.U_Seg1Desc, '')  + ' '
               + ISNULL(T0.U_Seg2Desc, '')  + ' '
               + ISNULL(T0.U_Seg3Desc, '')  + ' '
               + ISNULL(T0.U_Seg4Desc, '')  + ' '
               + ISNULL(T0.U_Seg5Desc, '')  + ' '
               + ISNULL(T0.U_Seg6Desc, '')  + ' '
               + ISNULL(T0.U_Seg7Desc, '')  + ' '
               + ISNULL(T0.U_Seg8Desc, '')  + ' '
               + ISNULL(T0.U_Seg9Desc, '')  + ' '
               + ISNULL(T0.U_Seg10Desc, '') + ' '
               + ISNULL(T0.U_Seg11Desc, '') + ' '
               + ISNULL(T0.U_Seg12Desc, '') + ' '
               + ISNULL(T0.U_Seg13Desc, '')
           )
    FROM dbo.OITM AS T0
    WHERE T0.ItemCode = @ItemCode
      AND T0.ItmsGrpCod = 103;
END
```

單次執行這段，最多只能修改一個 `ItemCode`。

但 `PostTransactionNotice` 有一個重要特性：如果 SAP、DTW 或 Add-on 一次批次修改很多項目，SAP 會針對每個項目分別呼叫它。結果看起來就像整個群組都被刷新，實際上是 SP 被呼叫很多次，每次更新一個品號。

可以直接在 `PostTransactionNotice` 暫時加入紀錄：

```sql
INSERT INTO dbo.Z_TN_ItemDebug
(
    LogTime,
    ObjectType,
    TransactionType,
    ItemCode
)
VALUES
(
    GETDATE(),
    @object_type,
    @transaction_type,
    @list_of_cols_val_tab_del
);
```

若操作一次後紀錄為：

```text
U  A001
U  A002
U  A003
U  A004
```

代表 SAP 送出了多筆更新，並非同一條 `UPDATE` 更新全部。

若紀錄只有：

```text
U  A001
```

但 A002、A003 也被改了，則目前實際執行的 SP 很可能仍保留舊條件，或另有 Trigger／Add-on。可先確認資料庫中真正的 SP 內容：

```sql
EXEC sp_helptext 'SBO_SP_PostTransactionNotice';
```

最關鍵的是，實際執行的版本必須同時有：

```sql
WHERE T0.ItemCode = @ItemCode
  AND T0.ItmsGrpCod = 103
```

而不能只留下 `ItmsGrpCod = 103`。

---

<a id="q8"></a>

## S095-Q008｜SQL／程式碼調整與說明

日期：2026-08-20

**你的提問**

--項目主檔建立更新紀錄填入--
if @object_type in('4') and (@transaction_type IN('A','U'))
BEGIN 
update T0
SET 
T0.U_CreateUser = T1.U_NAME,
T0.U_CreateDate = T0.CreateDate,
T0.U_UpdateUser = T2.U_NAME,
T0.U_UpdateDate = T0.UpdateDate
FROM OITM T0
LEFT JOIN OUSR T1 ON T0.UserSign = T1.USERID
LEFT JOIN OUSR T2 ON T0.UserSign2 = T1.USERID
WHERE T0.ItemCode = @list_of_cols_val_tab_del 
END

這個嗎

**當時回答**

這一段不會更新整個項目群組，因為已經限定：

```sql
WHERE T0.ItemCode = @list_of_cols_val_tab_del
```

所以它只會處理本次新增或更新的項目。

不過程式裡有一個 JOIN 寫錯了：

```sql
LEFT JOIN OUSR T2 ON T0.UserSign2 = T1.USERID
```

右邊應該是 `T2.USERID`：

```sql
LEFT JOIN OUSR T2 ON T0.UserSign2 = T2.USERID
```

另外，目前新增與更新都會重寫 `U_CreateUser`、`U_CreateDate`，導致原始建立紀錄可能在每次更新時被覆蓋。建議拆開：

```sql
-- 項目主檔：新增紀錄
IF @object_type = '4'
   AND @transaction_type = 'A'
BEGIN
    UPDATE T0
       SET T0.U_CreateUser = T1.U_NAME,
           T0.U_CreateDate = T0.CreateDate,
           T0.U_UpdateUser = T2.U_NAME,
           T0.U_UpdateDate = T0.UpdateDate
    FROM OITM AS T0
    LEFT JOIN OUSR AS T1
      ON T0.UserSign = T1.USERID
    LEFT JOIN OUSR AS T2
      ON T0.UserSign2 = T2.USERID
    WHERE T0.ItemCode = @list_of_cols_val_tab_del;
END;

-- 項目主檔：更新紀錄
IF @object_type = '4'
   AND @transaction_type = 'U'
BEGIN
    UPDATE T0
       SET T0.U_UpdateUser = T2.U_NAME,
           T0.U_UpdateDate = T0.UpdateDate
    FROM OITM AS T0
    LEFT JOIN OUSR AS T2
      ON T0.UserSign2 = T2.USERID
    WHERE T0.ItemCode = @list_of_cols_val_tab_del;
END;
```

結論：

- 這段只更新目前的 `ItemCode`，不是造成群組 103 全部 `ItemName` 改變的原因。
- `T2` 的 JOIN 原本寫錯，可能導致更新者名稱不正確或为空。
- 造成全部 `ItemName` 改變的，仍是原本只有以下條件的程式：

```sql
WHERE OITM.ItmsGrpCod = 103
```

`ItemName` 那一段必須同時限制：

```sql
WHERE T0.ItmsGrpCod = 103
  AND T0.ItemCode = @list_of_cols_val_tab_del
```

---

<a id="q9"></a>

## S095-Q009｜我目前寫的 當我新增或更新項目主檔，其他相同項目群組的也會影響到 幫我查看官方文件

日期：2026-08-21

**你的提問**

我目前寫的 當我新增或更新項目主檔，其他相同項目群組的也會影響到 幫我查看官方文件

**當時回答**

查過 SAP 官方資料後，可以確定：`SBO_SP_PostTransactionNotice` 不會因為項目群組相同，就自動處理該群組所有項目。造成全部 `ItemName` 被修改的是你原本 SQL 的篩選條件。

SAP 說明這些參數：

- `@object_type = '4'`：項目主檔。
- `@transaction_type = 'A' / 'U'`：本次新增／更新。
- `@list_of_key_cols_tab_del`：物件主鍵欄位；項目主檔是 `ItemCode`。
- `@list_of_cols_val_tab_del`：本次處理物件的主鍵值，也就是目前的 `ItemCode`。

SAP 官方文章也說明，這個參數是「用來取回本次物件的鍵值清單」，而不是項目群組：[SAP—The SP_TransactionNotification Stored Procedure](https://community.sap.com/t5/additional-blog-posts-by-sap/the-sp-transactionnotification-stored-procedure/bc-p/12908703/highlight/true)。

SAP 官方管理指南確認 `SBO_SP_PostTransactionNotice` 是公司資料庫內的標準程序：[SAP Business One Administrator’s Guide](https://help.sap.com/doc/f971ebb2d51940f597f9e746d5aa019b/10.0_SP_2605/en-US/9a2e1e4e14b3486099852acf7a8bcd84.pdf)。

你的真正原因是這段：

```sql
UPDATE OITM
SET ItemName = ...
WHERE OITM.ItmsGrpCod = 103
```

這個 `WHERE` 的意思就是：

> 更新 `OITM` 中所有 `ItmsGrpCod = 103` 的資料。

因此，每次任一項目新增／更新，`PostTransactionNotice` 被呼叫時，群組 103 的所有項目名稱都會重新組合一次。

應該改成以下完整版本：

```sql
IF @object_type = '4'
   AND @transaction_type IN ('A', 'U')
BEGIN
    DECLARE @CurrentItemCode NVARCHAR(50);

    SET @CurrentItemCode =
        CONVERT(NVARCHAR(50), @list_of_cols_val_tab_del);

    UPDATE T0
       SET T0.ItemName =
           RTRIM(
                 ISNULL(T0.U_Seg1Desc, '')  + ' '
               + ISNULL(T0.U_Seg2Desc, '')  + ' '
               + ISNULL(T0.U_Seg3Desc, '')  + ' '
               + ISNULL(T0.U_Seg4Desc, '')  + ' '
               + ISNULL(T0.U_Seg5Desc, '')  + ' '
               + ISNULL(T0.U_Seg6Desc, '')  + ' '
               + ISNULL(T0.U_Seg7Desc, '')  + ' '
               + ISNULL(T0.U_Seg8Desc, '')  + ' '
               + ISNULL(T0.U_Seg9Desc, '')  + ' '
               + ISNULL(T0.U_Seg10Desc, '') + ' '
               + ISNULL(T0.U_Seg11Desc, '') + ' '
               + ISNULL(T0.U_Seg12Desc, '') + ' '
               + ISNULL(T0.U_Seg13Desc, '')
           )
    FROM dbo.OITM AS T0
    WHERE T0.ItemCode = @CurrentItemCode
      AND T0.ItmsGrpCod = 103;
END;
```

你另外貼的建立／更新紀錄程式：

```sql
WHERE T0.ItemCode = @list_of_cols_val_tab_del
```

只會修改目前品號，不會修改整個群組。不過其中有一個筆誤：

```sql
LEFT JOIN OUSR T2
  ON T0.UserSign2 = T1.USERID
```

應改為：

```sql
LEFT JOIN OUSR T2
  ON T0.UserSign2 = T2.USERID
```

最終判斷：

- `PostTransactionNotice` 本身不會自動展開到同項目群組。
- `@list_of_cols_val_tab_del` 對項目主檔而言，是本次 `ItemCode`。
- 其他同群組項目被修改，是因為 `ItemName` 更新原先只寫了 `ItmsGrpCod = 103`。
- 必須同時加入 `ItemCode = @list_of_cols_val_tab_del`。
- SAP 社群中的支援說明也提醒，直接更新 `OITM` 等系統表不符合一般支援政策，較正式的修改方式應使用 SAP Business One DI API／Service Layer；可參考 [SAP Community—Updating ItemCode in OITM](https://community.sap.com/t5/enterprise-resource-planning-q-a/updating-itemcode-in-oitm-item-master-table/qaq-p/7822087)。

---

