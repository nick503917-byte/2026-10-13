# Locate U_CU_MRT 欄位

來源編號：S084  
聊天 ID：`01a01397-b04f-7a82-bd4a-3e4f187d1b81`  
聊天起始日期（台北）：2026-08-18  
保存位置：一般本機紀錄  
問答段落：7

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S084-Q001｜我想從 這個報表抓 欄位 U_CU_MRT

日期：2026-08-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-472bdfc3-c66a-4beb-9ce8-1e6c5f2d3ec6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-472bdfc3-c66a-4beb-9ce8-1e6c5f2d3ec6.png



## My request:
我想從 這個報表抓 欄位 U_CU_MRT 

[原對話含圖片，本文未嵌入圖片]

**當時回答**

從截圖下方狀態列可判斷，這個「大宗原物料市場均價」資料來源是 SAP B1 使用者自訂表：

```sql
[@RAWMATERIALS]
```

要抓 `U_CU_MRT` 欄位，可用：

```sql
SELECT T0.[U_CU_MRT]
FROM [@RAWMATERIALS] T0
WHERE T0.[Code] = N'指定的Code'
```

如果是在該 UDO 畫面中使用格式化搜尋，依目前畫面的 `Code` 取值：

```sql
SELECT T0.[U_CU_MRT]
FROM [@RAWMATERIALS] T0
WHERE T0.[Code] = $[@RAWMATERIALS.Code]
```

若是從銷售報價單明細的「項目號碼」比對 `Code`，可嘗試：

```sql
SELECT TOP 1 T0.[U_CU_MRT]
FROM [@RAWMATERIALS] T0
WHERE T0.[Code] = $[$38.1.0]
```

其中 `$[$38.1.0]` 是銷售報價單目前明細列的項目號碼。若 `Code` 並不是項目號碼，就需要改用實際關聯欄位。

---

<a id="q2"></a>

## S084-Q002｜我想知道 銷售報價的過帳日期 要等於 @RAWMATERIALS的 月份 因為我@RAWMATERIALS的Code 的各式 會是 202608 就是 2026年 8 月

日期：2026-08-18

**你的提問**

我想知道 銷售報價的過帳日期 要等於 @RAWMATERIALS的 月份

因為我@RAWMATERIALS的Code 的各式 會是 202608 就是 2026年 8 月

**當時回答**

可以直接把銷售報價單 `OQUT.DocDate`（過帳日期）轉成 `yyyyMM`，再與 `[@RAWMATERIALS].Code` 比對。

```sql
SELECT
    Q0.[DocNum],
    Q0.[DocDate],
    R0.[Code],
    R0.[U_CU_MRT]
FROM [OQUT] Q0
LEFT JOIN [@RAWMATERIALS] R0
    ON R0.[Code] = CONVERT(char(6), Q0.[DocDate], 112)
```

例如：

- `OQUT.DocDate = 2026-08-18`
- `CONVERT(char(6), DocDate, 112) = '202608'`
- 對應 `[@RAWMATERIALS].Code = '202608'`

如果是在銷售報價單上做「格式化搜尋」，依當張報價單的過帳日期抓 `U_CU_MRT`：

```sql
SELECT TOP 1 R0.[U_CU_MRT]
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] =
      LEFT(CONVERT(char(8), $[OQUT.DocDate], 112), 6)
```

若 SAP B1 對 `$[OQUT.DocDate]` 的日期格式解析失敗，可以改成依畫面欄位 UID 取值；但核心比對條件就是：

```sql
[@RAWMATERIALS].[Code]
=
CONVERT(char(6), [OQUT].[DocDate], 112)
```

---

<a id="q3"></a>

## S084-Q003｜我會因為 欄位的型態不同所以沒有資料嗎]

日期：2026-08-18

**你的提問**

我會因為 欄位的型態不同所以沒有資料嗎]

**當時回答**

有可能。主要要確認兩邊的型態：

- `OQUT.DocDate`：日期型態
- `[@RAWMATERIALS].Code`：通常是文字型態，例如 `202608`

所以不要直接寫：

```sql
R0.[Code] = Q0.[DocDate]
```

應先將日期轉成相同的六碼文字：

```sql
R0.[Code] = CONVERT(char(6), Q0.[DocDate], 112)
```

完整測試：

```sql
SELECT
    Q0.[DocDate],
    CONVERT(char(6), Q0.[DocDate], 112) AS [報價年月],
    R0.[Code],
    R0.[U_CU_MRT]
FROM [OQUT] Q0
LEFT JOIN [@RAWMATERIALS] R0
    ON LTRIM(RTRIM(R0.[Code]))
       = CONVERT(char(6), Q0.[DocDate], 112)
WHERE Q0.[DocNum] = 你的報價單號
```

若仍然沒有資料，單獨檢查兩邊實際值：

```sql
SELECT
    Q0.[DocNum],
    Q0.[DocDate],
    CONVERT(char(6), Q0.[DocDate], 112) AS [轉換後年月]
FROM [OQUT] Q0
WHERE Q0.[DocNum] = 你的報價單號;

SELECT
    R0.[Code],
    LEN(R0.[Code]) AS [字數],
    R0.[U_CU_MRT]
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code])) = '202608';
```

如果 `Code` 是數字型態，則改成數字比較：

```sql
TRY_CONVERT(int, R0.[Code])
=
YEAR(Q0.[DocDate]) * 100 + MONTH(Q0.[DocDate])
```

我比較建議統一用文字格式 `yyyyMM`。另外也要檢查 `Code` 是否有空白、全形數字，或實際儲存成 `202608.0`。

---

<a id="q4"></a>

## S084-Q004｜另外 我想問 我一個欄位是 金額 另一個是文字 我可以帶到金額的欄位裡面嗎

日期：2026-08-18

**你的提問**

另外 我想問  

我一個欄位是 金額 另一個是文字 我可以帶到金額的欄位裡面嗎

**當時回答**

可以，但文字內容必須是可轉換的數字，先轉成 SAP 金額欄位可接受的數值型態。

建議在 SQL Server 使用 `TRY_CONVERT`：

```sql
SELECT TRY_CONVERT(decimal(19, 6), R0.[U_CU_MRT])
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] = '202608'
```

如果文字無法轉成數字，`TRY_CONVERT` 會回傳 `NULL`，不會讓整個查詢報錯。若希望無法轉換時帶入 `0`：

```sql
SELECT ISNULL(
    TRY_CONVERT(decimal(19, 6), R0.[U_CU_MRT]),
    0
)
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] = '202608'
```

假設文字儲存成 `12,345.67`，要先移除千分位：

```sql
SELECT ISNULL(
    TRY_CONVERT(
        decimal(19, 6),
        REPLACE(R0.[U_CU_MRT], ',', '')
    ),
    0
)
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] = '202608'
```

如果包含幣別或單位，例如 `12,345.67 USD`，則不能直接轉換，需要先清除 `USD`：

```sql
SELECT ISNULL(
    TRY_CONVERT(
        decimal(19, 6),
        REPLACE(
            REPLACE(R0.[U_CU_MRT], 'USD', ''),
            ',',
            ''
        )
    ),
    0
)
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] = '202608'
```

結合報價單過帳月份，可寫成：

```sql
SELECT ISNULL(
    TRY_CONVERT(
        decimal(19, 6),
        REPLACE(R0.[U_CU_MRT], ',', '')
    ),
    0
)
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code]))
    = CONVERT(char(6), $[OQUT.DocDate], 112)
```

注意：如果目標金額欄位只允許固定小數位數，例如 2 位，則可以改用：

```sql
decimal(19, 2)
```

但若是單價、匯率或成本欄位，通常建議保留 6 位小數。

---

<a id="q5"></a>

## S084-Q005｜SQL／程式碼調整與說明

日期：2026-08-18

**你的提問**

SELECT TOP 1 R0.[U_CU_MRT]
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] =
      LEFT(CONVERT(char(8), $[OQUT.DocDate], 112), 6)

LEFT(CONVERT(char(8), $[OQUT.DocDate], 112), 6)這段應該是有問題，因為我把 [Code] 直接輸入數字 '202608'是有資料的

**當時回答**

對，問題很可能不是 `Code`，而是 `$[OQUT.DocDate]` 在「格式化搜尋」中沒有被正確解析成日期。

建議直接讀取銷售報價單畫面上的「過帳日期」欄位。SAP B1 行銷文件的過帳日期 Item UID 通常是 `10`：

```sql
SELECT TOP 1
    TRY_CONVERT(decimal(19, 6), R0.[U_CU_MRT])
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code])) =
      CONVERT(char(6), $[$10.0.DATE], 112)
```

如果只想先測試年月是否正確，可以建立測試查詢：

```sql
SELECT CONVERT(char(6), $[$10.0.DATE], 112)
```

當過帳日期是 `2026-08-18` 時，結果應該是：

```text
202608
```

也可以改用 `YEAR()` 和 `MONTH()`，避免 `LEFT()`：

```sql
SELECT TOP 1
    TRY_CONVERT(decimal(19, 6), R0.[U_CU_MRT])
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code])) =
      CONVERT(char(4), YEAR($[$10.0.DATE]))
      + RIGHT(
          '0' + CONVERT(varchar(2), MONTH($[$10.0.DATE])),
          2
        )
```

我會優先使用第一種：

```sql
CONVERT(char(6), $[$10.0.DATE], 112)
```

因為 `$[$10.0.DATE]` 明確告訴 SAP B1 要把畫面值當成日期，而 `$[OQUT.DocDate]` 在報價單尚未儲存、或日期顯示格式不同時，可能取不到或解析錯誤。

---

<a id="q6"></a>

## S084-Q006｜圖片／附件提問

日期：2026-08-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e69cb1dd-5c3b-4edb-ab25-a776dffa1f9b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e69cb1dd-5c3b-4edb-ab25-a776dffa1f9b.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個 `內部錯誤 (3006) [131-183]` 很可能是因為：格式化搜尋掛在右側的「表頭 UDF」，但你讀取的是主畫面的系統欄位「過帳日期」。

這種情況 Item UID 前通常要加負號，將：

```sql
$[$10.0.DATE]
```

改成：

```sql
$[$-10.0.DATE]
```

先測試：

```sql
SELECT CONVERT(char(6), $[$-10.0.DATE], 112)
```

過帳日期為 `2026-08-18` 時，應回傳：

```text
202608
```

完整查詢可改成：

```sql
SELECT TOP 1
    ISNULL(
        TRY_CONVERT(
            decimal(19, 6),
            REPLACE(R0.[U_CU_MRT], ',', '')
        ),
        0
    )
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code])) =
      CONVERT(char(6), $[$-10.0.DATE], 112)
```

如果 `U_CU_MRT` 本身已經是金額／數字型態，就不需要轉換：

```sql
SELECT TOP 1 R0.[U_CU_MRT]
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code])) =
      CONVERT(char(6), $[$-10.0.DATE], 112)
```

SAP Community 也有相同案例：當格式化搜尋掛在表頭 UDF，讀取主畫面的系統欄位時，需要使用負的 Item UID，例如 `$[$-4.0.0]`。[SAP Community 案例](https://community.sap.com/t5/enterprise-resource-planning-q-a/formatted-query-failure-on-user-defined-field/qaq-p/6790102)

所以這次最應該先嘗試的是 `$[$-10.0.DATE]`。

---

<a id="q7"></a>

## S084-Q007｜圖片／附件提問

日期：2026-08-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-62c64f77-4a32-4f39-b52b-d709b72dd78f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-62c64f77-4a32-4f39-b52b-d709b72dd78f.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

從截圖看，你目前仍然使用：

```sql
$[$10.0.DATE]
```

如果這個格式化搜尋是掛在右側的表頭 UDF，請改成負號 Item UID：

```sql
SELECT TOP 1 R0.[U_TEST]
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code])) =
      CONVERT(char(6), $[$-10.0.DATE], 112)
```

也就是關鍵差異：

```sql
-- 原本
$[$10.0.DATE]

-- 改成
$[$-10.0.DATE]
```

建議先只測日期轉換：

```sql
SELECT CONVERT(char(6), $[$-10.0.DATE], 112)
```

如果結果是 `202608`，再測：

```sql
SELECT TOP 1 R0.[U_TEST]
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] = '202608'
```

兩段都成功後，再合併成完整查詢。

若目標是「金額欄位」，而 `U_TEST` 是文字型態，使用：

```sql
SELECT TOP 1
    ISNULL(
        TRY_CONVERT(
            decimal(19,6),
            REPLACE(R0.[U_TEST], ',', '')
        ),
        0
    )
FROM [@RAWMATERIALS] R0
WHERE LTRIM(RTRIM(R0.[Code])) =
      CONVERT(char(6), $[$-10.0.DATE], 112)
```

如果 `$[$-10.0.DATE]` 仍出現 3006，下一步要確認格式化搜尋掛的是「表頭 UDF」還是「明細列 UDF」；負號只適用於從側邊 UDF 面板讀取主表單欄位的情況。

---

