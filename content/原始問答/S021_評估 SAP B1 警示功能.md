# 評估 SAP B1 警示功能

來源編號：S021  
聊天 ID：`019f91ff-e682-7551-8434-a0aff7d741a9`  
聊天起始日期（台北）：2026-07-24  
保存位置：一般本機紀錄  
問答段落：5

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S021-Q001｜SAP B1 可以 寫警世功能嗎

日期：2026-07-24

**你的提問**

SAP  B1 可以 寫警世功能嗎

**當時回答**

可以。SAP Business One（SAP B1）能製作「警示／預警」功能，常見方式包括：

- **內建警示管理**：定時查詢逾期應收、庫存不足、交期延誤等條件，並通知指定使用者。
- **核准流程**：例如訂單超過信用額度、折扣過高時，要求主管核准。
- **欄位即時提醒**：透過格式化搜尋或自訂欄位，在單據輸入時顯示提示。
- **阻止錯誤過帳**：使用 `SBO_SP_TransactionNotification` 檢查資料，不符合規則就顯示錯誤並禁止新增或更新。
- **客製化通知**：用 Add-on、Service Layer 或 Integration Framework 發送電子郵件、Teams／LINE 等外部通知。

例如「銷售訂單毛利低於 15% 就警告」：

- 只提醒：使用查詢警示。
- 必須主管同意：使用核准流程。
- 完全禁止存檔：使用 `TransactionNotification`。

如果你告訴我想警示的具體情況（例如庫存不足、客戶欠款或售價過低），我可以直接幫你設計 SQL 查詢及設定方式。

---

<a id="q2"></a>

## S021-Q002｜查詢警示可以怎麼寫

日期：2026-07-24

**你的提問**

查詢警示可以怎麼寫

**當時回答**

可以。SAP B1 的「查詢警示」原理是：**排程執行 SQL；只要查詢有回傳資料，就發送警示給指定使用者。**

以下先用常見的 **SQL Server 版 SAP B1** 示範。

### 1. 應收帳款逾期警示

```sql
SELECT
    T0.CardCode AS N'客戶編號',
    T0.CardName AS N'客戶名稱',
    T0.DocNum   AS N'發票號碼',
    T0.DocDueDate AS N'到期日',
    T0.DocTotal - T0.PaidToDate AS N'未收金額',
    DATEDIFF(DAY, T0.DocDueDate, GETDATE()) AS N'逾期天數'
FROM OINV T0
WHERE T0.DocStatus = 'O'
  AND T0.CANCELED = 'N'
  AND T0.DocDueDate < CAST(GETDATE() AS DATE)
  AND T0.DocTotal - T0.PaidToDate > 0
ORDER BY T0.DocDueDate;
```

只要存在逾期且尚未收清的 A/R Invoice，系統就會警示。

### 2. 庫存低於最低庫存量

```sql
SELECT
    T0.ItemCode AS N'品號',
    T0.ItemName AS N'品名',
    T1.WhsCode  AS N'倉庫',
    T1.OnHand   AS N'現有庫存',
    T1.IsCommited AS N'已承諾量',
    T1.OnOrder  AS N'已訂購量',
    T1.MinStock AS N'最低庫存'
FROM OITM T0
INNER JOIN OITW T1
    ON T0.ItemCode = T1.ItemCode
WHERE T0.InvntItem = 'Y'
  AND T0.FrozenFor = 'N'
  AND T1.OnHand < T1.MinStock
ORDER BY T1.WhsCode, T0.ItemCode;
```

如果要看「可用庫存」而非現有庫存，可改成：

```sql
AND (T1.OnHand - T1.IsCommited + T1.OnOrder) < T1.MinStock
```

### 3. 銷售訂單超過指定天數仍未完成

```sql
SELECT
    T0.DocNum AS N'訂單號碼',
    T0.CardCode AS N'客戶編號',
    T0.CardName AS N'客戶名稱',
    T0.DocDate AS N'訂單日期',
    T0.DocDueDate AS N'交貨日期',
    T0.DocTotal AS N'訂單總額',
    DATEDIFF(DAY, T0.DocDate, GETDATE()) AS N'未結天數'
FROM ORDR T0
WHERE T0.DocStatus = 'O'
  AND T0.CANCELED = 'N'
  AND DATEDIFF(DAY, T0.DocDate, GETDATE()) >= 7
ORDER BY T0.DocDate;
```

### 4. 客戶信用額度不足

```sql
SELECT
    T0.CardCode AS N'客戶編號',
    T0.CardName AS N'客戶名稱',
    T0.CreditLine AS N'信用額度',
    T0.Balance AS N'目前餘額',
    T0.OrdersBal AS N'未結訂單',
    T0.Balance + T0.OrdersBal AS N'信用占用',
    T0.Balance + T0.OrdersBal - T0.CreditLine AS N'超額金額'
FROM OCRD T0
WHERE T0.CardType = 'C'
  AND T0.FrozenFor = 'N'
  AND T0.CreditLine > 0
  AND T0.Balance + T0.OrdersBal > T0.CreditLine
ORDER BY N'超額金額' DESC;
```

### 設定方式

1. 在「查詢產生器／查詢管理員」建立並儲存查詢。
2. 開啟「警示管理」。
3. 建立新的使用者自訂警示。
4. 選擇剛才儲存的查詢。
5. 設定執行頻率，例如每 30 分鐘或每天上午 9 點。
6. 選擇接收警示的 SAP B1 使用者。
7. 啟用警示並測試。

警示查詢最好注意：

- 不要放需要使用者輸入的 `[%0]` 參數，因為排程無法互動輸入。
- 查詢沒有回傳資料代表不發警示。
- 回傳欄位不要太多，否則警示內容難閱讀。
- 如果同一筆資料每次排程都符合條件，就會重複警示。
- 查詢警示適合「通知」，不能可靠地阻止單據存檔；要阻止過帳應使用 `SBO_SP_TransactionNotification`。

如果你的 SAP B1 使用的是 **SAP HANA**，日期函數及引號寫法不同；例如 `GETDATE()` 通常改為 `CURRENT_DATE`，`DATEDIFF` 改為 `DAYS_BETWEEN`。

---

<a id="q3"></a>

## S021-Q003｜我是直接放在哪裡? SSMS ?

日期：2026-07-24

**你的提問**

我是直接放在哪裡? SSMS ?

**當時回答**

**不是直接放在 SSMS。**  
SSMS 主要用來撰寫、測試 SQL；測試成功後，要把查詢存進 **SAP B1 的「查詢管理員」**，再由「警示管理」排程執行。

流程如下：

1. 在 SSMS 測試

   - 連線到 SQL Server。
   - 選擇正確的 SAP B1 公司資料庫。
   - 執行 SQL，確認結果與速度正常。
   - **只使用 `SELECT`**，不要執行 `UPDATE`、`DELETE` 或直接修改 SAP 資料表。

2. 存到 SAP B1

   在 SAP B1 開啟：

   `工具 → 查詢 → 查詢產生器`

   貼上 SQL 並執行測試，接著選擇「儲存」，將它放入適當的查詢類別。

   也可以從：

   `工具 → 查詢 → 查詢管理員`

   找到及執行已儲存的查詢。

3. 設定警示

   到：

   `管理 → 警示管理`

   新增「使用者定義的警示」，選擇剛才儲存的查詢，然後設定：

   - 執行頻率
   - 接收者
   - 內部訊息或電子郵件
   - 啟用狀態

因此關係是：

`SSMS 測試 SQL → SAP B1 儲存查詢 → 警示管理排程`

另外，警示查詢不要使用 `[%0]` 這種需要人工輸入的參數，否則排程執行時沒有人可以輸入條件。選單名稱可能因 SAP B1 版本及語言而略有差異。

---

<a id="q4"></a>

## S021-Q004｜MGMEET是甚麼

日期：2026-07-29

**你的提問**

MGMEET是甚麼

**當時回答**

**MGMeet 是視訊會議軟體**，由台灣的 Meetingreat（光速會議）開發，可用來：

- 多人視訊與語音會議
- 分享簡報、畫面及桌面
- 連接 Meetingreat 的 SKY-MCU 雲端會議伺服器
- 透過電腦、Android 或 iPhone/iPad 加入會議

它**不是 SAP Business One 的標準模組，也不是 SAP B1 資料表**。如果你是在公司電腦或 SAP 伺服器上看到 `MGMeet`，比較可能是另外安裝的視訊會議程式；可從「已安裝的應用程式」查看發布者是否為 Meetingreat／光速會議。

參考：[Meetingreat 官方網站](https://public.meetingreat.com/?lang=zh-tw)、[Google Play 的 MGMeet](https://play.google.com/store/apps/details?id=com.meetingreat.mgdroid)。

---

<a id="q5"></a>

## S021-Q005｜公司會用到的東西嗎

日期：2026-07-29

**你的提問**

公司會用到的東西嗎

**當時回答**

有可能，但要看公司是否採用它。

MGMeet 主要是企業視訊會議工具，可能用於：

- 線上會議、跨部門或跨據點溝通
- 遠端教育訓練
- 客戶或供應商會議
- 分享簡報與電腦畫面
- 公司自建的視訊會議系統

它與 SAP B1 **沒有直接關係**。如果公司平常使用 Teams、Google Meet 或 Zoom，MGMeet 可能是舊系統、特定會議室設備的配套程式，甚至已不再使用。

若想確認能否移除，先看：

1. 公司是否提供 `mgmeet.io` 的會議連結。
2. 會議室設備是否使用 Meetingreat 或 SKY-MCU。
3. 「已安裝的應用程式」中的發布者與安裝日期。
4. 詢問資訊人員是否仍在使用。

在確認前，不建議直接從公司電腦或伺服器移除。

---

