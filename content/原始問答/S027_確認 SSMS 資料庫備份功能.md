# 確認 SSMS 資料庫備份功能

來源編號：S027  
聊天 ID：`019fa283-07c2-73b2-a230-d72bb8e17c23`  
聊天起始日期（台北）：2026-07-27  
保存位置：一般本機紀錄  
問答段落：6

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S027-Q001｜我要備份資料庫 SSMS 有內建嗎

日期：2026-07-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-5ad88607-a4c0-483a-9ea5-8c918dfec8e6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5ad88607-a4c0-483a-9ea5-8c918dfec8e6.png

## My request for Codex:


我要備份資料庫 SSMS 有內建嗎

[原對話含圖片，本文未嵌入圖片]

**當時回答**

有，SSMS 內建 SQL Server 資料庫備份功能。

操作方式：

1. 在左側 **Object Explorer** 展開伺服器。
2. 展開 **Databases（資料庫）**。
3. 對要備份的資料庫按右鍵。
4. 選 **Tasks（工作）→ Back Up（備份）**。
5. **Backup type** 選 `Full（完整）`。
6. **Destination** 選 `Disk`，按 **Add** 設定 `.bak` 檔案路徑。
7. 按 **OK** 開始備份。

注意：

- 備份路徑是 **SQL Server 所在主機**的路徑，不是你目前遠端桌面或本機電腦的路徑。
- SQL Server 的服務帳號必須有該資料夾的寫入權限。
- 若要日後還原，可對 **Databases** 按右鍵，選 **Restore Database**。
- 正式操作前，建議不要勾選覆寫既有備份檔；每次使用帶日期的檔名，例如 `WellShin_VN_Test_20260727.bak`。

也可以在查詢視窗執行：

```sql
BACKUP DATABASE [WellShin_VN_Test]
TO DISK = N'D:\SQLBackup\WellShin_VN_Test_20260727.bak'
WITH INIT, COMPRESSION, CHECKSUM, STATS = 10;
```

完成後可驗證備份：

```sql
RESTORE VERIFYONLY
FROM DISK = N'D:\SQLBackup\WellShin_VN_Test_20260727.bak'
WITH CHECKSUM;
```

---

<a id="q2"></a>

## S027-Q002｜BACKUP DATABASE [WellShin_VN_Test] TO DISK = N'D:\SQLBackup\WellShin_VN_Test_20260727.bak'…

日期：2026-07-27

**你的提問**

BACKUP DATABASE [WellShin_VN_Test]
TO DISK = N'D:\SQLBackup\WellShin_VN_Test_20260727.bak'
WITH INIT, COMPRESSION, CHECKSUM, STATS = 10; 這是查詢嗎

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q3"></a>

## S027-Q003｜圖片／附件提問

日期：2026-07-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-813c332d-6420-42f7-a7b1-c7763f617daa.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-813c332d-6420-42f7-a7b1-c7763f617daa.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

是的，這是 SQL Server 的備份查詢。你目前錯誤的原因是 `TO DISK` 填的是「資料夾」，必須填完整的 `.bak` 檔名。

請改成：

```sql
BACKUP DATABASE [WellShin_VN_Test]
TO DISK = N'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\WellShin_VN_Test_20260727.bak'
WITH COMPRESSION, CHECKSUM, STATS = 10;
```

然後按上方 **Execute（執行）**。

幾個重點：

- 路徑最後必須包含 `WellShin_VN_Test_20260727.bak`，不能只寫 `...\Backup\`。
- 我暫時移除了 `INIT`，避免不小心覆蓋同名的既有備份。
- 備份成功後，下面 Messages 會顯示 `BACKUP DATABASE successfully processed...`。
- 檔案會存到 **SQL Server 那台電腦**的上述路徑。

完成後再執行驗證：

```sql
RESTORE VERIFYONLY
FROM DISK = N'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\WellShin_VN_Test_20260727.bak'
WITH CHECKSUM;
```

若改完仍出現 `Operating system error`，通常是資料夾不存在或 SQL Server 服務帳號沒有寫入權限。

---

<a id="q4"></a>

## S027-Q004｜我現在只是要查詢

日期：2026-07-27

**你的提問**

我現在只是要查詢

**當時回答**

了解。如果你只是要一般查詢資料，**不要執行 `BACKUP DATABASE`**，那是備份命令，不是查資料。

例如查詢資料表：

```sql
USE [WellShin_VN_Test];
GO

SELECT TOP (100) *
FROM [dbo].[你的資料表名稱];
```

如果只是想確認目前有哪些資料表：

```sql
USE [WellShin_VN_Test];
GO

SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_TYPE = 'BASE TABLE'
ORDER BY TABLE_SCHEMA, TABLE_NAME;
```

`SELECT` 是查詢資料，不會修改資料；把「你的資料表名稱」換成實際名稱後按 **Execute** 即可。

---

<a id="q5"></a>

## S027-Q005｜因為已經有人設定每日備份 我只想要查詢是否是差異備份或是完整備份

日期：2026-07-27

**你的提問**

因為已經有人設定每日備份 我只想要查詢是否是差異備份或是完整備份

**當時回答**

了解，你只想查詢既有備份類型，不會建立新備份。請執行以下唯讀查詢：

```sql
SELECT TOP (100)
    bs.database_name AS [資料庫],
    CASE bs.type
        WHEN 'D' THEN N'完整備份'
        WHEN 'I' THEN N'差異備份'
        WHEN 'L' THEN N'交易記錄備份'
        ELSE bs.type
    END AS [備份類型],
    bs.backup_start_date AS [開始時間],
    bs.backup_finish_date AS [完成時間],
    CAST(bs.backup_size / 1024.0 / 1024.0 AS DECIMAL(18,2)) AS [備份大小_MB],
    bmf.physical_device_name AS [備份檔案位置]
FROM msdb.dbo.backupset AS bs
LEFT JOIN msdb.dbo.backupmediafamily AS bmf
    ON bs.media_set_id = bmf.media_set_id
WHERE bs.database_name = N'WellShin_VN_Test'
ORDER BY bs.backup_finish_date DESC;
```

看「備份類型」欄位：

- `完整備份`：Full Backup
- `差異備份`：Differential Backup
- `交易記錄備份`：Transaction Log Backup

這段只有 `SELECT`，**只會讀取備份歷史，不會執行備份或修改資料**。

若只想看每日最新一筆，將 `TOP (100)` 改成 `TOP (1)`。注意：這查的是 SQL Server `msdb` 內的備份紀錄；如果備份紀錄曾被清除，結果可能不完整。

---

<a id="q6"></a>

## S027-Q006｜所以 我ssms 可以怎麼設定自動備份

日期：2026-07-27

**你的提問**

所以 我ssms 可以怎麼設定自動備份

**當時回答**

可以，通常用 SSMS 裡的「維護計畫」設定排程備份，但必須有 **SQL Server Agent**，而且通常要是 **Developer／Standard／Enterprise** 版；Express 版沒有 Agent。

### 設定每日完整備份

1. 左側 **Object Explorer** 展開：
   `Management（管理） → Maintenance Plans（維護計畫）`
2. 對 **Maintenance Plans** 按右鍵，選：
   **Maintenance Plan Wizard（維護計畫精靈）**
3. 輸入名稱，例如：
   `WellShin_VN_Test 每日完整備份`
4. 按 **Change（變更）** 設定排程，例如每天凌晨 `02:00`。
5. 勾選：
   **Back Up Database (Full)（備份資料庫－完整）**
6. 選擇資料庫：
   `WellShin_VN_Test`
7. 設定備份資料夾、檔案副檔名 `bak`。
8. 建議勾選：

   - **Verify backup integrity（驗證備份完整性）**
   - **Perform checksum（執行總和檢查）**
   - **Compress backup（壓縮備份）**

9. 完成精靈後，展開 **SQL Server Agent → Jobs**，確認工作已建立。
10. 確認 **SQL Server Agent** 正在執行；否則排程不會啟動。

### 若要完整＋差異備份

常見設定是分成兩個排程：

- 每週日凌晨：`完整備份（Full）`
- 週一至週六凌晨：`差異備份（Differential）`

另外，若需要時間點還原，還要安排更頻繁的 `交易記錄備份（Log）`，例如每 15～30 分鐘一次；資料庫復原模式必須是 `FULL`。

不過你提到已經有人設定每日備份，**先不要再建立第二套排程**，避免重複備份、占滿磁碟。可以先到：

`SQL Server Agent → Jobs`

查看現有工作名稱、排程和步驟；也可到：

`Management → Maintenance Plans`

看看是否已存在維護計畫。

---

