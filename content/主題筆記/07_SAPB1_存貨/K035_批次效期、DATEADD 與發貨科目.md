---
title: "批次效期、DATEADD 與發貨科目"
category: "07_SAPB1_存貨"
updated: 2026-10-20
sources: ["S127","S208"]
---

# 批次效期、DATEADD 與發貨科目

[回總覽](../../00_閱讀入口.md)

## 整合摘要

保存七日內到期批次查詢、效期月份計算及 OIGE 特定序列／異動類型帶入科目的 FMS。效期來源與科目條件屬各自案例；SQL 與觸發欄位完整保留於來源問答。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S127｜查詢七日內到期批次](<../../原始問答/S127_查詢七日內到期批次.md>)：2026-09-02，2 段問答。
- [S208｜設定 OIGE 科目代碼](<../../原始問答/S208_設定 OIGE 科目代碼.md>)：2026-09-30，5 段問答。

## 問題索引

- [01｜SQL／程式碼調整與說明](#qa1)
- [02｜DATEADD 甚麼意思](#qa2)
- [03｜OIGE.Series =117的時候以及 OIGE.U_ChangType = F 的時候，IGE1.AcctCode 帶入 6483](#qa3)
- [04｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'OIGE.Seri…](#qa4)
- [05｜我綁訂了](#qa5)
- [06｜圖片／附件提問](#qa6)
- [07｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near '$30.0.0'.…](#qa7)

## 合併後歷史問答

<a id="qa1"></a>

### 1. SQL／程式碼調整與說明

來源：[S127-Q001](<../../原始問答/S127_查詢七日內到期批次.md#q1>)；2026-09-02

**問題**

SELECT\
    T0.ItemCode AS '項目號碼',\
    T0.DistNumber AS '批次',\
    T0.InDate AS '許可日期',\
    TRY\_CAST(T2.Name AS INT) AS '期限(月)',\
    DATEADD(\
        MONTH,\
        TRY\_CAST(T2.Name AS INT),\
        T0.InDate\
    ) AS '到期日'\
FROM OBTN T0\
INNER JOIN OITM T1\
    ON T0.ItemCode = T1.ItemCode\
INNER JOIN [@SERIESTYPE] T2\
    ON T1.U\_SeriesType = T2.Code\
INNER JOIN OBTQ T3\
    ON T0.AbsEntry = T3.MdAbsEntry\
    AND T0.ItemCode = T3.ItemCode\
WHERE T3.Quantity > 0\
  AND DATEDIFF(\
        DAY,\
        GETDATE(),\
        DATEADD(\
            MONTH,\
            TRY\_CAST(T2.Name AS INT),\
            T0.InDate\
        )\
      ) BETWEEN 0 AND 7\
ORDER BY\
    DATEADD(\
        MONTH,\
        TRY\_CAST(T2.Name AS INT),\
        T0.InDate\
    )

**歷史回答**

這段 SQL 會查出「目前仍有庫存，且在未來 7 天內到期」的批次資料。

您希望我協助哪一項：修正錯誤、優化效能、調整到期區間，還是改成 SAP Business One 查詢產生器可直接執行的格式？

<a id="qa2"></a>

### 2. DATEADD 甚麼意思

來源：[S127-Q002](<../../原始問答/S127_查詢七日內到期批次.md#q2>)；2026-09-02

**問題**

DATEADD   甚麼意思

**歷史回答**

`DATEADD` 是 SQL Server 的日期加減函數，用來在指定日期上增加或減少一段時間。

語法：

```sql
DATEADD(時間單位, 數量, 日期)
```

你這段 SQL：

```sql
DATEADD(
    MONTH,
    TRY_CAST(T2.Name AS INT),
    T0.InDate
)
```

意思是：

> 將 `T2.Name` 轉成整數（月數），加到 `T0.InDate`（許可日期），計算出到期日。

例如：

```sql
DATEADD(MONTH, 6, '2026-09-02')
```

結果：

```text
2027-03-02
```

也可以使用其他時間單位：

```sql
DATEADD(DAY, 7, GETDATE())     -- 今天加 7 天
DATEADD(MONTH, -1, GETDATE()) -- 今天減 1 個月
DATEADD(YEAR, 2, GETDATE())   -- 今天加 2 年
```

因此，你的 SQL 是用「許可日期＋期限（月）」來推算產品到期日。

<a id="qa3"></a>

### 3. OIGE.Series =117的時候以及 OIGE.U_ChangType = F 的時候，IGE1.AcctCode 帶入 6483

來源：[S208-Q001](<../../原始問答/S208_設定 OIGE 科目代碼.md#q1>)；2026-09-30

**問題**

OIGE.Series =117的時候以及 OIGE.U_ChangType = F 的時候，IGE1.AcctCode 帶入 6483

**歷史回答**

若是 **SAP B1 SQL Server 的 FMS**，將以下查詢綁定到 `IGE1.AcctCode`：

```sql
SELECT '6483'
WHERE $[OIGE.Series] = 117
  AND $[OIGE.U_ChangType] = 'F'
```

兩個條件**同時成立**時，回傳科目代碼 `6483`；不符合時不回傳資料。

`$[資料表.欄位]` 用來取得目前表單的欄位值。（[SAP FMS 語法參考](https://community.sap.com/t5/enterprise-resource-planning-q-a/formatted-search-not-working/qaq-p/10943451)）

<a id="qa4"></a>

### 4. 1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'OIGE.Seri…

來源：[S208-Q002](<../../原始問答/S208_設定 OIGE 科目代碼.md#q2>)；2026-09-30

**問題**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'OIGE.Series'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '使用者定義的值' (CSHS) (s) could not be prepared.

**歷史回答**

這個錯誤表示 SAP 可能沒有將 `$[OIGE.Series]` 轉換成表單上的值，就直接送到 SQL Server。前一版沒有說明執行位置，抱歉。

這段是 **FMS 表單取值語法**，需要綁定在「存貨發貨」的 `IGE1.AcctCode` 欄位，再於該欄位按 **Shift + F2** 執行；不能直接當成一般 SQL 在 SSMS 執行。

你是在「查詢管理員」直接測試，還是已經綁定到 `IGE1.AcctCode` 後出現錯誤？

如果已經綁定，請提供開啟「檢視 → 系統資訊」後，滑鼠移到 **Series、U_ChangType** 兩個欄位時，狀態列顯示的 **Item UID**，我就能改成直接讀取畫面欄位的 FMS 語法。

<a id="qa5"></a>

### 5. 我綁訂了

來源：[S208-Q003](<../../原始問答/S208_設定 OIGE 科目代碼.md#q3>)；2026-09-30

**問題**

我綁訂了

**歷史回答**

了解，已綁定後仍出錯，就需要確認畫面欄位的識別碼，改用 `$[$ItemUID.欄號.型別]` 取值。

請開啟 SAP 的 **「檢視 → 系統資訊」**，分別將滑鼠停在以下欄位，提供左下角狀態列的 **Item UID**（或截圖）：

- 單據編號系列（`Series`）
- 異動類型（`U_ChangType`）

有這兩個值，我就能給你可直接貼上的版本，條件維持 **Series = 117 且 U_ChangType = 'F' 時帶入 6483**。

<a id="qa6"></a>

### 6. 圖片／附件提問

來源：[S208-Q004](<../../原始問答/S208_設定 OIGE 科目代碼.md#q4>)；2026-09-30

**問題**

# Files mentioned by the user:

## codex-clipboard-2d9f9d23-3993-446f-8c69-3749baadc844.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2d9f9d23-3993-446f-8c69-3749baadc844.png
Image attachment: true

## codex-clipboard-2f0e180b-3c4d-419c-9f25-0e775880dd58.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2f0e180b-3c4d-419c-9f25-0e775880dd58.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**歷史回答**

截圖確認：系列的 Item UID 是 `30`，異動類型是 `U_ChangeType`，原先的 `U_ChangType` 少了 **e**。

請將綁定在 `IGE1.AcctCode` 的 FMS 改成：

```sql
SELECT '6483'
WHERE $[$30.0.0] = '117'
  AND $[$U_ChangeType.0.0] = 'F'
```

兩個條件同時成立時，帶入 `6483`。

<a id="qa7"></a>

### 7. 1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near '$30.0.0'.…

來源：[S208-Q005](<../../原始問答/S208_設定 OIGE 科目代碼.md#q5>)；2026-09-30

**問題**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near '$30.0.0'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '後端排程工作' (OBSJ) (s) could not be prepared.

**歷史回答**

這次錯誤顯示 `$[$30.0.0]` 仍未被 SAP 正確替換。先改回資料表欄位語法，並修正截圖確認的欄位名稱 **`U_ChangeType`**：

```sql
SELECT '6483'
WHERE $[OIGE.Series] = 117
  AND $[OIGE.U_ChangeType] = 'F'
```

請將查詢儲存後，在「存貨發貨」明細的**會計科目欄位**按 **Shift + F2** 測試。

如果仍報錯，請截圖「使用者定義的值」設定視窗，以及其中綁定的完整查詢。前兩次錯誤都涉及取值符號，需確認實際儲存及執行的內容，才能判斷原因。

