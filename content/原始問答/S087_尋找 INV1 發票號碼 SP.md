# 尋找 INV1 發票號碼 SP

來源編號：S087  
聊天 ID：`01a018e0-60aa-71d2-b091-f7239434e5a1`  
聊天起始日期（台北）：2026-08-19  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S087-Q001｜幫我找 我記得有 INV1的發票號碼 SP

日期：2026-08-19

**你的提問**

幫我找 我記得有 INV1的發票號碼 SP

**當時回答**

目前工作區找不到「INV1」或「SP」。

你記得它是在 Gmail、Outlook、Google Drive，還是某份本機檔案裡？告訴我來源，我就能繼續找。

---

<a id="q2"></a>

## S087-Q002｜SQL／程式碼調整與說明

日期：2026-08-19

**你的提問**

ALTER PROCEDURE [dbo].[Cmsp_Update_INV1_InvoiceNo]
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
END;  類似這個

**當時回答**

你要找的應該就是這類自訂 SP：

```sql
ALTER PROCEDURE [dbo].[Cmsp_Update_INV1_InvoiceNo]
    @DocEntry INT
AS
BEGIN
    SET NOCOUNT ON;

    IF ISNULL(@DocEntry, 0) <= 0
        RETURN;

    UPDATE T1
    SET T1.U_InvoiceNo = CONVERT(NVARCHAR(20), T0.DocNum)
    FROM dbo.INV1 AS T1
    INNER JOIN dbo.OINV AS T0
        ON T0.DocEntry = T1.DocEntry
    WHERE T1.DocEntry = @DocEntry
      AND ISNULL(T1.U_InvoiceNo, N'') = N'';
END;
```

用途是：

- 傳入 A/R Invoice 的 `DocEntry`
- 從表頭 `OINV.DocNum` 取得發票文件號碼
- 寫入所有對應的 `INV1.U_InvoiceNo`
- 已有號碼的明細不覆蓋

我搜尋了本機工作區與公開資料，都沒有找到完全相同的 `Cmsp_Update_INV1_InvoiceNo`，所以它很可能是你們資料庫裡自訂的 SP。可直接在 SQL Server 查：

```sql
SELECT
    s.name AS SchemaName,
    p.name AS ProcedureName
FROM sys.procedures AS p
JOIN sys.schemas AS s
    ON s.schema_id = p.schema_id
WHERE p.name LIKE '%INV1%'
   OR p.name LIKE '%InvoiceNo%';
```

若要連 SP 內容一起找：

```sql
SELECT
    s.name AS SchemaName,
    o.name AS ObjectName,
    o.type_desc,
    m.definition
FROM sys.sql_modules AS m
JOIN sys.objects AS o
    ON o.object_id = m.object_id
JOIN sys.schemas AS s
    ON s.schema_id = o.schema_id
WHERE m.definition LIKE '%INV1%'
  AND (
       m.definition LIKE '%U_InvoiceNo%'
       OR m.definition LIKE '%OINV%'
  );
```

---

