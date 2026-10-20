---
title: "FMS 畫面語法、日期與多查詢合併"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S123","S129"]
---

# FMS 畫面語法、日期與多查詢合併

[回總覽](../../00_閱讀入口.md)

## 整合摘要

整理 $[$-10.0.DATE]、主畫面／UDF 畫面的欄位引用，以及 UNION ALL 合併查詢。多 SELECT 不能只求包成一段，還要確認回傳欄位和單一回填值；此來源另含 DI API 安裝的 SLD 設定討論。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S123｜確認 FMS 多個 Select 包裝](<../../原始問答/S123_確認 FMS 多個 Select 包裝.md>)：2026-09-01，1 段問答。
- [S129｜解釋 $[$-10.0.DATE] 含義](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md>)：2026-09-03，7 段問答。

## 問題索引

- [01｜我想問 fms 可寫三個select 然後我把她包在一起嗎 有點像 (A 查詢 B 查詢 C 查詢 ) 但我只有一個查詢會有結果](#qa1)
- [02｜SQL／程式碼調整與說明](#qa2)
- [03｜如果我 1月的日期 然後 我 1 2 3 月都要用 4月 456 7月 789](#qa3)
- [04｜這樣](#qa4)
- [05｜如果我要放在 銷售報價 銷售訂單 採購單 然後我要參考 過帳日期 的話 我需要在多家甚麼嗎 還是這樣就夠了](#qa5)
- [06｜採購單 銷售報價 銷售訂單 英文簡寫怎麼說](#qa6)
- [07｜我想了解 API 或是 SERVICE LAYER 有甚麼用法或是情境 以及給我一些基本大家會用的場景](#qa7)
- [08｜圖片／附件提問](#qa8)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 我想問 fms 可寫三個select 然後我把她包在一起嗎 有點像 (A 查詢 B 查詢 C 查詢 ) 但我只有一個查詢會有結果

來源：[S123-Q001](<../../原始問答/S123_確認 FMS 多個 Select 包裝.md#q1>)；2026-09-01

**問題**

我想問 fms 可寫三個select 然後我把她包在一起嗎 

有點像 (A 查詢 B 查詢 C 查詢 ) 但我只有一個查詢會有結果

**歷史回答**

可以。若 A、B、C 三個 `SELECT` 的欄位數與型別一致，而且只有一個查詢會有資料，最直接是用 `UNION ALL` 包在一起：

```sql
SELECT 欄位1, 欄位2 FROM A查詢條件
UNION ALL
SELECT 欄位1, 欄位2 FROM B查詢條件
UNION ALL
SELECT 欄位1, 欄位2 FROM C查詢條件;
```

例如：

```sql
SELECT name, amount
FROM table_a
WHERE id = :id

UNION ALL

SELECT name, amount
FROM table_b
WHERE id = :id

UNION ALL

SELECT name, amount
FROM table_c
WHERE id = :id;
```

因為你確定只有一個查詢會有結果，建議用 `UNION ALL`，不需要讓資料庫額外去除重複資料。

如果三個查詢都是「回傳單一值」，也可以用 `COALESCE` 取第一個非 `NULL` 的結果：

```sql
SELECT COALESCE(
    (SELECT value FROM table_a WHERE id = :id),
    (SELECT value FROM table_b WHERE id = :id),
    (SELECT value FROM table_c WHERE id = :id)
) AS result;
```

差別是：

- 要回傳完整資料列：用 `UNION ALL`
- 只要一個值：用 `COALESCE`
- 三個查詢的欄位不同：需要先用別名、`NULL` 或型別轉換，把輸出欄位整理成相同格式

如果你說的 FMS 是 FileMaker，透過 `ExecuteSQL` 也能使用 `UNION ALL`；把你目前的三段查詢貼出來，我可以直接幫你合併。

<a id="qa2"></a>

### 2. SQL／程式碼調整與說明

來源：[S129-Q001](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md#q1>)；2026-09-03

**問題**

SELECT R0.[U\_DPHP\_SRD]\
FROM [@RAWMATERIALS] R0\
WHERE R0.[Code] = CONVERT(char(6), $[$-10.0.DATE], 112)

-10是甚麼意思

**歷史回答**

在 SAP Business One 的格式化搜尋（Formatted Search）語法中：

```sql
$[$-10.0.DATE]
```

`-10` 通常代表目前表單上的「過帳日期（Posting Date）」系統欄位 Item UID。

拆解如下：

- `$[...]`：取得目前 SAP B1 表單欄位值
- `$-10`：欄位的 Item UID；負號表示 SAP 系統內建欄位
- `.0`：第 0 欄（非矩陣欄位通常寫 `0`）
- `.DATE`：以日期型態讀取

因此整段 SQL 是：

1. 讀取目前表單的過帳日期。
2. 用 `CONVERT(char(6), ..., 112)` 轉成 `YYYYMM`。
3. 到 `[@RAWMATERIALS]` 使用該年月作為 `Code` 查詢 `U_DPHP_SRD`。

例如過帳日期是 `2026-09-03`，查詢條件會變成：

```sql
WHERE R0.[Code] = '202609'
```

注意：不同表單的 Item UID 可能不同，最好在 SAP B1 開啟「檢視 → 系統資訊」，將滑鼠移到日期欄位上，確認狀態列顯示的 Item UID 是否確實為 `-10`。

<a id="qa3"></a>

### 3. 如果我 1月的日期 然後 我 1 2 3 月都要用 4月 456 7月 789

來源：[S129-Q002](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md#q2>)；2026-09-03

**問題**

如果我 1月的日期 然後 我 1 2 3 月都要用 
4月 456
7月 789

**歷史回答**

你的需求是「每 3 個月共用該季第一個月的資料」：

- 1、2、3 月 → 使用 `YYYY01`
- 4、5、6 月 → 使用 `YYYY04`
- 7、8、9 月 → 使用 `YYYY07`
- 10、11、12 月 → 使用 `YYYY10`

SQL Server 可以改成：

```sql
SELECT R0.[U_DPHP_SRD]
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] = CONVERT(
    char(6),
    DATEADD(
        QUARTER,
        DATEDIFF(QUARTER, 0, $[$-10.0.DATE]),
        0
    ),
    112
)
```

例如：

| 輸入日期 | 查詢的 `Code` |
|---|---|
| 2026-01-15 | `202601` |
| 2026-02-10 | `202601` |
| 2026-03-31 | `202601` |
| 2026-04-01 | `202604` |
| 2026-07-20 | `202607` |
| 2026-12-01 | `202610` |

也就是會自動將日期換算成該季第一個月，再去 `[@RAWMATERIALS]` 查詢。

<a id="qa4"></a>

### 4. 這樣

來源：[S129-Q003](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md#q3>)；2026-09-03

**問題**

這樣

**歷史回答**

對，就是這樣。你可以直接使用：

```sql
SELECT R0.[U_DPHP_SRD]
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] = CONVERT(
    char(6),
    DATEADD(
        QUARTER,
        DATEDIFF(QUARTER, 0, $[$-10.0.DATE]),
        0
    ),
    112
)
```

對應規則：

- 1～3 月 → `YYYY01`
- 4～6 月 → `YYYY04`
- 7～9 月 → `YYYY07`
- 10～12 月 → `YYYY10`

<a id="qa5"></a>

### 5. 如果我要放在 銷售報價 銷售訂單 採購單 然後我要參考 過帳日期 的話 我需要在多家甚麼嗎 還是這樣就夠了

來源：[S129-Q004](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md#q4>)；2026-09-03

**問題**

如果我要放在 銷售報價 銷售訂單 採購單 然後我要參考 過帳日期 的話 我需要在多家甚麼嗎 還是這樣就夠了

**歷史回答**

如果是 SAP Business One 的「銷售報價、銷售訂單、採購單」，表頭的「過帳日期」通常都是 Item UID `10`，所以同一段查詢可共用，不必另外判斷單據類型。

但要修正一點：應該是 `10`，不是 `-10`。我前面把負號解釋成系統欄位標記並不正確；SAP 官方範例取得過帳日期使用 `$[$10.0.DATE]`。[SAP 格式化搜尋語法說明](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/syntax-of-formatted-search-in-sap-business-one-version-for-sap-hana/ba-p/13270247)

如果要依「過帳日期的年月」查詢，原本需求應寫：

```sql
SELECT R0.[U_DPHP_SRD]
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] =
      CONVERT(char(6), $[$10.0.DATE], 112)
```

例如過帳日期 `2026-09-03`，就查詢：

```text
202609
```

如果你要的是「該季第一個月」，才使用上一段 `DATEADD / DATEDIFF`：

```sql
SELECT R0.[U_DPHP_SRD]
FROM [@RAWMATERIALS] R0
WHERE R0.[Code] =
      CONVERT(
          char(6),
          DATEADD(
              QUARTER,
              DATEDIFF(QUARTER, 0, $[$10.0.DATE]),
              0
          ),
          112
      )
```

設定時仍要分別在三種單據上綁定格式化搜尋：

- 銷售報價
- 銷售訂單
- 採購單

查詢內容可使用同一支，但每一種單據表單都要把它指定到目標 UDF。若要更改過帳日期時自動重算，請在格式化搜尋設定中勾選「自動重新整理」，依據欄位選擇過帳日期（Item `10`）。

最後建議在每張單據開啟「檢視 → 系統資訊」，滑鼠停在過帳日期上確認狀態列顯示 `Item=10`；若你的客製表單或版本顯示不同，就要改用實際的 Item UID。

<a id="qa6"></a>

### 6. 採購單 銷售報價 銷售訂單 英文簡寫怎麼說

來源：[S129-Q005](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md#q5>)；2026-09-03

**問題**

採購單 銷售報價 銷售訂單 英文簡寫怎麼說

**歷史回答**

SAP Business One 常用英文簡寫：

- 採購單：**PO**（Purchase Order）
- 銷售報價：**SQ**（Sales Quotation）
- 銷售訂單：**SO**（Sales Order）

對應 SAP B1 資料表：

| 中文 | 英文 | 簡寫 | 表頭資料表 | 明細資料表 |
|---|---|---:|---|---|
| 採購單 | Purchase Order | PO | `OPOR` | `POR1` |
| 銷售報價 | Sales Quotation | SQ | `OQUT` | `QUT1` |
| 銷售訂單 | Sales Order | SO | `ORDR` | `RDR1` |

<a id="qa7"></a>

### 7. 我想了解 API 或是 SERVICE LAYER 有甚麼用法或是情境 以及給我一些基本大家會用的場景

來源：[S129-Q006](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md#q6>)；2026-09-03

**問題**

我想了解 API 或是 SERVICE LAYER 有甚麼用法或是情境 以及給我一些基本大家會用的場景

**歷史回答**

可以把它想成：**API 是「讓其他系統按照 SAP Business One 的規則操作資料的正式入口」；Service Layer 是 SAP B1 提供的一種 Web API。**

你目前使用的格式化搜尋（FMS）是在 SAP B1 畫面內查值；Service Layer 則能讓網站、手機、倉庫系統、電商平台或其他程式，從外部讀寫 SAP B1。

## API 與 Service Layer 是什麼？

「API」是統稱。SAP Business One 常見的 API 有：

| 技術 | 適合情境 |
|---|---|
| Service Layer | 網站、APP、電商、跨平台系統整合 |
| DI API | Windows、C#、傳統 SAP B1 Add-on |
| UI API | 操作或客製 SAP B1 用戶端畫面 |
| Integration Framework（B1iF） | 系統間流程整合、資料轉換與排程 |

Service Layer 使用標準的：

- HTTPS
- REST 概念
- OData
- JSON
- `GET / POST / PATCH / DELETE`

所以不論使用 C#、JavaScript、Python、Java 或 Power Automate 類型的平台，都比較容易呼叫。SAP 官方目前建議新開發優先考慮 OData v4，也就是 `/b1s/v2`。[SAP Service Layer 說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/60c7a0b745bd486589f05a1da77041f3.html)

## 最常見的應用情境

### 1. 電商訂單自動進入 SAP

例如客戶在購物網站下單：

```text
購物網站
  ↓
Service Layer
  ↓
SAP B1 銷售訂單（SO）
```

程式可以：

1. 檢查客戶是否存在。
2. 檢查商品資料與庫存。
3. 建立銷售訂單。
4. 將 SAP 的 `DocEntry`、單號回寫到電商平台。
5. 出貨後把狀態和物流單號傳回網站。

這是最典型的 Service Layer 場景。

---

### 2. 建立銷售報價、銷售訂單及採購單

對應的 Service Layer 資源如下：

| 中文 | 資料表 | Service Layer |
|---|---|---|
| 銷售報價 SQ | `OQUT / QUT1` | `Quotations` |
| 銷售訂單 SO | `ORDR / RDR1` | `Orders` |
| 採購單 PO | `OPOR / POR1` | `PurchaseOrders` |

例如建立銷售訂單：

```http
POST /b1s/v2/Orders
Content-Type: application/json
```

```json
{
  "CardCode": "C00001",
  "DocDate": "2026-09-03",
  "Comments": "由外部系統建立",
  "DocumentLines": [
    {
      "ItemCode": "A00001",
      "Quantity": 10,
      "WarehouseCode": "01"
    },
    {
      "ItemCode": "A00002",
      "Quantity": 5,
      "WarehouseCode": "01"
    }
  ]
}
```

SAP B1 會按照系統設定處理：

- 客戶是否有效
- 品號是否存在
- 倉庫是否存在
- 價格與稅碼
- 庫存與負庫存規則
- 會計期間
- 核准程序
- 必填欄位

這也是 Service Layer 比直接寫 SQL 安全的重要原因。

---

### 3. 查詢單據

取得 `DocEntry = 123` 的銷售訂單：

```http
GET /b1s/v2/Orders(123)
```

注意括號內通常是 **DocEntry，不是畫面上的 DocNum**。

依 SAP 單號搜尋：

```http
GET /b1s/v2/Orders?$filter=DocNum eq 100123
```

只取需要的欄位：

```http
GET /b1s/v2/Orders?$select=DocEntry,DocNum,CardCode,CardName,DocDate,DocTotal
```

查某客戶的訂單：

```http
GET /b1s/v2/Orders?$filter=CardCode eq 'C00001'
```

查詢最近的訂單：

```http
GET /b1s/v2/Orders?$select=DocEntry,DocNum,DocDate,CardCode,DocTotal
&$orderby=DocEntry desc
&$top=20
```

Service Layer 支援 OData 的篩選、排序與欄位選取，能減少不必要的傳輸量。官方也提供從 Business Partner 導航到 Orders 的關聯查詢方式。[SAP 關聯查詢範例](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/b937f33c9ba24498ab611eae12285d87.html)

---

### 4. 修改既有資料

例如修改銷售訂單備註：

```http
PATCH /b1s/v2/Orders(123)
Content-Type: application/json
```

```json
{
  "Comments": "客戶要求延後出貨"
}
```

`PATCH` 只更新你傳入的欄位；未提供的欄位通常維持不變，因此一般修改多使用 `PATCH`。

但單據能不能修改，仍受 SAP B1 狀態及商業規則限制。例如已取消、已關閉或已有後續單據的資料，不一定能任意修改。

---

### 5. 客戶與供應商主檔同步

例如 CRM 建立客戶後，自動建立 SAP 業務夥伴：

```http
POST /b1s/v2/BusinessPartners
```

```json
{
  "CardCode": "C00999",
  "CardName": "測試股份有限公司",
  "CardType": "cCustomer",
  "Phone1": "02-1234-5678",
  "EmailAddress": "service@example.com"
}
```

常見同步欄位包括：

- 客戶／供應商編號
- 名稱及統一編號
- 聯絡人
- 電話、Email
- 帳單地址、送貨地址
- 付款條件
- 業務人員
- 信用額度
- 自訂欄位 UDF

---

### 6. 庫存查詢與倉庫作業

倉庫或業務 APP 常見需求：

- 查詢各倉庫庫存量
- 查詢可用量
- 查詢批號、序號
- 建立收貨或發貨
- 建立庫存調撥
- 掃描條碼後回傳 SAP
- 顯示待揀貨單據

例如現場人員用平板掃描商品，系統透過 Service Layer 建立庫存調撥，不必回辦公室操作 SAP B1。

---

### 7. 自動產生採購單

當系統發現庫存不足時：

```text
庫存低於安全存量
  ↓
程式計算建議採購量
  ↓
Service Layer 建立 PO
  ↓
通知採購人員審核
```

也可以從已核准的銷售訂單、請購單或外部生產需求，自動建立採購單。

---

### 8. 儀表板與查詢網站

例如建立一個主管儀表板，顯示：

- 今日銷售額
- 未結銷售訂單
- 逾期應收帳款
- 庫存不足
- 本月採購金額
- 客戶訂單進度

如果只是複雜報表，有時直接使用唯讀報表資料庫或 HANA Calculation View 會更有效率；若需要操作 SAP 商業物件，才以 Service Layer 為主。

## 基本使用流程

### 第一步：登入

```http
POST https://SAP伺服器:50000/b1s/v2/Login
Content-Type: application/json
```

```json
{
  "CompanyDB": "SBODEMO",
  "UserName": "manager",
  "Password": "密碼"
}
```

登入成功後，Service Layer 會建立工作階段。後續請求需要攜帶登入後取得的 Cookie／Session。

### 第二步：呼叫功能

例如查詢品號：

```http
GET /b1s/v2/Items('A00001')
```

### 第三步：登出

```http
POST /b1s/v2/Logout
```

實務上通常由後端程式管理 Session，而不是每一筆資料都重新登入。

## Service Layer 與 SQL 的差異

假設你要建立銷售訂單：

直接對 `ORDR`、`RDR1` 執行 `INSERT` 是不應該的，因為可能繞過：

- 單號編制
- 稅額計算
- 庫存邏輯
- 會計期間檢查
- 核准程序
- 關聯單據
- SAP 內部狀態
- Transaction Notification

Service Layer 建立單據時，會走 SAP B1 的商業邏輯。官方架構說明指出，Service Layer 底層會呼叫與 DI API 相同體系的 business-object 核心，處理稅額、過帳等邏輯。[SAP 架構說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/36a5b88dce7d4d3b8c1abf1ac4e9dd2d.html)

簡單原則是：

- SQL：適合報表及唯讀查詢。
- Service Layer／DI API：適合新增、修改、取消正式 SAP 資料。
- UI API：適合控制或客製 SAP B1 畫面。
- 格式化搜尋：適合在使用者輸入單據時，自動帶入欄位值。

## 跟你目前需求最接近的應用

以你目前在處理的 SQ、SO、PO 和原料價格資料來看，可以做成：

1. 使用者建立 SQ、SO 或 PO。
2. 外部程式依過帳日期取得 `YYYYMM`。
3. 到 `@RAWMATERIALS` 使用 Code 找當月價格。
4. 將查到的值寫入單據 UDF。
5. 若缺少當月價格，就阻止建立或發出通知。

如果只需要在 SAP 畫面輸入單據時帶出一個欄位，**格式化搜尋就夠了**。如果需要批次處理、與外部系統交換資料、無人自動執行，或一次建立大量單據，才比較適合使用 **Service Layer**。

<a id="qa8"></a>

### 8. 圖片／附件提問

來源：[S129-Q007](<../../原始問答/S129_解釋 $[$-10.0.DATE] 含義.md#q7>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-5c6c0ec8-fb2a-4d3d-a5bf-de8ae7cf597b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5c6c0ec8-fb2a-4d3d-a5bf-de8ae7cf597b.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這個畫面是在安裝 SAP Business One DI API，要求你指定 **SLD（System Landscape Directory）伺服器**。

請依實際環境填寫：

- **SLD Server Name**：安裝 SAP Business One Server Tools／SLD 的伺服器名稱或 IP。
- **Port**：通常是 `40000`。
- **Enable certificate verification**：建議保留勾選。

例如 SLD 安裝在伺服器 `SAPSERVER`：

```text
SLD Server Name：SAPSERVER
Port：40000
```

或：

```text
SLD Server Name：192.168.1.10
Port：40000
```

只有在 **SLD 就安裝在你目前這台電腦** 時，才填：

```text
localhost
```

可以先用瀏覽器測試：

```text
https://SAP伺服器名稱:40000/ControlCenter
```

例如：

```text
https://192.168.1.10:40000/ControlCenter
```

能開啟 SAP Business One Control Center，通常表示主機與連接埠正確。

如果出現憑證錯誤：

1. 優先使用憑證所登記的完整伺服器名稱，不要直接用 IP。
2. 確認電腦信任該伺服器的憑證。
3. 測試環境可暫時取消 **Enable certificate verification**；正式環境不建議長期關閉。

畫面中的 **Shared Folder Location** 是另一種安裝來源選項；一般已經有可連線的 SLD，就選上面的 **SLD Server Name**，不需要選共享資料夾。最重要的是：目前的 `localhost` 是否真的是 SAP B1 伺服器；如果 SAP Server 在別台主機，就必須換成那台主機名稱或 IP。

