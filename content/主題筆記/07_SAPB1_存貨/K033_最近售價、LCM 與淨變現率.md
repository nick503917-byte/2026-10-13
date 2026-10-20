---
title: "最近售價、LCM 與淨變現率"
category: "07_SAPB1_存貨"
updated: 2026-10-20
sources: ["S178","S179","S184","S194"]
---

# 最近售價、LCM 與淨變現率

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併最近售價來源、銷量顯示零、usp_InventoryLCM、參數格式及淨變現率。ISNULL 為零可能掩蓋 JOIN 找不到資料；查詢日期、幣別、銷售單據範圍與成本口徑須一併核對。淨變現率範例為特定報表算法，非所有會計情境的通用公式。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S178｜確認最近售價取得方式](<../../原始問答/S178_確認最近售價取得方式.md>)：2026-09-16，23 段問答。
- [S179｜整理庫存銷售查詢](<../../原始問答/S179_整理庫存銷售查詢.md>)：2026-09-16，2 段問答。
- [S184｜解釋淨變現率算法](<../../原始問答/S184_解釋淨變現率算法.md>)：2026-09-17，1 段問答。
- [S194｜釐清未完成的請求](<../../原始問答/S194_釐清未完成的請求.md>)：2026-09-22，6 段問答。

## 問題索引

- [01｜標準單價就抓 項目主檔的成本，我想問 那我抓的到最近售價嗎](#qa1)
- [02｜你看我的截圖 有一筆是最近售價 以sapb1的查詢報表來說 我可以怎麼寫](#qa2)
- [03｜我的成本會抓 OITM.AvgPrice](#qa3)
- [04｜cl3](#qa4)
- [05｜好 幫我寫一段](#qa5)
- [06｜我是用FMS 所以沒辦法用Declare](#qa6)
- [07｜這是我select的格式。 如果有不確定的table 再問我](#qa7)
- [08｜對](#qa8)
- [09｜是否用 OITW.WhsCode、OWHS.WhsName？ 對 系列別用：OITM.U\_SeriesType OITW.OnHand 對 OITM.ItemName、OITM.…](#qa9)
- [10｜SQL／程式碼調整與說明](#qa10)
- [11｜但我總銷售數量都是0 是正常的嗎](#qa11)
- [12｜淨變現率算法: Total Expense-Management Exp= Operating Exp 淨變現率(Sales- Operating Exp)/ Sales 甚麼意思](#qa12)
- [13｜好 那我要繼續增加欄位了 最近出貨金額 = 期末庫存量\最近售價 淨變現價值 = 最近出貨金額\ 我想要自己填入數字 成本市價孰低 = 標準成本 - 淨變現價值 181天已提呆滯損…](#qa13)
- [14｜你可以查一下嗎 名稱是 排除庫存調撥帳齡異動](#qa14)
- [15｜對 但我現在要分倉](#qa15)
- [16｜一般](#qa16)
- [17｜所以哪一個檔案](#qa17)
- [18｜給我一個 我可以放進去查詢報表的](#qa18)
- [19｜如果我把這支放到SSMS裡面 等於是一支程序後，再從FMS呼叫這支可以嗎](#qa19)
- [20｜那給我SP](#qa20)
- [21｜@Factor = 0.80, @ItemCode = N'', @WhsCode = N'AW'; @Factor = 0.80可以讓我選嗎 就是給我個欄位選擇](#qa21)
- [22｜或是讓我填值進去 而不是在上面改](#qa22)
- [23｜'後端排程工作' (OBSJ)](#qa23)
- [24｜我可以修改畫面中的字嗎'](#qa24)
- [25｜然後我想再多一個 我可以篩選哪個日期要查](#qa25)
- [26｜SQL／程式碼調整與說明](#qa26)
- [27｜我記得我有一個 LCM相關的報表](#qa27)
- [28｜第二個](#qa28)
- [29｜他現在跑出\\](#qa29)
- [30｜1\). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keywo…](#qa30)
- [31｜/\ SELECT FROM [dbo].[OITM] T0 / / SELECT FROM [dbo].[OINV] T1 \/ EXEC dbo.usp\_InventoryL…](#qa31)
- [32｜/\ SELECT FROM [dbo].[OITM] T0 /\ / SELECT FROM [dbo].[OINV] T1 \/\ EXEC dbo.usp\_Inventor…](#qa32)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 標準單價就抓 項目主檔的成本，我想問 那我抓的到最近售價嗎

來源：[S178-Q001](<../../原始問答/S178_確認最近售價取得方式.md#q1>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-8327458b-c3c9-4485-8a5f-f86fcf902c8b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8327458b-c3c9-4485-8a5f-f86fcf902c8b.png



## My request:
標準單價就抓 項目主檔的成本，我想問 那我抓的到最近售價嗎

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**可以，但要有銷售交易明細，不能只靠項目主檔的成本。** 目前只有畫面，還無法確認系統是否提供這個資料來源。

建議這樣抓：

- **標準單價**：取「項目主檔」的成本。
- **最近售價**：依品料號，找報表截止日以前最後一筆有效銷售交易，取該筆單價，排除取消單。
- **沒有銷售紀錄**：留空或顯示「無銷售紀錄」，避免用成本代替售價。

你圖中的「最近售價」看起來都等於標準單價取到小數兩位，**可能目前帶的是成本，還不是真正的最近成交價**。

另外要統一幣別、含稅／未稅與計價單位，才方便比較。你這邊用的是哪套 ERP？「最近售價」想取**最近一筆出貨價**，還是**最近一筆訂單價**？

<a id="qa2"></a>

### 2. 你看我的截圖 有一筆是最近售價 以sapb1的查詢報表來說 我可以怎麼寫

來源：[S178-Q002](<../../原始問答/S178_確認最近售價取得方式.md#q2>)；2026-09-16

**問題**

你看我的截圖 有一筆是最近售價 以sapb1的查詢報表來說 我可以怎麼寫

**歷史回答**

有，你截圖 **L 欄是「最近售價」**。在 SAP B1 查詢報表，可以依「品料號」去抓**最近一筆未取消的 A/R 發票單價**，再接到原本報表。

先更正一下：光看截圖，不能判定你現在這欄是抓成本還是售價。

下面先以 **SQL Server 版 SAP B1** 示範，定義為「所有客戶、所有倉庫中，最近一筆發票的未稅、列折扣後單價」。

```sql
SELECT
    T0.ItemCode AS [品料號],
    T0.ItemName AS [品名規格],
    T0.AvgPrice AS [標準單價],
    S.Price     AS [最近售價],
    S.Currency  AS [售價幣別],
    S.DocDate   AS [最近銷售日期],
    S.DocNum    AS [發票號碼],
    S.CardName  AS [客戶名稱]
FROM OITM T0

OUTER APPLY (
    SELECT TOP 1
        D.Price,
        D.Currency,
        H.DocDate,
        H.DocNum,
        H.CardName
    FROM OINV H
    INNER JOIN INV1 D
        ON D.DocEntry = H.DocEntry
    WHERE D.ItemCode = T0.ItemCode
      AND H.CANCELED = 'N'
      AND D.Quantity > 0
    ORDER BY
        H.DocDate DESC,
        H.DocEntry DESC,
        D.LineNum DESC
) S

ORDER BY T0.ItemCode;
```

這段的邏輯是：

- `OITM.AvgPrice`：依你指定，帶項目主檔成本作為「標準單價」；若公司按倉庫管理成本，要確認是否應取 `OITW.AvgPrice`。
- `OINV`、`INV1`：A/R 發票表頭及明細，以品料號找最近一筆。
- `INV1.Price`：列折扣後單價，尚未分攤整張單據的表頭折扣；若要折扣前單價，改取 `PriceBefDi`。[SAP 官方欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/INV1.html)
- 沒有銷售紀錄時，「最近售價」會留空；同一天多筆時，以內部單號、列號由大到小取一筆。

**你這張是「期末」報表，還要限制銷售日期不能超過報表截止日。** 例如結算到 2026/08/31，在子查詢的 `WHERE` 加上：

```sql
AND H.DocDate <= '20260831'
```

售價先保留原幣別及銷售計價單位；若要直接和成本比較，必須再換算為相同幣別、相同庫存單位。

**把你目前報表的 SQL 貼上來，並告訴我是 SQL Server 還是 HANA，我就能直接把「最近售價」接進你原本那一欄。**

<a id="qa3"></a>

### 3. 我的成本會抓 OITM.AvgPrice

來源：[S178-Q003](<../../原始問答/S178_確認最近售價取得方式.md#q3>)；2026-09-16

**問題**

我的成本會抓 OITM.AvgPrice

**歷史回答**

了解，**「標準單價」就固定取 `OITM.AvgPrice`**，前面提供的 SQL 已經是這樣寫：

```sql
T0.AvgPrice AS [標準單價],
S.Price    AS [最近售價]
```

兩欄分別取不同來源：

| 報表欄位 | 資料來源 |
|---|---|
| 標準單價 | `OITM.AvgPrice`，項目主檔成本 |
| 最近售價 | `INV1.Price`，最近一筆未取消 A/R 發票的列折扣後未稅單價 |

如果是期末報表，最近售價就取**截止日以前最後一筆**；沒有銷售紀錄則留空。

你把**目前報表的 SQL** 貼上來，再告訴我是 **SQL Server 或 HANA**，我就可以直接幫你整合。

<a id="qa4"></a>

### 4. cl3

來源：[S178-Q004](<../../原始問答/S178_確認最近售價取得方式.md#q4>)；2026-09-16

**問題**

cl3

**歷史回答**

「cl3」是指什麼？如果是打錯字，可以直接重傳；如果要我整合查詢，貼上目前的 SQL 就可以。

<a id="qa5"></a>

### 5. 好 幫我寫一段

來源：[S178-Q005](<../../原始問答/S178_確認最近售價取得方式.md#q5>)；2026-09-16

**問題**

好 幫我寫一段

**歷史回答**

可以，以下先用 **SAP B1 的 SQL Server 版本**，成本固定抓 `OITM.AvgPrice`；最近售價抓截止日以前，最後一筆未取消的 A/R 發票單價。

執行時會要求輸入「截止日期」。

```sql
/* SELECT FROM [dbo].[OINV] P */
DECLARE @EndDate DATETIME;

/* WHERE */
SET @EndDate = /* P.DocDate */ '[%0]';

SELECT
    T0.ItemCode  AS [品料號],
    T0.ItemName  AS [品名規格],
    T0.AvgPrice  AS [標準單價],
    S.Price      AS [最近售價],
    S.Currency   AS [售價幣別],
    S.UnitMsr    AS [銷售單位],
    S.DocDate    AS [最近銷售日期],
    S.DocNum     AS [發票號碼],
    S.CardCode   AS [客戶代碼],
    S.CardName   AS [客戶名稱]
FROM OITM T0
OUTER APPLY (
    SELECT TOP 1
        D.Price,
        D.Currency,
        D.UnitMsr,
        H.DocDate,
        H.DocNum,
        H.CardCode,
        H.CardName
    FROM OINV H
    INNER JOIN INV1 D
        ON H.DocEntry = D.DocEntry
    WHERE D.ItemCode = T0.ItemCode
      AND H.CANCELED = 'N'
      AND H.DocDate <= @EndDate
      AND D.Quantity > 0
    ORDER BY
        H.DocDate DESC,
        H.DocEntry DESC,
        D.LineNum DESC
) S
WHERE T0.InvntItem = 'Y'
ORDER BY T0.ItemCode;
```

- **標準單價**：項目主檔的**目前成本**，不是截止日的歷史成本。
- **最近售價**：跨客戶、跨倉庫，取最近發票的列折扣後未稅單價，未分攤表頭折扣；無紀錄則留空。
- **幣別／單位**：保留原交易資料，尚未換算，和成本比較前需一致。

這段可先獨立查詢各品項的成本與最近售價，尚未加入截圖中的期末庫存量。**如果你的資料庫是 HANA，這段需要改成 HANA 語法。**

<a id="qa6"></a>

### 6. 我是用FMS 所以沒辦法用Declare

來源：[S178-Q006](<../../原始問答/S178_確認最近售價取得方式.md#q6>)；2026-09-16

**問題**

我是用FMS 所以沒辦法用Declare

**歷史回答**

了解，改成 **不含 `DECLARE` 的單一 `SELECT`**。

如果你是要查整份品項清單，這段會帶出成本及最近售價（**SQL Server 語法**）：

```sql
SELECT
    T0.ItemCode AS [品料號],
    T0.ItemName AS [品名規格],
    T0.AvgPrice AS [標準單價],
    (
        SELECT TOP 1
            D.Price
        FROM OINV H
        INNER JOIN INV1 D
            ON H.DocEntry = D.DocEntry
        WHERE D.ItemCode = T0.ItemCode
          AND H.CANCELED = 'N'
          AND D.Quantity > 0
          AND H.DocDate <= '20260831'
        ORDER BY
            H.DocDate DESC,
            H.DocEntry DESC,
            D.LineNum DESC
    ) AS [最近售價]
FROM OITM T0
WHERE T0.InvntItem = 'Y'
ORDER BY T0.ItemCode
```

把 `'20260831'` 改成你的**報表截止日期**。成本是 `OITM.AvgPrice` 的目前值；售價是截止日前最近一筆發票的列折扣後未稅單價，保留原幣別及計價單位，沒有紀錄則為 `NULL`。

**如果你說的 FMS 是 SAP B1「格式化搜尋」，要回填畫面上某一欄**，則應只回傳一個售價。以下以綁在「項目主檔」畫面為例：

```sql
SELECT TOP 1
    D.Price
FROM OINV H
INNER JOIN INV1 D
    ON H.DocEntry = D.DocEntry
WHERE D.ItemCode = $[OITM.ItemCode]
  AND H.CANCELED = 'N'
  AND D.Quantity > 0
  AND H.DocDate <= CAST(GETDATE() AS DATE)
ORDER BY
    H.DocDate DESC,
    H.DocEntry DESC,
    D.LineNum DESC
```

`$[OITM.ItemCode]` 代表目前項目主檔畫面的品料號；如果綁在其他畫面，要改成該畫面的欄位參照。[SAP FMS 語法說明](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/syntax-of-formatted-search-in-sap-business-one-version-for-sap-hana/ba-p/13270247)

<a id="qa7"></a>

### 7. 這是我select的格式。 如果有不確定的table 再問我

來源：[S178-Q007](<../../原始問答/S178_確認最近售價取得方式.md#q7>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-6aa0c6f1-f227-4d1f-b0c9-71e172b0750c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6aa0c6f1-f227-4d1f-b0c9-71e172b0750c.png



## My request:

這是我select的格式。

如果有不確定的table 再問我

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

了解，你要的 `SELECT` 欄位順序是：

```sql
SELECT
    ... AS [倉],
    ... AS [倉庫名稱],
    ... AS [品料號],
    ... AS [大類],
    ... AS [系列],
    ... AS [系列名稱],
    ... AS [品名規格],
    ... AS [期末庫存量],
    ... AS [單位],
    T0.AvgPrice AS [標準單價],
    ... AS [標準成本],
    ... AS [最近售價]
```

成本確定用 `OITM.AvgPrice`。要寫成完整可執行的 SQL，還需要你確認：

| 欄位 | 需要確認的來源／規則 |
|---|---|
| 倉、倉庫名稱 | 是否用 `OITW.WhsCode`、`OWHS.WhsName`？ |
| 大類、系列、系列名稱 | 各自是哪個 table、欄位？看起來可能是自訂欄位，不能直接猜。 |
| 期末庫存量 | 是目前庫存 `OITW.OnHand`，還是指定截止日的歷史庫存？歷史庫存目前從哪裡抓？ |
| 品名規格、單位 | 是否用 `OITM.ItemName`、`OITM.InvntryUom`？ |
| 標準成本 | 是否為「期末庫存量 × `OITM.AvgPrice`」，並四捨五入至整數？ |
| 最近售價 | 是否取 `OINV`＋`INV1` 最近未取消發票的 `Price`？依品料號跨倉抓，還是限定同一倉？ |

**最方便是貼上你現有的 `SELECT … FROM … JOIN …`**，我就能沿用你原本的 table 和欄位，把「最近售價」接進去。

<a id="qa8"></a>

### 8. 對

來源：[S178-Q008](<../../原始問答/S178_確認最近售價取得方式.md#q8>)；2026-09-16

**問題**

對

**歷史回答**

好，成本就用 `OITM.AvgPrice`，標準成本用「期末庫存量 × 成本」。

還缺截圖無法判斷的來源，請貼上**目前的 SQL**；如果還沒有 SQL，請提供：

- **大類、系列、系列名稱**：各自的 table 與欄位名稱。
- **期末庫存量**：用目前庫存 `OITW.OnHand`，還是指定日期的歷史庫存？
- **最近售價**：同品料號跨倉抓，還是限定同一倉？

有這些就能照你的欄位順序寫成完整查詢，不用 `DECLARE`。

<a id="qa9"></a>

### 9. 是否用 OITW.WhsCode、OWHS.WhsName？ 對 系列別用：OITM.U\_SeriesType OITW.OnHand 對 OITM.ItemName、OITM.…

來源：[S178-Q009](<../../原始問答/S178_確認最近售價取得方式.md#q9>)；2026-09-16

**問題**

是否用 `OITW.WhsCode`、`OWHS.WhsName`？   對
系列別用：OITM.U\_SeriesType
`OITW.OnHand`   對
`OITM.ItemName`、`OITM.InvntryUom`  對
是否取 `OINV`＋`INV1` 最近未取消發票的 `Price`？依品料號限定同一倉？

**歷史回答**

可以，**最近售價就依「同品料號＋同一倉庫」**，抓最近一筆未取消 A/R 發票的 `INV1.Price`。

下面不使用 `DECLARE`。你還沒提供「大類、系列名稱」的來源，我先留空，保留截圖的欄位順序：

```sql
SELECT
    W.WhsCode                       AS [倉],
    H.WhsName                       AS [倉庫名稱],
    I.ItemCode                      AS [品料號],
    ''                              AS [大類],
    I.U_SeriesType                   AS [系列],
    ''                              AS [系列名稱],
    I.ItemName                      AS [品名規格],
    W.OnHand                        AS [期末庫存量],
    I.InvntryUom                     AS [單位],
    I.AvgPrice                      AS [標準單價],
    ROUND(W.OnHand * I.AvgPrice, 0)  AS [標準成本],
    (
        SELECT TOP 1
            D.Price
        FROM OINV A
        INNER JOIN INV1 D
            ON A.DocEntry = D.DocEntry
        WHERE D.ItemCode = I.ItemCode
          AND D.WhsCode = W.WhsCode
          AND A.CANCELED = 'N'
          AND D.Quantity > 0
          AND A.DocDate <= CAST(GETDATE() AS DATE)
        ORDER BY
            A.DocDate DESC,
            A.DocEntry DESC,
            D.LineNum DESC
    )                               AS [最近售價]
FROM OITM I
INNER JOIN OITW W
    ON I.ItemCode = W.ItemCode
INNER JOIN OWHS H
    ON W.WhsCode = H.WhsCode
WHERE I.InvntItem = 'Y'
  AND W.OnHand <> 0
ORDER BY
    W.WhsCode,
    I.U_SeriesType,
    I.ItemCode
```

這段是 **SQL Server 語法**，只列出庫存不為零的品項。欄名雖沿用「期末庫存量」，實際取的是 `OITW.OnHand` **目前庫存**；最近售價沒有紀錄時為空值，且未做幣別、單位換算。

**剩下「大類」和「系列名稱」，分別是哪個 table／欄位？**

<a id="qa10"></a>

### 10. SQL／程式碼調整與說明

來源：[S179-Q001](<../../原始問答/S179_整理庫存銷售查詢.md#q1>)；2026-09-16

**問題**

SELECT 
    T0.[ItemCode] AS '項目代號', 
    T0.[ItemName] AS '項目名稱', 
    ISNULL(T1.[SalesQty], 0) AS '期間總銷售數量', 
    T0.[OnHand] AS '目前庫存', 
    T0.[OnOrder] AS '尚未結案採購單數量'
FROM OITM T0
LEFT JOIN (
    SELECT A.[ItemCode], SUM(A.[Quantity]) AS [SalesQty]
    FROM INV1 A
    INNER JOIN OINV B ON A.[DocEntry] = B.[DocEntry]
    WHERE B.[DocDate] >= [%0] AND B.[DocDate] <= [%1]
    GROUP BY A.[ItemCode]
) T1 ON T0.[ItemCode] = T1.[ItemCode]
WHERE T0.[InvntItem] = 'Y'

幫我整理一下

**歷史回答**

以下已移除 HTML 空白編碼，並統一縮排，保留原本查詢邏輯：

```sql
SELECT
    T0.[ItemCode]              AS [項目代號],
    T0.[ItemName]              AS [項目名稱],
    ISNULL(T1.[SalesQty], 0)   AS [期間總銷售數量],
    T0.[OnHand]                AS [目前庫存],
    T0.[OnOrder]               AS [尚未結案採購單數量]
FROM OITM T0
LEFT JOIN (
    SELECT
        A.[ItemCode],
        SUM(A.[Quantity]) AS [SalesQty]
    FROM INV1 A
    INNER JOIN OINV B
        ON A.[DocEntry] = B.[DocEntry]
    WHERE B.[DocDate] >= [%0]
      AND B.[DocDate] <= [%1]
    GROUP BY
        A.[ItemCode]
) T1
    ON T0.[ItemCode] = T1.[ItemCode]
WHERE T0.[InvntItem] = 'Y';
```

<a id="qa11"></a>

### 11. 但我總銷售數量都是0 是正常的嗎

來源：[S179-Q002](<../../原始問答/S179_整理庫存銷售查詢.md#q2>)；2026-09-16

**問題**

但我總銷售數量都是0 是正常的嗎

**歷史回答**

**如果這段期間確實有銷售，全部為 0 就需要檢查，不一定是真的沒有銷量。**

你原本的 SQL 有這一段：

```sql
ISNULL(T1.[SalesQty], 0)
```

當銷售子查詢找不到對應品項時，也會顯示 `0`，所以「沒有符合的資料」和「數量確實為零」看起來一樣。

常見原因有：

- **日期區間沒有符合的發票**，或起訖日期輸入相反。
- **日期參數的解析有問題**：原查詢的 `[%0]`、`[%1]` 沒有加單引號，建議先改成 `'[%0]'`、`'[%1]'` 測試。
- **實際做的是交貨單，尚未開立應收發票**：目前查詢是以應收發票 `OINV / INV1` 統計；交貨單屬於另一組資料。[SAP 文件](https://help.sap.com/saphelpiis_hc/B1_Image_Repository/Consultant_Training/Basic/B1_90_TB1200_03_01_Ex.pdf)

你可以先跑下面這段，確認所選期間有沒有發票明細：

```sql
SELECT TOP 100
    B.[DocNum]   AS [發票號碼],
    B.[DocDate]  AS [過帳日期],
    B.[CANCELED] AS [取消狀態],
    A.[ItemCode] AS [項目代號],
    A.[Dscription] AS [項目名稱],
    A.[Quantity] AS [銷售數量]
FROM OINV B
INNER JOIN INV1 A
    ON A.[DocEntry] = B.[DocEntry]
WHERE B.[DocDate] >= '[%0]'
  AND B.[DocDate] <= '[%1]'
ORDER BY
    B.[DocDate] DESC,
    B.[DocNum] DESC;
```

**如果沒有資料**，先檢查日期範圍，以及該期間是否有開應收發票；**如果有資料且數量不為 0**，再檢查那些品項是否屬於原查詢限定的庫存品項（`InvntItem = 'Y'`）。

你要的「總銷售數量」是以 **交貨單出貨量**，還是以 **應收發票數量** 為準？這會決定應該查哪一組資料表。

<a id="qa12"></a>

### 12. 淨變現率算法: Total Expense-Management Exp= Operating Exp 淨變現率(Sales- Operating Exp)/ Sales 甚麼意思

來源：[S184-Q001](<../../原始問答/S184_解釋淨變現率算法.md#q1>)；2026-09-17

**問題**

淨變現率算法:
Total Expense-Management Exp= Operating Exp
淨變現率(Sales- Operating Exp)/ Sales

甚麼意思

**歷史回答**

這個算法是在看：**銷售收入扣掉「不含管理費用的支出」後，還剩下多少比例。**

公式分成兩步：

1. **營運費用（Operating Exp）＝總費用（Total Expense）－管理費用（Management Exp）**
2. **淨變現率＝（銷售收入－營運費用）÷銷售收入 × 100%**

例如：

| 項目 | 金額 |
|---|---:|
| 銷售收入 Sales | 100 萬 |
| 總費用 Total Expense | 80 萬 |
| 管理費用 Management Exp | 10 萬 |
| 營運費用＝80－10 | 70 萬 |

淨變現率＝（100－70）÷100＝**30%**

意思是：**每做 100 元的銷售，扣掉上述營運費用後，剩下 30 元；但這 30 元還沒有扣除管理費用。**

要注意，你提供的公式把管理費用排除，所以**不能直接當成公司的淨利率，也不一定代表實際收到的現金比例**。「淨變現率」在這份報表中的確切意義，還要看 Total Expense 包含哪些費用，以及為什麼排除管理費用。

<a id="qa13"></a>

### 13. 好 那我要繼續增加欄位了 最近出貨金額 = 期末庫存量\最近售價 淨變現價值 = 最近出貨金額\ 我想要自己填入數字 成本市價孰低 = 標準成本 - 淨變現價值 181天已提呆滯損…

來源：[S178-Q010](<../../原始問答/S178_確認最近售價取得方式.md#q10>)；2026-09-17

**問題**

好 那我要繼續增加欄位了 

最近出貨金額 = 期末庫存量\*最近售價
淨變現價值 = 最近出貨金額\* 我想要自己填入數字
成本市價孰低 = 標準成本 - 淨變現價值
181天已提呆滯損失 這個是依倉庫分 我在 排除庫存調撥異動有類似的 可以沿用
應提跌價損失 = 當 181天已提呆滯損失 等於 0，而且  成本市價孰低 小於 0 時，顯示 成本市價孰低；其餘情況都顯示 0。

**歷史回答**

可以，新增欄位按你提供的公式計算。**「181天已提呆滯損失」需要你貼上「排除庫存調撥異動」那段 SQL**，我才能沿用倉庫判斷，目前先留 `NULL`；「應提跌價損失」也先留空，避免誤算成 0。

以下不使用 `DECLARE`，**自己填的數字集中在最後面的 `1.00`**：例如填 `0.80` 就是最近出貨金額的 80%。

```sql
SELECT
    C.*,
    CASE
        WHEN C.[181天已提呆滯損失] IS NULL THEN NULL
        WHEN C.[181天已提呆滯損失] = 0
         AND C.[成本市價孰低] < 0
        THEN C.[成本市價孰低]
        ELSE 0
    END AS [應提跌價損失]
FROM (
    SELECT
        B.*,
        B.[標準成本] - B.[淨變現價值] AS [成本市價孰低]
    FROM (
        SELECT
            A.*,
            A.[最近出貨金額] * A.[淨變現係數] AS [淨變現價值]
        FROM (
            SELECT
                W.WhsCode      AS [倉],
                H.WhsName      AS [倉庫名稱],
                I.ItemCode     AS [品料號],
                ''             AS [大類],
                I.U_SeriesType AS [系列],
                ''             AS [系列名稱],
                I.ItemName     AS [品名規格],
                W.OnHand       AS [期末庫存量],
                I.InvntryUom   AS [單位],
                I.AvgPrice     AS [標準單價],

                ROUND(W.OnHand * I.AvgPrice, 0)
                    AS [標準成本],

                S.Price AS [最近售價],

                W.OnHand * S.Price
                    AS [最近出貨金額],

                P.Factor AS [淨變現係數],

                -- 待接入「排除庫存調撥異動」的計算
                CAST(NULL AS DECIMAL(19, 6))
                    AS [181天已提呆滯損失]

            FROM OITM I
            INNER JOIN OITW W
                ON I.ItemCode = W.ItemCode
            INNER JOIN OWHS H
                ON W.WhsCode = H.WhsCode

            OUTER APPLY (
                SELECT TOP 1
                    D.Price
                FROM OINV V
                INNER JOIN INV1 D
                    ON V.DocEntry = D.DocEntry
                WHERE D.ItemCode = I.ItemCode
                  AND D.WhsCode = W.WhsCode
                  AND V.CANCELED = 'N'
                  AND D.Quantity > 0
                  AND V.DocDate <= CAST(GETDATE() AS DATE)
                ORDER BY
                    V.DocDate DESC,
                    V.DocEntry DESC,
                    D.LineNum DESC
            ) S

            -- 在這裡填數字：1.00 = 100%，0.80 = 80%
            CROSS JOIN (
                SELECT CAST(1.00 AS DECIMAL(19, 6)) AS Factor
            ) P

            WHERE I.InvntItem = 'Y'
              AND W.OnHand <> 0
        ) A
    ) B
) C
ORDER BY
    C.[倉],
    C.[系列],
    C.[品料號]
```

這版先讓所有品項共用一個係數，沿用目前庫存及同倉最近發票售價，尚未換算售價幣別／單位。

另確認你的正負方向：依你指定的公式，成本 100、淨變現價值 120，差額為 **−20**，已提呆滯損失為 0 時就顯示 **−20**；成本 100、淨變現價值 80，差額為 **20**，反而顯示 **0**。目前程式完全照這個規則寫。

請貼上**「排除庫存調撥異動」中計算 181 天已提呆滯損失的 SQL，包含相關 `FROM`／`JOIN`**，我再把留空處接上。

<a id="qa14"></a>

### 14. 你可以查一下嗎 名稱是 排除庫存調撥帳齡異動

來源：[S178-Q011](<../../原始問答/S178_確認最近售價取得方式.md#q11>)；2026-09-17

**問題**

你可以查一下嗎 名稱是 排除庫存調撥帳齡異動

**歷史回答**

找到了，**「排除庫存調撥帳齡異動」**裡的最新 SQL 可以沿用帳齡計算，但有一點要先釐清：**那份是依料號計算全公司 FIFO 帳齡，沒有分倉。**

原本的邏輯是：

- 入庫：收貨採購單，以及指定的生產／庫存收貨單。
- 出庫：交貨單，以及指定的生產／庫存發貨單。
- 不納入庫存調撥，按 FIFO 計算剩餘數量。
- 181 天以上各區間的提列比率都是 **100%**。

如果「181天已提呆滯損失」指 **181 天以上全部區間合計**，可沿用：

```sql
I.AvgPrice * (
    ISNULL(A.Age181365, 0)
  + ISNULL(A.Age1To2Years, 0)
  + ISNULL(A.Age2To3Years, 0)
  + ISNULL(A.AgeOver3Years, 0)
) AS [181天已提呆滯損失]
```

但這是**依帳齡算出的提列金額**，不是從會計帳上讀取已入帳金額。

要接到你現在「每個倉庫一列」的報表，不能直接把全公司金額重複放在每個倉庫，也不能只加上倉庫分組：例如貨品從 A 倉調到 B 倉，完全排除調撥後，B 倉就沒有那批貨的入庫來源。

**你要採哪種分倉方式？**

1. **按各倉目前庫存占比分攤**全公司 181 天以上提列金額，屬於估算。
2. **追蹤調撥，保留原入庫日期**，算出目前各倉真正承接的帳齡；調撥不會讓帳齡重新起算。

如果你要的是「分倉，而且調撥不影響帳齡」，應採第 2 種。

<a id="qa15"></a>

### 15. 對 但我現在要分倉

來源：[S178-Q012](<../../原始問答/S178_確認最近售價取得方式.md#q12>)；2026-09-17

**問題**

對 但我現在要分倉

**歷史回答**

了解，這版改成 **「品料號＋倉庫」各一列**，181 天以上呆滯損失也要各倉分開計算。

同時保留你的要求：**調撥不重算帳齡，但調撥數量仍要移到目的倉。** 例如：

| 異動 | A 倉 | B 倉 |
|---|---:|---:|
| 1/1 入庫 100 件到 A 倉 | 100 | 0 |
| 7/1 調撥 40 件到 B 倉 | 60 | 40 |

兩倉剩餘數量都沿用 **1/1** 的入庫日期，B 倉不從 7/1 重新計算。

因此需要在原本 SQL 加入 **`OWTR`／`WTR1` 調撥資料**，追蹤原入庫數量移到哪個倉庫，不能直接排除調撥後只加上倉庫分組。

**先確認一個會影響寫法的資料設定：你這份報表的品項是「一般品項」，還是有用「批次／序號管理」？**  
一般品項可以按來源倉 FIFO 分配調出的帳齡；批次／序號品項則需要依實際調出的批次／序號追蹤。

<a id="qa16"></a>

### 16. 一般

來源：[S178-Q013](<../../原始問答/S178_確認最近售價取得方式.md#q13>)；2026-09-17

**問題**

一般

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa17"></a>

### 17. 所以哪一個檔案

來源：[S178-Q014](<../../原始問答/S178_確認最近售價取得方式.md#q14>)；2026-09-17

**問題**

所以哪一個檔案

**歷史回答**

用這個：**[分倉庫齡與最近售價_草稿.sql](C:/Users/nick_yeh/Documents/維熹FMS/分倉庫齡與最近售價_草稿.sql)**

整段複製到查詢中，沒有 `DECLARE`。

- `Factor = 1.00`：改成你要的淨變現係數。
- `ItemFilter = N'BATT12V-23A'`：先查這個料號，核對後改成 `N''` 可查全部。

**這是試跑版，需要 SQL Server 2017 以上，尚未在你的 SAP 環境實測。**

<a id="qa18"></a>

### 18. 給我一個 我可以放進去查詢報表的

來源：[S178-Q015](<../../原始問答/S178_確認最近售價取得方式.md#q15>)；2026-09-17

**問題**

給我一個 我可以放進去查詢報表的

**歷史回答**

下面整段可複製到 **SAP B1 的 SQL Server 查詢報表**，不用 `DECLARE`。

先說明：**需要 SQL Server 2017 以上、資料庫相容性層級 130 以上，目前尚未在你的 SAP 環境實測。**

最上面兩個地方可以改：

- `1.00`：淨變現係數，例如 `0.80` 代表 80%。
- `N'BATT12V-23A'`：先查單一料號；確認結果和效能後，改成 `N''` 可查全部。

```sql
;WITH Params AS
(
    SELECT
        CAST(GETDATE() AS date) AS AsOfDate,
        CAST(1.00 AS decimal(19,6)) AS Factor,
        CAST(N'BATT12V-23A' AS nvarchar(50)) AS ItemFilter
),
RawEvents AS
(
    -- 收貨採購單：入庫
    SELECT
        L.ItemCode,
        H.DocDate,
        H.CreateDate,
        H.DocTime,
        20 AS DocType,
        H.DocEntry,
        L.LineNum,
        'I' AS Kind,
        CAST(N'' AS nvarchar(8)) AS FromWhs,
        L.WhsCode AS ToWhs,
        CAST(L.InvQty AS decimal(19,6)) AS Qty
    FROM OPDN H
    INNER JOIN PDN1 L
        ON L.DocEntry = H.DocEntry
    CROSS JOIN Params P
    WHERE H.CANCELED = 'N'
      AND L.InvQty > 0
      AND H.DocDate < DATEADD(day, 1, P.AsOfDate)
      AND (
          P.ItemFilter = N''
          OR L.ItemCode = P.ItemFilter
      )

    UNION ALL

    -- 生產相關及指定收貨單：入庫
    SELECT
        L.ItemCode,
        H.DocDate,
        H.CreateDate,
        H.DocTime,
        59,
        H.DocEntry,
        L.LineNum,
        'I',
        CAST(N'' AS nvarchar(8)),
        L.WhsCode,
        CAST(L.InvQty AS decimal(19,6))
    FROM OIGN H
    INNER JOIN IGN1 L
        ON L.DocEntry = H.DocEntry
    CROSS JOIN Params P
    WHERE H.CANCELED = 'N'
      AND L.InvQty > 0
      AND H.DocDate < DATEADD(day, 1, P.AsOfDate)
      AND (
          P.ItemFilter = N''
          OR L.ItemCode = P.ItemFilter
      )
      AND (
          L.BaseType = 202
          OR H.Series = 19
          OR H.DocNum = 800000000
      )

    UNION ALL

    -- 生產相關及指定發貨單：出庫
    SELECT
        L.ItemCode,
        H.DocDate,
        H.CreateDate,
        H.DocTime,
        60,
        H.DocEntry,
        L.LineNum,
        'O',
        L.WhsCode,
        CAST(N'' AS nvarchar(8)),
        CAST(L.InvQty AS decimal(19,6))
    FROM OIGE H
    INNER JOIN IGE1 L
        ON L.DocEntry = H.DocEntry
    CROSS JOIN Params P
    WHERE H.CANCELED = 'N'
      AND L.InvQty > 0
      AND H.DocDate < DATEADD(day, 1, P.AsOfDate)
      AND (
          P.ItemFilter = N''
          OR L.ItemCode = P.ItemFilter
      )
      AND (
          L.BaseType = 202
          OR H.Series = 20
          OR H.DocNum IN (650000000, 650000001)
      )

    UNION ALL

    -- 交貨單：出庫
    SELECT
        L.ItemCode,
        H.DocDate,
        H.CreateDate,
        H.DocTime,
        15,
        H.DocEntry,
        L.LineNum,
        'O',
        L.WhsCode,
        CAST(N'' AS nvarchar(8)),
        CAST(L.InvQty AS decimal(19,6))
    FROM ODLN H
    INNER JOIN DLN1 L
        ON L.DocEntry = H.DocEntry
    CROSS JOIN Params P
    WHERE H.CANCELED = 'N'
      AND L.InvQty > 0
      AND H.DocDate < DATEADD(day, 1, P.AsOfDate)
      AND (
          P.ItemFilter = N''
          OR L.ItemCode = P.ItemFilter
      )

    UNION ALL

    -- 庫存調撥：移轉數量，但保留原入庫日期
    SELECT
        L.ItemCode,
        H.DocDate,
        H.CreateDate,
        H.DocTime,
        67,
        H.DocEntry,
        L.LineNum,
        'T',
        L.FromWhsCod,
        L.WhsCode,
        CAST(L.InvQty AS decimal(19,6))
    FROM OWTR H
    INNER JOIN WTR1 L
        ON L.DocEntry = H.DocEntry
    CROSS JOIN Params P
    WHERE H.CANCELED = 'N'
      AND L.InvQty > 0
      AND H.DocDate < DATEADD(day, 1, P.AsOfDate)
      AND (
          P.ItemFilter = N''
          OR L.ItemCode = P.ItemFilter
      )
      AND L.FromWhsCod <> L.WhsCode
),
Events AS
(
    SELECT
        *,
        ROW_NUMBER() OVER
        (
            PARTITION BY ItemCode
            ORDER BY
                DocDate,
                CreateDate,
                DocTime,
                DocType,
                DocEntry,
                LineNum
        ) AS Seq,
        COUNT_BIG(*) OVER
        (
            PARTITION BY ItemCode
        ) AS EventCount
    FROM RawEvents
),
Replay AS
(
    SELECT
        E.ItemCode,
        E.Seq,
        E.EventCount,
        CAST(0 AS int) AS Phase,
        CAST(N'[]' AS nvarchar(max)) AS Lots,
        CAST(0 AS int) AS LotCount,
        CAST(0 AS int) AS ScanIdx,
        CAST(-1 AS int) AS BestIdx,
        CAST(N'99999999' AS nvarchar(8)) AS BestDate,
        CAST(0 AS decimal(19,6)) AS QtyLeft,
        CAST(0 AS int) AS HasError
    FROM Events E
    WHERE E.Seq = 1

    UNION ALL

    SELECT
        R.ItemCode,

        CAST(
            CASE
                WHEN R.Phase = 0 AND E.Kind = 'I'
                    THEN R.Seq + 1
                WHEN R.Phase = 2
                 AND R.BestIdx >= 0
                 AND R.QtyLeft <= CONVERT(
                     decimal(19,6),
                     JSON_VALUE(
                         R.Lots,
                         '$[' + CONVERT(
                             varchar(12),
                             CASE WHEN R.BestIdx < 0
                                  THEN 0 ELSE R.BestIdx END
                         ) + '].q'
                     )
                 )
                    THEN R.Seq + 1
                ELSE R.Seq
            END
        AS bigint),

        R.EventCount,

        CAST(
            CASE
                WHEN R.Phase = 0
                    THEN CASE WHEN E.Kind = 'I' THEN 0 ELSE 1 END
                WHEN R.Phase = 1
                    THEN CASE WHEN R.ScanIdx < R.LotCount
                              THEN 1 ELSE 2 END
                WHEN R.BestIdx < 0
                    THEN 3
                WHEN R.QtyLeft <= CONVERT(
                    decimal(19,6),
                    JSON_VALUE(
                        R.Lots,
                        '$[' + CONVERT(
                            varchar(12),
                            CASE WHEN R.BestIdx < 0
                                 THEN 0 ELSE R.BestIdx END
                        ) + '].q'
                    )
                )
                    THEN 0
                ELSE 1
            END
        AS int),

        CAST(
            CASE
                WHEN R.Phase = 0 AND E.Kind = 'I'
                THEN JSON_MODIFY(
                    R.Lots,
                    'append $',
                    JSON_QUERY(
                        JSON_MODIFY(
                            JSON_MODIFY(
                                JSON_MODIFY(
                                    N'{}',
                                    '$.w',
                                    E.ToWhs
                                ),
                                '$.d',
                                CONVERT(nvarchar(8), E.DocDate, 112)
                            ),
                            '$.q',
                            E.Qty
                        )
                    )
                )

                WHEN R.Phase = 2 AND R.BestIdx >= 0
                THEN
                    CASE WHEN E.Kind = 'T'
                    THEN JSON_MODIFY(
                        JSON_MODIFY(
                            R.Lots,
                            '$[' + CONVERT(
                                varchar(12),
                                CASE WHEN R.BestIdx < 0
                                     THEN 0 ELSE R.BestIdx END
                            ) + '].q',
                            CAST(
                                CONVERT(
                                    decimal(19,6),
                                    JSON_VALUE(
                                        R.Lots,
                                        '$[' + CONVERT(
                                            varchar(12),
                                            CASE WHEN R.BestIdx < 0
                                                 THEN 0 ELSE R.BestIdx END
                                        ) + '].q'
                                    )
                                )
                                -
                                CASE
                                    WHEN R.QtyLeft < CONVERT(
                                        decimal(19,6),
                                        JSON_VALUE(
                                            R.Lots,
                                            '$[' + CONVERT(
                                                varchar(12),
                                                CASE WHEN R.BestIdx < 0
                                                     THEN 0 ELSE R.BestIdx END
                                            ) + '].q'
                                        )
                                    )
                                    THEN R.QtyLeft
                                    ELSE CONVERT(
                                        decimal(19,6),
                                        JSON_VALUE(
                                            R.Lots,
                                            '$[' + CONVERT(
                                                varchar(12),
                                                CASE WHEN R.BestIdx < 0
                                                     THEN 0 ELSE R.BestIdx END
                                            ) + '].q'
                                        )
                                    )
                                END
                            AS decimal(19,6))
                        ),
                        'append $',
                        JSON_QUERY(
                            JSON_MODIFY(
                                JSON_MODIFY(
                                    JSON_MODIFY(
                                        N'{}',
                                        '$.w',
                                        E.ToWhs
                                    ),
                                    '$.d',
                                    JSON_VALUE(
                                        R.Lots,
                                        '$[' + CONVERT(
                                            varchar(12),
                                            CASE WHEN R.BestIdx < 0
                                                 THEN 0 ELSE R.BestIdx END
                                        ) + '].d'
                                    )
                                ),
                                '$.q',
                                CASE
                                    WHEN R.QtyLeft < CONVERT(
                                        decimal(19,6),
                                        JSON_VALUE(
                                            R.Lots,
                                            '$[' + CONVERT(
                                                varchar(12),
                                                CASE WHEN R.BestIdx < 0
                                                     THEN 0 ELSE R.BestIdx END
                                            ) + '].q'
                                        )
                                    )
                                    THEN R.QtyLeft
                                    ELSE CONVERT(
                                        decimal(19,6),
                                        JSON_VALUE(
                                            R.Lots,
                                            '$[' + CONVERT(
                                                varchar(12),
                                                CASE WHEN R.BestIdx < 0
                                                     THEN 0 ELSE R.BestIdx END
                                            ) + '].q'
                                        )
                                    )
                                END
                            )
                        )
                    )

                    ELSE JSON_MODIFY(
                        R.Lots,
                        '$[' + CONVERT(
                            varchar(12),
                            CASE WHEN R.BestIdx < 0
                                 THEN 0 ELSE R.BestIdx END
                        ) + '].q',
                        CAST(
                            CONVERT(
                                decimal(19,6),
                                JSON_VALUE(
                                    R.Lots,
                                    '$[' + CONVERT(
                                        varchar(12),
                                        CASE WHEN R.BestIdx < 0
                                             THEN 0 ELSE R.BestIdx END
                                    ) + '].q'
                                )
                            )
                            -
                            CASE
                                WHEN R.QtyLeft < CONVERT(
                                    decimal(19,6),
                                    JSON_VALUE(
                                        R.Lots,
                                        '$[' + CONVERT(
                                            varchar(12),
                                            CASE WHEN R.BestIdx < 0
                                                 THEN 0 ELSE R.BestIdx END
                                        ) + '].q'
                                    )
                                )
                                THEN R.QtyLeft
                                ELSE CONVERT(
                                    decimal(19,6),
                                    JSON_VALUE(
                                        R.Lots,
                                        '$[' + CONVERT(
                                            varchar(12),
                                            CASE WHEN R.BestIdx < 0
                                                 THEN 0 ELSE R.BestIdx END
                                        ) + '].q'
                                    )
                                )
                            END
                        AS decimal(19,6))
                    )
                    END

                ELSE R.Lots
            END
        AS nvarchar(max)),

        CAST(
            R.LotCount +
            CASE
                WHEN R.Phase = 0 AND E.Kind = 'I' THEN 1
                WHEN R.Phase = 2
                 AND R.BestIdx >= 0
                 AND E.Kind = 'T' THEN 1
                ELSE 0
            END
        AS int),

        CAST(
            CASE WHEN R.Phase = 1
                 THEN R.ScanIdx + 1 ELSE 0 END
        AS int),

        CAST(
            CASE WHEN R.Phase = 1
            THEN
                CASE
                    WHEN R.ScanIdx < R.LotCount
                     AND JSON_VALUE(
                         R.Lots,
                         '$[' + CONVERT(varchar(12), R.ScanIdx) + '].w'
                     ) = E.FromWhs
                     AND CONVERT(
                         decimal(19,6),
                         JSON_VALUE(
                             R.Lots,
                             '$[' + CONVERT(varchar(12), R.ScanIdx) + '].q'
                         )
                     ) > 0
                     AND JSON_VALUE(
                         R.Lots,
                         '$[' + CONVERT(varchar(12), R.ScanIdx) + '].d'
                     ) < R.BestDate
                    THEN R.ScanIdx
                    ELSE R.BestIdx
                END
            ELSE -1
            END
        AS int),

        CAST(
            CASE WHEN R.Phase = 1
            THEN
                CASE
                    WHEN R.ScanIdx < R.LotCount
                     AND JSON_VALUE(
                         R.Lots,
                         '$[' + CONVERT(varchar(12), R.ScanIdx) + '].w'
                     ) = E.FromWhs
                     AND CONVERT(
                         decimal(19,6),
                         JSON_VALUE(
                             R.Lots,
                             '$[' + CONVERT(varchar(12), R.ScanIdx) + '].q'
                         )
                     ) > 0
                     AND JSON_VALUE(
                         R.Lots,
                         '$[' + CONVERT(varchar(12), R.ScanIdx) + '].d'
                     ) < R.BestDate
                    THEN JSON_VALUE(
                        R.Lots,
                        '$[' + CONVERT(varchar(12), R.ScanIdx) + '].d'
                    )
                    ELSE R.BestDate
                END
            ELSE N'99999999'
            END
        AS nvarchar(8)),

        CAST(
            CASE
                WHEN R.Phase = 0 THEN E.Qty
                WHEN R.Phase = 2 AND R.BestIdx >= 0
                THEN R.QtyLeft -
                    CASE
                        WHEN R.QtyLeft < CONVERT(
                            decimal(19,6),
                            JSON_VALUE(
                                R.Lots,
                                '$[' + CONVERT(
                                    varchar(12),
                                    CASE WHEN R.BestIdx < 0
                                         THEN 0 ELSE R.BestIdx END
                                ) + '].q'
                            )
                        )
                        THEN R.QtyLeft
                        ELSE CONVERT(
                            decimal(19,6),
                            JSON_VALUE(
                                R.Lots,
                                '$[' + CONVERT(
                                    varchar(12),
                                    CASE WHEN R.BestIdx < 0
                                         THEN 0 ELSE R.BestIdx END
                                ) + '].q'
                            )
                        )
                    END
                ELSE R.QtyLeft
            END
        AS decimal(19,6)),

        CAST(
            CASE WHEN R.Phase = 2 AND R.BestIdx < 0
                 THEN 1 ELSE R.HasError END
        AS int)

    FROM Replay R
    INNER JOIN Events E
        ON E.ItemCode = R.ItemCode
       AND E.Seq = R.Seq
    WHERE R.HasError = 0
),
FinalState AS
(
    SELECT *
    FROM Replay
    WHERE Seq = EventCount + 1
       OR HasError = 1
),
Aging AS
(
    SELECT
        F.ItemCode,
        L.WhsCode,
        SUM(L.Qty) AS ReplayedQty,
        SUM(
            CASE
                WHEN DATEDIFF(
                    day,
                    CONVERT(date, L.OriginDate, 112),
                    P.AsOfDate
                ) >= 181
                THEN L.Qty
                ELSE 0
            END
        ) AS Qty181
    FROM FinalState F
    CROSS JOIN Params P
    CROSS APPLY OPENJSON(F.Lots)
    WITH
    (
        WhsCode    nvarchar(8)   '$.w',
        OriginDate nvarchar(8)   '$.d',
        Qty        decimal(19,6) '$.q'
    ) L
    WHERE F.HasError = 0
    GROUP BY
        F.ItemCode,
        L.WhsCode
)
SELECT
    W.WhsCode      AS [倉],
    H.WhsName      AS [倉庫名稱],
    I.ItemCode     AS [品料號],
    N''            AS [大類],
    I.U_SeriesType AS [系列],
    N''            AS [系列名稱],
    I.ItemName     AS [品名規格],
    W.OnHand       AS [期末庫存量],
    I.InvntryUom   AS [單位],
    I.AvgPrice     AS [標準單價],

    C.StandardCost AS [標準成本],
    S.Price        AS [最近售價],
    C.SalesValue   AS [最近出貨金額],
    N.NetValue     AS [淨變現價值],
    V.Difference   AS [成本市價孰低],
    C.Loss181      AS [181天已提呆滯損失],

    CASE
        WHEN C.Loss181 IS NULL THEN NULL
        WHEN C.Loss181 = 0 AND V.Difference IS NULL THEN NULL
        WHEN C.Loss181 = 0 AND V.Difference < 0
            THEN V.Difference
        ELSE 0
    END AS [應提跌價損失],

    P.Factor AS [淨變現係數],
    A.ReplayedQty AS [帳齡核對數量],
    W.OnHand - ISNULL(A.ReplayedQty, 0) AS [庫存核對差異],
    S.Currency AS [售價幣別],
    S.UnitMsr AS [售價單位],

    CASE
        WHEN F.ItemCode IS NULL
            THEN N'缺少指定單據來源'
        WHEN F.HasError = 1
            THEN N'來源倉不足：核對負庫存、漏列單據及同日順序'
        WHEN ABS(W.OnHand - ISNULL(A.ReplayedQty, 0)) >= 0.000001
            THEN N'庫存不符：帳齡金額留空'
        WHEN S.Price IS NULL
            THEN N'數量吻合；無同倉銷售紀錄'
        ELSE N'數量吻合；仍需核對單據範圍、售價幣別與單位'
    END AS [核對狀態]

FROM OITM I
INNER JOIN OITW W
    ON W.ItemCode = I.ItemCode
INNER JOIN OWHS H
    ON H.WhsCode = W.WhsCode
CROSS JOIN Params P
LEFT JOIN FinalState F
    ON F.ItemCode = I.ItemCode
LEFT JOIN Aging A
    ON A.ItemCode = I.ItemCode
   AND A.WhsCode = W.WhsCode

OUTER APPLY
(
    SELECT TOP 1
        D.Price,
        D.Currency,
        D.UnitMsr
    FROM OINV V
    INNER JOIN INV1 D
        ON D.DocEntry = V.DocEntry
    WHERE D.ItemCode = I.ItemCode
      AND D.WhsCode = W.WhsCode
      AND V.CANCELED = 'N'
      AND D.Quantity > 0
      AND V.DocDate < DATEADD(day, 1, P.AsOfDate)
    ORDER BY
        V.DocDate DESC,
        V.DocEntry DESC,
        D.LineNum DESC
) S

CROSS APPLY
(
    SELECT
        ROUND(W.OnHand * I.AvgPrice, 0) AS StandardCost,
        W.OnHand * S.Price AS SalesValue,

        CASE
            WHEN F.HasError = 0
             AND ABS(W.OnHand - ISNULL(A.ReplayedQty, 0)) < 0.000001
            THEN I.AvgPrice * ISNULL(A.Qty181, 0)
            ELSE NULL
        END AS Loss181
) C

CROSS APPLY
(
    SELECT C.SalesValue * P.Factor AS NetValue
) N

CROSS APPLY
(
    SELECT C.StandardCost - N.NetValue AS Difference
) V

WHERE I.InvntItem = 'Y'
  AND (
      W.OnHand <> 0
      OR ISNULL(A.ReplayedQty, 0) <> 0
  )
  AND (
      P.ItemFilter = N''
      OR I.ItemCode = P.ItemFilter
  )

ORDER BY
    W.WhsCode,
    I.U_SeriesType,
    I.ItemCode

OPTION (MAXRECURSION 32767);
```

這版使用**目前庫存、目前成本**，沿用舊報表指定的單據範圍；大類及系列名稱暫留空。「181天已提呆滯損失」是 FIFO 推算金額，未讀取會計已入帳金額。若出現錯誤，請貼完整錯誤訊息。

<a id="qa19"></a>

### 19. 如果我把這支放到SSMS裡面 等於是一支程序後，再從FMS呼叫這支可以嗎

來源：[S178-Q016](<../../原始問答/S178_確認最近售價取得方式.md#q16>)；2026-09-17

**問題**

如果我把這支放到SSMS裡面 等於是一支程序後，再從FMS呼叫這支可以嗎

**歷史回答**

**可以。以你現在這種「整張查詢報表」來說，可以把計算邏輯建成 SQL Server 預存程序，再由 SAP B1 查詢管理員呼叫。** SAP Community 也有透過 `EXEC` 呼叫的案例，但仍需確認你環境的權限與查詢限制。[參考案例](https://community.sap.com/t5/enterprise-resource-planning-q-a/procedure-handle-in-query-manager/qaq-p/7635895)

不過，**只把 SQL 貼到 SSMS 執行，不會自動變成程序**；需要使用 `CREATE PROCEDURE`，建立在 SAP B1 對應的公司資料庫中。[Microsoft 說明](https://learn.microsoft.com/en-us/sql/relational-databases/stored-procedures/create-a-stored-procedure)

可以設計成：

| 放置位置 | 內容 |
|---|---|
| SSMS／公司資料庫 | 預存程序：處理分倉 FIFO、調撥承接帳齡、最近售價及損失計算 |
| SAP B1 查詢管理員 | 一小段 `EXEC` 呼叫程序並傳入參數 |

例如，**程序建立完成後**，SAP 查詢中只需要：

```sql
EXEC dbo.usp_InventoryLCM
    @Factor = 0.80,
    @ItemCode = N'BATT12V-23A';
```

其中：

- `@Factor`：你自行設定的淨變現係數。
- `@ItemCode`：指定料號；可以把程序設計成空字串代表全部。
- 程序內可以使用 `DECLARE`、暫存表等，不需要把這些語法放在 SAP 查詢裡。
- SAP 使用的資料庫帳號需要有這支程序的執行權限。

**這個方式適合你目前的報表。** 但包成程序本身不會自動加速；可以進一步把前面較長的 JSON 遞迴改成暫存表計算，再核對結果與效能。

如果你說的 FMS 是「欄位上的格式化搜尋」，則要另外做只回傳單一值的版本；你目前這張多欄、多列報表，應放在**查詢管理員**呼叫。

<a id="qa20"></a>

### 20. 那給我SP

來源：[S178-Q017](<../../原始問答/S178_確認最近售價取得方式.md#q17>)；2026-09-17

**問題**

那給我SP

**歷史回答**

以下是 **SP 完整版**。先在 SSMS 選擇你的 **SAP B1 公司資料庫**，執行整段建立程序。

也已存成檔案：[usp_InventoryLCM.sql](C:/Users/nick_yeh/Documents/維熹FMS/usp_InventoryLCM.sql)。

**這版使用暫存表，不使用 JSON；`CREATE OR ALTER` 需要 SQL Server 2016 SP1 以上。** [Microsoft 說明](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-procedure-transact-sql)

```sql
CREATE OR ALTER PROCEDURE dbo.usp_InventoryLCM
    @Factor   decimal(19,6) = 1.00,
    @ItemCode nvarchar(50) = N'',
    @WhsCode  nvarchar(8) = N''
AS
BEGIN
    SET NOCOUNT ON;

    IF @Factor IS NULL OR @Factor < 0
        THROW 50001, N'淨變現係數不可空白或小於零。', 1;

    SET @ItemCode = ISNULL(LTRIM(RTRIM(@ItemCode)), N'');
    SET @WhsCode = ISNULL(LTRIM(RTRIM(@WhsCode)), N'');

    DECLARE @AsOfDate date = CAST(GETDATE() AS date);

    -- 目前庫存、項目主檔成本
    -- 倉庫條件只篩選最後結果，不影響跨倉追蹤
    SELECT
        I.ItemCode,
        I.ItemName,
        I.U_SeriesType,
        I.InvntryUom,
        I.AvgPrice,
        W.WhsCode,
        H.WhsName,
        W.OnHand
    INTO #Stock
    FROM OITM I
    INNER JOIN OITW W
        ON W.ItemCode = I.ItemCode
    INNER JOIN OWHS H
        ON H.WhsCode = W.WhsCode
    WHERE I.InvntItem = 'Y'
      AND (@ItemCode = N'' OR I.ItemCode = @ItemCode);

    ;WITH RawEvents AS
    (
        -- 收貨採購單
        SELECT
            L.ItemCode, H.DocDate, H.CreateDate, H.DocTime,
            20 AS DocType, H.DocEntry, L.LineNum,
            'I' AS Kind,
            CAST(N'' AS nvarchar(8)) AS FromWhs,
            L.WhsCode AS ToWhs,
            CAST(L.InvQty AS decimal(19,6)) AS Qty
        FROM OPDN H
        INNER JOIN PDN1 L
            ON L.DocEntry = H.DocEntry
        WHERE H.CANCELED = 'N'
          AND L.InvQty > 0
          AND H.DocDate < DATEADD(day, 1, @AsOfDate)
          AND (@ItemCode = N'' OR L.ItemCode = @ItemCode)

        UNION ALL

        -- 生產相關及指定收貨單
        SELECT
            L.ItemCode, H.DocDate, H.CreateDate, H.DocTime,
            59, H.DocEntry, L.LineNum,
            'I', CAST(N'' AS nvarchar(8)), L.WhsCode,
            CAST(L.InvQty AS decimal(19,6))
        FROM OIGN H
        INNER JOIN IGN1 L
            ON L.DocEntry = H.DocEntry
        WHERE H.CANCELED = 'N'
          AND L.InvQty > 0
          AND H.DocDate < DATEADD(day, 1, @AsOfDate)
          AND (@ItemCode = N'' OR L.ItemCode = @ItemCode)
          AND (
              L.BaseType = 202
              OR H.Series = 19
              OR H.DocNum = 800000000
          )

        UNION ALL

        -- 生產相關及指定發貨單
        SELECT
            L.ItemCode, H.DocDate, H.CreateDate, H.DocTime,
            60, H.DocEntry, L.LineNum,
            'O', L.WhsCode, CAST(N'' AS nvarchar(8)),
            CAST(L.InvQty AS decimal(19,6))
        FROM OIGE H
        INNER JOIN IGE1 L
            ON L.DocEntry = H.DocEntry
        WHERE H.CANCELED = 'N'
          AND L.InvQty > 0
          AND H.DocDate < DATEADD(day, 1, @AsOfDate)
          AND (@ItemCode = N'' OR L.ItemCode = @ItemCode)
          AND (
              L.BaseType = 202
              OR H.Series = 20
              OR H.DocNum IN (650000000, 650000001)
          )

        UNION ALL

        -- 交貨單
        SELECT
            L.ItemCode, H.DocDate, H.CreateDate, H.DocTime,
            15, H.DocEntry, L.LineNum,
            'O', L.WhsCode, CAST(N'' AS nvarchar(8)),
            CAST(L.InvQty AS decimal(19,6))
        FROM ODLN H
        INNER JOIN DLN1 L
            ON L.DocEntry = H.DocEntry
        WHERE H.CANCELED = 'N'
          AND L.InvQty > 0
          AND H.DocDate < DATEADD(day, 1, @AsOfDate)
          AND (@ItemCode = N'' OR L.ItemCode = @ItemCode)

        UNION ALL

        -- 庫存調撥：保留原入庫日期
        SELECT
            L.ItemCode, H.DocDate, H.CreateDate, H.DocTime,
            67, H.DocEntry, L.LineNum,
            'T', L.FromWhsCod, L.WhsCode,
            CAST(L.InvQty AS decimal(19,6))
        FROM OWTR H
        INNER JOIN WTR1 L
            ON L.DocEntry = H.DocEntry
        WHERE H.CANCELED = 'N'
          AND L.InvQty > 0
          AND H.DocDate < DATEADD(day, 1, @AsOfDate)
          AND (@ItemCode = N'' OR L.ItemCode = @ItemCode)
          AND L.FromWhsCod <> L.WhsCode
    )
    SELECT
        ROW_NUMBER() OVER (
            ORDER BY
                ItemCode, DocDate, CreateDate, DocTime,
                DocType, DocEntry, LineNum
        ) AS EventID,
        ItemCode, DocDate, Kind, FromWhs, ToWhs, Qty,
        DocType, DocEntry, LineNum
    INTO #Events
    FROM RawEvents
    WHERE EXISTS (
        SELECT 1
        FROM #Stock S
        WHERE S.ItemCode = RawEvents.ItemCode
    );

    CREATE UNIQUE CLUSTERED INDEX IX_Events
        ON #Events(EventID);

    SELECT ItemCode
    INTO #Seen
    FROM #Events
    GROUP BY ItemCode;

    CREATE UNIQUE CLUSTERED INDEX IX_Seen
        ON #Seen(ItemCode);

    -- 各倉剩餘的 FIFO 入庫層
    CREATE TABLE #Layers
    (
        LayerID bigint IDENTITY(1,1) NOT NULL PRIMARY KEY,
        ItemCode nvarchar(50) COLLATE DATABASE_DEFAULT NOT NULL,
        WhsCode nvarchar(8) COLLATE DATABASE_DEFAULT NOT NULL,
        OriginDate date NOT NULL,
        OriginEvent bigint NOT NULL,
        Qty decimal(19,6) NOT NULL
    );

    CREATE INDEX IX_Layers_FIFO
        ON #Layers(
            ItemCode, WhsCode, OriginDate, OriginEvent, LayerID
        )
        INCLUDE(Qty);

    CREATE TABLE #Errors
    (
        ItemCode nvarchar(50) COLLATE DATABASE_DEFAULT
            NOT NULL PRIMARY KEY,
        EventID bigint NOT NULL
    );

    DECLARE
        @EventID bigint,
        @Item nvarchar(50),
        @DocDate date,
        @Kind char(1),
        @FromWhs nvarchar(8),
        @ToWhs nvarchar(8),
        @Qty decimal(19,6),
        @QtyLeft decimal(19,6),
        @Available decimal(38,6),
        @LayerID bigint,
        @OriginDate date,
        @OriginEvent bigint,
        @LayerQty decimal(19,6),
        @Take decimal(19,6);

    DECLARE EventCursor CURSOR LOCAL FAST_FORWARD FOR
        SELECT
            EventID, ItemCode, DocDate, Kind,
            FromWhs, ToWhs, Qty
        FROM #Events
        ORDER BY EventID;

    OPEN EventCursor;

    FETCH NEXT FROM EventCursor INTO
        @EventID, @Item, @DocDate, @Kind,
        @FromWhs, @ToWhs, @Qty;

    WHILE @@FETCH_STATUS = 0
    BEGIN
        IF NOT EXISTS (
            SELECT 1 FROM #Errors WHERE ItemCode = @Item
        )
        BEGIN
            IF @Kind = 'I'
            BEGIN
                INSERT INTO #Layers(
                    ItemCode, WhsCode, OriginDate, OriginEvent, Qty
                )
                VALUES(
                    @Item, @ToWhs, @DocDate, @EventID, @Qty
                );
            END
            ELSE
            BEGIN
                SELECT @Available = ISNULL(SUM(Qty), 0)
                FROM #Layers
                WHERE ItemCode = @Item
                  AND WhsCode = @FromWhs;

                IF @Available < @Qty
                BEGIN
                    -- 無法追溯足夠來源，該品項停止計算
                    INSERT INTO #Errors(ItemCode, EventID)
                    VALUES(@Item, @EventID);
                END
                ELSE
                BEGIN
                    SET @QtyLeft = @Qty;

                    WHILE @QtyLeft > 0
                    BEGIN
                        SET @LayerID = NULL;

                        SELECT TOP 1
                            @LayerID = LayerID,
                            @OriginDate = OriginDate,
                            @OriginEvent = OriginEvent,
                            @LayerQty = Qty
                        FROM #Layers
                        WHERE ItemCode = @Item
                          AND WhsCode = @FromWhs
                          AND Qty > 0
                        ORDER BY
                            OriginDate, OriginEvent, LayerID;

                        IF @LayerID IS NULL
                        BEGIN
                            INSERT INTO #Errors(ItemCode, EventID)
                            VALUES(@Item, @EventID);
                            BREAK;
                        END;

                        SET @Take =
                            CASE WHEN @QtyLeft < @LayerQty
                                 THEN @QtyLeft ELSE @LayerQty END;

                        UPDATE #Layers
                        SET Qty = Qty - @Take
                        WHERE LayerID = @LayerID;

                        DELETE FROM #Layers
                        WHERE LayerID = @LayerID
                          AND Qty = 0;

                        IF @Kind = 'T'
                        BEGIN
                            -- 調入倉承接原入庫層，不重算帳齡
                            INSERT INTO #Layers(
                                ItemCode, WhsCode,
                                OriginDate, OriginEvent, Qty
                            )
                            VALUES(
                                @Item, @ToWhs,
                                @OriginDate, @OriginEvent, @Take
                            );
                        END;

                        SET @QtyLeft = @QtyLeft - @Take;
                    END;
                END;
            END;
        END;

        FETCH NEXT FROM EventCursor INTO
            @EventID, @Item, @DocDate, @Kind,
            @FromWhs, @ToWhs, @Qty;
    END;

    CLOSE EventCursor;
    DEALLOCATE EventCursor;

    -- 按料號、倉庫彙總 181 天以上數量
    SELECT
        L.ItemCode,
        L.WhsCode,
        SUM(L.Qty) AS ReplayedQty,
        SUM(
            CASE
                WHEN DATEDIFF(day, L.OriginDate, @AsOfDate) >= 181
                THEN L.Qty
                ELSE 0
            END
        ) AS Qty181
    INTO #Aging
    FROM #Layers L
    WHERE NOT EXISTS (
        SELECT 1
        FROM #Errors E
        WHERE E.ItemCode = L.ItemCode
    )
    GROUP BY L.ItemCode, L.WhsCode;

    CREATE UNIQUE CLUSTERED INDEX IX_Aging
        ON #Aging(ItemCode, WhsCode);

    SELECT
        I.WhsCode      AS [倉],
        I.WhsName      AS [倉庫名稱],
        I.ItemCode     AS [品料號],
        N''            AS [大類],
        I.U_SeriesType AS [系列],
        N''            AS [系列名稱],
        I.ItemName     AS [品名規格],
        I.OnHand       AS [期末庫存量],
        I.InvntryUom   AS [單位],
        I.AvgPrice     AS [標準單價],

        C.StandardCost AS [標準成本],
        S.Price        AS [最近售價],
        C.SalesValue   AS [最近出貨金額],
        N.NetValue     AS [淨變現價值],
        V.Difference   AS [成本市價孰低],
        C.Loss181      AS [181天已提呆滯損失],

        CASE
            WHEN C.Loss181 IS NULL THEN NULL
            WHEN C.Loss181 = 0 AND V.Difference IS NULL THEN NULL
            WHEN C.Loss181 = 0 AND V.Difference < 0
                THEN V.Difference
            ELSE 0
        END AS [應提跌價損失],

        @Factor AS [淨變現係數],

        CASE WHEN E.ItemCode IS NULL
             THEN ISNULL(A.ReplayedQty, 0)
             ELSE NULL
        END AS [帳齡核對數量],

        CASE WHEN E.ItemCode IS NULL
             THEN I.OnHand - ISNULL(A.ReplayedQty, 0)
             ELSE NULL
        END AS [庫存核對差異],

        S.Currency AS [售價幣別],
        S.UnitMsr AS [售價單位],

        CASE
            WHEN E.ItemCode IS NOT NULL
                THEN N'來源倉不足：核對負庫存、漏列單據及同日順序'
            WHEN T.ItemCode IS NULL
                THEN N'缺少指定單據來源'
            WHEN ABS(I.OnHand - ISNULL(A.ReplayedQty, 0)) >= 0.000001
                THEN N'庫存不符：帳齡金額留空'
            WHEN S.Price IS NULL
                THEN N'數量吻合；無同倉銷售紀錄'
            ELSE N'數量吻合；仍需核對單據範圍、售價幣別與單位'
        END AS [核對狀態]

    FROM #Stock I
    LEFT JOIN #Seen T
        ON T.ItemCode = I.ItemCode
    LEFT JOIN #Errors E
        ON E.ItemCode = I.ItemCode
    LEFT JOIN #Aging A
        ON A.ItemCode = I.ItemCode
       AND A.WhsCode = I.WhsCode

    OUTER APPLY
    (
        SELECT TOP 1
            D.Price,
            D.Currency,
            D.UnitMsr
        FROM OINV H
        INNER JOIN INV1 D
            ON D.DocEntry = H.DocEntry
        WHERE H.CANCELED = 'N'
          AND D.Quantity > 0
          AND D.ItemCode = I.ItemCode
          AND D.WhsCode = I.WhsCode
          AND H.DocDate < DATEADD(day, 1, @AsOfDate)
        ORDER BY
            H.DocDate DESC,
            H.DocEntry DESC,
            D.LineNum DESC
    ) S

    CROSS APPLY
    (
        SELECT
            ROUND(I.OnHand * I.AvgPrice, 0) AS StandardCost,
            I.OnHand * S.Price AS SalesValue,

            CASE
                WHEN T.ItemCode IS NOT NULL
                 AND E.ItemCode IS NULL
                 AND ABS(I.OnHand - ISNULL(A.ReplayedQty, 0)) < 0.000001
                THEN I.AvgPrice * ISNULL(A.Qty181, 0)
                ELSE NULL
            END AS Loss181
    ) C

    CROSS APPLY
    (
        SELECT C.SalesValue * @Factor AS NetValue
    ) N

    CROSS APPLY
    (
        SELECT C.StandardCost - N.NetValue AS Difference
    ) V

    WHERE (
        I.OnHand <> 0
        OR ISNULL(A.ReplayedQty, 0) <> 0
    )
      AND (@WhsCode = N'' OR I.WhsCode = @WhsCode)

    ORDER BY
        I.WhsCode,
        I.U_SeriesType,
        I.ItemCode;
END;
GO
```

建立完成後，**SAP 查詢報表只放下面這段**，先測一個料號：

```sql
EXEC dbo.usp_InventoryLCM
    @Factor = 0.80,
    @ItemCode = N'BATT12V-23A',
    @WhsCode = N'';
```

查全部料號、全部倉庫：

```sql
EXEC dbo.usp_InventoryLCM
    @Factor = 0.80,
    @ItemCode = N'',
    @WhsCode = N'';
```

指定 AW 倉：

```sql
EXEC dbo.usp_InventoryLCM
    @Factor = 0.80,
    @ItemCode = N'',
    @WhsCode = N'AW';
```

**已通過靜態語法解析與 FIFO 邏輯模擬，尚未在你的 SAP 資料庫實測。** 這版仍沿用舊報表指定的入出庫單據範圍，使用目前庫存與成本；大類、系列名稱暫留空，售價尚未換算幣別及單位。

<a id="qa21"></a>

### 21. @Factor = 0.80, @ItemCode = N'', @WhsCode = N'AW'; @Factor = 0.80可以讓我選嗎 就是給我個欄位選擇

來源：[S178-Q018](<../../原始問答/S178_確認最近售價取得方式.md#q18>)；2026-09-18

**問題**

@Factor = 0.80,     @ItemCode = N'',     @WhsCode = N'AW';

@Factor = 0.80可以讓我選嗎 就是給我個欄位選擇

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa22"></a>

### 22. 或是讓我填值進去 而不是在上面改

來源：[S178-Q019](<../../原始問答/S178_確認最近售價取得方式.md#q19>)；2026-09-18

**問題**

或是讓我填值進去 而不是在上面改

**歷史回答**

可以，**SP 不用修改**，把 SAP 查詢報表的呼叫改成下面這段，使用 `[%0]` 接收你輸入的係數：

```sql
EXEC dbo.usp_InventoryLCM
    @Factor = '[%0]',
    @ItemCode = N'',
    @WhsCode = N'AW';
```

執行查詢時，輸入：

- `0.80`：最近出貨金額 × 80%。
- `0.95`：最近出貨金額 × 95%。
- `1.00`：最近出貨金額 × 100%。

**填小數，不要輸入 `80%`。** 這樣仍查全部料號、固定 AW 倉，每次只需輸入係數。

SAP B1 查詢管理員有這種 SP 參數傳入方式，但不同版本可能需要額外的欄位參照註解；若沒有跳出輸入視窗或出現錯誤，貼錯誤訊息給我即可。[SAP Community 範例](https://community.sap.com/t5/enterprise-resource-planning-q-a/how-to-add-parameters-to-a-stored-procedure-in-b1-query-generator/qaq-p/3826453)

<a id="qa23"></a>

### 23. '後端排程工作' (OBSJ)

來源：[S178-Q020](<../../原始問答/S178_確認最近售價取得方式.md#q20>)；2026-09-18

**問題**

'後端排程工作' (OBSJ)

**歷史回答**

`'後端排程工作' (OBSJ)` 通常只是錯誤訊息最後附帶的名稱，**還需要前面的錯誤文字，才能確定原因**。

你先將 SAP 查詢報表改成下面這段，補上參數欄位參照；SP 不用改，也不用 `DECLARE`：

```sql
/* SELECT FROM [dbo].[OITM] T0 */

EXEC dbo.usp_InventoryLCM
    @Factor = /* T0.AvgPrice */ '[%0]',
    @ItemCode = N'',
    @WhsCode = N'AW';
```

跳出輸入視窗後填 `0.80`。這裡 `T0.AvgPrice` 只是提供 SAP 辨識數值參數的欄位資訊，**實際係數仍使用你輸入的數字**。

如果仍報錯，請貼上**完整錯誤訊息，包含 OBSJ 前面的文字**，或直接傳錯誤畫面。

<a id="qa24"></a>

### 24. 我可以修改畫面中的字嗎'

來源：[S178-Q021](<../../原始問答/S178_確認最近售價取得方式.md#q21>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-760d1d13-7bc6-4313-b3a5-2652799fc9b5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-760d1d13-7bc6-4313-b3a5-2652799fc9b5.png



## My request:
 我可以修改畫面中的字嗎'

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

可以改成「**淨變現係數**」，但**不能只在 SQL 加 `AS [淨變現係數]` 就改掉**。

畫面現在顯示「項目成本」，是因為我們參照了：

```sql
/* T0.AvgPrice */
```

SAP 會取這個欄位的說明作為輸入提示。要自訂文字，可以建立一個自訂欄位，再讓參數參照它。[SAP Community 說明](https://community.sap.com/t5/enterprise-resource-planning-q-a/how-to-change-label-text-on-sap-b1-prompted-query-input-field/qaq-p/9043805)

例如，**先透過 SAP 的自訂欄位管理，在項目主檔建立**：

| 設定 | 內容 |
|---|---|
| 欄位名稱 | `NRVFactor` |
| 說明 | 淨變現係數 |
| 類型 | 可輸入小數的數值欄位 |

建立後，資料庫欄位會是 `OITM.U_NRVFactor`，查詢改成：

```sql
/* SELECT FROM [dbo].[OITM] T0 */

EXEC dbo.usp_InventoryLCM
    @Factor = /* T0.U_NRVFactor */ '[%0]',
    @ItemCode = N'',
    @WhsCode = N'AW';
```

這樣便可用自訂欄位的說明作為提示。**SP 不用修改，也不用在每個品項填係數**；這裡只是借用欄位說明，實際數字仍由你執行報表時輸入。

<a id="qa25"></a>

### 25. 然後我想再多一個 我可以篩選哪個日期要查

來源：[S178-Q022](<../../原始問答/S178_確認最近售價取得方式.md#q22>)；2026-09-18

**問題**

然後我想再多一個 我可以篩選哪個日期要查

**歷史回答**

可以，改成執行時輸入 **「係數＋查詢截止日期」**。

**先在 SSMS 執行更新後的 [usp_InventoryLCM.sql](C:/Users/nick_yeh/Documents/維熹FMS/usp_InventoryLCM.sql)**，更新原本 SP，再把 SAP 查詢報表改成：

```sql
/* SELECT FROM [dbo].[OITM] T0 */
/* SELECT FROM [dbo].[OINV] T1 */

EXEC dbo.usp_InventoryLCM
    @Factor = /* T0.AvgPrice */ '[%0]',
    @ItemCode = N'',
    @WhsCode = N'AW',
    @AsOfDate = /* T1.DocDate */ '[%1]';
```

執行時輸入：

| 輸入欄位 | 範例 |
|---|---|
| 項目成本（實際作為係數） | `0.80` |
| 過帳日期（查詢截止日期） | `2026/08/31` |

如果你已建立前面說的 `U_NRVFactor`，把 `T0.AvgPrice` 換成 `T0.U_NRVFactor`，就能沿用「淨變現係數」提示。

選擇 **2026/08/31** 後，會查：

- **期末庫存量**：由 `OINM` 累計到 8/31 的分倉結存，不再使用今天的 `OITW.OnHand`。[計算方式參考](https://community.sap.com/t5/enterprise-resource-planning-q-a/sap-b1-sql-inventory-listing-per-warehouse-on-specific-date-with-batch/qaq-p/12430387)
- **最近售價**：8/31 以前最近一筆同倉售價。
- **181 天帳齡**：以 8/31 為計算基準。
- **成本**：仍用你指定的**目前 `OITM.AvgPrice`**，不是歷史成本。

新版已通過靜態語法檢查，尚未在 SAP 實測；帳齡仍沿用原單據範圍及目前取消狀態，歷史結果需核對。

<a id="qa26"></a>

### 26. SQL／程式碼調整與說明

來源：[S178-Q023](<../../原始問答/S178_確認最近售價取得方式.md#q23>)；2026-09-18

**問題**

CREATE OR ALTER PROCEDURE dbo.usp\_InventoryLCM 這個意思是 如果我有相同名稱會更新嗎

**歷史回答**

對，**在目前選取的資料庫中**：

- 已有 `dbo.usp_InventoryLCM` → 更新這支預存程序的定義。
- 沒有這支程序 → 新增。

```sql
CREATE OR ALTER PROCEDURE dbo.usp_InventoryLCM
```

注意：**會用你這次提供的完整程式碼取代原本內容，不是只合併修改的部分。** 執行前確認 SSMS 選的是正確的 SAP 公司資料庫。

這個動作只建立／更新程序，**不會立即執行報表**；呼叫 `EXEC dbo.usp_InventoryLCM ...` 才會執行。

<a id="qa27"></a>

### 27. 我記得我有一個 LCM相關的報表

來源：[S194-Q001](<../../原始問答/S194_釐清未完成的請求.md#q1>)；2026-09-22

**問題**

我記得我有一個  LCM相關的報表

**歷史回答**

找到兩個與 LCM 相關的檔案，你記得的報表可能是第一個：

- [202606存貨LCM評價.xlsx](C:/Users/nick_yeh/Desktop/202606存貨LCM評價.xlsx) — 在桌面上。
- [usp_InventoryLCM.sql](C:/Users/nick_yeh/Documents/維熹FMS/usp_InventoryLCM.sql) — 在「維熹FMS」資料夾。

要我打開 Excel，幫你確認報表內容嗎？

<a id="qa28"></a>

### 28. 第二個

來源：[S194-Q002](<../../原始問答/S194_釐清未完成的請求.md#q2>)；2026-09-22

**問題**

第二個

**歷史回答**

是這份：[usp_InventoryLCM.sql](C:/Users/nick_yeh/Documents/維熹FMS/usp_InventoryLCM.sql)，已幫你開啟。

它是 SAP B1 的**存貨成本市價孰低（LCM）報表程序**，包含期末庫存、成本、最近售價、淨變現價值、181 天呆滯損失及應提跌價損失，可依截止日、料號、倉庫查詢。

目前版本註記：**成本仍採目前平均成本，尚未在 SAP 公司資料庫執行驗證。**

<a id="qa29"></a>

### 29. 他現在跑出\\

來源：[S194-Q003](<../../原始問答/S194_釐清未完成的請求.md#q3>)；2026-09-22

**問題**

他現在跑出\\

**歷史回答**

把目前跑出的結果或錯誤訊息貼上來，也可以直接傳截圖，我幫你看。

<a id="qa30"></a>

### 30. 1\). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keywo…

來源：[S194-Q004](<../../原始問答/S194_釐清未完成的請求.md#q4>)；2026-09-22

**問題**

1\). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keyword 'CONVERT'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '文件' (RDOC) (s) could not be prepared.

**歷史回答**

這個錯誤可能出在**呼叫報表的 SQL**：SQL Server 的 `EXEC` 參數不能直接放 `CONVERT(...)`，必須先轉換成變數，再傳入程序。

如果你目前這樣寫：

```sql
EXEC dbo.usp_InventoryLCM
    @AsOfDate = CONVERT(date, '20260630', 112);
```

請改成：

```sql
DECLARE @ReportDate date;
SET @ReportDate = CONVERT(date, '20260630', 112);

EXEC dbo.usp_InventoryLCM
    @Factor = 1.00,
    @ItemCode = N'',
    @WhsCode = N'',
    @AsOfDate = @ReportDate;
```

日期請換成你的報表截止日。

我檢查的檔案中，`IF` 裡的 `CONVERT` 用法本身合法；**目前還不能確認錯誤位置**。請貼上你在 SAP 查詢管理員實際執行的 SQL，尤其是 `EXEC` 和日期參數那段，我就能針對原句修正。

<a id="qa31"></a>

### 31. /\ SELECT FROM [dbo].[OITM] T0 / / SELECT FROM [dbo].[OINV] T1 \/ EXEC dbo.usp\_InventoryL…

來源：[S194-Q005](<../../原始問答/S194_釐清未完成的請求.md#q5>)；2026-09-22

**問題**

/\* SELECT FROM [dbo].[OITM] T0 */
/* SELECT FROM [dbo].[OINV] T1 \*/
EXEC dbo.usp\_InventoryLCM
@Factor = /\* T0.U\_NRVFactor */ '[%0]',
@ItemCode = N'',
@WhsCode = N'',
@AsOfDate = /* T1.DocDate \*/ '[%1]';

這是我目前的寫法

**歷史回答**

你這段沒有直接使用 `CONVERT`，所以先前推測不適用。可以先改成**先接收參數、再呼叫程序**：

```sql
/* SELECT FROM [dbo].[OITM] T0 */
/* SELECT FROM [dbo].[OINV] T1 */

DECLARE @Factor decimal(19,6);
DECLARE @AsOfDate date;

/* WHERE */
SET @Factor = /* T0.U_NRVFactor */ '[%0]';
SET @AsOfDate = /* T1.DocDate */ '[%1]';

EXEC dbo.usp_InventoryLCM
    @Factor = @Factor,
    @ItemCode = N'',
    @WhsCode = N'',
    @AsOfDate = @AsOfDate;
```

若仍出現相同錯誤，請在 SAP 查詢管理員用下面這段固定參數測試，以區分是 SAP 參數處理，還是資料庫程序的問題：

```sql
EXEC dbo.usp_InventoryLCM
    @Factor = 1.00,
    @ItemCode = N'',
    @WhsCode = N'',
    @AsOfDate = '20260630';
```

目前本機檔案無法證明資料庫內的程序與它相同；若固定參數也失敗，就需要查看資料庫中實際的程序定義。

<a id="qa32"></a>

### 32. /\ SELECT FROM [dbo].[OITM] T0 /\ / SELECT FROM [dbo].[OINV] T1 \/\ EXEC dbo.usp\_Inventor…

來源：[S194-Q006](<../../原始問答/S194_釐清未完成的請求.md#q6>)；2026-09-22

**問題**

/\* SELECT FROM [dbo].[OITM] T0 */*\
*/* SELECT FROM [dbo].[OINV] T1 \*/\
EXEC dbo.usp\_InventoryLCM\
@Factor = /\* T0.U\_NRVFactor */ '[%0]',*\
*@ItemCode = N'',*\
*@WhsCode = N'',*\
*@AsOfDate = /* T1.DocDate \*/ '[%1]';  

我要這支 幫我整理一下格是

**歷史回答**

```sql
/* SELECT FROM [dbo].[OITM] T0 */
/* SELECT FROM [dbo].[OINV] T1 */

EXEC dbo.usp_InventoryLCM
    @Factor   = /* T0.U_NRVFactor */ '[%0]',
    @ItemCode = N'',
    @WhsCode  = N'',
    @AsOfDate = /* T1.DocDate */ '[%1]';
```

