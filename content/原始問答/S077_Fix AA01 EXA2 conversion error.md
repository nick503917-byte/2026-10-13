# Fix AA01 EXA2 conversion error

來源編號：S077  
聊天 ID：`019ff9b8-a128-7cd1-b407-fea28995834f`  
聊天起始日期（台北）：2026-08-13  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S077-Q001｜1 Created Failed AA01 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion fai…

日期：2026-08-13

**你的提問**

1 Created Failed AA01  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion failed when converting the nvarchar value 'EXA2' to data type int. (CINF)UDO_RAWITEMCREA

**當時回答**

這是 SQL 型別不符：

`UDO_RAWITEMCREA` 建立資料時，某個 `int` 欄位收到文字值 `'EXA2'`，因此轉換失敗。`CINF` 很可能是發生錯誤的資料表或程序位置。

優先檢查：

1. `UDO_RAWITEMCREA` 的欄位定義，找出型別為 `int` 的欄位。
2. 新增資料的欄位順序是否錯位，例如漏寫欄位名稱，導致 `'EXA2'` 被塞入整數欄位。
3. SQL 中是否有：
   - `CAST(... AS INT)`／`CONVERT(INT, ...)`
   - `CASE` 同時回傳數字與文字
   - 整數欄位和文字欄位做 `JOIN` 或比較
4. 若 `'EXA2'` 是合法代碼，目標欄位應改為 `nvarchar`，或修正 mapping，寫入正確的文字欄位。

可先用以下 SQL 找相關欄位型別：

```sql
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    DATA_TYPE,
    CHARACTER_MAXIMUM_LENGTH
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME LIKE '%RAWITEMCREA%'
   OR TABLE_NAME = 'CINF'
ORDER BY TABLE_NAME, ORDINAL_POSITION;
```

如果是 SAP Business One UDO，請特別比對 `U_` 使用者欄位的實際 SQL 型別與程式設定；不要直接修改 SAP 系統表。把造成錯誤的 `INSERT/UPDATE` SQL、UDO 欄位清單或完整匯入資料貼上來，我可以直接定位是哪一欄。

---

<a id="q2"></a>

## S077-Q002｜1 Created Failed AA01 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion fai…

日期：2026-08-13

**你的提問**

1 Created Failed AA01  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion failed when converting the nvarchar value 'EXA2' to data type int. (CINF)UDO_RAWITEMCREA 

我現在無法建立

**當時回答**

已確認：錯誤來源幾乎可以鎖定在公司資料庫的 `SBO_SP_TransactionNotification`，不是 `UDO_RAWITEMCREA` 欄位本身。

`EXA2` 是 UDO 的文字 `object_type`，但該預存程序把 `@object_type` 當成 `int`，或自訂卡控 SQL 強制將它轉成整數，所以 UDO 無法建立。SAP 也有相同錯誤的官方案例：[SAP KBA 3495039](https://userapps.support.sap.com/sap/support/knowledge/en/3495039)。

先在出錯的公司資料庫執行：

```sql
SELECT
    p.name AS ParameterName,
    TYPE_NAME(p.user_type_id) AS DataType,
    p.max_length
FROM sys.parameters p
WHERE p.object_id = OBJECT_ID('dbo.SBO_SP_TransactionNotification')
ORDER BY p.parameter_id;
```

正常應類似：

```text
@object_type                 nvarchar
@transaction_type            nchar
@num_of_cols_in_key          int
@list_of_key_cols_tab_del    nvarchar
@list_of_cols_val_tab_del    nvarchar
```

如果 `@object_type` 顯示為 `int`，就必須將程序開頭修正為：

```sql
ALTER PROCEDURE [dbo].[SBO_SP_TransactionNotification]
    @object_type NVARCHAR(30),
    @transaction_type NCHAR(1),
    @num_of_cols_in_key INT,
    @list_of_key_cols_tab_del NVARCHAR(255),
    @list_of_cols_val_tab_del NVARCHAR(255)
AS
BEGIN
    -- 保留原本程序內容
END
```

不要直接只執行上面範例，否則可能覆蓋原有卡控。應先備份完整程序：

```sql
EXEC sp_helptext 'dbo.SBO_SP_TransactionNotification';
```

如果參數已經是 `nvarchar`，則問題在程序內的自訂判斷。搜尋以下寫法：

```sql
CAST(@object_type AS INT)
CONVERT(INT, @object_type)
ISNULL(@object_type, 0)
@object_type IN (13, 18, 20)
```

建議一律使用文字比較：

```sql
IF @object_type = N'13'
```

或：

```sql
IF @object_type IN (N'13', N'18', N'EXA2')
```

尤其不要寫：

```sql
IF @object_type IN (13, 18, N'EXA2')
```

因為數字與文字混用可能觸發 SQL Server 的隱含整數轉換。

修正 `SBO_SP_TransactionNotification` 後，再重新建立 `AA01 / UDO_RAWITEMCREA`。不要修改 `CINF` 系統表。

---

