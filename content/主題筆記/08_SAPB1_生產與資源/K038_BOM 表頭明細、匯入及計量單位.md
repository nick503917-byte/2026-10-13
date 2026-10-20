---
title: "BOM 表頭明細、匯入及計量單位"
category: "08_SAPB1_生產與資源"
updated: 2026-10-20
sources: ["S195","S249","S259"]
---

# BOM 表頭明細、匯入及計量單位

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 OITT.Code = ITT1.Father 關聯、DTW 格式／明細行號及 BOM 子物料計量單位。歷史回答指出對照 OITM.InvntryUom 與 ITT1.Uom；主檔有值但 BOM 空白時需比對兩邊資料。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S195｜Fix invalid Father column query](<../../原始問答/S195_Fix invalid Father column query.md>)：2026-09-22，1 段問答。
- [S249｜檢查匯入失敗的特殊格式](<../../原始問答/S249_檢查匯入失敗的特殊格式.md>)：2026-10-15，5 段問答。
- [S259｜查詢 SAPB1 物料計量單位來源](<../../原始問答/S259_查詢 SAPB1 物料計量單位來源.md>)：2026-10-20，1 段問答。

## 問題索引

- [01｜SQL／程式碼調整與說明](#qa1)
- [02｜幫我檢查有沒有特殊格式會導致匯入不進去](#qa2)
- [03｜| 檔案行數 | 母件料號 | 發現的問題 | | ------- | ------------ | ---------------------------------------…](#qa3)
- [04｜5 Created Failed Z002-0028V Enter valid code [ITT1.Code][line: 6] , 'CTN2.33AK2AYY' Enter …](#qa4)
- [05｜SQL／程式碼調整與說明](#qa5)
- [06｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'V'. 2). […](#qa6)
- [07｜SAPB1 物料表的計量單位是抓甚麼位置的](#qa7)

## 合併後歷史問答

<a id="qa1"></a>

### 1. SQL／程式碼調整與說明

來源：[S195-Q001](<../../原始問答/S195_Fix invalid Father column query.md#q1>)；2026-09-22

**問題**

SELECT T0.[Code],T2.[ItemName], T0.[TreeType], T0.[Qauntity], T0.[ToWH], T1.[ChildNum], T1.[Code], T1.[Quantity], T1.[Warehouse], T1.[IssueMthd] FROM [dbo].[OITT]  T0 INNER JOIN [dbo].[ITT1]  T1 ON T0.[Father] = T1.[Code] INNER JOIN OITM T2 ON T0.[Code] = T2.[ItemCode]

1\). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Invalid column name 'Father'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '後端排程工作' (OBSJ) (s) could not be prepared.

**歷史回答**

錯誤在 JOIN 條件：`Father` 是明細表 `ITT1` 的欄位，不在主檔 `OITT`。應改成 **`T0.[Code] = T1.[Father]`**。[SAP 表格關聯說明](https://community.sap.com/t5/enterprise-resource-planning-q-a/relationship-between-the-oitm-and-oitt-tables/qaq-p/12714057)

修正後 SQL：

```sql
SELECT
    T0.[Code],
    T2.[ItemName],
    T0.[TreeType],
    T0.[Qauntity],
    T0.[ToWH],
    T1.[ChildNum],
    T1.[Code],
    T1.[Quantity],
    T1.[Warehouse],
    T1.[IssueMthd]
FROM [dbo].[OITT] T0
INNER JOIN [dbo].[ITT1] T1
    ON T0.[Code] = T1.[Father]
INNER JOIN [dbo].[OITM] T2
    ON T0.[Code] = T2.[ItemCode];
```

注意：`T0.[Qauntity]` 雖然看起來拼錯，但這是 SAP 的實際欄位名稱，請保留。[OITT 官方欄位定義](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKhelp/OITT.html)

第二個 `OBSJ ... could not be prepared` 訊息可能是第一個欄位錯誤引發的連帶結果，先執行以上修正版確認。

<a id="qa2"></a>

### 2. 幫我檢查有沒有特殊格式會導致匯入不進去

來源：[S249-Q001](<../../原始問答/S249_檢查匯入失敗的特殊格式.md#q1>)；2026-10-15

**問題**

# Files mentioned by the user:

## OITT.txt: C:/Users/nick_yeh/Desktop/OITT.txt

## ITT1.txt: C:/Users/nick_yeh/Desktop/ITT1.txt



## My request:
幫我檢查有沒有特殊格式會導致匯入不進去

**歷史回答**

檢查完了：**沒有發現特殊字元或文字格式異常，但有行號問題，以及需要確認的 DTW 欄位格式。** 原檔未修改。

**1. ITT1.txt 的 `LineNum` 有明顯異常**

以下行數包含前兩行標題：

| 檔案行數 | 母件料號 | 發現的問題 |
|---|---|---|
| 37–55 | `Z002-0029V` | 19 筆明細編號為 `17–35`，不是從 `0` 開始 |
| 102 | `Z001-0004` | `LineNum` 又變成 `0`，與第 69 行重複；若按目前順序連號，應為 `33` |
| 103–137 | `Z001-0003` | 35 筆明細編號為 `1–35`，缺少 `0` |

**若是更新既有 BOM，不能直接全部重新編號**，必須對照 SAP 中原本的明細行號；目前重複的 `0` 尤其需要先處理。

**2. 標題與代碼有匯入對應風險**

- ITT1 第一行最後一欄是 `IssueMthd`，SAP API 屬性名稱是 **`IssueMethod`**；`IssueMthd` 是資料庫欄位名稱。建議對照你目前 DTW 匯出的範本，確認欄位有正確映射。[SAP 欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~ProductTrees_Lines_members.html)
- OITT 的 `Treetype` 建議依範本寫成 `TreeType`。
- 目前類型填 `P`、發料方式填 `M`。SAP API 對應名稱分別是 `iProductionTree`、`im_Manual`；**你的 DTW 是否接受單字母代碼，仍須以該版本範本及映射設定確認**，不能只憑檔案斷定一定可用。[BOM 類型](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~Enumerations~BoItemTreeTypes_EN.html)、[發料方式](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~Enumerations~BoIssueMethod_EN.html)

**檢查正常的部分：**

- OITT 共 **7 筆**、ITT1 共 **144 筆**資料。
- 都是 Tab 分隔，固定為 4 欄、7 欄，沒有缺欄或多欄。
- 全為 ASCII 字元，沒有 BOM、全形字元、隱藏控制字元或欄位前後空白。
- 換行統一為 Windows CRLF。
- 數量格式正常，沒有科學記號或千分位逗號，且全部大於零。
- ITT1 的母件料號全部能對應 OITT。

**你這次是「新增 BOM」還是「更新既有 BOM」？** 這會決定 `LineNum` 應如何處理。料號與倉庫 `VL` 是否存在於 SAP，則無法單靠這兩個檔案確認。

<a id="qa3"></a>

### 3. | 檔案行數 | 母件料號 | 發現的問題 | | ------- | ------------ | ---------------------------------------…

來源：[S249-Q002](<../../原始問答/S249_檢查匯入失敗的特殊格式.md#q2>)；2026-10-15

**問題**

| 檔案行數    | 母件料號         | 發現的問題                                        |
| ------- | ------------ | -------------------------------------------- |
| 37–55   | `Z002-0029V` | 19 筆明細編號為 `17–35`，不是從 `0` 開始                 |
| 102     | `Z001-0004`  | `LineNum` 又變成 `0`，與第 69 行重複；若按目前順序連號，應為 `33` |
| 103–137 | `Z001-0003`  | 35 筆明細編號為 `1–35`，缺少 `0`                      |

幫我修正

**歷史回答**

已修正：[下載 ITT1_fixed.txt](C:/Users/nick_yeh/Documents/ChatGPT/SAPB1_DTW/ITT1_fixed.txt)

- `Z002-0029V`：改為 `0–18`
- `Z001-0004`：最後一筆改為 `33`
- `Z001-0003`：改為 `0–34`

已驗證 144 筆資料完整、行號無重複，只修改 `LineNum`，原始檔保留。

<a id="qa4"></a>

### 4. 5 Created Failed Z002-0028V Enter valid code [ITT1.Code][line: 6] , 'CTN2.33AK2AYY' Enter …

來源：[S249-Q003](<../../原始問答/S249_檢查匯入失敗的特殊格式.md#q3>)；2026-10-15

**問題**

5 Created Failed Z002-0028V  Enter valid code  [ITT1.Code][line: 6] , 'CTN2.33AK2AYY' Enter valid code  [ITT1.Code][line: 6] , 'CTN2.33AK2AYY'65171

但我系統有這個物料

**歷史回答**

**物料已存在，仍可能出現這個錯誤；目前訊息只能確認 SAP 拒絕了這個子件代碼，不能直接判定物料不存在。**

這筆在檔案中是：
```text
Z002-0028V   5   CTN2.33AK2AYY   0.006944   4   VL   M
```
對應第 6 筆子件，`LineNum = 5` 正常，而且這筆沒有被我修改。先前檢查也確認料號沒有隱藏字元或前後空白。

建議依序確認：

1. **DTW 連線的公司資料庫，是否與你查到物料的公司相同**，尤其正式庫與測試庫。
2. **從 SAP 物料主檔複製「物料號碼」比對**。檔案中同時有 `CTN2.33AK2AYY` 和 `CTN2.33AK2KYY`，兩者只差一個字母，需確認不是查到另一個。
3. **在相同公司內，手動將 `CTN2.33AK2AYY` 選入 BOM 明細，倉庫選 `VL`，先不要儲存**：
   - 若也被拒絕：檢查物料啟用狀態、有效日期及畫面提示。
   - 若可以選入：優先檢查 DTW 欄位映射與實際選取的匯入檔案。

DTW 映射畫面應確認：

| 來源欄位 | 目標屬性 |
|---|---|
| `ItemCode` | `ItemCode` |
| `ItemType` | `ItemType` |
| `Warehouse` | `Warehouse` |
| `IssueMthd` | `IssueMethod` |

SAP 的 BOM 子件 `ItemCode` 會對應物料主檔，因此「精確料號、公司資料庫、映射」是優先排查項目。[SAP 欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~ProductTrees_Lines_members.html)

**你在同一家公司手動把這個料號加入 BOM 明細時，能成功選入嗎？**

<a id="qa5"></a>

### 5. SQL／程式碼調整與說明

來源：[S249-Q004](<../../原始問答/S249_檢查匯入失敗的特殊格式.md#q4>)；2026-10-15

**問題**

SELECT *  FROM OITT T0 INNER JOIN ITT1 T1 ON T0.[Code] = T1.[Father] WHERE T0.[Code] IN (Z006-0040V
,Z002-0029V,Z002-0028V,Z001-0093V,Z001-0004,Z001-0003,N001-0001V)

**歷史回答**

料號是文字，`IN (...)` 裡每個料號都要加單引號：

```sql
SELECT *
FROM OITT T0
INNER JOIN ITT1 T1 ON T0.[Code] = T1.[Father]
WHERE T0.[Code] IN (
    'Z006-0040V',
    'Z002-0029V',
    'Z002-0028V',
    'Z001-0093V',
    'Z001-0004',
    'Z001-0003',
    'N001-0001V'
)
ORDER BY T0.[Code], T1.[ChildNum];
```

這段查的是**已存在的 BOM 與子件**。若要確認剛才報錯的物料是否存在，請在 **DTW 連線的同一個公司資料庫**查：

```sql
SELECT ItemCode, ItemName
FROM OITM
WHERE ItemCode = 'CTN2.33AK2AYY';
```

<a id="qa6"></a>

### 6. 1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'V'. 2). […

來源：[S249-Q005](<../../原始問答/S249_檢查匯入失敗的特殊格式.md#q5>)；2026-10-15

**問題**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'V'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '後端排程工作' (OBSJ) (s) could not be prepared.

**歷史回答**

`Incorrect syntax near 'V'` 通常是料號沒有用**英文半形單引號 `'`** 包住。請把原查詢全部替換成以下內容執行：

```sql
SELECT T0.*, T1.*
FROM OITT T0
INNER JOIN ITT1 T1
    ON T0.[Code] = T1.[Father]
WHERE T0.[Code] IN (
    'Z006-0040V',
    'Z002-0029V',
    'Z002-0028V',
    'Z001-0093V',
    'Z001-0004',
    'Z001-0003',
    'N001-0001V'
)
ORDER BY T0.[Code], T1.[ChildNum];
```

第二個 `OBSJ ... could not be prepared` 訊息可能是前面語法錯誤的連帶結果，先修正第一個錯誤。

如果貼上這段仍出現相同錯誤，請貼出**查詢視窗實際執行的完整 SQL**，才能確認是否有殘留內容或引號被替換。

<a id="qa7"></a>

### 7. SAPB1 物料表的計量單位是抓甚麼位置的

來源：[S259-Q001](<../../原始問答/S259_查詢 SAPB1 物料計量單位來源.md#q1>)；2026-10-20

**問題**

# Files mentioned by the user:

## codex-clipboard-b377af90-67b7-45bc-be4a-bf11d58d8c87.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b377af90-67b7-45bc-be4a-bf11d58d8c87.png
Image attachment: true



## My request:
SAPB1 物料表的計量單位是抓甚麼位置的 

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你截圖中，BOM 明細的「計量單位」（KGS、PCS、卷等），標準是帶入**各列子物料主檔的「庫存計量單位」**。

可以到這裡查看：

**存貨 → 物料主檔資料 → 查詢該子物料 →「存貨資料」頁籤 →「庫存計量單位」**  
也可以直接點 BOM 明細物料代碼旁的黃色箭頭，開啟該物料主檔。

資料庫對應如下：

| 位置 | 欄位 |
|---|---|
| 物料主檔的庫存計量單位 | `OITM.InvntryUom` |
| BOM 明細儲存的計量單位 | `ITT1.Uom` |
| 子物料關聯 | `ITT1.Code = OITM.ItemCode` |

SAP 官方欄位定義可參考：[物料主檔 OITM](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/OITM.html)、[BOM 明細 ITT1](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/ITT1.html)。

你圖中有些列是空白，**可先檢查那些子物料的庫存計量單位是否有填**；若主檔有值、BOM 卻空白，再比對上述兩個欄位。

