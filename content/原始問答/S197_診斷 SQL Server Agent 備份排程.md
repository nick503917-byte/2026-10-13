# 診斷 SQL Server Agent 備份排程

來源編號：S197  
聊天 ID：`01a0c85b-ee82-77f2-9de0-19fb10219132`  
聊天起始日期（台北）：2026-09-22  
保存位置：一般本機紀錄  
問答段落：57

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S197-Q001｜。

日期：2026-09-22

**你的提問**

。

**當時回答**

我在，請告訴我你遇到什麼電腦問題。

---

<a id="q2"></a>

## S197-Q002｜我想問 SSMS 我有設定排程 SQL Server Agent 去做備份。 但前陣子好像莫名會有關閉的行為發生，導致沒有備份到。 所以我想用windows或是其他方式去固定檢查

日期：2026-09-22

**你的提問**

我想問 

SSMS 我有設定排程 SQL Server Agent 去做備份。

但前陣子好像莫名會有關閉的行為發生，導致沒有備份到。

所以我想用windows或是其他方式去固定檢查

**當時回答**

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

---

<a id="q3"></a>

## S197-Q003｜我要怎麼做

日期：2026-09-22

**你的提問**

我要怎麼做

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q4"></a>

## S197-Q004｜SQL Server Agent 服務停止

日期：2026-09-22

**你的提問**

SQL Server Agent 服務停止

**當時回答**

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

---

<a id="q5"></a>

## S197-Q005｜圖片／附件提問

日期：2026-09-22

**你的提問**

# Files mentioned by the user:

## codex-clipboard-1c9a721a-b7ac-40f5-91c5-5deb080b9fe5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1c9a721a-b7ac-40f5-91c5-5deb080b9fe5.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q6"></a>

## S197-Q006｜這個嗎

日期：2026-09-22

**你的提問**

這個嗎

**當時回答**

對，如果你指的是上一張圖的 **「SQL Server Agent (MSSQLSERVER)」**，就是這個。

點兩下開啟，把「啟動類型」從 **手動（Manual）** 改成 **自動（Automatic）**，再按「套用」。

如果你已開啟另一個設定視窗，請貼上截圖，我幫你確認。

---

<a id="q7"></a>

## S197-Q007｜圖片／附件提問

日期：2026-09-22

**你的提問**

# Files mentioned by the user:

## codex-clipboard-6f1c4a62-3310-4c3f-8471-e11d32b95461.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6f1c4a62-3310-4c3f-8471-e11d32b95461.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，就是這裡。選 **Automatic（自動）**，然後按右下角 **Apply（套用）**。

接著點上方 **Recovery（復原）**，設定：

- **First failure**：Restart the Service
- **Second failure**：Restart the Service
- **Subsequent failures**：Restart the Service
- **Restart service after**：1 minute

完成後按 **Apply → OK**。目前服務是 Running，不需要按 Stop。

---

<a id="q8"></a>

## S197-Q008｜這樣會不會很好效能

日期：2026-09-22

**你的提問**

這樣會不會很好效能

**當時回答**

你是指「會不會很耗效能」嗎？**這樣設定幾乎不會增加效能負擔。**

- **Automatic（自動）**：只是讓服務在開機時自動啟動。目前已經是 Running，改完後平常的負擔不變。
- **Recovery（復原）**：只有服務失敗時才嘗試重新啟動。
- **每 5 分鐘檢查**：如果之後有設定，也只是短暫查詢服務狀態，負擔很小。

真正比較會使用 CPU、磁碟 I/O 的是**備份工作執行期間**，不是這些啟動與檢查設定。

---

<a id="q9"></a>

## S197-Q009｜我還有另一個問題 我用cyberduck 做異地備份 然後我在想 我ssms 的備份檔 我可以用甚麼辦法自動在備份一份到cyberduck

日期：2026-09-22

**你的提問**

我還有另一個問題 我用cyberduck 做異地備份 然後我在想 我ssms 的備份檔 我可以用甚麼辦法自動在備份一份到cyberduck

**當時回答**

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

---

<a id="q10"></a>

## S197-Q010｜但我是免費檔的

日期：2026-09-22

**你的提問**

但我是免費檔的

**當時回答**

你是指 **Cyberduck 免費版**嗎？如果是，**可以自動上傳，不需要購買付費版**。

做法是另外安裝免費的 **Cyberduck CLI（`duck`）**，再用 Windows 工作排程器執行上傳指令。[官方工具介紹](https://duck.sh/)

不過，異地儲存空間本身的容量或流量費用，要看你使用的服務。

你目前 Cyberduck 是連到哪裡？例如 **公司 NAS、SFTP 伺服器、Google Drive 或 S3**？不知道的話，貼連線畫面給我看即可，記得遮住密碼與金鑰。

---

<a id="q11"></a>

## S197-Q011｜S3

日期：2026-09-22

**你的提問**

S3

**當時回答**

可以，**Cyberduck 免費版連到 S3，也能設定自動上傳 SQL 備份**。可以使用免費的 `duck` 命令列工具搭配 Windows 工作排程器；S3 儲存空間與傳輸費用則另外計算。[官方說明](https://docs.cyberduck.io/cli/)

要幫你整理成可以直接操作的設定，先確認三件事：

1. **是 Amazon S3，還是其他相容 S3 的服務？** 不確定可以貼 Cyberduck 連線設定畫面，遮住帳號、Access Key 與 Secret Key。
2. **SQL 備份檔放在哪個資料夾？** 例如 `D:\SQLBackup`。
3. **Cyberduck 和 SQL Server 裝在同一台電腦嗎？**

知道這些後，就能設定「確認備份完成 → 上傳到 S3 → 記錄結果、失敗重試」。

---

<a id="q12"></a>

## S197-Q012｜2JO4

日期：2026-09-22

**你的提問**

2JO4

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q13"></a>

## S197-Q013｜同一台電腦

日期：2026-09-22

**你的提問**

同一台電腦

**當時回答**

了解，**Cyberduck 和 SQL Server 在同一台電腦**，可以直接用 Windows 排程上傳本機備份檔。

接下來還需要：

1. **備份檔的資料夾路徑**，例如 `D:\SQLBackup`。
2. **S3 的連線資訊**：服務端點（Server）、Bucket 名稱，以及要存放的遠端資料夾。

你可以貼 Cyberduck 的「編輯書籤／連線設定」畫面，**遮住 Access Key、Secret Key 和密碼**。我確認後，就能提供對應的上傳指令與排程步驟。

---

<a id="q14"></a>

## S197-Q014｜你跟我說哪裡要填 備份檔的路徑就可以了 先跟我說怎麼設定

日期：2026-09-22

**你的提問**

你跟我說哪裡要填 備份檔的路徑就可以了 先跟我說怎麼設定

**當時回答**

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

---

<a id="q15"></a>

## S197-Q015｜圖片／附件提問

日期：2026-09-22

**你的提問**

# Files mentioned by the user:

## codex-clipboard-07384008-5182-400d-87cc-2bf13f400ff5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-07384008-5182-400d-87cc-2bf13f400ff5.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

你現在看到的是**說明首頁**。請直接開啟 [Cyberduck CLI 安裝說明](https://docs.cyberduck.io/cli/#installation)。

1. 在 **Installation** 區塊選 **Windows**。
2. 找到 **MSI Installer**，點下方的 **Download**。
3. 下載 Windows 的 `.msi` 安裝檔並安裝。

安裝完成後，重新開啟 **PowerShell**，輸入：

```powershell
where.exe duck
```

把執行結果貼給我，我幫你確認腳本中的 `$Duck` 路徑要填什麼。

---

<a id="q16"></a>

## S197-Q016｜?

日期：2026-09-22

**你的提問**

## My request:
?

**當時回答**

你目前這頁是說明首頁，還不是下載位置。

請先點這個連結：**[開啟 Cyberduck CLI 安裝頁](https://docs.cyberduck.io/cli/#installation)**。

進去後選 **Windows**，再點 **MSI Installer** 下方的 **Download**。如果找不到，貼新畫面給我，我幫你指出位置。

---

<a id="q17"></a>

## S197-Q017｜圖片／附件提問

日期：2026-09-22

**你的提問**

# Files mentioned by the user:

## codex-clipboard-8da81089-dfa1-4836-9264-5091305f0c27.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8da81089-dfa1-4836-9264-5091305f0c27.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，這次是正確頁面，而且你已經選到 **Windows**。

請再**往下捲一點**，找 **Download／MSI Installer** 的下載連結，下載安裝檔。

畫面下方的 `choco install duck` 是另一種安裝方式，先不用輸入。若往下捲後不確定要點哪個，再貼畫面給我。

---

<a id="q18"></a>

## S197-Q018｜圖片／附件提問

日期：2026-09-22

**你的提問**

# Files mentioned by the user:

## codex-clipboard-5c1590ff-24b6-40d6-be5e-2185930e43b2.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-5c1590ff-24b6-40d6-be5e-2185930e43b2.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

請點黑色指令框下方，藍色的 **「installation options」**。

它就在這句話中：
> See other **installation options** to download the MSI installer for Windows.

進去後找 **Windows → MSI Installer → Download**。

---

<a id="q19"></a>

## S197-Q019｜圖片／附件提問

日期：2026-09-22

**你的提問**

# Files mentioned by the user:

## codex-clipboard-712e1e7a-5186-45ce-be0f-c63586826207.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-712e1e7a-5186-45ce-be0f-c63586826207.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，請點最上面第一個：

**`duck-9.5.4.45528-x64.msi`**

這是一般 Intel／AMD 64 位元 Windows 使用的安裝檔。下載後點兩下，依安裝精靈完成安裝。

裝好後，重新開啟 **PowerShell**，輸入：

```powershell
where.exe duck
```

把結果貼給我，就能確認下一步的程式路徑。

---

<a id="q20"></a>

## S197-Q020｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-2b9eb55b-b875-42f4-b728-9a6c8f416099.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2b9eb55b-b875-42f4-b728-9a6c8f416099.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q21"></a>

## S197-Q021｜bucket甚麼意思

日期：2026-09-23

**你的提問**

## My request:
bucket甚麼意思

**當時回答**

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

---

<a id="q22"></a>

## S197-Q022｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-414cf692-10dd-4877-b52f-b0b5ea8119c7.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-414cf692-10dd-4877-b52f-b0b5ea8119c7.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q23"></a>

## S197-Q023｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-cd94340a-e411-4a97-a9b7-9e476490fb17.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cd94340a-e411-4a97-a9b7-9e476490fb17.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q24"></a>

## S197-Q024｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-9187e0fa-8f08-4faf-8a36-14384e27c12c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9187e0fa-8f08-4faf-8a36-14384e27c12c.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這次是指令格式問題：你輸入了 `$Duck = ... --help`，請改成下面這整行，**開頭是 `&`，不是 `$Duck =`**：

```powershell
& 'C:\Program Files\Cyberduck CLI\duck.exe' --help
```

執行後，再輸入：

```powershell
dotnet --list-runtimes
```

把這兩段的結果貼給我。

---

<a id="q25"></a>

## S197-Q025｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a15fa391-ec6a-45c2-8027-326bc0513a86.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a15fa391-ec6a-45c2-8027-326bc0513a86.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q26"></a>

## S197-Q026｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-c33edbca-a7e3-49c9-9872-011380eafe8d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c33edbca-a7e3-49c9-9872-011380eafe8d.png

## codex-clipboard-40cee87b-d64b-4059-9a67-295baa614bc6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-40cee87b-d64b-4059-9a67-295baa614bc6.png





## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q27"></a>

## S197-Q027｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-ad8e6150-a612-4406-a497-0adc5c3aa618.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-ad8e6150-a612-4406-a497-0adc5c3aa618.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q28"></a>

## S197-Q028｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-8ed54dc2-a57e-49cf-b1a9-ccd3433b2ded.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8ed54dc2-a57e-49cf-b1a9-ccd3433b2ded.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q29"></a>

## S197-Q029｜給我英文Windows「程式和功能」

日期：2026-09-23

**你的提問**

## My request:
給我英文Windows「程式和功能」

**當時回答**

英文是 **Programs and Features**。

按 `Win + R`，輸入：

```text
appwiz.cpl
```

按 Enter，找到 **Cyberduck CLI**，點選 **Uninstall（解除安裝）**。

---

<a id="q30"></a>

## S197-Q030｜再來'

日期：2026-09-23

**你的提問**

## My request:
再來'

**當時回答**

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

---

<a id="q31"></a>

## S197-Q031｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-987f4f91-a71d-467c-b6ed-e47ece9659de.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-987f4f91-a71d-467c-b6ed-e47ece9659de.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q32"></a>

## S197-Q032｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-c8b28f05-ad75-4971-b075-1fe10b5a094f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c8b28f05-ad75-4971-b075-1fe10b5a094f.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個畫面保持預設即可，不用修改路徑。

按 **Next → Install → Finish**。

完成後，關閉原本的 PowerShell，再重新開啟，輸入：

```powershell
aws --version
```

把執行結果貼給我。

---

<a id="q33"></a>

## S197-Q033｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-936c391c-5144-4cc1-bfce-4d7a4e50d363.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-936c391c-5144-4cc1-bfce-4d7a4e50d363.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q34"></a>

## S197-Q034｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-c6532b87-76d9-493a-9761-602b456838e9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c6532b87-76d9-493a-9761-602b456838e9.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q35"></a>

## S197-Q035｜用意是甚麼

日期：2026-09-23

**你的提問**

## My request:
用意是甚麼

**當時回答**

剛才那兩段指令，是為了**確認要上傳哪些本機備份檔**：

- `$BackupFolder = 'D:\SQLBackup'`：告訴 PowerShell，你的 SQL 備份放在哪個資料夾。
- `Get-ChildItem ...`：列出該資料夾及子資料夾中的前 10 個 `.bak` 檔案，確認路徑正確、檔案找得到。

**目前只是查看，不會上傳、刪除或修改檔案。**

確認後，才接著設定「把備份檔複製到 S3」及「Windows 自動排程」。

另外，更換金鑰是因為上一張截圖露出了完整登入憑證，需要讓舊金鑰失效。

---

<a id="q36"></a>

## S197-Q036｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-95272f18-dda3-4ce7-ba6d-94587120a77f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-95272f18-dda3-4ce7-ba6d-94587120a77f.png





## My request:

**當時回答**

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

---

<a id="q37"></a>

## S197-Q037｜我需要在點進去嗎 還是在這層就好

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-f4f89833-4761-4164-a55f-81e64ba70d57.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f4f89833-4761-4164-a55f-81e64ba70d57.png





## My request:
我需要在點進去嗎 還是在這層就好

[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q38"></a>

## S197-Q038｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-252f5306-5573-4bf6-be7f-25aab18b284d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-252f5306-5573-4bf6-be7f-25aab18b284d.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q39"></a>

## S197-Q039｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e9bc5325-34d1-455f-b30e-a6766e6d69fa.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e9bc5325-34d1-455f-b30e-a6766e6d69fa.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q40"></a>

## S197-Q040｜這樣嗎

日期：2026-09-23

**你的提問**

## My request:
這樣嗎

**當時回答**

我這則沒有看到新的截圖，還不能確認結果。

如果你已執行上傳，等畫面回到 `PS C:\Users\Administrator>` 後，輸入：

```powershell
$LASTEXITCODE
```

顯示 **`0`** 就代表這次上傳成功；若不是 `0`，請貼錯誤畫面給我。

---

<a id="q41"></a>

## S197-Q041｜那如果檔名重複的話 會自動檔下嗎

日期：2026-09-23

**你的提問**

## My request:
那如果檔名重複的話 會自動檔下嗎

**當時回答**

如果你是問「同名檔案會不會自動跳過」，**會比較後決定，不是只看檔名**。

目前這個 `aws s3 sync` 指令的行為是：

| 情況 | 處理方式 |
|---|---|
| S3 同一路徑沒有這個檔案 | 上傳 |
| 同名、大小相同，且本機修改時間沒有比較新 | 跳過 |
| 同名，但大小不同，或本機修改時間比較新 | 重新上傳，更新 S3 的同名檔案 |

你的備份檔名包含日期與時間，所以**每天的新備份通常會另外新增一份**，不會蓋掉前一天的檔案。

另外，它不會逐一比對檔案內容；目前指令也沒有加 `--delete`，因此本機舊備份刪除後，S3 的那份仍會保留。[官方說明](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html)

---

<a id="q42"></a>

## S197-Q042｜那如果我想要的事比對呢

日期：2026-09-23

**你的提問**

## My request:
那如果我想要的事比對呢

**當時回答**

可以，要看你想比對到什麼程度：

| 比對方式 | 能確認什麼 |
|---|---|
| **檔名、大小、修改時間** | 哪些檔案需要上傳，目前 `sync` 就是這種 |
| **檔案內容的校驗碼（checksum）** | 確認本機與 S3 的檔案內容是否一致，即使同名、同大小也能檢查 |

你是想要 **「確認有沒有漏傳」**，還是 **「確認上傳後的備份內容完全一致」**？

如果是備份驗證，我建議做第二種；但檔案一致仍不代表 SQL 備份一定能還原，還原測試是另一項檢查。

---

<a id="q43"></a>

## S197-Q043｜確認有沒有漏傳 以及我的SSMS的會刪七天前的資料

日期：2026-09-23

**你的提問**

## My request:
確認有沒有漏傳 以及我的SSMS的會刪七天前的資料

**當時回答**

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

---

<a id="q44"></a>

## S197-Q044｜那先上傳看看

日期：2026-09-23

**你的提問**

## My request:
那先上傳看看

**當時回答**

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

---

<a id="q45"></a>

## S197-Q045｜沒問題了 那可以設定固定了 然後我想要用比對的方式

日期：2026-09-23

**你的提問**

## My request:
沒問題了 那可以設定固定了 然後我想要用比對的方式

**當時回答**

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

---

<a id="q46"></a>

## S197-Q046｜如果本機七天前刪除 我可以跟著一起刪除嗎

日期：2026-09-23

**你的提問**

## My request:
如果本機七天前刪除 我可以跟著一起刪除嗎

**當時回答**

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

---

<a id="q47"></a>

## S197-Q047｜那先不刪好了

日期：2026-09-23

**你的提問**

## My request:
那先不刪好了

**當時回答**

好，**維持不加 `--delete`**。

這樣排程會比對並補傳備份，本機七天前的檔案刪除後，**S3 的備份仍會保留**。

如果你已修改腳本，將 `--delete` 移除即可；`--dryrun` 也不要留在正式排程裡。

---

<a id="q48"></a>

## S197-Q048｜OK 在給我一次腳本

日期：2026-09-23

**你的提問**

## My request:
OK 在給我一次腳本

**當時回答**

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

---

<a id="q49"></a>

## S197-Q049｜這樣嗎

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-7166be7d-4e58-4296-9ab3-5c7f49e6b608.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7166be7d-4e58-4296-9ab3-5c7f49e6b608.png





## My request:
這樣嗎

[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q50"></a>

## S197-Q050｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-bb130d72-ed2f-46ea-8b4c-3914f05453e2.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-bb130d72-ed2f-46ea-8b4c-3914f05453e2.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q51"></a>

## S197-Q051｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-cfd827ec-5d9e-4b10-912f-0b6d41a7c93d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cfd827ec-5d9e-4b10-912f-0b6d41a7c93d.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q52"></a>

## S197-Q052｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-d13fe7ba-08a7-4b5c-b072-eea6858c0fbf.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d13fe7ba-08a7-4b5c-b072-eea6858c0fbf.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

請點畫面 **Apps 下方、有時鐘圖示的「Task Scheduler」**（工作排程器）。

開啟後，在右側點 **Create Task…（建立工作）**，就可以開始設定。

---

<a id="q53"></a>

## S197-Q053｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-c60559b0-95d8-4da6-9dbd-e62163b79f27.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c60559b0-95d8-4da6-9dbd-e62163b79f27.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q54"></a>

## S197-Q054｜再來

日期：2026-09-23

**你的提問**

## My request:
再來

**當時回答**

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

---

<a id="q55"></a>

## S197-Q055｜我剛剛設定到一半 我好像關掉 我不太確定

日期：2026-09-23

**你的提問**

## My request:
我剛剛設定到一半 我好像關掉 我不太確定

**當時回答**

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

---

<a id="q56"></a>

## S197-Q056｜圖片／附件提問

日期：2026-09-23

**你的提問**

# Files mentioned by the user:

## codex-clipboard-14a8ea93-faa9-4a0d-b1c9-c4526e09e54c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-14a8ea93-faa9-4a0d-b1c9-c4526e09e54c.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

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

---

<a id="q57"></a>

## S197-Q057｜對

日期：2026-09-23

**你的提問**

## My request:
對

**當時回答**

好，那就維持 **每天凌晨 00:00** 自動比對並補傳「每日備份」，**不刪除 S3 舊檔**。

最後確認今天 `.log` 的最新紀錄有 **`END ExitCode=0`**，以及失敗重試已設定完成即可。

---

