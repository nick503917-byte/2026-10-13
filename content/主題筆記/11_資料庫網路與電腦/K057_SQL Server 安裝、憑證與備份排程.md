---
title: "SQL Server 安裝、憑證與備份排程"
category: "11_資料庫網路與電腦"
updated: 2026-10-20
sources: ["S002","S027","S177","S189","S196","S197"]
---

# SQL Server 安裝、憑證與備份排程

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 SSMS 憑證信任、SQL 安裝、完整／差異／交易記錄備份、Agent 服務、排程與 S3 補傳。最後的補傳安排為每日 00:00、不刪除 S3 舊檔；本次未執行任何備份、上傳或變更服務。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S002｜Fix SQL Server certificate trust](<../../原始問答/S002_Fix SQL Server certificate trust.md>)：2026-07-21，2 段問答。
- [S027｜確認 SSMS 資料庫備份功能](<../../原始問答/S027_確認 SSMS 資料庫備份功能.md>)：2026-07-27，6 段問答。
- [S177｜下載 SQL Server](<../../原始問答/S177_下載 SQL Server.md>)：2026-09-16，6 段問答。
- [S189｜確認 SSMS 資料庫備份](<../../原始問答/S189_確認 SSMS 資料庫備份.md>)：2026-09-18，21 段問答。
- [S196｜診斷 SQL Server Agent 備份排程](<../../原始問答/S196_診斷 SQL Server Agent 備份排程.md>)：2026-09-22，1 段問答。
- [S197｜診斷 SQL Server Agent 備份排程](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md>)：2026-09-22，57 段問答。

## 問題索引

- [01｜TITLE: Connect to Server ------------------------------ Cannot connect to EC2AMAZ-IGRTDRC.…](#qa1)
- [02｜給我中文](#qa2)
- [03｜我要備份資料庫 SSMS 有內建嗎](#qa3)
- [04｜BACKUP DATABASE [WellShin_VN_Test] TO DISK = N'D:\SQLBackup\WellShin_VN_Test_20260727.bak'…](#qa4)
- [05｜圖片／附件提問](#qa5)
- [06｜我現在只是要查詢](#qa6)
- [07｜因為已經有人設定每日備份 我只想要查詢是否是差異備份或是完整備份](#qa7)
- [08｜所以 我ssms 可以怎麼設定自動備份](#qa8)
- [09｜我要下載 SQL Server](#qa9)
- [10｜圖片／附件提問](#qa10)
- [11｜圖片／附件提問](#qa11)
- [12｜圖片／附件提問](#qa12)
- [13｜圖片／附件提問](#qa13)
- [14｜圖片／附件提問](#qa14)
- [15｜可以幫我確認 我的SSMS 的資料庫 有沒有在備份嗎](#qa15)
- [16｜不是這台電腦](#qa16)
- [17｜給我語法](#qa17)
- [18｜圖片／附件提問](#qa18)
- [19｜Backup Schedule1 1 Daily backup 1 每日 2026-09-02 22:59:00.000 失敗 The job failed. The Job wa…](#qa19)
- [20｜Msg 105, Level 15, State 1, Line 48 Unclosed quotation mark after the character string '最近…](#qa20)
- [21｜B1AS NULL NULL NULL B1LOGGING NULL NULL NULL IFSERV NULL NULL NULL master NULL NULL NULL m…](#qa21)
- [22｜Backup Schedule1 WS\_VN\_Official Auto Backup and Delete 2026-09-02 22:59:00.000 0 Execute…](#qa22)
- [23｜圖片／附件提問](#qa23)
- [24｜Backup Schedule1 1 WS\_VN\_Official Auto Backup and Delete TSQL WellShin\_VN\_Official\_1 …](#qa24)
- [25｜我可以看現在有沒有排程嗎 因為我怕重複](#qa25)
- [26｜JobName JobEnabled ScheduleName ScheduleEnabled ScheduleType FrequencyInterval StartTime W…](#qa26)
- [27｜圖片／附件提問](#qa27)
- [28｜圖片／附件提問](#qa28)
- [29｜圖片／附件提問](#qa29)
- [30｜圖片／附件提問](#qa30)
- [31｜圖片／附件提問](#qa31)
- [32｜但我確實沒有備份到](#qa32)
- [33｜這樣的話 我需要知道為甚麼沒有](#qa33)
- [34｜圖片／附件提問](#qa34)
- [35｜圖片／附件提問](#qa35)
- [36｜我想問 SSMS 我有設定排程 SQL Server Agent 去做備份。 但前陣子好像莫名會有關閉的行為發生，導致沒有被到](#qa36)
- [37｜。](#qa37)
- [38｜我想問 SSMS 我有設定排程 SQL Server Agent 去做備份。 但前陣子好像莫名會有關閉的行為發生，導致沒有備份到。 所以我想用windows或是其他方式去固定檢查](#qa38)
- [39｜我要怎麼做](#qa39)
- [40｜SQL Server Agent 服務停止](#qa40)
- [41｜圖片／附件提問](#qa41)
- [42｜這個嗎](#qa42)
- [43｜圖片／附件提問](#qa43)
- [44｜這樣會不會很好效能](#qa44)
- [45｜我還有另一個問題 我用cyberduck 做異地備份 然後我在想 我ssms 的備份檔 我可以用甚麼辦法自動在備份一份到cyberduck](#qa45)
- [46｜但我是免費檔的](#qa46)
- [47｜S3](#qa47)
- [48｜2JO4](#qa48)
- [49｜同一台電腦](#qa49)
- [50｜你跟我說哪裡要填 備份檔的路徑就可以了 先跟我說怎麼設定](#qa50)
- [51｜圖片／附件提問](#qa51)
- [52｜?](#qa52)
- [53｜圖片／附件提問](#qa53)
- [54｜圖片／附件提問](#qa54)
- [55｜圖片／附件提問](#qa55)
- [56｜圖片／附件提問](#qa56)
- [57｜bucket甚麼意思](#qa57)
- [58｜圖片／附件提問](#qa58)
- [59｜圖片／附件提問](#qa59)
- [60｜圖片／附件提問](#qa60)
- [61｜圖片／附件提問](#qa61)
- [62｜圖片／附件提問](#qa62)
- [63｜圖片／附件提問](#qa63)
- [64｜圖片／附件提問](#qa64)
- [65｜給我英文Windows「程式和功能」](#qa65)
- [66｜再來'](#qa66)
- [67｜圖片／附件提問](#qa67)
- [68｜圖片／附件提問](#qa68)
- [69｜圖片／附件提問](#qa69)
- [70｜圖片／附件提問](#qa70)
- [71｜用意是甚麼](#qa71)
- [72｜圖片／附件提問](#qa72)
- [73｜我需要在點進去嗎 還是在這層就好](#qa73)
- [74｜圖片／附件提問](#qa74)
- [75｜圖片／附件提問](#qa75)
- [76｜這樣嗎](#qa76)
- [77｜那如果檔名重複的話 會自動檔下嗎](#qa77)
- [78｜那如果我想要的事比對呢](#qa78)
- [79｜確認有沒有漏傳 以及我的SSMS的會刪七天前的資料](#qa79)
- [80｜那先上傳看看](#qa80)
- [81｜沒問題了 那可以設定固定了 然後我想要用比對的方式](#qa81)
- [82｜如果本機七天前刪除 我可以跟著一起刪除嗎](#qa82)
- [83｜那先不刪好了](#qa83)
- [84｜OK 在給我一次腳本](#qa84)
- [85｜這樣嗎](#qa85)
- [86｜圖片／附件提問](#qa86)
- [87｜圖片／附件提問](#qa87)
- [88｜圖片／附件提問](#qa88)
- [89｜圖片／附件提問](#qa89)
- [90｜再來](#qa90)
- [91｜我剛剛設定到一半 我好像關掉 我不太確定](#qa91)
- [92｜圖片／附件提問](#qa92)
- [93｜對](#qa93)

## 合併後歷史問答

<a id="qa1"></a>

### 1. TITLE: Connect to Server ------------------------------ Cannot connect to EC2AMAZ-IGRTDRC.…

來源：[S002-Q001](<../../原始問答/S002_Fix SQL Server certificate trust.md#q1>)；2026-07-21

**問題**

TITLE: Connect to Server
------------------------------

Cannot connect to EC2AMAZ-IGRTDRC.

------------------------------
ADDITIONAL INFORMATION:

A connection was successfully established with the server, but then an error occurred during the login process. (provider: SSL Provider, error: 0 - The certificate chain was issued by an authority that is not trusted.) (Microsoft SQL Server, Error: -2146893019)

For help, click: https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver--2146893019-database-engine-error

------------------------------

The certificate chain was issued by an authority that is not trusted

------------------------------
BUTTONS:

OK
------------------------------

**歷史回答**

SQL Server is reachable; the login fails because its TLS certificate isn’t trusted by your PC.

Quick fix in SSMS:

1. In **Connect to Server**, click **Options >>**.
2. Open **Connection Properties**.
3. Check **Trust server certificate**.
4. Connect again.

For a permanent secure fix, install the issuing CA/root (and any intermediate) certificate into the client’s trusted certificate store, and ensure SQL Server is configured with a certificate whose name matches `EC2AMAZ-IGRTDRC`.

For connection strings, the equivalent temporary workaround is:

```text
TrustServerCertificate=True
```

Avoid using that permanently for production or sensitive connections; it bypasses validation of the server certificate.

<a id="qa2"></a>

### 2. 給我中文

來源：[S002-Q002](<../../原始問答/S002_Fix SQL Server certificate trust.md#q2>)；2026-07-21

**問題**

給我中文

**歷史回答**

SQL Server 可以連到，但登入時因為伺服器的 TLS/SSL 憑證不受你電腦信任而失敗。

最快的 SSMS 處理方式：

1. 在「連線到伺服器」視窗點選 **選項 >>**
2. 開啟 **連線屬性**
3. 勾選 **信任伺服器憑證**（Trust server certificate）
4. 再重新連線

若是連線字串，加入：

```text
TrustServerCertificate=True
```

正式且較安全的作法是：在用戶端安裝簽發該 SQL Server 憑證的根憑證／中繼憑證，並確認 SQL Server 憑證中的伺服器名稱與 `EC2AMAZ-IGRTDRC` 相符。

「信任伺服器憑證」適合內網測試或暫時排除問題；正式環境建議修正憑證信任鏈，而不是長期略過驗證。

<a id="qa3"></a>

### 3. 我要備份資料庫 SSMS 有內建嗎

來源：[S027-Q001](<../../原始問答/S027_確認 SSMS 資料庫備份功能.md#q1>)；2026-07-27

**問題**

# Files mentioned by the user:

## codex-clipboard-5ad88607-a4c0-483a-9ea5-8c918dfec8e6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5ad88607-a4c0-483a-9ea5-8c918dfec8e6.png

## My request for Codex:


我要備份資料庫 SSMS 有內建嗎

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa4"></a>

### 4. BACKUP DATABASE [WellShin_VN_Test] TO DISK = N'D:\SQLBackup\WellShin_VN_Test_20260727.bak'…

來源：[S027-Q002](<../../原始問答/S027_確認 SSMS 資料庫備份功能.md#q2>)；2026-07-27

**問題**

BACKUP DATABASE [WellShin_VN_Test]
TO DISK = N'D:\SQLBackup\WellShin_VN_Test_20260727.bak'
WITH INIT, COMPRESSION, CHECKSUM, STATS = 10; 這是查詢嗎

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa5"></a>

### 5. 圖片／附件提問

來源：[S027-Q003](<../../原始問答/S027_確認 SSMS 資料庫備份功能.md#q3>)；2026-07-27

**問題**

# Files mentioned by the user:

## codex-clipboard-813c332d-6420-42f7-a7b1-c7763f617daa.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-813c332d-6420-42f7-a7b1-c7763f617daa.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa6"></a>

### 6. 我現在只是要查詢

來源：[S027-Q004](<../../原始問答/S027_確認 SSMS 資料庫備份功能.md#q4>)；2026-07-27

**問題**

我現在只是要查詢

**歷史回答**

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

<a id="qa7"></a>

### 7. 因為已經有人設定每日備份 我只想要查詢是否是差異備份或是完整備份

來源：[S027-Q005](<../../原始問答/S027_確認 SSMS 資料庫備份功能.md#q5>)；2026-07-27

**問題**

因為已經有人設定每日備份 我只想要查詢是否是差異備份或是完整備份

**歷史回答**

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

<a id="qa8"></a>

### 8. 所以 我ssms 可以怎麼設定自動備份

來源：[S027-Q006](<../../原始問答/S027_確認 SSMS 資料庫備份功能.md#q6>)；2026-07-27

**問題**

所以 我ssms 可以怎麼設定自動備份

**歷史回答**

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

<a id="qa9"></a>

### 9. 我要下載 SQL Server

來源：[S177-Q001](<../../原始問答/S177_下載 SQL Server.md#q1>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-c356fe84-2729-4d0c-be75-62e392534be4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c356fe84-2729-4d0c-be75-62e392534be4.png



## My request:
我要下載 SQL Server

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你已經開啟 **SQL Server 2022 安裝中心**，可以直接開始安裝：

1. 點左側 **Installation（安裝）**。
2. 點 **New SQL Server stand-alone installation or add features to an existing installation**（新增 SQL Server 獨立安裝或新增功能）。
3. 到 **Feature Selection（功能選取）** 時，勾選 **Database Engine Services（資料庫引擎服務）**。
4. 到 **Database Engine Configuration（資料庫引擎設定）** 時，若是自己在這台電腦練習，可使用 **Windows authentication mode**，並點 **Add Current User（新增目前使用者）**。
5. 繼續按 **Next**，最後按 **Install**。[微軟官方安裝說明](https://learn.microsoft.com/en-us/sql/database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup)

先點左側 **Installation**，把下一個畫面貼給我，我可以接著帶你選。

<a id="qa10"></a>

### 10. 圖片／附件提問

來源：[S177-Q002](<../../原始問答/S177_下載 SQL Server.md#q2>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-cbba229a-6850-4140-b69a-37fb86a948c7.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cbba229a-6850-4140-b69a-37fb86a948c7.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這是 **版本選擇（Edition）** 畫面。如果你是用來學習、練習 SQL 或開發程式：

1. 點選 **Specify a free edition** 左邊的圓圈。
2. 把下拉選單的 **Evaluation** 改成 **Developer**。
3. 按右下角 **Next**。

**Developer 免費且無到期日，僅供開發與測試使用**；Evaluation 則有 180 天期限。選擇 Developer 不需要輸入產品金鑰。

下一頁是授權條款，閱讀並同意後，就能繼續安裝。

<a id="qa11"></a>

### 11. 圖片／附件提問

來源：[S177-Q003](<../../原始問答/S177_下載 SQL Server.md#q3>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-398e68f4-eaa3-4dc8-996b-bb1dbb0627fb.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-398e68f4-eaa3-4dc8-996b-bb1dbb0627fb.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這頁是 **Azure 雲端服務擴充功能**。如果只是在自己電腦上學習 SQL：

1. **取消勾選**上方的 **Azure Extension for SQL Server**。
2. 下方 Azure 帳號等欄位都不用填。
3. 點右下角 **Next**。

下一頁是 **Feature Selection（功能選取）**，請勾選 **Database Engine Services（資料庫引擎服務）**。

<a id="qa12"></a>

### 12. 圖片／附件提問

來源：[S177-Q004](<../../原始問答/S177_下載 SQL Server.md#q4>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-0e731ced-2494-4127-a0d3-58936fc2d579.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-0e731ced-2494-4127-a0d3-58936fc2d579.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這頁只要勾選第一項 **Database Engine Services（資料庫引擎服務）**，其他功能先不用勾，安裝路徑維持預設。

**但畫面上方顯示「Standard」版本**。如果你是要免費學習使用，請先按 **Back** 回到 **Edition**，確認選擇 **Specify a free edition → Developer**，再繼續。

如果你確定有 Standard 授權，就可以勾選資料庫引擎後按 **Next**。

<a id="qa13"></a>

### 13. 圖片／附件提問

來源：[S177-Q005](<../../原始問答/S177_下載 SQL Server.md#q5>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-af49bf4f-3eba-4516-933f-ead4c7c1c739.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-af49bf4f-3eba-4516-933f-ead4c7c1c739.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這頁是 **Instance Configuration（執行個體設定）**，維持目前設定即可：

- 選擇 **Default instance**。
- **Instance ID** 保留 `MSSQLSERVER`。

另外，左側出現 **PolyBase Configuration**，表示你可能也勾選了 PolyBase。一般學習 SQL 不需要，可以按 **Back** 回到功能選取，只保留 **Database Engine Services**。

完成後按 **Next** 繼續。

<a id="qa14"></a>

### 14. 圖片／附件提問

來源：[S177-Q006](<../../原始問答/S177_下載 SQL Server.md#q6>)；2026-09-16

**問題**

# Files mentioned by the user:

## codex-clipboard-8a39aac8-13d7-480b-84b8-6ad4d83231c0.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8a39aac8-13d7-480b-84b8-6ad4d83231c0.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這頁是 **Server Configuration（伺服器設定）**，先維持預設即可：

- **Account Name／Password**：不用修改或填寫。
- **Startup Type**：維持目前設定。
- 下方 **Grant Perform Volume Maintenance Tasks…**：先不勾也可以正常使用。

按右下角 **Next**。

下一頁 **Database Engine Configuration**，選 **Windows authentication mode**，並記得按 **Add Current User（新增目前使用者）**，讓你的 Windows 帳號能管理資料庫。

<a id="qa15"></a>

### 15. 可以幫我確認 我的SSMS 的資料庫 有沒有在備份嗎

來源：[S189-Q001](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q1>)；2026-09-18

**問題**

可以幫我確認 我的SSMS 的資料庫 有沒有在備份嗎

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa16"></a>

### 16. 不是這台電腦

來源：[S189-Q002](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q2>)；2026-09-18

**問題**

不是這台電腦

**歷史回答**

了解，是另一台電腦上的 SQL Server。剛才查到的是本機狀態，不能用來判斷你的目標資料庫。

請提供 SSMS 連線的「伺服器名稱」及要確認的「資料庫名稱」，不用提供密碼。若這裡無法連線，我可以提供查詢語法，讓你在那台 SSMS 執行，確認最近備份時間與自動備份排程。

<a id="qa17"></a>

### 17. 給我語法

來源：[S189-Q003](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q3>)；2026-09-18

**問題**

給我語法

**歷史回答**

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

<a id="qa18"></a>

### 18. 圖片／附件提問

來源：[S189-Q004](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q4>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-7a307366-72ca-4391-acd5-39e3951a126d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7a307366-72ca-4391-acd5-39e3951a126d.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa19"></a>

### 19. Backup Schedule1 1 Daily backup 1 每日 2026-09-02 22:59:00.000 失敗 The job failed. The Job wa…

來源：[S189-Q005](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q5>)；2026-09-18

**問題**

Backup Schedule1	1	Daily backup	1	每日	2026-09-02 22:59:00.000	失敗	The job failed.  The Job was invoked by Schedule 9 (Daily backup).  The last step to run was step 1 (WS\_VN\_Official Auto Backup and Delete).
Day Backup Schedule.Subplan\_1	1	Backup Schedule.Subplan\_1	1	每日	2026-09-02 22:59:00.000	成功	The job succeeded.  The Job was invoked by Schedule 10 (Backup Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Day Delete Schedule US.Subplan\_1	1	Day Delete Schedule US.Subplan\_1	1	每日	2026-09-02 23:24:00.000	成功	The job succeeded.  The Job was invoked by Schedule 16 (Day Delete Schedule US.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Day Delete Schedule VN.Subplan\_1	1	Day Delete Schedule VN.Subplan\_1	1	每日	2026-09-02 23:39:00.000	成功	The job succeeded.  The Job was invoked by Schedule 17 (Day Delete Schedule VN.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Month Backup Schedule.Subplan\_1	1	Mounth Backup Schedule.Subplan\_1	1	每月	2026-09-01 00:20:00.000	成功	The job succeeded.  The Job was invoked by Schedule 14 (Mounth Backup Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Month Delete Schedule.Subplan\_1	1	Month Delete Schedule.Subplan\_1	1	每月	2026-09-01 00:30:00.000	成功	The job succeeded.  The Job was invoked by Schedule 15 (Month Delete Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
syspolicy\_purge\_history	1	syspolicy\_purge\_history\_schedule	1	每日	2026-09-03 02:00:00.000	成功	The job succeeded.  The Job was invoked by Schedule 8 (syspolicy\_purge\_history\_schedule).  The last step to run was step 3 (Erase Phantom System Health Records.).
Week Backup Schedule.Subplan\_1	1	Week Backup Schedule.Subplan\_1	1	每週	2026-08-30 00:00:00.000	成功	The job succeeded.  The Job was invoked by Schedule 12 (Week Backup Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).
Week Dekete Schedule.Subplan\_1	1	Week Dekete Schedule.Subplan\_1	1	每週	2026-08-30 00:10:00.000	成功	The job succeeded.  The Job was invoked by Schedule 13 (Week Dekete Schedule.Subplan\_1).  The last step to run was step 1 (Subplan\_1).

**歷史回答**

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

<a id="qa20"></a>

### 20. Msg 105, Level 15, State 1, Line 48 Unclosed quotation mark after the character string '最近…

來源：[S189-Q006](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q6>)；2026-09-18

**問題**

Msg 105, Level 15, State 1, Line 48
Unclosed quotation mark after the character string '最近差異備份
'.
Msg 102, Level 15, State 1, Line 48
Incorrect syntax near '最近差異備份

**歷史回答**

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

<a id="qa21"></a>

### 21. B1AS NULL NULL NULL B1LOGGING NULL NULL NULL IFSERV NULL NULL NULL master NULL NULL NULL m…

來源：[S189-Q007](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q7>)；2026-09-18

**問題**

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

**歷史回答**

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

<a id="qa22"></a>

### 22. Backup Schedule1 WS\_VN\_Official Auto Backup and Delete 2026-09-02 22:59:00.000 0 Execute…

來源：[S189-Q008](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q8>)；2026-09-18

**問題**

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

**歷史回答**

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

<a id="qa23"></a>

### 23. 圖片／附件提問

來源：[S189-Q009](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q9>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-f8668414-d91b-43aa-9207-a4649d637259.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f8668414-d91b-43aa-9207-a4649d637259.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa24"></a>

### 24. Backup Schedule1 1 WS\_VN\_Official Auto Backup and Delete TSQL WellShin\_VN\_Official\_1 …

來源：[S189-Q010](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q10>)；2026-09-18

**問題**

Backup Schedule1	1	WS\_VN\_Official Auto Backup and Delete	TSQL	WellShin\_VN\_Official\_1	DECLARE @DBName NVARCHAR(128) = N'WellShin\_VN\_Official\_1';  DECLARE @BackupPath NVARCHAR(500) = N'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\Auto\_backup';  DECLARE @FileName NVARCHAR(500);  DECLARE @SQL NVARCHAR(MAX);    -- 1. 組出本次備份檔名  SET @FileName = @BackupPath + @DBName + '*'       + CONVERT(VARCHAR(8), GETDATE(), 112)      + '*'       + REPLACE(CONVERT(VARCHAR(5), GETDATE(), 108), ':', '')      + '.bak';    -- 2. 執行備份  SET @SQL = N'  BACKUP DATABASE [' + @DBName + N']  TO DISK = N''' + @FileName + N'''  WITH NOFORMAT, INIT, NAME = N''Daily Full Backup'',   SKIP, NOREWIND, NOUNLOAD, STATS = 10;';    EXEC sp\_executesql @SQL;    -- 3. 刪除超過 7 天的舊備份檔（.bak）  EXEC master.dbo.xp\_delete\_file       0,                  -- 0 = 一般備份檔, 1 = 報表檔      @BackupPath,        -- 資料夾路徑      N'bak',             -- 副檔名      GETDATE(),          -- 基準時間      7;                  -- 保留天數，超過此天數的檔案會被刪
Backup Schedule1	2	WS\_US\_Official Auto Backup and Delete	TSQL	WellShin\_US\_Official	DECLARE @DBName NVARCHAR(128) = N'WellShin\_US\_Official';  DECLARE @BackupPath NVARCHAR(500) = N'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\Auto\_backup';  DECLARE @FileName NVARCHAR(500);  DECLARE @SQL NVARCHAR(MAX);    -- 1. 組出本次備份檔名  SET @FileName = @BackupPath + @DBName + '*'       + CONVERT(VARCHAR(8), GETDATE(), 112)      + '*'       + REPLACE(CONVERT(VARCHAR(5), GETDATE(), 108), ':', '')      + '.bak';    -- 2. 執行備份  SET @SQL = N'  BACKUP DATABASE [' + @DBName + N']  TO DISK = N''' + @FileName + N'''  WITH NOFORMAT, INIT, NAME = N''Daily Full Backup'',   SKIP, NOREWIND, NOUNLOAD, STATS = 10;';    EXEC sp\_executesql @SQL;    -- 3. 刪除超過 7 天的舊備份檔（.bak）  EXEC master.dbo.xp\_delete\_file       0,                  -- 0 = 一般備份檔, 1 = 報表檔      @BackupPath,        -- 資料夾路徑      N'bak',             -- 副檔名      GETDATE(),          -- 基準時間      7;                  -- 保留天數，超過此天數的檔案會被刪

**歷史回答**

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

<a id="qa25"></a>

### 25. 我可以看現在有沒有排程嗎 因為我怕重複

來源：[S189-Q011](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q11>)；2026-09-18

**問題**

我可以看現在有沒有排程嗎 因為我怕重複

**歷史回答**

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

<a id="qa26"></a>

### 26. JobName JobEnabled ScheduleName ScheduleEnabled ScheduleType FrequencyInterval StartTime W…

來源：[S189-Q012](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q12>)；2026-09-18

**問題**

JobName	JobEnabled	ScheduleName	ScheduleEnabled	ScheduleType	FrequencyInterval	StartTime	WithinDayType	WithinDayInterval	NextRunTime
Backup Schedule1	1	Daily backup	1	每日	1	22:59:00	指定時間執行一次	0	2026-09-03 22:59:00.000
Day Backup Schedule.Subplan\_1	1	Backup Schedule.Subplan\_1	1	每日	1	22:59:00	指定時間執行一次	0	2026-09-03 22:59:00.000
Month Backup Schedule.Subplan\_1	1	Mounth Backup Schedule.Subplan\_1	1	每月	1	00:20:00	指定時間執行一次	0	2026-10-01 00:20:00.000
Week Backup Schedule.Subplan\_1	1	Week Backup Schedule.Subplan\_1	1	每週	1	00:00:00	指定時間執行一次	0	2026-09-06 00:00:00.000

**歷史回答**

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

<a id="qa27"></a>

### 27. 圖片／附件提問

來源：[S189-Q013](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q13>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-b91c0ef0-1c62-48e6-a6b2-cacc9737f11e.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b91c0ef0-1c62-48e6-a6b2-cacc9737f11e.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，就是這裡。請點 **Modify（修改）**，不要點 Execute（執行）。

接著：

1. 在開啟的設計畫面，雙擊 **Back Up Database Task（備份資料庫工作）**。
2. 查看 **Databases（資料庫）**，點開確認勾選哪些資料庫。
3. 將資料庫選取畫面，以及 **Backup type（備份類型）**、備份資料夾截圖給我。

這樣就能確認每日排程是否已包含 US、VN，判斷是否重複。查看後按 **Cancel（取消）**，不用儲存變更。

<a id="qa28"></a>

### 28. 圖片／附件提問

來源：[S189-Q014](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q14>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-397bf8ab-0dc4-4a16-8e1c-32aab99922e5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-397bf8ab-0dc4-4a16-8e1c-32aab99922e5.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這個畫面確認每日計畫設定為 **Full（完整備份）**，備份到磁碟，並選擇「指定資料庫」。

接著請：

1. 點 **Database(s)** 右側的 **▼**，截圖已勾選的資料庫。
2. 查看完後，點 **Destination** 分頁，截圖備份存放路徑。

如果勾選包含 `WellShin_US_Official` 和 `WellShin_VN_Official_1`，就能確認它與 `Backup Schedule1` 的備份對象重複。先不用修改設定。

<a id="qa29"></a>

### 29. 圖片／附件提問

來源：[S189-Q015](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q15>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-86e6d8a3-0eb4-4983-bbae-3a2db2cfac03.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-86e6d8a3-0eb4-4983-bbae-3a2db2cfac03.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa30"></a>

### 30. 圖片／附件提問

來源：[S189-Q016](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q16>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-9b4ce193-07a3-46f3-80b7-991589587d74.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9b4ce193-07a3-46f3-80b7-991589587d74.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa31"></a>

### 31. 圖片／附件提問

來源：[S189-Q017](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q17>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-f4057f11-0211-4720-b5ac-27d92cffdc9d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f4057f11-0211-4720-b5ac-27d92cffdc9d.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa32"></a>

### 32. 但我確實沒有備份到

來源：[S189-Q018](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q18>)；2026-09-18

**問題**

但我確實沒有備份到

**歷史回答**

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

<a id="qa33"></a>

### 33. 這樣的話 我需要知道為甚麼沒有

來源：[S189-Q019](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q19>)；2026-09-18

**問題**

這樣的話 我需要知道為甚麼沒有

**歷史回答**

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

<a id="qa34"></a>

### 34. 圖片／附件提問

來源：[S189-Q020](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q20>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-8cfc1dba-a9a2-4ace-a948-7d7867e7bd81.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8cfc1dba-a9a2-4ace-a948-7d7867e7bd81.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa35"></a>

### 35. 圖片／附件提問

來源：[S189-Q021](<../../原始問答/S189_確認 SSMS 資料庫備份.md#q21>)；2026-09-18

**問題**

# Files mentioned by the user:

## codex-clipboard-5f3d376e-1cd6-4e9a-8b5e-c40386d80480.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5f3d376e-1cd6-4e9a-8b5e-c40386d80480.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，圖中 **SQL Server Agent 的紅色叉號**與查詢結果一致，表示服務已停止。

請在 **SQL Server Agent** 上按右鍵 → **Start（啟動）**。若出現啟動確認，選「是」；如果失敗，把完整錯誤訊息貼給我。

啟動成功後：

1. 展開 **Jobs**。
2. 找到 **`Day Backup Schedule.Subplan_1`**。
3. 右鍵 → **Start Job at Step…**，執行一次補備份。這會實際備份 US、VN，可能需要一些時間。
4. 完成後確認 `D:\每日備份` 下兩個資料庫的子資料夾都有今天的新 `.bak` 檔。

**啟動 Agent 不會自動補跑錯過的每日備份**，所以需要上述手動補跑。之後還要將 Agent 的啟動模式從 **Manual 改為 Automatic**，避免伺服器重開後沒有自動啟動。

<a id="qa36"></a>

### 36. 我想問 SSMS 我有設定排程 SQL Server Agent 去做備份。 但前陣子好像莫名會有關閉的行為發生，導致沒有被到

來源：[S196-Q001](<../../原始問答/S196_診斷 SQL Server Agent 備份排程.md#q1>)；2026-09-22

**問題**

我想問 

SSMS 我有設定排程 SQL Server Agent 去做備份。

但前陣子好像莫名會有關閉的行為發生，導致沒有被到

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa37"></a>

### 37. 。

來源：[S197-Q001](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q1>)；2026-09-22

**問題**

。

**歷史回答**

我在，請告訴我你遇到什麼電腦問題。

<a id="qa38"></a>

### 38. 我想問 SSMS 我有設定排程 SQL Server Agent 去做備份。 但前陣子好像莫名會有關閉的行為發生，導致沒有備份到。 所以我想用windows或是其他方式去固定檢查

來源：[S197-Q002](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q2>)；2026-09-22

**問題**

我想問 

SSMS 我有設定排程 SQL Server Agent 去做備份。

但前陣子好像莫名會有關閉的行為發生，導致沒有備份到。

所以我想用windows或是其他方式去固定檢查

**歷史回答**

可以，你的情況建議用 **Windows 工作排程器，定期檢查 SQL Server Agent 服務，以及備份是否真的成功**。這樣即使 Agent 停止，檢查機制仍然能運作。

先釐清：**關掉 SSMS 通常不會影響排程備份**，因為排程由 SQL Server Agent 在背景執行。漏備份可能是 Agent 服務停止、主機關機，或備份工作本身失敗。

建議分成三層：

| 檢查項目 | 建議頻率 | 異常時處理 |
|---|---|---|
| SQL Server Agent 服務是否執行 | 每 5～10 分鐘 | 記錄異常、嘗試啟動並通知 |
| 最近一次成功備份是否超過期限 | 每天預定備份完成後 | 通知備份逾期 |
| 備份檔是否可用 | 定期 | 驗證備份，並安排還原測試 |

也可以設定 Windows 服務的「復原」選項，在服務異常終止後重新啟動。不過，**服務重新啟動不代表錯過的備份會自動補跑**，所以仍然要檢查最後成功備份時間。

你說的「關閉」，是指 **SQL Server Agent 服務停止**，還是 **整台電腦關機／重新啟動**？另外，SQL Server 是裝在你目前這台電腦，還是另一台伺服器？

<a id="qa39"></a>

### 39. 我要怎麼做

來源：[S197-Q003](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q3>)；2026-09-22

**問題**

我要怎麼做

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa40"></a>

### 40. SQL Server Agent 服務停止

來源：[S197-Q004](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q4>)；2026-09-22

**問題**

SQL Server Agent 服務停止

**歷史回答**

了解，是 **SQL Server Agent 服務自己停止**。你可以用 **Windows 工作排程器，每 5 分鐘檢查一次，停止時自動啟動**。

以下操作要在「安裝 SQL Server 的伺服器」上執行。

**1. 確認服務名稱，設定開機自動啟動**

按 `Win + R`，輸入：

```text
services.msc
```

找到 **SQL Server Agent（執行個體名稱）**，點兩下：

- 記下「服務名稱」，不是顯示名稱。
- 「啟動類型」設為「自動」。
- 到「復原」頁籤，將第一次、第二次及後續失敗設為「重新啟動服務」，延遲設為 1 分鐘。

常見服務名稱：

| SQL Server 類型 | 服務名稱 |
|---|---|
| 預設執行個體 | `SQLSERVERAGENT` |
| 具名執行個體，例如 SQL2019 | `SQLAgent$SQL2019` |

SQL Server Agent 是獨立的 Windows 服務，可設定啟動與停止行為。[Microsoft 官方說明](https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services)

**2. 建立 Windows 定期檢查工作**

按 `Win + R`，輸入：

```text
taskschd.msc
```

右側選擇 **「建立工作」**。

「一般」頁籤：

- 名稱：`檢查 SQL Server Agent`
- 按「變更使用者或群組」，輸入 `SYSTEM`，按「檢查名稱」後確定。
- 勾選「以最高權限執行」。

「觸發程序」頁籤 →「新增」：

- 開始工作：依排程。
- 設定為每天。
- 勾選「重複工作間隔」，設為 **5 分鐘**。
- 持續時間設為 **無限期**。
- 勾選「已啟用」。

「動作」頁籤 →「新增」：

「程式或指令碼」填入：

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

「新增引數」填入以下整行：

```powershell
-NoProfile -NonInteractive -Command "try { $s = Get-Service -Name 'SQLSERVERAGENT' -ErrorAction Stop; if ($s.Status -eq 'Stopped') { Start-Service -InputObject $s -ErrorAction Stop; $s.WaitForStatus('Running', [TimeSpan]::FromSeconds(30)) }; $s.Refresh(); if ($s.Status -ne 'Running') { exit 1 } } catch { exit 1 }"
```

**如果步驟 1 查到的服務名稱不同，請替換上面的 `SQLSERVERAGENT`，並保留單引號。**

「條件」頁籤：

- 取消「只有在電腦使用 AC 電源時才啟動工作」，避免電源條件阻止檢查。

「設定」頁籤：

- 勾選「如果錯過排定的啟動時間，立即執行工作」。
- 若工作已在執行，選擇「不要啟動新的執行個體」。

完成後按「確定」。

**3. 測試工作是否能執行**

在工作排程器找到這項工作，按右鍵 →「執行」，稍後重新整理：

- `0x0`：檢查成功，服務正在執行。
- `0x1`：檢查或啟動失敗，需要確認服務名稱、SQL Server 狀態或服務登入帳號。

不用為了測試而停止正在執行備份的 Agent。

**注意：這項工作會把人為停止的 Agent 也重新啟動，維護時請先停用它。另外，Agent 恢復執行不代表漏掉的備份已補做，設定完後仍要到 SSMS 查看備份工作的「檢視歷程記錄」。**

若要找出停止原因，可以查看停止時間附近的 Windows「事件檢視器 → Windows 記錄 → 系統」以及 SQL Server Agent 的錯誤記錄。

<a id="qa41"></a>

### 41. 圖片／附件提問

來源：[S197-Q005](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q5>)；2026-09-22

**問題**

# Files mentioned by the user:

## codex-clipboard-1c9a721a-b7ac-40f5-91c5-5deb080b9fe5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1c9a721a-b7ac-40f5-91c5-5deb080b9fe5.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

從截圖看到 **SQL Server Agent (MSSQLSERVER)**：

- **狀態：Running**，目前正在執行。
- **啟動類型：Manual（手動）**，電腦重新開機後可能不會自動啟動。

你先把它改成自動啟動：

1. 點兩下 **SQL Server Agent (MSSQLSERVER)**。
2. 在「一般」頁籤，把「啟動類型」從 **手動**改成 **自動**。
3. 按「套用」。
4. 切到「復原」頁籤，將第一次、第二次及後續失敗都設為 **重新啟動服務**，延遲設為 **1 分鐘**。
5. 按「確定」。

目前服務正在執行，**不需要停止或重新啟動它**。

這個設定先解決開機自動啟動與服務失敗後的復原；每 5 分鐘檢查則接著依前面的工作排程器步驟設定。你的執行個體是 `MSSQLSERVER`，前面指令中的服務名稱 `SQLSERVERAGENT` 可以直接使用。

另外，截圖只能確認目前是手動啟動，還不能判定前陣子停止的原因。

<a id="qa42"></a>

### 42. 這個嗎

來源：[S197-Q006](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q6>)；2026-09-22

**問題**

這個嗎

**歷史回答**

對，如果你指的是上一張圖的 **「SQL Server Agent (MSSQLSERVER)」**，就是這個。

點兩下開啟，把「啟動類型」從 **手動（Manual）** 改成 **自動（Automatic）**，再按「套用」。

如果你已開啟另一個設定視窗，請貼上截圖，我幫你確認。

<a id="qa43"></a>

### 43. 圖片／附件提問

來源：[S197-Q007](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q7>)；2026-09-22

**問題**

# Files mentioned by the user:

## codex-clipboard-6f1c4a62-3310-4c3f-8471-e11d32b95461.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6f1c4a62-3310-4c3f-8471-e11d32b95461.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，就是這裡。選 **Automatic（自動）**，然後按右下角 **Apply（套用）**。

接著點上方 **Recovery（復原）**，設定：

- **First failure**：Restart the Service
- **Second failure**：Restart the Service
- **Subsequent failures**：Restart the Service
- **Restart service after**：1 minute

完成後按 **Apply → OK**。目前服務是 Running，不需要按 Stop。

<a id="qa44"></a>

### 44. 這樣會不會很好效能

來源：[S197-Q008](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q8>)；2026-09-22

**問題**

這樣會不會很好效能

**歷史回答**

你是指「會不會很耗效能」嗎？**這樣設定幾乎不會增加效能負擔。**

- **Automatic（自動）**：只是讓服務在開機時自動啟動。目前已經是 Running，改完後平常的負擔不變。
- **Recovery（復原）**：只有服務失敗時才嘗試重新啟動。
- **每 5 分鐘檢查**：如果之後有設定，也只是短暫查詢服務狀態，負擔很小。

真正比較會使用 CPU、磁碟 I/O 的是**備份工作執行期間**，不是這些啟動與檢查設定。

<a id="qa45"></a>

### 45. 我還有另一個問題 我用cyberduck 做異地備份 然後我在想 我ssms 的備份檔 我可以用甚麼辦法自動在備份一份到cyberduck

來源：[S197-Q009](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q9>)；2026-09-22

**問題**

我還有另一個問題 我用cyberduck 做異地備份 然後我在想 我ssms 的備份檔 我可以用甚麼辦法自動在備份一份到cyberduck

**歷史回答**

可以，**你可以在 SQL 備份完成後，自動把 `.bak` 檔上傳到目前 Cyberduck 連線的異地空間。**

Cyberduck 是傳檔工具，實際存放位置是你連線的伺服器或雲端空間。它有 Windows 可用的命令列工具 **Cyberduck CLI（`duck`）**，可以搭配排程自動上傳。[官方說明](https://docs.cyberduck.io/cli/)

我建議流程設成：

1. SQL Server Agent 完成本機備份。
2. Windows 工作排程器定期檢查，找出**備份已完成、尚未上傳**的檔案。
3. 使用 `duck` 上傳，保留本機備份。
4. 記錄上傳結果，失敗時重試；當天沒有成功備份也要記錄異常。

**不要只設定「凌晨 2 點備份、2 點 10 分直接上傳」**，因為備份時間可能變長，必須先確認備份成功完成。

要給你能直接照做的設定，我需要知道：

- **Cyberduck 連線類型**：例如 SFTP、FTP、Amazon S3、Backblaze B2？
- **SQL 備份檔存放資料夾**：例如 `D:\SQLBackup`。
- **Cyberduck 和 SQL Server 是否在同一台電腦？**

不知道連線類型的話，可以貼 Cyberduck 的連線設定畫面，**遮住帳號、密碼與金鑰**，我幫你確認。

<a id="qa46"></a>

### 46. 但我是免費檔的

來源：[S197-Q010](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q10>)；2026-09-22

**問題**

但我是免費檔的

**歷史回答**

你是指 **Cyberduck 免費版**嗎？如果是，**可以自動上傳，不需要購買付費版**。

做法是另外安裝免費的 **Cyberduck CLI（`duck`）**，再用 Windows 工作排程器執行上傳指令。[官方工具介紹](https://duck.sh/)

不過，異地儲存空間本身的容量或流量費用，要看你使用的服務。

你目前 Cyberduck 是連到哪裡？例如 **公司 NAS、SFTP 伺服器、Google Drive 或 S3**？不知道的話，貼連線畫面給我看即可，記得遮住密碼與金鑰。

<a id="qa47"></a>

### 47. S3

來源：[S197-Q011](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q11>)；2026-09-22

**問題**

S3

**歷史回答**

可以，**Cyberduck 免費版連到 S3，也能設定自動上傳 SQL 備份**。可以使用免費的 `duck` 命令列工具搭配 Windows 工作排程器；S3 儲存空間與傳輸費用則另外計算。[官方說明](https://docs.cyberduck.io/cli/)

要幫你整理成可以直接操作的設定，先確認三件事：

1. **是 Amazon S3，還是其他相容 S3 的服務？** 不確定可以貼 Cyberduck 連線設定畫面，遮住帳號、Access Key 與 Secret Key。
2. **SQL 備份檔放在哪個資料夾？** 例如 `D:\SQLBackup`。
3. **Cyberduck 和 SQL Server 裝在同一台電腦嗎？**

知道這些後，就能設定「確認備份完成 → 上傳到 S3 → 記錄結果、失敗重試」。

<a id="qa48"></a>

### 48. 2JO4

來源：[S197-Q012](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q12>)；2026-09-22

**問題**

2JO4

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa49"></a>

### 49. 同一台電腦

來源：[S197-Q013](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q13>)；2026-09-22

**問題**

同一台電腦

**歷史回答**

了解，**Cyberduck 和 SQL Server 在同一台電腦**，可以直接用 Windows 排程上傳本機備份檔。

接下來還需要：

1. **備份檔的資料夾路徑**，例如 `D:\SQLBackup`。
2. **S3 的連線資訊**：服務端點（Server）、Bucket 名稱，以及要存放的遠端資料夾。

你可以貼 Cyberduck 的「編輯書籤／連線設定」畫面，**遮住 Access Key、Secret Key 和密碼**。我確認後，就能提供對應的上傳指令與排程步驟。

<a id="qa50"></a>

### 50. 你跟我說哪裡要填 備份檔的路徑就可以了 先跟我說怎麼設定

來源：[S197-Q014](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q14>)；2026-09-22

**問題**

你跟我說哪裡要填 備份檔的路徑就可以了 先跟我說怎麼設定

**歷史回答**

可以，你照下面設定就好。**備份路徑填在腳本的 `$BackupFolder` 那一行**，其他要修改的地方也有標出。

這個做法是：**Windows 每 30 分鐘檢查一次，將 SQL Server 已完成的 `.bak` 備份上傳到 S3。**

**1. 安裝 Cyberduck CLI**

到 [Cyberduck CLI 官方網站](https://duck.sh/) 下載 Windows 安裝程式並安裝。

安裝後重新開啟 PowerShell，輸入：

```powershell
where.exe duck
```

把顯示的 `duck.exe` 完整路徑記下來，後面會用到。

**2. 建立放腳本的資料夾**

在 C 槽建立：

```text
C:\SQLUpload
```

開啟記事本，貼上以下內容。**先修改最上面 5 行設定**：

```powershell
# ===== 請修改這 5 行 =====
$BackupFolder = 'D:\SQLBackup'               # ← SQL 備份檔資料夾填這裡
$Duck = 'C:\填入實際安裝路徑\duck.exe'       # ← 步驟 1 查到的路徑
$Remote = 's3://s3.amazonaws.com/你的Bucket/sql-backup/'
$AccessKey = '你的Access Key ID'            # ← Cyberduck 使用的 Access Key
$SqlServer = 'localhost'                    # ← 你的預設執行個體可用此值

# S3 相容服務：把上面的 s3.amazonaws.com 改成 Cyberduck 的 Server
# 不要把 Secret Key 寫進這個檔案

$ErrorActionPreference = 'Stop'
$StateFolder = 'C:\SQLUpload\Uploaded'
$Log = 'C:\SQLUpload\upload.log'
New-Item -ItemType Directory -Path $StateFolder -Force | Out-Null

try {
    if (!(Test-Path -LiteralPath $Duck)) {
        throw '找不到 duck.exe，請確認路徑'
    }
    $Root = (Get-Item -LiteralPath $BackupFolder).FullName.TrimEnd('\') + '\'

    # 透過 SQL Server 備份歷程，取得已完成的完整備份
    $Connection = New-Object System.Data.SqlClient.SqlConnection
    $Connection.ConnectionString =
        "Server=$SqlServer;Database=msdb;Integrated Security=True;Encrypt=True;TrustServerCertificate=True"

    try {
        $Connection.Open()
        $Command = $Connection.CreateCommand()
        $Command.CommandText = @'
SELECT DISTINCT mf.physical_device_name
FROM dbo.backupset AS bs
JOIN dbo.backupmediafamily AS mf
  ON bs.media_set_id = mf.media_set_id
WHERE bs.type = 'D'
  AND bs.backup_finish_date IS NOT NULL
  AND mf.device_type = 2;
'@
        $Table = New-Object System.Data.DataTable
        $Reader = $Command.ExecuteReader()
        $Table.Load($Reader)
    }
    finally {
        $Connection.Dispose()
    }

    foreach ($Row in $Table.Rows) {
        $Path = [string]$Row.physical_device_name
        if (!(Test-Path -LiteralPath $Path -PathType Leaf)) { continue }

        $File = Get-Item -LiteralPath $Path
        if (!$File.FullName.StartsWith(
            $Root, [StringComparison]::OrdinalIgnoreCase)) { continue }
        if ($File.Extension -ne '.bak') { continue }

        $Relative = $File.FullName.Substring($Root.Length)
        $Marker = Join-Path $StateFolder ($Relative + '.uploaded')
        if (Test-Path -LiteralPath $Marker) { continue }

        $Target = $Remote.TrimEnd('/') + '/' + $Relative.Replace('\', '/')
        & $Duck --username $AccessKey --retry --existing overwrite `
            --upload $Target $File.FullName >> $Log 2>&1

        if ($LASTEXITCODE -ne 0) {
            throw "上傳失敗：$($File.FullName)"
        }

        New-Item -ItemType Directory `
            -Path (Split-Path $Marker) -Force | Out-Null
        Set-Content -LiteralPath $Marker -Value (Get-Date -Format o)
        Add-Content $Log "$(Get-Date -Format o) SUCCESS $Relative"
    }
    exit 0
}
catch {
    Add-Content $Log "$(Get-Date -Format o) ERROR $($_.Exception.Message)"
    exit 1
}
```

另存新檔：

```text
C:\SQLUpload\Upload-SQLBackup.ps1
```

儲存類型選 **「所有檔案」**，編碼選 **UTF-8**，避免變成 `.ps1.txt`。

**這份腳本適用於「每次產生不同檔名」的完整備份**，例如 `MyDB_20260922_010000.bak`。如果你的備份一直覆寫同一個檔名，先不要套用，因為成功上傳的檔名會被記錄並略過。首次執行會上傳資料夾內符合備份歷程的既有檔案。

**3. 先手動登入一次 S3**

用之後要執行排程的 Windows 帳號開啟 PowerShell，輸入以下指令，修改其中三個填寫處：

```powershell
& 'C:\填入實際安裝路徑\duck.exe' --username '你的Access Key ID' --list 's3://s3.amazonaws.com/你的Bucket/'
```

如果詢問密碼，輸入 **S3 Secret Key**；若詢問是否儲存，選擇儲存。

再執行一次，確認不必輸入 Secret Key 就能列出內容。`duck` 支援使用 Windows 儲存的認證，也支援 S3 上傳與失敗重試。[官方說明](https://docs.cyberduck.io/cli/)

**4. 手動測試腳本**

在 PowerShell 執行：

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-SQLBackup.ps1"
```

完成後確認：

- Cyberduck 的 S3 `sql-backup` 資料夾出現備份檔。
- `C:\SQLUpload\upload.log` 有 `SUCCESS` 記錄。

執行帳號需要能讀取備份資料夾，以及查詢 SQL Server 的 `msdb` 備份歷程。如果記錄出現登入或權限錯誤，需先處理該權限。

**5. 設定 Windows 工作排程器**

按 `Win + R` → 輸入 `taskschd.msc` → 選 **「建立工作」**。

| 頁籤 | 設定 |
|---|---|
| 一般 | 名稱填「SQL 備份上傳 S3」 |
| 一般 | 使用步驟 3 的 Windows 帳號，**這個工作不要使用 SYSTEM** |
| 一般 | 選「不論使用者登入與否均執行」；不要勾「不要儲存密碼」 |
| 觸發程序 | 每天，重複間隔 **30 分鐘**，持續 **無限期** |
| 動作 | 啟動程式，填下面的程式與引數 |
| 設定 | 勾選錯過排程後立即執行 |
| 設定 | 工作已執行時，選「不要啟動新的執行個體」 |

**程式或指令碼：**

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

**新增引數：**

```text
-NoProfile -NonInteractive -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-SQLBackup.ps1"
```

儲存時輸入該 Windows 帳號的密碼，再右鍵「執行」測試一次。

這樣就會定期上傳，失敗的檔案會在下一次排程再嘗試。**這份腳本負責完整備份上傳；尚未包含備份逾期通知、差異備份與交易記錄備份。**

<a id="qa51"></a>

### 51. 圖片／附件提問

來源：[S197-Q015](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q15>)；2026-09-22

**問題**

# Files mentioned by the user:

## codex-clipboard-07384008-5182-400d-87cc-2bf13f400ff5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-07384008-5182-400d-87cc-2bf13f400ff5.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你現在看到的是**說明首頁**。請直接開啟 [Cyberduck CLI 安裝說明](https://docs.cyberduck.io/cli/#installation)。

1. 在 **Installation** 區塊選 **Windows**。
2. 找到 **MSI Installer**，點下方的 **Download**。
3. 下載 Windows 的 `.msi` 安裝檔並安裝。

安裝完成後，重新開啟 **PowerShell**，輸入：

```powershell
where.exe duck
```

把執行結果貼給我，我幫你確認腳本中的 `$Duck` 路徑要填什麼。

<a id="qa52"></a>

### 52. ?

來源：[S197-Q016](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q16>)；2026-09-22

**問題**

## My request:
?

**歷史回答**

你目前這頁是說明首頁，還不是下載位置。

請先點這個連結：**[開啟 Cyberduck CLI 安裝頁](https://docs.cyberduck.io/cli/#installation)**。

進去後選 **Windows**，再點 **MSI Installer** 下方的 **Download**。如果找不到，貼新畫面給我，我幫你指出位置。

<a id="qa53"></a>

### 53. 圖片／附件提問

來源：[S197-Q017](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q17>)；2026-09-22

**問題**

# Files mentioned by the user:

## codex-clipboard-8da81089-dfa1-4836-9264-5091305f0c27.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8da81089-dfa1-4836-9264-5091305f0c27.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，這次是正確頁面，而且你已經選到 **Windows**。

請再**往下捲一點**，找 **Download／MSI Installer** 的下載連結，下載安裝檔。

畫面下方的 `choco install duck` 是另一種安裝方式，先不用輸入。若往下捲後不確定要點哪個，再貼畫面給我。

<a id="qa54"></a>

### 54. 圖片／附件提問

來源：[S197-Q018](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q18>)；2026-09-22

**問題**

# Files mentioned by the user:

## codex-clipboard-5c1590ff-24b6-40d6-be5e-2185930e43b2.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5c1590ff-24b6-40d6-be5e-2185930e43b2.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

請點黑色指令框下方，藍色的 **「installation options」**。

它就在這句話中：
> See other **installation options** to download the MSI installer for Windows.

進去後找 **Windows → MSI Installer → Download**。

<a id="qa55"></a>

### 55. 圖片／附件提問

來源：[S197-Q019](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q19>)；2026-09-22

**問題**

# Files mentioned by the user:

## codex-clipboard-712e1e7a-5186-45ce-be0f-c63586826207.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-712e1e7a-5186-45ce-be0f-c63586826207.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，請點最上面第一個：

**`duck-9.5.4.45528-x64.msi`**

這是一般 Intel／AMD 64 位元 Windows 使用的安裝檔。下載後點兩下，依安裝精靈完成安裝。

裝好後，重新開啟 **PowerShell**，輸入：

```powershell
where.exe duck
```

把結果貼給我，就能確認下一步的程式路徑。

<a id="qa56"></a>

### 56. 圖片／附件提問

來源：[S197-Q020](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q20>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-2b9eb55b-b875-42f4-b728-9a6c8f416099.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2b9eb55b-b875-42f4-b728-9a6c8f416099.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

已找到程式，路徑是：

```text
C:\Program Files\Cyberduck CLI\duck.exe
```

前面腳本中的 `$Duck` 請填：

```powershell
$Duck = 'C:\Program Files\Cyberduck CLI\duck.exe'
```

**下一步先測試 S3 連線**。在目前的 PowerShell 輸入：

```powershell
& 'C:\Program Files\Cyberduck CLI\duck.exe' --username '你的Access Key ID' --list 's3://你的Server/你的Bucket/'
```

請替換這三處，單引號保留：

| 要替換的文字 | 填寫內容 |
|---|---|
| `你的Access Key ID` | Cyberduck 連線使用的 Access Key ID |
| `你的Server` | Cyberduck 連線設定中的 Server；若是 Amazon S3，通常為 `s3.amazonaws.com` |
| `你的Bucket` | 要存放備份的 Bucket 名稱 |

若提示輸入密碼，填入 **Secret Access Key**；若詢問儲存認證，選擇儲存。

這一步只列出遠端內容，不會上傳檔案。成功後，再設定本機備份路徑與排程。**密鑰直接在你的電腦輸入，不用貼給我。**

<a id="qa57"></a>

### 57. bucket甚麼意思

來源：[S197-Q021](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q21>)；2026-09-23

**問題**

## My request:
bucket甚麼意思

**歷史回答**

**Bucket 就是 S3 的「儲存桶」**，可以把它想成存放檔案的最外層資料夾。

例如你在 Cyberduck 看到：

```text
my-company-backup       ← Bucket 名稱
└─ sql-backup           ← 裡面的資料夾
   └─ database.bak      ← 備份檔
```

那指令裡就填：

```text
s3://你的Server/my-company-backup/
```

打開 Cyberduck，連上 S3 後，回到最上層，通常就能看到 Bucket 名稱。如果不確定，可以貼檔案列表畫面，我幫你指出來。

<a id="qa58"></a>

### 58. 圖片／附件提問

來源：[S197-Q022](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q22>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-414cf692-10dd-4877-b52f-b0b5ea8119c7.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-414cf692-10dd-4877-b52f-b0b5ea8119c7.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你這張圖的 **Bucket 名稱是 `bucket01-prd-sapb1`**。

資料夾結構是：

```text
bucket01-prd-sapb1           ← Bucket
├─ 每日備份
│  ├─ WellShin_VN_Official_1
│  └─ WellShin_US_Official
└─ 每周備份
```

所以剛才的連線測試指令可以填成：

```powershell
& 'C:\Program Files\Cyberduck CLI\duck.exe' --username '你的Access Key ID' --list 's3://你的Server/bucket01-prd-sapb1/'
```

**只要再替換 `你的Access Key ID` 和 `你的Server`**。Server 請照 Cyberduck 連線設定填寫，這張圖沒有顯示它。

先執行這一步，確認能列出「每日備份」和「每周備份」，再接著設定自動上傳。

<a id="qa59"></a>

### 59. 圖片／附件提問

來源：[S197-Q023](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q23>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-cd94340a-e411-4a97-a9b7-9e476490fb17.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cd94340a-e411-4a97-a9b7-9e476490fb17.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這個錯誤是 **Cyberduck CLI 啟動時，找不到需要的 .NET 元件**：

```text
System.Configuration.ConfigurationManager, Version=10.0.0.0
```

還沒連到 S3，所以目前不是 Bucket 或密碼驗證失敗。先確認安裝狀況，請在 PowerShell **依序執行下面三段**：

**① 測試程式本身能否啟動**

```powershell
& 'C:\Program Files\Cyberduck CLI\duck.exe' --help
```

**② 查看已安裝的 .NET 執行環境**

```powershell
dotnet --list-runtimes
```

**③ 查看 duck 要求的執行環境**

```powershell
Get-ChildItem 'C:\Program Files\Cyberduck CLI' -Filter '*.runtimeconfig.json' |
    ForEach-Object { Get-Content -LiteralPath $_.FullName }
```

把結果貼給我；如果某段顯示「找不到指令」或沒有輸出，也一起告訴我。確認後才能判斷要補裝執行環境，還是修復 CLI 安裝。

<a id="qa60"></a>

### 60. 圖片／附件提問

來源：[S197-Q024](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q24>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-9187e0fa-8f08-4faf-8a36-14384e27c12c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9187e0fa-8f08-4faf-8a36-14384e27c12c.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這次是指令格式問題：你輸入了 `$Duck = ... --help`，請改成下面這整行，**開頭是 `&`，不是 `$Duck =`**：

```powershell
& 'C:\Program Files\Cyberduck CLI\duck.exe' --help
```

執行後，再輸入：

```powershell
dotnet --list-runtimes
```

把這兩段的結果貼給我。

<a id="qa61"></a>

### 61. 圖片／附件提問

來源：[S197-Q025](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q25>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-a15fa391-ec6a-45c2-8027-326bc0513a86.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a15fa391-ec6a-45c2-8027-326bc0513a86.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這次程式有被啟動，但仍出現相同的 **缺少 .NET 元件**錯誤。

另外，你輸入的是 `-- help`，中間多了空格；正確是 `--help`。不過目前程式在啟動階段就失敗，先查執行環境。

請直接複製這行執行：

```powershell
dotnet --list-runtimes
```

再執行：

```powershell
Get-ChildItem 'C:\Program Files\Cyberduck CLI' -Filter '*.runtimeconfig.json' |
    ForEach-Object { Get-Content -LiteralPath $_.FullName }
```

把兩段結果貼給我；如果沒有輸出也告訴我，才能確認下一步該補裝什麼。

<a id="qa62"></a>

### 62. 圖片／附件提問

來源：[S197-Q026](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q26>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-c33edbca-a7e3-49c9-9872-011380eafe8d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c33edbca-a7e3-49c9-9872-011380eafe8d.png

## codex-clipboard-40cee87b-d64b-4059-9a67-295baa614bc6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-40cee87b-d64b-4059-9a67-295baa614bc6.png





## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**歷史回答**

確認了，**你已安裝 .NET 10.0.11，不需要再補裝 .NET。**

第二張圖的 `includedFrameworks` 顯示，這份 CLI 自帶 .NET 10.0.8 執行環境。這類程式主要使用隨附元件，因此另外安裝 .NET 不一定能解決缺檔問題。[Microsoft 說明](https://learn.microsoft.com/en-us/dotnet/core/deploying/)

目前比較像是 **CLI 安裝檔案缺少、版本不符，或套件本身有問題**，還不能確定是哪一種。

請先複製下面整段執行，檢查出錯的 DLL 是否存在以及版本：

```powershell
$p = 'C:\Program Files\Cyberduck CLI\System.Configuration.ConfigurationManager.dll'
if (Test-Path -LiteralPath $p) {
    [System.Reflection.AssemblyName]::GetAssemblyName($p).FullName
} else {
    '找不到這個 DLL'
}
```

把結果貼給我，再決定要修復安裝還是更換 CLI 版本。

<a id="qa63"></a>

### 63. 圖片／附件提問

來源：[S197-Q027](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q27>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-ad8e6150-a612-4406-a497-0adc5c3aa618.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-ad8e6150-a612-4406-a497-0adc5c3aa618.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

找到關鍵了：**程式需要 `10.0.0.0`，但安裝資料夾裡的 DLL 是 `8.0.0.0`**，版本不符。

先修復 CLI 安裝：

1. 再次開啟下載的 **`duck-9.5.4.45528-x64.msi`**。
2. 如果有 **Repair（修復）**，選它並完成操作。
3. 完成後重新開啟 PowerShell，執行：

```powershell
& 'C:\Program Files\Cyberduck CLI\duck.exe' --help
```

如果沒有 Repair，請到 Windows「程式和功能」移除 **Cyberduck CLI**，再重新安裝同一個 MSI。一般的 Cyberduck 不需要移除。

**如果修復後仍出現相同錯誤，就可能是這版安裝套件本身的問題**，下一步再更換版本；先不要手動覆蓋 DLL。

<a id="qa64"></a>

### 64. 圖片／附件提問

來源：[S197-Q028](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q28>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-8ed54dc2-a57e-49cf-b1a9-ccd3433b2ded.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8ed54dc2-a57e-49cf-b1a9-ccd3433b2ded.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

還是同一個錯誤，**CLI 尚未能正常啟動**。下一步改用你先前下載列表中的 **9.5.2 版**測試；目前不能保證舊版沒有同樣問題。

請依序操作：

1. 到 Windows「程式和功能」，移除 **Cyberduck CLI**。
2. 回到剛才的下載列表，下載：
   ```text
   duck-9.5.2.45323-x64.msi
   ```
3. 安裝完成後，重新開啟 PowerShell，執行：
   ```powershell
   & 'C:\Program Files\Cyberduck CLI\duck.exe' --help
   ```

**原本有黃色小鴨圖示的 Cyberduck 不用移除，SQL Server 也不用重啟。**

如果成功，會顯示指令說明；把結果貼給我，再繼續設定 S3 上傳。

<a id="qa65"></a>

### 65. 給我英文Windows「程式和功能」

來源：[S197-Q029](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q29>)；2026-09-23

**問題**

## My request:
給我英文Windows「程式和功能」

**歷史回答**

英文是 **Programs and Features**。

按 `Win + R`，輸入：

```text
appwiz.cpl
```

按 Enter，找到 **Cyberduck CLI**，點選 **Uninstall（解除安裝）**。

<a id="qa66"></a>

### 66. 再來'

來源：[S197-Q030](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q30>)；2026-09-23

**問題**

## My request:
再來'

**歷史回答**

移除 **Cyberduck CLI** 後，接著：

1. 回到剛才的下載列表，找到並下載：
   ```text
   duck-9.5.2.45323-x64.msi
   ```
2. 點兩下安裝，依畫面選 **Next → Install → Finish**。
3. 關閉原本的 PowerShell，再重新開啟，貼上：

   ```powershell
   & 'C:\Program Files\Cyberduck CLI\duck.exe' --help
   ```

把執行結果貼給我。正常的話會出現指令說明。

<a id="qa67"></a>

### 67. 圖片／附件提問

來源：[S197-Q031](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q31>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-987f4f91-a71d-467c-b6ed-e47ece9659de.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-987f4f91-a71d-467c-b6ed-e47ece9659de.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

還是同樣的錯誤，換版也沒有解決。抱歉，前面讓你反覆安裝了。

**我們改用 AWS 官方的 AWS CLI 來自動上傳 S3**，原本 Cyberduck 仍可用來查看備份檔。

先做這一步：

1. 點此下載 **[AWS CLI Windows 安裝檔](https://awscli.amazonaws.com/AWSCLIV2.msi)**。
2. 開啟安裝檔，依序完成 **Next → Install → Finish**。
3. 關閉 PowerShell，再重新開啟，輸入：

   ```powershell
   aws --version
   ```

看到 `aws-cli/2...` 就代表安裝成功。[官方安裝說明](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

把結果貼給我，接著設定 S3 帳號和備份路徑。前面的 `duck` 上傳腳本先不要加入排程，後面會改成 AWS CLI 版本。

<a id="qa68"></a>

### 68. 圖片／附件提問

來源：[S197-Q032](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q32>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-c8b28f05-ad75-4971-b075-1fe10b5a094f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c8b28f05-ad75-4971-b075-1fe10b5a094f.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這個畫面保持預設即可，不用修改路徑。

按 **Next → Install → Finish**。

完成後，關閉原本的 PowerShell，再重新開啟，輸入：

```powershell
aws --version
```

把執行結果貼給我。

<a id="qa69"></a>

### 69. 圖片／附件提問

來源：[S197-Q033](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q33>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-936c391c-5144-4cc1-bfce-4d7a4e50d363.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-936c391c-5144-4cc1-bfce-4d7a4e50d363.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**安裝成功，畫面顯示 AWS CLI 2.37.0。**

接下來在 PowerShell 輸入：

```powershell
aws configure --profile sqlbackup
```

它會依序詢問四項，填完一項就按 Enter：

| 畫面提示 | 填什麼 |
|---|---|
| `AWS Access Key ID` | Cyberduck 使用的 Access Key ID |
| `AWS Secret Access Key` | 對應的 Secret Key |
| `Default region name` | Bucket 所在區域；不知道先按 Enter 留空 |
| `Default output format` | 填 `json` |

這會建立名為 `sqlbackup` 的連線設定。[官方說明](https://docs.aws.amazon.com/cli/latest/reference/configure/)

完成後，執行以下指令測試讀取：

```powershell
aws s3 ls "s3://bucket01-prd-sapb1/" --profile sqlbackup
```

正常會列出「每日備份」「每周備份」。**只貼最後這段的結果即可，不要截到前面輸入的 Secret Key。**

<a id="qa70"></a>

### 70. 圖片／附件提問

來源：[S197-Q034](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q34>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-c6532b87-76d9-493a-9761-602b456838e9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c6532b87-76d9-493a-9761-602b456838e9.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**連線成功了！** 已經能列出 S3 的「每周備份」和「每日備份」。

不過，這張截圖包含完整的 **Access Key 與 Secret Key**。請到 AWS IAM 將這組金鑰停用並更換，再執行以下指令更新本機設定：

```powershell
aws configure --profile sqlbackup
```

新金鑰只在你的電腦輸入，不要再貼出。

接著先設定**本機備份資料夾**，在 PowerShell 輸入下面這行，把路徑換成實際位置：

```powershell
$BackupFolder = 'D:\SQLBackup'
```

再執行以下指令，確認能找到備份檔：

```powershell
Get-ChildItem -LiteralPath $BackupFolder -Filter '*.bak' -File -Recurse |
    Select-Object -First 10 FullName, Length, LastWriteTime
```

這一步只列出本機檔案。貼出這段結果後，就能接著設定上傳位置，保留你現有的「每日備份／資料庫名稱」目錄結構。

<a id="qa71"></a>

### 71. 用意是甚麼

來源：[S197-Q035](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q35>)；2026-09-23

**問題**

## My request:
用意是甚麼

**歷史回答**

剛才那兩段指令，是為了**確認要上傳哪些本機備份檔**：

- `$BackupFolder = 'D:\SQLBackup'`：告訴 PowerShell，你的 SQL 備份放在哪個資料夾。
- `Get-ChildItem ...`：列出該資料夾及子資料夾中的前 10 個 `.bak` 檔案，確認路徑正確、檔案找得到。

**目前只是查看，不會上傳、刪除或修改檔案。**

確認後，才接著設定「把備份檔複製到 S3」及「Windows 自動排程」。

另外，更換金鑰是因為上一張截圖露出了完整登入憑證，需要讓舊金鑰失效。

<a id="qa72"></a>

### 72. 圖片／附件提問

來源：[S197-Q036](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q36>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-95272f18-dda3-4ce7-ba6d-94587120a77f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-95272f18-dda3-4ce7-ba6d-94587120a77f.png





## My request:

**歷史回答**

錯誤表示 **`D:\SQLBackup` 這個資料夾不存在**。那是我提供的範例，需要換成你實際存放 `.bak` 的位置，抱歉前面沒有說得夠清楚。

請在這台伺服器上：

1. 開啟「檔案總管」，找到 SQL 備份的 `.bak` 檔。
2. 點上方網址列，複製所在資料夾的完整路徑。
3. 在 PowerShell 重新輸入：

   ```powershell
   $BackupFolder = '把實際資料夾路徑貼在這裡'
   ```

再執行：

```powershell
Get-ChildItem -LiteralPath $BackupFolder -Filter '*.bak' -File -Recurse |
    Select-Object -First 10 FullName, Length, LastWriteTime
```

**不用另外建立 `D:\SQLBackup`**，我們要找的是原本已經有備份檔的資料夾。

<a id="qa73"></a>

### 73. 我需要在點進去嗎 還是在這層就好

來源：[S197-Q037](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q37>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-f4f89833-4761-4164-a55f-81e64ba70d57.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f4f89833-4761-4164-a55f-81e64ba70d57.png





## My request:
我需要在點進去嗎 還是在這層就好

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**這層就能確認路徑，不用再點進去。** 你的備份分成：

- `D:\每日備份`
- `D:\每周備份`
- `D:\每月備份`

我們先設定「每日備份」。請在 PowerShell 貼上：

```powershell
$BackupFolder = 'D:\每日備份'
```

再執行：

```powershell
Get-ChildItem -LiteralPath $BackupFolder -Filter '*.bak' -File -Recurse |
    Select-Object -First 10 FullName, Length, LastWriteTime
```

`-Recurse` 會連裡面的資料庫子資料夾一起找，**不用指定到個別 `.bak` 檔案**。這一步只列出檔案，還不會上傳。

<a id="qa74"></a>

### 74. 圖片／附件提問

來源：[S197-Q038](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q38>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-252f5306-5573-4bf6-be7f-25aab18b284d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-252f5306-5573-4bf6-be7f-25aab18b284d.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**路徑正確，兩個資料庫的 `.bak` 都找到了。**

接著在 PowerShell 貼上這一整行，先預覽上傳清單：

```powershell
aws s3 sync "D:\每日備份" "s3://bucket01-prd-sapb1/每日備份/" --profile sqlbackup --exclude "*" --include "*.bak" --dryrun
```

它會保留資料夾結構，例如：

```text
D:\每日備份\WellShin_US_Official\檔名.bak
    ↓
S3 → 每日備份 → WellShin_US_Official → 檔名.bak
```

**目前有 `--dryrun`，只會顯示預計上傳哪些檔案，不會實際上傳或刪除。** [官方說明](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html)

把結果貼給我。確認位置後，再進行實際上傳；設定自動排程時，也會加入備份完成的檢查，避免傳到還在寫入的檔案。

<a id="qa75"></a>

### 75. 圖片／附件提問

來源：[S197-Q039](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q39>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-e9bc5325-34d1-455f-b30e-a6766e6d69fa.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e9bc5325-34d1-455f-b30e-a6766e6d69fa.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**預覽成功，目的地正確**：檔案會放到 S3「每日備份」底下各自的資料庫資料夾。

先確認 SSMS 的備份工作已完成、目前沒有正在寫入這些檔案，再執行實際上傳：

```powershell
aws s3 sync "D:\每日備份" "s3://bucket01-prd-sapb1/每日備份/" --profile sqlbackup --exclude "*" --include "*.bak"
```

這次移除了 `--dryrun`，**會真的上傳**。既有同名檔案若被判定有差異，會更新；不會刪除本機或 S3 的檔案。

等上傳結束、出現 `PS C:\Users\Administrator>` 後，立即輸入：

```powershell
$LASTEXITCODE
```

結果是 **`0`** 表示這次執行成功。把結果貼給我，接著設定自動排程。

<a id="qa76"></a>

### 76. 這樣嗎

來源：[S197-Q040](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q40>)；2026-09-23

**問題**

## My request:
這樣嗎

**歷史回答**

我這則沒有看到新的截圖，還不能確認結果。

如果你已執行上傳，等畫面回到 `PS C:\Users\Administrator>` 後，輸入：

```powershell
$LASTEXITCODE
```

顯示 **`0`** 就代表這次上傳成功；若不是 `0`，請貼錯誤畫面給我。

<a id="qa77"></a>

### 77. 那如果檔名重複的話 會自動檔下嗎

來源：[S197-Q041](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q41>)；2026-09-23

**問題**

## My request:
那如果檔名重複的話 會自動檔下嗎

**歷史回答**

如果你是問「同名檔案會不會自動跳過」，**會比較後決定，不是只看檔名**。

目前這個 `aws s3 sync` 指令的行為是：

| 情況 | 處理方式 |
|---|---|
| S3 同一路徑沒有這個檔案 | 上傳 |
| 同名、大小相同，且本機修改時間沒有比較新 | 跳過 |
| 同名，但大小不同，或本機修改時間比較新 | 重新上傳，更新 S3 的同名檔案 |

你的備份檔名包含日期與時間，所以**每天的新備份通常會另外新增一份**，不會蓋掉前一天的檔案。

另外，它不會逐一比對檔案內容；目前指令也沒有加 `--delete`，因此本機舊備份刪除後，S3 的那份仍會保留。[官方說明](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html)

<a id="qa78"></a>

### 78. 那如果我想要的事比對呢

來源：[S197-Q042](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q42>)；2026-09-23

**問題**

## My request:
那如果我想要的事比對呢

**歷史回答**

可以，要看你想比對到什麼程度：

| 比對方式 | 能確認什麼 |
|---|---|
| **檔名、大小、修改時間** | 哪些檔案需要上傳，目前 `sync` 就是這種 |
| **檔案內容的校驗碼（checksum）** | 確認本機與 S3 的檔案內容是否一致，即使同名、同大小也能檢查 |

你是想要 **「確認有沒有漏傳」**，還是 **「確認上傳後的備份內容完全一致」**？

如果是備份驗證，我建議做第二種；但檔案一致仍不代表 SQL 備份一定能還原，還原測試是另一項檢查。

<a id="qa79"></a>

### 79. 確認有沒有漏傳 以及我的SSMS的會刪七天前的資料

來源：[S197-Q043](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q43>)；2026-09-23

**問題**

## My request:
確認有沒有漏傳 以及我的SSMS的會刪七天前的資料

**歷史回答**

了解，你要的是：**檢查本機備份有沒有漏傳，而且本機刪除七天前的備份時，S3 仍保留那份。**

目前這個指令就符合這個方向：

```powershell
aws s3 sync "D:\每日備份" "s3://bucket01-prd-sapb1/每日備份/" --profile sqlbackup --exclude "*" --include "*.bak"
```

它會：

- **補傳**本機有、S3 沒有的備份。
- 跳過判定不需更新的檔案。
- **保留 S3 舊備份**，即使 SQL 備份清理工作已刪除本機七天前的檔案。不要加 `--delete`。

如果只想檢查、不上傳，就用：

```powershell
aws s3 sync "D:\每日備份" "s3://bucket01-prd-sapb1/每日備份/" --profile sqlbackup --exclude "*" --include "*.bak" --dryrun
```

出現 `(dryrun) upload:` 表示該檔案需要補傳或更新；**沒有清單且執行成功，代表目前本機現存的 `.bak` 都沒有待同步項目**。

但有一個限制：**如果某份備份從未上傳，就已被本機七天清理刪掉，這個指令就無法再發現或補傳它。** 所以自動排程應在備份完成後執行，並記錄上傳失敗，讓問題能在七天內處理。

另外，若 S3 本身設定了到期刪除的 Lifecycle 規則，遠端檔案仍會依那個規則清理。

<a id="qa80"></a>

### 80. 那先上傳看看

來源：[S197-Q044](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q44>)；2026-09-23

**問題**

## My request:
那先上傳看看

**歷史回答**

可以，在伺服器的 PowerShell 貼上這一整行：

```powershell
aws s3 sync "D:\每日備份" "s3://bucket01-prd-sapb1/每日備份/" --profile sqlbackup --exclude "*" --include "*.bak"
```

**先確認 SQL 備份工作已完成，再執行。** 這會補傳或更新需要同步的 `.bak`，保留子資料夾結構，也不會刪除 S3 舊備份。

上傳期間保持 PowerShell 開啟。等畫面回到 `PS C:\Users\Administrator>`，立即輸入：

```powershell
$LASTEXITCODE
```

顯示 **`0`** 代表本次執行成功；其他數字請連同錯誤訊息貼給我。

<a id="qa81"></a>

### 81. 沒問題了 那可以設定固定了 然後我想要用比對的方式

來源：[S197-Q045](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q45>)；2026-09-23

**問題**

## My request:
沒問題了 那可以設定固定了 然後我想要用比對的方式

**歷史回答**

可以，**就用 `sync` 比對本機與 S3，再由 Windows 固定執行**：

- 本機有、S3 沒有：補傳。
- 大小與時間判定不需更新：跳過。
- 本機七天前的備份被刪除：**S3 仍保留**。

先把「每日備份」設定好。

**1. 建立腳本**

建立資料夾：

```text
C:\SQLUpload
```

開啟記事本，貼上：

```powershell
$ErrorActionPreference = 'Stop'

# 本機備份路徑
$Source = 'D:\每日備份'

# S3 目的地
$Destination = 's3://bucket01-prd-sapb1/每日備份/'

$Aws = 'C:\Program Files\Amazon\AWSCLIV2\aws.exe'
$Log = "C:\SQLUpload\upload-$(Get-Date -Format 'yyyy-MM-dd').log"

try {
    if (!(Test-Path -LiteralPath $Source -PathType Container)) {
        throw "找不到備份資料夾：$Source"
    }
    if (!(Test-Path -LiteralPath $Aws -PathType Leaf)) {
        throw "找不到 AWS CLI：$Aws"
    }

    Add-Content -LiteralPath $Log -Value "$(Get-Date -Format o) START"

    & $Aws s3 sync $Source $Destination `
        --profile sqlbackup `
        --exclude '*' --include '*.bak' `
        --no-progress >> $Log 2>&1

    $Result = $LASTEXITCODE
    Add-Content -LiteralPath $Log -Value "$(Get-Date -Format o) END ExitCode=$Result"
    exit $Result
}
catch {
    Add-Content -LiteralPath $Log -Value "$(Get-Date -Format o) ERROR $($_.Exception.Message)"
    exit 1
}
```

另存為：

```text
C:\SQLUpload\Upload-DailyBackup.ps1
```

儲存類型選 **All Files（所有檔案）**，編碼選 **UTF-8 with BOM**；舊版記事本若只有 **UTF-8**，選它即可。

**2. 手動測試腳本**

在 PowerShell 執行：

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-DailyBackup.ps1"
```

這次輸出會寫進 `C:\SQLUpload` 的每日紀錄檔。完成後輸入：

```powershell
$LASTEXITCODE
```

確認是 **`0`**，再設定排程。

**3. 建立 Windows 排程**

按 `Win + R`，輸入：

```text
taskschd.msc
```

右側選 **Create Task（建立工作）**。

在 **General（一般）**：

- Name：`SQL Daily Backup to S3`
- 使用你剛才成功上傳的 Windows 帳號。
- 選 **Run whether user is logged on or not**。
- **不要勾選 Do not store password**。
- 不要換成 `SYSTEM`，因為 `sqlbackup` 認證存放在目前帳號底下。

在 **Triggers（觸發程序）→ New**：

- 選 **On a schedule → Daily**。
- 時間設定在每日 SQL 備份**完成之後**，並留一些緩衝。
- 勾選 **Enabled**。

**目前腳本會比對檔案，但不會判斷 SQL 備份是否完成，所以執行時間必須避開備份寫入期間。** 例如平常凌晨 1 點完成，可先安排凌晨 3 點；這只是例子，請依你的實際完成時間設定。

在 **Actions（動作）→ New**：

**Program/script：**

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

**Add arguments：**

```text
-NoProfile -NonInteractive -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-DailyBackup.ps1"
```

**Start in：**

```text
C:\SQLUpload
```

在 **Settings（設定）**：

- 勾選 **Allow task to be run on demand**。
- 勾選 **If the task fails, restart every**：設為 `30 minutes`。
- **Attempt to restart up to**：設為 `3 times`。
- **If the task is already running**：選 **Do not start a new instance**。

按 **OK**，輸入該 Windows 帳號的登入密碼。

**4. 測試排程**

右鍵這個工作 → **Run**。執行結束後按重新整理：

- **Last Run Result = `0x0`**：成功。
- 到 `C:\SQLUpload` 查看當天紀錄，應有 `END ExitCode=0`。

這樣每天會自動比對並補傳，失敗時重試。**目前有紀錄與重試，尚未設定失敗通知；範圍也只有「每日備份」。**

<a id="qa82"></a>

### 82. 如果本機七天前刪除 我可以跟著一起刪除嗎

來源：[S197-Q046](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q46>)；2026-09-23

**問題**

## My request:
如果本機七天前刪除 我可以跟著一起刪除嗎

**歷史回答**

可以，**在 `sync` 指令加上 `--delete`**，S3 就會跟著刪除「本機已經不存在」的 `.bak`。

但要注意：**它不看是否超過七天，只看本機有沒有這個檔案。** 本機誤刪的備份，也會跟著從 S3 刪除。

先執行預覽，確認刪除清單：

```powershell
aws s3 sync "D:\每日備份" "s3://bucket01-prd-sapb1/每日備份/" --profile sqlbackup --exclude "*" --include "*.bak" --delete --dryrun
```

看到 `(dryrun) delete:` 就是預計刪除的 S3 檔案，**現在不會真的刪除**。

確認清單符合需求後，將腳本中的這行：

```powershell
--no-progress >> $Log 2>&1
```

改成：

```powershell
--delete --no-progress >> $Log 2>&1
```

之後排程就會同步上傳與刪除，範圍僅限 **S3「每日備份」底下的 `.bak`**，不影響「每周備份」或「每月備份」。

如果你只是希望 **S3 自己保留七天**，則可以設定 S3 Lifecycle 到期規則，讓遠端保留期限不受本機誤刪影響。

<a id="qa83"></a>

### 83. 那先不刪好了

來源：[S197-Q047](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q47>)；2026-09-23

**問題**

## My request:
那先不刪好了

**歷史回答**

好，**維持不加 `--delete`**。

這樣排程會比對並補傳備份，本機七天前的檔案刪除後，**S3 的備份仍會保留**。

如果你已修改腳本，將 `--delete` 移除即可；`--dryrun` 也不要留在正式排程裡。

<a id="qa84"></a>

### 84. OK 在給我一次腳本

來源：[S197-Q048](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q48>)；2026-09-23

**問題**

## My request:
OK 在給我一次腳本

**歷史回答**

這份是**比對後補傳、不刪除 S3 舊備份**的版本。可以整段複製：

```powershell
$ErrorActionPreference = 'Stop'

# 本機備份資料夾
$Source = 'D:\每日備份'

# S3 目的地，保留資料庫子資料夾結構
$Destination = 's3://bucket01-prd-sapb1/每日備份/'

# AWS CLI 程式與連線設定
$Aws = 'C:\Program Files\Amazon\AWSCLIV2\aws.exe'
$Profile = 'sqlbackup'

# 每天一份執行紀錄
$LogFolder = 'C:\SQLUpload'
New-Item -ItemType Directory -Path $LogFolder -Force | Out-Null
$Log = Join-Path $LogFolder "upload-$(Get-Date -Format 'yyyy-MM-dd').log"

try {
    if (!(Test-Path -LiteralPath $Source -PathType Container)) {
        throw "找不到備份資料夾：$Source"
    }

    if (!(Test-Path -LiteralPath $Aws -PathType Leaf)) {
        throw "找不到 AWS CLI：$Aws"
    }

    Add-Content -LiteralPath $Log -Encoding UTF8 `
        -Value "$(Get-Date -Format o) START"

    # 比對並上傳；不刪除本機或 S3 檔案
    & $Aws s3 sync $Source $Destination `
        --profile $Profile `
        --exclude '*' `
        --include '*.bak' `
        --no-progress >> $Log 2>&1

    $Result = $LASTEXITCODE

    Add-Content -LiteralPath $Log -Encoding UTF8 `
        -Value "$(Get-Date -Format o) END ExitCode=$Result"

    exit $Result
}
catch {
    Add-Content -LiteralPath $Log -Encoding UTF8 `
        -Value "$(Get-Date -Format o) ERROR $($_.Exception.Message)"

    exit 1
}
```

建立 `C:\SQLUpload` 資料夾，將腳本存成：

```text
C:\SQLUpload\Upload-DailyBackup.ps1
```

記事本另存時選 **All Files（所有檔案）**，編碼選 **UTF-8 with BOM**，避免存成 `.ps1.txt`。

用先前設定 `sqlbackup` 的 Windows 帳號測試：

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-DailyBackup.ps1"
```

執行完立即輸入：

```powershell
$LASTEXITCODE
```

**`0` 代表成功**，詳細紀錄在 `C:\SQLUpload\upload-日期.log`。

這份腳本會實際上傳，**請安排在 SQL 備份完成後執行**；它本身不檢查備份是否仍在寫入。

<a id="qa85"></a>

### 85. 這樣嗎

來源：[S197-Q049](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q49>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-7166be7d-4e58-4296-9ab3-5c7f49e6b608.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7166be7d-4e58-4296-9ab3-5c7f49e6b608.png





## My request:
這樣嗎

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**資料夾位置對了，但目前只看到 `.log`，還沒看到要排程執行的 `.ps1` 腳本。**

請這樣儲存：

1. 開啟記事本，貼上我上一則提供的完整腳本。
2. 選 **File → Save As**。
3. 儲存位置選 `C:\SQLUpload`。
4. **File name** 填：
   ```text
   Upload-DailyBackup.ps1
   ```
5. **Save as type** 選 **All Files (*.*)**。
6. **Encoding** 選 **UTF-8 with BOM**，再按 **Save**。

接著在檔案總管選 **View → File name extensions**，確認完整檔名是：

```text
Upload-DailyBackup.ps1
```

不是 `Upload-DailyBackup.ps1.txt`。目前的 `.log` 可以保留。

<a id="qa86"></a>

### 86. 圖片／附件提問

來源：[S197-Q050](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q50>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-bb130d72-ed2f-46ea-8b4c-3914f05453e2.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-bb130d72-ed2f-46ea-8b4c-3914f05453e2.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，**`Upload-DailyBackup.ps1` 已經存成正確的 PowerShell 腳本格式**。

接下來在 PowerShell 貼上，測試執行：

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-DailyBackup.ps1"
```

等執行結束、提示字元重新出現後，再輸入：

```powershell
$LASTEXITCODE
```

**顯示 `0` 就代表成功**。過程可能沒有畫面輸出，因為結果寫進 `.log`。把結果貼給我，接著設定固定排程。

<a id="qa87"></a>

### 87. 圖片／附件提問

來源：[S197-Q051](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q51>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-cfd827ec-5d9e-4b10-912f-0b6d41a7c93d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cfd827ec-5d9e-4b10-912f-0b6d41a7c93d.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**顯示 `0`，腳本測試成功。** 現在設定每天自動執行。

1. 按 `Win + R`，輸入 `taskschd.msc`，按 Enter。
2. 右側選 **Create Task…**。

**General（一般）**

- Name：`SQL Daily Backup to S3`
- 使用剛才測試成功的 **Administrator 帳號**，不要改成 SYSTEM。
- 選 **Run whether user is logged on or not**。
- 不要勾 **Do not store password**。

**Triggers → New（觸發程序）**

- Begin the task：**On a schedule**
- 選 **Daily**，每 `1` 天。
- 時間設在 **SQL 備份完成之後**，並留緩衝。
- 勾 **Enabled**，按 OK。

例如你確認備份每天凌晨 1 點前完成，就可以設凌晨 3 點。**腳本本身不會檢查備份是否仍在寫入。**

**Actions → New（動作）**

Action 選 **Start a program**，填入：

Program/script：

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

Add arguments：

```text
-NoProfile -NonInteractive -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-DailyBackup.ps1"
```

Start in：

```text
C:\SQLUpload
```

**Settings（設定）**

- 勾 **Allow task to be run on demand**。
- 勾 **If the task fails, restart every**：`30 minutes`。
- **Attempt to restart up to**：`3 times`。
- **If the task is already running**：選 **Do not start a new instance**。

按 **OK**，輸入該 Windows 帳號的密碼。

最後，右鍵新工作 → **Run**，等完成後按重新整理。**Last Run Result 顯示 `0x0`，才代表排程環境也測試成功。**

這個排程會比對並補傳「每日備份」，**不會刪除 S3 舊檔案**。

<a id="qa88"></a>

### 88. 圖片／附件提問

來源：[S197-Q052](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q52>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-d13fe7ba-08a7-4b5c-b072-eea6858c0fbf.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d13fe7ba-08a7-4b5c-b072-eea6858c0fbf.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

請點畫面 **Apps 下方、有時鐘圖示的「Task Scheduler」**（工作排程器）。

開啟後，在右側點 **Create Task…（建立工作）**，就可以開始設定。

<a id="qa89"></a>

### 89. 圖片／附件提問

來源：[S197-Q053](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q53>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-c60559b0-95d8-4da6-9dbd-e62163b79f27.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c60559b0-95d8-4da6-9dbd-e62163b79f27.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

目前設定是 **每天晚上 11:30 執行一次**，而且 **Enabled 已勾選**。

**只要 SQL 備份確定在晚上 11:30 前完成，這樣就可以。** 前面檔名約是晚上 10:59，但檔名無法確認實際完成時間，請留足備份時間。

確認時間後按 **OK**，接著到 **Actions → New**，填入：

**Program/script：**
```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

**Add arguments：**
```text
-NoProfile -NonInteractive -ExecutionPolicy RemoteSigned -File "C:\SQLUpload\Upload-DailyBackup.ps1"
```

**Start in：**
```text
C:\SQLUpload
```

填好後按 **OK**。

<a id="qa90"></a>

### 90. 再來

來源：[S197-Q054](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q54>)；2026-09-23

**問題**

## My request:
再來

**歷史回答**

接著設定失敗重試，再儲存並測試。

**1. 點「Settings」頁籤**

- 勾選 **Allow task to be run on demand**。
- 勾選 **If the task fails, restart every**，選 `30 minutes`。
- **Attempt to restart up to** 填 `3 times`。
- 最下方 **If the task is already running…** 選 **Do not start a new instance**。

**2. 回到「General」確認**

- 使用剛才上傳成功的 **Administrator 帳號**。
- 選 **Run whether user is logged on or not**。
- **Do not store password** 不要勾選。

按 **OK**，輸入該 Windows 帳號的登入密碼。

**3. 測試排程**

在 **Task Scheduler Library** 找到剛建立的工作，按右鍵 → **Run**。

等工作結束後按 **F5** 重新整理，查看 **Last Run Result**：

- **`0x0`**：排程執行成功。
- **`0x41301`**：仍在執行，稍後再看。
- 其他代碼：貼畫面給我確認。

也可以開啟 `C:\SQLUpload` 裡今天的 `.log`，確認最新一筆是 **`END ExitCode=0`**。

<a id="qa91"></a>

### 91. 我剛剛設定到一半 我好像關掉 我不太確定

來源：[S197-Q055](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q55>)；2026-09-23

**問題**

## My request:
我剛剛設定到一半 我好像關掉 我不太確定

**歷史回答**

沒關係，**先確認有沒有儲存成功，不用急著重建**。

1. 開啟 **Task Scheduler**。
2. 點左邊的 **Task Scheduler Library**。
3. 按 **F5** 重新整理，在中間清單找：
   ```text
   SQL Daily Backup to S3
   ```
   如果你用了其他名稱，就找你設定的名稱。

- **找得到**：點兩下開啟，檢查 **Triggers** 是否有每天晚上 **11:30**，以及 **Actions** 是否有執行 PowerShell 的設定，可以繼續補完。
- **找不到**：如果剛才還沒按建立工作視窗最下方的 **OK**，通常尚未儲存，需要重新建立。

你可以貼 **Task Scheduler Library 的工作清單**，我幫你確認。之前存好的 `.ps1` 腳本不用重做。

<a id="qa92"></a>

### 92. 圖片／附件提問

來源：[S197-Q056](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q56>)；2026-09-23

**問題**

# Files mentioned by the user:

## codex-clipboard-14a8ea93-faa9-4a0d-b1c9-c4526e09e54c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-14a8ea93-faa9-4a0d-b1c9-c4526e09e54c.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**有儲存成功，不用重建。** 畫面中的 `SQL Daily Ba...` 就是這個工作，目前狀態顯示 **Running（執行中）**。

另外，現在的排程是 **每天凌晨 12:00（00:00）**，不是之前的晚上 11:30。如果備份在午夜前已完成，這個時間可以。

接著：

1. 先不要再按 **Run**，等目前工作完成。
2. 按 **F5** 重新整理，確認狀態變成 **Ready**。
3. 開啟 `C:\SQLUpload` 裡今天的 `.log`，查看最新一筆是否為：
   ```text
   END ExitCode=0
   ```

若有這筆，就代表本次排程執行成功。再點右側 **Properties → Settings**，確認失敗重試已設為 **每 30 分鐘、最多 3 次**。

<a id="qa93"></a>

### 93. 對

來源：[S197-Q057](<../../原始問答/S197_診斷 SQL Server Agent 備份排程.md#q57>)；2026-09-23

**問題**

## My request:
對

**歷史回答**

好，那就維持 **每天凌晨 00:00** 自動比對並補傳「每日備份」，**不刪除 S3 舊檔**。

最後確認今天 `.log` 的最新紀錄有 **`END ExitCode=0`**，以及失敗重試已設定完成即可。

