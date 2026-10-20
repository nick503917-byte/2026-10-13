---
title: "分欄、合併、IF、VLOOKUP 與比對方向"
category: "13_Excel與資料整理"
updated: 2026-10-20
sources: ["S019","S093","S169","S185","S186","S255"]
---

# 分欄、合併、IF、VLOOKUP 與比對方向

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 Excel 字串分欄、欄位加空格、IF 公式與 VLOOKUP。比對須先指定主表：原案例最後以工作表2為主，81 筆使用者中有 55 筆未出現在第一頁員工代碼；不能沿用先前反向比對數字。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S019｜分離 Excel 三個欄位](<../../原始問答/S019_分離 Excel 三個欄位.md>)：2026-07-23，11 段問答。
- [S093｜轉成直式文字檔](<../../原始問答/S093_轉成直式文字檔.md>)：2026-08-20，2 段問答。
- [S169｜合併兩個欄位並加入空格](<../../原始問答/S169_合併兩個欄位並加入空格.md>)：2026-09-14，1 段問答。
- [S185｜Fix IF formula error](<../../原始問答/S185_Fix IF formula error.md>)：2026-09-17，1 段問答。
- [S186｜Fix IF formula error](<../../原始問答/S186_Fix IF formula error.md>)：2026-09-17，1 段問答。
- [S255｜說明 Excel VLOOKUP 用法](<../../原始問答/S255_說明 Excel VLOOKUP 用法.md>)：2026-10-19，6 段問答。

## 問題索引

- [01｜excel 如果我想把這三個字段分出來](#qa1)
- [02｜1 Created Failed A1 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert th…](#qa2)
- [03｜我是要匯入 用dtw](#qa3)
- [04｜這個版本可以幫我向下填嗎 我要1234567](#qa4)
- [05｜只能手動往下嗎](#qa5)
- [06｜我在匯入dtw UDT 的時候 出現這個ERROR 7 Created Failed A1 [Microsoft][ODBC Driver 18 for SQL Server][S…](#qa6)
- [07｜我用文字檔匯入的](#qa7)
- [08｜4 Created Failed Code Enter valid code Enter valid code 65171](#qa8)
- [09｜圖片／附件提問](#qa9)
- [10｜EXCEL 中間有空格 我要把資料中間都沒有空格怎麼輟](#qa10)
- [11｜圖片／附件提問](#qa11)
- [12｜轉成 直式的文字檔給我](#qa12)
- [13｜我要EXCEL](#qa13)
- [14｜我想要把這兩個欄位合併後 中間有空格](#qa14)
- [15｜IF(S80=0,IF(R80<0,R80,0),0) EXCEO](#qa15)
- [16｜IF(S80=0,IF(R80<0,R80,0),0) EXCEL攻勢](#qa16)
- [17｜EXCEL VLOOK UP 怎麼使用](#qa17)
- [18｜我要把更新員工主檔 頁籤中員工代碼 跟 工作表1的員工代碼MAPPING後 帶出員工號碼在更新員工主檔 頁籤中員工代碼 的隔壁](#qa18)
- [19｜圖片／附件提問](#qa19)
- [20｜幫我把 工作表2 的 使用者代碼跟 第一個頁籤的員工代碼做MAPPING](#qa20)
- [21｜我要知道哪一些是沒有的](#qa21)
- [22｜我要的是 工作表2 的](#qa22)

## 合併後歷史問答

<a id="qa1"></a>

### 1. excel 如果我想把這三個字段分出來

來源：[S019-Q001](<../../原始問答/S019_分離 Excel 三個欄位.md#q1>)；2026-07-23

**問題**

# Files mentioned by the user:

## codex-clipboard-3c49680f-b4e0-4af1-bc31-b3315bf205ae.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3c49680f-b4e0-4af1-bc31-b3315bf205ae.png

## My request for Codex:
 excel 如果我想把這三個字段分出來

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa2"></a>

### 2. 1 Created Failed A1 [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert th…

來源：[S019-Q002](<../../原始問答/S019_分離 Excel 三個欄位.md#q2>)；2026-07-23

**問題**

1 Created Failed A1  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails. [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails.65171

**歷史回答**

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

<a id="qa3"></a>

### 3. 我是要匯入 用dtw

來源：[S019-Q003](<../../原始問答/S019_分離 Excel 三個欄位.md#q3>)；2026-07-23

**問題**

我是要匯入 用dtw

**歷史回答**

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

<a id="qa4"></a>

### 4. 這個版本可以幫我向下填嗎 我要1234567

來源：[S019-Q004](<../../原始問答/S019_分離 Excel 三個欄位.md#q4>)；2026-07-23

**問題**

# Files mentioned by the user:

## codex-clipboard-08ca4ed8-e8eb-45c9-8277-48de984dcab8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-08ca4ed8-e8eb-45c9-8277-48de984dcab8.png

## My request for Codex:
 這個版本可以幫我向下填嗎 我要1234567

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa5"></a>

### 5. 只能手動往下嗎

來源：[S019-Q005](<../../原始問答/S019_分離 Excel 三個欄位.md#q5>)；2026-07-23

**問題**

只能手動往下嗎

**歷史回答**

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

<a id="qa6"></a>

### 6. 我在匯入dtw UDT 的時候 出現這個ERROR 7 Created Failed A1 [Microsoft][ODBC Driver 18 for SQL Server][S…

來源：[S019-Q006](<../../原始問答/S019_分離 Excel 三個欄位.md#q6>)；2026-07-23

**問題**

我在匯入dtw UDT 的時候 出現這個ERROR 
7 Created Failed A1  [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails. [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Cannot insert the value NULL into column 'LineId', table 'WellShin_VN_Test.dbo.@ITEM81'; column does not allow nulls. INSERT fails.65171

**歷史回答**

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

<a id="qa7"></a>

### 7. 我用文字檔匯入的

來源：[S019-Q007](<../../原始問答/S019_分離 Excel 三個欄位.md#q7>)；2026-07-23

**問題**

我用文字檔匯入的

**歷史回答**

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

<a id="qa8"></a>

### 8. 4 Created Failed Code Enter valid code Enter valid code 65171

來源：[S019-Q008](<../../原始問答/S019_分離 Excel 三個欄位.md#q8>)；2026-07-23

**問題**

4 Created Failed Code  Enter valid code  Enter valid code 65171

**歷史回答**

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

<a id="qa9"></a>

### 9. 圖片／附件提問

來源：[S019-Q009](<../../原始問答/S019_分離 Excel 三個欄位.md#q9>)；2026-07-23

**問題**

# Files mentioned by the user:

## codex-clipboard-2b2f3d5c-20d3-4f50-80ec-d86fa5feb8a5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2b2f3d5c-20d3-4f50-80ec-d86fa5feb8a5.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa10"></a>

### 10. EXCEL 中間有空格 我要把資料中間都沒有空格怎麼輟

來源：[S019-Q010](<../../原始問答/S019_分離 Excel 三個欄位.md#q10>)；2026-07-23

**問題**

EXCEL 中間有空格 我要把資料中間都沒有空格怎麼輟

**歷史回答**

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

<a id="qa11"></a>

### 11. 圖片／附件提問

來源：[S019-Q011](<../../原始問答/S019_分離 Excel 三個欄位.md#q11>)；2026-07-23

**問題**

# Files mentioned by the user:

## codex-clipboard-07142e1d-39d1-48bb-bb11-4aa63cb9ba24.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-07142e1d-39d1-48bb-bb11-4aa63cb9ba24.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你這張圖是「中間有空白列」，不是文字空格。可以一次刪除：

1. 只選取有編號資料的 A 欄範圍，例如 `A3:A1000`。
2. 按 `F5`。
3. 點「特殊」→ 選「空值」→「確定」。
4. 按 `Ctrl` + `-`。
5. 選「整列」→「確定」。

這樣第 42～48 列之類的空白列會全部刪除，下面資料會自動往上移。

注意：一定要先選擇用來判斷是否有資料的 A 欄，不要選整張工作表，否則其他正常的空白儲存格也可能造成整列被刪除。

<a id="qa12"></a>

### 12. 轉成 直式的文字檔給我

來源：[S093-Q001](<../../原始問答/S093_轉成直式文字檔.md#q1>)；2026-08-20

**問題**

# Files mentioned by the user:

## codex-clipboard-f2299924-e5e1-4656-a990-c4fdf20aefb6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f2299924-e5e1-4656-a990-c4fdf20aefb6.png



## My request:
轉成 直式的文字檔給我

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

已轉成直式文字檔：

[下載「圖片文字_直式.txt」](C:/Users/nick_yeh/Documents/Codex/2026-08-20/new-chat-2/outputs/圖片文字_直式.txt)

<a id="qa13"></a>

### 13. 我要EXCEL

來源：[S093-Q002](<../../原始問答/S093_轉成直式文字檔.md#q2>)；2026-08-20

**問題**

我要EXCEL

**歷史回答**

已轉成直式 Excel，內容分為「組別、代碼、說明」三欄：

[原成果：圖片文字_直式.xlsx](<C:\Users\nick_yeh\Documents\Codex\2026-08-20\new-chat-2\outputs\excel_vertical_text\圖片文字_直式.xlsx>)

<a id="qa14"></a>

### 14. 我想要把這兩個欄位合併後 中間有空格

來源：[S169-Q001](<../../原始問答/S169_合併兩個欄位並加入空格.md#q1>)；2026-09-14

**問題**

# Files mentioned by the user:

## codex-clipboard-7cac741a-206e-4ae4-b2c4-4a79880bc77b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7cac741a-206e-4ae4-b2c4-4a79880bc77b.png



## My request:
我想要把這兩個欄位合併後 中間有空格

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

在新欄位輸入以下公式，將 J 欄和 K 欄合併，中間加一個空格：

```excel
=J2&" "&K2
```

再將公式往下填滿即可。例如：`NM 1682 PIONEER COURT 88005`。

若想在其中一欄空白時避免多餘空格，可用：

```excel
=TEXTJOIN(" ",TRUE,J2:K2)
```

<a id="qa15"></a>

### 15. IF(S80=0,IF(R80<0,R80,0),0) EXCEO

來源：[S185-Q001](<../../原始問答/S185_Fix IF formula error.md#q1>)；2026-09-17

**問題**

IF(S80=0,IF(R80<0,R80,0),0) EXCEO

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa16"></a>

### 16. IF(S80=0,IF(R80<0,R80,0),0) EXCEL攻勢

來源：[S186-Q001](<../../原始問答/S186_Fix IF formula error.md#q1>)；2026-09-17

**問題**

IF(S80=0,IF(R80<0,R80,0),0) EXCEL攻勢

**歷史回答**

這個 Excel 公式：

```excel
=IF(S80=0,IF(R80<0,R80,0),0)
```

意思是：**當 S80 等於 0，而且 R80 小於 0 時，顯示 R80；其餘情況都顯示 0。**

| S80 | R80 | 結果 |
|---:|---:|---:|
| 0 | -100 | -100 |
| 0 | 100 | 0 |
| 5 | -100 | 0 |

也可以改寫成：

```excel
=IF(AND(S80=0,R80<0),R80,0)
```

<a id="qa17"></a>

### 17. EXCEL VLOOK UP 怎麼使用

來源：[S255-Q001](<../../原始問答/S255_說明 Excel VLOOKUP 用法.md#q1>)；2026-10-19

**問題**

EXCEL VLOOK UP 怎麼使用

**歷史回答**

Excel 的 **VLOOKUP** 可以在表格第一欄找資料，再傳回同一列中指定欄位的內容。例如：用「商品編號」查「價格」。

公式語法：

```excel
=VLOOKUP(查找值, 資料範圍, 回傳第幾欄, FALSE)
```

假設 A1:C4 有以下資料：

| 商品編號 | 商品名稱 | 價格 |
|---|---|---|
| P001 | 鉛筆 | 10 |
| P002 | 原子筆 | 20 |
| P003 | 橡皮擦 | 15 |

在 **E2** 輸入 `P002`，在 **F2** 輸入：

```excel
=VLOOKUP(E2,$A$2:$C$4,3,FALSE)
```

結果會顯示 **20**。

這個公式的意思是：

- **E2**：要查找的商品編號。
- **$A$2:$C$4**：資料範圍；`$` 可避免向下複製公式時範圍跟著移動。
- **3**：回傳資料範圍的第 3 欄，也就是價格。
- **FALSE**：完全符合才回傳，一般查編號、姓名時都用這個。

**兩個常見注意事項：**

- 查找值必須在資料範圍的**最左欄**；VLOOKUP 只能往右取資料。
- 出現 `#N/A` 表示找不到符合值，可檢查多餘空白，或數字與文字格式是否一致。

如果希望找不到時顯示「查無資料」：

```excel
=IFERROR(VLOOKUP(E2,$A$2:$C$4,3,FALSE),"查無資料")
```

你可以貼上目前的欄位排列，以及想查什麼，我可以直接幫你寫公式。

<a id="qa18"></a>

### 18. 我要把更新員工主檔 頁籤中員工代碼 跟 工作表1的員工代碼MAPPING後 帶出員工號碼在更新員工主檔 頁籤中員工代碼 的隔壁

來源：[S255-Q002](<../../原始問答/S255_說明 Excel VLOOKUP 用法.md#q2>)；2026-10-19

**問題**

我要把更新員工主檔 頁籤中員工代碼 跟 工作表1的員工代碼MAPPING後 帶出員工號碼在更新員工主檔 頁籤中員工代碼 的隔壁

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa19"></a>

### 19. 圖片／附件提問

來源：[S255-Q003](<../../原始問答/S255_說明 Excel VLOOKUP 用法.md#q3>)；2026-10-19

**問題**

# Files mentioned by the user:

## 越南員工主檔_1016NE.xlsx: C:/Users/nick_yeh/Desktop/越南員工主檔_1016NE.xlsx



## My request:

**歷史回答**

已在「更新員工主檔_1016」的 **F 欄（員工代碼旁）**加入對應公式。

- 成功對應 **10 筆**。
- **19 筆**未匹配，顯示「查無資料」。
- 其中 `V000084` 與來源的 `V00084` 不一致，需確認代碼。

原始檔案保留，更新版如下：

[原成果：越南員工主檔_員工號碼對應完成.xlsx](<C:/Users/nick_yeh/Documents/Codex/2026-10-19/v/outputs/越南員工主檔_員工號碼對應完成.xlsx>)

<a id="qa20"></a>

### 20. 幫我把 工作表2 的 使用者代碼跟 第一個頁籤的員工代碼做MAPPING

來源：[S255-Q004](<../../原始問答/S255_說明 Excel VLOOKUP 用法.md#q4>)；2026-10-19

**問題**

# Files mentioned by the user:

## 越南員工主檔_1016NE.xlsx: C:/Users/nick_yeh/Desktop/越南員工主檔_1016NE.xlsx



## My request:
幫我把 工作表2 的 使用者代碼跟 第一個頁籤的員工代碼做MAPPING

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa21"></a>

### 21. 我要知道哪一些是沒有的

來源：[S255-Q005](<../../原始問答/S255_說明 Excel VLOOKUP 用法.md#q5>)；2026-10-19

**問題**

我要知道哪一些是沒有的

**歷史回答**

以第一個頁籤的「員工代碼」比對「工作表2」的「使用者代碼」，**29 人中有 26 人匹配，以下 3 人找不到：**

| 員工代碼 | 姓名 | 第一個頁籤列號 |
|---|---|---|
| V000025 | 尹志高 | 2 |
| V000084 | 阮有日明 | 3 |
| D0E2190 | 孫霞 | 13 |

以上依代碼完全一致比對，表示這 3 人的代碼未出現在「工作表2」。

[原成果：越南員工主檔_1016NE.xlsx](<C:/Users/nick_yeh/Desktop/越南員工主檔_1016NE.xlsx>)

<a id="qa22"></a>

### 22. 我要的是 工作表2 的

來源：[S255-Q006](<../../原始問答/S255_說明 Excel VLOOKUP 用法.md#q6>)；2026-10-19

**問題**

我要的是 工作表2 的

**歷史回答**

抱歉，剛才比對方向反了。以 **「工作表2」為主**，81 筆使用者代碼中，**55 筆未出現在第一個頁籤的員工代碼中**，如下：

| 使用者代碼 | 使用者名稱 |
|---|---|
| manager | manager |
| B1i | B1i |
| Workflow | Workflow |
| Support | Support |
| AlertSvc | AlertSvc |
| EDsUser | EDsUser |
| H101002 | Andy Wu |
| D0C3697 | 張玲 |
| D055696 | 黃民輝 |
| D040042 | 雷世兵 |
| D040055 | 葉金堂 |
| D040371 | 阮焱燁 |
| D040477 | 餘業航 |
| D044480 | 夏小勇 |
| D044562 | 姚曉華 |
| D047030 | 吳丹 |
| D0B5984 | 潘新蘭 |
| D0B9655 | 歐陽霞 |
| D0D1666 | 廖銘楊 |
| D0D1815 | 包建清 |
| D0E5461 | 黃光蘭 |
| D0F1639 | 高璇 |
| D0F1656 | 賴鈺霞 |
| D0P2406 | 胡慧琳 |
| H101001 | JS Wu |
| H101059 | 游嘉德 |
| H101061 | 陳淑娟 |
| H101063 | 張宣仁 |
| H101097 | 周煌慶 |
| V000007 | 阮 氏 和 |
| V000071 | 阮氏雲英 |
| H101461 | 葉鎮瑋 |
| H101280 | 邱柏翰 |
| D0P8497 | 范思倩 |
| D0D4886 | 黃雪芬 |
| D0P8425 | 王湘凝 |
| D0P8476 | 鄭曉瑩 |
| D0P8496 | 陳家琪 |
| D0P9014 | 黃亞良 |
| D051550 | 陽麗 |
| D0B6161 | 魏娟 |
| D0D9534 | 萬平林 |
| D040797 | 柯尊波 |
| D0A3474 | 王霞 |
| D0B5703 | 李杏梨 |
| D0D9719 | 李林芳 |
| D0E4893 | 張淑溶 |
| D0P8386 | 謝欣瑤 |
| D0P8567 | 李雯 |
| D0P8619 | 劉宇璇 |
| D0P8468 | 劉榮珍 |
| D0P8821 | 陳珊 |
| D0P9001 | 李知閑 |
| H101417 | Erin |
| H101435 | 黃如明 |

[原成果：越南員工主檔_1016NE.xlsx](<C:/Users/nick_yeh/Desktop/越南員工主檔_1016NE.xlsx>)

