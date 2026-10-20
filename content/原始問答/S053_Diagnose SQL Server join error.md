# Diagnose SQL Server join error

來源編號：S053  
聊天 ID：`019fd502-b025-72a3-907c-365e505c837e`  
聊天起始日期（台北）：2026-08-06  
保存位置：一般本機紀錄  
問答段落：5

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S053-Q001｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]'AJDT' is not a recognized join …

日期：2026-08-06

**你的提問**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]'AJDT' is not a recognized join option. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '使用者定義的值' (CSHS) (s) could not be prepared.

**當時回答**

第 2 条通常只是第 1 条导致的连带错误：SQL Server 无法解析 JOIN，因此整条语句无法 prepare。

核心错误是：

> `'AJDT' is not a recognized join option`

SQL Server 在预期 `JOIN` 类型或选项的位置读到了 `AJDT`。请检查 SQL 中 `AJDT` 附近，常见错误例如：

```sql
-- 错误
LEFT OUTER AJDT JOIN SomeTable ...
LEFT AJDT JOIN SomeTable ...
INNER AJDT JOIN SomeTable ...
```

如果 `AJDT` 是表别名，应写成：

```sql
LEFT OUTER JOIN SomeTable AS AJDT
    ON ...
```

如果 `AJDT` 是表名，应写成：

```sql
LEFT OUTER JOIN AJDT
    ON ...
```

也请检查动态拼接 SQL 是否漏了空格或关键字，例如：

```sql
... LEFT OUTER JOIN AJDT AS A ON ...
```

而不是：

```sql
... LEFT OUTER AJDT JOIN ...
```

`Statement(s) could not be prepared` 不需要单独处理；修正 JOIN 语法后通常会一起消失。若贴出包含 `AJDT` 前后约 10 行的完整 SQL，我可以直接指出需要修改的位置。

---

<a id="q2"></a>

## S053-Q002｜我想看這張日記帳分錄是誰建立的ˊ

日期：2026-08-06

**你的提問**

我想看這張日記帳分錄是誰建立的ˊ

**當時回答**

如果是 SAP Business One，要查看「日記帳分錄是誰建立的」，請用 `OJDT.UserSign` 連接 `OUSR.USERID`，不要把 `AJDT` 放在 `JOIN` 選項的位置。

```sql
SELECT
    T0.TransId       AS N'交易號碼',
    T0.RefDate       AS N'過帳日期',
    T0.Memo          AS N'備註',
    T0.CreateDate    AS N'建立日期',
    T1.USER_CODE     AS N'建立者代碼',
    T1.U_NAME        AS N'建立者姓名'
FROM OJDT T0
LEFT JOIN OUSR T1
    ON T0.UserSign = T1.USERID
WHERE T0.TransId = 12345;
```

請把 `12345` 換成該日記帳分錄的「交易號碼（TransId）」。

如果你是在 SAP B1 查詢產生器中使用參數：

```sql
WHERE T0.TransId = [%0]
```

完整查詢可寫成：

```sql
SELECT
    T0.TransId,
    T0.Number,
    T0.RefDate,
    T0.Memo,
    T0.CreateDate,
    T1.USER_CODE,
    T1.U_NAME
FROM OJDT T0
LEFT JOIN OUSR T1
    ON T0.UserSign = T1.USERID
WHERE T0.TransId = [%0];
```

其中 `AJDT` 是日記帳分錄的歷史／稽核相關資料表；單純查「最初建立者」通常使用 `OJDT.UserSign` 即可。

---

<a id="q3"></a>

## S053-Q003｜我的來源 TransTpye是顯示數字 但我想要顯示中文

日期：2026-08-06

**你的提問**

我的來源 TransTpye是顯示數字　但我想要顯示中文

**當時回答**

可以用 SQL Server 的 `CASE`，把 `OJDT.TransType` 數字轉成中文來源名稱：

```sql
SELECT
    T0.TransId AS N'交易號碼',
    T0.RefDate AS N'過帳日期',
    T0.Memo    AS N'備註',

    CASE T0.TransType
        WHEN 13  THEN N'應收發票'
        WHEN 14  THEN N'應收貸項通知單'
        WHEN 15  THEN N'交貨'
        WHEN 16  THEN N'退貨'
        WHEN 18  THEN N'應付發票'
        WHEN 19  THEN N'應付貸項通知單'
        WHEN 20  THEN N'採購收貨'
        WHEN 21  THEN N'採購退貨'
        WHEN 22  THEN N'採購單'
        WHEN 23  THEN N'銷售報價'
        WHEN 24  THEN N'收款'
        WHEN 30  THEN N'手動日記帳分錄'
        WHEN 46  THEN N'付款'
        WHEN 59  THEN N'收貨'
        WHEN 60  THEN N'發貨'
        WHEN 67  THEN N'庫存轉儲'
        WHEN 69  THEN N'到岸成本'
        WHEN 162 THEN N'庫存重估'
        WHEN 202 THEN N'生產訂單'
        WHEN 203 THEN N'應收預付款發票'
        WHEN 204 THEN N'應付預付款發票'
        ELSE N'其他類型 (' +
             CONVERT(NVARCHAR(20), T0.TransType) + N')'
    END AS N'來源類型',

    T1.USER_CODE AS N'建立者代碼',
    T1.U_NAME    AS N'建立者姓名'
FROM OJDT T0
LEFT JOIN OUSR T1
    ON T0.UserSign = T1.USERID
WHERE T0.TransId = [%0];
```

注意欄位名稱是 `TransType`，不是 `TransTpye`。

其中：

- `30`：使用者直接建立的手動日記帳分錄
- `13`、`18` 等：由銷售或採購文件自動產生日記帳分錄
- 未列出的類型會顯示成 `其他類型 (數字)`，方便後續補上中文名稱。

---

<a id="q4"></a>

## S053-Q004｜LECT T0.[TransType], T0.TransId , T0.RefDate AS N'過帳日期', T2.[ShortName], T2.[Account] AS N…

日期：2026-08-06

**你的提問**

LECT
    T0.[TransType],
    T0.TransId       ,
    T0.RefDate       AS N'過帳日期',
    T2.[ShortName],
    T2.[Account]    AS N'科目代號',
    T2.[Debit],
    T2.[Credit],
    T0.Memo          AS N'備註',
    T0.CreateDate    AS N'建立日期',
    T1.USER_CODE     AS N'建立者代碼',
    T1.U_NAME        AS N'建立者姓名'
FROM OJDT T0
LEFT JOIN OUSR T1
    ON T0.UserSign = T1.USERID
INNER JOIN JDT1 T2 ON  T0.[TransId] = T2.[TransId]
WHERE T1.USER_CODE = [%0]


這是我目前的寫法　請加入我剛剛所說的需求　但不變我的語法

**當時回答**

保留你原本的查询结构，只在 `T0.[TransType]` 后面增加中文来源栏位：

```sql
SELECT
    T0.[TransType],

    CASE T0.[TransType]
        WHEN 13  THEN N'应收发票'
        WHEN 14  THEN N'应收贷项通知单'
        WHEN 15  THEN N'交货'
        WHEN 16  THEN N'退货'
        WHEN 18  THEN N'应付发票'
        WHEN 19  THEN N'应付贷项通知单'
        WHEN 20  THEN N'采购收货'
        WHEN 21  THEN N'采购退货'
        WHEN 22  THEN N'采购单'
        WHEN 23  THEN N'销售报价'
        WHEN 24  THEN N'收款'
        WHEN 30  THEN N'手动日记帐分录'
        WHEN 46  THEN N'付款'
        WHEN 59  THEN N'收货'
        WHEN 60  THEN N'发货'
        WHEN 67  THEN N'库存转储'
        WHEN 69  THEN N'到岸成本'
        WHEN 162 THEN N'库存重估'
        WHEN 202 THEN N'生产订单'
        WHEN 203 THEN N'应收预付款发票'
        WHEN 204 THEN N'应付预付款发票'
        ELSE N'其他类型 (' 
             + CONVERT(NVARCHAR(20), T0.[TransType]) 
             + N')'
    END AS N'来源类型',

    T0.TransId,
    T0.RefDate       AS N'过帐日期',
    T2.[ShortName],
    T2.[Account]     AS N'科目代号',
    T2.[Debit],
    T2.[Credit],
    T0.Memo          AS N'备注',
    T0.CreateDate    AS N'建立日期',
    T1.USER_CODE     AS N'建立者代码',
    T1.U_NAME        AS N'建立者姓名'
FROM OJDT T0
LEFT JOIN OUSR T1
    ON T0.UserSign = T1.USERID
INNER JOIN JDT1 T2
    ON T0.[TransId] = T2.[TransId]
WHERE T1.USER_CODE = [%0]
```

这样会同时显示：

- `TransType`：原始数字
- `来源类型`：对应的中文名称

另外，你贴出的第一行是 `LECT`；如果原查询也是如此，需要改为 `SELECT` 才能执行。

---

<a id="q5"></a>

## S053-Q005｜我要繁體值中文

日期：2026-08-06

**你的提問**

我要繁體值中文

**當時回答**

可以，以下改為繁體中文，其餘語法維持不變：

```sql
SELECT
    T0.[TransType],

    CASE T0.[TransType]
        WHEN 13  THEN N'應收發票'
        WHEN 14  THEN N'應收貸項通知單'
        WHEN 15  THEN N'交貨'
        WHEN 16  THEN N'退貨'
        WHEN 18  THEN N'應付發票'
        WHEN 19  THEN N'應付貸項通知單'
        WHEN 20  THEN N'採購收貨'
        WHEN 21  THEN N'採購退貨'
        WHEN 22  THEN N'採購單'
        WHEN 23  THEN N'銷售報價'
        WHEN 24  THEN N'收款'
        WHEN 30  THEN N'手動日記帳分錄'
        WHEN 46  THEN N'付款'
        WHEN 59  THEN N'收貨'
        WHEN 60  THEN N'發貨'
        WHEN 67  THEN N'庫存轉儲'
        WHEN 69  THEN N'到岸成本'
        WHEN 162 THEN N'庫存重估'
        WHEN 202 THEN N'生產訂單'
        WHEN 203 THEN N'應收預付款發票'
        WHEN 204 THEN N'應付預付款發票'
        ELSE N'其他類型（'
             + CONVERT(NVARCHAR(20), T0.[TransType])
             + N'）'
    END AS N'來源類型',

    T0.TransId,
    T0.RefDate       AS N'過帳日期',
    T2.[ShortName],
    T2.[Account]     AS N'科目代號',
    T2.[Debit],
    T2.[Credit],
    T0.Memo          AS N'備註',
    T0.CreateDate    AS N'建立日期',
    T1.USER_CODE     AS N'建立者代碼',
    T1.U_NAME        AS N'建立者姓名'
FROM OJDT T0
LEFT JOIN OUSR T1
    ON T0.UserSign = T1.USERID
INNER JOIN JDT1 T2
    ON T0.[TransId] = T2.[TransId]
WHERE T1.USER_CODE = [%0]
```

`N'文字'` 的 `N` 建議保留，才能確保 SQL Server 正確顯示繁體中文。

---

