# 修正RAWITEMCREA轉換錯誤

來源編號：S079  
聊天 ID：`019ffdde-1f08-7280-bc06-7f1e5329515e`  
聊天起始日期（台北）：2026-08-14  
保存位置：封存紀錄  
問答段落：6

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S079-Q001｜SQL／程式碼調整與說明

日期：2026-08-14

**你的提問**

--IF @object_type = N'RAWITEMCREA'
--   AND @transaction_type IN (N'A', N'U')
--BEGIN
--    IF EXISTS (
--        SELECT 1
--        FROM dbo.[@RAWITEMCREA1] AS T1
--        WHERE T1.Code =
--              TRY_CONVERT(INT, @list_of_cols_val_tab_del)
--          AND (
--                 T1.U_ItemCode IS NULL
--              OR LTRIM(RTRIM(T1.U_ItemCode)) = N''
--              OR T1.U_ItemCode COLLATE Latin1_General_100_BIN2
--                 LIKE N'%[^一-龥A-Za-z0-9]%'
--          )
--    )
--    BEGIN
--        SET @error = -1;
--        SET @error_message =
--            N'U_ItemCode 只能輸入中文、英文及數字';
--    END;
--END;


這隻拿掉後 就沒有這個error了

1 Created Failed AA01  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion failed when converting the nvarchar value 'EXA2' to data type int. (CINF)UDO_RAWITEMCREA

**當時回答**

錯誤原因就在這段：

```sql
T1.Code = TRY_CONVERT(INT, @list_of_cols_val_tab_del)
```

`T1.Code` 是 `nvarchar`，資料可能是 `EXA2`；右邊卻轉成 `INT`。SQL Server 比較時因為 `INT` 的型別優先序較高，會嘗試把 `T1.Code` 的所有候選值轉成 `INT`，於是 `EXA2` 轉換失敗。

直接用字串比較即可：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.Code = LTRIM(RTRIM(@list_of_cols_val_tab_del))
          AND (
                 T1.U_ItemCode IS NULL
              OR LTRIM(RTRIM(T1.U_ItemCode)) = N''
              OR T1.U_ItemCode COLLATE Latin1_General_100_BIN2
                   LIKE N'%[^一-龥A-Za-z0-9]%'
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
    END;
END;
```

重點是把：

```sql
TRY_CONVERT(INT, @list_of_cols_val_tab_del)
```

拿掉。SAP Business One UDO 的 `Code` 欄位通常是字串型別，即使某些 Code 看起來是數字，也不應轉成 `INT`。像 `EXA2` 這種 Code 必須以 `nvarchar` 比較。

---

<a id="q2"></a>

## S079-Q002｜ok

日期：2026-08-14

**你的提問**

ok

**當時回答**

好的，改成字串比較即可解決 `EXA2` 轉換 `INT` 的錯誤。

---

<a id="q3"></a>

## S079-Q003｜我有特殊字元 但沒有卡到

日期：2026-08-17

**你的提問**

我有特殊字元 但沒有卡到

**當時回答**

可能不是特殊字元判斷本身，而是 `WHERE T1.Code = ...` 沒有抓到該筆資料，所以整個 `EXISTS` 為 `FALSE`。

先用實際 Code 測試：

```sql
SELECT
    T1.Code,
    T1.U_ItemCode,
    PATINDEX(
        N'%[^一-龥A-Za-z0-9]%',
        T1.U_ItemCode COLLATE Latin1_General_100_BIN2
    ) AS InvalidPosition
FROM dbo.[@RAWITEMCREA1] AS T1
WHERE T1.Code = N'EXA2';
```

若 `InvalidPosition > 0`，代表特殊字元有被辨識。

建議卡控改成以下寫法，並先將輸入值清除前後空白：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.Code =
              CONVERT(NVARCHAR(254), @list_of_cols_val_tab_del)
          AND (
                 NULLIF(LTRIM(RTRIM(T1.U_ItemCode)), N'') IS NULL
              OR PATINDEX(
                     N'%[^一-龥A-Za-z0-9]%',
                     T1.U_ItemCode COLLATE Latin1_General_100_BIN2
                 ) > 0
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
    END;
END;
```

也可在 Transaction Notification 裡暫時加入下面訊息，確認 SAP 傳進來的主鍵到底是什麼：

```sql
SET @error = -1;
SET @error_message =
    N'Key=[' + ISNULL(@list_of_cols_val_tab_del, N'NULL') + N']';
```

如果傳入值包含 Tab，例如 `EXA2<TAB>其他值`，直接與 `T1.Code = @list_of_cols_val_tab_del` 比較就不會命中，需要先取第一段：

```sql
DECLARE @Code NVARCHAR(254);

SET @Code =
    LEFT(
        @list_of_cols_val_tab_del,
        CHARINDEX(
            CHAR(9),
            @list_of_cols_val_tab_del + CHAR(9)
        ) - 1
    );
```

然後條件改成：

```sql
WHERE T1.Code = @Code
```

另外，如果你輸入的「特殊字元」是空白，建議明確檢查，因為尾端空白在 SQL Server 某些字串比較情況下可能被忽略：

```sql
OR T1.U_ItemCode LIKE N'% %'
OR T1.U_ItemCode LIKE N'%' + NCHAR(12288) + N'%' -- 全形空白
```

最關鍵的是先看 `InvalidPosition` 結果，以及 `@list_of_cols_val_tab_del` 的實際內容。

---

<a id="q4"></a>

## S079-Q004｜我發現 上一個你給邏輯是 T1.U_ItemCode 但我是卡 code

日期：2026-08-17

**你的提問**

我發現 上一個你給邏輯是 T1.U_ItemCode 但我是卡 code

**當時回答**

對，問題就在這裡：先前程式是在檢查 `T1.U_ItemCode`，但你實際要卡控的是 `T1.Code`。

請改成：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.Code =
              LTRIM(RTRIM(@list_of_cols_val_tab_del))
          AND (
                 T1.Code IS NULL
              OR LTRIM(RTRIM(T1.Code)) = N''
              OR PATINDEX(
                     N'%[^一-龥A-Za-z0-9]%',
                     T1.Code COLLATE Latin1_General_100_BIN2
                 ) > 0
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message = N'Code 只能輸入中文、英文及數字';
    END;
END;
```

但要注意：`[@RAWITEMCREA1]` 看起來像 UDO 的明細表，其中 `Code` 通常是用來連接表頭的系統欄位，不一定是使用者畫面上輸入的料號。

如果你要檢查的是表頭 `[@RAWITEMCREA]` 的 `Code`，應改成：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA] AS T0
        WHERE T0.Code =
              LTRIM(RTRIM(@list_of_cols_val_tab_del))
          AND (
                 T0.Code IS NULL
              OR LTRIM(RTRIM(T0.Code)) = N''
              OR PATINDEX(
                     N'%[^一-龥A-Za-z0-9]%',
                     T0.Code COLLATE Latin1_General_100_BIN2
                 ) > 0
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message = N'Code 只能輸入中文、英文及數字';
    END;
END;
```

如果畫面上的欄位其實是使用者自訂欄位，例如 `U_Code` 或 `U_ItemCode`，則判斷欄位要換成該 `U_` 欄位；`WHERE T0.Code = @list_of_cols_val_tab_del` 仍然只負責定位目前這張單據。

---

<a id="q5"></a>

## S079-Q005｜〔程式碼〕

日期：2026-08-17

**你的提問**

```
COLLATE  甚麼意思
```

**當時回答**

`COLLATE` 是指定 SQL Server 在比較字串時要採用的「定序規則」，它會影響：

- 大小寫是否視為相同
- 中文及英文如何排序
- 重音符號是否視為相同
- `LIKE` 的字元範圍如何判斷

你的程式：

```sql
T0.Code COLLATE Latin1_General_100_BIN2
```

意思是：這次檢查 `T0.Code` 時，暫時使用 `Latin1_General_100_BIN2` 定序。

`BIN2` 會依照 Unicode 字元碼進行精確比較，適合這種特殊字元檢查：

```sql
PATINDEX(
    N'%[^一-龥A-Za-z0-9]%',
    T0.Code COLLATE Latin1_General_100_BIN2
) > 0
```

其中：

```sql
[^一-龥A-Za-z0-9]
```

代表「不屬於以下範圍的任何字元」：

- `一-龥`：常用中文漢字
- `A-Z`：英文大寫
- `a-z`：英文小寫
- `0-9`：數字
- `^`：放在中括號開頭表示「不包含」

因此只要出現空白、`-`、`_`、`@`、`#`、括號或其他符號，`PATINDEX` 就會大於 `0`，進而觸發卡控。

`COLLATE` 不會修改資料庫中的内容，也不會永久改變欄位設定；它只影響這一次字串比較。

例如：

```sql
SELECT
    PATINDEX(
        N'%[^一-龥A-Za-z0-9]%',
        N'ABC-123' COLLATE Latin1_General_100_BIN2
    );
```

會回傳 `4`，表示第 4 個字元 `-` 是不允許的特殊字元。

---

<a id="q6"></a>

## S079-Q006｜〔程式碼〕

日期：2026-08-17

**你的提問**

```
T0.Code COLLATE Latin1_General_100_BIN2 解釋
```

**當時回答**

```sql
T0.Code COLLATE Latin1_General_100_BIN2
```

可以拆成三部分：

- `T0.Code`：要檢查的字串欄位。
- `COLLATE`：指定這一次字串判斷所使用的定序規則。
- `Latin1_General_100_BIN2`：採用 Unicode 字元碼進行精確比較的定序。

也就是：

> 將 `T0.Code` 暫時套用 `Latin1_General_100_BIN2` 規則來比較或搜尋字元。

名稱含義：

- `Latin1_General`：定序規則的語系名稱。
- `100`：較新版本的 Windows 定序規則。
- `BIN2`：依字元的 Unicode 編碼值進行二進位比較。

例如：

```sql
PATINDEX(
    N'%[^A-Za-z0-9]%',
    T0.Code COLLATE Latin1_General_100_BIN2
)
```

在 `BIN2` 規則下，以下字元會精確區分：

```text
A ≠ a
Ａ ≠ A
１ ≠ 1
```

因此：

```text
ABC123    → 全部符合 A-Z、a-z、0-9
ABC-123   → 「-」不符合
ＡBC123   → 全形「Ａ」不符合
ABC１２３ → 全形數字不符合
```

要特別注意，`COLLATE`：

- 不會修改 `T0.Code` 的資料。
- 不會永久修改欄位的定序設定。
- 只對目前這個 SQL 運算式生效。
- 不代表把资料转换成 `Latin1`。
- 中文仍然可以正常處理，因為 `nvarchar` 使用 Unicode。

在你的用途裡，使用 `BIN2` 是為了讓：

```sql
[^一-龥A-Za-z0-9]
```

按照明確的字元碼範圍判斷，避免資料庫原本較寬鬆的定序把全形字、重音字母或相似字元當作一般英文字母。

---

