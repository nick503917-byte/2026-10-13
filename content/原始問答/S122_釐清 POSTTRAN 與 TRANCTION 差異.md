# 釐清 POSTTRAN 與 TRANCTION 差異

來源編號：S122  
聊天 ID：`01a05af2-2494-77e1-b5a5-32464938ac04`  
聊天起始日期（台北）：2026-09-01  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S122-Q001｜我有問過 POSTTRAN 跟 TRANCTION 的 差異

日期：2026-09-01

**你的提問**

我有問過 POSTTRAN 跟 TRANCTION 的 差異

**當時回答**

有，你之前在「更新AR發票過帳日期」任務中問過 `PostTransactionNotice` 的使用方式，但沒有直接詢問它和 `TransactionNotification` 的差異。

當時內容是：在 `SBO_SP_PostTransactionNotice` 中，把 AR 發票過帳日期更新成來源交貨單的交貨日期。

---

<a id="q2"></a>

## S122-Q002｜我想知道 差異

日期：2026-09-01

**你的提問**

我想知道 差異

**當時回答**

在 SAP Business One 中，通常是比較這兩支 SP：

- `SBO_SP_TransactionNotification`
- `SBO_SP_PostTransactionNotice`

核心差異是執行時機與能不能阻止交易。

| 項目 | TransactionNotification | PostTransactionNotice |
|---|---|---|
| 執行時機 | SAP 寫入交易、提交前 | 交易已成功提交後 |
| 能否阻止新增或更新 | 可以 | 不可以 |
| 能否回傳錯誤訊息 | 可以，設定 `@error`、`@error_message` | 無法讓原交易失敗 |
| 常見用途 | 必填檢查、邏輯驗證、禁止操作 | 後續更新、同步資料、寫入紀錄 |
| 發生錯誤的影響 | 整筆交易回滾 | 原始 SAP 單據通常已經成功 |
| 是否適合直接改單據資料 | 不建議 | 技術上可更新，但仍有風險 |

簡單記法：

```text
TransactionNotification = 交易前檢查，錯了可以擋
PostTransactionNotice    = 交易後處理，已經不能擋
```

例如要求 AR 發票必須填「客戶參考號碼」，應放在 `TransactionNotification`：

```sql
IF @object_type = '13' AND @transaction_type IN ('A', 'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM OINV
        WHERE DocEntry = @list_of_cols_val_tab_del
          AND ISNULL(NumAtCard, '') = ''
    )
    BEGIN
        SET @error = -1
        SET @error_message = N'請填寫客戶參考號碼'
    END
END
```

你之前要「AR 發票建立後，將 `OINV.DocDate` 改成交貨單日期」，才會考慮放在 `PostTransactionNotice`。但這有一個重要風險：發票表頭日期改了，已產生的會計分錄 `OJDT.RefDate` 不一定一起改，可能造成單據與分錄日期不一致。

因此原則上：

- 要驗證、阻止交易：用 `TransactionNotification`
- 要在成功後記錄或同步其他資料：用 `PostTransactionNotice`
- 要修改已過帳單據的核心日期或金額：兩者都不是理想方案，最好在建立單據前就帶入正確值

---

