# 撰寫 SAP B1 區間存貨報表語法

來源編號：S090  
聊天 ID：`01a01ca4-b40f-7773-9c6e-3476b9dba6f0`  
聊天起始日期（台北）：2026-08-20  
保存位置：一般本機紀錄  
問答段落：5

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S090-Q001｜如果我想要 拉出 一個區間的存貨報表 sapb1 可以幫我寫語法嗎

日期：2026-08-20

**你的提問**

如果我想要 拉出 一個區間的存貨報表 sapb1 可以幫我寫語法嗎

**當時回答**

可以。SAP Business One 可用 `OINM`（存貨異動）拉出指定日期區間的「期初、入庫、出庫、期末」報表。

先提供 SQL Server 版，可直接放進 SAP B1 查詢產生器：

```sql
/* [%0] 起始日期 */
/* [%1] 結束日期 */

SELECT
    T0.ItemCode                         AS '料號',
    MAX(T1.ItemName)                    AS '品名',
    T0.Warehouse                        AS '倉庫',

    SUM(
        CASE WHEN T0.DocDate < [%0]
             THEN T0.InQty - T0.OutQty
             ELSE 0
        END
    )                                   AS '期初數量',

    SUM(
        CASE WHEN T0.DocDate BETWEEN [%0] AND [%1]
             THEN T0.InQty
             ELSE 0
        END
    )                                   AS '期間入庫',

    SUM(
        CASE WHEN T0.DocDate BETWEEN [%0] AND [%1]
             THEN T0.OutQty
             ELSE 0
        END
    )                                   AS '期間出庫',

    SUM(
        CASE WHEN T0.DocDate <= [%1]
             THEN T0.InQty - T0.OutQty
             ELSE 0
        END
    )                                   AS '期末數量',

    SUM(
        CASE WHEN T0.DocDate < [%0]
             THEN T0.TransValue
             ELSE 0
        END
    )                                   AS '期初金額',

    SUM(
        CASE WHEN T0.DocDate BETWEEN [%0] AND [%1]
             THEN T0.TransValue
             ELSE 0
        END
    )                                   AS '期間異動金額',

    SUM(
        CASE WHEN T0.DocDate <= [%1]
             THEN T0.TransValue
             ELSE 0
        END
    )                                   AS '期末金額'

FROM OINM T0
INNER JOIN OITM T1
    ON T0.ItemCode = T1.ItemCode

WHERE T0.DocDate <= [%1]

GROUP BY
    T0.ItemCode,
    T0.Warehouse

HAVING
       SUM(CASE WHEN T0.DocDate < [%0]
                THEN T0.InQty - T0.OutQty ELSE 0 END) <> 0
    OR SUM(CASE WHEN T0.DocDate BETWEEN [%0] AND [%1]
                THEN T0.InQty + T0.OutQty ELSE 0 END) <> 0

ORDER BY
    T0.ItemCode,
    T0.Warehouse;
```

這份報表的計算方式是：

- 期初數量：開始日期以前的累積入庫－出庫
- 期間入庫／出庫：指定日期區間的異動
- 期末數量：結束日期以前的累積入庫－出庫
- 金額：依 SAP B1 的 `TransValue` 計算

若你使用的是 SAP HANA，欄位與語法需要改成雙引號版本。你告訴我以下資訊，我可以幫你寫成正式可用版本：

1. 資料庫是 SQL Server 還是 SAP HANA？
2. 要全部倉庫，還是可選倉庫？
3. 是否需要批號、序號或品項群組？
4. 金額想看「存貨成本異動」還是「期末庫存價值」？

---

<a id="q2"></a>

## S090-Q002｜這支很好 但可以把期間異動金額 幫我變成 入庫異動金額 出庫異動金額 然後 我的篩選條件只要 我選的過帳日期之前 0731 就是之前都要

日期：2026-08-20

**你的提問**

這支很好 但可以把期間異動金額 幫我變成 入庫異動金額  出庫異動金額 然後 我的篩選條件只要 我選的過帳日期之前 0731 就是之前都要

**當時回答**

可以，改成只輸入一個「截止過帳日期」。例如輸入 `2026/07/31`，就會統計所有過帳日期小於等於 7/31 的資料。

以下為 SQL Server 版本：

```sql
/* [%0] 截止過帳日期 */

DECLARE @ToDate DATETIME

SET @ToDate = /* T0.DocDate */ '[%0]'

SELECT
    T0.ItemCode                                      AS '料號',
    MAX(T1.ItemName)                                 AS '品名',
    T0.Warehouse                                     AS '倉庫',

    SUM(T0.InQty)                                    AS '累積入庫數量',
    SUM(T0.OutQty)                                   AS '累積出庫數量',
    SUM(T0.InQty - T0.OutQty)                        AS '截止庫存數量',

    SUM(
        CASE
            WHEN T0.InQty > 0 THEN T0.TransValue
            ELSE 0
        END
    )                                                AS '入庫異動金額',

    SUM(
        CASE
            WHEN T0.OutQty > 0 THEN -T0.TransValue
            ELSE 0
        END
    )                                                AS '出庫異動金額',

    SUM(T0.TransValue)                               AS '截止庫存金額'

FROM OINM T0

INNER JOIN OITM T1
    ON T0.ItemCode = T1.ItemCode

WHERE T0.DocDate <= @ToDate

GROUP BY
    T0.ItemCode,
    T0.Warehouse

HAVING
       SUM(T0.InQty - T0.OutQty) <> 0
    OR SUM(T0.TransValue) <> 0

ORDER BY
    T0.ItemCode,
    T0.Warehouse;
```

其中：

- 輸入 `2026/07/31`，條件就是 `DocDate <= 2026/07/31`
- 入庫異動金額：有入庫數量的交易金額
- 出庫異動金額：將 SAP 原本通常為負數的出庫金額轉為正數顯示
- 截止庫存金額：所有入庫與出庫異動金額加總後的庫存價值

如果你希望出庫異動金額維持 SAP 原始的負數，將：

```sql
THEN -T0.TransValue
```

改成：

```sql
THEN T0.TransValue
```

另外要注意，這一版是「截至指定日期的累積報表」，不是單獨統計 7 月份。輸入 7/31 時，會包含系統建帳以來到 7/31 為止的所有存貨異動。

---

<a id="q3"></a>

## S090-Q003｜我是fms

日期：2026-08-20

**你的提問**

我是fms

**當時回答**

了解，你指的是 SAP B1 的 FMS（格式化搜尋），那和「查詢產生器報表」不同。

FMS 通常只能把查詢結果回填到一個欄位，不能直接顯示「多料號、多倉庫」的整張存貨報表。因此：

- 若要拉完整存貨報表：應使用「查詢管理員／查詢產生器」。
- 若要在 FMS 畫面依某個過帳日期，自動帶出單一料號或單一倉庫的庫存數量、金額：可以寫，但要引用畫面欄位，例如 `$[$欄位UID.0.DATE]`。

例如單一料號截至畫面過帳日期的庫存數量：

```sql
SELECT SUM(T0.InQty - T0.OutQty)
FROM OINM T0
WHERE T0.ItemCode = $[$料號欄位UID.0.0]
  AND T0.DocDate <= $[$過帳日期欄位UID.0.DATE]
```

但我需要知道 FMS 掛在哪個 SAP B1 畫面，以及：

- 過帳日期欄位的 UID
- 料號欄位的 UID
- 要回填的是數量、入庫金額、出庫金額，還是庫存金額

如果你是在維熹 FMS 系統中執行「SAP B1 報表 SQL」，而不是 SAP 的「格式化搜尋」，也請告訴我；那我會把上一支改成 FMS 報表參數格式。

---

<a id="q4"></a>

## S090-Q004｜跟 剛剛第一個一樣 沒辦法用declare

日期：2026-08-20

**你的提問**

跟 剛剛第一個一樣 沒辦法用declare

**當時回答**

了解，沿用第一支 SAP B1 查詢格式，不使用 `DECLARE`，只保留一個截止過帳日期參數 `[%0]`。

```sql
/* [%0] 截止過帳日期 */

SELECT
    T0.ItemCode                              AS '料號',
    MAX(T1.ItemName)                         AS '品名',
    T0.Warehouse                             AS '倉庫',

    SUM(T0.InQty)                            AS '入庫數量',
    SUM(T0.OutQty)                           AS '出庫數量',
    SUM(T0.InQty - T0.OutQty)                AS '截止庫存數量',

    SUM(
        CASE
            WHEN T0.InQty > 0
            THEN T0.TransValue
            ELSE 0
        END
    )                                        AS '入庫異動金額',

    SUM(
        CASE
            WHEN T0.OutQty > 0
            THEN -T0.TransValue
            ELSE 0
        END
    )                                        AS '出庫異動金額',

    SUM(T0.TransValue)                       AS '截止庫存金額'

FROM OINM T0

INNER JOIN OITM T1
    ON T0.ItemCode = T1.ItemCode

WHERE T0.DocDate <= [%0]

GROUP BY
    T0.ItemCode,
    T0.Warehouse

HAVING
       SUM(T0.InQty - T0.OutQty) <> 0
    OR SUM(T0.TransValue) <> 0

ORDER BY
    T0.ItemCode,
    T0.Warehouse;
```

執行時只會要求輸入一個日期。例如輸入 `2026/07/31`，就會抓取：

```sql
T0.DocDate <= 2026/07/31
```

也就是包含 7/31 當天，以及 7/31 以前的所有存貨異動。

目前「出庫異動金額」以正數呈現；如果希望保留 SAP 原始負數，把：

```sql
THEN -T0.TransValue
```

改成：

```sql
THEN T0.TransValue
```

---

<a id="q5"></a>

## S090-Q005｜ORDER BY T0.ItemCode, T0.Warehouse; 這是甚麼意思

日期：2026-08-20

**你的提問**

ORDER BY
    T0.ItemCode,
    T0.Warehouse; 這是甚麼意思

**當時回答**

這段只是設定查詢結果的排列順序：

```sql
ORDER BY
    T0.ItemCode,
    T0.Warehouse;
```

意思是：

1. 先依「料號」`ItemCode` 排序。
2. 同一料號有多個倉庫時，再依「倉庫代碼」`Warehouse` 排序。

例如：

```text
料號    倉庫
A001    01
A001    02
A002    01
A002    03
```

它不會影響庫存數量或金額計算，只會影響顯示順序。

也可以改成用中文欄位名稱排序：

```sql
ORDER BY
    '料號',
    '倉庫';
```

但 SQL Server 可能把單引號內容視為固定文字，因此建議保留原本寫法。若不在意排序，整段 `ORDER BY` 可以直接刪除。

---

