---
title: "特殊字元、UDO 文字鍵與 COLLATE"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S022","S077","S079"]
---

# 特殊字元、UDO 文字鍵與 COLLATE

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 RAWITEMCREA、EXA2 轉 int 失敗與特殊字元限制。原對話後續確認實際檢核 Code 而非 U_ItemCode，且需限定本次物件與鍵；BIN2 用於精確字元比較，不應將所有 UDO object_type 轉成整數。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S022｜新增欄位特殊字元禁用SP](<../../原始問答/S022_新增欄位特殊字元禁用SP.md>)：2026-07-24，18 段問答。
- [S077｜Fix AA01 EXA2 conversion error](<../../原始問答/S077_Fix AA01 EXA2 conversion error.md>)：2026-08-13，2 段問答。
- [S079｜修正RAWITEMCREA轉換錯誤](<../../原始問答/S079_修正RAWITEMCREA轉換錯誤.md>)：2026-08-14，6 段問答。

## 問題索引

- [01｜我要寫sp 然後是要禁用關於欄位裡面不能有特殊字元 !@$%這種東西 可以做到](#qa1)
- [02｜我要只要可以 中文 英文 數字 這樣就好](#qa2)
- [03｜我要卡的是 FROM @RAWITEMCREA1 的 U_ItemCode](#qa3)
- [04｜SQL／程式碼調整與說明](#qa4)
- [05｜這兩個的差異](#qa5)
- [06｜我是不是要先declare @RAWITEMCREA1](#qa6)
- [07｜Msg 1087, Level 15, State 2, Procedure SBO_SP_TransactionNotification, Line 1532 [Batch St…](#qa7)
- [08｜[Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Invalid object name 'dbp.@RAWITEMCRE…](#qa8)
- [09｜我想說明一下 我的 @RAWITEMCREA1 是表身 @RAWITEMCREA這個是表頭 我是想要在表身的時候 要按更新就卡住 但目前是我表身@RAWITEMCREA1 可以更新…](#qa9)
- [10｜如果我是用 CREATE PROCEDURE [_CmSp_TEST] -- Add the parameters for the stored procedure here @P…](#qa10)
- [11｜為甚麼需要補dbo才不會也error是因為 要指定型態嗎](#qa11)
- [12｜所以才會跳出說 我沒有declare嗎](#qa12)
- [13｜括弧的意思?](#qa13)
- [14｜[](#qa14)
- [15｜{}](#qa15)
- [16｜了解 所以如果我是資料表 或是需要指定物件 通常都需要[]，去做表示嗎](#qa16)
- [17｜SQL／程式碼調整與說明](#qa17)
- [18｜Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1535 [Batch Sta…](#qa18)
- [19｜1 Created Failed AA01 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion fai…](#qa19)
- [20｜1 Created Failed AA01 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion fai…](#qa20)
- [21｜SQL／程式碼調整與說明](#qa21)
- [22｜ok](#qa22)
- [23｜我有特殊字元 但沒有卡到](#qa23)
- [24｜我發現 上一個你給邏輯是 T1.U_ItemCode 但我是卡 code](#qa24)
- [25｜〔程式碼〕](#qa25)
- [26｜〔程式碼〕](#qa26)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 我要寫sp 然後是要禁用關於欄位裡面不能有特殊字元 !@$%這種東西 可以做到

來源：[S022-Q001](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q1>)；2026-07-24

**問題**

我要寫sp 然後是要禁用關於欄位裡面不能有特殊字元 !@#$%這種東西 可以做到

**歷史回答**

可以。假設你使用 **SQL Server**，可以在 Stored Procedure 寫入前檢查「關於」欄位；只要包含 `!@#$%` 任一字元，就拒絕執行：

```sql
CREATE OR ALTER PROCEDURE dbo.UpdateAbout
    @UserId INT,
    @About NVARCHAR(500)
AS
BEGIN
    SET NOCOUNT ON;

    -- 禁止 ! @ # $ %
    IF @About LIKE N'%[!@#$%]%'
    BEGIN
        THROW 50001, N'「關於」欄位不能包含特殊字元：! @ # $ %', 1;
    END;

    UPDATE dbo.Users
    SET About = @About
    WHERE UserId = @UserId;
END;
GO
```

如果需求是「只能輸入中文、英文、數字和空白」，使用白名單會更安全：

```sql
IF @About LIKE N'%[^a-zA-Z0-9一-龥 ]%'
BEGIN
    THROW 50001, N'「關於」欄位只能包含中文、英文、數字及空白', 1;
END;
```

不過 `一-龥` 不能涵蓋所有罕見中文字。如果只需要禁止明確列出的符號，建議採用第一種寫法。前端也可以同步驗證，但資料庫端仍應保留檢查，避免有人繞過前端直接寫入。

<a id="qa2"></a>

### 2. 我要只要可以 中文 英文 數字 這樣就好

來源：[S022-Q002](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q2>)；2026-07-24

**問題**

我要只要可以 中文 英文 數字 這樣就好

**歷史回答**

可以，改成「白名單驗證」：只允許中文、英文字母及數字，其他符號與空白全部禁止。

```sql
IF @About IS NULL
   OR LTRIM(RTRIM(@About)) = N''
   OR @About COLLATE Latin1_General_100_BIN2
      LIKE N'%[^一-龥A-Za-z0-9]%'
BEGIN
    THROW 50001, N'「關於」欄位只能輸入中文、英文及數字', 1;
END;
```

完整 SP 範例：

```sql
CREATE OR ALTER PROCEDURE dbo.UpdateAbout
    @UserId INT,
    @About  NVARCHAR(500)
AS
BEGIN
    SET NOCOUNT ON;

    IF @About IS NULL
       OR LTRIM(RTRIM(@About)) = N''
       OR @About COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
    BEGIN
        THROW 50001, N'「關於」欄位只能輸入中文、英文及數字', 1;
    END;

    UPDATE dbo.Users
    SET About = @About
    WHERE UserId = @UserId;
END;
GO
```

允許：

```text
你好ABC123
產品介紹2026
```

禁止：

```text
你好!
ABC 123   -- 空白也禁止
test@email
```

若要允許空白，把條件改成：

```sql
LIKE N'%[^一-龥A-Za-z0-9 ]%'
```

<a id="qa3"></a>

### 3. 我要卡的是 FROM @RAWITEMCREA1 的 U_ItemCode

來源：[S022-Q003](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q3>)；2026-07-24

**問題**

我要卡的是 FROM @RAWITEMCREA1 的 U_ItemCode

**歷史回答**

了解，要檢查 `@RAWITEMCREA1` 裡每一筆 `U_ItemCode`，只允許中文、英文、數字：

```sql
IF EXISTS (
    SELECT 1
    FROM @RAWITEMCREA1
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    THROW 50001, N'U_ItemCode 只能輸入中文、英文及數字', 1;
END;
```

如果想先查出哪些资料有問題：

```sql
SELECT U_ItemCode
FROM @RAWITEMCREA1
WHERE U_ItemCode IS NULL
   OR LTRIM(RTRIM(U_ItemCode)) = N''
   OR U_ItemCode COLLATE Latin1_General_100_BIN2
      LIKE N'%[^一-龥A-Za-z0-9]%';
```

若 `NULL` 或空字串可以接受，則只保留特殊字元檢查：

```sql
IF EXISTS (
    SELECT 1
    FROM @RAWITEMCREA1
    WHERE U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    THROW 50001, N'U_ItemCode 只能輸入中文、英文及數字', 1;
END;
```

<a id="qa4"></a>

### 4. SQL／程式碼調整與說明

來源：[S022-Q004](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q4>)；2026-07-24

**問題**

IF EXISTS (
    SELECT 1
    FROM @RAWITEMCREA1
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    THROW 50001, N'U_ItemCode 只能輸入中文、英文及數字', 1;
END; 
可以幫我分段 解析這段這怎麼寫的嗎

**歷史回答**

可以。這段程式的目的，是檢查 `@RAWITEMCREA1` 裡是否存在不符合規則的 `U_ItemCode`。只要找到一筆，就停止 SP 並顯示錯誤。

```sql
IF EXISTS (
    SELECT 1
    FROM @RAWITEMCREA1
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    THROW 50001, N'U_ItemCode 只能輸入中文、英文及數字', 1;
END;
```

### 1. `IF EXISTS`

```sql
IF EXISTS (
    ...
)
```

`EXISTS` 的意思是：「括號內的查詢，有沒有找到任何一筆資料？」

- 有找到至少一筆：結果為 `TRUE`，執行 `BEGIN ... END`
- 完全找不到：結果為 `FALSE`，繼續執行後面的程式

這裡用來判斷是否存在不合法的 `U_ItemCode`。

---

### 2. `SELECT 1`

```sql
SELECT 1
FROM @RAWITEMCREA1
```

`EXISTS` 只在乎「有沒有資料」，不在乎實際查出什麼欄位，所以通常寫成：

```sql
SELECT 1
```

也可以寫 `SELECT U_ItemCode` 或 `SELECT *`，但沒有必要。

`@RAWITEMCREA1` 看起來是一個 Table Variable（資料表變數）。

---

### 3. 檢查是否為 `NULL`

```sql
WHERE U_ItemCode IS NULL
```

檢查 `U_ItemCode` 是否沒有值。

`NULL` 和空字串不一樣：

```text
NULL  → 完全沒有值
''    → 有值，但內容是空的
```

如果資料裡有任何一筆 `U_ItemCode` 是 `NULL`，就會判定不合法。

---

### 4. 檢查空字串或全部是空白

```sql
OR LTRIM(RTRIM(U_ItemCode)) = N''
```

先看裡面的兩個函數：

```sql
RTRIM(U_ItemCode)
```

移除右邊空白。

```sql
LTRIM(RTRIM(U_ItemCode))
```

再移除左邊空白。

例如：

```text
原始內容：'   ABC   '
處理結果：'ABC'
```

如果原始內容全部都是空白：

```text
原始內容：'      '
處理結果：''
```

最後判斷：

```sql
= N''
```

表示處理完後是不是空字串。

`N` 表示這是 Unicode 字串，適合處理中文：

```sql
N''
N'中文訊息'
```

---

### 5. `OR` 的意思

```sql
WHERE 條件一
   OR 條件二
   OR 條件三
```

`OR` 表示只要其中一個條件成立，該筆資料就是不合法。

這裡的三個條件是：

```text
U_ItemCode 是 NULL
或
U_ItemCode 是空字串／全部空白
或
U_ItemCode 包含中文、英文、數字以外的字元
```

---

### 6. `COLLATE`

```sql
U_ItemCode COLLATE Latin1_General_100_BIN2
```

`COLLATE` 是指定 SQL Server 用什麼規則比較字元。

`BIN2` 是使用字元的 Unicode 編碼值進行比較，讓下面的字元範圍判斷更明確：

```sql
一-龥
A-Z
a-z
0-9
```

它只影響這次比較，不會修改欄位內容，也不會更改資料庫設定。

---

### 7. `LIKE` 字元規則

```sql
LIKE N'%[^一-龥A-Za-z0-9]%'
```

可以拆成以下幾個部分。

#### `%`

```sql
%
```

代表任意數量的字元，零個或多個都可以。

前後都有 `%`：

```sql
%條件%
```

代表在字串中任何位置尋找符合條件的字元。

#### `[一-龥A-Za-z0-9]`

中括號表示符合其中任一字元：

```text
一-龥  → 常用中文字
A-Z    → 大寫英文字母
a-z    → 小寫英文字母
0-9    → 數字
```

#### `^`

```sql
[^一-龥A-Za-z0-9]
```

中括號裡面的 `^` 表示「不屬於」。

所以完整意思是：

```sql
LIKE N'%[^一-龥A-Za-z0-9]%'
```

檢查字串裡是否包含至少一個「不是中文、英文或數字」的字元。

例如：

```text
ABC123       → 不符合此條件，代表合法
產品ABC123   → 不符合此條件，代表合法
ABC-123      → 符合，因為包含 -
ABC 123      → 符合，因為包含空白
ABC@123      → 符合，因為包含 @
```

要注意：這個條件是在找「不合法字元」，找到時才會進入錯誤處理。

---

### 8. `BEGIN ... END`

```sql
BEGIN
    ...
END;
```

代表 `IF` 條件成立時要執行的程式區塊。

這裡就是：只要找到一筆不合法資料，就拋出錯誤。

---

### 9. `THROW`

```sql
THROW 50001, N'U_ItemCode 只能輸入中文、英文及數字', 1;
```

分成三個參數：

```sql
THROW 錯誤編號, 錯誤訊息, 狀態碼;
```

#### `50001`

自訂錯誤編號。SQL Server 自訂錯誤編號必須大於或等於 `50000`。

#### 錯誤訊息

```sql
N'U_ItemCode 只能輸入中文、英文及數字'
```

使用者或程式會收到的錯誤內容。

#### `1`

錯誤狀態碼，可用來區分錯誤發生的位置，一般使用 `1` 即可。

執行 `THROW` 後，目前的 SQL 批次或 Stored Procedure 會停止，不再繼續執行後面的新增或更新。

---

### 整體執行流程

```text
讀取 @RAWITEMCREA1
        ↓
尋找不合法的 U_ItemCode
        ↓
是否找到至少一筆？
   ├─ 否 → 繼續執行 SP
   └─ 是 → THROW 錯誤並停止 SP
```

如果想讓錯誤訊息直接顯示哪個 `U_ItemCode` 有問題，可以改成先取得錯誤資料，再組合錯誤訊息。

<a id="qa5"></a>

### 5. 這兩個的差異

來源：[S022-Q005](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q5>)；2026-07-24

**問題**

# Files mentioned by the user:

## codex-clipboard-649726bb-e154-4522-b2c9-1caf2c416f11.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-649726bb-e154-4522-b2c9-1caf2c416f11.png

## My request for Codex:
這兩個的差異

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

圖片中其實有三個 SAP Business One 的 SP，核心差異是「執行時機」以及「能不能阻止資料新增／修改」。

| SP | 執行時機 | 能否擋單 | 主要用途 |
|---|---|---:|---|
| `SBO_SP_TransactionNotification` | 交易完成前 | 可以 | 驗證欄位、阻止不合法資料 |
| `SBO_SP_PostTransactionNotice` | 交易完成後 | 不可以 | 寫入紀錄、通知、後續處理 |
| `SBO_SP_EDF_TransactionNotification` | 原本用於電子文件 EDF | 不建議使用 | SAP Support 表示目前已不再觸發 |

### 你的需求該放哪裡？

你要限制 `U_ItemCode` 只能輸入中文、英文、數字，必須放在：

```sql
dbo.SBO_SP_TransactionNotification
```

因為它在資料正式寫入前執行，可以把錯誤資料擋下來。

### `SBO_SP_TransactionNotification`

執行順序大致是：

```text
使用者按新增／更新
        ↓
執行 TransactionNotification
        ↓
檢查成功 → 寫入資料
檢查失敗 → 取消交易並顯示錯誤
```

在 SAP B1 的 `TransactionNotification` 裡，通常不要直接使用：

```sql
THROW 50001, N'錯誤訊息', 1;
```

應設定 SAP B1 已經提供的輸出變數：

```sql
SET @error = -1;
SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
```

例如：

```sql
IF EXISTS (
    SELECT 1
    FROM @RAWITEMCREA1
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    SET @error = -1;
    SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
END;
```

這樣 SAP B1 畫面會顯示錯誤訊息，並阻止資料新增或更新。

### `SBO_SP_PostTransactionNotice`

它是在資料已經成功寫入之後才執行：

```text
資料成功寫入
      ↓
交易提交
      ↓
執行 PostTransactionNotice
```

因此即使它發現 `U_ItemCode` 有問題，原本的新增或更新通常也已經完成，無法用它阻止。

適合拿來做：

- 寫入操作紀錄
- 更新額外的自訂資料
- 執行後續同步
- 觸發通知

不適合拿來做必須擋單的欄位驗證。

### `SBO_SP_EDF_TransactionNotification`

`EDF` 是 Electronic Document Framework，原本跟電子文件處理有關。SAP Support 在 SAP Community 表示，這個 SP 已經不再使用，也不再被觸發，因此不要把新的驗證寫在這裡。[SAP Community 說明](https://community.sap.com/t5/enterprise-resource-planning-q-a/sbo-sp-edf-transactionnotification/qaq-p/12181608)

結論：你的 `U_ItemCode` 驗證應寫在 `SBO_SP_TransactionNotification`，並以 `@error`、`@error_message` 回傳錯誤，不要放在 `PostTransactionNotice` 或 `EDF_TransactionNotification`。SAP 官方文件也將前兩者列為可加入自訂程式碼的公司資料庫程序，但建議先在測試環境驗證，避免影響 DI API 或外部同步。[SAP Help](https://help.sap.com/docs/SAP_FIELD_SERVICE_MANAGEMENT/fsm_system_requirements/sap-b1-cloud-connector-requirements.html?locale=zh-CN)

<a id="qa6"></a>

### 6. 我是不是要先declare @RAWITEMCREA1

來源：[S022-Q006](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q6>)；2026-07-24

**問題**

我是不是要先declare @RAWITEMCREA1

**歷史回答**

如果 `RAWITEMCREA1` 是 SAP Business One 的「使用者定義表（UDT）」，就不需要 `DECLARE`。

SAP B1 的 UDT 實際資料表名稱會包含 `@`，SQL Server 必須用中括號包起來：

```sql
FROM [@RAWITEMCREA1]
```

不是：

```sql
FROM @RAWITEMCREA1
```

兩者意思不同：

```sql
@RAWITEMCREA1
```

代表 SQL Server 的「變數」，所以必須先 `DECLARE`。

```sql
[@RAWITEMCREA1]
```

代表名稱叫作 `@RAWITEMCREA1` 的實際資料表，不需要 `DECLARE`。

因此你的檢查應改成：

```sql
IF EXISTS (
    SELECT 1
    FROM [@RAWITEMCREA1]
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    SET @error = -1;
    SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
END;
```

不過這段會檢查整張表。只要舊資料中有任何一筆不合法，以後每次觸發都可能報錯。建議只檢查本次新增或更新的資料。

如果 `RAWITEMCREA1` 是主資料型 UDT，主鍵通常是 `Code`：

```sql
IF @transaction_type IN ('A', 'U')
AND EXISTS (
    SELECT 1
    FROM [@RAWITEMCREA1]
    WHERE Code = @list_of_cols_val_tab_del
      AND (
             U_ItemCode IS NULL
          OR LTRIM(RTRIM(U_ItemCode)) = N''
          OR U_ItemCode COLLATE Latin1_General_100_BIN2
             LIKE N'%[^一-龥A-Za-z0-9]%'
      )
)
BEGIN
    SET @error = -1;
    SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
END;
```

還要在外層加上該 UDO／UDT 對應的 `@object_type`，例如：

```sql
IF @object_type = N'你的物件代碼'
   AND @transaction_type IN ('A', 'U')
BEGIN
    -- U_ItemCode 檢查
END;
```

重點是：如果它是 SAP B1 資料表，請寫 `[@RAWITEMCREA1]`，不用 `DECLARE`。

<a id="qa7"></a>

### 7. Msg 1087, Level 15, State 2, Procedure SBO_SP_TransactionNotification, Line 1532 [Batch St…

來源：[S022-Q007](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q7>)；2026-07-24

**問題**

Msg 1087, Level 15, State 2, Procedure SBO_SP_TransactionNotification, Line 1532 [Batch Start Line 7]
Must declare the table variable "@RAWITEMCREA1".

**歷史回答**

這個錯誤代表 SP 第 1532 行仍然寫成：

```sql
FROM @RAWITEMCREA1
```

SQL Server 因此把它當成「資料表變數」，才要求 `DECLARE`。

請改為實際資料表名稱：

```sql
FROM dbo.[@RAWITEMCREA1]
```

完整寫法：

```sql
IF EXISTS (
    SELECT 1
    FROM dbo.[@RAWITEMCREA1]
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    SET @error = -1;
    SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
END;
```

注意中括號的位置：

```sql
-- 正確
dbo.[@RAWITEMCREA1]

-- 錯誤：會被當成變數
@RAWITEMCREA1

-- 錯誤：物件名稱會不一樣
[dbo.@RAWITEMCREA1]
```

可以先執行以下 SQL，確認資料表真正存在：

```sql
SELECT
    SCHEMA_NAME(schema_id) AS SchemaName,
    name AS TableName
FROM sys.tables
WHERE name = N'@RAWITEMCREA1';
```

如果查到：

```text
SchemaName  TableName
dbo         @RAWITEMCREA1
```

就確定應該使用：

```sql
FROM dbo.[@RAWITEMCREA1]
```

另外，請在整個 `SBO_SP_TransactionNotification` 搜尋：

```text
@RAWITEMCREA1
```

確保所有作為資料表使用的地方都有改成 `dbo.[@RAWITEMCREA1]`。只要漏掉一個 `FROM @RAWITEMCREA1` 或 `JOIN @RAWITEMCREA1`，建立 SP 時仍會出現 `Msg 1087`。

<a id="qa8"></a>

### 8. [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Invalid object name 'dbp.@RAWITEMCRE…

來源：[S022-Q008](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q8>)；2026-07-24

**問題**

[Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Invalid object name 'dbp.@RAWITEMCREA1'.  '公司資訊' (CINF) (CINF)

**歷史回答**

這次錯誤是拼字問題：你寫成了 `dbp`，正確應該是 `dbo`。

錯誤訊息：

```text
Invalid object name 'dbp.@RAWITEMCREA1'
```

請把：

```sql
FROM dbp.[@RAWITEMCREA1]
```

改成：

```sql
FROM dbo.[@RAWITEMCREA1]
```

完整範例：

```sql
IF EXISTS (
    SELECT 1
    FROM dbo.[@RAWITEMCREA1]
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    SET @error = -1;
    SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
END;
```

差別在最後一個字母：

```text
dbp  ← 錯誤
dbo  ← 正確（database owner）
```

可以先單獨測試：

```sql
SELECT TOP (10) *
FROM dbo.[@RAWITEMCREA1];
```

如果這段仍顯示 `Invalid object name`，代表真正的資料表名稱可能不是 `@RAWITEMCREA1`，或目前選到錯的公司資料庫。可執行：

```sql
SELECT
    SCHEMA_NAME(schema_id) AS SchemaName,
    name AS TableName
FROM sys.tables
WHERE name LIKE N'%RAWITEMCREA%';
```

再依查到的名稱填入。SQL Server 錯誤訊息不顯示中括號是正常的；目前最直接的問題是 `dbp` 應改成 `dbo`。

<a id="qa9"></a>

### 9. 我想說明一下 我的 @RAWITEMCREA1 是表身 @RAWITEMCREA這個是表頭 我是想要在表身的時候 要按更新就卡住 但目前是我表身@RAWITEMCREA1 可以更新…

來源：[S022-Q009](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q9>)；2026-07-24

**問題**

我想說明一下 我的 @RAWITEMCREA1 是表身 @RAWITEMCREA這個是表頭 我是想要在表身的時候 要按更新就卡住 但目前是我表身@RAWITEMCREA1 可以更新 但我按到外面表頭要確認卻不行

**歷史回答**

了解。你目前遇到的情況其實是 SAP Business One UDO 的正常流程：

```text
在表身修改 U_ItemCode
        ↓
資料暫時只在 SAP B1 畫面
        ↓
尚未真正寫入 SQL Server
        ↓
按表頭／整張單據的「更新」
        ↓
觸發 SBO_SP_TransactionNotification
        ↓
SP 驗證並阻止更新
```

所以 `SBO_SP_TransactionNotification` 無法在你剛輸入完表身欄位時立刻擋住；必須等整張 UDO 按下「更新」，資料送到資料庫時才會觸發。

你現在「表身可以編輯，但按外面表頭更新時被擋住」，代表 SP 實際上有正常運作。

### 正確檢查本次表頭的表身資料

不要檢查整張 `[@RAWITEMCREA1]`，應該只檢查目前正在更新的這張單據。

如果 `[@RAWITEMCREA]` 是「文件型 UDO」，通常使用 `DocEntry` 關聯：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.DocEntry =
              TRY_CONVERT(INT, @list_of_cols_val_tab_del)
          AND (
                 T1.U_ItemCode IS NULL
              OR LTRIM(RTRIM(T1.U_ItemCode)) = N''
              OR T1.U_ItemCode COLLATE Latin1_General_100_BIN2
                 LIKE N'%[^一-龥A-Za-z0-9]%'
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message =
            N'表身 U_ItemCode 只能輸入中文、英文及數字';
    END;
END;
```

表頭、表身的關聯通常是：

```text
表頭：[@RAWITEMCREA].DocEntry
                  ↓
表身：[@RAWITEMCREA1].DocEntry
```

### 如果是主資料型 UDO

若表頭不是 `DocEntry`，而是使用 `Code`，則應改為：

```sql
WHERE T1.Code = @list_of_cols_val_tab_del
```

完整條件為：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.Code = @list_of_cols_val_tab_del
          AND (
                 T1.U_ItemCode IS NULL
              OR LTRIM(RTRIM(T1.U_ItemCode)) = N''
              OR T1.U_ItemCode COLLATE Latin1_General_100_BIN2
                 LIKE N'%[^一-龥A-Za-z0-9]%'
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message =
            N'表身 U_ItemCode 只能輸入中文、英文及數字';
    END;
END;
```

### 如果要在表身輸入當下就擋住

只靠 SQL Stored Procedure 做不到，因為當時資料還沒有送到 SQL Server。需要在 SAP B1 前端做驗證，例如：

- SAP Business One UI API Add-on
- B1UP Validation
- 自訂 Add-on 的欄位事件
- 在矩陣欄位離開或 Validate 事件中檢查

所以目前可達成的資料庫驗證時機，就是使用者按整張 UDO 的「新增／更新」時阻止儲存。這是可靠的後端防線。

<a id="qa10"></a>

### 10. 如果我是用 CREATE PROCEDURE [_CmSp_TEST] -- Add the parameters for the stored procedure here @P…

來源：[S022-Q010](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q10>)；2026-07-24

**問題**

〔此提問引用其他聊天，重複背景已省略；原聊天另見來源索引。〕

## My request for Codex:
如果我是用 

CREATE PROCEDURE [_CmSp_TEST] 
	-- Add the parameters for the stored procedure here
	@PARAM AS Date
AS
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;

    -- Insert statements for procedure here
	SELECT
		T0.DocTotal, T0.DocNum, T0.DocDate
	FROM OINV T0
	WHERE T0.DocDate = @PARAM
END



DECLARE @PARAM AS date

set @PARAM=/*SELECT 1 FROM OINV T0 WHERE T0."DocDate"*/  '[%0]'

EXECUTE [_CmSp_TEST] [@辨識 Alternative Items 語法](thread://019f8347-287e-75f0-a506-117e7fc3c024) 

類似這種辦法呢 能不能

**歷史回答**

可以用這種方式呼叫 Stored Procedure，但它比較適合「查詢報表」。若目的是在 UDO 表身輸入 `U_ItemCode` 時立即阻擋，它不能直接取代 `SBO_SP_TransactionNotification`。

你目前的寫法少了把 `@PARAM` 傳入 SP。

### 正確的日期查詢寫法

建立 SP：

```sql
CREATE OR ALTER PROCEDURE dbo.[_CmSp_TEST]
    @PARAM DATE
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        T0.DocTotal,
        T0.DocNum,
        T0.DocDate
    FROM dbo.OINV AS T0
    WHERE T0.DocDate = @PARAM;
END;
GO
```

在 SAP B1 查詢管理員執行：

```sql
DECLARE @PARAM DATE;

SET @PARAM =
    /* SELECT T0.DocDate FROM OINV T0
       WHERE T0.DocDate */
    '[%0]';

EXECUTE dbo.[_CmSp_TEST]
    @PARAM = @PARAM;
```

你原本只有：

```sql
EXECUTE [_CmSp_TEST]
```

沒有將參數傳入，所以應改成：

```sql
EXECUTE dbo.[_CmSp_TEST] @PARAM = @PARAM;
```

---

### 套用到 `U_ItemCode` 驗證

可以另外建立一個驗證 SP：

```sql
CREATE OR ALTER PROCEDURE dbo.[_CmSp_CheckItemCode]
    @ItemCode NVARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    IF @ItemCode IS NULL
       OR LTRIM(RTRIM(@ItemCode)) = N''
       OR @ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
    BEGIN
        SELECT
            N'N' AS IsValid,
            N'U_ItemCode 只能輸入中文、英文及數字' AS Message;
        RETURN;
    END;

    SELECT
        N'Y' AS IsValid,
        N'資料正確' AS Message;
END;
GO
```

手動測試：

```sql
EXECUTE dbo.[_CmSp_CheckItemCode]
    @ItemCode = N'測試ABC123';
```

結果：

```text
IsValid  Message
Y        資料正確
```

如果傳入特殊字元：

```sql
EXECUTE dbo.[_CmSp_CheckItemCode]
    @ItemCode = N'測試ABC@123';
```

結果：

```text
IsValid  Message
N        U_ItemCode 只能輸入中文、英文及數字
```

### 但有一個重要限制

從 SAP B1 查詢管理員執行這個 SP，只能：

- 手動檢查內容
- 顯示查詢結果
- 提供 `[%0]` 讓使用者輸入
- 給報表或查詢使用

它無法直接阻止 UDO 表身更新。

原因是表身尚未按整張表單的「更新」前，內容通常還在 SAP B1 畫面記憶體裡，資料庫中的 `[@RAWITEMCREA1]` 還看不到新值。

因此有三種層級：

| 方法 | 驗證時機 | 能否阻止儲存 |
|---|---|---:|
| 查詢管理員執行自訂 SP | 手動執行查詢時 | 不行 |
| `SBO_SP_TransactionNotification` | 按整張 UDO「新增／更新」時 | 可以 |
| UI API Add-On／B1UP | 離開表身欄位或按鈕時 | 可以立即攔截 |

所以你可以把驗證邏輯寫進 `_CmSp_CheckItemCode`，再由 `SBO_SP_TransactionNotification` 呼叫它；但若要「表身輸入完成當下就卡住」，仍需要 UI API Add-On 或 B1UP。SQL SP 本身無法捕捉尚未送進資料庫的畫面事件。

<a id="qa11"></a>

### 11. 為甚麼需要補dbo才不會也error是因為 要指定型態嗎

來源：[S022-Q011](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q11>)；2026-07-24

**問題**

為甚麼需要補dbo才不會也error是因為　要指定型態嗎

**歷史回答**

不是指定「型態」，`dbo` 是指定資料庫物件所屬的 **Schema（結構描述／命名空間）**。

SQL Server 的完整物件名稱通常是：

```text
資料庫名稱.Schema名稱.物件名稱
```

例如：

```sql
公司資料庫.dbo.[_CmSp_TEST]
公司資料庫.dbo.[@RAWITEMCREA1]
```

在目前公司資料庫內，可以省略資料庫名稱：

```sql
dbo.[_CmSp_TEST]
dbo.[@RAWITEMCREA1]
```

### 為什麼不加 `dbo` 有時會出錯？

當你寫：

```sql
EXEC [_CmSp_TEST]
```

SQL Server 必須自行判斷這個 SP 屬於哪個 Schema。它通常會依序尋找：

```text
目前登入者的預設 Schema
dbo
```

如果使用者的預設 Schema 不正確、權限不同，或同名物件存在，就可能找不到或找到錯的物件。

明確寫成：

```sql
EXEC dbo.[_CmSp_TEST]
```

就是告訴 SQL Server：

> 執行 `dbo` Schema 底下的 `_CmSp_TEST`。

### 套用到 SAP B1 使用者定義表

```sql
dbo.[@RAWITEMCREA1]
```

可拆成：

```text
dbo                 → Schema 名稱
[@RAWITEMCREA1]     → 資料表名稱
```

中括號是因為資料表名稱包含 `@`：

```sql
SELECT *
FROM dbo.[@RAWITEMCREA1];
```

這與下面的寫法完全不同：

```sql
SELECT *
FROM @RAWITEMCREA1;
```

沒有中括號時，SQL Server 會將 `@RAWITEMCREA1` 視為「資料表變數」，因此出現：

```text
Must declare the table variable "@RAWITEMCREA1"
```

### 常見錯誤對照

```sql
-- 正確：dbo Schema 底下的實際資料表
FROM dbo.[@RAWITEMCREA1]

-- 錯誤：會被當成資料表變數
FROM @RAWITEMCREA1

-- 錯誤：dbp 拼錯
FROM dbp.[@RAWITEMCREA1]

-- 可能可以執行，但建議明確加 dbo
FROM [@RAWITEMCREA1]
```

所以，補上 `dbo` 不是指定資料型態，而是精確指定物件的位置。SQL Server 與 SAP B1 的 SQL 程式中，建議資料表和 SP 都明確加上 `dbo.`。

<a id="qa12"></a>

### 12. 所以才會跳出說 我沒有declare嗎

來源：[S022-Q012](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q12>)；2026-07-24

**問題**

所以才會跳出說 我沒有declare嗎

**歷史回答**

對，但真正讓 SQL Server 認為你沒有 `DECLARE` 的關鍵，是少了中括號 `[]`，不只是少了 `dbo`。

當你寫：

```sql
FROM @RAWITEMCREA1
```

SQL Server 看到名稱以 `@` 開頭，就把它認定為「資料表變數」。資料表變數必須先宣告，例如：

```sql
DECLARE @RAWITEMCREA1 TABLE (
    U_ItemCode NVARCHAR(100)
);
```

因為你沒有宣告，所以出現：

```text
Must declare the table variable "@RAWITEMCREA1".
```

但你的 `@RAWITEMCREA1` 是 SAP B1 真實的使用者定義表，不是變數，因此要用中括號表示它是物件名稱：

```sql
FROM [@RAWITEMCREA1]
```

更完整、明確的寫法是：

```sql
FROM dbo.[@RAWITEMCREA1]
```

可以這樣記：

```text
@RAWITEMCREA1        → SQL Server 認為是變數，必須 DECLARE
[@RAWITEMCREA1]      → 實際資料表
dbo.[@RAWITEMCREA1]  → 明確指定 dbo Schema 裡的實際資料表
```

因此，避免 `Must declare` 錯誤的核心是加上中括號：

```sql
[@RAWITEMCREA1]
```

`dbo.` 則是進一步指定這張資料表位於哪個 Schema。

<a id="qa13"></a>

### 13. 括弧的意思?

來源：[S022-Q013](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q13>)；2026-07-24

**問題**

括弧的意思?

**歷史回答**

你看到的 `[]` 是「中括號」，在 SQL Server 裡表示：

> 中括號裡的內容是一個完整的物件名稱或欄位名稱。

例如：

```sql
[@RAWITEMCREA1]
```

意思是：這是一張名稱真的叫做 `@RAWITEMCREA1` 的資料表。

如果不加中括號：

```sql
@RAWITEMCREA1
```

SQL Server 看到 `@`，就會認為它是一個變數，因此要求先 `DECLARE`。

### 中括號常見用途

物件名稱包含特殊字元：

```sql
SELECT *
FROM dbo.[@RAWITEMCREA1];
```

物件名稱包含空白：

```sql
SELECT [Item Code]
FROM dbo.[Item Master];
```

物件名稱剛好是 SQL 保留字：

```sql
SELECT [Order]
FROM dbo.[User];
```

一般名稱也可以使用中括號：

```sql
SELECT [U_ItemCode]
FROM [dbo].[@RAWITEMCREA1];
```

等同於：

```sql
SELECT U_ItemCode
FROM dbo.[@RAWITEMCREA1];
```

因為 `U_ItemCode` 和 `dbo` 沒有特殊字元，所以中括號可以省略。

### 三種括號不要混淆

```sql
[]  -- 表示資料表、欄位或其他物件名稱
()  -- 條件分組、函數參數或子查詢
''  -- 表示文字內容
```

例如：

```sql
SELECT [U_ItemCode]
FROM dbo.[@RAWITEMCREA1]
WHERE (U_ItemCode = N'ABC123');
```

拆開看：

```text
[U_ItemCode]       → 欄位名稱
[@RAWITEMCREA1]    → 資料表名稱
(...)              → WHERE 條件分組
N'ABC123'          → Unicode 文字內容
```

因此你的情況最重要的是：

```sql
dbo.[@RAWITEMCREA1]
```

- `dbo`：資料表所在的 Schema
- `[]`：告訴 SQL Server，`@RAWITEMCREA1` 是資料表名稱
- `@RAWITEMCREA1`：SAP B1 使用者定義表的實際名稱

<a id="qa14"></a>

### 14. [

來源：[S022-Q014](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q14>)；2026-07-24

**問題**

[

**歷史回答**

這個符號：

```text
[
```

叫做「左中括號」。

需要和右中括號一起使用：

```text
[ ]
```

在 SQL Server 中，用來包住資料表名稱或欄位名稱：

```sql
[@RAWITEMCREA1]
```

意思是：

> `@RAWITEMCREA1` 整段是資料表名稱，不是變數。

正確：

```sql
SELECT *
FROM dbo.[@RAWITEMCREA1];
```

少了右中括號則是錯誤語法：

```sql
SELECT *
FROM dbo.[@RAWITEMCREA1;
```

可以把 `[]` 想成「名稱的外框」：框起來後，SQL Server 就會把裡面的文字視為物件名稱。

<a id="qa15"></a>

### 15. {}

來源：[S022-Q015](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q15>)；2026-07-24

**問題**

{}

**歷史回答**

這個符號：

```text
{}
```

叫做「大括號」或「花括號」。

在一般 SQL Server 的 T-SQL 語法中，通常不使用 `{}` 包資料表或欄位名稱。

```sql
-- 錯誤
SELECT *
FROM dbo.{@RAWITEMCREA1};
```

SQL Server 應使用中括號：

```sql
-- 正確
SELECT *
FROM dbo.[@RAWITEMCREA1];
```

常見括號整理：

```text
[]  中括號：包住資料表、欄位等物件名稱
()  小括號：函數、條件分組、子查詢
{}  大括號：一般 T-SQL 很少直接使用
```

你的 SAP B1 使用者定義表要寫成：

```sql
dbo.[@RAWITEMCREA1]
```

<a id="qa16"></a>

### 16. 了解 所以如果我是資料表 或是需要指定物件 通常都需要[]，去做表示嗎

來源：[S022-Q016](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q16>)；2026-07-24

**問題**

了解 所以如果我是資料表 或是需要指定物件 通常都需要[]，去做表示嗎

**歷史回答**

對，但不是所有資料表或欄位都「一定」要加 `[]`。

`[]` 的作用是明確告訴 SQL Server：

> 中括號內是一個物件名稱。

一般正常命名可以不加：

```sql
SELECT ItemCode
FROM dbo.OITM;
```

也可以加，結果相同：

```sql
SELECT [ItemCode]
FROM [dbo].[OITM];
```

以下情況通常需要加 `[]`：

- 名稱包含特殊字元，例如 `@`
- 名稱包含空白
- 名稱是 SQL Server 保留字
- 名稱以數字開頭

例如：

```sql
-- SAP B1 使用者定義表包含 @
SELECT *
FROM dbo.[@RAWITEMCREA1];

-- 欄位名稱包含空白
SELECT [Item Code]
FROM dbo.OITM;

-- 名稱是 SQL 保留字
SELECT [Order]
FROM dbo.[User];

-- 名稱以數字開頭
SELECT *
FROM dbo.[2026Data];
```

可以這樣記：

```text
dbo.OITM                 → 一般名稱，不必加 []
dbo.[@RAWITEMCREA1]      → 包含 @，需要加 []
dbo.[Item Master]        → 包含空白，需要加 []
dbo.[Order]              → SQL 保留字，建議加 []
```

所以你目前的 SAP B1 表：

```sql
dbo.[@RAWITEMCREA1]
```

必須加 `[]` 的主要原因，是資料表名稱本身包含 `@`；如果不加，SQL Server 就會把它當成變數。

<a id="qa17"></a>

### 17. SQL／程式碼調整與說明

來源：[S022-Q017](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q17>)；2026-07-24

**問題**

IF EXISTS (
    SELECT 1
    FROM @RAWITEMCREA1
    WHERE U_ItemCode IS NULL
       OR LTRIM(RTRIM(U_ItemCode)) = N''
       OR U_ItemCode COLLATE Latin1_General_100_BIN2
          LIKE N'%[^一-龥A-Za-z0-9]%'
)
BEGIN
    SET @error = -1;
    SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
END;

如果我只想要卡 我的自訂議表單 但我剛剛測試 她會影響到我其他單據

**歷史回答**

會影響其他單據，是因為目前程式沒有判斷 `@object_type`，而且每次都掃描整張表。SAP B1 任何單據新增或更新時，都會執行 `SBO_SP_TransactionNotification`，所以需要限定只處理你的自訂表單。

假設你的 UDO 代碼是 `RAWITEMCREA`，而且是文件型 UDO：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.DocEntry =
              TRY_CONVERT(INT, @list_of_cols_val_tab_del)
          AND (
                 T1.U_ItemCode IS NULL
              OR LTRIM(RTRIM(T1.U_ItemCode)) = N''
              OR T1.U_ItemCode COLLATE Latin1_General_100_BIN2
                 LIKE N'%[^一-龥A-Za-z0-9]%'
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message =
            N'U_ItemCode 只能輸入中文、英文及數字';
    END;
END;
```

這裡加了三層限制：

```sql
@object_type = N'RAWITEMCREA'
```

只處理你的自訂 UDO，其他銷售訂單、採購單、發票都不受影響。

```sql
@transaction_type IN (N'A', N'U')
```

只在新增或更新時檢查：

```text
A = Add，新增
U = Update，更新
```

以及：

```sql
T1.DocEntry = TRY_CONVERT(INT, @list_of_cols_val_tab_del)
```

只檢查目前這一張單據的表身，不掃描其他歷史資料。

### 確認真正的 UDO Code

可以執行：

```sql
SELECT
    Code,
    Name,
    TableName
FROM dbo.OUDO
WHERE TableName = N'RAWITEMCREA';
```

假設查詢結果是：

```text
Code           Name         TableName
RAWITEMCREA    原料建立單    RAWITEMCREA
```

那就使用：

```sql
IF @object_type = N'RAWITEMCREA'
```

如果 `Code` 查出來是其他值，例如：

```text
UDO_RAWITEM
```

就要改成：

```sql
IF @object_type = N'UDO_RAWITEM'
```

### 如果表頭使用 `Code` 而不是 `DocEntry`

若 `[@RAWITEMCREA]` 是主資料型 UDO，表頭、表身可能使用 `Code` 關聯，則把：

```sql
WHERE T1.DocEntry =
      TRY_CONVERT(INT, @list_of_cols_val_tab_del)
```

改成：

```sql
WHERE T1.Code = @list_of_cols_val_tab_del
```

你可以先確認表頭結構：

```sql
SELECT TOP (1) *
FROM dbo.[@RAWITEMCREA];
```

- 有 `DocEntry`：通常採用 `DocEntry`
- 主要識別欄位是 `Code`：採用 `Code`

另外，原本的：

```sql
FROM @RAWITEMCREA1
```

仍要改成：

```sql
FROM dbo.[@RAWITEMCREA1]
```

否則 SQL Server 會再次把它當成未宣告的資料表變數。

<a id="qa18"></a>

### 18. Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1535 [Batch Sta…

來源：[S022-Q018](<../../原始問答/S022_新增欄位特殊字元禁用SP.md#q18>)；2026-07-24

**問題**

Msg 207, Level 16, State 1, Procedure SBO_SP_TransactionNotification, Line 1535 [Batch Start Line 7]
Invalid column name 'DocEntry'.

**歷史回答**

這表示 `dbo.[@RAWITEMCREA1]` 表身沒有 `DocEntry` 欄位。你的 UDO 很可能是「主資料型」，表頭與表身透過 `Code` 關聯。

把：

```sql
T1.DocEntry = TRY_CONVERT(INT, @list_of_cols_val_tab_del)
```

改成：

```sql
T1.Code = @list_of_cols_val_tab_del
```

完整寫法：

```sql
IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.Code = @list_of_cols_val_tab_del
          AND (
                 T1.U_ItemCode IS NULL
              OR LTRIM(RTRIM(T1.U_ItemCode)) = N''
              OR T1.U_ItemCode COLLATE Latin1_General_100_BIN2
                 LIKE N'%[^一-龥A-Za-z0-9]%'
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message =
            N'U_ItemCode 只能輸入中文、英文及數字';
    END;
END;
```

可以先查看表身有哪些欄位：

```sql
SELECT
    C.column_id,
    C.name AS ColumnName,
    TYPE_NAME(C.user_type_id) AS DataType
FROM sys.columns AS C
WHERE C.object_id = OBJECT_ID(N'dbo.[@RAWITEMCREA1]')
ORDER BY C.column_id;
```

主資料型 UDO 的表身通常類似：

```text
Code
LineId
Object
LogInst
U_ItemCode
```

其中：

- `Code`：對應表頭的 `Code`
- `LineId`：表身行號
- `U_ItemCode`：你的自訂欄位

因此這裡應該使用：

```sql
WHERE T1.Code = @list_of_cols_val_tab_del
```

而不是 `DocEntry`。如果改完後沒有觸發驗證，下一步要確認 `RAWITEMCREA` 是否確實是這張 UDO 登記的 `Code`：

```sql
SELECT Code, Name, TableName
FROM dbo.OUDO
WHERE TableName = N'RAWITEMCREA';
```

查詢結果的 `Code` 才是 `@object_type` 應比較的值。

<a id="qa19"></a>

### 19. 1 Created Failed AA01 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion fai…

來源：[S077-Q001](<../../原始問答/S077_Fix AA01 EXA2 conversion error.md#q1>)；2026-08-13

**問題**

1 Created Failed AA01  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion failed when converting the nvarchar value 'EXA2' to data type int. (CINF)UDO_RAWITEMCREA

**歷史回答**

這是 SQL 型別不符：

`UDO_RAWITEMCREA` 建立資料時，某個 `int` 欄位收到文字值 `'EXA2'`，因此轉換失敗。`CINF` 很可能是發生錯誤的資料表或程序位置。

優先檢查：

1. `UDO_RAWITEMCREA` 的欄位定義，找出型別為 `int` 的欄位。
2. 新增資料的欄位順序是否錯位，例如漏寫欄位名稱，導致 `'EXA2'` 被塞入整數欄位。
3. SQL 中是否有：
   - `CAST(... AS INT)`／`CONVERT(INT, ...)`
   - `CASE` 同時回傳數字與文字
   - 整數欄位和文字欄位做 `JOIN` 或比較
4. 若 `'EXA2'` 是合法代碼，目標欄位應改為 `nvarchar`，或修正 mapping，寫入正確的文字欄位。

可先用以下 SQL 找相關欄位型別：

```sql
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    DATA_TYPE,
    CHARACTER_MAXIMUM_LENGTH
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME LIKE '%RAWITEMCREA%'
   OR TABLE_NAME = 'CINF'
ORDER BY TABLE_NAME, ORDINAL_POSITION;
```

如果是 SAP Business One UDO，請特別比對 `U_` 使用者欄位的實際 SQL 型別與程式設定；不要直接修改 SAP 系統表。把造成錯誤的 `INSERT/UPDATE` SQL、UDO 欄位清單或完整匯入資料貼上來，我可以直接定位是哪一欄。

<a id="qa20"></a>

### 20. 1 Created Failed AA01 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion fai…

來源：[S077-Q002](<../../原始問答/S077_Fix AA01 EXA2 conversion error.md#q2>)；2026-08-13

**問題**

1 Created Failed AA01  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Conversion failed when converting the nvarchar value 'EXA2' to data type int. (CINF)UDO_RAWITEMCREA 

我現在無法建立

**歷史回答**

已確認：錯誤來源幾乎可以鎖定在公司資料庫的 `SBO_SP_TransactionNotification`，不是 `UDO_RAWITEMCREA` 欄位本身。

`EXA2` 是 UDO 的文字 `object_type`，但該預存程序把 `@object_type` 當成 `int`，或自訂卡控 SQL 強制將它轉成整數，所以 UDO 無法建立。SAP 也有相同錯誤的官方案例：[SAP KBA 3495039](https://userapps.support.sap.com/sap/support/knowledge/en/3495039)。

先在出錯的公司資料庫執行：

```sql
SELECT
    p.name AS ParameterName,
    TYPE_NAME(p.user_type_id) AS DataType,
    p.max_length
FROM sys.parameters p
WHERE p.object_id = OBJECT_ID('dbo.SBO_SP_TransactionNotification')
ORDER BY p.parameter_id;
```

正常應類似：

```text
@object_type                 nvarchar
@transaction_type            nchar
@num_of_cols_in_key          int
@list_of_key_cols_tab_del    nvarchar
@list_of_cols_val_tab_del    nvarchar
```

如果 `@object_type` 顯示為 `int`，就必須將程序開頭修正為：

```sql
ALTER PROCEDURE [dbo].[SBO_SP_TransactionNotification]
    @object_type NVARCHAR(30),
    @transaction_type NCHAR(1),
    @num_of_cols_in_key INT,
    @list_of_key_cols_tab_del NVARCHAR(255),
    @list_of_cols_val_tab_del NVARCHAR(255)
AS
BEGIN
    -- 保留原本程序內容
END
```

不要直接只執行上面範例，否則可能覆蓋原有卡控。應先備份完整程序：

```sql
EXEC sp_helptext 'dbo.SBO_SP_TransactionNotification';
```

如果參數已經是 `nvarchar`，則問題在程序內的自訂判斷。搜尋以下寫法：

```sql
CAST(@object_type AS INT)
CONVERT(INT, @object_type)
ISNULL(@object_type, 0)
@object_type IN (13, 18, 20)
```

建議一律使用文字比較：

```sql
IF @object_type = N'13'
```

或：

```sql
IF @object_type IN (N'13', N'18', N'EXA2')
```

尤其不要寫：

```sql
IF @object_type IN (13, 18, N'EXA2')
```

因為數字與文字混用可能觸發 SQL Server 的隱含整數轉換。

修正 `SBO_SP_TransactionNotification` 後，再重新建立 `AA01 / UDO_RAWITEMCREA`。不要修改 `CINF` 系統表。

<a id="qa21"></a>

### 21. SQL／程式碼調整與說明

來源：[S079-Q001](<../../原始問答/S079_修正RAWITEMCREA轉換錯誤.md#q1>)；2026-08-14

**問題**

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

**歷史回答**

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

<a id="qa22"></a>

### 22. ok

來源：[S079-Q002](<../../原始問答/S079_修正RAWITEMCREA轉換錯誤.md#q2>)；2026-08-14

**問題**

ok

**歷史回答**

好的，改成字串比較即可解決 `EXA2` 轉換 `INT` 的錯誤。

<a id="qa23"></a>

### 23. 我有特殊字元 但沒有卡到

來源：[S079-Q003](<../../原始問答/S079_修正RAWITEMCREA轉換錯誤.md#q3>)；2026-08-17

**問題**

我有特殊字元 但沒有卡到

**歷史回答**

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

<a id="qa24"></a>

### 24. 我發現 上一個你給邏輯是 T1.U_ItemCode 但我是卡 code

來源：[S079-Q004](<../../原始問答/S079_修正RAWITEMCREA轉換錯誤.md#q4>)；2026-08-17

**問題**

我發現 上一個你給邏輯是 T1.U_ItemCode 但我是卡 code

**歷史回答**

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

<a id="qa25"></a>

### 25. 〔程式碼〕

來源：[S079-Q005](<../../原始問答/S079_修正RAWITEMCREA轉換錯誤.md#q5>)；2026-08-17

**問題**

```
COLLATE  甚麼意思
```

**歷史回答**

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

<a id="qa26"></a>

### 26. 〔程式碼〕

來源：[S079-Q006](<../../原始問答/S079_修正RAWITEMCREA轉換錯誤.md#q6>)；2026-08-17

**問題**

```
T0.Code COLLATE Latin1_General_100_BIN2 解釋
```

**歷史回答**

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

