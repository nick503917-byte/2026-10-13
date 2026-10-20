# 分離 Excel 三個欄位

來源編號：S019  
聊天 ID：`019f8ce5-586e-7741-9cc8-58e60ff11efb`  
聊天起始日期（台北）：2026-07-23  
保存位置：一般本機紀錄  
問答段落：11

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S019-Q001｜excel 如果我想把這三個字段分出來

日期：2026-07-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-3c49680f-b4e0-4af1-bc31-b3315bf205ae.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3c49680f-b4e0-4af1-bc31-b3315bf205ae.png

## My request for Codex:
 excel 如果我想把這三個字段分出來

[原對話含圖片，本文未嵌入圖片]

**當時回答**

如果資料都在同一個儲存格（例如 `A2`），三個欄位之間是空格，可以用 Excel 365 公式：

```excel
=TEXTSPLIT(TRIM(A2)," ",,TRUE)
```

輸入後會自動分到三欄：

| 原始內容 | 欄位1 | 欄位2 | 欄位3 |
|---|---|---|---|
| A1 頭部 0239-6(IEC309) | A1 | 頭部 | 0239-6(IEC309) |

再向下填滿即可。

如果資料之間是 Tab 而不是空格，使用：

```excel
=TEXTSPLIT(A2,CHAR(9),,TRUE)
```

也可以不用公式：選取 A 欄 →「資料」→「資料剖析／文字分欄」→「分隔符號」→ 勾選「空格」，並勾選「將連續分隔符號視為單一分隔符號」。

---

<a id="q2"></a>

## S019-Q002｜1 Created Failed A1 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert th…

日期：2026-07-23

**你的提問**

1 Created Failed A1  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails. [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails.65171

**當時回答**

這段錯誤的重點是：

```text
Cannot insert the value NULL into column 'LineId'
```

代表匯入 `@ITEM81` 資料表時，`LineId` 欄位沒有值，但資料庫規定此欄不能為空。

請在 Excel 匯入檔中加入或補齊 `LineId`，例如同一筆主資料的明細依序編號：

```text
LineId
1
2
3
4
```

若這是 SAP Business One 的子表，通常還要確認：

- `DocEntry`／父項目鍵值有填寫且正確。
- 每組明細的 `LineId` 不重複。
- 欄位標題確實是 `LineId`，沒有拼錯或多餘空格。
- 不要只填 Excel 公式但結果為空字串。
- 如果通过 SQL 写入，`INSERT` 语句必须明确提供 `LineId`。

最後的 `65171` 比較像該筆資料或父項目的識別碼；真正造成失敗的是 `LineId = NULL`。

---

<a id="q3"></a>

## S019-Q003｜我是要匯入 用dtw

日期：2026-07-23

**你的提問**

我是要匯入 用dtw

**當時回答**

了解，你是用 SAP Business One 的 DTW 匯入。這個錯誤表示 `@ITEM81` 是 UDO 的明細子表，但 DTW 匯入檔中的 `LineId` 沒有提供值。

在 DTW 的明細檔中補上 `LineId`，每個父項目從 `1` 開始依序編號，例如：

| Code／父項目鍵值 | LineId | U_欄位1 | U_欄位2 |
|---|---:|---|---|
| A1 | 1 | 頭部 | 0239-6(IEC309) |
| A1 | 2 | 頭部 | 210 |
| A1 | 3 | 頭部 | 219306 |
| A1 | 4 | 頭部 | 230AA |

如果下一個父項目是 `A2`，可以重新從 1 開始：

| Code／父項目鍵值 | LineId |
|---|---:|
| A2 | 1 |
| A2 | 2 |

注意：

- 要使用 DTW 產生的 UDO 模板，不要自行新增資料庫欄位名稱。
- 明細檔中的父項目鍵值必須和主檔完全一致。
- `LineId` 必須是數字，不能留白。
- 同一個父項目下的 `LineId` 不能重複。
- 如果是更新現有資料，要先查出原本的 `LineId`，不可隨意重新編號，否則可能更新錯行或新增重複明細。

若模板裡完全沒有 `LineId` 欄位，請從 DTW 重新匯出該 UDO 的範本；不要直接對 SQL 表 `@ITEM81` 做一般 UDT 匯入。

---

<a id="q4"></a>

## S019-Q004｜這個版本可以幫我向下填嗎 我要1234567

日期：2026-07-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-08ca4ed8-e8eb-45c9-8277-48de984dcab8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-08ca4ed8-e8eb-45c9-8277-48de984dcab8.png

## My request for Codex:
 這個版本可以幫我向下填嗎 我要1234567

[原對話含圖片，本文未嵌入圖片]

**當時回答**

可以，這個舊版 Excel 也能向下填號碼。

如果要 `1、2、3、4、5、6、7`：

1. 在 `B3` 輸入 `1`。
2. 在 `B4` 輸入 `2`。
3. 同時選取 `B3:B4`。
4. 抓住選取範圍右下角的小黑點，向下拖曳。
5. Excel 就會自動填入 `3、4、5、6、7……`。

也可以在 `B3` 輸入公式：

```excel
=ROW()-2
```

再向下複製，結果就是從 1 開始。

不過你這張 DTW 模板的欄位是 `LineNum`，SAP Business One 通常要從 `0` 開始：

```text
0
1
2
3
4
5
6
7
```

因此針對這次 DTW，建議保留 `B3=0、B4=1、B5=2`，選取這三格後向下拖曳。這個 `LineNum` 會由 DTW 對應至資料庫的 `LineId`。

---

<a id="q5"></a>

## S019-Q005｜只能手動往下嗎

日期：2026-07-23

**你的提問**

只能手動往下嗎

**當時回答**

不用手動一格一格填，可以用公式快速產生。

你這份 DTW 的 `LineNum` 要從 `0` 開始，在 `B3` 輸入：

```excel
=ROW()-3
```

然後：

1. 按 Enter。
2. 再選取 `B3`。
3. 雙擊儲存格右下角的小黑點。

只要旁邊的 A 欄或 C 欄有連續資料，Excel 就會自動填到最後一列，結果為：

```text
0
1
2
3
4
5
6
7
```

匯入 DTW 前，建議將 B 欄公式結果轉成固定數值：

1. 複製整個 B 欄的結果。
2. 右鍵 →「選擇性貼上」。
3. 選擇「值」。

這樣 DTW 讀取時比較穩定。

---

<a id="q6"></a>

## S019-Q006｜我在匯入dtw UDT 的時候 出現這個ERROR 7 Created Failed A1 [Microsoft][ODBC Driver 18 for SQL Server][S…

日期：2026-07-23

**你的提問**

我在匯入dtw UDT 的時候 出現這個ERROR 
7 Created Failed A1  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails. [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails.65171

**當時回答**

這個錯誤就是 `LineNum` 沒有填完整。從你前面的截圖看，B 欄只有前三筆是 `0、1、2`，後面的資料列仍是空白，因此 DTW 寫入 `@ITEM81.LineId` 時得到 `NULL`。

在 `B3` 輸入：

```excel
=ROW()-3
```

然後雙擊 B3 右下角的小黑點，讓所有有資料的列都產生編號：

```text
0
1
2
3
4
5
6
7
...
```

接著務必：

1. 確認每一筆資料的 B 欄都不是空白。
2. 複製整個 B 欄。
3. 右鍵 →「選擇性貼上」→「值」。
4. 儲存檔案後關閉 Excel，再重新用 DTW 匯入。

你的模板對應應該保持：

| DTW 欄位 | 內容 |
|---|---|
| `ParentKey` | `A1` |
| `LineNum` | `0、1、2、3…` |
| `Code` | `A1` |
| `U_name` | 品號／名稱 |

另外，`@ITEM81` 看起來是 UDO 的子表，不是普通的獨立 UDT。匯入時要選擇對應的 UDO，並同時使用主檔及明細檔模板；如果直接以普通 UDT 方式匯入子表，即使填了 `LineNum`，DTW 仍可能無法正確建立 `LineId`。

---

<a id="q7"></a>

## S019-Q007｜我用文字檔匯入的

日期：2026-07-23

**你的提問**

我用文字檔匯入的

**當時回答**

了解，用文字檔匯入時不需要公式；要直接確認文字檔第 7 筆的 `LineNum` 欄位是否為空。

用記事本開啟明細 `.txt`，內容應為 Tab 分隔，類似：

```text
ParentKey	LineNum	Code	U_name
Code	LineNum	Code	U_name
A1	0	A1	0239-6(IEC309)
A1	1	A1	210
A1	2	A1	219306
A1	3	A1	230AA
A1	4	A1	260306
A1	5	A1	261
A1	6	A1	2811
```

錯誤通常是某行實際變成：

```text
A1		A1	260306
```

`A1` 後面出現兩個連續的 Tab，代表 `LineNum` 是空白，DTW 最後寫入 `LineId` 就會得到 `NULL`。

請檢查：

- 每一筆資料都有 `LineNum`，建議同一個 `ParentKey` 從 `0` 開始。
- 欄位之間使用 Tab，不是一般空格。
- 每行的欄位數量完全相同。
- 不要刪除 DTW 模板原本的兩行標題。
- `@ITEM81` 若是 UDO 子表，必須透過對應的 UDO 明細模板匯入，不能當成普通独立 UDT 匯入。

錯誤最前面的 `7 Created Failed A1` 很可能就是指出第 7 筆資料失敗，優先檢查文字檔的第 7 筆資料是否少了 `LineNum`。如果你把 `.txt` 檔上傳，我可以直接幫你找出是哪一欄或哪個 Tab 有問題。

---

<a id="q8"></a>

## S019-Q008｜4 Created Failed Code Enter valid code Enter valid code 65171

日期：2026-07-23

**你的提問**

4 Created Failed Code  Enter valid code  Enter valid code 65171

**當時回答**

這個錯誤中的關鍵是：

```text
Created Failed Code
Enter valid code
```

DTW 把文字 `Code` 當成資料匯入了，表示你的 TXT 第二行標題被當成正式資料。

你的文字檔目前可能是：

```text
ParentKey	LineNum	Code	U_name
Code	LineNum	Code	U_name
A1	0	A1	0239-6(IEC309)
```

用 TXT 匯入時，先刪除第二行，改成：

```text
ParentKey	LineNum	Code	U_name
A1	0	A1	0239-6(IEC309)
A1	1	A1	210
A1	2	A1	219306
```

也就是：

- 第一行保留 DTW 欄位名稱。
- 第二行直接開始正式資料。
- 不要再放 `Code LineNum Code U_name` 那一行。

另外確認 `ParentKey=A1` 對應的 UDO 主檔已經存在。如果主檔還沒有 `A1`，也會出現 `Enter valid code`；這時需要先匯入主檔，再匯入 `@ITEM81` 明細檔。

---

<a id="q9"></a>

## S019-Q009｜圖片／附件提問

日期：2026-07-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-2b2f3d5c-20d3-4f50-80ec-d86fa5feb8a5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2b2f3d5c-20d3-4f50-80ec-d86fa5feb8a5.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個錯誤已經不是 TXT 內容問題，而是 SAP Business One 的 DI API「查詢驗證允許清單」擋住了 DTW。SAP 官方說明：啟用 Query Validation 後，DI API 只能存取允許清單內的資料表。[SAP Help：General Settings – Services](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/450706efae742461e10000000a1553f7.html)

請用有管理員權限的 SAP B1 帳號設定：

1. 開啟 SAP Business One。
2. 進入「管理」→「系統初始化」→「一般設定」。
3. 選擇「服務」頁籤。
4. 找到「DI API Configuration」。
5. 在「Enable Query Validation」旁按 `…`。
6. 將以下資料表加入允許清單：

```text
OADM
OUSR
OUDO
UDO1
OUTB
CUFD
@ITEM81
```

如果 `@ITEM81` 是 UDO 子表，還要把它的「主表」也加入，例如：

```text
@主表名稱
@ITEM81
```

若 DTW 還有匯入其他子表，也要逐一加入。名稱應使用 SQL Server 中的實際表名，包含 `@`。

儲存後：

1. 關閉 DTW。
2. 最好重新登入 SAP B1。
3. 重新開啟 DTW。
4. 先執行 `Run Simulation`。

也可以暫時取消勾選 `Enable Query Validation` 測試，但基於安全考量，正式環境建議保留啟用，只加入 DTW 必須使用的表。這項限制是 SAP B1 10.0 FP 2405 之後常見的安全機制；允許清單設定完成後，才繼續處理先前的 `LineNum/LineId` 資料問題。

---

<a id="q10"></a>

## S019-Q010｜EXCEL 中間有空格 我要把資料中間都沒有空格怎麼輟

日期：2026-07-23

**你的提問**

EXCEL 中間有空格 我要把資料中間都沒有空格怎麼輟

**當時回答**

如果要刪除儲存格內「所有空格」，假設資料在 `A2`，使用：

```excel
=SUBSTITUTE(A2," ","")
```

例如：

```text
A1 頭部 0239-6
```

會變成：

```text
A1頭部0239-6
```

如果資料可能包含全形空格或不可見空格，可以用：

```excel
=SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(A2," ",""),"　",""),CHAR(160),"")
```

完成後向下填滿，再：

1. 複製公式結果。
2. 右鍵 →「選擇性貼上」→「值」。
3. 再存成 DTW 使用的 TXT 檔。

也可以不用公式：按 `Ctrl+H` →「尋找內容」輸入一個空格 →「取代為」留空 →「全部取代」。但如果是 DTW 的 Tab 分隔資料，不要在 TXT 裡直接刪除 Tab，否則欄位會黏在一起。

---

<a id="q11"></a>

## S019-Q011｜圖片／附件提問

日期：2026-07-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-07142e1d-39d1-48bb-bb11-4aa63cb9ba24.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-07142e1d-39d1-48bb-bb11-4aa63cb9ba24.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

你這張圖是「中間有空白列」，不是文字空格。可以一次刪除：

1. 只選取有編號資料的 A 欄範圍，例如 `A3:A1000`。
2. 按 `F5`。
3. 點「特殊」→ 選「空值」→「確定」。
4. 按 `Ctrl` + `-`。
5. 選「整列」→「確定」。

這樣第 42～48 列之類的空白列會全部刪除，下面資料會自動往上移。

注意：一定要先選擇用來判斷是否有資料的 A 欄，不要選整張工作表，否則其他正常的空白儲存格也可能造成整列被刪除。

---

