# 檢查 OITM.U_ItemSpec FMS

來源編號：S237  
聊天 ID：`01a12e05-6d32-7570-9bb2-bf8c292d6f73`  
聊天起始日期（台北）：2026-10-12  
保存位置：一般本機紀錄  
問答段落：5

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S237-Q001｜OITM.U_ItemSpec = ISNULL(OITM.U_WireType,'') + ' ' + ISNULL(OITM.U_WireSpec,'') + ' ' + IS…

日期：2026-10-12

**你的提問**

OITM.U_ItemSpec = ISNULL(OITM.U_WireType,'') + ' ' + ISNULL(OITM.U_WireSpec,'') + ' ' + ISNULL(OITM.U_CoreCount,'') + ' ' + ISNULL(OITM.U_TempRating,'') + ' ' +ISNULL(OITM.U_Color,'') + ' ' +ISNULL(OITM.U_Length,'') + 
--            CASE WHEN ISNULL(U_Length,'') <> '' THEN 'MM' ELSE '' END --CASE
--    FROM OITM
--	WHERE OITM.ItmsGrpCod = 101

幫我檢查這段 我是要在 OITM.U_ItemSpec  掛FMS

**當時回答**

可以，掛在 `OITM.U_ItemSpec` 的 FMS，應該用 **`SELECT` 回傳組合字串**，不用寫 `OITM.U_ItemSpec =`。

你原本這段有三個地方要調整：

- 缺少 `SELECT`，而且最後的 `+` 後面被註解掉，會造成語法錯誤。
- 要讀取畫面上目前輸入的值，欄位改用 `$[OITM.欄位名稱]`。
- 不需要 `FROM OITM`，否則可能讀到資料庫已儲存的值，而不是畫面上剛修改的內容。[SAP Community 語法說明](https://community.sap.com/t5/enterprise-resource-planning-q-a/query-purchase-order/qaa-p/11242898)

依你使用的 `ISNULL`，以下先以 **SQL Server** 為準，保留「長度有值才加 MM」：

```sql
SELECT
    LTRIM(RTRIM(
        ISNULL(CAST($[OITM.U_WireType] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_WireSpec] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_CoreCount] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_TempRating] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_Color] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_Length] AS NVARCHAR(254)), N'')
        +
        CASE
            WHEN ISNULL(
                CAST($[OITM.U_Length] AS NVARCHAR(254)), N''
            ) <> N''
            THEN N'MM'
            ELSE N''
        END
    ))
```

`CAST` 是避免芯數或長度為數值欄位時，`+` 被當作數值運算。這版會去除整段頭尾空白，但中間欄位空白時，仍會留下連續空格。

**如果只限物料群組 `101` 使用**，在上述查詢最後加上：

```sql
WHERE $[OITM.ItmsGrpCod] = 101
```

將查詢存好後，綁定到 `U_ItemSpec` 即可。實際結果仍需在你的 SAP B1 物料主檔畫面測試，並確認 `U_ItemSpec` 的欄位長度足夠。

---

<a id="q2"></a>

## S237-Q002｜要加上

日期：2026-10-12

**你的提問**

要加上

**當時回答**

已加上 **物料群組 `101`** 的條件，可用以下完整查詢掛在 `OITM.U_ItemSpec`：

```sql
SELECT
    LTRIM(RTRIM(
        ISNULL(CAST($[OITM.U_WireType] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_WireSpec] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_CoreCount] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_TempRating] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_Color] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_Length] AS NVARCHAR(254)), N'')
        +
        CASE
            WHEN ISNULL(
                CAST($[OITM.U_Length] AS NVARCHAR(254)), N''
            ) <> N''
            THEN N'MM'
            ELSE N''
        END
    ))
WHERE $[OITM.ItmsGrpCod] = 101
```

此查詢僅在群組為 `101` 時回傳組合規格，長度有值才加上 `MM`。

---

<a id="q3"></a>

## S237-Q003｜找不到值欸

日期：2026-10-12

**你的提問**

找不到值欸

**當時回答**

如果顯示「找不到值」，**先確認是不是 `WHERE` 的群組條件把結果排除了**。前一版沒有確認畫面上的群組值，這裡先拆開測試。

請在同一個物料主檔畫面，暫時把 FMS 查詢換成：

```sql
SELECT
    N'群組=['
    + ISNULL(CAST($[OITM.ItmsGrpCod] AS NVARCHAR(50)), N'NULL')
    + N'] 線種=['
    + ISNULL(CAST($[OITM.U_WireType] AS NVARCHAR(254)), N'NULL')
    + N']'
```

這段沒有 `WHERE`，正常應回傳像這樣的文字：

```text
群組=[101] 線種=[UL2464]
```

- **有結果，但群組不是 `101`**：原本的條件會排除這筆物料，要確認正確的群組代碼。
- **群組是 `101`，線種也正確**：表單取值正常，再檢查其他欄位。
- **仍然找不到值或出現錯誤**：需要確認 FMS 的綁定位置與表單欄位取值方式。

**你是在「物料主檔」的 `U_ItemSpec` 執行嗎？這段測試顯示什麼文字，還是有完整錯誤訊息？** 測試文字不要儲存進物料規格。

---

<a id="q4"></a>

## S237-Q004｜OITM.U_ProductForm = ISNULL(OITM.U_HeadType,'') + '' + ISNULL(OITM.U_TailType,'') -- FROM …

日期：2026-10-12

**你的提問**

OITM.U_ProductForm = ISNULL(OITM.U_HeadType,'') + '' + ISNULL(OITM.U_TailType,'')
--    FROM OITM
--	WHERE OITM.ItmsGrpCod = 101

另一段 需要修改

**當時回答**

這段掛在 **`OITM.U_ProductForm`**，將頭型與尾型直接串接、不加空格，並保留群組 `101` 的條件：

```sql
SELECT
    ISNULL(CAST($[OITM.U_HeadType] AS NVARCHAR(254)), N'')
    +
    ISNULL(CAST($[OITM.U_TailType] AS NVARCHAR(254)), N'')
WHERE $[OITM.ItmsGrpCod] = 101
```

如果同樣顯示「找不到值」，先移除最後一行 `WHERE` 測試，確認是否為群組條件造成。

---

<a id="q5"></a>

## S237-Q005｜她沒有加空格嗎?

日期：2026-10-12

**你的提問**

她沒有加空格嗎?

**當時回答**

對，原本的 `''` 是空字串，**不會加空格**。要在頭型與尾型之間加一個空格，改成 `N' '`：

```sql
SELECT
    LTRIM(RTRIM(
        ISNULL(CAST($[OITM.U_HeadType] AS NVARCHAR(254)), N'')
        + N' ' +
        ISNULL(CAST($[OITM.U_TailType] AS NVARCHAR(254)), N'')
    ))
WHERE $[OITM.ItmsGrpCod] = 101
```

`LTRIM(RTRIM(...))` 會去掉頭尾空白，避免其中一個欄位沒值時多出空格。

---

