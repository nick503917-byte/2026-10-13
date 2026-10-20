---
title: "未結採購單 DTW 更新與內碼對應"
category: "04_SAPB1_採購應付"
updated: 2026-10-20
sources: ["S213","S214"]
---

# 未結採購單 DTW 更新與內碼對應

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 OPOR／POR1 更新、DocNum 與 DocEntry 對應、欄名空白及 Tab 分隔。歷史檔案修正不等於已成功匯入；需依單據狀態、後續單據與 DTW 範本確認可更新欄位。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S213｜確認未結採購單更新方式](<../../原始問答/S213_確認未結採購單更新方式.md>)：2026-10-01，1 段問答。
- [S214｜修正 OPOR DocNum 查詢](<../../原始問答/S214_修正 OPOR DocNum 查詢.md>)：2026-10-01，23 段問答。

## 問題索引

- [01｜SAPB1 DTW 如果我想更新未結採購單的資料可以更新嗎 (OPOR、POR1)](#qa1)
- [02｜SQL／程式碼調整與說明](#qa2)
- [03｜圖片／附件提問](#qa3)
- [04｜這兩個我在測試更新資料 有問題](#qa4)
- [05｜圖片／附件提問](#qa5)
- [06｜圖片／附件提問](#qa6)
- [07｜圖片／附件提問](#qa7)
- [08｜圖片／附件提問](#qa8)
- [09｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keywor…](#qa9)
- [10｜圖片／附件提問](#qa10)
- [11｜如果我把itemcode去掉可以嗎](#qa11)
- [12｜幫我用剛剛的檔案 把itemcode及資料拿掉](#qa12)
- [13｜圖片／附件提問](#qa13)
- [14｜一樣](#qa14)
- [15｜圖片／附件提問](#qa15)
- [16｜圖片／附件提問](#qa16)
- [17｜圖片／附件提問](#qa17)
- [18｜我測試匯入可以 但正式匯入跑出這個](#qa18)
- [19｜圖片／附件提問](#qa19)
- [20｜我已經把sp拿掉了](#qa20)
- [21｜幫我看有沒有空格](#qa21)
- [22｜步會發生剛剛那個問題 對吧](#qa22)
- [23｜這個問題](#qa23)
- [24｜幫我排除這個問題](#qa24)

## 合併後歷史問答

<a id="qa1"></a>

### 1. SAPB1 DTW 如果我想更新未結採購單的資料可以更新嗎 (OPOR、POR1)

來源：[S213-Q001](<../../原始問答/S213_確認未結採購單更新方式.md#q1>)；2026-10-01

**問題**

SAPB1 DTW 
如果我想更新未結採購單的資料可以更新嗎 (OPOR、POR1)

**歷史回答**

**可以，SAP Business One 的未結採購單可以透過 DTW 更新表頭（OPOR）及明細（POR1），但不是所有欄位都能修改。** 是否允許更新，還要看欄位、明細狀態，以及是否已產生後續收貨或發票。

DTW 是透過採購單業務物件更新資料，並不是直接對 OPOR、POR1 執行 SQL UPDATE。

| 更新範圍 | 使用範本 | 定位方式 |
|---|---|---|
| 表頭 OPOR | Documents | `DocEntry`：採購單內部編號 |
| 明細 POR1 | Document_Lines | 對應表頭的鍵值＋`LineNum` |

操作時選擇 **Update Existing Data → Purchasing → Purchase Orders**，使用你目前 DTW 版本產生的範本，保留識別欄位與要更新的欄位。SAP 官方說明採購文件可透過 DTW 更新，明細列索引從 `0` 開始。[SAP 官方更新說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/25bde9551224405e84e69c760e272e89/4925db7db34d14cde10000000a42189b.html)

要特別注意：

- **使用 `DocEntry`，不要把畫面上的 `DocNum` 當成更新鍵值。**
- **表頭未結，不代表每一列都未結或每個欄位都能改。** 部分收貨的採購單，需要逐列確認。
- **明細的 `LineNum` 要核對。** 曾刪除明細的單據，DTW 列索引可能與資料庫 `POR1.LineNum` 不一致，匯入前應檢查 Target Data，避免更新錯列。[SAP 的 LineNum 說明](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/tips-for-linenum-of-dtw/ba-p/13355130)
- 建議先在測試公司用一張單驗證，再批次更新。

**你想更新哪些欄位？** 例如表頭備註、交貨日期、明細數量、單價或 UDF；另外，這批採購單是否已經部分收貨？提供欄位名稱後，就能進一步確認限制與範本填法。

<a id="qa2"></a>

### 2. SQL／程式碼調整與說明

來源：[S214-Q001](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q1>)；2026-10-01

**問題**

SELECT T1.[DocEntry] FROM OPOR T0 INNER JOIN POR1 T1 ON T0.[DocEntry] = T1.[DocEntry]
WHERE T0.[DocNum] IN '100000147,
,100000148
,100000149
,100000150
,100000151
,100000152
,100000153
,100000154
,100000155
,100000156
,100000157
,100000158
,100000159
,100000160
,100000161
,100000162
,100000163
,100000164
,100000165
,100000166
,100000167
,100000168
,100000169
,100000170
,100000171
,100000172
,100000173
,100000174
,100000175
,100000176
,100000177
,100000178
,100000179
1,00000180
,100000181
,100000182
,100000183
,100000184
,100000185
,100000186
,100000187
,100000188
,100000189
,100000190
,100000191
,100000192
,100000193
,100000194
,100000195
,100000196
,100000197
,100000198
,100000199
,100000200
,100000201
,100000203
,100000204
,100000205
,100000207
,100000208
,100000209
,100000210
,100000211
,100000212
,100000213
,100000214
,100000215
,100000216
,100000217
,100000218
,100000219
,100000220
,100000221
,100000222
,100000223
,100000224
,100000225
,100000226
,100000228
,100000229
,100000231
,100000232
,100000234
,100000235
,100000236
,100000237
,100000238
,100000239
,100000240
,100000241
,100000242
,100000243
,100000244
,100000246
,100000247
,100000248
,100000249
,100000250
,100000251
,100000252
,100000253
,100000254
,100000255
,100000256
,100000258
,100000259
,100000260
,100000261
,100000264
,100000265
,100000267
,100000269
,100000270
,100000271
,100000272
,100000275
,100000276
,100000278
,100000279
,100000280
,100000281
,100000283
,100000284
,100000285
,100000286
,100000287
,100000288)


這樣出錯

**歷史回答**

`IN` 後面要用括號 `(...)`，不能將整串數字用單引號包住。你的清單還有多餘的逗號，以及 `1,00000180`，以下先將它修正為 `100000180`。

可直接改成：

```sql
SELECT DISTINCT T1.[DocEntry]
FROM OPOR T0
INNER JOIN POR1 T1 ON T0.[DocEntry] = T1.[DocEntry]
WHERE T0.[DocNum] IN (
    100000147, 100000148, 100000149, 100000150,
    100000151, 100000152, 100000153, 100000154,
    100000155, 100000156, 100000157, 100000158,
    100000159, 100000160, 100000161, 100000162,
    100000163, 100000164, 100000165, 100000166,
    100000167, 100000168, 100000169, 100000170,
    100000171, 100000172, 100000173, 100000174,
    100000175, 100000176, 100000177, 100000178,
    100000179, 100000180, 100000181, 100000182,
    100000183, 100000184, 100000185, 100000186,
    100000187, 100000188, 100000189, 100000190,
    100000191, 100000192, 100000193, 100000194,
    100000195, 100000196, 100000197, 100000198,
    100000199, 100000200, 100000201, 100000203,
    100000204, 100000205, 100000207, 100000208,
    100000209, 100000210, 100000211, 100000212,
    100000213, 100000214, 100000215, 100000216,
    100000217, 100000218, 100000219, 100000220,
    100000221, 100000222, 100000223, 100000224,
    100000225, 100000226, 100000228, 100000229,
    100000231, 100000232, 100000234, 100000235,
    100000236, 100000237, 100000238, 100000239,
    100000240, 100000241, 100000242, 100000243,
    100000244, 100000246, 100000247, 100000248,
    100000249, 100000250, 100000251, 100000252,
    100000253, 100000254, 100000255, 100000256,
    100000258, 100000259, 100000260, 100000261,
    100000264, 100000265, 100000267, 100000269,
    100000270, 100000271, 100000272, 100000275,
    100000276, 100000278, 100000279, 100000280,
    100000281, 100000283, 100000284, 100000285,
    100000286, 100000287, 100000288
);
```

這裡加了 `DISTINCT`，讓同一個 `DocEntry` 只顯示一次；如果你需要保留每筆明細對應的結果，移除 `DISTINCT` 即可。

<a id="qa3"></a>

### 3. 圖片／附件提問

來源：[S214-Q002](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q2>)；2026-10-01

**問題**

# Files pasted by the user:

## "1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]The multi-part identi…": C:\Users\nick_yeh\.codex/attachments/1cb05319-db1c-4057-8c17-9a8843fdecf8/貼上的文字.txt

Pasted text contains the user's request.

## My request:

**歷史回答**

這次錯誤是 **SQL Server 找不到 `T0` 這個資料表別名**。上一版有宣告 `FROM OPOR T0`，因此單看錯誤訊息，還無法確定實際執行的 SQL 哪裡不同。

請將原本 SQL **整段替換**成下面這版，直接使用資料表名稱，避開 `T0`、`T1` 別名：

```sql
SELECT DISTINCT POR1.[DocEntry]
FROM OPOR
INNER JOIN POR1 ON OPOR.[DocEntry] = POR1.[DocEntry]
WHERE OPOR.[DocNum] IN (
    100000147, 100000148, 100000149, 100000150,
    100000151, 100000152, 100000153, 100000154,
    100000155, 100000156, 100000157, 100000158,
    100000159, 100000160, 100000161, 100000162,
    100000163, 100000164, 100000165, 100000166,
    100000167, 100000168, 100000169, 100000170,
    100000171, 100000172, 100000173, 100000174,
    100000175, 100000176, 100000177, 100000178,
    100000179, 100000180, 100000181, 100000182,
    100000183, 100000184, 100000185, 100000186,
    100000187, 100000188, 100000189, 100000190,
    100000191, 100000192, 100000193, 100000194,
    100000195, 100000196, 100000197, 100000198,
    100000199, 100000200, 100000201, 100000203,
    100000204, 100000205, 100000207, 100000208,
    100000209, 100000210, 100000211, 100000212,
    100000213, 100000214, 100000215, 100000216,
    100000217, 100000218, 100000219, 100000220,
    100000221, 100000222, 100000223, 100000224,
    100000225, 100000226, 100000228, 100000229,
    100000231, 100000232, 100000234, 100000235,
    100000236, 100000237, 100000238, 100000239,
    100000240, 100000241, 100000242, 100000243,
    100000244, 100000246, 100000247, 100000248,
    100000249, 100000250, 100000251, 100000252,
    100000253, 100000254, 100000255, 100000256,
    100000258, 100000259, 100000260, 100000261,
    100000264, 100000265, 100000267, 100000269,
    100000270, 100000271, 100000272, 100000275,
    100000276, 100000278, 100000279, 100000280,
    100000281, 100000283, 100000284, 100000285,
    100000286, 100000287, 100000288
);
```

如果仍然出錯，請貼上**目前實際執行的完整 SQL**，並告訴我是在「查詢產生器」還是「查詢管理員」執行，才能確認是否有其他內容影響查詢。

<a id="qa4"></a>

### 4. 這兩個我在測試更新資料 有問題

來源：[S214-Q003](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q3>)；2026-10-01

**問題**

# Files mentioned by the user:

## OPOR.txt: C:/Users/nick_yeh/Desktop/OPOR.txt

## POR1.txt: C:/Users/nick_yeh/Desktop/POR1.txt



## My request:
這兩個我在測試更新資料 有問題

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa5"></a>

### 5. 圖片／附件提問

來源：[S214-Q004](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q4>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-eb68a2a4-b23d-4f4e-a915-253311bd2efe.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-eb68a2a4-b23d-4f4e-a915-253311bd2efe.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這個錯誤是 **欄位名稱含有空白字元（0x20）**。你的 `POR1.txt` 前兩列中，` Quantity `、` Price ` 前後都有空白，需改成 `Quantity`、`Price`。

已另存修正版，原始檔未修改：

- [下載 OPOR.txt](outputs/OPOR.txt)
- [下載 POR1.txt](outputs/POR1.txt)

請重新載入修正版檔案。

**另外，目前 POR1 沒有 `LineNum` 欄位**。更新既有明細還需要指定要更新哪一列；`DocEntry = 148` 只能識別單據。這次修正只處理欄名空白，尚未補入明細行號。

<a id="qa6"></a>

### 6. 圖片／附件提問

來源：[S214-Q005](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q5>)；2026-10-01

**問題**

# Files mentioned by the user:

## POR1.txt: C:/Users/nick_yeh/Desktop/POR1.txt



## My request:

**歷史回答**

最新檔案已填入 `LineNum = 0`，但前兩列的 ` Quantity `、` Price ` **仍有前後空白**，會造成截圖中的錯誤。

已移除欄名空白，請用這份重新載入 DTW：

[下載修正版 POR1.txt](C:/Users/nick_yeh/Documents/Codex/2026-10-01/select-t1-docentry-from-opor-t0/outputs/POR1.txt)

`LineNum = 0` 指向第一筆明細，請確認該筆品號確實是 `15364`。目前只確認檔案格式修正，尚未驗證 DTW 更新結果。

<a id="qa7"></a>

### 7. 圖片／附件提問

來源：[S214-Q006](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q6>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-9b1d3876-706f-4922-a42c-93c8e19c4ff9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9b1d3876-706f-4922-a42c-93c8e19c4ff9.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

畫面顯示 **`Item number is missing [POR1.ItemCode][line: 2]`**，表示送進 SAP 的明細有一行缺少品號。**這不一定是 TXT 的第 2 列，也不能據此把 `LineNum` 改成 2。**

你剛才的檔案已有 `ItemCode = 15364`，所以接下來要檢查 **DTW 欄位對應與 Target Data（目標資料）**：

- `LineNum` 是否對應到目標 `LineNum`，值為 `0`。
- `ItemCode` 是否對應到目標 `ItemCode`，值為 `15364`。
- 是否出現額外的空白明細。
- 匯入模式是否選擇「更新既有資料」。

SAP 官方也建議用 Target Data 核對明細行號；DTW 行號從 `0` 開始，若原單據刪除過明細，可能與資料庫的 `LineNum` 不同。[SAP 說明](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/tips-for-linenum-of-dtw/ba-p/13355130)

請先執行這段唯讀查詢，確認這張單的實際明細：

```sql
SELECT
    OPOR.DocNum,
    POR1.DocEntry,
    ROW_NUMBER() OVER (
        PARTITION BY POR1.DocEntry
        ORDER BY POR1.LineNum
    ) - 1 AS DTW_LineNum,
    POR1.LineNum AS DB_LineNum,
    POR1.ItemCode,
    POR1.Quantity,
    POR1.Price
FROM OPOR
INNER JOIN POR1 ON OPOR.DocEntry = POR1.DocEntry
WHERE OPOR.DocNum = 100000147
ORDER BY POR1.LineNum;
```

**請貼上查詢結果，以及 DTW 的 `Document_Lines` 欄位對應／Target Data 畫面**，就能進一步判斷是行號、欄位映射，還是多出空白明細。

<a id="qa8"></a>

### 8. 圖片／附件提問

來源：[S214-Q007](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q7>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-797685b3-30fb-4df0-a4ee-dcc2a41bc834.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-797685b3-30fb-4df0-a4ee-dcc2a41bc834.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

截圖顯示貼上後，換行消失，導致 SQL 黏在一起：`POR1.PriceFROM`、`OPORINNER`、`DocEntryWHERE`、`100000147ORDER`。

請清空查詢內容，改貼這段**單行 SQL**：

```sql
SELECT OPOR.DocNum, POR1.DocEntry, ROW_NUMBER() OVER (PARTITION BY POR1.DocEntry ORDER BY POR1.LineNum) - 1 AS DTW_LineNum, POR1.LineNum AS DB_LineNum, POR1.ItemCode, POR1.Quantity, POR1.Price FROM OPOR INNER JOIN POR1 ON OPOR.DocEntry = POR1.DocEntry WHERE OPOR.DocNum = 100000147 ORDER BY POR1.LineNum;
```

執行後請貼上結果，就能確認品號 `15364` 應使用哪個 `DTW_LineNum`。

<a id="qa9"></a>

### 9. 1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keywor…

來源：[S214-Q008](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q8>)；2026-10-01

**問題**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keyword 'FOR'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '後端排程工作' (OBSJ) (s) could not be prepared.

**歷史回答**

這段查詢本身沒有 `FOR`，可能是查詢工具附加語句時，與結尾的分號衝突；目前還不能確定。

先用下面這段**不含分號的單行查詢**，確認單據與明細資料：

```sql
SELECT OPOR.DocNum, POR1.DocEntry, POR1.LineNum, POR1.ItemCode, POR1.Quantity, POR1.Price FROM OPOR INNER JOIN POR1 ON OPOR.DocEntry = POR1.DocEntry WHERE OPOR.DocNum = 100000147 ORDER BY POR1.LineNum
```

請將原內容全部清空後再貼上，執行後把結果傳給我。這裡查出的 `LineNum` 是資料庫行號，還需要確認後才能填入 DTW。

<a id="qa10"></a>

### 10. 圖片／附件提問

來源：[S214-Q009](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q9>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-341283be-4696-417c-a480-f2fb1e3db981.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-341283be-4696-417c-a480-f2fb1e3db981.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

查詢已成功，確認這張單目前只有一筆明細：

| 欄位 | 值 |
|---|---:|
| DocNum | 100000147 |
| DocEntry | 148 |
| ItemCode | 15364 |
| 目前數量 | 1698 |
| 目前價格 | 35 |
| DTW_LineNum | 0（依查詢公式，唯一一筆結果為 0） |

你的匯入資料是要將數量改成 **1560**、價格維持 **35**，因此 `LineNum = 0` 的方向正確。

現在需要確認 **DTW 實際讀入的資料與欄位對應**。先前「第 2 行缺少品號」的錯誤，單靠這張查詢結果還無法判定原因。

請貼上 DTW 中 **`Document_Lines` 的欄位對應與 Target Data（目標資料）畫面**，尤其是 `LineNum`、`ItemCode`、`Quantity`、`Price`。應看到同一筆資料為 `0 / 15364 / 1560 / 35`；若多出空白明細，也請一起截入。

<a id="qa11"></a>

### 11. 如果我把itemcode去掉可以嗎

來源：[S214-Q010](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q10>)；2026-10-01

**問題**

如果我把itemcode去掉可以嗎

**歷史回答**

**可以，如果只是更新既有明細的數量／價格，通常可以不提供 `ItemCode`**，由單據識別鍵與 `LineNum` 指定要更新的那一行。

請把 `ItemCode` **整欄刪除**（包含前兩列欄名與資料列的 `15364`），不要只清空品號值。`LineNum` 保留 `0`。

但如果 DTW 因欄位對應問題，把資料當成「新增明細」，仍會出現缺少品號的錯誤。所以移除 `ItemCode` 後，先在 **Target Data** 確認是更新原本第 0 行，數量為 `1560`、價格為 `35`，再進行測試。

<a id="qa12"></a>

### 12. 幫我用剛剛的檔案 把itemcode及資料拿掉

來源：[S214-Q011](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q11>)；2026-10-01

**問題**

幫我用剛剛的檔案 把itemcode及資料拿掉

**歷史回答**

已用剛剛另存的修正版，移除 `ItemCode` 整欄及品號資料，保留其他欄位。

[下載 POR1_without_ItemCode.txt](C:/Users/nick_yeh/Documents/Codex/2026-10-01/select-t1-docentry-from-opor-t0/outputs/POR1_without_ItemCode.txt)

<a id="qa13"></a>

### 13. 圖片／附件提問

來源：[S214-Q012](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q12>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-701b740c-5312-40a2-a41c-bfefa859fe73.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-701b740c-5312-40a2-a41c-bfefa859fe73.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

移除 `ItemCode` 後仍是相同錯誤，表示**刪除品號欄沒有解決問題**。我前面建議先移除欄位，沒有先確認 DTW 對應，這一步不夠周全。

目前已確認原單只有一筆明細，品號是 `15364`。錯誤卻提到 `[line: 2]`，**可能是 DTW 未正確識別既有明細，或產生了額外明細**；還需要看匯入畫面才能確定。

請回到 DTW 的 **Map Object Fields（欄位對應）**，選擇 **Document_Lines**，截圖完整對應表；若能查看 **Target Data（目標資料）**，也請一併貼上。

重點是確認 `LineNum` 有對應到目標 `LineNum`，而且讀入的值確實為 `0`。先不要把它改成 `1` 或 `2`。

<a id="qa14"></a>

### 14. 一樣

來源：[S214-Q013](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q13>)；2026-10-01

**問題**

一樣

**歷史回答**

那就先不要再改 `ItemCode` 或重跑了。**目前確認檔案裡有 `LineNum = 0`，但還沒確認 DTW 是否真的把它讀成更新行號。** 我前面只修改檔案，沒有確認這一點，未能解決問題。

請回到 DTW 上一步的 **「Map Object Fields／欄位對應」**，點選 **`Document_Lines`**，把整個畫面截給我，需包含左右兩邊的來源欄位與目標欄位。

這次需要的是**欄位對應畫面，不是最後的錯誤清單**。相同錯誤無法分辨是 `LineNum` 沒有對應、主明細關聯錯誤，還是多讀了一筆空白明細；看到對應內容後才能具體修正。

<a id="qa15"></a>

### 15. 圖片／附件提問

來源：[S214-Q014](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q14>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-f38bc986-dcfb-4b35-8e86-d6e92991f272.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f38bc986-dcfb-4b35-8e86-d6e92991f272.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張是 **Source Data（來源資料）**，確認 DTW 已正確讀到：

- `ParentKey = 100000147`
- `DocEntry = 148`
- `LineNum = 0`
- `Quantity = 1560`
- `Price = 35`

因此來源檔的 `LineNum` 並沒有漏讀。

**請保持左側選取 `Document_Lines`，點上方的「Mapping Rules」並截圖。** 要確認來源 `LineNum` 是否真的對應到目標 `LineNum`，以及 `DocEntry` 被對應到哪個欄位。再點「Target Data」截一張，就能比較實際送出的資料是否與來源一致。

<a id="qa16"></a>

### 16. 圖片／附件提問

來源：[S214-Q015](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q15>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-c8cf572e-60e5-4011-a2fd-05ba236c444b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c8cf572e-60e5-4011-a2fd-05ba236c444b.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張仍是 **Source Data（來源資料）**，這次已包含 `ItemCode = 15364`，且 `LineNum = 0`。

下一步請點上方**最左邊的「Mapping Rules」**（在 Source Data 左邊），左側保持選取 `Document_Lines`，再截圖給我。

我們需要看「來源欄位 → 目標欄位」的對應；目前這張只顯示檔案內容，還無法確認 DTW 把資料送到哪些欄位。

<a id="qa17"></a>

### 17. 圖片／附件提問

來源：[S214-Q016](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q16>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-a0cbbee4-64fc-4dd3-8842-19b431c8ad1a.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a0cbbee4-64fc-4dd3-8842-19b431c8ad1a.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張確認了：**`LineNum → LineNum`、`ItemCode → ItemCode` 都有正確對應**，所以不能再歸因於這兩個欄位未映射。

值得檢查的是明細中的 **`DocEntry → DocEntry`**。我們先前在 `POR1` 額外放了這欄，現在應先排除它對更新的影響：

1. 在這個畫面將 **`DocEntry` 那列的 Target Fields 清空／設為不對應**。
2. 保留 `ParentKey`、`LineNum`、`ItemCode`、`Quantity`、`Price` 原有對應。
3. 點 **Target Data**，確認只有一筆明細，值為 `LineNum = 0`、`ItemCode = 15364`、`Quantity = 1560`、`Price = 35`。

**主檔 `Documents` 的 `DocEntry = 148` 仍須保留。** 這是一次排除測試，尚不能保證就是根因；請先用 DTW 模擬執行確認結果。

<a id="qa18"></a>

### 18. 我測試匯入可以 但正式匯入跑出這個

來源：[S214-Q017](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q17>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-30c42e48-13cb-4a26-b1c4-cb704f22e7e4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-30c42e48-13cb-4a26-b1c4-cb704f22e7e4.png
Image attachment: true



## My request:
我測試匯入可以 但正式匯入跑出這個

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這次是**另一個錯誤**，畫面顯示：

> `(2008) 類別名稱與該單據系列別設定不符，請重新確認。`

失敗的單據鍵值是 **148**。目前訊息指向「類別／單據系列」檢查，已不是先前的品號缺漏。

**這段文字看起來可能是你們公司的自訂驗證訊息**，但右側被截斷，尚不能確認來源。測試成功、正式匯入失敗，也要先區分：
- 若是 **DTW 模擬成功、實際執行失敗**：需查看完整錯誤及實際更新時的驗證。
- 若是 **測試資料庫成功、正式資料庫失敗**：需比較兩個資料庫的單據資料、系列設定及自訂驗證。

請先**拉寬 Description 欄位，或從 Log 複製完整錯誤文字**，尤其是後面 `Tra...` 的部分。並告訴我，你說的「測試匯入」是 **DTW 模擬**，還是 **另一個測試資料庫**？

先保留目前的欄位對應，不要為了通過匯入而直接修改單據系列。

<a id="qa19"></a>

### 19. 圖片／附件提問

來源：[S214-Q018](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q18>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-a12de0a3-9176-4536-b479-a8e2c039ad36.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a12de0a3-9176-4536-b479-a8e2c039ad36.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

完整訊息確認：**單據 `DocEntry = 148` 在提交更新時，被「類別名稱與單據系列別不符」的檢查擋下，因此這筆更新沒有成功儲存。**

`Transaction commit unsuccessfully` 是提交失敗的結果；真正要處理的是前面的 **`(2008) 類別名稱與該單據系列別設定不符`**。

這很可能是公司自訂的驗證規則。即使只改數量，更新時仍可能重新檢查整張單據。接下來應請 SAP 管理員／顧問確認：

- 採購單 `100000147`（內部 ID `148`）的「類別名稱」與「系列別」設定。
- `SBO_SP_TransactionNotification` 或其呼叫的程序中，是否有錯誤碼 `2008` 或這段中文訊息。

你可以直接把這段交給顧問：

> DTW 更新採購單，DocEntry＝148、DocNum＝100000147，將明細第 0 行數量由 1698 改成 1560，價格維持 35。提交時回傳「(2008) 類別名稱與該單據系列別設定不符，請重新確認」。請協助查明觸發的驗證規則及不符的欄位值。

**目前不用再刪改 `ItemCode` 或 `LineNum`；先查清楚這條驗證規則。**

<a id="qa20"></a>

### 20. 我已經把sp拿掉了

來源：[S214-Q019](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q19>)；2026-10-01

**問題**

我已經把sp拿掉了

**歷史回答**

你說的 SP 是 **`SBO_SP_TransactionNotification`** 嗎？「拿掉」是刪除整支程序，還是只移除裡面的自訂檢查？

**如果修改後重新匯入，仍出現完全相同的 `(2008)` 中文訊息**，就需要確認 DTW 連線的資料庫，以及訊息是否還存在其他程序。先在 **DTW 實際連線的公司資料庫**執行以下唯讀查詢：

```sql
SELECT @@SERVERNAME AS ServerName, DB_NAME() AS DatabaseName
```

再搜尋訊息來源：

```sql
SELECT SCHEMA_NAME(o.schema_id) AS SchemaName, o.name AS ObjectName, o.type_desc FROM sys.sql_modules m INNER JOIN sys.objects o ON m.object_id = o.object_id WHERE m.definition LIKE N'%類別名稱%' OR m.definition LIKE N'%系列別設定不符%'
```

請貼上查詢結果，並確認 **移除 SP 後是否已重新匯入，新的錯誤是否完全一樣**。搜尋沒有結果也不代表一定沒有驗證，還可能受檢視權限或程序加密影響。

<a id="qa21"></a>

### 21. 幫我看有沒有空格

來源：[S214-Q020](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q20>)；2026-10-01

**問題**

# Files mentioned by the user:

## OPOR.txt: C:/Users/nick_yeh/Documents/Codex/2026-10-01/select-t1-docentry-from-opor-t0/outputs/OPOR.txt

## POR1.txt: C:/Users/nick_yeh/Documents/Codex/2026-10-01/select-t1-docentry-from-opor-t0/outputs/POR1.txt



## My request:
幫我看有沒有空格

**歷史回答**

有，檢查結果：

- **OPOR.txt**：沒有多餘空格。
- **POR1.txt**：第 1、2 列的 ` Quantity `、` Price ` 前後都有空格，共 **8 個半形空格（0x20）**，應改成 `Quantity`、`Price`。
- 兩份檔案的資料列都沒有多餘空格；欄位間的 **Tab 是正常分隔符號，應保留**。

目前僅檢查，尚未修改檔案。

<a id="qa22"></a>

### 22. 步會發生剛剛那個問題 對吧

來源：[S214-Q021](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q21>)；2026-10-01

**問題**

步會發生剛剛那個問題 對吧

**歷史回答**

如果你是問「不會再發生剛剛的問題，對吧？」——**目前 POR1 的欄名仍有空格，所以仍可能出現 `0x20 無法包含在名稱中`，要先移除。**

至於 **`(2008) 類別名稱與單據系列別不符`**，那是另一個驗證問題，與空格無關，不能靠移除空格保證解決。

<a id="qa23"></a>

### 23. 這個問題

來源：[S214-Q022](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q22>)；2026-10-01

**問題**

# Files mentioned by the user:

## codex-clipboard-6003688f-cdf9-4399-9a57-9ba29ebf8dbd.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6003688f-cdf9-4399-9a57-9ba29ebf8dbd.png
Image attachment: true



## My request:
這個問題

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，就是這個 **`0x20` 空格錯誤**。你目前提供的 `POR1.txt` 前兩列仍有 ` Quantity `、` Price `，所以**還會觸發這個問題**。

將前後空格移除，改成 `Quantity`、`Price`，保留欄位間的 Tab，再重新載入修正版，就能排除這個欄名空格問題。

<a id="qa24"></a>

### 24. 幫我排除這個問題

來源：[S214-Q023](<../../原始問答/S214_修正 OPOR DocNum 查詢.md#q23>)；2026-10-01

**問題**

幫我排除這個問題

**歷史回答**

已修正 **POR1.txt** 前兩列的 8 個多餘空格，並確認：

- 兩份檔案都沒有多餘空格。
- 126 筆資料及 Tab 分隔保持不變。

[下載修正後的 POR1.txt](C:/Users/nick_yeh/Documents/Codex/2026-10-01/select-t1-docentry-from-opor-t0/outputs/POR1.txt)

請在 DTW **重新選取並載入此檔案**，排除這次欄名造成的 `0x20` 錯誤。

