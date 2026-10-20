# 查找 SAPB1 付款條件翻譯

來源編號：S038  
聊天 ID：`019fc588-bc3a-7a51-950a-7ce52e313551`  
聊天起始日期（台北）：2026-08-03  
保存位置：一般本機紀錄  
問答段落：8

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S038-Q001｜這是 SAPB1 的翻譯介面，然後我手動新增了 一個新的關於付款條件的翻譯。 但我查詢沒有看到欸 只有原生的

日期：2026-08-03

**你的提問**

# Files mentioned by the user:

## codex-clipboard-fbe7afaf-c9b5-4c8d-89e9-aaa406e43198.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-fbe7afaf-c9b5-4c8d-89e9-aaa406e43198.png

## My request for Codex:
這是 SAPB1 的翻譯介面，然後我手動新增了 一個新的關於付款條件的翻譯。

但我查詢沒有看到欸 只有原生的

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這是正常的：你新增的英文 `180days` 不會寫回付款條件主檔 `OCTG.PymntGroup`。

SAP B1 將：

- 原生說明存在 `OCTG.PymntGroup`
- 翻譯索引存在 `OMLT`
- 各語言翻譯內容存在 `MLT1.Trans`

所以只查 `OCTG`，一定只會看到原生的 `T180-出貨後180天`。[SAP 官方的 OMLT 資料表說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKhelp/OMLT.html)也顯示翻譯是另外依 `TableName`、`FieldAlias`、`PK` 儲存；付款條件主檔則是 `OCTG`。[SAP 官方 OCTG 說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/octg.html)

先直接執行這段查代碼 `68` 的翻譯：

```sql
SELECT
    T0.TranEntry,
    T0.TableName,
    T0.FieldAlias,
    T0.PK,
    T1.LangCode,
    T1.Trans
FROM OMLT T0
LEFT JOIN MLT1 T1
    ON T1.TranEntry = T0.TranEntry
WHERE T0.TableName = 'OCTG'
  AND T0.PK = '68';
```

預期應該會看到類似：

```text
TableName   FieldAlias    PK   LangCode   Trans
OCTG        PymntGroup    68   ...        180days
```

若要把原生說明和英文翻譯一起查出來，SQL Server 可以這樣寫：

```sql
SELECT
    T0.GroupNum,
    T0.PymntGroup AS [原生付款條件],
    T2.LangCode,
    T2.Trans      AS [翻譯]
FROM OCTG T0
LEFT JOIN OMLT T1
    ON T1.TableName  = 'OCTG'
   AND T1.FieldAlias = 'PymntGroup'
   AND T1.PK = CAST(T0.GroupNum AS NVARCHAR(254))
LEFT JOIN MLT1 T2
    ON T2.TranEntry = T1.TranEntry
WHERE T0.GroupNum = 68;
```

如果第一段完全查不到，再放寬條件確認 SAP 實際記錄的名稱：

```sql
SELECT
    T0.TranEntry,
    T0.TableName,
    T0.FieldAlias,
    T0.PK,
    T1.LangCode,
    T1.Trans
FROM OMLT T0
LEFT JOIN MLT1 T1
    ON T1.TranEntry = T0.TranEntry
WHERE T0.PK = '68'
   OR T1.Trans = '180days';
```

若這段仍沒有資料，較可能是翻譯視窗尚未真正儲存，例如按了「確定」後，付款條件主畫面還需要再按一次「更新／確定」。翻譯的用途主要是依商業夥伴語言顯示或列印文件，不是取代原生說明。[SAP 翻譯視窗說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/4506b8487d720487e10000000a155369.html?locale=en-US&state=PRODUCTION&version=9.3)

---

<a id="q2"></a>

## S038-Q002｜這是我的匯入格式

日期：2026-08-03

**你的提問**

# Files mentioned by the user:

## codex-clipboard-497b8bb6-7206-4315-a344-423b434d81d6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-497b8bb6-7206-4315-a344-423b434d81d6.png

## codex-clipboard-ba41ed69-abb5-4bf9-a57b-364d087605cb.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-ba41ed69-abb5-4bf9-a57b-364d087605cb.png

## My request for Codex:
這是我的匯入格式

[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**當時回答**

這兩張就是 DTW 的多語言翻譯匯入格式：

- 第一張：`OMLT` 翻譯主檔
- 第二張：`MLT1` 各語言的翻譯明細

以付款條件代碼 `68`、英文翻譯 `180days` 為例，可以這樣填。

OMLT 主檔：

| Numerator | TableName | FieldAlias | PrimaryKeyofobject |
|---:|---|---|---:|
| 1 | OCTG | PymntGroup | 68 |

保留第二列系統欄位名稱：

```text
TranEntry | TableName | FieldAlias | PK
```

MLT1 明細：

| ParentKey | LineNum | LanguageCodeOfUserLanguage | Translationscontent |
|---:|---:|---:|---|
| 1 | 0 | 英文的 LangCode | 180days |

保留第二列系統欄位名稱：

```text
Numerator | LineNum | LangCode | Trans
```

關鍵點是：

- `ParentKey = 1` 必須對應主檔的 `Numerator = 1`
- `TableName` 填 `OCTG`
- `FieldAlias` 填 `PymntGroup`
- `PK` 填付款條件的內碼 `GroupNum`，你的案例就是 `68`
- `LineNum` 第一筆通常填 `0`
- `LangCode` 不是自己輸入 `EN` 或 `English`，要填 SAP B1 語言的數字代碼
- `Trans` 才是 `180days`

英文的實際語言代碼可以先查：

```sql
SELECT *
FROM OLNG
ORDER BY Code;
```

找到名稱為 English 的那一列，把它的 `Code` 放到 `MLT1.LangCode`。不要直接猜數字，因為自訂語言或不同環境可能不一樣。

匯入後可用這段確認：

```sql
SELECT
    T0.TranEntry,
    T0.TableName,
    T0.FieldAlias,
    T0.PK,
    T1.LineNum,
    T1.LangCode,
    T1.Trans
FROM OMLT T0
INNER JOIN MLT1 T1
    ON T1.TranEntry = T0.TranEntry
WHERE T0.TableName = 'OCTG'
  AND T0.FieldAlias = 'PymntGroup'
  AND T0.PK = '68';
```

另外要注意：如果你已經從 SAP B1 畫面手動建立過 `OCTG + PymntGroup + 68`，`OMLT` 主檔可能已經存在。此時再用 DTW 當成新增資料匯入，可能產生重複鍵錯誤；應使用 DTW 的「更新現有資料」模式，或者先查出既有的 `TranEntry` 再判斷。不要直接刪除 `OMLT/MLT1` 系統資料。

---

<a id="q3"></a>

## S038-Q003｜如果我現在要匯入 那我怎麼知道我的TranEntry 目前有甚麼

日期：2026-08-03

**你的提問**

如果我現在要匯入 那我怎麼知道我的TranEntry 目前有甚麼

**當時回答**

你可以直接查 `OMLT`，這裡面的 `TranEntry` 就是目前系統已經存在的翻譯主檔編號。

查全部現有 TranEntry：

```sql
SELECT
    T0.TranEntry,
    T0.TableName,
    T0.FieldAlias,
    T0.PK,
    T0.DataSource,
    T0.UserSign
FROM OMLT T0
ORDER BY T0.TranEntry;
```

如果只想查付款條件：

```sql
SELECT
    T0.TranEntry,
    T0.TableName,
    T0.FieldAlias,
    T0.PK,
    T1.LangCode,
    T1.LineNum,
    T1.Trans
FROM OMLT T0
LEFT JOIN MLT1 T1
    ON T1.TranEntry = T0.TranEntry
WHERE T0.TableName = 'OCTG'
ORDER BY T0.PK, T1.LangCode;
```

只查付款條件 `68`：

```sql
SELECT
    T0.TranEntry,
    T0.TableName,
    T0.FieldAlias,
    T0.PK,
    T1.LineNum,
    T1.LangCode,
    T1.Trans
FROM OMLT T0
LEFT JOIN MLT1 T1
    ON T1.TranEntry = T0.TranEntry
WHERE T0.TableName = 'OCTG'
  AND T0.FieldAlias = 'PymntGroup'
  AND T0.PK = '68';
```

假設結果是：

```text
TranEntry  TableName  FieldAlias  PK  LineNum  LangCode  Trans
125        OCTG       PymntGroup  68  0        3         180days
```

那麼你 DTW 的明細檔更新時：

```text
ParentKey = 125
LineNum   = 0
LangCode  = 3
Trans     = 180days
```

也就是 `ParentKey` 要使用資料庫實際的 `OMLT.TranEntry`。

特別注意：`TranEntry` 是 SAP 自動產生的流水號，而且 SDK 將 `Numerator/TranEntry` 定義為系統指派的唯讀鍵，[SAP 官方說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~MultiLanguageTranslations~Numerator.html)。因此：

- 更新現有翻譯：先查出真正的 `TranEntry`
- 新增翻譯：不要用 `MAX(TranEntry) + 1` 自行猜號碼
- 判斷是否已存在，應看 `TableName + FieldAlias + PK`
- 同一個翻譯主檔下，每個語言則由 `LangCode` 區分

你也可以先跑這段，列出哪些付款條件還沒有建立翻譯主檔：

```sql
SELECT
    T0.GroupNum,
    T0.PymntGroup,
    T1.TranEntry
FROM OCTG T0
LEFT JOIN OMLT T1
    ON T1.TableName = 'OCTG'
   AND T1.FieldAlias = 'PymntGroup'
   AND T1.PK = CAST(T0.GroupNum AS NVARCHAR(254))
ORDER BY T0.GroupNum;
```

其中 `TranEntry` 為 `NULL`，就代表該付款條件目前尚未建立翻譯記錄。

---

<a id="q4"></a>

## S038-Q004｜圖片／附件提問

日期：2026-08-03

**你的提問**

# Files mentioned by the user:

## codex-clipboard-3cc74ba2-9b6a-4786-a68b-0c14ca4d01bb.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3cc74ba2-9b6a-4786-a68b-0c14ca4d01bb.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個結果很清楚：

- 目前只有付款條件 `GroupNum = 68` 已建立翻譯
- 它的實際 `TranEntry = 1`
- 其他付款條件的 `TranEntry` 都是空白，代表尚未建立 `OMLT` 翻譯主檔

所以你剛才手動新增的 `180days` 已經成功寫入資料庫了。

再查明細確認：

```sql
SELECT
    T0.TranEntry,
    T0.TableName,
    T0.FieldAlias,
    T0.PK,
    T1.LineNum,
    T1.LangCode,
    T1.Trans
FROM OMLT T0
LEFT JOIN MLT1 T1
    ON T1.TranEntry = T0.TranEntry
WHERE T0.TranEntry = 1;
```

應該會看到：

```text
TranEntry = 1
TableName = OCTG
FieldAlias = PymntGroup
PK = 68
Trans = 180days
```

如果要更新這筆，DTW 檔案要用實際編號：

OMLT：

```text
Numerator | TableName | FieldAlias | PrimaryKeyofobject
1         | OCTG      | PymntGroup | 68
```

MLT1：

```text
ParentKey | LineNum | LanguageCodeOfUserLanguage | Translationscontent
1         | 0       | 英文LangCode               | 180days
```

但如果你要替 `51～67` 新增翻譯，不能因為目前最大值是 `1`，就自己填 `2、3、4…` 當作真正的 `TranEntry`。`TranEntry` 是由 SAP 自動配置的系統鍵。

比較安全的操作方式是：

1. DTW 選擇 Multi-Language Translations。
2. 使用「新增資料」模式匯入新組合。
3. OMLT 每筆提供：
   - `TableName = OCTG`
   - `FieldAlias = PymntGroup`
   - `PK = GroupNum`
4. 同時提供對應的 MLT1 語言及翻譯內容。
5. 匯入完成後，再查 `OMLT` 取得 SAP 實際產生的 `TranEntry`。

你現在不要用「更新」模式匯入 `51～67`，因為它們目前根本沒有 `TranEntry`；更新模式會找不到物件。代碼 `68` 才能用更新模式，並以 `TranEntry = 1` 作為 `ParentKey`。

---

<a id="q5"></a>

## S038-Q005｜那這樣的話

日期：2026-08-03

**你的提問**

那這樣的話

**當時回答**

對，目前狀況是：

- `68`：已存在翻譯，`TranEntry = 1`，要用「更新」。
- `51～67`：尚無 `TranEntry`，要用「新增」。

你可以繼續貼預計匯入的 OMLT／MLT1 內容，我幫你確認每個欄位應該怎麼填。

---

<a id="q6"></a>

## S038-Q006｜好

日期：2026-08-03

**你的提問**

好

**當時回答**

好，請把準備匯入的 Excel 內容或檔案貼上來。我會幫你檢查：

- OMLT 與 MLT1 的對應
- 新增／更新模式
- `Numerator` 與 `ParentKey`
- `LineNum`
- 英文 `LangCode`
- 是否可能產生重複鍵錯誤

---

<a id="q7"></a>

## S038-Q007｜所以pk值也是系統自動帶入?

日期：2026-08-03

**你的提問**

所以pk值也是系統自動帶入?

**當時回答**

不是。這裡要分成兩種鍵：

- `TranEntry`：翻譯資料的流水號，由 SAP 自動產生。
- `PK`：被翻譯資料本身的主鍵，需要你在匯入檔指定。

以付款條件為例：

```text
TableName  = OCTG
FieldAlias = PymntGroup
PK         = GroupNum
```

例如：

| 付款條件 | OCTG.GroupNum | OMLT.PK |
|---|---:|---:|
| T120-出貨後120天 | 67 | 67 |
| T180-出貨後180天 | 68 | 68 |

因此，要翻譯付款條件 `67`，OMLT 應提供：

```text
TableName  = OCTG
FieldAlias = PymntGroup
PK         = 67
```

SAP 匯入成功後才會替這組翻譯產生新的 `TranEntry`。

簡單記法：

```text
PK          = 你告訴 SAP「要翻譯哪一筆付款條件」
TranEntry   = SAP 告訴你「這組翻譯的內部編號」
```

所以 `PK` 不能留空，也不能填付款條件文字；付款條件必須填 `OCTG.GroupNum`。

---

<a id="q8"></a>

## S038-Q008｜1 Created Failed 3 Translations not found Translations not found 65171

日期：2026-08-03

**你的提問**

1 Created Failed 3  Translations not found  Translations not found 65171

**當時回答**

這個錯誤代表：DTW 有讀到第 `3` 筆 OMLT 主資料，但沒有找到能配對的 MLT1 翻譯明細。

新增時最常見的原因：

1. OMLT 的 `Numerator` 和 MLT1 的 `ParentKey` 不一致。
2. DTW 只掛了 OMLT 檔，沒有把 MLT1 掛在其子節點。
3. 新增資料時 `LineNum` 填了 `0`；建議新增時留空，更新既有明細才填實際 `LineNum`。
4. `LangCode` 不存在。
5. OMLT、MLT1 的欄位標題或第二列資料庫欄位名稱被修改。

例如新增付款條件 `67` 的翻譯，兩個檔案必須成對：

OMLT：

```text
Numerator  TableName  FieldAlias  PK
3          OCTG       PymntGroup  67
```

MLT1：

```text
ParentKey  LineNum  LangCode  Trans
3                   英文代碼   120days
```

重點：

```text
OMLT.Numerator = 3
MLT1.ParentKey = 3
```

新增時 `MLT1.LineNum` 先留白，不要填 `0`。SAP 的 DTW 官方說明也是新增子資料時 `LineNum` 留空，更新時才使用 `0、1、2…`。[SAP DTW 子物件格式說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/25bde9551224405e84e69c760e272e89/aa2d42f312a5479f8b9be4e43bbff58d.html)

另外，在 DTW 的欄位配對畫面應該呈現：

```text
MultiLanguageTranslations
├─ OMLT：選你的主檔
└─ MLT1：選你的翻譯明細檔
```

不能分兩次用「新增」匯入，因為第一次只有 OMLT、沒有 MLT1 時，就會出現 `Translations not found`。

先查英文代碼：

```sql
SELECT *
FROM OLNG
ORDER BY Code;
```

也請注意：`3` 在這次新增檔裡只是 OMLT 與 MLT1 的配對識別值，不代表匯入後資料庫的 `TranEntry` 必然是 `3`；實際 `TranEntry` 仍由 SAP 配置。匯入完成後再查 `OMLT` 確認。

如果你目前兩個檔案都是填 `3`，那下一個最可能的問題就是：MLT1 沒有掛在 DTW 的子節點，或者新增時 `LineNum` 填了值。

---

