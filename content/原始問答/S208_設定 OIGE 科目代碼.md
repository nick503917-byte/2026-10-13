# 設定 OIGE 科目代碼

來源編號：S208  
聊天 ID：`01a0f010-7f20-7c91-8d76-2a80a2bb00e2`  
聊天起始日期（台北）：2026-09-30  
保存位置：一般本機紀錄  
問答段落：5

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S208-Q001｜OIGE.Series =117的時候以及 OIGE.U_ChangType = F 的時候，IGE1.AcctCode 帶入 6483

日期：2026-09-30

**你的提問**

OIGE.Series =117的時候以及 OIGE.U_ChangType = F 的時候，IGE1.AcctCode 帶入 6483

**當時回答**

若是 **SAP B1 SQL Server 的 FMS**，將以下查詢綁定到 `IGE1.AcctCode`：

```sql
SELECT '6483'
WHERE $[OIGE.Series] = 117
  AND $[OIGE.U_ChangType] = 'F'
```

兩個條件**同時成立**時，回傳科目代碼 `6483`；不符合時不回傳資料。

`$[資料表.欄位]` 用來取得目前表單的欄位值。（[SAP FMS 語法參考](https://community.sap.com/t5/enterprise-resource-planning-q-a/formatted-search-not-working/qaq-p/10943451)）

---

<a id="q2"></a>

## S208-Q002｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'OIGE.Seri…

日期：2026-09-30

**你的提問**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near 'OIGE.Series'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '使用者定義的值' (CSHS) (s) could not be prepared.

**當時回答**

這個錯誤表示 SAP 可能沒有將 `$[OIGE.Series]` 轉換成表單上的值，就直接送到 SQL Server。前一版沒有說明執行位置，抱歉。

這段是 **FMS 表單取值語法**，需要綁定在「存貨發貨」的 `IGE1.AcctCode` 欄位，再於該欄位按 **Shift + F2** 執行；不能直接當成一般 SQL 在 SSMS 執行。

你是在「查詢管理員」直接測試，還是已經綁定到 `IGE1.AcctCode` 後出現錯誤？

如果已經綁定，請提供開啟「檢視 → 系統資訊」後，滑鼠移到 **Series、U_ChangType** 兩個欄位時，狀態列顯示的 **Item UID**，我就能改成直接讀取畫面欄位的 FMS 語法。

---

<a id="q3"></a>

## S208-Q003｜我綁訂了

日期：2026-09-30

**你的提問**

我綁訂了

**當時回答**

了解，已綁定後仍出錯，就需要確認畫面欄位的識別碼，改用 `$[$ItemUID.欄號.型別]` 取值。

請開啟 SAP 的 **「檢視 → 系統資訊」**，分別將滑鼠停在以下欄位，提供左下角狀態列的 **Item UID**（或截圖）：

- 單據編號系列（`Series`）
- 異動類型（`U_ChangType`）

有這兩個值，我就能給你可直接貼上的版本，條件維持 **Series = 117 且 U_ChangType = 'F' 時帶入 6483**。

---

<a id="q4"></a>

## S208-Q004｜圖片／附件提問

日期：2026-09-30

**你的提問**

# Files mentioned by the user:

## codex-clipboard-2d9f9d23-3993-446f-8c69-3749baadc844.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2d9f9d23-3993-446f-8c69-3749baadc844.png
Image attachment: true

## codex-clipboard-2f0e180b-3c4d-419c-9f25-0e775880dd58.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2f0e180b-3c4d-419c-9f25-0e775880dd58.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**當時回答**

截圖確認：系列的 Item UID 是 `30`，異動類型是 `U_ChangeType`，原先的 `U_ChangType` 少了 **e**。

請將綁定在 `IGE1.AcctCode` 的 FMS 改成：

```sql
SELECT '6483'
WHERE $[$30.0.0] = '117'
  AND $[$U_ChangeType.0.0] = 'F'
```

兩個條件同時成立時，帶入 `6483`。

---

<a id="q5"></a>

## S208-Q005｜1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near '$30.0.0'.…

日期：2026-09-30

**你的提問**

1). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Incorrect syntax near '$30.0.0'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '後端排程工作' (OBSJ) (s) could not be prepared.

**當時回答**

這次錯誤顯示 `$[$30.0.0]` 仍未被 SAP 正確替換。先改回資料表欄位語法，並修正截圖確認的欄位名稱 **`U_ChangeType`**：

```sql
SELECT '6483'
WHERE $[OIGE.Series] = 117
  AND $[OIGE.U_ChangeType] = 'F'
```

請將查詢儲存後，在「存貨發貨」明細的**會計科目欄位**按 **Shift + F2** 測試。

如果仍報錯，請截圖「使用者定義的值」設定視窗，以及其中綁定的完整查詢。前兩次錯誤都涉及取值符號，需確認實際儲存及執行的內容，才能判斷原因。

---

