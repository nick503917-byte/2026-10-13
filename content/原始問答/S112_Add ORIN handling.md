# Add ORIN handling

來源編號：S112  
聊天 ID：`01a036a9-b4d4-7590-a0bb-857b257fcf04`  
聊天起始日期（台北）：2026-08-25  
保存位置：一般本機紀錄  
問答段落：4

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S112-Q001｜SQL／程式碼調整與說明

日期：2026-08-25

**你的提問**

IF @object\_type IN ('13')\
   AND @transaction\_type IN ('U', 'A')\
BEGIN\
\
    \-- 更新 AR 發票備註\
    UPDATE T0\
    SET T0.JrnlMemo =\
          'AR發票 -'\
        \+ ISNULL(T0.CardCode, '')\
        \+ ' '\
        \+ CONVERT(VARCHAR(8), T0.DocDate, 112)\
        \+ ' '\
        \+ CONVERT(VARCHAR(20), T0.DocNum)\
    FROM OINV T0\
    WHERE T0.DocEntry = @list\_of\_cols\_val\_tab\_del;\
\
\
    \-- 更新傳票表頭備註\
    UPDATE T1\
    SET T1.Memo =\
          'AR發票 -'\
        \+ ISNULL(T0.CardCode, '')\
        \+ ' '\
        \+ CONVERT(VARCHAR(8), T0.DocDate, 112)\
        \+ ' '\
        \+ CONVERT(VARCHAR(20), T0.DocNum)\
    FROM OINV T0\
    INNER JOIN OJDT T1\
        ON T0.DocNum = T1.BaseRef\
       AND T1.TransType = '13'\
    WHERE T0.DocEntry = @list\_of\_cols\_val\_tab\_del;\
\
\
    \-- 更新傳票明細備註\
    UPDATE T2\
    SET T2.LineMemo =\
          'AR發票 -'\
        \+ ISNULL(T0.CardCode, '')\
        \+ ' '\
        \+ CONVERT(VARCHAR(8), T0.DocDate, 112)\
        \+ ' '\
        \+ CONVERT(VARCHAR(20), T0.DocNum)\
    FROM OINV T0\
    INNER JOIN OJDT T1\
        ON T0.DocNum = T1.BaseRef\
       AND T1.TransType = '13'\
    INNER JOIN JDT1 T2\
        ON T1.TransId = T2.TransId\
    WHERE T0.DocEntry = @list\_of\_cols\_val\_tab\_del;\
\
END;


這個模板我要增加 ORIN

**當時回答**

可以在原本 `OINV`（AR 發票，Object Type 13）區塊後面，增加以下 `ORIN`（AR 貸項通知單，Object Type 14）區塊：

```sql
IF @object_type = '14'
   AND @transaction_type IN ('U', 'A')
BEGIN

    -- 更新 AR 貸項通知單備註
    UPDATE T0
    SET T0.JrnlMemo =
          'AR貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票表頭備註
    UPDATE T1
    SET T1.Memo =
          'AR貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '14'
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票明細備註
    UPDATE T2
    SET T2.LineMemo =
          'AR貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '14'
    INNER JOIN JDT1 T2
        ON T2.TransId = T1.TransId
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;

END;
```

我把傳票關聯改成：

```sql
T1.TransId = T0.TransId
```

這通常比用 `DocNum = BaseRef` 更準確，可避免文件號碼重複或 `BaseRef` 格式差異造成更新錯誤。原本 `OINV` 的兩個傳票更新也建議同樣改成：

```sql
INNER JOIN OJDT T1
    ON T1.TransId = T0.TransId
   AND T1.TransType = '13'
```

---

<a id="q2"></a>

## S112-Q002｜全部都要一樣

日期：2026-08-25

**你的提問**

全部都要一樣

**當時回答**

了解，`ORIN` 的備註內容也全部維持和 `OINV` 一樣，都是 `AR發票 -客戶代碼 日期 文件號碼`：

```sql
IF @object_type IN ('13')
   AND @transaction_type IN ('U', 'A')
BEGIN

    -- 更新 AR 發票備註
    UPDATE T0
    SET T0.JrnlMemo =
          'AR發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM OINV T0
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票表頭備註
    UPDATE T1
    SET T1.Memo =
          'AR發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM OINV T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '13'
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票明細備註
    UPDATE T2
    SET T2.LineMemo =
          'AR發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM OINV T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '13'
    INNER JOIN JDT1 T2
        ON T2.TransId = T1.TransId
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;

END;


IF @object_type IN ('14')
   AND @transaction_type IN ('U', 'A')
BEGIN

    -- 更新 AR 貸項通知單備註
    UPDATE T0
    SET T0.JrnlMemo =
          'AR發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票表頭備註
    UPDATE T1
    SET T1.Memo =
          'AR發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '14'
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票明細備註
    UPDATE T2
    SET T2.LineMemo =
          'AR發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '14'
    INNER JOIN JDT1 T2
        ON T2.TransId = T1.TransId
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;

END;
```

`OINV` 和 `ORIN` 現在三個位置的格式完全一致：

```text
AR發票 -CardCode YYYYMMDD DocNum
```

---

<a id="q3"></a>

## S112-Q003｜備註改 AR貸項

日期：2026-08-25

**你的提問**

備註改 AR貸項

**當時回答**

將 `ORIN` 區塊內三個備註全部改成 `AR貸項 -`：

```sql
IF @object_type IN ('14')
   AND @transaction_type IN ('U', 'A')
BEGIN

    -- 更新 AR 貸項備註
    UPDATE T0
    SET T0.JrnlMemo =
          'AR貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票表頭備註
    UPDATE T1
    SET T1.Memo =
          'AR貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '14'
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票明細備註
    UPDATE T2
    SET T2.LineMemo =
          'AR貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORIN T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '14'
    INNER JOIN JDT1 T2
        ON T2.TransId = T1.TransId
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;

END;
```

最終格式：

```text
AR貸項 -CardCode YYYYMMDD DocNum
```

---

<a id="q4"></a>

## S112-Q004｜一樣的模板 再給我 OPCH ORPC

日期：2026-08-25

**你的提問**

一樣的模板 再給我 OPCH ORPC

**當時回答**

以下是相同模板的 `OPCH`（AP 發票，Object Type `18`）及 `ORPC`（AP 貸項，Object Type `19`）。

### OPCH－AP 發票

```sql
IF @object_type IN ('18')
   AND @transaction_type IN ('U', 'A')
BEGIN

    -- 更新 AP 發票備註
    UPDATE T0
    SET T0.JrnlMemo =
          'AP發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM OPCH T0
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票表頭備註
    UPDATE T1
    SET T1.Memo =
          'AP發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM OPCH T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '18'
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票明細備註
    UPDATE T2
    SET T2.LineMemo =
          'AP發票 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM OPCH T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '18'
    INNER JOIN JDT1 T2
        ON T2.TransId = T1.TransId
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;

END;
```

### ORPC－AP 貸項

```sql
IF @object_type IN ('19')
   AND @transaction_type IN ('U', 'A')
BEGIN

    -- 更新 AP 貸項備註
    UPDATE T0
    SET T0.JrnlMemo =
          'AP貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORPC T0
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票表頭備註
    UPDATE T1
    SET T1.Memo =
          'AP貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORPC T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '19'
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;


    -- 更新傳票明細備註
    UPDATE T2
    SET T2.LineMemo =
          'AP貸項 -'
        + ISNULL(T0.CardCode, '')
        + ' '
        + CONVERT(VARCHAR(8), T0.DocDate, 112)
        + ' '
        + CONVERT(VARCHAR(20), T0.DocNum)
    FROM ORPC T0
    INNER JOIN OJDT T1
        ON T1.TransId = T0.TransId
       AND T1.TransType = '19'
    INNER JOIN JDT1 T2
        ON T2.TransId = T1.TransId
    WHERE T0.DocEntry = @list_of_cols_val_tab_del;

END;
```

產生的備註格式分別為：

```text
AP發票 -CardCode YYYYMMDD DocNum
AP貸項 -CardCode YYYYMMDD DocNum
```

---

