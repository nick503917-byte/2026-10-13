# 確認 SSMS 資料庫備份

來源編號：S189  
聊天 ID：`01a0b2a6-2241-7820-8494-129eae84be33`  
聊天起始日期（台北）：2026-09-18  
保存位置：一般本機紀錄  
問答段落：21

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S189-Q001｜可以幫我確認 我的SSMS 的資料庫 有沒有在備份嗎

日期：2026-09-18

**你的提問**

可以幫我確認 我的SSMS 的資料庫 有沒有在備份嗎

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q2"></a>

## S189-Q002｜不是這台電腦

日期：2026-09-18

**你的提問**

不是這台電腦

**當時回答**

了解，是另一台電腦上的 SQL Server。剛才查到的是本機狀態，不能用來判斷你的目標資料庫。

請提供 SSMS 連線的「伺服器名稱」及要確認的「資料庫名稱」，不用提供密碼。若這裡無法連線，我可以提供查詢語法，讓你在那台 SSMS 執行，確認最近備份時間與自動備份排程。

---

<a id="q3"></a>

## S189-Q003｜給我語法

日期：2026-09-18

**你的提問**

給我語法

**當時回答**

在 SSMS 開啟「新增查詢」，執行以下語法。都是唯讀查詢，不會更動資料。

**① 查看每個資料庫最近一次成功備份時間**

```sql
SELECT
    d.name AS [資料庫名稱],
    d.recovery_model_desc AS [復原模式],
    MAX(CASE WHEN b.type = 'D' THEN b.backup_finish_date END) AS [最近完整備份],
    MAX(CASE WHEN b.type = 'I' THEN b.backup_finish_date END) AS [最近差異備份],
    MAX(CASE WHEN b.type = 'L' THEN b.backup_finish_date END) AS [最近交易記錄備份]
FROM sys.databases AS d
LEFT JOIN msdb.dbo.backupset AS b
    ON b.database_name = d.name
WHERE d.name <> N'tempdb'
GROUP BY d.name, d.recovery_model_desc
ORDER BY d.name;
```

**② 查看指定資料庫最近 20 筆備份及存放位置**

將 `你的資料庫名稱` 換成實際名稱。

```sql
SELECT TOP (20)
    b.database_name AS [資料庫名稱],
    CASE b.type
        WHEN 'D' THEN N'完整備份'
        WHEN 'I' THEN N'差異備份'
        WHEN 'L' THEN N'交易記錄備份'
        ELSE b.type
    END AS [備份類型],
    b.backup_start_date AS [開始時間],
    b.backup_finish_date AS [完成時間],
    b.is_copy_only AS [是否僅複製備份],
    m.physical_device_name AS [備份位置]
FROM msdb.dbo.backupset AS b
INNER JOIN msdb.dbo.backupmediafamily AS m
    ON m.media_set_id = b.media_set_id
WHERE b.database_name = N'你的資料庫名稱'
ORDER BY b.backup_finish_date DESC;
```

**③ 查看 SQL Server Agent 工作、排程及最近執行結果**

這會列出所有工作，方便找出備份或維護計畫。

```sql
SELECT
    j.name AS [工作名稱],
    j.enabled AS [工作啟用],
    s.name AS [排程名稱],
    s.enabled AS [排程啟用],
    CASE s.freq_type
        WHEN 1   THEN N'單次'
        WHEN 4   THEN N'每日'
        WHEN 8   THEN N'每週'
        WHEN 16  THEN N'每月'
        WHEN 32  THEN N'每月相對日期'
        WHEN 64  THEN N'Agent 啟動時'
        WHEN 128 THEN N'電腦閒置時'
    END AS [排程類型],
    CASE WHEN h.run_date > 0
        THEN msdb.dbo.agent_datetime(h.run_date, h.run_time)
    END AS [最近執行時間],
    CASE h.run_status
        WHEN 0 THEN N'失敗'
        WHEN 1 THEN N'成功'
        WHEN 2 THEN N'重試'
        WHEN 3 THEN N'取消'
        WHEN 4 THEN N'執行中'
        ELSE N'無執行紀錄'
    END AS [最近執行結果],
    h.message AS [執行訊息]
FROM msdb.dbo.sysjobs AS j
LEFT JOIN msdb.dbo.sysjobschedules AS js
    ON js.job_id = j.job_id
LEFT JOIN msdb.dbo.sysschedules AS s
    ON s.schedule_id = js.schedule_id
OUTER APPLY (
    SELECT TOP (1)
        run_date, run_time, run_status, message
    FROM msdb.dbo.sysjobhistory
    WHERE job_id = j.job_id
      AND step_id = 0
    ORDER BY instance_id DESC
) AS h
ORDER BY j.name, s.name;
```

注意：`NULL` 表示查不到備份歷史，也可能是歷史已清除；有成功紀錄也不代表備份檔目前還存在或能還原。第三段只涵蓋 SQL Server Agent 排程。

把結果貼給我，我可以幫你判斷是否持續有備份、最近有沒有失敗。

---

<a id="q4"></a>

## S189-Q004｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-7a307366-72ca-4391-acd5-39e3951a126d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7a307366-72ca-4391-acd5-39e3951a126d.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

從截圖可確認：**有設定自動備份排程，但其中一個備份工作失敗，還不能確認你的資料庫持續正常備份。**

| 備份工作 | 排程 | 最近執行時間 | 結果 |
|---|---|---|---|
| `Backup Schedule1` | 每日、已啟用 | 2026-09-02 22:59 | **失敗** |
| `Day Backup Schedule.Subplan_1` | 每日、已啟用 | 2026-09-02 22:59 | 成功 |
| `Week Backup Schedule.Subplan_1` | 每週、已啟用 | 2026-08-30 00:00 | 成功 |
| `Month Backup Schedule.Subplan_1` | 每月、已啟用 | 2026-09-01 00:20 | 成功 |

另外，`Delete Schedule` 是清除工作，成功不代表備份成功。

**如果這是今天（9/18）剛查的結果，每日備份最近卻停在 9/2，需要查明原因。** 排程啟用不代表 SQL Server Agent 目前仍在執行。

請再執行下面語法，查看失敗工作的詳細原因：

```sql
SELECT TOP (30)
    j.name AS [工作名稱],
    h.step_id AS [步驟編號],
    h.step_name AS [步驟名稱],
    msdb.dbo.agent_datetime(h.run_date, h.run_time) AS [執行時間],
    CASE h.run_status
        WHEN 0 THEN N'失敗'
        WHEN 1 THEN N'成功'
        WHEN 2 THEN N'重試'
        WHEN 3 THEN N'取消'
        WHEN 4 THEN N'執行中'
    END AS [結果],
    h.message AS [詳細訊息]
FROM msdb.dbo.sysjobhistory AS h
INNER JOIN msdb.dbo.sysjobs AS j
    ON j.job_id = h.job_id
WHERE j.name = N'Backup Schedule1'
ORDER BY h.instance_id DESC;
```

把這個結果，以及前面 **①「每個資料庫最近一次成功備份時間」** 的結果貼上來，就能進一步確認哪些資料庫有備份，以及失敗原因。

---

<a id="q5"></a>

## S189-Q005｜Backup Schedule1 1 Daily backup 1 每日 2026-09-02 22:59:00.000 失敗 The job failed. The Job wa…

日期：2026-09-18

**你的提問**

Backup Schedule1	1	Daily backup	1	每日	2026-09-02 22:59:00.000	失敗	The job failed.  The Job was invoked by Schedule 9 (Daily backup).  The last step to run was step 1 (WS\_VN\_Official Auto Backup and Delete).
Day Backup Schedule.Subplan\_1	1	Backup Schedule.Subplan\_1	1	每日	2026-09-02 22:59:00.000	成功	The job succeeded.  The Job was invoked by Schedule 10 (Backup Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Day Delete Schedule US.Subplan\_1	1	Day Delete Schedule US.Subplan\_1	1	每日	2026-09-02 23:24:00.000	成功	The job succeeded.  The Job was invoked by Schedule 16 (Day Delete Schedule US.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Day Delete Schedule VN.Subplan\_1	1	Day Delete Schedule VN.Subplan\_1	1	每日	2026-09-02 23:39:00.000	成功	The job succeeded.  The Job was invoked by Schedule 17 (Day Delete Schedule VN.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Month Backup Schedule.Subplan\_1	1	Mounth Backup Schedule.Subplan\_1	1	每月	2026-09-01 00:20:00.000	成功	The job succeeded.  The Job was invoked by Schedule 14 (Mounth Backup Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Month Delete Schedule.Subplan\_1	1	Month Delete Schedule.Subplan\_1	1	每月	2026-09-01 00:30:00.000	成功	The job succeeded.  The Job was invoked by Schedule 15 (Month Delete Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
syspolicy\_purge\_history	1	syspolicy\_purge\_history\_schedule	1	每日	2026-09-03 02:00:00.000	成功	The job succeeded.  The Job was invoked by Schedule 8 (syspolicy\_purge\_history\_schedule).  The last step to run was step 3 (Erase Phantom System Health Records.).
Week Backup Schedule.Subplan\_1	1	Week Backup Schedule.Subplan\_1	1	每週	2026-08-30 00:00:00.000	成功	The job succeeded.  The Job was invoked by Schedule 12 (Week Backup Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Week Dekete Schedule.Subplan\_1	1	Week Dekete Schedule.Subplan\_1	1	每週	2026-08-30 00:10:00.000	成功	The job succeeded.  The Job was invoked by Schedule 13 (Week Dekete Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).

**當時回答**

這份結果顯示：**有備份排程，也曾成功執行，但尚不能確認你的目標資料庫目前有正常備份。**

- 每日備份工作 `Day Backup Schedule.Subplan_1`：最近在 **9/2 22:59 成功**。
- `Backup Schedule1`：同時間**失敗**，最後執行的步驟是 `WS_VN_Official Auto Backup and Delete`。目前訊息只有工作摘要，沒有實際錯誤原因。
- 如果是今天（9/18）查詢，每日工作紀錄停在 9/2，需進一步確認是否已停止執行。

請一次執行以下兩段，把結果貼給我：

**1. 確認各資料庫實際備份時間**

```sql
SELECT GETDATE() AS [伺服器目前時間];

SELECT
    d.name AS [資料庫名稱],
    MAX(CASE WHEN b.type = 'D'
        THEN b.backup_finish_date END) AS [最近完整備份],
    MAX(CASE WHEN b.type = 'I'
        THEN b.backup_finish_date END) AS [最近差異備份],
    MAX(CASE WHEN b.type = 'L'
        THEN b.backup_finish_date END) AS [最近交易記錄備份]
FROM sys.databases AS d
LEFT JOIN msdb.dbo.backupset AS b
    ON b.database_name = d.name
WHERE d.name <> N'tempdb'
GROUP BY d.name
ORDER BY d.name;
```

**2. 查失敗步驟的詳細訊息**

```sql
SELECT TOP (10)
    j.name AS [工作名稱],
    h.step_name AS [步驟名稱],
    msdb.dbo.agent_datetime(h.run_date, h.run_time) AS [執行時間],
    h.run_status AS [狀態_0失敗_1成功_2重試_3取消_4執行中],
    h.message AS [詳細錯誤訊息]
FROM msdb.dbo.sysjobhistory AS h
JOIN msdb.dbo.sysjobs AS j
    ON j.job_id = h.job_id
WHERE j.name = N'Backup Schedule1'
  AND h.step_id > 0
ORDER BY h.instance_id DESC;
```

第一段能確認資料庫的備份歷史；第二段才有機會看到失敗原因。**備份歷史存在，仍不代表備份檔目前還在或可成功還原。**

---

<a id="q6"></a>

## S189-Q006｜Msg 105, Level 15, State 1, Line 48 Unclosed quotation mark after the character string '最近…

日期：2026-09-18

**你的提問**

Msg 105, Level 15, State 1, Line 48
Unclosed quotation mark after the character string '最近差異備份
'.
Msg 102, Level 15, State 1, Line 48
Incorrect syntax near '最近差異備份

**當時回答**

這個錯誤表示 SQL 中有單引號 `'` 沒有成對關閉，可能是複製時內容不完整。

請在 SSMS **開一個新的查詢視窗**，完整貼上以下語法執行。我將欄位名稱改成英文，方便確認：

```sql
SELECT GETDATE() AS ServerTime;

SELECT
    d.name AS DatabaseName,
    MAX(CASE WHEN b.type = 'D'
        THEN b.backup_finish_date END) AS LastFullBackup,
    MAX(CASE WHEN b.type = 'I'
        THEN b.backup_finish_date END) AS LastDiffBackup,
    MAX(CASE WHEN b.type = 'L'
        THEN b.backup_finish_date END) AS LastLogBackup
FROM sys.databases AS d
LEFT JOIN msdb.dbo.backupset AS b
    ON b.database_name = d.name
WHERE d.name <> N'tempdb'
GROUP BY d.name
ORDER BY d.name;
```

欄位依序是：**資料庫名稱、最近完整備份、最近差異備份、最近交易記錄備份**。把執行結果貼給我即可。

---

<a id="q7"></a>

## S189-Q007｜B1AS NULL NULL NULL B1LOGGING NULL NULL NULL IFSERV NULL NULL NULL master NULL NULL NULL m…

日期：2026-09-18

**你的提問**

B1AS	NULL	NULL	NULL
B1LOGGING	NULL	NULL	NULL
IFSERV	NULL	NULL	NULL
master	NULL	NULL	NULL
model	NULL	NULL	NULL
msdb	NULL	NULL	NULL
SBO-COMMON	NULL	NULL	NULL
SLDModel.SLDData	NULL	NULL	NULL
WellShin\_US\_NewTest	2026-07-17 23:42:37.000	NULL	NULL
WellShin\_US\_Official	2026-09-02 22:59:10.000	NULL	NULL
WellShin\_US\_OldOfficial	2026-07-06 11:20:31.000	NULL	NULL
WellShin\_US\_Test\_260416	2026-06-09 16:42:10.000	NULL	NULL
WellShin\_VN\_Official\_1	2026-09-02 22:59:14.000	NULL	NULL
WellShin\_VN\_Test	2026-08-25 17:41:11.000	NULL	NULL
WellShin\_VN\_Test\_260428	2026-06-09 16:47:18.000	NULL	NULL

**當時回答**

**US、VN 正式資料庫都有完整備份紀錄，但最新都停在 2026/9/2，尚不能確認目前仍有正常備份。**

| 正式資料庫 | 最近完整備份 |
|---|---|
| `WellShin_US_Official` | 2026/9/2 22:59:10 |
| `WellShin_VN_Official_1` | 2026/9/2 22:59:14 |

如果這是今天（9/18）查的結果，代表這台 SQL Server 的備份歷史中，**約 16 天沒有這兩個資料庫的新備份紀錄**。其他資料庫的 `NULL` 表示查不到紀錄，不一定代表從未備份；所有資料庫也都查不到差異及交易記錄備份。

下一步先確認 **SQL Server Agent 是否停止**。請執行這段唯讀查詢：

```sql
SELECT GETDATE() AS ServerTime;

SELECT
    servicename,
    status_desc,
    startup_type_desc,
    last_startup_time
FROM sys.dm_server_services
WHERE servicename LIKE N'SQL Server Agent%';
```

- `Running`：目前正在執行，需繼續查排程及工作歷史。
- `Stopped`：目前已停止，Agent 的自動備份排程無法執行。
- 若出現權限錯誤，貼上錯誤訊息即可。

另外，VN 正式庫在 9/2 有成功備份紀錄，因此前面那個工作失敗，**不等於 VN 完全沒有備份**；仍要查失敗步驟才能知道是備份、刪除或其他動作出錯。

---

<a id="q8"></a>

## S189-Q008｜Backup Schedule1 WS\_VN\_Official Auto Backup and Delete 2026-09-02 22:59:00.000 0 Execute…

日期：2026-09-18

**你的提問**

Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-09-02 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-09-01 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-31 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-30 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-29 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-28 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-27 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-26 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-25 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.
Backup Schedule1	WS\_VN\_Official Auto Backup and Delete	2026-08-24 22:59:00.000	0	Executed as user: NT SERVICE\SQLSERVERAGENT. Incorrect syntax near ')'. [SQLSTATE 42000] (Error 102).  The step failed.

**當時回答**

已確認：**`Backup Schedule1` 在 8/24～9/2 連續 10 次因相同的 SQL 語法錯誤失敗**：

> `Incorrect syntax near ')'`：右括號 `)` 附近有語法錯誤。

目前還無法判斷是哪一行，需要查看該工作步驟的程式碼。請執行以下唯讀查詢，**只會顯示設定，不會執行備份或刪除**：

```sql
SELECT
    j.name AS JobName,
    s.step_id AS StepID,
    s.step_name AS StepName,
    s.subsystem AS StepType,
    s.database_name AS DatabaseName,
    s.command AS StepCommand
FROM msdb.dbo.sysjobs AS j
JOIN msdb.dbo.sysjobsteps AS s
    ON s.job_id = j.job_id
WHERE j.name = N'Backup Schedule1'
ORDER BY s.step_id;
```

請複製 `StepCommand` 欄位的完整內容給我；若有密碼請先遮掉，我就能幫你定位並修正。

另外，**這個語法錯誤與「備份紀錄停在 9/2」仍需分別查明**。US、VN 正式庫在 9/2 都有成功完整備份紀錄，但之後是否停止排程，還需要前一段 SQL Server Agent 狀態查詢的結果。

---

<a id="q9"></a>

## S189-Q009｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-f8668414-d91b-43aa-9207-a4649d637259.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f8668414-d91b-43aa-9207-a4649d637259.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

截圖確認這個工作有兩個步驟：第 1 步備份 VN、第 2 步備份 US。但 **`StepCommand` 只顯示開頭，還看不到出錯的程式碼**。

請執行以下查詢，只取出失敗的第 1 步：

```sql
SELECT s.command AS StepCommand
FROM msdb.dbo.sysjobsteps AS s
JOIN msdb.dbo.sysjobs AS j
    ON j.job_id = s.job_id
WHERE j.name = N'Backup Schedule1'
  AND s.step_id = 1;
```

在結果中的 `StepCommand` 儲存格按右鍵 → **複製**，直接貼到對話裡，不用截圖。

如果複製內容仍不完整，可以在左側展開 **SQL Server Agent → 作業 → Backup Schedule1 → 右鍵「屬性」→ 步驟 → 選第 1 步 → 編輯**，將「命令」框內的文字全選複製貼上，最後按「取消」離開。

---

<a id="q10"></a>

## S189-Q010｜Backup Schedule1 1 WS\_VN\_Official Auto Backup and Delete TSQL WellShin\_VN\_Official\_1 …

日期：2026-09-18

**你的提問**

Backup Schedule1	1	WS\_VN\_Official Auto Backup and Delete	TSQL	WellShin\_VN\_Official\_1	DECLARE @DBName NVARCHAR(128) = N'WellShin\_VN\_Official\_1';  DECLARE @BackupPath NVARCHAR(500) = N'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\Auto\_backup';  DECLARE @FileName NVARCHAR(500);  DECLARE @SQL NVARCHAR(MAX);    -- 1. 組出本次備份檔名  SET @FileName = @BackupPath + @DBName + '*'       + CONVERT(VARCHAR(8), GETDATE(), 112)      + '*'       + REPLACE(CONVERT(VARCHAR(5), GETDATE(), 108), ':', '')      + '.bak';    -- 2. 執行備份  SET @SQL = N'  BACKUP DATABASE [' + @DBName + N']  TO DISK = N''' + @FileName + N'''  WITH NOFORMAT, INIT, NAME = N''Daily Full Backup'',   SKIP, NOREWIND, NOUNLOAD, STATS = 10;';    EXEC sp\_executesql @SQL;    -- 3. 刪除超過 7 天的舊備份檔（.bak）  EXEC master.dbo.xp\_delete\_file       0,                  -- 0 = 一般備份檔, 1 = 報表檔      @BackupPath,        -- 資料夾路徑      N'bak',             -- 副檔名      GETDATE(),          -- 基準時間      7;                  -- 保留天數，超過此天數的檔案會被刪
Backup Schedule1	2	WS\_US\_Official Auto Backup and Delete	TSQL	WellShin\_US\_Official	DECLARE @DBName NVARCHAR(128) = N'WellShin\_US\_Official';  DECLARE @BackupPath NVARCHAR(500) = N'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\Auto\_backup';  DECLARE @FileName NVARCHAR(500);  DECLARE @SQL NVARCHAR(MAX);    -- 1. 組出本次備份檔名  SET @FileName = @BackupPath + @DBName + '*'       + CONVERT(VARCHAR(8), GETDATE(), 112)      + '*'       + REPLACE(CONVERT(VARCHAR(5), GETDATE(), 108), ':', '')      + '.bak';    -- 2. 執行備份  SET @SQL = N'  BACKUP DATABASE [' + @DBName + N']  TO DISK = N''' + @FileName + N'''  WITH NOFORMAT, INIT, NAME = N''Daily Full Backup'',   SKIP, NOREWIND, NOUNLOAD, STATS = 10;';    EXEC sp\_executesql @SQL;    -- 3. 刪除超過 7 天的舊備份檔（.bak）  EXEC master.dbo.xp\_delete\_file       0,                  -- 0 = 一般備份檔, 1 = 報表檔      @BackupPath,        -- 資料夾路徑      N'bak',             -- 副檔名      GETDATE(),          -- 基準時間      7;                  -- 保留天數，超過此天數的檔案會被刪

**當時回答**

找到原因了，**兩個步驟的程式碼都有相同問題**：

1. **這次語法錯誤來自 `EXEC ... GETDATE()`**：呼叫預存程序時不能直接用函數當參數，必須先存進變數。[Microsoft 語法說明](https://learn.microsoft.com/en-us/sql/relational-databases/stored-procedures/specify-parameters?view=sql-server-ver17)
2. 備份檔名使用 `*`，這是 Windows 檔名不允許的字元。
3. 資料夾路徑結尾少了 `\`，直接串接資料庫名稱會產生錯誤路徑。
4. `xp_delete_file` 最後的 `7` **不是保留七天**；保留期限應透過截止日期計算。原本用 `GETDATE()` 作為截止日期也不符合保留七天的目的。

你已有另外的清除排程。建議先將這兩個步驟改成以下**只執行備份的版本**，清除規則另外確認，避免共用資料夾中的其他備份一起被刪除。

**第 1 步：VN，將「命令」完整替換為：**

```sql
DECLARE @DBName SYSNAME = N'WellShin_VN_Official_1';

DECLARE @BackupPath NVARCHAR(500) =
    N'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\Auto_backup\';

DECLARE @Now DATETIME = GETDATE();
DECLARE @FileName NVARCHAR(1000);

SET @FileName =
    @BackupPath
    + @DBName + N'_'
    + CONVERT(NVARCHAR(8), @Now, 112) + N'_'
    + REPLACE(CONVERT(NVARCHAR(12), @Now, 114), N':', N'')
    + N'.bak';

BACKUP DATABASE @DBName
TO DISK = @FileName
WITH
    NOINIT,
    CHECKSUM,
    NAME = N'Daily Full Backup',
    STATS = 10;

SELECT @FileName AS BackupFile;
```

**第 2 步：US，使用同一份語法，只改第一行：**

```sql
DECLARE @DBName SYSNAME = N'WellShin_US_Official';
```

請確認路徑是 **SQL Server 伺服器上的資料夾**、已存在，而且 SQL Server 服務帳號具有寫入權限。上面使用一般底線 `_`，沒有 `\_`。

儲存後，右鍵 `Backup Schedule1` → **於步驟啟動作業／Start Job at Step** → 從第 1 步開始。這會實際執行完整備份，請選擇可接受備份負載的時間；完成後再查兩個正式庫的最新完整備份時間。

**這份修正尚未在你的伺服器測試，也還沒解釋為何所有排程紀錄停在 9/2；SQL Server Agent 的狀態仍需要確認。**

---

<a id="q11"></a>

## S189-Q011｜我可以看現在有沒有排程嗎 因為我怕重複

日期：2026-09-18

**你的提問**

我可以看現在有沒有排程嗎 因為我怕重複

**當時回答**

可以，**先不要新增排程或手動啟動備份**。你先前的結果已有多個備份工作，而且兩個每日工作都在 **22:59** 啟動，值得先查是否備份相同資料庫。

以下都是唯讀查詢。

**① 查看現有備份工作的排程、時間與下一次執行時間**

```sql
SELECT
    j.name AS JobName,
    j.enabled AS JobEnabled,
    s.name AS ScheduleName,
    s.enabled AS ScheduleEnabled,
    CASE s.freq_type
        WHEN 1 THEN N'單次'
        WHEN 4 THEN N'每日'
        WHEN 8 THEN N'每週'
        WHEN 16 THEN N'每月'
        WHEN 32 THEN N'每月相對日期'
        WHEN 64 THEN N'Agent 啟動時'
        WHEN 128 THEN N'電腦閒置時'
    END AS ScheduleType,
    s.freq_interval AS FrequencyInterval,
    STUFF(
        STUFF(
            RIGHT('000000' + CAST(s.active_start_time AS VARCHAR(6)), 6),
            3, 0, ':'
        ),
        6, 0, ':'
    ) AS StartTime,
    CASE s.freq_subday_type
        WHEN 1 THEN N'指定時間執行一次'
        WHEN 2 THEN N'每隔秒數'
        WHEN 4 THEN N'每隔分鐘'
        WHEN 8 THEN N'每隔小時'
    END AS WithinDayType,
    s.freq_subday_interval AS WithinDayInterval,
    CASE WHEN js.next_run_date > 0
        THEN msdb.dbo.agent_datetime(
            js.next_run_date, js.next_run_time
        )
    END AS NextRunTime
FROM msdb.dbo.sysjobs AS j
LEFT JOIN msdb.dbo.sysjobschedules AS js
    ON js.job_id = j.job_id
LEFT JOIN msdb.dbo.sysschedules AS s
    ON s.schedule_id = js.schedule_id
WHERE j.name LIKE N'%Backup%'
ORDER BY j.name, s.name;
```

`JobEnabled` 和 `ScheduleEnabled` 都是 `1`，代表工作與排程都已啟用；仍需 Agent 運作才會執行。

**② 查看各備份工作實際執行的內容**

```sql
SELECT
    j.name AS JobName,
    s.step_id AS StepID,
    s.step_name AS StepName,
    s.subsystem AS StepType,
    s.database_name AS DatabaseContext,
    s.command AS StepCommand
FROM msdb.dbo.sysjobs AS j
JOIN msdb.dbo.sysjobsteps AS s
    ON s.job_id = j.job_id
WHERE j.name LIKE N'%Backup%'
ORDER BY j.name, s.step_id;
```

**有多個備份排程不一定是重複**，每日、每週、每月可能各有保留用途。關鍵是比較「備份哪個資料庫、備份類型、時間和存放位置」。

把這兩段結果貼上來即可。若 `Day Backup Schedule.Subplan_1` 顯示的是維護計畫套件路徑，還需要從 **管理 → 維護計畫 → 對應計畫 → 備份資料庫工作** 查看選取的資料庫；`DatabaseContext` 本身不能證明實際備份對象。

---

<a id="q12"></a>

## S189-Q012｜JobName JobEnabled ScheduleName ScheduleEnabled ScheduleType FrequencyInterval StartTime W…

日期：2026-09-18

**你的提問**

JobName	JobEnabled	ScheduleName	ScheduleEnabled	ScheduleType	FrequencyInterval	StartTime	WithinDayType	WithinDayInterval	NextRunTime
Backup Schedule1	1	Daily backup	1	每日	1	22:59:00	指定時間執行一次	0	2026-09-03 22:59:00.000
Day Backup Schedule.Subplan\_1	1	Backup Schedule.Subplan\_1	1	每日	1	22:59:00	指定時間執行一次	0	2026-09-03 22:59:00.000
Month Backup Schedule.Subplan\_1	1	Mounth Backup Schedule.Subplan\_1	1	每月	1	00:20:00	指定時間執行一次	0	2026-10-01 00:20:00.000
Week Backup Schedule.Subplan\_1	1	Week Backup Schedule.Subplan\_1	1	每週	1	00:00:00	指定時間執行一次	0	2026-09-06 00:00:00.000

**當時回答**

**有，目前已設定 4 個備份排程，而且全部啟用，不需要再新增。**

| 工作 | 執行時間 | 狀況 |
|---|---|---|
| `Backup Schedule1` | 每天 22:59 | 先前連續失敗，已找到語法問題 |
| `Day Backup Schedule.Subplan_1` | 每天 22:59 | 最近紀錄成功 |
| `Month Backup Schedule.Subplan_1` | 每月 1 日 00:20 | 最近紀錄成功 |
| `Week Backup Schedule.Subplan_1` | 每週日 00:00 | 最近紀錄成功 |

**前兩個每日工作時間完全相同，有重複備份的可能。** 已知道 `Backup Schedule1` 要備份 VN、US；接著只要確認 `Day Backup Schedule.Subplan_1` 是否也包含這兩個資料庫。

請在 SSMS 左側：

1. 展開 **管理 → 維護計畫**。
2. 找到對應的每日備份計畫，右鍵 → **修改**。
3. 雙擊裡面的「備份資料庫工作」。
4. 查看 **資料庫選取範圍、備份類型、備份資料夾**，截圖給我；看完按「取消」即可。

如果每日計畫已涵蓋 US、VN 且符合需求，就可能不需要修復並啟用另一套重複工作。

另外，**若這是 9/18 查的即時結果，下一次執行時間卻仍顯示 9/3、9/6，不能當作排程正常運作的證據**。請一併執行這段確認伺服器時間與 Agent 狀態：

```sql
SELECT GETDATE() AS ServerTime;

SELECT
    servicename,
    status_desc,
    startup_type_desc,
    last_startup_time
FROM sys.dm_server_services
WHERE servicename LIKE N'SQL Server Agent%';
```

目前先保留設定，等確認每日維護計畫涵蓋哪些資料庫後，再決定是否停用重複工作。

---

<a id="q13"></a>

## S189-Q013｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-b91c0ef0-1c62-48e6-a6b2-cacc9737f11e.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b91c0ef0-1c62-48e6-a6b2-cacc9737f11e.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，就是這裡。請點 **Modify（修改）**，不要點 Execute（執行）。

接著：

1. 在開啟的設計畫面，雙擊 **Back Up Database Task（備份資料庫工作）**。
2. 查看 **Databases（資料庫）**，點開確認勾選哪些資料庫。
3. 將資料庫選取畫面，以及 **Backup type（備份類型）**、備份資料夾截圖給我。

這樣就能確認每日排程是否已包含 US、VN，判斷是否重複。查看後按 **Cancel（取消）**，不用儲存變更。

---

<a id="q14"></a>

## S189-Q014｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-397bf8ab-0dc4-4a16-8e1c-32aab99922e5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-397bf8ab-0dc4-4a16-8e1c-32aab99922e5.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個畫面確認每日計畫設定為 **Full（完整備份）**，備份到磁碟，並選擇「指定資料庫」。

接著請：

1. 點 **Database(s)** 右側的 **▼**，截圖已勾選的資料庫。
2. 查看完後，點 **Destination** 分頁，截圖備份存放路徑。

如果勾選包含 `WellShin_US_Official` 和 `WellShin_VN_Official_1`，就能確認它與 `Backup Schedule1` 的備份對象重複。先不用修改設定。

---

<a id="q15"></a>

## S189-Q015｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-86e6d8a3-0eb4-4983-bbae-3a2db2cfac03.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-86e6d8a3-0eb4-4983-bbae-3a2db2cfac03.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個訊息表示：**目前維護計畫編輯器的連線尚未初始化**，所以無法載入資料庫清單。它本身不代表排程備份失敗。

上一張圖最上方的 **Connection 是空白的**。請先：

1. 按 **OK** 關閉錯誤訊息。
2. 查看上方 **Connection** 下拉選單能否選到既有連線。
3. 如果沒有選項，按 **Cancel** 離開，先不要按 **New** 新增連線，也不要儲存變更。

我們可以先從成功備份紀錄確認存放位置，不必修改維護計畫。執行：

```sql
SELECT TOP (20)
    b.database_name AS DatabaseName,
    b.backup_start_date AS StartTime,
    b.backup_finish_date AS FinishTime,
    b.user_name AS ExecutedBy,
    m.physical_device_name AS BackupPath
FROM msdb.dbo.backupset AS b
JOIN msdb.dbo.backupmediafamily AS m
    ON m.media_set_id = b.media_set_id
WHERE b.database_name IN (
    N'WellShin_US_Official',
    N'WellShin_VN_Official_1'
)
  AND b.type = 'D'
ORDER BY b.backup_finish_date DESC;
```

把結果貼上來，可以核對兩個正式庫的備份時間與位置；**這能確認歷史備份，但不能單靠它確定目前維護計畫的勾選設定。**

---

<a id="q16"></a>

## S189-Q016｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-9b4ce193-07a3-46f3-80b7-991589587d74.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9b4ce193-07a3-46f3-80b7-991589587d74.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

確認了，這個每日維護計畫已勾選：

- `WellShin_US_Official`
- `WellShin_VN_Official_1`

搭配前面的設定，**它已每天 22:59 對這兩個正式資料庫做完整備份，與 `Backup Schedule1` 的備份對象、類型及時間重複。** 不需要再新增排程。

但要區分：**已設定每日備份，不代表目前每天都有成功執行**。目前查到最後成功備份仍是 9/2。

建議先按「取消」離開，不更動設定。下一步確認 Agent 狀態：

```sql
SELECT GETDATE() AS ServerTime;

SELECT
    servicename,
    status_desc,
    startup_type_desc,
    last_startup_time
FROM sys.dm_server_services
WHERE servicename LIKE N'SQL Server Agent%';
```

等確認每日維護計畫持續成功、存放位置及保留規則符合需求後，就可以考慮**停用有語法錯誤的 `Backup Schedule1`**，保留每日維護計畫。

---

<a id="q17"></a>

## S189-Q017｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-f4057f11-0211-4720-b5ac-27d92cffdc9d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f4057f11-0211-4720-b5ac-27d92cffdc9d.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

確認了，這個每日備份計畫設定為：

- **資料庫**：US、VN 兩個正式庫。
- **時間／類型**：每天 22:59，完整備份。
- **位置**：SQL Server 伺服器上的 `D:\每日備份`。
- **檔案方式**：每個資料庫各自建立子資料夾，存放 `.bak` 檔。

所以預期位置是：

```text
D:\每日備份\WellShin_US_Official\
D:\每日備份\WellShin_VN_Official_1\
```

**確實已有涵蓋 US、VN 的每日備份排程，不需要再新增。** 另一個失敗的 `Backup Schedule1` 雖然存到 C 槽，但備份對象和時間重複。

接下來最直接的確認方式：到**資料庫伺服器**上開啟上述兩個資料夾，查看最新 `.bak` 檔的日期、大小。若最新仍停在 **9/2**，就需要繼續查 Agent 與排程執行狀態；若有更新的檔案，請把日期貼給我核對。

這個設定畫面按 **Cancel** 離開即可。

---

<a id="q18"></a>

## S189-Q018｜但我確實沒有備份到

日期：2026-09-18

**你的提問**

但我確實沒有備份到

**當時回答**

了解，**目前只能確認「有設定排程」，不能確認備份有成功落到檔案**。我前面說「每天做完整備份」不夠精確，應該是「設定為每天執行」。

先不新增排程，也不修改重複工作。我們查兩件事：**Agent 有沒有運作、最近一次成功紀錄實際寫到哪裡**。

請在同一個 SSMS 連線執行以下唯讀查詢，將結果貼上：

```sql
-- 1. 確認目前連線的伺服器及時間
SELECT
    @@SERVERNAME AS ServerName,
    SERVERPROPERTY('MachineName') AS MachineName,
    GETDATE() AS ServerTime;

-- 2. 確認 SQL Server Agent 狀態
SELECT
    servicename,
    status_desc,
    startup_type_desc,
    last_startup_time
FROM sys.dm_server_services
WHERE servicename LIKE N'SQL Server Agent%';

-- 3. 查兩個正式庫最近完整備份的實際位置
SELECT TOP (10)
    b.database_name AS DatabaseName,
    b.backup_start_date AS StartTime,
    b.backup_finish_date AS FinishTime,
    m.physical_device_name AS BackupPath
FROM msdb.dbo.backupset AS b
JOIN msdb.dbo.backupmediafamily AS m
    ON m.media_set_id = b.media_set_id
WHERE b.database_name IN (
    N'WellShin_US_Official',
    N'WellShin_VN_Official_1'
)
  AND b.type = 'D'
ORDER BY b.backup_finish_date DESC;
```

你前面的每日工作與備份紀錄都停在 **9/2**，這是目前最需要查明的地方。檔案找不到也可能與另外設定的刪除工作有關，但要先核對實際路徑與執行時間，才能判斷。

---

<a id="q19"></a>

## S189-Q019｜這樣的話 我需要知道為甚麼沒有

日期：2026-09-18

**你的提問**

這樣的話 我需要知道為甚麼沒有

**當時回答**

對，我們現在要查的是**為什麼沒有產生或留下備份檔**。

目前已確定：
- `Backup Schedule1` 因語法錯誤失敗。
- 另一個每日維護計畫有成功紀錄，US、VN 最近完整備份時間是 **9/2**。
- 同時有刪除備份的排程。

所以還不能直接認定是同一個原因。請一次執行下面三段**唯讀查詢**，能查出「排程是否停止、備份寫去哪、清除工作做了什麼」。

```sql
-- 1. 伺服器時間與 Agent 狀態
SELECT
    @@SERVERNAME AS ServerName,
    GETDATE() AS ServerTime;

SELECT
    servicename,
    status_desc,
    startup_type_desc,
    last_startup_time
FROM sys.dm_server_services
WHERE servicename LIKE N'SQL Server Agent%';


-- 2. US、VN 最近完整備份的實際位置
SELECT TOP (10)
    b.database_name AS DatabaseName,
    b.backup_finish_date AS FinishTime,
    m.physical_device_name AS BackupPath
FROM msdb.dbo.backupset AS b
JOIN msdb.dbo.backupmediafamily AS m
    ON m.media_set_id = b.media_set_id
WHERE b.database_name IN (
    N'WellShin_US_Official',
    N'WellShin_VN_Official_1'
)
  AND b.type = 'D'
ORDER BY b.backup_finish_date DESC;


-- 3. 每日備份與清除工作的詳細執行紀錄
SELECT TOP (60)
    j.name AS JobName,
    h.step_id AS StepID,
    h.step_name AS StepName,
    msdb.dbo.agent_datetime(
        h.run_date, h.run_time
    ) AS RunTime,
    h.run_status AS RunStatus,
    h.message AS Detail
FROM msdb.dbo.sysjobhistory AS h
JOIN msdb.dbo.sysjobs AS j
    ON j.job_id = h.job_id
WHERE j.name IN (
    N'Day Backup Schedule.Subplan_1',
    N'Day Delete Schedule US.Subplan_1',
    N'Day Delete Schedule VN.Subplan_1'
)
ORDER BY h.instance_id DESC;
```

把三組結果貼給我；若有權限錯誤也一起貼上。**現在不用改設定或重新執行備份，先用紀錄定位原因。**

---

<a id="q20"></a>

## S189-Q020｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-8cfc1dba-a9a2-4ace-a948-7d7867e7bd81.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8cfc1dba-a9a2-4ace-a948-7d7867e7bd81.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

**找到目前排程沒有執行的原因了：SQL Server Agent 已停止，而且啟動類型設為「手動」。**

你的截圖顯示：

| 項目 | 結果 |
|---|---|
| 伺服器時間 | 2026/9/18 |
| SQL Server Agent | **Stopped（已停止）** |
| 啟動類型 | **Manual（手動）** |
| US、VN 最近成功備份 | **2026/9/2** |
| 歷史備份位置 | `D:\每日備份\…` |

**Agent 停止時，即使備份工作與排程都已啟用，也不會按時執行。** 這能解釋目前為什麼沒有新的排程備份；但它何時、為何停止，還需要查服務或系統記錄，不能直接認定是重開機造成。

建議處理順序：

1. 在伺服器開啟 **SQL Server Configuration Manager → SQL Server Services**。
2. 找到 **SQL Server Agent (MSSQLSERVER)**，將啟動模式設為 **Automatic（自動）**，並啟動服務。
3. 在 SSMS 手動執行已確認涵蓋 US、VN 的 **`Day Backup Schedule.Subplan_1`**，完成後確認工作成功、兩個資料夾都有新的 `.bak` 檔。
4. 將已確認有語法錯誤、備份對象重複的 **`Backup Schedule1` 停用**，保留每日維護計畫。

如果你要追查**為什麼 Agent 停掉**，請到伺服器的 **事件檢視器 → Windows 記錄 → 系統**，查看 **9/3 前後**的 `Service Control Manager` 與關機／重開機事件。若啟動 Agent 時出現錯誤，將完整訊息貼給我。

另外，**如果連 9/2 以前的備份檔也找不到，Agent 停止無法解釋舊檔消失**，那部分還要查清除工作與磁碟上的檔案。

---

<a id="q21"></a>

## S189-Q021｜圖片／附件提問

日期：2026-09-18

**你的提問**

# Files mentioned by the user:

## codex-clipboard-5f3d376e-1cd6-4e9a-8b5e-c40386d80480.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5f3d376e-1cd6-4e9a-8b5e-c40386d80480.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，圖中 **SQL Server Agent 的紅色叉號**與查詢結果一致，表示服務已停止。

請在 **SQL Server Agent** 上按右鍵 → **Start（啟動）**。若出現啟動確認，選「是」；如果失敗，把完整錯誤訊息貼給我。

啟動成功後：

1. 展開 **Jobs**。
2. 找到 **`Day Backup Schedule.Subplan_1`**。
3. 右鍵 → **Start Job at Step…**，執行一次補備份。這會實際備份 US、VN，可能需要一些時間。
4. 完成後確認 `D:\每日備份` 下兩個資料庫的子資料夾都有今天的新 `.bak` 檔。

**啟動 Agent 不會自動補跑錯過的每日備份**，所以需要上述手動補跑。之後還要將 Agent 的啟動模式從 **Manual 改為 Automatic**，避免伺服器重開後沒有自動啟動。

---

