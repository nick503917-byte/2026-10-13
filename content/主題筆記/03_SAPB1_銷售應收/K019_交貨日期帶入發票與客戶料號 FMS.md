---
title: "交貨日期帶入發票與客戶料號 FMS"
category: "03_SAPB1_銷售應收"
updated: 2026-10-20
sources: ["S121","S135"]
---

# 交貨日期帶入發票與客戶料號 FMS

[回總覽](../../00_閱讀入口.md)

## 整合摘要

整理從來源交貨單讀取日期、銷售訂單由品項帶入客戶料號的 FMS。原對話後續明確改為畫面 FMS，不能與交易通知 SP 混用；來源單據鍵與矩陣欄位需按實際畫面確認。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S121｜更新AR發票過帳日期](<../../原始問答/S121_更新AR發票過帳日期.md>)：2026-09-01，5 段問答。
- [S135｜掛接FMS抓取項目號碼](<../../原始問答/S135_掛接FMS抓取項目號碼.md>)：2026-09-07，1 段問答。

## 問題索引

- [01｜我想做SP 做在postTransaction 我的AR發票過帳日期 要抓 交貨單的交貨日期 單據只會是一對一的情況](#qa1)
- [02｜只會有一對一的情況](#qa2)
- [03｜所以如果我一對一 這樣寫也可以嗎](#qa3)
- [04｜如果我寫在 TransactionNotice 可行嗎](#qa4)
- [05｜我的AR發票過帳日期 要抓 交貨單的交貨日期 單據只會是一對一的情況 我要寫FMS](#qa5)
- [06｜我要在這個欄位掛FMS 我要抓 項目號碼主檔 欄位 OITM.U\_CustItemNo](#qa6)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 我想做SP 做在postTransaction 我的AR發票過帳日期 要抓 交貨單的交貨日期 單據只會是一對一的情況

來源：[S121-Q001](<../../原始問答/S121_更新AR發票過帳日期.md#q1>)；2026-09-01

**問題**

我想做SP 做在postTransaction 我的AR發票過帳日期 要抓 交貨單的交貨日期 

單據只會是一對一的情況

**歷史回答**

已完成可直接放入 `dbo.SBO_SP_PostTransactionNotice` 的 SQL：

[AR發票過帳日期取交貨日期_PostTransaction.sql](<C:\Users\nick_yeh\Documents\維熹卡控\AR發票過帳日期取交貨日期_PostTransaction.sql>)

邏輯如下：

- 僅處理新增 AR 發票：`object_type = 13`、`transaction_type = A`
- 透過 `INV1.BaseType = 15`、`BaseEntry` 找來源交貨單
- 將 `OINV.DocDate` 更新為 `ODLN.DocDueDate`
- 非由交貨單複製的發票不處理
- 按照一張發票對一張交貨單設計

提醒：`PostTransactionNotice` 是過帳完成後直接更新 `OINV`，可能造成發票日期與會計分錄 `OJDT.RefDate` 不一致。正式上線前建議先在測試帳套確認會計期間、分錄日期及 SAP 支援政策。

<a id="qa2"></a>

### 2. 只會有一對一的情況

來源：[S121-Q002](<../../原始問答/S121_更新AR發票過帳日期.md#q2>)；2026-09-01

**問題**

只會有一對一的情況

**歷史回答**

了解，既然固定一張 AR 發票只對應一張交貨單，目前 SQL 可直接使用。

即使發票有多筆明細，`INV1.BaseEntry` 都會指向同一張交貨單，因此取得的 `ODLN.DocDueDate` 也相同；使用 `MAX()` 只是避免多筆發票明細造成查詢回傳多列，不代表支援多張交貨單。

<a id="qa3"></a>

### 3. 所以如果我一對一 這樣寫也可以嗎

來源：[S121-Q003](<../../原始問答/S121_更新AR發票過帳日期.md#q3>)；2026-09-01

**問題**

所以如果我一對一 這樣寫也可以嗎

**歷史回答**

可以，一對一情況下這樣寫沒問題。

`MAX(D.DocDueDate)` 只是把同一張交貨單被多筆 `INV1` 明細重複查到的結果，收斂成單一日期，不會影響一對一關係。

確認欄位定義即可：

- `OINV.DocDate`：AR 發票過帳日期
- `ODLN.DocDueDate`：交貨單交貨日期
- `INV1.BaseType = 15`：來源為交貨單

不過要注意：在 `PostTransactionNotice` 更新 `OINV.DocDate`，不會同步更新已產生的會計分錄日期 `OJDT.RefDate`，兩者可能不一致。

<a id="qa4"></a>

### 4. 如果我寫在 TransactionNotice 可行嗎

來源：[S121-Q004](<../../原始問答/S121_更新AR發票過帳日期.md#q4>)；2026-09-01

**問題**

如果我寫在  `TransactionNotice`   可行嗎

**歷史回答**

如果你指的是 `SBO_SP_TransactionNotification`，技術上可以執行 `UPDATE OINV`，但不建議。

原因是 `TransactionNotification` 執行時，SAP 已經建立發票及相關會計分錄；只更新：

```sql
UPDATE OINV SET DocDate = ...
```

不保證同步更新：

- `OJDT.RefDate`（會計分錄過帳日期）
- `JDT1.RefDate`
- 稅務及其他關聯資料

因此可能造成發票與會計分錄日期不一致，而且直接修改 SAP 系統資料表通常不在原廠支援範圍內。

兩支 SP 的差異：

- `SBO_SP_TransactionNotification`：提交前執行，適合檢核與擋單。
- `SBO_SP_PostTransactionNotice`：提交後執行，適合後續處理，但同樣不適合直接變更會計過帳日期。

最安全的方式是在 AR 發票按下「新增」之前，透過 SAP B1 Add-on／UI API／DI API，把 `Documents.DocDate` 設為來源交貨單的 `DocDueDate`，讓 SAP 自己建立日期一致的發票與會計分錄。

結論：寫在 `TransactionNotification` 雖可能看到 `OINV.DocDate` 被改掉，但有帳務一致性風險，不建議正式環境這樣做。

<a id="qa5"></a>

### 5. 我的AR發票過帳日期 要抓 交貨單的交貨日期 單據只會是一對一的情況 我要寫FMS

來源：[S121-Q005](<../../原始問答/S121_更新AR發票過帳日期.md#q5>)；2026-09-01

**問題**

我的AR發票過帳日期 要抓 交貨單的交貨日期 
單據只會是一對一的情況

我要寫FMS

**歷史回答**

了解，你要的是 FMS（格式化搜尋），不需要寫在任何 Transaction SP。

SQL Server 查詢可寫：

```sql
SELECT TOP 1
       T0.DocDueDate
FROM ODLN T0
WHERE T0.DocEntry = $[$38.45.0]
```

用途：

- `ODLN.DocDueDate`：來源交貨單的「交貨日期」
- `$[$38.45.0]`：AR 發票明細目前列的來源交貨單 `BaseEntry`
- `TOP 1`：因為是一張發票對一張交貨單
- 將這支 FMS 綁在 AR 發票表頭的「過帳日期」欄位

如果發票有多筆明細，也可以寫得更明確：

```sql
SELECT TOP 1
       T0.DocDueDate
FROM ODLN T0
INNER JOIN INV1 T1
    ON T1.BaseEntry = T0.DocEntry
   AND T1.BaseType = 15
WHERE T1.DocEntry = $[$8.0.0]
```

不過新增發票尚未儲存時，`DocEntry` 可能還不存在，因此建議採用第一種，直接從畫面矩陣取得 `BaseEntry`。

注意：不同 SAP B1 版本的矩陣欄位 UID 可能不同。請開啟「檢視 → 系統資訊」，滑鼠移到發票明細的「基礎文件內部號碼」欄位，確認是否為：

```text
Item = 38
Column = 45
```

如果系統顯示的 Column 不是 `45`，把 `$[$38.45.0]` 中的 `45` 換成實際欄位 UID 即可。

<a id="qa6"></a>

### 6. 我要在這個欄位掛FMS 我要抓 項目號碼主檔 欄位 OITM.U\_CustItemNo

來源：[S135-Q001](<../../原始問答/S135_掛接FMS抓取項目號碼.md#q1>)；2026-09-07

**問題**

# Files mentioned by the user:

## codex-clipboard-019c6462-a256-4f1c-9a63-ce9f3f25826b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-019c6462-a256-4f1c-9a63-ce9f3f25826b.png



## My request:


我要在這個欄位掛FMS 我要抓 項目號碼主檔 欄位 OITM.U\_CustItemNo

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

可以，將 FMS 掛在圖片中的「客戶料號」欄位（`RDR1.SubCatNum`），依同一列的「項目號碼」抓取 `OITM.U_CustItemNo`。

**SQL Server 版：**

```sql
SELECT T0.[U_CustItemNo]
FROM OITM T0
WHERE T0.[ItemCode] = $[$38.1.0]
```

**SAP HANA 版：**

```sql
SELECT T0."U_CustItemNo"
FROM OITM T0
WHERE T0."ItemCode" = $[$38.1.0]
```

`$[$38.1.0]` 代表目前列的「項目號碼」，例如圖片中的 `0764-0091`。[SAP FMS 語法說明](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/syntax-of-formatted-search-in-sap-business-one-version-for-sap-hana/ba-p/13270247)

設定方式：

1. 將對應資料庫版本的 SQL 存到「查詢管理員」。
2. 游標放在「客戶料號」欄位，按 **Alt + Shift + F2** 開啟 FMS 設定，選取剛才儲存的查詢。
3. 啟用欄位變更時自動重新整理，觸發欄位選 **「項目號碼」**。

完成後，輸入或變更項目號碼，就會帶入該項目主檔的客戶料號。

