# 修正 OSLP 刪除銷售人員問題

來源編號：S231  
聊天 ID：`01a1158b-06d0-7851-8c67-e5f47fbcb700`  
聊天起始日期（台北）：2026-10-07  
保存位置：一般本機紀錄  
問答段落：9

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S231-Q001｜我要刪OSLP的時候 無法移除正在使用的銷售人員

日期：2026-10-07

**你的提問**

我要刪OSLP的時候
無法移除正在使用的銷售人員

**當時回答**

你指的是 **SAP Business One 的 OSLP（銷售人員資料表）**嗎？

「無法移除正在使用的銷售人員」通常表示：**這名銷售人員仍被資料引用，不是指他目前正在登入。** 可以先檢查業務夥伴的預設銷售人員，以及相關單據、銷售機會。

處理方式：

- **只是離職或不再使用**：優先在銷售人員設定中取消「有效／Active」。OSLP 有此狀態欄位。[SAP 官方欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/OSLP.html)
- **確實要刪除**：先查出引用來源，再判斷是否能透過 SAP 介面解除；若已有歷史交易，建議保留人員資料並停用。
- 不要直接用 `DELETE FROM OSLP` 強制刪除，以免造成關聯資料不一致。

你用的是 **SQL Server 還是 HANA**？要刪除的 **SlpCode** 是多少？我可以依你的環境提供查詢引用來源的唯讀 SQL。

---

<a id="q2"></a>

## S231-Q002｜都還沒有使用過

日期：2026-10-07

**你的提問**

都還沒有使用過

**當時回答**

了解，**即使沒有開過單據，也可能已被主檔或預設設定引用**，所以出現這個訊息不一定代表有交易。

先查最常見的「業務夥伴預設銷售人員」。把 `123` 換成要刪除的 `SlpCode`，以下是唯讀查詢：

```sql
SELECT "CardCode", "CardName", "SlpCode"
FROM "OCRD"
WHERE "SlpCode" = 123;
```

如果查到資料，先在 SAP 的業務夥伴主檔中改派銷售人員，再嘗試移除。若查不到，還要檢查草稿、銷售機會、員工主檔關聯及預設設定。

你使用 **SQL Server 還是 HANA**？要刪除的 **SlpCode** 是多少？我再幫你整理對應的完整檢查 SQL。

---

<a id="q3"></a>

## S231-Q003｜都沒有 SlpCode = 4 先小規模測試

日期：2026-10-07

**你的提問**

都沒有 SlpCode = 4 先小規模測試

**當時回答**

可以，先針對 **`SlpCode = 4` 做小範圍唯讀測試**。請在發生問題的公司資料庫執行。

先確認人員資料；`EmpID` 是 OSLP 的員工關聯欄位。[SAP 欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/OSLP.html)

```sql
SELECT "SlpCode", "SlpName", "Active", "EmpID"
FROM "OSLP"
WHERE "SlpCode" = 4;
```

再查業務夥伴、草稿（含明細）與銷售機會：

```sql
SELECT 'OCRD' AS "SourceTable", COUNT(*) AS "RefCount"
FROM "OCRD" WHERE "SlpCode" = 4

UNION ALL

SELECT 'ODRF', COUNT(*)
FROM "ODRF" WHERE "SlpCode" = 4

UNION ALL

SELECT 'DRF1', COUNT(*)
FROM "DRF1" WHERE "SlpCode" = 4

UNION ALL

SELECT 'OOPR', COUNT(*)
FROM "OOPR" WHERE "SlpCode" = 4;
```

把這兩段的結果貼給我。**若全部是 0，只能排除這四個來源**，下一步再縮小檢查其他關聯。

---

<a id="q4"></a>

## S231-Q004｜給我格式貼上

日期：2026-10-07

**你的提問**

給我格式貼上

**當時回答**

執行後，直接填這個格式貼回來即可：

```text
資料庫類型：SQL Server / HANA

OSLP：
SlpCode：4
SlpName：
Active：
EmpID：（若為 NULL 請填 NULL）

引用筆數：
OCRD：
ODRF：
DRF1：
OOPR：

若執行出錯，錯誤訊息：
```

---

<a id="q5"></a>

## S231-Q005｜變成這樣

日期：2026-10-07

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a420c082-55f6-4130-800c-9fd2c4734678.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a420c082-55f6-4130-800c-9fd2c4734678.png
Image attachment: true



## My request:
變成這樣

[原對話含圖片，本文未嵌入圖片]

**當時回答**

看起來貼上時換行被移除了，變成 `4UNION ALLSELECT`，造成 SQL 語法黏在一起。

請清空原本內容，改貼下面這段**單行 SQL**，必要空格都已保留：

```sql
SELECT 'OCRD' AS "SourceTable", COUNT(*) AS "RefCount" FROM "OCRD" WHERE "SlpCode" = 4 UNION ALL SELECT 'ODRF', COUNT(*) FROM "ODRF" WHERE "SlpCode" = 4 UNION ALL SELECT 'DRF1', COUNT(*) FROM "DRF1" WHERE "SlpCode" = 4 UNION ALL SELECT 'OOPR', COUNT(*) FROM "OOPR" WHERE "SlpCode" = 4
```

執行後，把下方四筆查詢結果貼給我即可。

---

<a id="q6"></a>

## S231-Q006｜圖片／附件提問

日期：2026-10-07

**你的提問**

# Files mentioned by the user:

## codex-clipboard-d709d943-6875-461c-afb9-e62e6dadbb8e.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d709d943-6875-461c-afb9-e62e6dadbb8e.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

查詢已成功執行，但圖片中的 `RefCount` 都是空白，還不能直接確認是 0。

改用文字顯示結果，避免數字 0 被隱藏。請貼這段單行 SQL：

```sql
SELECT 'OCRD' AS "SourceTable", CASE WHEN COUNT(*) = 0 THEN 'NONE' ELSE 'FOUND' END AS "CheckResult" FROM "OCRD" WHERE "SlpCode" = 4 UNION ALL SELECT 'ODRF', CASE WHEN COUNT(*) = 0 THEN 'NONE' ELSE 'FOUND' END FROM "ODRF" WHERE "SlpCode" = 4 UNION ALL SELECT 'DRF1', CASE WHEN COUNT(*) = 0 THEN 'NONE' ELSE 'FOUND' END FROM "DRF1" WHERE "SlpCode" = 4 UNION ALL SELECT 'OOPR', CASE WHEN COUNT(*) = 0 THEN 'NONE' ELSE 'FOUND' END FROM "OOPR" WHERE "SlpCode" = 4
```

- `NONE`：該表沒有引用。
- `FOUND`：該表有引用。

另外，請分開執行這段，確認銷售人員是否關聯員工：

```sql
SELECT "SlpCode", "SlpName", "Active", "EmpID" FROM "OSLP" WHERE "SlpCode" = 4
```

貼回這兩個結果，就能接著縮小範圍。

---

<a id="q7"></a>

## S231-Q007｜圖片／附件提問

日期：2026-10-07

**你的提問**

# Files mentioned by the user:

## codex-clipboard-ef1c20d8-58d4-4bfa-9db0-2273ae93028d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-ef1c20d8-58d4-4bfa-9db0-2273ae93028d.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

確認了，四個來源都是 **`NONE`**：業務夥伴、草稿表頭、草稿明細、銷售機會都沒有引用 `SlpCode = 4`。

下一步先查這名銷售人員的員工關聯，請貼上這段單行 SQL：

```sql
SELECT "SlpCode", "SlpName", "Active", "EmpID" FROM "OSLP" WHERE "SlpCode" = 4
```

把結果截圖貼回來。目前只排除了上述四個來源，還不能確定沒有其他引用。

---

<a id="q8"></a>

## S231-Q008｜圖片／附件提問

日期：2026-10-07

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a9916b2c-b68f-49bc-80b1-e794e901521c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a9916b2c-b68f-49bc-80b1-e794e901521c.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

目前確認 **`SlpCode = 4` 已停用（Active = N）**，員工 ID 顯示空白，但仍需檢查員工主檔的 `salesPrson` 是否指向它。[SAP 欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/OHEM.html)

先執行這段單行唯讀查詢：

```sql
SELECT "empID", "lastName", "firstName", "salesPrson" FROM "OHEM" WHERE "salesPrson" = 4
```

若有資料，就是找到員工主檔的引用；若顯示「找不到資料」，回覆我「沒有」，再往正式單據檢查。

---

<a id="q9"></a>

## S231-Q009｜圖片／附件提問

日期：2026-10-07

**你的提問**

# Files mentioned by the user:

## codex-clipboard-3732af88-4415-4172-a393-28dd5f0d2f3d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3732af88-4415-4172-a393-28dd5f0d2f3d.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

找到引用了：**員工編號 `67` 的員工主檔，銷售人員仍設為 `4`**。即使沒開過單據，這筆關聯也表示它仍被使用。

請先做這個小範圍測試：

1. 點查詢結果中員工編號 **67** 旁的黃色箭頭，開啟員工主檔。
2. 找到「銷售人員」欄位，清除或選擇「無」（依介面可選項目），再按「更新」儲存。
3. 重新執行剛才的查詢，確認不再查到員工 67。
4. 回到銷售人員設定，再嘗試移除代碼 **4**。

**不用刪除員工 67，只需解除其銷售人員關聯。** 若仍無法移除，再檢查其他引用。

---

