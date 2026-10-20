# FortiClient 驅動安裝失敗

來源編號：S180  
聊天 ID：`01a0acea-11cc-7443-8cfe-7af80ec7131c`  
聊天起始日期（台北）：2026-09-17  
保存位置：一般本機紀錄  
問答段落：4

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S180-Q001｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-7344bae1-b4e0-4219-adbc-b4bb6bf10c3f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7344bae1-b4e0-4219-adbc-b4bb6bf10c3f.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

The FortiClient VPN installer failed before completion; that screen doesn’t include the underlying error.

Try, in order:

1. Restart Windows.
2. Uninstall any existing FortiClient / FortiClient VPN entry in **Settings → Apps**.
3. Download a fresh installer from your organization’s VPN portal or Fortinet—ensure it matches your Windows architecture.
4. Right-click the installer → **Run as administrator**.
5. Temporarily disable third-party antivirus/endpoint protection if your IT policy allows it.

If it still fails, check the installer logs in `%TEMP%` and send the newest FortiClient/MSI log filename or its last ~30 lines. If this is a work-managed PC, IT may need to remove an older managed FortiClient component or deploy their approved package.

---

<a id="q2"></a>

## S180-Q002｜給我中文

日期：2026-09-17

**你的提問**

給我中文

**當時回答**

FortiClient VPN 安裝程式提前結束了，但畫面沒有顯示真正的錯誤原因。

請依序嘗試：

1. 重新啟動 Windows。
2. 到「設定 → 應用程式」移除舊的 **FortiClient／FortiClient VPN**。
3. 從公司 VPN 入口或 Fortinet 重新下載安裝檔，確認版本與 Windows 架構相符。
4. 對安裝檔按右鍵，選「以系統管理員身分執行」。
5. 若公司政策允許，暫時停用第三方防毒或端點防護軟體後重試。

還是失敗的話，請到 `%TEMP%` 查看最新的 FortiClient 或 MSI 安裝紀錄，貼出檔名或最後約 30 行內容。我可以幫你判讀原因。公司管理的電腦則可能需要請 IT 移除舊版元件，或提供公司核准的安裝套件。

---

<a id="q3"></a>

## S180-Q003｜D

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-3e8c1716-2313-44c2-8f46-985d4cda2a76.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3e8c1716-2313-44c2-8f46-985d4cda2a76.png



## My request:
D

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這份紀錄已經確認關鍵錯誤：

- `1603`：Windows Installer 致命安裝失敗
- `0x80070643`：安裝期間發生嚴重錯誤
- 失敗點在 FortiClient 的「手動驅動程式安裝」階段（`DoManuallyDriveInstallation`）

通常是舊版 FortiClient 的 VPN/網路驅動程式殘留，或 Windows 阻擋驅動程式安裝。

建議這樣處理：

1. 先重新開機。
2. 到「設定 → 應用程式」解除安裝所有 FortiClient / Fortinet 相關項目。
3. 開啟「裝置管理員 → 檢視 → 顯示隱藏的裝置」。
4. 在「網路介面卡」中移除名稱包含 **Fortinet、FortiClient、SSL VPN、Virtual Ethernet** 的殘留介面卡；勾選「刪除此裝置的驅動程式」若有該選項。
5. 再重新開機，使用公司提供的 FortiClient 安裝檔，以右鍵「以系統管理員身分執行」重新安裝。

若仍失敗，請把紀錄中第一個出現 `Return value 3` 附近的約 30–50 行貼出來；那一段通常會顯示是哪個實際驅動或元件被 Windows 擋住。

---

<a id="q4"></a>

## S180-Q004｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-72e74ba0-ec82-4fdb-a555-e61bf971b7b1.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-72e74ba0-ec82-4fdb-a555-e61bf971b7b1.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

原因已更明確：FortiClient 在安裝核心網路篩選驅動程式 **`FortiFilter.inf`** 時失敗：

```text
install_driver_inf_newdev ... FortiFilter.inf
MSI_InstallDrivers(), retval=1603
Return value 3
```

這不是單純安裝檔損壞，而是 Windows 拒絕或無法安裝 FortiClient 的網路驅動程式。最常見是舊版 Fortinet 驅動殘留、其他 VPN/防毒網路過濾器衝突，或「記憶體完整性」封鎖。

請依此順序處理：

1. 移除舊版 FortiClient，重新啟動。
2. 暫時解除安裝其他 VPN（如 Cisco、GlobalProtect、AnyConnect、OpenVPN 等）或防毒的網路防護元件後再試。
3. 到「Windows 安全性 → 裝置安全性 → 核心隔離」檢查「記憶體完整性」。  
   若已開啟，可暫時關閉、重新開機、安裝 FortiClient；安裝完成後再開啟。若公司電腦受政策管理，請先詢問 IT。
4. 使用公司指定、較新版的 FortiClient VPN 安裝檔，以系統管理員身分執行。

若仍失敗，請開啟：

```text
C:\Windows\INF\setupapi.dev.log
```

搜尋 `FortiFilter.inf`，貼出其附近的錯誤行；這份 Windows 驅動安裝紀錄會直接指出是簽章、存取權限、相容性還是既有驅動衝突。

---

