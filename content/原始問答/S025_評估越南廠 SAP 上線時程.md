# 評估越南廠 SAP 上線時程

來源編號：S025  
聊天 ID：`019fa118-4087-7061-ab2f-6f0d6f18704f`  
聊天起始日期（台北）：2026-07-27  
保存位置：一般本機紀錄  
問答段落：17

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S025-Q001｜您們好, 越南廠SAP培訓於7/28固資和財務上機模擬後將全部完成, 原定越南廠於8月正式啟用SAP B1同時與PROGRESS SYS 8,9並行作業兩個月, 但由於SAP系統的…

日期：2026-07-27

**你的提問**

您們好, 越南廠SAP培訓於7/28固資和財務上機模擬後將全部完成, 原定越南廠於8月正式啟用SAP B1同時與PROGRESS SYS 8,9並行作業兩個月, 但由於SAP系統的報表和查核功能仍有諸多不足, 我想聽聽你們的意見 1.照原定排成8月上線 2.延後上線時程( 幾月較恰當) . 3.  其他 .. 謝謝!

我的主管問我這個問題

**當時回答**

建議不要在「照常上線」與「全面延後」之間二選一，而採取「8月試運行、9月正式上線」的方式。報表與查核功能若仍不足，直接正式上線會增加財務結帳、固定資產核對及稽核風險；但完全暫停，也可能讓培訓成果中斷。

可如此回覆主管：


subject: 越南廠 SAP B1 上線時程建議

主管您好，

考量越南廠將於 7/28 完成固定資產及財務上機模擬，但目前 SAP B1 的報表與查核功能仍有部分不足，我建議採取「8月試運行、9月正式上線」的方式，而不是在功能尚未確認前直接全面上線。

建議安排如下：

1. 8月先進行試運行  
SAP B1 與 PROGRESS SYS 8、9 同步操作及核對，但暫時仍以原系統資料作為正式帳務依據。

2. 8月集中改善及驗證  
整理目前缺少的報表、查核功能及差異項目，依「必要、重要、可延後」分類，優先完成會影響月結、固定資產、稅務及稽核的必要項目。

3. 設定正式上線條件  
至少完成期初資料核對、主要交易流程測試、財務及固定資產報表核對、權限與稽核軌跡確認，以及異常處理與備援方案。

4. 建議9月正式上線  
若8月份測試及改善結果符合上述條件，可於9月正式啟用 SAP B1，並與 PROGRESS SYS 8、9 平行作業兩個月；若關鍵問題尚未完成，再依8月底的評估結果決定是否延至10月。

因此，我目前不建議在缺乏明確驗收條件的情況下於8月直接正式上線，也不建議現在就無期限延後。較穩健的作法是將8月定位為試運行及改善期，以9月正式上線為目標，並設置明確的上線／延後判斷標準。

以上是我的建議，供您參考，謝謝。

---

<a id="q2"></a>

## S025-Q002｜sapb1 收貨採購單 的草稿

日期：2026-07-27

**你的提問**

sapb1 收貨採購單 的草稿

**當時回答**

您是想了解 SAP B1「收貨採購單草稿」的哪一部分？

- 如何建立及儲存草稿
- 如何查詢、修改或刪除草稿
- 如何由草稿轉成正式收貨採購單
- 草稿的報表／查核方式
- 草稿是否會影響庫存與會計帳

一般而言，**草稿尚未正式過帳，不會增加庫存，也不會產生會計分錄**；轉成正式收貨採購單後才會生效。

---

<a id="q3"></a>

## S025-Q003｜table

日期：2026-07-27

**你的提問**

table

**當時回答**

SAP Business One「收貨採購單草稿」使用以下資料表：

| 資料內容 | 草稿 Table | 正式單據 Table |
|---|---|---|
| 表頭 | `ODRF` | `OPDN` |
| 明細 | `DRF1` | `PDN1` |
| 單據類型 | `ObjType = 20` | `ObjType = 20` |

SQL Server 查詢範例：

```sql
SELECT
    T0.DocEntry,
    T0.DocNum,
    T0.DocDate,
    T0.CardCode,
    T0.CardName,
    T0.DocTotal,
    T1.LineNum,
    T1.ItemCode,
    T1.Dscription,
    T1.Quantity,
    T1.WhsCode
FROM ODRF T0
INNER JOIN DRF1 T1
    ON T0.DocEntry = T1.DocEntry
WHERE T0.ObjType = '20'
ORDER BY T0.DocEntry, T1.LineNum;
```

重點：`ODRF`、`DRF1`同時存放多種單據的草稿，所以一定要加上：

```sql
WHERE T0.ObjType = '20'
```

避免把採購單、應付發票等其他草稿一起查出來。

---

<a id="q4"></a>

## S025-Q004｜OPOR 跟 OPDN 會有交集嗎

日期：2026-07-27

**你的提問**

OPOR 跟 OPDN 會有交集嗎

**當時回答**

有關聯，但**不會直接用 `DocEntry` 相連**。

- `OPOR`：採購單表頭
- `POR1`：採購單明細
- `OPDN`：收貨採購單表頭
- `PDN1`：收貨採購單明細

關聯資訊記錄在收貨採購單明細 `PDN1`：

| `PDN1` 欄位 | 對應內容 |
|---|---|
| `BaseType` | 基礎單據類型；採購單為 `22` |
| `BaseEntry` | `OPOR.DocEntry` |
| `BaseLine` | `POR1.LineNum` |

SQL Server 查詢：

```sql
SELECT
    PO.DocNum     AS PurchaseOrderNo,
    PO.DocDate    AS PurchaseOrderDate,
    PO.CardCode,
    PO.CardName,
    POR.LineNum   AS PurchaseOrderLine,
    POR.ItemCode,
    POR.Quantity  AS OrderedQuantity,

    GRPO.DocNum   AS GoodsReceiptPONo,
    GRPO.DocDate  AS GoodsReceiptDate,
    GRL.LineNum   AS GoodsReceiptLine,
    GRL.Quantity  AS ReceivedQuantity
FROM OPOR PO
INNER JOIN POR1 POR
    ON PO.DocEntry = POR.DocEntry
LEFT JOIN PDN1 GRL
    ON GRL.BaseType  = 22
   AND GRL.BaseEntry = POR.DocEntry
   AND GRL.BaseLine  = POR.LineNum
LEFT JOIN OPDN GRPO
    ON GRPO.DocEntry = GRL.DocEntry
ORDER BY PO.DocNum, POR.LineNum, GRPO.DocNum;
```

如果只想查「已經有收貨」的採購單，把兩個 `LEFT JOIN` 改成 `INNER JOIN`。

另外要注意：

- 一張採購單可以分多次收貨，因此一筆 `POR1` 可能對應多筆 `PDN1`。
- 一張收貨採購單也可以引用多張採購單。
- 若收貨採購單是手動建立、沒有「複製自採購單」，`PDN1.BaseEntry` 可能為空或 `-1`，這時無法從文件流直接連回 `OPOR`。
- `OPOR.DocEntry = OPDN.DocEntry` 即使數字碰巧相同，也不代表兩張單據有關聯。

---

<a id="q5"></a>

## S025-Q005｜可以先寫一小段讓我測試收貨採購單跟採購單的關聯嗎

日期：2026-07-27

**你的提問**

可以先寫一小段讓我測試收貨採購單跟採購單的關聯嗎

**當時回答**

可以，先用這段簡單查詢測試。它會列出「由採購單複製產生」的收貨採購單明細：

```sql
SELECT TOP 20
    T0.DocNum       AS [採購單號],
    T1.LineNum + 1  AS [採購單列號],
    T1.ItemCode     AS [品號],
    T1.Quantity     AS [採購數量],
    T2.DocNum       AS [收貨採購單號],
    T3.LineNum + 1  AS [收貨列號],
    T3.Quantity     AS [收貨數量],
    T3.BaseType,
    T3.BaseEntry,
    T3.BaseLine
FROM OPOR T0
INNER JOIN POR1 T1
    ON T0.DocEntry = T1.DocEntry
INNER JOIN PDN1 T3
    ON T3.BaseType  = 22
   AND T3.BaseEntry = T1.DocEntry
   AND T3.BaseLine  = T1.LineNum
INNER JOIN OPDN T2
    ON T2.DocEntry = T3.DocEntry
ORDER BY T2.DocEntry DESC, T3.LineNum;
```

關聯核心是：

```sql
PDN1.BaseType  = 22
PDN1.BaseEntry = OPOR.DocEntry
PDN1.BaseLine  = POR1.LineNum
```

若系統是 SAP HANA，將第一行的：

```sql
SELECT TOP 20
```

改成 `SELECT`，並在查詢最後加入：

```sql
LIMIT 20
```

---

<a id="q6"></a>

## S025-Q006｜採購單號、品料號(項目) 、採購數量、累計入庫數量、累計收貨(草稿數量) 、累計退貨數量、草稿單號、草稿收貨數量 草稿的ORDF

日期：2026-07-27

**你的提問**

**採購單號、品料號(項目) 、採購數量、累計入庫數量、累計收貨(草稿數量) 、累計退貨數量、草稿單號、草稿收貨數量**

草稿的ORDF

**當時回答**

草稿表正確名稱是 **`ODRF`**，不是 `ORDF`；草稿明細是 **`DRF1`**。以下為 SQL Server 查詢，一筆資料以「採購單明細＋草稿單」呈現：

```sql
WITH ReceiptQty AS
(
    -- 已正式入庫的收貨採購單數量
    SELECT
        P.BaseEntry AS PODocEntry,
        P.BaseLine  AS POLineNum,
        SUM(P.Quantity) AS ReceivedQty
    FROM PDN1 P
    INNER JOIN OPDN H
        ON H.DocEntry = P.DocEntry
       AND H.CANCELED = 'N'
    WHERE P.BaseType = 22
    GROUP BY
        P.BaseEntry,
        P.BaseLine
),
ReturnQty AS
(
    -- 已正式過帳的採購退貨數量
    SELECT
        GR.BaseEntry AS PODocEntry,
        GR.BaseLine  AS POLineNum,
        SUM(R.Quantity) AS ReturnedQty
    FROM RPD1 R
    INNER JOIN ORPD RH
        ON RH.DocEntry = R.DocEntry
       AND RH.CANCELED = 'N'
    INNER JOIN PDN1 GR
        ON R.BaseType  = 20
       AND R.BaseEntry = GR.DocEntry
       AND R.BaseLine  = GR.LineNum
    WHERE GR.BaseType = 22
    GROUP BY
        GR.BaseEntry,
        GR.BaseLine
),
DraftDetail AS
(
    -- 收貨採購單草稿：ObjType = 20
    SELECT
        D1.BaseEntry AS PODocEntry,
        D1.BaseLine  AS POLineNum,
        D0.DocEntry  AS DraftEntry,
        D0.DocNum    AS DraftDocNum,
        SUM(D1.Quantity) AS DraftReceiptQty
    FROM ODRF D0
    INNER JOIN DRF1 D1
        ON D0.DocEntry = D1.DocEntry
    WHERE D0.ObjType = '20'
      AND D1.BaseType = 22
    GROUP BY
        D1.BaseEntry,
        D1.BaseLine,
        D0.DocEntry,
        D0.DocNum
),
DraftTotal AS
(
    -- 每一筆採購單明細的全部草稿收貨累計
    SELECT
        PODocEntry,
        POLineNum,
        SUM(DraftReceiptQty) AS TotalDraftReceiptQty
    FROM DraftDetail
    GROUP BY
        PODocEntry,
        POLineNum
)
SELECT
    PO.DocNum                         AS [採購單號],
    POL.ItemCode                      AS [品料號(項目)],
    POL.Quantity                      AS [採購數量],
    ISNULL(RC.ReceivedQty, 0)         AS [累計入庫數量],
    ISNULL(DT.TotalDraftReceiptQty, 0) AS [累計收貨(草稿數量)],
    ISNULL(RT.ReturnedQty, 0)         AS [累計退貨數量],
    DD.DraftEntry                     AS [草稿單號],
    ISNULL(DD.DraftReceiptQty, 0)     AS [草稿收貨數量]
FROM OPOR PO
INNER JOIN POR1 POL
    ON PO.DocEntry = POL.DocEntry
LEFT JOIN ReceiptQty RC
    ON RC.PODocEntry = POL.DocEntry
   AND RC.POLineNum  = POL.LineNum
LEFT JOIN ReturnQty RT
    ON RT.PODocEntry = POL.DocEntry
   AND RT.POLineNum  = POL.LineNum
LEFT JOIN DraftTotal DT
    ON DT.PODocEntry = POL.DocEntry
   AND DT.POLineNum  = POL.LineNum
LEFT JOIN DraftDetail DD
    ON DD.PODocEntry = POL.DocEntry
   AND DD.POLineNum  = POL.LineNum
WHERE PO.CANCELED = 'N'
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

其中：

- `累計入庫數量`：正式收貨採購單 `OPDN/PDN1`
- `累計收貨(草稿數量)`：該採購明細所有收貨草稿的數量合計
- `累計退貨數量`：由正式收貨採購單產生的採購退貨 `ORPD/RPD1`
- `草稿單號`：使用 `ODRF.DocEntry`，這通常是辨識草稿最可靠的內部編號
- 同一筆採購明細若存在多張收貨草稿，查詢會顯示多列；累計數量相同，但每列會列出各自的草稿單號及草稿收貨數量。

---

<a id="q7"></a>

## S025-Q007｜但我的篩選條件想用採購單可以嗎 我填入採購單但號

日期：2026-07-27

**你的提問**

但我的篩選條件想用採購單可以嗎 我填入採購單但號

**當時回答**

可以，篩選條件使用「採購單號 `OPOR.DocNum`」即可。

把原查詢最後的 `WHERE` 改成：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = [%0]
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

在 SAP B1 查詢管理員執行時，系統會跳出參數視窗，您在 `[%0]` 輸入採購單號即可。

若 SAP B1 無法自動辨識參數，建議在整段 SQL 最前面加入：

```sql
/* SELECT FROM OPOR P0 */
DECLARE @PONum INT

/* WHERE */
SET @PONum = /* P0.DocNum */ '[%0]';
```

然後查詢最後改成：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

請輸入畫面上看到的「採購單號」，也就是 `DocNum`，不是系統內部的 `DocEntry`。

---

<a id="q8"></a>

## S025-Q008｜WITH ReceiptQty AS ( -- 已正式入庫的收貨採購單數量 SELECT P.BaseEntry AS PODocEntry, P.BaseLine AS POLi…

日期：2026-07-27

**你的提問**

WITH ReceiptQty AS
(
    -- 已正式入庫的收貨採購單數量
    SELECT
        P.BaseEntry AS PODocEntry,
        P.BaseLine  AS POLineNum,
        SUM(P.Quantity) AS ReceivedQty
    FROM PDN1 P
    INNER JOIN OPDN H
        ON H.DocEntry = P.DocEntry
       AND H.CANCELED = 'N'
    WHERE P.BaseType = 22
    GROUP BY
        P.BaseEntry,
        P.BaseLine
),
ReturnQty AS
(
    -- 已正式過帳的採購退貨數量
    SELECT
        GR.BaseEntry AS PODocEntry,
        GR.BaseLine  AS POLineNum,
        SUM(R.Quantity) AS ReturnedQty
    FROM RPD1 R
    INNER JOIN ORPD RH
        ON RH.DocEntry = R.DocEntry
       AND RH.CANCELED = 'N'
    INNER JOIN PDN1 GR
        ON R.BaseType  = 20
       AND R.BaseEntry = GR.DocEntry
       AND R.BaseLine  = GR.LineNum
    WHERE GR.BaseType = 22
    GROUP BY
        GR.BaseEntry,
        GR.BaseLine
),
DraftDetail AS
(
    -- 收貨採購單草稿：ObjType = 20
    SELECT
        D1.BaseEntry AS PODocEntry,
        D1.BaseLine  AS POLineNum,
        D0.DocEntry  AS DraftEntry,
        D0.DocNum    AS DraftDocNum,
        SUM(D1.Quantity) AS DraftReceiptQty
    FROM ODRF D0
    INNER JOIN DRF1 D1
        ON D0.DocEntry = D1.DocEntry
    WHERE D0.ObjType = '20'
      AND D1.BaseType = 22
    GROUP BY
        D1.BaseEntry,
        D1.BaseLine,
        D0.DocEntry,
        D0.DocNum
),
DraftTotal AS
(
    -- 每一筆採購單明細的全部草稿收貨累計
    SELECT
        PODocEntry,
        POLineNum,
        SUM(DraftReceiptQty) AS TotalDraftReceiptQty
    FROM DraftDetail
    GROUP BY
        PODocEntry,
        POLineNum
)
SELECT
    PO.DocNum                         AS [採購單號],
    POL.ItemCode                      AS [品料號(項目)],
    POL.Quantity                      AS [採購數量],
    ISNULL(RC.ReceivedQty, 0)         AS [累計入庫數量],
    ISNULL(DT.TotalDraftReceiptQty, 0) AS [累計收貨(草稿數量)],
    ISNULL(RT.ReturnedQty, 0)         AS [累計退貨數量],
    DD.DraftEntry                     AS [草稿單號],
    ISNULL(DD.DraftReceiptQty, 0)     AS [草稿收貨數量]
FROM OPOR PO
INNER JOIN POR1 POL
    ON PO.DocEntry = POL.DocEntry
LEFT JOIN ReceiptQty RC
    ON RC.PODocEntry = POL.DocEntry
   AND RC.POLineNum  = POL.LineNum
LEFT JOIN ReturnQty RT
    ON RT.PODocEntry = POL.DocEntry
   AND RT.POLineNum  = POL.LineNum
LEFT JOIN DraftTotal DT
    ON DT.PODocEntry = POL.DocEntry
   AND DT.POLineNum  = POL.LineNum
LEFT JOIN DraftDetail DD
    ON DD.PODocEntry = POL.DocEntry
   AND DD.POLineNum  = POL.LineNum
WHERE PO.CANCELED = 'N'
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;


幫我解析這段

**當時回答**

這段 SQL 的目的，是以「採購單明細」為主體，把正式入庫、收貨草稿與採購退貨的數量整合在同一份報表中。

整體資料關係如下：

```text
採購單
OPOR → POR1
          │
          ├─ 正式收貨：PDN1 → OPDN
          ├─ 收貨草稿：DRF1 → ODRF
          └─ 採購退貨：PDN1 ← RPD1 → ORPD
```

## 1. `ReceiptQty`：累計正式入庫數量

```sql
WITH ReceiptQty AS
(
    SELECT
        P.BaseEntry AS PODocEntry,
        P.BaseLine  AS POLineNum,
        SUM(P.Quantity) AS ReceivedQty
    FROM PDN1 P
    INNER JOIN OPDN H
        ON H.DocEntry = P.DocEntry
       AND H.CANCELED = 'N'
    WHERE P.BaseType = 22
    GROUP BY
        P.BaseEntry,
        P.BaseLine
)
```

資料來源：

- `OPDN`：收貨採購單表頭
- `PDN1`：收貨採購單明細
- `BaseType = 22`：表示這筆收貨是由採購單產生

關聯欄位：

```text
PDN1.BaseEntry = OPOR.DocEntry
PDN1.BaseLine  = POR1.LineNum
```

如果一筆採購單明細分三次入庫：

```text
第一次：30
第二次：40
第三次：20
```

此區段會彙總為：

```text
ReceivedQty = 90
```

`H.CANCELED = 'N'` 用來排除已取消的收貨採購單。

---

## 2. `ReturnQty`：累計採購退貨數量

```sql
ReturnQty AS
(
    SELECT
        GR.BaseEntry AS PODocEntry,
        GR.BaseLine  AS POLineNum,
        SUM(R.Quantity) AS ReturnedQty
    FROM RPD1 R
    INNER JOIN ORPD RH
        ON RH.DocEntry = R.DocEntry
       AND RH.CANCELED = 'N'
    INNER JOIN PDN1 GR
        ON R.BaseType  = 20
       AND R.BaseEntry = GR.DocEntry
       AND R.BaseLine  = GR.LineNum
    WHERE GR.BaseType = 22
    GROUP BY
        GR.BaseEntry,
        GR.BaseLine
)
```

資料來源：

- `ORPD`：採購退貨表頭
- `RPD1`：採購退貨明細
- `PDN1`：原始收貨採購單明細

由於採購退貨通常是從「收貨採購單」複製產生，所以需要經過兩層關聯：

```text
採購退貨 RPD1
    ↓ BaseType = 20
收貨採購單 PDN1
    ↓ BaseType = 22
原始採購單 POR1
```

關鍵欄位：

```sql
R.BaseType  = 20
R.BaseEntry = GR.DocEntry
R.BaseLine  = GR.LineNum
```

找到原收貨明細後，再由：

```sql
GR.BaseEntry
GR.BaseLine
```

找出原始採購單的 `DocEntry` 和 `LineNum`。

例如已入庫100個、後續退貨10個：

```text
累計入庫數量 = 100
累計退貨數量 = 10
```

目前查詢不會自動計算淨入庫數量；如有需要，可以新增：

```sql
ISNULL(RC.ReceivedQty, 0)
- ISNULL(RT.ReturnedQty, 0) AS [淨入庫數量]
```

---

## 3. `DraftDetail`：每一張收貨草稿的數量

```sql
DraftDetail AS
(
    SELECT
        D1.BaseEntry AS PODocEntry,
        D1.BaseLine  AS POLineNum,
        D0.DocEntry  AS DraftEntry,
        D0.DocNum    AS DraftDocNum,
        SUM(D1.Quantity) AS DraftReceiptQty
    FROM ODRF D0
    INNER JOIN DRF1 D1
        ON D0.DocEntry = D1.DocEntry
    WHERE D0.ObjType = '20'
      AND D1.BaseType = 22
    GROUP BY
        D1.BaseEntry,
        D1.BaseLine,
        D0.DocEntry,
        D0.DocNum
)
```

資料來源：

- `ODRF`：草稿表頭
- `DRF1`：草稿明細
- `ODRF.ObjType = 20`：收貨採購單草稿
- `DRF1.BaseType = 22`：草稿明細由採購單產生

這個區段會保留每張草稿：

| 採購單明細 | 草稿單號 | 草稿數量 |
|---|---:|---:|
| PO第1列 | 101 | 20 |
| PO第1列 | 105 | 30 |

因此同一筆採購單明細有多張草稿時，`DraftDetail` 會有多筆資料。

目前輸出的草稿單號使用：

```sql
D0.DocEntry AS DraftEntry
```

這是草稿的內部識別碼。雖然也讀取了 `D0.DocNum`，但最終查詢沒有顯示它。

---

## 4. `DraftTotal`：所有草稿累計數量

```sql
DraftTotal AS
(
    SELECT
        PODocEntry,
        POLineNum,
        SUM(DraftReceiptQty) AS TotalDraftReceiptQty
    FROM DraftDetail
    GROUP BY
        PODocEntry,
        POLineNum
)
```

此區段將同一筆採購單明細的所有草稿加總。

例如：

| 草稿單號 | 草稿收貨數量 |
|---|---:|
| 101 | 20 |
| 105 | 30 |

彙總結果：

```text
累計收貨（草稿數量）= 50
```

所以：

- `DraftDetail`：顯示每張草稿的個別數量
- `DraftTotal`：顯示所有草稿的合計數量

---

## 5. 主查詢：以採購單為基礎

```sql
FROM OPOR PO
INNER JOIN POR1 POL
    ON PO.DocEntry = POL.DocEntry
```

主體是：

- `OPOR`：採購單表頭
- `POR1`：採購單明細

所以報表最基本的資料單位是「一張採購單中的一個項目列」。

接著使用 `LEFT JOIN` 加入正式收貨、退貨及草稿：

```sql
LEFT JOIN ReceiptQty RC
LEFT JOIN ReturnQty RT
LEFT JOIN DraftTotal DT
LEFT JOIN DraftDetail DD
```

使用 `LEFT JOIN` 的效果是：即使採購單還沒有收貨、退貨或草稿，採購單明細仍然會顯示，數量則為 `0`。

---

## 6. 最後顯示的欄位

```sql
SELECT
    PO.DocNum                          AS [採購單號],
    POL.ItemCode                       AS [品料號(項目)],
    POL.Quantity                       AS [採購數量],
    ISNULL(RC.ReceivedQty, 0)          AS [累計入庫數量],
    ISNULL(DT.TotalDraftReceiptQty, 0) AS [累計收貨(草稿數量)],
    ISNULL(RT.ReturnedQty, 0)          AS [累計退貨數量],
    DD.DraftEntry                      AS [草稿單號],
    ISNULL(DD.DraftReceiptQty, 0)      AS [草稿收貨數量]
```

`ISNULL(..., 0)` 表示沒有相關單據時，把空值 `NULL` 顯示成 `0`。

例如：

| 採購單號 | 品料號 | 採購量 | 入庫量 | 草稿累計 | 退貨量 | 草稿單號 | 該草稿數量 |
|---|---|---:|---:|---:|---:|---:|---:|
| 10001 | A001 | 100 | 60 | 30 | 5 | 101 | 10 |
| 10001 | A001 | 100 | 60 | 30 | 5 | 105 | 20 |

因為有兩張草稿，所以同一筆採購明細會顯示兩列。`草稿累計30`會在兩列重複顯示，這是目前查詢設計的正常結果。

---

## 7. 篩選與排序

```sql
WHERE PO.CANCELED = 'N'
```

目前只排除已取消的採購單，沒有指定採購單號。

如要讓使用者輸入採購單號，可改成：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = [%0]
```

排序方式：

```sql
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

依序按照：

1. 採購單號
2. 採購單明細列號
3. 草稿內部編號

排列結果。

## 需要留意的地方

- 採購退貨目前只統計「由收貨採購單複製」的退貨。
- 手動建立、沒有基礎單據關聯的收貨或退貨，無法追溯到原採購單。
- 一筆採購明細存在多張草稿時，會產生多列。
- 草稿累計數量可能包含已經不再使用、但仍留在系統中的草稿；是否需要排除特定草稿狀態，要依您公司的流程決定。
- 若同一品料號在採購單中出現多列，目前畫面沒有顯示 `LineNum`，看起來可能像重複资料。建議增加：

```sql
POL.LineNum + 1 AS [採購單列號]
```

---

<a id="q9"></a>

## S025-Q009｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion failed when convertin…

日期：2026-07-27

**你的提問**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion failed when converting the varchar value '%0' to data type int.  '後端排程工作' (OBSJ)

**當時回答**

這個錯誤表示 SQL Server 收到的是文字 `'%0'`，而不是您輸入的採購單號；但 `OPOR.DocNum` 是整數，所以轉換失敗。

另外訊息顯示「後端排程工作 `(OBSJ)`」，很可能這段查詢是透過背景排程執行。背景排程無法跳出 `[%0]` 的輸入視窗，因此不能直接使用互動式參數。

### 在查詢管理員手動執行

確認條件寫成 `[%0]`，不能寫成 `%0`：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = [%0]
```

更穩定的 SAP B1 SQL Server 寫法：

```sql
/* SELECT FROM OPOR P0 */
DECLARE @PONum INT;

/* WHERE */
SET @PONum = /* P0.DocNum */ '[%0]';

WITH ReceiptQty AS
(
    -- 原本內容
    SELECT
        P.BaseEntry AS PODocEntry,
        P.BaseLine AS POLineNum,
        SUM(P.Quantity) AS ReceivedQty
    FROM PDN1 P
    INNER JOIN OPDN H
        ON H.DocEntry = P.DocEntry
       AND H.CANCELED = 'N'
    WHERE P.BaseType = 22
    GROUP BY P.BaseEntry, P.BaseLine
)

/* 後面接原查詢 */
```

主查詢最後使用：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
```

### 如果只是先測試

直接暫時填入實際採購單號，例如：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = 12345
```

`12345` 請換成實際採購單號。

### 如果必須由後端排程執行

後端排程不能等待使用者輸入 `[%0]`，需要改成不含互動參數，例如：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocDate >= DATEADD(DAY, -1, CAST(GETDATE() AS DATE))
```

或把要查詢的採購單號存入自訂設定表，再由排程讀取。

不建議只改成：

```sql
TRY_CONVERT(INT, '[%0]')
```

雖然可能不再報錯，但排程仍然沒有實際採購單號，最後只會查不到資料，並沒有真正解決問題。

---

<a id="q10"></a>

## S025-Q010｜WHERE PO.CANCELED = 'N' AND PO.DocDate >= DATEADD(DAY, -1, CAST(GETDATE() AS DATE)) 這是甚麼意思

日期：2026-07-27

**你的提問**

WHERE PO.CANCELED = 'N'
  AND PO.DocDate >= DATEADD(DAY, -1, CAST(GETDATE() AS DATE))  這是甚麼意思

**當時回答**

這個條件表示：

> 只查詢「未取消」且「過帳日期從昨天開始到未來」的採購單。

```sql
WHERE PO.CANCELED = 'N'
```

表示排除已取消的採購單：

- `N`：未取消
- `Y`：已取消

```sql
AND PO.DocDate >= DATEADD(DAY, -1, CAST(GETDATE() AS DATE))
```

各部分意思：

- `GETDATE()`：取得現在的日期與時間
- `CAST(GETDATE() AS DATE)`：只保留今天日期，移除時間
- `DATEADD(DAY, -1, ...)`：往前減一天，得到昨天日期
- `PO.DocDate >=`：採購單過帳日期大於或等於昨天

假設今天是 `2026-07-27`，條件等同於：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocDate >= '2026-07-26'
```

因此會包含：

- 昨天的採購單
- 今天的採購單
- 未來日期的採購單

如果只想查昨天，不包含今天，可以寫成：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocDate >= DATEADD(DAY, -1, CAST(GETDATE() AS DATE))
  AND PO.DocDate <  CAST(GETDATE() AS DATE)
```

如果要查今天：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocDate >= CAST(GETDATE() AS DATE)
  AND PO.DocDate <  DATEADD(DAY, 1, CAST(GETDATE() AS DATE))
```

不過您原本的需求是「輸入採購單號查詢」，若是由使用者手動執行，還是應使用：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = [%0]
```

日期條件主要是給無法輸入參數的後端排程使用。

---

<a id="q11"></a>

## S025-Q011｜對 我想要篩選單號 可以嗎

日期：2026-07-27

**你的提問**

對 我想要篩選單號 可以嗎

**當時回答**

可以，您可以輸入採購單號篩選。請在完整 SQL 最前面加入：

```sql
/* SELECT FROM OPOR T0 */
DECLARE @PONum INT;

/* WHERE */
SET @PONum = /* T0.DocNum */ '[%0]';

;WITH ReceiptQty AS
(
```

注意原本的：

```sql
WITH ReceiptQty AS
```

要改成：

```sql
;WITH ReceiptQty AS
```

然後把主查詢最後的條件改成：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

執行查詢時，SAP B1 會出現參數輸入視窗，請直接輸入採購單號，例如：

```text
23000125
```

完整結構會是：

```sql
/* SELECT FROM OPOR T0 */
DECLARE @PONum INT;

/* WHERE */
SET @PONum = /* T0.DocNum */ '[%0]';

;WITH ReceiptQty AS
(
    -- 原本的內容
),
ReturnQty AS
(
    -- 原本的內容
),
DraftDetail AS
(
    -- 原本的內容
),
DraftTotal AS
(
    -- 原本的內容
)
SELECT
    -- 原本的欄位
FROM OPOR PO
INNER JOIN POR1 POL
    ON PO.DocEntry = POL.DocEntry
-- 原本的其他 JOIN
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

這種 `[%0]` 輸入方式應在 SAP B1「查詢管理員」中手動執行。如果放在警報或背景排程中，因為排程無法等待人工輸入採購單號，便不適用。

---

<a id="q12"></a>

## S025-Q012｜SQL／程式碼調整與說明

日期：2026-07-27

**你的提問**

DECLARE @PONum INT;
SET @PONum =  PO.DocNum 


WITH ReceiptQty AS
(
    -- 已正式入庫的收貨採購單數量
    SELECT
        P.BaseEntry AS PODocEntry,
        P.BaseLine  AS POLineNum,
        SUM(P.Quantity) AS ReceivedQty
    FROM PDN1 P
    INNER JOIN OPDN H
        ON H.DocEntry = P.DocEntry
       AND H.CANCELED = 'N'
    WHERE P.BaseType = 22
    GROUP BY
        P.BaseEntry,
        P.BaseLine
),
ReturnQty AS
(
    -- 已正式過帳的採購退貨數量
    SELECT
        GR.BaseEntry AS PODocEntry,
        GR.BaseLine  AS POLineNum,
        SUM(R.Quantity) AS ReturnedQty
    FROM RPD1 R
    INNER JOIN ORPD RH
        ON RH.DocEntry = R.DocEntry
       AND RH.CANCELED = 'N'
    INNER JOIN PDN1 GR
        ON R.BaseType  = 20
       AND R.BaseEntry = GR.DocEntry
       AND R.BaseLine  = GR.LineNum
    WHERE GR.BaseType = 22
    GROUP BY
        GR.BaseEntry,
        GR.BaseLine
),
DraftDetail AS
(
    -- 收貨採購單草稿：ObjType = 20
    SELECT
        D1.BaseEntry AS PODocEntry,
        D1.BaseLine  AS POLineNum,
        D0.DocEntry  AS DraftEntry,
        D0.DocNum    AS DraftDocNum,
        SUM(D1.Quantity) AS DraftReceiptQty
    FROM ODRF D0
    INNER JOIN DRF1 D1
        ON D0.DocEntry = D1.DocEntry
    WHERE D0.ObjType = '20'
      AND D1.BaseType = 22
    GROUP BY
        D1.BaseEntry,
        D1.BaseLine,
        D0.DocEntry,
        D0.DocNum
),
DraftTotal AS
(
    -- 每一筆採購單明細的全部草稿收貨累計
    SELECT
        PODocEntry,
        POLineNum,
        SUM(DraftReceiptQty) AS TotalDraftReceiptQty
    FROM DraftDetail
    GROUP BY
        PODocEntry,
        POLineNum
)
SELECT
    PO.DocNum                         AS [採購單號],
    POL.ItemCode                      AS [品料號(項目)],
    POL.Quantity                      AS [採購數量],
    ISNULL(RC.ReceivedQty, 0)         AS [累計入庫數量],
    ISNULL(DT.TotalDraftReceiptQty, 0) AS [累計收貨(草稿數量)],
    ISNULL(RT.ReturnedQty, 0)         AS [累計退貨數量],
    DD.DraftEntry                     AS [草稿單號],
    ISNULL(DD.DraftReceiptQty, 0)     AS [草稿收貨數量]
FROM OPOR PO
INNER JOIN POR1 POL
    ON PO.DocEntry = POL.DocEntry
LEFT JOIN ReceiptQty RC
    ON RC.PODocEntry = POL.DocEntry
   AND RC.POLineNum  = POL.LineNum
LEFT JOIN ReturnQty RT
    ON RT.PODocEntry = POL.DocEntry
   AND RT.POLineNum  = POL.LineNum
LEFT JOIN DraftTotal DT
    ON DT.PODocEntry = POL.DocEntry
   AND DT.POLineNum  = POL.LineNum
LEFT JOIN DraftDetail DD
    ON DD.PODocEntry = POL.DocEntry
   AND DD.POLineNum  = POL.LineNum
WHERE PO.CANCELED = 'N' AND @PONum = 100000001'
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;

這是我寫在 sapb1 查詢工具裡面的

**當時回答**

目前有四個問題：

1. `PO.DocNum` 的別名 `PO` 要到主查詢 `FROM OPOR PO` 才存在，不能在最前面使用。
2. `WITH` 前面需要分號：`;WITH`。
3. `100000001'` 最後多了一個單引號。
4. `WHERE @PONum = 100000001` 沒有拿採購單欄位來篩選，應為 `PO.DocNum = @PONum`。

如果要在 SAP B1 查詢工具執行時輸入採購單號，請把最前面改成：

```sql
/* SELECT FROM OPOR T0 */
DECLARE @PONum INT;

/* WHERE */
SET @PONum = /* T0.DocNum */ '[%0]';

;WITH ReceiptQty AS
(
```

中間的 CTE 內容維持不變，最後條件改成：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

也就是完整查詢的關鍵結構：

```sql
/* SELECT FROM OPOR T0 */
DECLARE @PONum INT;

/* WHERE */
SET @PONum = /* T0.DocNum */ '[%0]';

;WITH ReceiptQty AS
(
    -- 原本內容
),
ReturnQty AS
(
    -- 原本內容
),
DraftDetail AS
(
    -- 原本內容
),
DraftTotal AS
(
    -- 原本內容
)
SELECT
    -- 原本內容
FROM OPOR PO
INNER JOIN POR1 POL
    ON PO.DocEntry = POL.DocEntry
-- 原本的 LEFT JOIN
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

如果只是固定測試採購單 `100000001`，最前面可直接寫：

```sql
DECLARE @PONum INT;
SET @PONum = 100000001;

;WITH ReceiptQty AS
(
```

最後仍然必須寫：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
```

不能寫：

```sql
SET @PONum = PO.DocNum
```

因為 SQL 執行到這一行時，尚未讀取 `OPOR`，所以還不知道 `PO` 是哪一個資料表。

---

<a id="q13"></a>

## S025-Q013｜所以完整怎麼寫比較好

日期：2026-07-27

**你的提問**

所以完整怎麼寫比較好

**當時回答**

以下是可直接貼到 SAP B1「查詢管理員」的完整 SQL Server 版本。執行時會要求輸入採購單號。

```sql
/* SELECT FROM OPOR T0 */
DECLARE @PONum INT;

/* WHERE */
SET @PONum = /* T0.DocNum */ '[%0]';

;WITH ReceiptQty AS
(
    /* 正式收貨採購單累計入庫數量 */
    SELECT
        P.BaseEntry AS PODocEntry,
        P.BaseLine  AS POLineNum,
        SUM(P.Quantity) AS ReceivedQty
    FROM PDN1 P
    INNER JOIN OPDN H
        ON H.DocEntry = P.DocEntry
       AND H.CANCELED = 'N'
    WHERE P.BaseType = 22
    GROUP BY
        P.BaseEntry,
        P.BaseLine
),
ReturnQty AS
(
    /* 已正式過帳的採購退貨數量 */
    SELECT
        GR.BaseEntry AS PODocEntry,
        GR.BaseLine  AS POLineNum,
        SUM(R.Quantity) AS ReturnedQty
    FROM RPD1 R
    INNER JOIN ORPD RH
        ON RH.DocEntry = R.DocEntry
       AND RH.CANCELED = 'N'
    INNER JOIN PDN1 GR
        ON R.BaseType  = 20
       AND R.BaseEntry = GR.DocEntry
       AND R.BaseLine  = GR.LineNum
    WHERE GR.BaseType = 22
    GROUP BY
        GR.BaseEntry,
        GR.BaseLine
),
DraftDetail AS
(
    /* 收貨採購單草稿明細 */
    SELECT
        D1.BaseEntry AS PODocEntry,
        D1.BaseLine  AS POLineNum,
        D0.DocEntry  AS DraftEntry,
        D0.DocNum    AS DraftDocNum,
        SUM(D1.Quantity) AS DraftReceiptQty
    FROM ODRF D0
    INNER JOIN DRF1 D1
        ON D0.DocEntry = D1.DocEntry
    WHERE D0.ObjType = '20'
      AND D1.BaseType = 22
    GROUP BY
        D1.BaseEntry,
        D1.BaseLine,
        D0.DocEntry,
        D0.DocNum
),
DraftTotal AS
(
    /* 每筆採購單明細的全部收貨草稿累計 */
    SELECT
        PODocEntry,
        POLineNum,
        SUM(DraftReceiptQty) AS TotalDraftReceiptQty
    FROM DraftDetail
    GROUP BY
        PODocEntry,
        POLineNum
)
SELECT
    PO.DocNum                           AS [採購單號],
    POL.LineNum + 1                     AS [採購單列號],
    POL.ItemCode                        AS [品料號(項目)],
    POL.Dscription                      AS [項目說明],
    POL.Quantity                        AS [採購數量],

    ISNULL(RC.ReceivedQty, 0)           AS [累計入庫數量],
    ISNULL(DT.TotalDraftReceiptQty, 0)  AS [累計收貨(草稿數量)],
    ISNULL(RT.ReturnedQty, 0)           AS [累計退貨數量],

    ISNULL(RC.ReceivedQty, 0)
      - ISNULL(RT.ReturnedQty, 0)       AS [淨入庫數量],

    DD.DraftEntry                       AS [草稿單號],
    ISNULL(DD.DraftReceiptQty, 0)       AS [草稿收貨數量]

FROM OPOR PO

INNER JOIN POR1 POL
    ON PO.DocEntry = POL.DocEntry

LEFT JOIN ReceiptQty RC
    ON RC.PODocEntry = POL.DocEntry
   AND RC.POLineNum  = POL.LineNum

LEFT JOIN ReturnQty RT
    ON RT.PODocEntry = POL.DocEntry
   AND RT.POLineNum  = POL.LineNum

LEFT JOIN DraftTotal DT
    ON DT.PODocEntry = POL.DocEntry
   AND DT.POLineNum  = POL.LineNum

LEFT JOIN DraftDetail DD
    ON DD.PODocEntry = POL.DocEntry
   AND DD.POLineNum  = POL.LineNum

WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum

ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

執行後，在 `[%0]` 的提示欄位輸入畫面上的採購單號，例如：

```text
100000001
```

請只輸入數字，不要加單引號。

如果想先固定單號測試，可以把最前面的參數部分暫時改成：

```sql
DECLARE @PONum INT;
SET @PONum = 100000001;

;WITH ReceiptQty AS
```

這份查詢若同一個採購項目有兩張收貨草稿，會顯示兩列；「累計收貨（草稿數量）」會是所有草稿合計，而「草稿收貨數量」則是各張草稿自己的數量。

---

<a id="q14"></a>

## S025-Q014｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near ' ORDER BY…

日期：2026-07-27

**你的提問**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near ' ORDER BY     PO.DocNum,     POL.LineNum,     DD.DraftEntry; /* SELECT FROM OPOR T0 */ DECLARE @PONum INT;  /* WHERE */ SET @PONu'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Unclosed quotation mark after the character string '   AND PO.DocNum = @PONum  ORDER BY     PO.DocNum,     POL.LineNum,     DD.DraftEntry;'. 3). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '使用者定義的值' (CSHS) (s) could not be prepared.

**當時回答**

錯誤訊息顯示：**舊 SQL 沒有清除，新 SQL 被接在舊 SQL 後面**。而舊 SQL 中這一行最後多了一個單引號：

```sql
AND @PONum = 100000001'
```

因此 SQL Server 把後面整段內容都視為尚未結束的字串，才會同時出現：

- `Incorrect syntax near ORDER BY`
- `Unclosed quotation mark`
- 錯誤內容中同時出現 `ORDER BY ... /* SELECT FROM OPOR ...`

請先把 SAP B1 查詢編輯區的內容**全部刪除**，不要接在舊查詢後面，再重新貼入一份完整 SQL。

建議先用固定單號測試，最前面寫：

```sql
DECLARE @PONum INT;
SET @PONum = 100000001;

;WITH ReceiptQty AS
(
```

最後寫：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

確認固定單號可以執行後，再改成輸入參數版本：

```sql
/* SELECT FROM [dbo].[OPOR] T0 */
DECLARE @PONum AS INT

/* WHERE */
SET @PONum = /* T0.DocNum */ '[%0]'

;WITH ReceiptQty AS
(
```

最後同樣維持：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = @PONum
ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

請特別確認：

- 編輯區只有一份 SQL，不要重複貼上。
- `100000001` 後面不能有單引號。
- `WITH ReceiptQty` 前面要有分號：`;WITH ReceiptQty`。
- 不要再寫 `SET @PONum = PO.DocNum`。
- 如果是在「使用者定義的值／格式化搜尋」中執行，而不是「查詢管理員」，`[%0]` 的輸入方式可能不適用；應先在查詢管理員中測試。

---

<a id="q15"></a>

## S025-Q015｜還是不行

日期：2026-07-27

**你的提問**

還是不行

**當時回答**

先不要測整段，問題可能不是統計邏輯，而是目前執行的位置不支援 `DECLARE`、CTE 或 `[%0]`。請依序測試下面三段。

### 測試一：固定採購單號

在 SAP B1 編輯區把原內容全部刪除，只執行：

```sql
SELECT
    T0.DocEntry,
    T0.DocNum,
    T0.DocDate,
    T0.CardCode,
    T0.CardName
FROM OPOR T0
WHERE T0.DocNum = 100000001;
```

請把 `100000001` 換成確實存在的採購單號。

如果這段不能執行，請把完整錯誤貼給我；可能輸入的不是 `DocNum`，或目前不是在「查詢管理員」執行。

### 測試二：輸入參數

若測試一成功，再把內容全部換成：

```sql
SELECT
    T0.DocEntry,
    T0.DocNum,
    T0.DocDate,
    T0.CardCode,
    T0.CardName
FROM OPOR T0
WHERE T0.DocNum = '[%0]';
```

正常情況下，SAP B1 應該跳出輸入視窗。

如果又顯示：

```text
Conversion failed when converting '%0' to int
```

代表目前執行位置沒有處理 `[%0]`，通常不是從「工具 → 查詢 → 查詢管理員」直接執行，而是在「使用者定義的值／格式化搜尋」或背景功能執行。

### 測試三：採購單與收貨關聯

如果測試二成功，再測：

```sql
SELECT
    PO.DocNum        AS [採購單號],
    POL.LineNum + 1  AS [採購單列號],
    POL.ItemCode     AS [品料號],
    POL.Quantity     AS [採購數量],
    GRPO.DocNum      AS [收貨採購單號],
    GRL.Quantity     AS [收貨數量]
FROM OPOR PO
INNER JOIN POR1 POL
    ON POL.DocEntry = PO.DocEntry
LEFT JOIN PDN1 GRL
    ON GRL.BaseType  = 22
   AND GRL.BaseEntry = POL.DocEntry
   AND GRL.BaseLine  = POL.LineNum
LEFT JOIN OPDN GRPO
    ON GRPO.DocEntry = GRL.DocEntry
   AND GRPO.CANCELED = 'N'
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = '[%0]'
ORDER BY
    POL.LineNum,
    GRPO.DocNum;
```

目前先不要使用 `DECLARE` 和 `WITH`。請告訴我：

1. 上面哪一個測試開始失敗。
2. 您是從「查詢管理員」執行，還是「使用者定義的值／格式化搜尋」執行。
3. 失敗時的完整錯誤訊息。

有這三項就能準確判斷是參數機制還是 SQL 結構問題。

---

<a id="q16"></a>

## S025-Q016｜三個都可以

日期：2026-07-27

**你的提問**

三個都可以

**當時回答**

既然三個測試都成功，表示：

- `[%0]` 參數可以正常使用。
- 採購單號沒有問題。
- 問題主要出在 SAP B1 查詢工具對 `DECLARE + WITH CTE` 的解析。

建議改用不含 `DECLARE`、不含 `WITH` 的版本。以下可以直接貼入查詢管理員：

```sql
SELECT
    PO.DocNum AS [採購單號],
    POL.LineNum + 1 AS [採購單列號],
    POL.ItemCode AS [品料號(項目)],
    POL.Dscription AS [項目說明],
    POL.Quantity AS [採購數量],

    /* 累計正式入庫數量 */
    ISNULL(
        (
            SELECT SUM(P.Quantity)
            FROM PDN1 P
            INNER JOIN OPDN H
                ON H.DocEntry = P.DocEntry
               AND H.CANCELED = 'N'
            WHERE P.BaseType = 22
              AND P.BaseEntry = POL.DocEntry
              AND P.BaseLine = POL.LineNum
        ),
        0
    ) AS [累計入庫數量],

    /* 累計收貨草稿數量 */
    ISNULL(
        (
            SELECT SUM(D1.Quantity)
            FROM DRF1 D1
            INNER JOIN ODRF D0
                ON D0.DocEntry = D1.DocEntry
            WHERE D0.ObjType = '20'
              AND D1.BaseType = 22
              AND D1.BaseEntry = POL.DocEntry
              AND D1.BaseLine = POL.LineNum
        ),
        0
    ) AS [累計收貨(草稿數量)],

    /* 累計正式退貨數量 */
    ISNULL(
        (
            SELECT SUM(R.Quantity)
            FROM RPD1 R
            INNER JOIN ORPD RH
                ON RH.DocEntry = R.DocEntry
               AND RH.CANCELED = 'N'
            INNER JOIN PDN1 GR
                ON R.BaseType = 20
               AND R.BaseEntry = GR.DocEntry
               AND R.BaseLine = GR.LineNum
            WHERE GR.BaseType = 22
              AND GR.BaseEntry = POL.DocEntry
              AND GR.BaseLine = POL.LineNum
        ),
        0
    ) AS [累計退貨數量],

    DD.DraftEntry AS [草稿單號],
    ISNULL(DD.DraftReceiptQty, 0) AS [草稿收貨數量]

FROM OPOR PO

INNER JOIN POR1 POL
    ON POL.DocEntry = PO.DocEntry

/* 各張收貨採購單草稿 */
LEFT JOIN
(
    SELECT
        D1.BaseEntry AS PODocEntry,
        D1.BaseLine AS POLineNum,
        D0.DocEntry AS DraftEntry,
        SUM(D1.Quantity) AS DraftReceiptQty
    FROM ODRF D0
    INNER JOIN DRF1 D1
        ON D1.DocEntry = D0.DocEntry
    WHERE D0.ObjType = '20'
      AND D1.BaseType = 22
    GROUP BY
        D1.BaseEntry,
        D1.BaseLine,
        D0.DocEntry
) DD
    ON DD.PODocEntry = POL.DocEntry
   AND DD.POLineNum = POL.LineNum

WHERE PO.CANCELED = 'N'
  AND PO.DocNum = '[%0]'

ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry;
```

執行時輸入採購單號即可，例如：

```text
100000001
```

這個版本的結果邏輯是：

- 一個採購項目沒有草稿：顯示一列，草稿數量為 `0`。
- 一個採購項目有一張草稿：顯示一列及該草稿單號。
- 一個採購項目有多張草稿：每張草稿顯示一列。
- `累計收貨(草稿數量)`：所有草稿的合計。
- `草稿收貨數量`：目前這一張草稿的數量。

---

<a id="q17"></a>

## S025-Q017｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keywor…

日期：2026-07-27

**你的提問**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keyword 'FOR'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '使用者定義的值' (CSHS) (s) could not be prepared.

**當時回答**

這個錯誤很可能是因為查詢最後有分號：

```sql
DD.DraftEntry;
```

SAP B1 某些「使用者定義的值」功能會自動在 SQL 後面加上：

```sql
FOR BROWSE
```

最後實際變成：

```sql
ORDER BY DD.DraftEntry;
FOR BROWSE
```

分號已經把前面的 SQL 結束，所以 SQL Server 會回報：

```text
Incorrect syntax near the keyword 'FOR'
```

請將查詢最後改成以下內容，**最後不要加分號**：

```sql
WHERE PO.CANCELED = 'N'
  AND PO.DocNum = '[%0]'

ORDER BY
    PO.DocNum,
    POL.LineNum,
    DD.DraftEntry
```

也就是：

錯誤：

```sql
DD.DraftEntry;
```

正確：

```sql
DD.DraftEntry
```

如果移除最後分號後仍出現 `CSHS 使用者定義的值`，代表您可能是在「格式化搜尋／使用者定義的值」中執行。這份查詢會回傳多欄、多列，應在：

```text
工具 → 查詢 → 查詢管理員
```

中執行，不適合直接設定成某個欄位的格式化搜尋。先移除最後分號再試，這次的 `FOR` 錯誤通常就能排除。

---

