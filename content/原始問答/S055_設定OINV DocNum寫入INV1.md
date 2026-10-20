# 設定OINV DocNum寫入INV1

來源編號：S055  
聊天 ID：`019fd62b-f5db-7563-92e0-4eb452503d09`  
聊天起始日期（台北）：2026-08-06  
保存位置：一般本機紀錄  
問答段落：13

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S055-Q001｜SAPB1 OINV的DocNum 當我正式新增 OINV的時候，新增的同時 我想要抓 OINV的DocNum這個數字到 INV1 U_InvoiceNo

日期：2026-08-06

**你的提問**

SAPB1 OINV的DocNum

當我正式新增 OINV的時候，新增的同時 我想要抓  OINV的DocNum這個數字到 INV1 U_InvoiceNo

**當時回答**

可以做到。最簡單的方法是用「格式化搜尋」，在新增 A/R Invoice 前，把畫面上的 `DocNum` 自動帶入每一筆 `INV1.U_InvoiceNo`。

SQL Server 查詢：

```sql
SELECT $[$8.0.0]
```

SAP HANA 查詢：

```sql
SELECT $[$8.0.0] FROM DUMMY
```

設定方式：

1. 建立上述使用者查詢。
2. 開啟 A/R Invoice。
3. 游標放在明細欄位 `U_InvoiceNo`。
4. 按 `Shift + F2`。
5. 指定剛建立的查詢。
6. 設定「自動重新整理」。
7. 觸發欄位選擇文件號碼 `DocNum`。

一般 A/R Invoice 畫面的 `DocNum Item UID` 是 `8`；可開啟「檢視 → 系統資訊」，把滑鼠移到文件號碼欄位確認。

如果是透過 DI API 新增，因為正式 `DocNum` 是執行 `Add()` 後才由 SAP 配號，所以需要新增後再更新：

```csharp
invoice.Add();

string docEntry = company.GetNewObjectKey();

var savedInvoice = (SAPbobsCOM.Documents)
    company.GetBusinessObject(SAPbobsCOM.BoObjectTypes.oInvoices);

savedInvoice.GetByKey(int.Parse(docEntry));

string docNum = savedInvoice.DocNum.ToString();

for (int i = 0; i < savedInvoice.Lines.Count; i++)
{
    savedInvoice.Lines.SetCurrentLine(i);
    savedInvoice.Lines.UserFields.Fields
        .Item("U_InvoiceNo").Value = docNum;
}

savedInvoice.Update();
```

不要用 `MAX(DocNum) + 1` 或读取 `NNM1.AutoKey`，多人同时新增时可能取得错误号码。也不建议直接用 SQL `UPDATE INV1`，应通过格式化搜尋、DI API 或 Service Layer 修改，以免绕过 SAP Business One 的业务逻辑。SAP 官方也说明，非手工编号文件的 `DocNum` 是新增时由编号系统自动分配的：[Documents.DocNum](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~Documents_members.html)、[Formatted Searches](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~FormattedSearches.html)。

---

<a id="q2"></a>

## S055-Q002｜不能寫在PostTransactionNotice

日期：2026-08-06

**你的提問**

不能寫在PostTransactionNotice

**當時回答**

技術上可以寫在 `SBO_SP_PostTransactionNotice`，因為這時 `OINV.DocNum` 已經正式產生。

SQL Server 範例：

```sql
IF @object_type = '13'
   AND @transaction_type = 'A'
BEGIN
    UPDATE T1
       SET T1.U_InvoiceNo = CONVERT(NVARCHAR(20), T0.DocNum)
    FROM INV1 T1
    INNER JOIN OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T0.DocEntry = TRY_CONVERT(
              INT,
              @list_of_cols_val_tab_del
          )
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
```

SAP HANA 範例：

```sql
IF :object_type = '13'
   AND :transaction_type = 'A'
THEN

    UPDATE "INV1"
       SET "U_InvoiceNo" =
       (
           SELECT TO_NVARCHAR(T0."DocNum")
           FROM "OINV" T0
           WHERE T0."DocEntry" =
                 TO_INT(:list_of_cols_val_tab_del)
       )
     WHERE "DocEntry" =
           TO_INT(:list_of_cols_val_tab_del)
       AND IFNULL("U_InvoiceNo", '') = '';

END IF;
```

其中：

- `object_type = '13'`：A/R Invoice
- `transaction_type = 'A'`：新增
- `list_of_cols_val_tab_del`：新增发票的 `DocEntry`
- PostTransactionNotice 执行时，`OINV.DocNum` 已经是正式号码

但要注意：这属于直接 `UPDATE INV1`。即使只更新 UDF，`INV1` 仍然是 SAP 系统表，SAP 官方明确表示直接对系统表执行 DML 不受支持，可能影响原厂支持：[SAP Raw Data Access Objects](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/38f011a207dd4792b0ca0a28054fccb5.html)。

另外，`PostTransactionNotice` 已经在原交易完成之后：

- 更新失败不能阻止发票新增。
- `U_InvoiceNo` 的写入不是原新增交易的一部分。
- SQL 直接更新不会经过 DI API 的业务逻辑。
- 不会产生正常的 SAP 文件更新事件与变更记录。

所以结论是：

- 只问「能不能」：可以，上面的 SQL 能做。
- 要 SAP 正式支持：新增后通过 DI API／Service Layer 再更新文件。
- 如果只从 SAP 客户端手工新增：优先使用格式化搜尋，在按下新增前把画面上的 `DocNum` 带入明细。

---

<a id="q3"></a>

## S055-Q003｜我想要直接寫一隻完整的程序，然後我在PostTransactionNotice 把這支程序execute就好

日期：2026-08-06

**你的提問**

我想要直接寫一隻完整的程序，然後我在PostTransactionNotice 把這支程序execute就好

**當時回答**

可以。以下先以 SAP Business One on SQL Server 为例：建立一支独立预存程序，再由 `SBO_SP_PostTransactionNotice` 调用。

### 1. 建立独立预存程序

```sql
USE [你的SAP公司数据库];
GO

CREATE PROCEDURE [dbo].[SP_Set_INV1_InvoiceNo]
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    IF ISNULL(@DocEntry, 0) <= 0
        RETURN;

    UPDATE T1
       SET T1.U_InvoiceNo = CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 T1
    INNER JOIN dbo.OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
GO
```

如果希望每次都强制覆盖 `U_InvoiceNo`，移除这一行：

```sql
AND ISNULL(T1.U_InvoiceNo, '') = ''
```

### 2. 修改 PostTransactionNotice

打开：

```sql
ALTER PROCEDURE [dbo].[SBO_SP_PostTransactionNotice]
```

在程序接近结尾的位置、最终 `SELECT` 或 `END` 之前加入：

```sql
/* A/R Invoice 新增后，将 DocNum 写入 INV1.U_InvoiceNo */
IF @object_type = '13'
   AND @transaction_type = 'A'
BEGIN
    DECLARE @InvoiceDocEntry INT;

    SET @InvoiceDocEntry =
        TRY_CONVERT(INT, @list_of_cols_val_tab_del);

    IF ISNULL(@InvoiceDocEntry, 0) > 0
    BEGIN
        EXEC dbo.SP_Set_INV1_InvoiceNo
            @DocEntry = @InvoiceDocEntry;
    END;
END;
```

整体结构大致如下：

```sql
ALTER PROCEDURE [dbo].[SBO_SP_PostTransactionNotice]
(
    @object_type NVARCHAR(30),
    @transaction_type NCHAR(1),
    @num_of_cols_in_key INT,
    @list_of_key_cols_tab_del NVARCHAR(255),
    @list_of_cols_val_tab_del NVARCHAR(255)
)
AS
BEGIN
    SET NOCOUNT ON;

    /* SAP 或其他既有程序内容，请保留 */

    IF @object_type = '13'
       AND @transaction_type = 'A'
    BEGIN
        DECLARE @InvoiceDocEntry INT;

        SET @InvoiceDocEntry =
            TRY_CONVERT(INT, @list_of_cols_val_tab_del);

        IF ISNULL(@InvoiceDocEntry, 0) > 0
        BEGIN
            EXEC dbo.SP_Set_INV1_InvoiceNo
                @DocEntry = @InvoiceDocEntry;
        END;
    END;
END;
GO
```

### 3. 测试现有发票

可以先找一张测试发票：

```sql
DECLARE @DocEntry INT = 12345;

EXEC dbo.SP_Set_INV1_InvoiceNo
    @DocEntry = @DocEntry;

SELECT
    T0.DocEntry,
    T0.DocNum,
    T1.LineNum,
    T1.U_InvoiceNo
FROM OINV T0
INNER JOIN INV1 T1
    ON T1.DocEntry = T0.DocEntry
WHERE T0.DocEntry = @DocEntry;
```

注意：`@list_of_cols_val_tab_del` 传入的是 `DocEntry`，不是 `DocNum`。程序会用 `DocEntry` 查询正式的 `OINV.DocNum`，再写入全部发票明细。

这项做法能够运行，但仍属于直接更新 SAP 系统表 `INV1`，SAP 官方不支持此类 DML；正式环境部署前应先备份并在测试数据库验证。

---

<a id="q4"></a>

## S055-Q004｜建立独立预存程序 怎麼用 然後不要再出現簡體中文了

日期：2026-08-06

**你的提問**

建立独立预存程序 怎麼用 然後不要再出現簡體中文了

**當時回答**

抱歉，後續我會全程使用繁體中文。

以下以 SAP Business One + SQL Server 為例。「建立獨立預存程序」的意思是：先在公司資料庫建立一個可重複呼叫的 SQL 程序，之後由 `SBO_SP_PostTransactionNotice` 執行它。

### 一、建立預存程序

開啟 SQL Server Management Studio（SSMS）：

1. 連線至 SAP Business One 的 SQL Server。
2. 點選你的 SAP 公司資料庫。
3. 按「新增查詢」。
4. 貼上以下程式。
5. 將 `[你的公司資料庫]` 改成實際資料庫名稱。
6. 按「執行」。

```sql
USE [你的公司資料庫];
GO

CREATE PROCEDURE dbo.usp_SetINV1InvoiceNo
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    IF ISNULL(@DocEntry, 0) <= 0
        RETURN;

    UPDATE T1
       SET T1.U_InvoiceNo = CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 T1
    INNER JOIN dbo.OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
GO
```

例如你的公司資料庫叫做 `SBODemoTW`：

```sql
USE [SBODemoTW];
GO
```

執行成功後，可以在 SSMS 找到：

```text
資料庫
└─ SBODemoTW
   └─ 可程式性
      └─ 預存程序
         └─ dbo.usp_SetINV1InvoiceNo
```

如果沒有看到，對「預存程序」按右鍵並選擇「重新整理」。

### 二、手動測試預存程序

先找一張測試發票：

```sql
SELECT TOP 10
    DocEntry,
    DocNum,
    CardCode,
    CardName
FROM OINV
ORDER BY DocEntry DESC;
```

假設要測試的 `DocEntry` 是 `12345`，執行：

```sql
EXEC dbo.usp_SetINV1InvoiceNo
    @DocEntry = 12345;
```

接著檢查結果：

```sql
SELECT
    T0.DocEntry,
    T0.DocNum,
    T1.LineNum,
    T1.U_InvoiceNo
FROM OINV T0
INNER JOIN INV1 T1
    ON T1.DocEntry = T0.DocEntry
WHERE T0.DocEntry = 12345
ORDER BY T1.LineNum;
```

如果成功，每一筆 `INV1.U_InvoiceNo` 都會等於 `OINV.DocNum`。

### 三、在 PostTransactionNotice 呼叫

在 SSMS 執行：

```sql
USE [你的公司資料庫];
GO

EXEC sp_helptext 'dbo.SBO_SP_PostTransactionNotice';
```

取得原本內容後，使用：

```sql
ALTER PROCEDURE dbo.SBO_SP_PostTransactionNotice
```

在原本程序最後一個 `END` 之前加入：

```sql
IF @object_type = '13'
   AND @transaction_type = 'A'
BEGIN
    DECLARE @InvoiceDocEntry INT;

    SET @InvoiceDocEntry =
        TRY_CONVERT(INT, @list_of_cols_val_tab_del);

    IF ISNULL(@InvoiceDocEntry, 0) > 0
    BEGIN
        EXEC dbo.usp_SetINV1InvoiceNo
            @DocEntry = @InvoiceDocEntry;
    END;
END;
```

其中：

- `13`：A/R 發票，也就是 `OINV`
- `A`：新增
- `@list_of_cols_val_tab_del`：新增發票的 `DocEntry`

之後每次新增 A/R 發票，流程就是：

```text
新增 OINV
→ SAP 產生正式 DocNum
→ 執行 SBO_SP_PostTransactionNotice
→ 呼叫 dbo.usp_SetINV1InvoiceNo
→ 將 DocNum 寫入所有 INV1.U_InvoiceNo
```

如果執行 `CREATE PROCEDURE` 時出現「物件已存在」，代表程序已經建立過，將：

```sql
CREATE PROCEDURE
```

改成：

```sql
ALTER PROCEDURE
```

再重新執行即可。

---

<a id="q5"></a>

## S055-Q005｜我在 〔程式碼〕

日期：2026-08-06

**你的提問**

我在
```
預存程序 沒有看到
```

**當時回答**

先不要看左側目錄，直接用 SQL 確認預存程序是否建立成功。請在 SSMS 執行：

```sql
SELECT
    DB_NAME() AS 目前資料庫,
    SCHEMA_NAME(schema_id) AS SchemaName,
    name AS ProcedureName,
    create_date,
    modify_date
FROM sys.procedures
WHERE name = 'usp_SetINV1InvoiceNo';
```

### 如果查得到資料

代表預存程序已經存在，只是 SSMS 尚未重新整理。

請在左側依序展開：

```text
資料庫
└─ 你的 SAP 公司資料庫
   └─ 可程式性
      └─ 預存程序
```

對「預存程序」按右鍵 →「重新整理」。

也可以直接測試：

```sql
EXEC dbo.usp_SetINV1InvoiceNo
    @DocEntry = 12345;
```

請將 `12345` 換成實際的發票 `DocEntry`。

### 如果查不到資料

通常代表建立在錯誤的資料庫，或者前面的建立指令沒有成功。

先確認目前資料庫：

```sql
SELECT DB_NAME() AS 目前資料庫;
```

如果不是 SAP 公司資料庫，請執行：

```sql
USE [你的SAP公司資料庫名稱];
GO
```

接著重新建立：

```sql
CREATE PROCEDURE dbo.usp_SetINV1InvoiceNo
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    IF ISNULL(@DocEntry, 0) <= 0
        RETURN;

    UPDATE T1
       SET T1.U_InvoiceNo =
           CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 T1
    INNER JOIN dbo.OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
GO
```

執行後應該會看到：

```text
命令已成功完成。
```

再確認一次：

```sql
SELECT
    DB_NAME() AS 目前資料庫,
    name
FROM sys.procedures
WHERE name = 'usp_SetINV1InvoiceNo';
```

### 如果出現「已經有名為……的物件」

代表程序已經存在，只需改用：

```sql
ALTER PROCEDURE dbo.usp_SetINV1InvoiceNo
```

或直接確認物件所在位置：

```sql
SELECT
    OBJECT_SCHEMA_NAME(object_id) AS SchemaName,
    name,
    type_desc
FROM sys.objects
WHERE name = 'usp_SetINV1InvoiceNo';
```

最常見的原因是：

- 左側「預存程序」尚未重新整理。
- SQL 查詢視窗選到了 `master`。
- 建立在另一個 SAP 公司資料庫。
- 執行 `CREATE PROCEDURE` 時有錯誤但被忽略。

---

<a id="q6"></a>

## S055-Q006｜偶

日期：2026-08-06

**你的提問**

偶

**當時回答**

好的。請先執行這段確認：

```sql
SELECT
    DB_NAME() AS 目前資料庫,
    name AS 預存程序名稱
FROM sys.procedures
WHERE name = 'usp_SetINV1InvoiceNo';
```

如果有查到資料，重新整理左側的「可程式性 → 預存程序」即可。

---

<a id="q7"></a>

## S055-Q007｜我可以改我原本那隻已經存取的ˊ名字嗎

日期：2026-08-06

**你的提問**

我可以改我原本那隻已經存取的ˊ名字嗎

**當時回答**

可以。如果你是說「已經存在的預存程序名稱」，有兩種情況。

如果只是要沿用你原本的名稱，直接把範例中的：

```sql
dbo.usp_SetINV1InvoiceNo
```

全部改成你的預存程序名稱即可，例如：

```sql
EXEC dbo.你的原本程序名稱
    @DocEntry = @InvoiceDocEntry;
```

如果要替現有預存程序重新命名，可以執行：

```sql
EXEC sp_rename
    'dbo.舊的程序名稱',
    '新的程序名稱';
```

例如：

```sql
EXEC sp_rename
    'dbo.usp_SetINV1InvoiceNo',
    'usp_UpdateInvoiceNo';
```

重新命名後，`PostTransactionNotice` 也要改成新名稱：

```sql
EXEC dbo.usp_UpdateInvoiceNo
    @DocEntry = @InvoiceDocEntry;
```

不過比較建議不要用 `sp_rename`，因為其他程式若仍呼叫舊名稱，不會自動跟著修改。最安全的方式是保留原名稱，直接用 `ALTER PROCEDURE` 修改內容：

```sql
ALTER PROCEDURE dbo.你的原本程序名稱
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    UPDATE T1
       SET T1.U_InvoiceNo =
           CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 T1
    INNER JOIN dbo.OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
GO
```

重點是：`PostTransactionNotice` 裡 `EXEC` 的名稱，必須與實際預存程序名稱完全相同。

---

<a id="q8"></a>

## S055-Q008｜因為我現在找不到那隻程序 所以我才要改名字 但我還沒掛到post

日期：2026-08-06

**你的提問**

因為我現在找不到那隻程序 所以我才要改名字 但我還沒掛到post

**當時回答**

了解。既然還沒有掛到 `SBO_SP_PostTransactionNotice`，其實不用處理或重新命名原本找不到的程序。

直接在正確的 SAP 公司資料庫，用你想要的新名稱重新建立即可。

例如新名稱叫：

```text
usp_UpdateInvoiceNo
```

執行：

```sql
USE [你的SAP公司資料庫名稱];
GO

CREATE OR ALTER PROCEDURE dbo.usp_UpdateInvoiceNo
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    IF ISNULL(@DocEntry, 0) <= 0
        RETURN;

    UPDATE T1
       SET T1.U_InvoiceNo =
           CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 T1
    INNER JOIN dbo.OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
GO
```

`CREATE OR ALTER` 的意思是：

- 程序不存在：自動建立。
- 程序已經存在：直接修改。
- 不需要先在左側找到它。

建立後確認：

```sql
SELECT
    DB_NAME() AS 資料庫名稱,
    SCHEMA_NAME(schema_id) AS SchemaName,
    name AS 程序名稱
FROM sys.procedures
WHERE name = 'usp_UpdateInvoiceNo';
```

應該會查到：

```text
dbo    usp_UpdateInvoiceNo
```

然後手動測試：

```sql
EXEC dbo.usp_UpdateInvoiceNo
    @DocEntry = 你的測試DocEntry;
```

例如：

```sql
EXEC dbo.usp_UpdateInvoiceNo
    @DocEntry = 12345;
```

確認結果：

```sql
SELECT
    T0.DocEntry,
    T0.DocNum,
    T1.LineNum,
    T1.U_InvoiceNo
FROM OINV T0
INNER JOIN INV1 T1
    ON T1.DocEntry = T0.DocEntry
WHERE T0.DocEntry = 12345;
```

測試成功後，才在 `SBO_SP_PostTransactionNotice` 裡加入：

```sql
IF @object_type = '13'
   AND @transaction_type = 'A'
BEGIN
    DECLARE @InvoiceDocEntry INT;

    SET @InvoiceDocEntry =
        TRY_CONVERT(INT, @list_of_cols_val_tab_del);

    IF ISNULL(@InvoiceDocEntry, 0) > 0
    BEGIN
        EXEC dbo.usp_UpdateInvoiceNo
            @DocEntry = @InvoiceDocEntry;
    END;
END;
```

原本那支找不到、而且沒有掛到 `PostTransactionNotice` 的程序，不會影響新程序執行。

---

<a id="q9"></a>

## S055-Q009｜所以我可以 給舊名字 〔程式碼〕

日期：2026-08-06

**你的提問**

所以我可以 給舊名字
```
	dbo.usp_UpdateInvoiceNo 改成 dbo.Cmsp.UpdateInvoiceNo
```

**當時回答**

不可以直接寫成：

```sql
dbo.Cmsp.UpdateInvoiceNo
```

因為 SQL Server 會把三段名稱解讀成：

```text
資料庫名稱.Schema名稱.程序名稱
```

也就是：

```text
資料庫：dbo
Schema：Cmsp
程序：UpdateInvoiceNo
```

建議選擇以下其中一種。

最簡單的命名方式：

```sql
dbo.Cmsp_UpdateInvoiceNo
```

建立程序：

```sql
CREATE OR ALTER PROCEDURE dbo.Cmsp_UpdateInvoiceNo
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    UPDATE T1
       SET T1.U_InvoiceNo =
           CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 T1
    INNER JOIN dbo.OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
GO
```

呼叫時：

```sql
EXEC dbo.Cmsp_UpdateInvoiceNo
    @DocEntry = 12345;
```

如果 `Cmsp` 是你想建立的 Schema，則需要先建立：

```sql
IF NOT EXISTS (
    SELECT 1
    FROM sys.schemas
    WHERE name = 'Cmsp'
)
BEGIN
    EXEC('CREATE SCHEMA Cmsp');
END;
GO
```

再建立：

```sql
CREATE OR ALTER PROCEDURE Cmsp.UpdateInvoiceNo
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    UPDATE T1
       SET T1.U_InvoiceNo =
           CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 T1
    INNER JOIN dbo.OINV T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, '') = '';
END;
GO
```

呼叫方式：

```sql
EXEC Cmsp.UpdateInvoiceNo
    @DocEntry = 12345;
```

建議你使用第一種：

```sql
dbo.Cmsp_UpdateInvoiceNo
```

這樣不需要額外建立 Schema，也比較不容易在 `PostTransactionNotice` 中寫錯。

---

<a id="q10"></a>

## S055-Q010｜所以我原本那支程序就放著也沒關係?

日期：2026-08-06

**你的提問**

所以我原本那支程序就放著也沒關係?

**當時回答**

對，原本那支程序放著沒關係，前提是：

- 沒有被 `SBO_SP_PostTransactionNotice` 呼叫。
- 沒有被其他程式、排程或 Add-on 呼叫。
- 它本身不會自動執行。

預存程序只有在被 `EXEC` 呼叫時才會執行，所以你可以建立新的：

```sql
dbo.Cmsp_UpdateInvoiceNo
```

然後只在 `SBO_SP_PostTransactionNotice` 呼叫新的程序：

```sql
EXEC dbo.Cmsp_UpdateInvoiceNo
    @DocEntry = @InvoiceDocEntry;
```

如果想確認舊程序是否被其他 SQL 程式引用，可以執行：

```sql
SELECT
    OBJECT_SCHEMA_NAME(referencing_id) AS SchemaName,
    OBJECT_NAME(referencing_id) AS ObjectName
FROM sys.sql_expression_dependencies
WHERE referenced_entity_name = '舊程序名稱';
```

目前既然還沒掛到 `PostTransactionNotice`，可以先放著，等新程序測試成功後再決定是否刪除。不要急著刪，保留會比較安全。

---

<a id="q11"></a>

## S055-Q011｜是按 New stored Procedure

日期：2026-08-06

**你的提問**

# Files mentioned by the user:

## codex-clipboard-4961e5e6-070a-49f7-8063-345a4e52c806.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-4961e5e6-070a-49f7-8063-345a4e52c806.png

## My request for Codex:
是按 New stored Procedure

[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，但從你的截圖來看，`dbo.Cmsp_UpdateInvoice...` 已經存在了。

所以目前不需要按 **New Stored Procedure**，請這樣做：

1. 找到左側的：

```text
dbo.Cmsp_UpdateInvoiceNo
```

2. 對它按右鍵。
3. 選擇 **Modify**。
4. 將內容修改成我們需要的程式。
5. 按上方 **Execute** 執行儲存。

如果要建立一支全新的程序，才對「Stored Procedures／預存程序」資料夾按右鍵，選擇：

```text
New Stored Procedure...
```

你目前可以直接對 `dbo.Cmsp_UpdateInvoiceNo` 選擇 **Modify**，確認它的完整內容。如果它就是剛才建立的那支，就不必再建立第二支。

另外，修改 `SBO_SP_PostTransactionNotice` 時，也是在你截圖選取的位置按右鍵，選擇 **Modify**，然後把呼叫程式放到最後的 `END` 前面：

```sql
IF @object_type = '13'
   AND @transaction_type = 'A'
BEGIN
    DECLARE @InvoiceDocEntry INT;

    SET @InvoiceDocEntry =
        TRY_CONVERT(INT, @list_of_cols_val_tab_del);

    IF ISNULL(@InvoiceDocEntry, 0) > 0
    BEGIN
        EXEC dbo.Cmsp_UpdateInvoiceNo
            @DocEntry = @InvoiceDocEntry;
    END;
END;
```

先修改並測試 `dbo.Cmsp_UpdateInvoiceNo`，確認成功後，再修改 `SBO_SP_PostTransactionNotice`。

---

<a id="q12"></a>

## S055-Q012｜所以我是寫 execute嗎

日期：2026-08-06

**你的提問**

# Files mentioned by the user:

## codex-clipboard-5bdd83fb-9c83-446f-96a9-2c1417953dac.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5bdd83fb-9c83-446f-96a9-2c1417953dac.png

## My request for Codex:
 所以我是寫 execute嗎

[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，有兩個「Execute」：

1. 在程式裡寫 `EXEC`，用來呼叫你的獨立預存程序。
2. 寫完後按 SSMS 上方的 **Execute／執行**，用來儲存 `ALTER PROCEDURE` 的修改。

請在畫面中的：

```sql
-- ADD YOUR CODE HERE
```

下面加入：

```sql
-- 新增 A/R 發票後，將 DocNum 寫入 INV1.U_InvoiceNo
IF @object_type = '13'
   AND @transaction_type = 'A'
BEGIN
    DECLARE @CmspInvoiceDocEntry INT;

    SET @CmspInvoiceDocEntry =
        TRY_CONVERT(INT, @list_of_cols_val_tab_del);

    IF ISNULL(@CmspInvoiceDocEntry, 0) > 0
    BEGIN
        EXEC dbo.Cmsp_UpdateInvoiceNo
            @DocEntry = @CmspInvoiceDocEntry;
    END;
END;
```

放置位置如下：

```sql
------------------------------------------------------------

-- ADD YOUR CODE HERE

-- 新增 A/R 發票後，將 DocNum 寫入 INV1.U_InvoiceNo
IF @object_type = '13'
   AND @transaction_type = 'A'
BEGIN
    DECLARE @CmspInvoiceDocEntry INT;

    SET @CmspInvoiceDocEntry =
        TRY_CONVERT(INT, @list_of_cols_val_tab_del);

    IF ISNULL(@CmspInvoiceDocEntry, 0) > 0
    BEGIN
        EXEC dbo.Cmsp_UpdateInvoiceNo
            @DocEntry = @CmspInvoiceDocEntry;
    END;
END;

------------------------------------------------------------

-- Select the return values
SELECT @error, @error_message;

END;
```

然後：

1. 確認程序名稱確實是：

```sql
dbo.Cmsp_UpdateInvoiceNo
```

2. 按上方工具列的 **Execute／執行**。
3. 下方出現「命令已成功完成」就代表 `PostTransactionNotice` 已儲存。
4. 回到 SAP Business One 新增一張測試發票。
5. 用下面 SQL 檢查：

```sql
SELECT
    T0.DocEntry,
    T0.DocNum,
    T1.LineNum,
    T1.U_InvoiceNo
FROM OINV T0
INNER JOIN INV1 T1
    ON T1.DocEntry = T0.DocEntry
WHERE T0.DocEntry = (
    SELECT MAX(DocEntry)
    FROM OINV
)
ORDER BY T1.LineNum;
```

請不要選右鍵選單的 **Execute Stored Procedure** 來修改 `PostTransactionNotice`；你現在開啟的 `ALTER proc` 畫面是正確的，加入程式後按上方 **Execute** 即可。

---

<a id="q13"></a>

## S055-Q013｜我看不懂欸 太模糊了

日期：2026-08-06

**你的提問**

我看不懂欸 太模糊了

**當時回答**

你現在這個畫面是正確的。

只要找到這一行：

```sql
-- ADD YOUR CODE HERE
```

在它的下一行貼上這段：

```sql
IF @object_type = '13' AND @transaction_type = 'A'
BEGIN
    EXEC dbo.Cmsp_UpdateInvoiceNo
        @DocEntry = @list_of_cols_val_tab_del;
END
```

畫面最後應該像這樣：

```sql
------------------------------------------------------------

-- ADD YOUR CODE HERE

IF @object_type = '13' AND @transaction_type = 'A'
BEGIN
    EXEC dbo.Cmsp_UpdateInvoiceNo
        @DocEntry = @list_of_cols_val_tab_del;
END

------------------------------------------------------------

-- Select the return values
SELECT @error, @error_message

END
```

貼好以後：

1. 按鍵盤 `F5`，或按上方紅色驚嘆號旁的 **Execute**。
2. 看到「命令已成功完成」就完成了。
3. 接著進 SAP 新增一張 A/R 發票測試。

這裡的：

```sql
EXEC dbo.Cmsp_UpdateInvoiceNo
```

就是呼叫你建立的那支程序。前提是程序的完整名稱確實為：

```sql
dbo.Cmsp_UpdateInvoiceNo
```

---

