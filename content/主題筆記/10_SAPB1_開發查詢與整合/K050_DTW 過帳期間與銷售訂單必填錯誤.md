---
title: "DTW 過帳期間與銷售訂單必填錯誤"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S033","S120"]
---

# DTW 過帳期間與銷售訂單必填錯誤

[回總覽](../../00_閱讀入口.md)

## 整合摘要

保存過帳期間鎖定與銷售訂單 Applicant／部門／運費缺值兩類匯入錯誤。錯誤碼需連同訊息及物件判讀；資料補齊或欄位映射修正後仍須重新匯入驗證。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S033｜DTW 過帳期間鎖定錯誤](<../../原始問答/S033_DTW 過帳期間鎖定錯誤.md>)：2026-07-29，1 段問答。
- [S120｜銷售訂單 DTW 缺少必填欄位](<../../原始問答/S120_銷售訂單 DTW 缺少必填欄位.md>)：2026-08-31，2 段問答。

## 問題索引

- [01｜圖片／附件提問](#qa1)
- [02｜圖片／附件提問](#qa2)
- [03｜我再會銷售訂單](#qa3)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 圖片／附件提問

來源：[S033-Q001](<../../原始問答/S033_DTW 過帳期間鎖定錯誤.md#q1>)；2026-07-29

**問題**

# Files mentioned by the user:

## codex-clipboard-27f112ed-6de6-429f-bbfb-8fb04e305d87.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-27f112ed-6de6-429f-bbfb-8fb04e305d87.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

畫面顯示匯入失敗，錯誤為：

> **Posting period locked; specify an alternative date（錯誤碼 65171）**

意思是文件使用的日期落在已鎖定的過帳期間。請在 SAP Business One：

1. 檢查匯入資料的 **Posting Date／過帳日期**。
2. 將日期改到目前開放的過帳期間；或由有權限的人員暫時解除該期間的鎖定。
3. 重新匯入資料。

若同時有 **Document Date** 或 **Due Date**，也建議確認是否落在允許的日期範圍內。

<a id="qa2"></a>

### 2. 圖片／附件提問

來源：[S120-Q001](<../../原始問答/S120_銷售訂單 DTW 缺少必填欄位.md#q1>)；2026-08-31

**問題**

# Files mentioned by the user:

## codex-clipboard-6aa9b1bd-9dab-45cd-a2e6-51daae056799.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6aa9b1bd-9dab-45cd-a2e6-51daae056799.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

The screenshot shows 182 failed record creations. Each failure reports:

> “(1901) Applicant, Applicants Department, Shipping Charge Field are required. Transaction commit unsuccessfully.”

In other words, the import is missing required values for **Applicant**, **Applicants Department**, and **Shipping Charge**.

Would you like me to troubleshoot the import, extract the error list, or explain how to fix these required fields?

<a id="qa3"></a>

### 3. 我再會銷售訂單

來源：[S120-Q002](<../../原始問答/S120_銷售訂單 DTW 缺少必填欄位.md#q2>)；2026-08-31

**問題**

我再會銷售訂單

**歷史回答**

了解，你是在**匯入銷售訂單**。畫面顯示 182 筆都建立失敗，原因是每張訂單缺少以下必填欄位：

- Applicant（申請人）
- Applicants Department（申請人部門）
- Shipping Charge（運費）

請回到銷售訂單匯入檔，找到對應欄位並補值；若這些是系統的自訂欄位（UDF），還要確認匯入範本使用的是正確欄位代碼，然後再按 **Re-Import Errors** 重匯失敗資料。

如果你把匯入用的 Excel／CSV 檔附上來，我可以直接幫你找出應填的欄位。

