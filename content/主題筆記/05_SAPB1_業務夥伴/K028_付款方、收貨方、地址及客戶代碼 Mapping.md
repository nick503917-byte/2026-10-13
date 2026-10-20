---
title: "付款方、收貨方、地址及客戶代碼 Mapping"
category: "05_SAPB1_業務夥伴"
updated: 2026-10-20
sources: ["S150","S163","S230"]
---

# 付款方、收貨方、地址及客戶代碼 Mapping

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 BPAddresses 的付款／收貨地址類別、OCRD 客戶代碼映射及資料整理。Mapping 案例為 592 筆中 404 筆對應、188 筆待確認；不確定名稱不可直接選一個代碼。另保存 OCTG 轉 DTW 文字檔的紀錄。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S150｜分辨 DTW 匯入的付款方與收貨方](<../../原始問答/S150_分辨 DTW 匯入的付款方與收貨方.md>)：2026-09-09，1 段問答。
- [S163｜取得 MAPPING 資料](<../../原始問答/S163_取得 MAPPING 資料.md>)：2026-09-10，2 段問答。
- [S230｜整理資料並匯出 Excel](<../../原始問答/S230_整理資料並匯出 Excel.md>)：2026-10-07，7 段問答。

## 問題索引

- [01｜我用 DTW 匯入的話 我要怎麼分辨是付款方還是收貨方](#qa1)
- [02｜我想要MAPPING資料 關於](#qa2)
- [03｜OCRD 有完整的客戶待碼跟客戶名稱 ERP 那個只有名稱，但我想要在左邊列增加項對應的客戶代碼](#qa3)
- [04｜幫我整理後 我要excel](#qa4)
- [05｜我用txt檔匯入 變成亂碼](#qa5)
- [06｜圖片／附件提問](#qa6)
- [07｜SAP DTW](#qa7)
- [08｜幫我寫查詢 OCTG GROUPNUM是 | 11 | | -- | | 13 | | 15 | | 16 | | 18 | | 19 | | 20 | | 21 | | 23 |…](#qa8)
- [09｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keywor…](#qa9)
- [10｜你整理給我的EXCEL 沒有把欄位放進去嗎](#qa10)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 我用 DTW 匯入的話 我要怎麼分辨是付款方還是收貨方

來源：[S150-Q001](<../../原始問答/S150_分辨 DTW 匯入的付款方與收貨方.md#q1>)；2026-09-09

**問題**

# Files mentioned by the user:

## codex-clipboard-e29eaa9c-d453-4352-b745-646e69acf847.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e29eaa9c-d453-4352-b745-646e69acf847.png



## My request:
我用 DTW 匯入的話 我要怎麼分辨是付款方還是收貨方

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

在 DTW 匯入時，是用 **`BPAddresses` 地址範本裡的 `AddressType` 欄位**，區分「付款方」與「收貨方」。

| 畫面上的分類 | `AddressType` 填入值 |
|---|---|
| 付款方（Bill To） | `bo_BillTo` |
| 收貨方（Ship To） | `bo_ShipTo` |

這兩個值對應 SAP 的地址類型定義。[SAP 官方說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~Enumerations~BoAddressType_EN.html)

例如同一個客戶要匯入兩種地址，地址子表就各放一列：

| `AddressName`（地址 ID） | `AddressType` | `Street`（街道） |
|---|---|---|
| 帳單地址 | `bo_BillTo` | 帳單寄送地址 |
| 收貨倉庫 | `bo_ShipTo` | 實際送貨地址 |

**你截圖中的「Bill To」是地址 ID，也就是 `AddressName`，名稱可以自訂；真正決定歸在哪一類的是 `AddressType`。** 即使名稱寫「Bill To」，仍然要把類型填成 `bo_BillTo`。

另外，`AddressType` 不要留空，SAP 的預設地址類型是收貨方（Ship To）。[欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~BPAddresses~AddressType.html)

<a id="qa2"></a>

### 2. 我想要MAPPING資料 關於

來源：[S163-Q001](<../../原始問答/S163_取得 MAPPING 資料.md#q1>)；2026-09-10

**問題**

# Files mentioned by the user:

## OCRD_NAME.xlsx: C:\Users\nick_yeh\Downloads\Telegram Desktop\OCRD_NAME.xlsx

## ERP BillTo Customer address.xlsx: C:\Users\nick_yeh\Desktop\ERP BillTo Customer address.xlsx



## My request:
我想要MAPPING資料 關於

**歷史回答**

你想將這兩個 Excel 的哪些資料做對應（Mapping）？例如：依客戶代碼或客戶名稱，將 ERP 的帳單地址對應到 OCRD_NAME。

請補充對應條件，以及結果想保留哪些欄位。

<a id="qa3"></a>

### 3. OCRD 有完整的客戶待碼跟客戶名稱 ERP 那個只有名稱，但我想要在左邊列增加項對應的客戶代碼

來源：[S163-Q002](<../../原始問答/S163_取得 MAPPING 資料.md#q2>)；2026-09-10

**問題**

OCRD 有完整的客戶待碼跟客戶名稱

ERP 那個只有名稱，但我想要在左邊列增加項對應的客戶代碼

**歷史回答**

已在 ERP 最左側新增「客戶代碼」，保留原有資料。

- **404 筆**成功對應（忽略大小寫、空白及標點差異）。
- **188 筆**留白並標黃：180 筆找不到對應名稱、8 筆有多個代碼待確認。

[原成果：ERP BillTo Customer address_Mapped.xlsx](<C:/Users/nick_yeh/Documents/Codex/2026-09-10/new-chat/outputs/customer-mapping/ERP BillTo Customer address_Mapped.xlsx>)

<a id="qa4"></a>

### 4. 幫我整理後 我要excel

來源：[S230-Q001](<../../原始問答/S230_整理資料並匯出 Excel.md#q1>)；2026-10-07

**問題**

# Files mentioned by the user:

## OCRD - BusinessPartners.txt: C:/Users/nick_yeh/Downloads/Telegram Desktop/OCRD - BusinessPartners.txt



## My request:
幫我整理後 我要excel

**歷史回答**

已整理完成，共 17 筆資料，包含可篩選的整理表及完整原始資料，電話與代碼均保留文字格式。

[下載 Excel](outputs/business-partners/OCRD_業務夥伴整理.xlsx)

<a id="qa5"></a>

### 5. 我用txt檔匯入 變成亂碼

來源：[S230-Q002](<../../原始問答/S230_整理資料並匯出 Excel.md#q2>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-85515416-4157-4a76-8407-7e0c3ac89448.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-85515416-4157-4a76-8407-7e0c3ac89448.png
Image attachment: true

## codex-clipboard-4b423337-e6a6-4dca-9480-90e440dfb7e0.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-4b423337-e6a6-4dca-9480-90e440dfb7e0.png
Image attachment: true



## My request:
 我用txt檔匯入 變成亂碼

[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**歷史回答**

從截圖看，中文在 Excel 裡正常，但匯入後變成 `æ…` 這類字元，**很可能是 TXT 的儲存編碼與匯入程式的讀取編碼不一致**。

你這次畫面是「付款條件」，與前面提供的「業務夥伴」TXT 不同，因此還需要確認實際匯入檔的編碼，才能正確轉換。

請傳給我：

- **這次匯入付款條件的 TXT 原檔**。
- 使用哪個工具匯入？例如 **SAP DTW**，或其他匯入程式。

我可以幫你檢查並轉成適用的 TXT。請提供匯入前、中文仍正常的版本；若檔案本身已存成亂碼，單純換編碼不一定能還原。

<a id="qa6"></a>

### 6. 圖片／附件提問

來源：[S230-Q003](<../../原始問答/S230_整理資料並匯出 Excel.md#q3>)；2026-10-07

**問題**

# Files mentioned by the user:

## OCTG.txt: C:/Users/nick_yeh/Downloads/Telegram Desktop/OCTG.txt



## My request:

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa7"></a>

### 7. SAP DTW

來源：[S230-Q004](<../../原始問答/S230_整理資料並匯出 Excel.md#q4>)；2026-10-07

**問題**

SAP DTW

**歷史回答**

已確認：原檔是 **UTF-8 無 BOM**，中文內容正常；你截圖的亂碼很可能是 DTW 用不同編碼讀取造成的。

我已轉成 **Unicode UTF-16 LE（含 BOM）**，保留全部 69 筆資料、雙列標頭及 Tab 分隔，並核對轉換前後內容一致。

[下載 DTW 用 Unicode TXT](C:/Users/nick_yeh/Documents/Codex/2026-10-07/new-chat-2/outputs/OCTG_Unicode_UTF16LE.txt)

在 DTW 選擇此檔，分隔符號使用 **Tab**，先確認預覽中文正常再匯入。若原資料已匯入成亂碼，請使用「更新既有資料」，並核對 GroupNumber 對應，避免重複新增。

<a id="qa8"></a>

### 8. 幫我寫查詢 OCTG GROUPNUM是 | 11 | | -- | | 13 | | 15 | | 16 | | 18 | | 19 | | 20 | | 21 | | 23 |…

來源：[S230-Q005](<../../原始問答/S230_整理資料並匯出 Excel.md#q5>)；2026-10-07

**問題**

幫我寫查詢 OCTG   GROUPNUM是

| 11 |
| -- |
| 13 |
| 15 |
| 16 |
| 18 |
| 19 |
| 20 |
| 21 |
| 23 |
| 25 |
| 26 |
| 27 |
| 28 |
| 29 |

**歷史回答**

```sql
SELECT *
FROM OCTG
WHERE GroupNum IN (
    11, 13, 15, 16, 18, 19, 20,
    21, 23, 25, 26, 27, 28, 29
)
ORDER BY GroupNum;
```

若使用 SAP HANA：

```sql
SELECT *
FROM "OCTG"
WHERE "GroupNum" IN (
    11, 13, 15, 16, 18, 19, 20,
    21, 23, 25, 26, 27, 28, 29
)
ORDER BY "GroupNum";
```

<a id="qa9"></a>

### 9. 1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keywor…

來源：[S230-Q006](<../../原始問答/S230_整理資料並匯出 Excel.md#q6>)；2026-10-07

**問題**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near the keyword 'IN'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '後端排程工作' (OBSJ) (s) could not be prepared.

**歷史回答**

你使用的是 **SQL Server**。請先清空查詢視窗，再完整貼上這段 SQL（不要包含前後的 ``` 標記）：

```sql
SELECT T0.[GroupNum], T0.[PymntGroup]
FROM [dbo].[OCTG] T0
WHERE T0.[GroupNum] IN (11,13,15,16,18,19,20,21,23,25,26,27,28,29)
ORDER BY T0.[GroupNum];
```

這段 `IN` 語法在 SQL Server 是有效的。若仍出現相同錯誤，請貼上**查詢視窗內實際執行的完整 SQL**，才能確認是否有多餘字元或其他語句造成錯誤。

<a id="qa10"></a>

### 10. 你整理給我的EXCEL 沒有把欄位放進去嗎

來源：[S230-Q007](<../../原始問答/S230_整理資料並匯出 Excel.md#q7>)；2026-10-07

**問題**

你整理給我的EXCEL 沒有把欄位放進去嗎

**歷史回答**

有，先前整理的 **OCRD 業務夥伴 Excel** 有兩個工作表：

- **整理資料**：呈現有資料的 48 個欄位。
- **原始資料**：保留全部 323 個欄位，以及原檔的兩列欄位名稱。

但 **OCTG 付款條件** 那份，我只幫你轉成 DTW 用的 Unicode TXT，尚未製作 Excel。

你指的是 **OCRD Excel 少了欄位**，還是希望把 **OCTG 也整理成含欄位名稱的 Excel**？

