---
title: "SQL JOIN 與主檔最後修改日期"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S132"]
---

# SQL JOIN 與主檔最後修改日期

[回總覽](../../00_閱讀入口.md)

## 整合摘要

整理 LEFT JOIN／RIGHT JOIN 保留哪側資料、以及 OITM.UpdateDate／UpdateTS。主檔修改日期與實際庫存異動日期是不同概念，報表不能互相替代。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S132｜Explain RIGHT JOIN and LEFT JOIN](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md>)：2026-09-03，7 段問答。

## 問題索引

- [01｜RIGHT JOIN 跟 LEFT JOIN 差異](#qa1)
- [02｜如果我的主單是要交貨單 然後我要從發票號碼的表身拉一個欄位資料 怎麼寫比較好](#qa2)
- [03｜SQL／程式碼調整與說明](#qa3)
- [04｜sapb1 銷售分析報表 跑出來的 跟我自己寫的查詢報表不一樣 有甚麼可能性](#qa4)
- [05｜銷售分析報表 是用甚麼邏輯的](#qa5)
- [06｜這是甚麼](#qa6)
- [07｜如果我想抓項目的最後有異動 抓的到日期嗎](#qa7)

## 合併後歷史問答

<a id="qa1"></a>

### 1. RIGHT JOIN 跟 LEFT JOIN 差異

來源：[S132-Q001](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md#q1>)；2026-09-03

**問題**

RIGHT JOIN 跟 LEFT JOIN 差異

**歷史回答**

`LEFT JOIN` 和 `RIGHT JOIN` 的差別，主要是「保留哪一邊資料表的所有資料」。

### LEFT JOIN

保留左表的所有資料；右表沒有符合的資料時，右表欄位會是 `NULL`。

```sql
SELECT *
FROM A
LEFT JOIN B ON A.id = B.id;
```

### RIGHT JOIN

保留右表的所有資料；左表沒有符合的資料時，左表欄位會是 `NULL`。

```sql
SELECT *
FROM A
RIGHT JOIN B ON A.id = B.id;
```

假設資料如下：

**A 表**

| id | name |
|---:|---|
| 1 | Amy |
| 2 | Bob |

**B 表**

| id | order |
|---:|---|
| 2 | Book |
| 3 | Pen |

`A LEFT JOIN B` 的結果：

| id | name | order |
|---:|---|---|
| 1 | Amy | NULL |
| 2 | Bob | Book |

`A RIGHT JOIN B` 的結果：

| id | name | order |
|---:|---|---|
| 2 | Bob | Book |
| 3 | NULL | Pen |

簡單記法：

- `LEFT JOIN`：左邊全部保留
- `RIGHT JOIN`：右邊全部保留
- 交換資料表順序後，兩者通常可以互相改寫：

```sql
A RIGHT JOIN B
```

等同於：

```sql
B LEFT JOIN A
```

實務上通常較常使用 `LEFT JOIN`，因為查詢閱讀方向比較直觀。

<a id="qa2"></a>

### 2. 如果我的主單是要交貨單 然後我要從發票號碼的表身拉一個欄位資料 怎麼寫比較好

來源：[S132-Q002](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md#q2>)；2026-09-03

**問題**

如果我的主單是要交貨單 然後我要從發票號碼的表身拉一個欄位資料 怎麼寫比較好

**歷史回答**

如果「交貨單」是主資料，要從「發票表身」拉欄位，通常用 `LEFT JOIN`，確保尚未開發票的交貨單也不會消失。

```sql
SELECT
    D.delivery_no,          -- 交貨單號
    D.delivery_date,        -- 交貨日期
    D.item_no,              -- 品號
    I.invoice_no,           -- 發票號碼
    I.your_field            -- 要抓的發票表身欄位
FROM delivery_detail AS D
LEFT JOIN invoice_detail AS I
    ON I.delivery_no = D.delivery_no
   AND I.delivery_line_no = D.line_no;
```

重點是 JOIN 條件通常不能只放「交貨單號」，還要加「項次」：

```sql
ON I.delivery_no = D.delivery_no
AND I.delivery_line_no = D.line_no
```

否則一張交貨單有多筆表身時，可能會互相配錯或造成資料筆數倍增。

如果資料結構是「交貨單 → 發票表頭 → 發票表身」，可以這樣寫：

```sql
SELECT
    D.delivery_no,
    D.line_no,
    H.invoice_no,
    I.your_field
FROM delivery_detail AS D
LEFT JOIN invoice_header AS H
    ON H.delivery_no = D.delivery_no
LEFT JOIN invoice_detail AS I
    ON I.invoice_no = H.invoice_no
   AND I.line_no = D.line_no;
```

但實際關聯鍵可能是「來源單別、來源單號、來源序號」：

```sql
LEFT JOIN invoice_detail AS I
    ON I.source_type = D.delivery_type
   AND I.source_no   = D.delivery_no
   AND I.source_line = D.line_no
```

建議原則：

- 交貨單必須全部顯示：使用 `LEFT JOIN`
- 只需要已開發票的交貨單：使用 `INNER JOIN`
- 同一交貨明細可能對應多張發票：結果會出現多筆，這是正常的一對多關係
- 若只想取一筆，需要先決定取最新、最早或任意一筆，不能單純用 `DISTINCT` 隱藏問題

最常見的寫法會是：

```sql
SELECT
    交貨單.*,
    發票表身.目標欄位
FROM 交貨單
LEFT JOIN 發票表身
    ON 發票表身.來源單號 = 交貨單.單號
   AND 發票表身.來源序號 = 交貨單.序號;
```

<a id="qa3"></a>

### 3. SQL／程式碼調整與說明

來源：[S132-Q003](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md#q3>)；2026-09-03

**問題**

SELECT\
    T0.DocNum,\
    T0.[DocStatus],\
    T0.CardCode,\
    T0.CardName,\
    T1.ItemCode,\
    T1.Quantity,\
    T1.Price,\
    T0.DocTotal,\
    T1.WhsCode,\
    T0.DocDate,\
    T0.TaxDate,\
    T2.DocNum AS InvoiceNo,\
    T3.[U\_InvoiceNo]\
\
FROM ODLN T0\
INNER JOIN DLN1 T1\
    ON T0.DocEntry = T1.DocEntry\
\
LEFT JOIN INV1 T3\
    ON T3.BaseType = 15\
    AND T3.BaseEntry = T1.DocEntry\
    AND T3.BaseLine = T1.LineNum\
\
LEFT JOIN OINV T2\
    ON T2.DocEntry = T3.DocEntry\
\
WHERE T0.[DocDate] BETWEEN [%0] AND [%1] AND T0.[CANCELED] = 'N'\
\
ORDER BY\
    T0.DocNum,\
    T1.LineNum

**歷史回答**

你的寫法基本正確：以交貨單 `ODLN / DLN1` 為主，透過發票表身 `INV1` 的來源欄位關聯，再取得發票表頭 `OINV` 的發票號碼。

整理後如下：

```sql
SELECT
    T0.DocNum,
    T0.DocStatus,
    T0.CardCode,
    T0.CardName,
    T1.LineNum,
    T1.ItemCode,
    T1.Quantity,
    T1.Price,
    T0.DocTotal,
    T1.WhsCode,
    T0.DocDate,
    T0.TaxDate,
    T2.DocNum AS InvoiceNo,
    T3.U_InvoiceNo
FROM ODLN T0
INNER JOIN DLN1 T1
    ON T0.DocEntry = T1.DocEntry
LEFT JOIN INV1 T3
    ON T3.BaseType  = 15
   AND T3.BaseEntry = T1.DocEntry
   AND T3.BaseLine  = T1.LineNum
LEFT JOIN OINV T2
    ON T2.DocEntry = T3.DocEntry
   AND T2.CANCELED = 'N'
WHERE T0.DocDate BETWEEN [%0] AND [%1]
  AND T0.CANCELED = 'N'
ORDER BY
    T0.DocNum,
    T1.LineNum;
```

這幾個關聯條件很重要：

```sql
T3.BaseType = 15
```

`15` 代表發票表身的來源文件是交貨單。

```sql
T3.BaseEntry = T1.DocEntry
```

對應到同一張交貨單。

```sql
T3.BaseLine = T1.LineNum
```

對應到交貨單的同一個明細列，避免不同品項配錯。

我把：

```sql
T2.CANCELED = 'N'
```

放在 `LEFT JOIN` 的 `ON` 裡，而不是 `WHERE`，這樣尚未開發票的交貨單明細仍然會顯示，只是 `InvoiceNo` 和 `U_InvoiceNo` 為 `NULL`。

另外要注意：

- `T0.DocTotal` 是整張交貨單總額，每個明細列都會重複顯示。
- 如果同一筆交貨明細分批開了多張發票，結果會出現多列，這是正常的一對多結果。
- `T3.U_InvoiceNo` 必須確定 UDF 是建立在「發票表身 `INV1`」；如果其實建立在發票表頭 `OINV`，應改成：

```sql
T2.U_InvoiceNo
```

如果你要的是 SAP 發票「文件號碼」，目前的 `T2.DocNum AS InvoiceNo` 已經可以取得，不一定需要自訂欄位。

<a id="qa4"></a>

### 4. sapb1 銷售分析報表 跑出來的 跟我自己寫的查詢報表不一樣 有甚麼可能性

來源：[S132-Q004](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md#q4>)；2026-09-03

**問題**

sapb1 銷售分析報表 跑出來的 跟我自己寫的查詢報表不一樣 有甚麼可能性

**歷史回答**

很常見，SAP B1 內建「銷售分析報表」和自寫 SQL 不一致，通常不是 JOIN 錯而已，而是兩邊的統計口徑不同。

最可能的原因：

1. 日期欄位不同  
   你的查詢目前用：

```sql
T0.DocDate BETWEEN [%0] AND [%1]
```

但內建報表可能依設定使用：

- `DocDate`：過帳日期
- `TaxDate`：文件日期
- `DocDueDate`：到期日

日期基準不同，金額就會不同。

2. 內建報表可能以發票為統計來源  
   你目前以交貨單為主：

```sql
ODLN
INNER JOIN DLN1
```

但銷售分析通常可能統計：

- A/R Invoice：`OINV`、`INV1`
- A/R Credit Memo：`ORIN`、`RIN1`
- 交貨單：`ODLN`、`DLN1`

如果內建報表以發票減貸項憑證計算，自然不會等於交貨單金額。

3. 銷售退貨或貸項憑證沒有扣除  
   內建報表可能會扣除：

- 銷售退貨：`ORDN`、`RDN1`
- A/R 貸項憑證：`ORIN`、`RIN1`

你的查詢目前只有交貨單，沒有扣除退貨或貸項。

4. 你的 JOIN 可能讓交貨單明細重複  
   一筆交貨單明細可能分批開出多張發票：

```sql
DLN1 → INV1
```

此時同一筆 `DLN1` 會被展開成多列。如果再加總：

```sql
SUM(T1.Quantity)
SUM(T1.Price * T1.Quantity)
```

交貨數量與金額就會重複計算。

可先檢查：

```sql
SELECT
    T1.DocEntry,
    T1.LineNum,
    COUNT(*) AS JoinCount
FROM DLN1 T1
LEFT JOIN INV1 T3
    ON T3.BaseType  = 15
   AND T3.BaseEntry = T1.DocEntry
   AND T3.BaseLine  = T1.LineNum
GROUP BY
    T1.DocEntry,
    T1.LineNum
HAVING COUNT(*) > 1;
```

5. 金額欄位口徑不同  
   SAP B1 常見欄位的意義不一樣：

- `LineTotal`：明細未稅金額，通常是本幣
- `TotalFrgn`：外幣明細金額
- `DocTotal`：整張文件含稅總額
- `DocTotalFC`：文件外幣總額
- `VatSum`：稅額
- `DiscSum`：文件折扣
- `RoundDif`：尾差
- 運費或其他費用也可能另計

尤其你的結果是一筆明細顯示一次 `T0.DocTotal`。若把它加總，文件總額會依明細筆數重複。

若按品項分析，一般應優先使用：

```sql
T1.LineTotal
```

而不是加總：

```sql
T0.DocTotal
```

6. 取消文件的判斷不同  
   你已排除取消交貨單：

```sql
T0.CANCELED = 'N'
```

但取消發票、取消貸項或取消退貨也可能影響內建報表。取消文件在 SAP B1 中也可能伴隨反向文件，須依報表口徑處理。

7. 品項列和服務列  
   內建報表可能包含或排除：

- 服務型文件
- 非存貨品項
- 運費列
- 文字列
- 特殊列型態

自寫查詢只抓 `ItemCode` 時，服務型文件可能沒有 `ItemCode`，造成遺漏或分類不同。

8. 篩選條件不同  
   內建報表還可能套用：

- 客戶或客戶群組
- 業務員
- 品項或品項群組
- 倉庫
- 分公司
- 銷售分析依「訂單、交貨或發票」選項
- 只顯示庫存品項或特定文件類型

最值得先確認的是內建報表執行時選擇的「分析依據」。如果它是依發票分析，你應直接從發票與貸項憑證查詢，例如：

```sql
-- A/R 發票
SELECT
    T0.DocDate,
    T0.DocNum,
    T1.ItemCode,
    T1.Quantity,
    T1.LineTotal AS SalesAmount
FROM OINV T0
INNER JOIN INV1 T1
    ON T0.DocEntry = T1.DocEntry
WHERE T0.DocDate BETWEEN [%0] AND [%1]
  AND T0.CANCELED = 'N'

UNION ALL

-- A/R 貸項憑證，以負數扣除
SELECT
    T0.DocDate,
    T0.DocNum,
    T1.ItemCode,
    -T1.Quantity,
    -T1.LineTotal
FROM ORIN T0
INNER JOIN RIN1 T1
    ON T0.DocEntry = T1.DocEntry
WHERE T0.DocDate BETWEEN [%0] AND [%1]
  AND T0.CANCELED = 'N';
```

建議先用「單一客戶、單一月份、單一品項」比對，並分別核對：

- 文件數量
- 銷售數量
- 未稅金額
- 稅額
- 含稅總額
- 退貨或貸項金額

以你目前的 SQL 來看，最優先懷疑的是：**內建報表使用發票口徑，但你的查詢使用交貨單口徑；以及 `INV1` 一對多 JOIN 造成交貨明細重複。**

<a id="qa5"></a>

### 5. 銷售分析報表 是用甚麼邏輯的

來源：[S132-Q005](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md#q5>)；2026-09-03

**問題**

銷售分析報表 是用甚麼邏輯的

**歷史回答**

SAP B1 標準「銷售分析報表」不是固定只查某一張資料表；邏輯取決於你在查詢條件中選擇的文件類型：

| 報表選項 | 主要來源 | 扣減文件 | 核心邏輯 |
|---|---|---|---|
| 發票 | `OINV + INV1` | `ORIN + RIN1` 貸項憑證 | 發票銷售減貸項 |
| 訂單 | `ORDR + RDR1` | 無 | 通常顯示尚未交貨／開票的未結數量 |
| 交貨單 | `ODLN + DLN1` | `ORDN + RDN1` 退貨 | 交貨減退貨，並著重尚未開票的未結數量 |

SAP 官方明確指出：選擇「交貨單」時，退貨會納入並扣減銷售金額；訂單或交貨單若已完全複製到後續文件，數量與金額會顯示為零，部分複製則顯示剩餘未結數量。[SAP Sales Analysis—Items](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/4509430165e16c32e10000000a114a6b.html?locale=en-us)

### 你現在選「交貨單」時的重要邏輯

這不是單純加總：

```sql
DLN1.Quantity
DLN1.LineTotal
```

而是偏向計算交貨明細尚未被後續文件處理的部分：

```text
可分析數量 ≈ OpenQty
可分析金額 ≈ 原明細金額 × OpenQty / Quantity
退貨數量與金額則反向扣除
```

因此：

- 交貨單完全複製到 A/R 發票後，`DLN1.OpenQty` 通常變成 `0`
- 標準報表選「交貨單」時，該筆可能顯示 `0`，或不再構成未結交貨銷售
- 你的 SQL 仍直接顯示 `T1.Quantity`，所以一定可能不同
- 如果交貨後又發生退貨，標準報表會扣除退貨，而你的 SQL 沒有查 `ORDN/RDN1`

### 如果選「發票」

概念通常是：

```text
淨銷售額 = A/R 發票金額 − A/R 貸項憑證金額
淨數量   = 發票數量 − 貸項憑證數量
```

常見資料來源：

```sql
OINV + INV1
UNION ALL
ORIN + RIN1  -- 數量與金額轉成負數
```

此外，官方特別註明：以 A/R 預收款發票為基礎建立的 A/R 貸項憑證，不會出現在該報表。[SAP Sales Analysis—Sales Employee](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/4509430465e16c32e10000000a114a6b.html)

### 金額口徑

標準銷售分析通常以明細金額為分析基礎，而不是把表頭 `DocTotal` 分配給每個品項：

```sql
INV1.LineTotal
DLN1.LineTotal
RIN1.LineTotal
RDN1.LineTotal
```

這和你目前查詢中的：

```sql
T0.DocTotal
```

差異非常大。`DocTotal` 是整張單據含稅總額，而且每個表身列都會重複顯示；若再加總就會重複。

另外還會受到以下設定影響：

- 顯示本幣或系統幣別
- 未稅銷售額或文件總額
- 表頭折扣如何分攤
- 匯率
- 退貨與貸項憑證
- 取消文件
- 過帳日期、到期日、文件日期篩選
- 依客戶、品項或業務員彙總

### 對照你目前的 SQL

你的 SQL 是：

```text
全部未取消交貨單數量
＋找出由交貨單產生的發票
```

但標準報表選「交貨單」比較接近：

```text
交貨單尚未開票的未結數量／金額
－退貨數量／金額
```

所以兩者本來就不會完全相等。

如果你的目的其實是「已交貨的完整銷售紀錄，旁邊顯示發票號碼」，目前以 `ODLN/DLN1` 為主的方向正確，但不應拿總額直接和標準銷售分析報表比；至少應改用 `T1.LineTotal`，並處理 `INV1` 一對多造成的重複列。

<a id="qa6"></a>

### 6. 這是甚麼

來源：[S132-Q006](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md#q6>)；2026-09-03

**問題**

# Files mentioned by the user:

## codex-clipboard-85ac000f-6988-4a6e-b248-537da3b9c350.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-85ac000f-6988-4a6e-b248-537da3b9c350.png



## My request:
這是甚麼

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這是 SAP Business One 主選單中的一個報表項目：

```text
銷售報表
└─ 電子報表
   └─ AR_Invoice_WS
```

`AR_Invoice_WS` 很可能不是 SAP B1 標準的「銷售分析」報表，而是公司自行建立或由顧問／Add-on 加入的自訂電子報表。

名稱可能代表：

- `AR Invoice`：A/R Invoice，應收帳款發票
- `WS`：可能是 Web Service、Worksheet，或公司內部命名縮寫

它與下面的標準「銷售分析(S)」是不同報表，因此資料來源、日期條件、退貨處理與金額算法都可能不同。

要確認它實際查什麼，可以：

1. 直接執行 `AR_Invoice_WS`，查看跳出的條件畫面及輸出格式。
2. 到「工具 → 查詢 → 查詢管理員」，搜尋同名查詢。
3. 檢查是否由 Electronic Document、Crystal Reports 或其他 Add-on 建立。
4. 請系統管理員查看該選單項目的 SQL、報表配置或 Add-on 來源。

單看畫面只能確定它位於「電子報表」分類，無法確定底層 SQL；但它不是你前面詢問的標準「銷售分析(S)」。

<a id="qa7"></a>

### 7. 如果我想抓項目的最後有異動 抓的到日期嗎

來源：[S132-Q007](<../../原始問答/S132_Explain RIGHT JOIN and LEFT JOIN.md#q7>)；2026-09-03

**問題**

如果我想抓項目的最後有異動 抓的到日期嗎

**歷史回答**

可以，但要先區分你說的「異動」是哪一種。

### 1. 品項主檔最後修改日期

如果是品項主檔資料被修改，例如品名、品項群組、設定等，可從 `OITM` 取得：

```sql
SELECT
    T0.ItemCode,
    T0.ItemName,
    T0.CreateDate,
    T0.UpdateDate,
    T0.UpdateTS
FROM OITM T0
ORDER BY T0.UpdateDate DESC;
```

- `CreateDate`：品項建立日期
- `UpdateDate`：最後修改日期
- `UpdateTS`：最後修改時間，通常是 `HHMMSS` 格式的數字

日期加時間可以寫成：

```sql
SELECT
    T0.ItemCode,
    T0.ItemName,
    T0.UpdateDate,
    STUFF(
        STUFF(
            RIGHT('000000' + CAST(T0.UpdateTS AS VARCHAR(6)), 6),
            3, 0, ':'
        ),
        6, 0, ':'
    ) AS UpdateTime
FROM OITM T0;
```

不過 `OITM.UpdateDate` 只能知道最後一次修改時間，無法直接知道「改了哪個欄位」。

### 2. 品項最後一次庫存交易日期

如果你說的異動是進貨、出貨、庫存轉儲、生產或盤點造成的庫存異動，可從庫存交易紀錄 `OINM` 查：

```sql
SELECT
    T0.ItemCode,
    T0.ItemName,
    MAX(T1.DocDate) AS LastMovementDate
FROM OITM T0
LEFT JOIN OINM T1
    ON T1.ItemCode = T0.ItemCode
GROUP BY
    T0.ItemCode,
    T0.ItemName
ORDER BY
    LastMovementDate DESC;
```

若要同時依倉庫顯示：

```sql
SELECT
    T0.ItemCode,
    T0.ItemName,
    T1.Warehouse,
    MAX(T1.DocDate) AS LastMovementDate
FROM OITM T0
LEFT JOIN OINM T1
    ON T1.ItemCode = T0.ItemCode
GROUP BY
    T0.ItemCode,
    T0.ItemName,
    T1.Warehouse
ORDER BY
    T0.ItemCode,
    T1.Warehouse;
```

### 3. 最後一次銷售／交貨日期

如果是想加到你前面的交貨單查詢，可以取得該品項最後交貨日期：

```sql
SELECT
    T1.ItemCode,
    MAX(T0.DocDate) AS LastDeliveryDate
FROM ODLN T0
INNER JOIN DLN1 T1
    ON T1.DocEntry = T0.DocEntry
WHERE T0.CANCELED = 'N'
GROUP BY
    T1.ItemCode;
```

接到原查詢可以用：

```sql
LEFT JOIN (
    SELECT
        D1.ItemCode,
        MAX(D0.DocDate) AS LastDeliveryDate
    FROM ODLN D0
    INNER JOIN DLN1 D1
        ON D1.DocEntry = D0.DocEntry
    WHERE D0.CANCELED = 'N'
    GROUP BY D1.ItemCode
) L
    ON L.ItemCode = T1.ItemCode
```

然後在 `SELECT` 加上：

```sql
L.LastDeliveryDate
```

簡單來說：

- 主檔最後被修改：`OITM.UpdateDate`
- 最後庫存進出：`MAX(OINM.DocDate)`
- 最後交貨：`MAX(ODLN.DocDate)`
- 最後開發票：`MAX(OINV.DocDate)`

如果目的是找「多久沒有銷售的品項」，通常應抓最後交貨或最後發票日期，而不是 `OITM.UpdateDate`。

