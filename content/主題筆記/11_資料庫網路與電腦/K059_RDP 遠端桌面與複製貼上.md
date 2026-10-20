---
title: "RDP 遠端桌面與複製貼上"
category: "11_資料庫網路與電腦"
updated: 2026-10-20
sources: ["S018","S051","S183"]
---

# RDP 遠端桌面與複製貼上

[回總覽](../../00_閱讀入口.md)

## 整合摘要

整理遠端剪貼簿、0x204 與 RDP 設定。原聊天包含逐步排查與設定，保留具體錯誤；遠端主機設定與目前電腦問題需分開。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S018｜排查遠端複製貼上問題](<../../原始問答/S018_排查遠端複製貼上問題.md>)：2026-07-23，1 段問答。
- [S051｜排查遠端桌面錯誤 0x204](<../../原始問答/S051_排查遠端桌面錯誤 0x204.md>)：2026-08-06，5 段問答。
- [S183｜設定 RDP 遠端連線](<../../原始問答/S183_設定 RDP 遠端連線.md>)：2026-09-17，19 段問答。

## 問題索引

- [01｜我連進去遠端 是不是就沒辦法用複製貼上的方式](#qa1)
- [02｜[Window Title] 遠端桌面連線 [Content] 遠端桌面無法連線到遠端電腦，造成此問題的原因可能是下列之一: 1) 未啟用對伺服器的遠端存取 2) 遠端電腦已關機 …](#qa2)
- [03｜給我CMD的語法](#qa3)
- [04｜PING 不到](#qa4)
- [05｜172.16.100.9](#qa5)
- [06｜圖片／附件提問](#qa6)
- [07｜我如果想讓別人連rdp進來我的電腦 我該如何設定](#qa7)
- [08｜我要怎麼建立](#qa8)
- [09｜那我的rdp 我給她甚麼連線資訊嗎](#qa9)
- [10｜BIOS 設定方式 - 開機後按 Delete or F2 進入 BIOS模式 - 進入 Boot - 找到 Wait For F1 if Error - 設定為 Disable …](#qa10)
- [11｜我現在是THINKCENTRE](#qa11)
- [12｜是桌電喔](#qa12)
- [13｜11DASOE300](#qa13)
- [14｜是按F1](#qa14)
- [15｜圖片／附件提問](#qa15)
- [16｜圖片／附件提問](#qa16)
- [17｜圖片／附件提問](#qa17)
- [18｜圖片／附件提問](#qa18)
- [19｜圖片／附件提問](#qa19)
- [20｜圖片／附件提問](#qa20)
- [21｜圖片／附件提問](#qa21)
- [22｜圖片／附件提問](#qa22)
- [23｜圖片／附件提問](#qa23)
- [24｜圖片／附件提問](#qa24)
- [25｜我還有一個是 WINDOWS的電源計畫](#qa25)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 我連進去遠端 是不是就沒辦法用複製貼上的方式

來源：[S018-Q001](<../../原始問答/S018_排查遠端複製貼上問題.md#q1>)；2026-07-23

**問題**

我連進去遠端 是不是就沒辦法用複製貼上的方式

**歷史回答**

不一定，通常遠端連線仍可複製貼上，取決於連線方式：

- **SSH 終端機**：通常可複製文字；Windows Terminal 常用 `Ctrl+Shift+C / Ctrl+Shift+V`，或右鍵貼上。
- **Windows 遠端桌面（RDP）**：可共享剪貼簿；若失效，要確認「本機資源 → 剪貼簿」已勾選。
- **瀏覽器內的遠端主控台／虛擬機 Console**：可能限制快捷鍵，有時要用頁面上的 Clipboard 功能。
- **公司管控環境**：管理員可能基於資安政策停用剪貼簿或檔案傳輸。

如果你告訴我是用 **SSH、RDP、AnyDesk、TeamViewer，還是網站上的 Console**，我可以直接告訴你該怎麼貼上。

<a id="qa2"></a>

### 2. [Window Title] 遠端桌面連線 [Content] 遠端桌面無法連線到遠端電腦，造成此問題的原因可能是下列之一: 1) 未啟用對伺服器的遠端存取 2) 遠端電腦已關機 …

來源：[S051-Q001](<../../原始問答/S051_排查遠端桌面錯誤 0x204.md#q1>)；2026-08-06

**問題**

[Window Title]
遠端桌面連線

[Content]
遠端桌面無法連線到遠端電腦，造成此問題的原因可能是下列之一:

1) 未啟用對伺服器的遠端存取
2) 遠端電腦已關機
3) 遠端電腦未連線到網路

請確定遠端電腦已開啟、已連接到網路，而且已經啟用遠端存取。

[^] 隱藏詳細資料(D)  [確定]

[Expanded Information]
錯誤碼： 0x204
延伸的錯誤碼： 0x0
時間戳記 (UTC)： 08/06/26 01:09:12 AM

按Ctrl+C以複製。

**歷史回答**

錯誤碼 `0x204` 表示遠端桌面用戶端無法連到目標電腦，通常是網路、連接埠或遠端桌面服務未開啟；不是帳號密碼錯誤。

請依序檢查：

1. 在本機 PowerShell 測試連線：

```powershell
Test-NetConnection 遠端電腦IP -Port 3389
```

- `TcpTestSucceeded : True`：網路與 RDP 連接埠正常，接著檢查帳號、權限或憑證。
- `False`：目標未開機、IP 不對、防火牆阻擋、RDP 未啟用，或需要先連 VPN。

2. 確認使用正確位址：

- 同一內網：使用遠端電腦的內網 IP，例如 `192.168.x.x`
- 公司環境：先連 VPN
- 跨網際網路：需透過 VPN、RD Gateway，或正確設定連接埠轉送；不建議直接將 3389 暴露到網際網路

3. 在遠端電腦現場確認：

- Windows「設定 → 系統 → 遠端桌面」已啟用
- 電腦未睡眠或關機
- 網路連線正常
- Windows 防火牆允許「遠端桌面」
- `Remote Desktop Services` 服務正在執行
- Windows 版本是 Pro／Enterprise／Server；Windows Home 不能作為內建 RDP 主機

4. 若使用電腦名稱失敗，改用 IP 位址連線。若 IP 可以、名稱不行，就是 DNS／名稱解析問題。

錯誤記錄時間為 UTC `2026-08-06 01:09:12`，台北時間是 `2026-08-06 09:09:12`。最關鍵的下一步是執行上面的 `Test-NetConnection`；它能快速判斷問題在網路/連接埠，還是遠端桌面登入層。

<a id="qa3"></a>

### 3. 給我CMD的語法

來源：[S051-Q002](<../../原始問答/S051_排查遠端桌面錯誤 0x204.md#q2>)；2026-08-06

**問題**

給我CMD的語法

**歷史回答**

在 CMD 輸入以下指令，把 `遠端IP` 換成實際位址：

```cmd
ping 遠端IP
```

測試遠端桌面的 `3389` 連接埠：

```cmd
powershell -Command "Test-NetConnection '遠端IP' -Port 3389"
```

例如：

```cmd
powershell -Command "Test-NetConnection '192.168.1.100' -Port 3389"
```

查看結果：

- `TcpTestSucceeded : True`：3389 可連線
- `TcpTestSucceeded : False`：網路、防火牆、VPN 或遠端桌面服務有問題

如果使用電腦名稱連線，也可檢查名稱解析：

```cmd
nslookup 遠端電腦名稱
```

直接啟動遠端桌面：

```cmd
mstsc /v:192.168.1.100
```

若使用非預設連接埠：

```cmd
mstsc /v:192.168.1.100:連接埠
```

<a id="qa4"></a>

### 4. PING 不到

來源：[S051-Q003](<../../原始問答/S051_排查遠端桌面錯誤 0x204.md#q3>)；2026-08-06

**問題**

PING 不到

**歷史回答**

`ping` 不到不一定代表遠端桌面無法連線，因為遠端電腦可能只封鎖 ICMP。請直接測試 RDP 的 3389 連接埠：

```cmd
powershell -Command "Test-NetConnection '遠端IP' -Port 3389"
```

判讀結果：

- `TcpTestSucceeded : True`：忽略 Ping，直接連線：

```cmd
mstsc /v:遠端IP
```

- `TcpTestSucceeded : False`：請依序檢查：
  1. 是否連上公司 VPN
  2. 遠端 IP 是否正確
  3. 遠端電腦是否開機、未休眠
  4. 是否在同一網路或有可達的路由
  5. 遠端電腦是否啟用「遠端桌面」
  6. 防火牆是否允許 TCP 3389

也可查看封包在哪一段中斷：

```cmd
tracert 遠端IP
```

如果遠端 IP 是 `192.168.x.x`、`10.x.x.x` 或 `172.16.x.x–172.31.x.x`，它是內網位址；人在外部網路時通常必須先連 VPN，不能直接連線。

<a id="qa5"></a>

### 5. 172.16.100.9

來源：[S051-Q004](<../../原始問答/S051_排查遠端桌面錯誤 0x204.md#q4>)；2026-08-06

**問題**

172.16.100.9

**歷史回答**

`172.16.100.9` 是私人內網 IP，無法直接從網際網路連線。你必須與遠端電腦位於同一內網，或先連上該公司的 VPN。

在 CMD 依序執行：

```cmd
ipconfig
```

確認自己的 IPv4 是否也在 `172.16.x.x` 網段，並確認有 VPN 網路介面。接著測試路由：

```cmd
tracert 172.16.100.9
```

再直接測試 RDP 連接埠：

```cmd
powershell -Command "Test-NetConnection '172.16.100.9' -Port 3389"
```

若顯示：

```text
TcpTestSucceeded : True
```

即可連線：

```cmd
mstsc /v:172.16.100.9
```

若是 `False`，最可能是：

- 尚未連公司 VPN
- VPN 沒有通往 `172.16.100.0` 網段的路由
- 遠端電腦關機或休眠
- 遠端桌面或 TCP 3389 被防火牆封鎖

可用以下指令確認是否存在該網段路由：

```cmd
route print 172.16.100.9
```

如果完全沒有對應路由，請先連 VPN，或請網管設定通往 `172.16.100.0/24` 的路由。

<a id="qa6"></a>

### 6. 圖片／附件提問

來源：[S051-Q005](<../../原始問答/S051_排查遠端桌面錯誤 0x204.md#q5>)；2026-08-06

**問題**

# Files mentioned by the user:

## codex-clipboard-c917e854-2bf9-4966-97d8-6abce4a38631.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c917e854-2bf9-4966-97d8-6abce4a38631.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

從畫面可判斷：目前封包走到中華電信（`59.124.228.254`），代表 `172.16.100.9` 被送往一般網際網路，而不是遠端公司的內部網路。私人 IP 在公網上無法路由，所以 RDP 會出現 `0x204`。

請先連上公司／目標端 VPN，再執行：

```cmd
tracert 172.16.100.9
```

正常情況下不應再出現 `hinet-ip.hinet.net`。接著測試：

```cmd
powershell -Command "Test-NetConnection '172.16.100.9' -Port 3389"
```

也可查看目前 Windows 如何選路：

```cmd
route print 172.16.100.9
```

如果已連 VPN，但仍然走 HiNet，表示 VPN 沒有下發前往 `172.16.100.0/24` 的路由，需請 VPN／網管人員加入：

```text
172.16.100.0/24 → VPN 通道
```

不要隨意執行 `route add`，因為必須先知道正確的 VPN 閘道或介面編號。你目前的問題重點不是遠端桌面設定，而是缺少通往目標內網的 VPN 路由。

<a id="qa7"></a>

### 7. 我如果想讓別人連rdp進來我的電腦 我該如何設定

來源：[S183-Q001](<../../原始問答/S183_設定 RDP 遠端連線.md#q1>)；2026-09-17

**問題**

我如果想讓別人連rdp進來我的電腦 我該如何設定

**歷史回答**

可以。**先確認你的 Windows 版本，以及對方是否跟你在同一個網路**，設定方式會不同。

1. **確認 Windows 版本**  
   到「設定 → 系統 → 關於」查看版本。你的電腦需要 **Pro（專業版）、Enterprise 或 Education**；Home（家用版）不能接受內建 RDP 連線。[Microsoft 說明](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/remotepc/remote-desktop-allow-access)

2. **開啟遠端桌面**  
   到「設定 → 系統 → 遠端桌面」，開啟「遠端桌面」，保留「網路層級驗證（NLA）」。

3. **設定對方登入的帳號**  
   建議建立一個有密碼的專用 Windows 帳號，再到「遠端桌面使用者」加入該帳號。對方需要這個帳號的**密碼，不是你平常解鎖用的 PIN**。

4. **讓電腦保持開機、連網且不休眠**  
   螢幕可以關閉，但電腦休眠時通常無法連線。以上必要條件可參考 [Microsoft 設定指南](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/remotepc/remote-desktop-allow-access)。

接著依連線位置操作：

| 對方的位置 | 連線方式 |
|---|---|
| 同一個 Wi-Fi／區域網路 | 你的電腦開啟命令提示字元，輸入 `ipconfig`，找目前網路的 IPv4 位址，例如 `192.168.1.100`。對方在 Windows 按 `Win + R`，輸入 `mstsc`，再填入你的 IP 與帳號密碼。 |
| 外面的網路 | 先透過 VPN 連入你所在的網路，再使用 RDP。避免直接把路由器的 **3389 埠**開放到網際網路。 |

你目前是 **Windows 10 還是 11、家用版還是專業版**？對方是要從**同一個網路，還是外面連進來**？

<a id="qa8"></a>

### 8. 我要怎麼建立

來源：[S183-Q002](<../../原始問答/S183_設定 RDP 遠端連線.md#q2>)；2026-09-17

**問題**

我要怎麼建立

**歷史回答**

如果你是指建立「給對方 RDP 登入的帳號」，可以這樣做：

1. 開啟「**設定 → 帳戶 → 其他使用者**」（Windows 10 可能叫「家庭與其他使用者」）。
2. 點「**新增帳戶**」。
3. 選「**我沒有此人的登入資訊**」→「**新增沒有 Microsoft 帳戶的使用者**」。
4. 輸入帳號名稱，例如 `rdpuser`，設定一組密碼並完成安全性問題。帳戶保留「**標準使用者**」即可。
5. 到「**設定 → 系統 → 遠端桌面 → 遠端桌面使用者**」。
6. 點「**新增**」，輸入 `rdpuser` →「**檢查名稱**」→「確定」。

對方連線時，輸入：

- **使用者名稱：** `你的電腦名稱\rdpuser`
- **密碼：** 剛才建立的密碼

電腦名稱可以在「**設定 → 系統 → 關於**」的「裝置名稱」找到。

<a id="qa9"></a>

### 9. 那我的rdp 我給她甚麼連線資訊嗎

來源：[S183-Q003](<../../原始問答/S183_設定 RDP 遠端連線.md#q3>)；2026-09-17

**問題**

那我的rdp 我給她甚麼連線資訊嗎

**歷史回答**

對，你需要給她這 **3 項資訊**：

| 資訊 | 範例 |
|---|---|
| 電腦 IP 位址 | `192.168.1.100` |
| 使用者名稱 | `你的電腦名稱\rdpuser` |
| 密碼 | 你替 `rdpuser` 設定的密碼，不是 PIN |

**如果她跟你在同一個 Wi-Fi／區域網路：**

你按 `Win + R` → 輸入 `cmd` → 輸入 `ipconfig`，找到目前使用的 Wi-Fi 或乙太網路下的 **IPv4 位址**，給她即可。

她按 `Win + R` → 輸入 `mstsc` → 在「電腦」填入你的 IP → 連線 → 輸入帳號密碼。

**如果她從外面連進來：**

`192.168.x.x` 這種內網 IP 無法直接從外面連線，需要先設定 VPN，讓她連入，再使用 RDP。

她現在是跟你**同一個網路**，還是在**其他地方**？

<a id="qa10"></a>

### 10. BIOS 設定方式 - 開機後按 Delete or F2 進入 BIOS模式 - 進入 Boot - 找到 Wait For F1 if Error - 設定為 Disable …

來源：[S183-Q004](<../../原始問答/S183_設定 RDP 遠端連線.md#q4>)；2026-09-17

**問題**

BIOS 設定方式

- 開機後按 Delete or F2 進入 BIOS模式
- 進入 Boot
  - 找到 Wait For F1 if Error 
    - 設定為 Disable
  - 找到 CSM (Compatibility Support Module)  
    - 設定為 Disable
  - 如果有 Fast Boot 
    - 設定成 Disable
- 進入 Advanced
  - 進入APM 
    - 找到 Restore AC Power Loss 
      - 設定為 Last Status
  - 進入System Agent Configuration \ Graphic Configuration 
    - 找到 Primary Dispaly 
      - 設定為 iGP
  - 我想設定這個

**歷史回答**

可以，這些要在**重新啟動後進入 BIOS** 設定，無法透過一般 RDP 操作；重新啟動進 BIOS 後，遠端連線會中斷，需要有人在電腦旁操作。

你貼的選單名稱看起來像 ASUS 主機板，但要先確認型號，才能給你正確路徑，也要確認 CPU 有內建顯示核心，才能設定 `iGP`。

請先在 Windows：

1. 按 `Win + R`。
2. 輸入 `msinfo32`，按 Enter。
3. 把以下資訊貼給我，或截圖：
   - **系統製造廠商／系統型號**
   - **主機板製造廠商／主機板產品**
   - **處理器**
   - **BIOS 模式**

先不要直接關閉 CSM；如果目前系統使用 Legacy 模式開機，關閉後可能無法進入 Windows。確認上述資訊後，我再依你的主機板帶你逐項設定。

<a id="qa11"></a>

### 11. 我現在是THINKCENTRE

來源：[S183-Q005](<../../原始問答/S183_設定 RDP 遠端連線.md#q5>)；2026-09-17

**問題**

我現在是THINKCENTRE

**歷史回答**

ThinkCentre 是 Lenovo 的電腦，你貼的 BIOS 選單不一定適用，實際位置要看型號。

你可以先這樣進 BIOS：

1. 重新啟動電腦。
2. 出現 Lenovo 標誌時，連續按 **F1**；若無法進入，試試先按 **Enter**，再選 **F1 Setup**。

請提供 **完整型號**，例如 ThinkCentre M720q、M920q、M70q Gen 3。可以在機身貼紙或 `msinfo32` 的「系統型號」找到，不需要提供序號。

如果已經進入 BIOS，直接拍主畫面給我，我就能依照你的選單帶你設定。**先別改 CSM 或內顯選項**，需要先確認開機模式與硬體是否支援。

<a id="qa12"></a>

### 12. 是桌電喔

來源：[S183-Q006](<../../原始問答/S183_設定 RDP 遠端連線.md#q6>)；2026-09-17

**問題**

是桌電喔

**歷史回答**

了解，是 **Lenovo ThinkCentre 桌上型電腦**。ThinkCentre 有多種桌機型號，BIOS 選單也會不同。

請按 **`Win + R` → 輸入 `msinfo32` → Enter**，把「**系統型號**」那一行貼給我，我就能依你的型號告訴你怎麼設定。

如果你已經進入 BIOS，也可以直接拍目前的畫面給我。

<a id="qa13"></a>

### 13. 11DASOE300

來源：[S183-Q007](<../../原始問答/S183_設定 RDP 遠端連線.md#q7>)；2026-09-17

**問題**

11DASOE300

**歷史回答**

查到了，**11DA 對應 Lenovo ThinkCentre M70t 桌上型電腦**。[Lenovo 官方型號頁](https://pcsupport.lenovo.com/us/en/products/desktops-and-all-in-ones/thinkcentre-m-series-desktops/thinkcentre-m70t/11da)

你貼的設定是其他品牌的選單名稱，不能完全照搬。先設定「斷電後恢復狀態」：

1. 重新啟動，出現 Lenovo 標誌時連按 **F1** 進 BIOS。
2. 找到 **Power → After Power Loss**。
3. 選擇 **Last State**，相當於你清單中的 `Last Status`：
   - 斷電前開機 → 復電後開機。
   - 斷電前關機 → 復電後保持關機。
4. 全部設定確認後，按 **F10** 儲存離開。[Lenovo 電源設定說明](https://docs.lenovocdrt.com/ref/bios/settings/thinkcentre/power/)

至於 **F1 錯誤等待、CSM、Fast Boot、內顯優先**，需要依你目前 BIOS 畫面確認，部分選項可能沒有提供。尤其 CSM 要先確認 Windows 使用 UEFI 開機；內顯優先則要確認 CPU 支援內顯。

**你先進 BIOS，拍一張主畫面給我**，我就可以接著帶你找其餘選項。

<a id="qa14"></a>

### 14. 是按F1

來源：[S183-Q008](<../../原始問答/S183_設定 RDP 遠端連線.md#q8>)；2026-09-17

**問題**

是按F1

**歷史回答**

對，這台是按 **F1** 進 BIOS。重新啟動，出現 **Lenovo 標誌時連續按 F1**。

進去後拍一下 BIOS 畫面給我，我帶你逐項設定。

<a id="qa15"></a>

### 15. 圖片／附件提問

來源：[S183-Q009](<../../原始問答/S183_設定 RDP 遠端連線.md#q9>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-3a0e877e-eb56-4cb5-8cba-2f761ef558ff.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3a0e877e-eb56-4cb5-8cba-2f761ef558ff.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你已經成功進入 BIOS，畫面也確認是 **ThinkCentre M70t**。

先設定斷電後恢復狀態：

1. 選左側的 **Power**。
2. 找 **After Power Loss**。
3. 如果有，將它設為 **Last State**，就是你原本清單的 `Last Status`。

**先不用按 F10**，等其他項目確認完再一起儲存。請拍一下 **Power 頁面**，我帶你確認。

<a id="qa16"></a>

### 16. 圖片／附件提問

來源：[S183-Q010](<../../原始問答/S183_設定 RDP 遠端連線.md#q10>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-85bca9d2-6f47-4726-a2a7-44871845630d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-85bca9d2-6f47-4726-a2a7-44871845630d.png

## codex-clipboard-32f8cb12-21d6-4b6b-a172-204ff9a62cd7.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-32f8cb12-21d6-4b6b-a172-204ff9a62cd7.png



## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**歷史回答**

畫面中的 **After Power Loss 已經是 `Last State`**，符合你的設定需求，這項不用再改。

接著請點左側 **Startup**，拍一下裡面的選項，我們確認 **CSM、Fast Boot 和錯誤時等待 F1** 的設定。

先不用按 F10，等全部確認後再一起儲存。

<a id="qa17"></a>

### 17. 圖片／附件提問

來源：[S183-Q011](<../../原始問答/S183_設定 RDP 遠端連線.md#q11>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-48accb53-7e2c-4880-ae5b-5c88b997acf5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-48accb53-7e2c-4880-ae5b-5c88b997acf5.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張是 **Startup 頁面上半部**，右側還可以往下捲。

請**往下捲到最底，再拍一張**，確認下面有哪些設定。

目前看到的 **Primary／Automatic／Error Boot Sequence** 都是開機順序，先不用修改；其中 **Error Boot Sequence 並不是「錯誤時等待 F1」的開關**。

<a id="qa18"></a>

### 18. 圖片／附件提問

來源：[S183-Q012](<../../原始問答/S183_設定 RDP 遠端連線.md#q12>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-8bd76384-733b-4850-ac7f-1eef44842982.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8bd76384-733b-4850-ac7f-1eef44842982.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**Fast Boot 已經是 `Disabled`，符合你的需求。**

下方的 **Option Keys Display** 只是控制開機時是否顯示「按 F1」等提示，**不是錯誤時等待 F1 的設定**，保持原樣即可。

目前已確認：

- **After Power Loss：Last State** ✓
- **Fast Boot：Disabled** ✓

這個 Startup 頁面沒有看到 CSM 或錯誤等待選項。下一步請點左側 **Devices**，拍一下畫面，我們找內顯優先設定。

<a id="qa19"></a>

### 19. 圖片／附件提問

來源：[S183-Q013](<../../原始問答/S183_設定 RDP 遠端連線.md#q13>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-a5ffcdc1-fb34-44df-b9ea-d9dabd49d1c0.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a5ffcdc1-fb34-44df-b9ea-d9dabd49d1c0.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

找到顯示設定了。請選畫面中間的 **Video Setup**，按 **Enter** 進入，再拍一下裡面的選項，我們確認內顯優先要怎麼設。

<a id="qa20"></a>

### 20. 圖片／附件提問

來源：[S183-Q014](<../../原始問答/S183_設定 RDP 遠端連線.md#q14>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-f1e3dcb1-c529-4a8a-a92d-1ef1e61252f4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f1e3dcb1-c529-4a8a-a92d-1ef1e61252f4.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這裡請選 **`IGD`**，就是你清單中 `iGP` 的對應選項，代表使用內建顯示晶片。

畫面也註明：CPU 不支援內顯就不會出現 IGD；你這台有提供此選項。

**若螢幕線目前插在獨立顯示卡上，儲存重開後可能需要改接主機板的顯示輸出孔。**

選好後，按左上角返回，再進左側 **Advanced** 拍一下畫面，我們繼續確認剩下的設定。先不用按 F10。

<a id="qa21"></a>

### 21. 圖片／附件提問

來源：[S183-Q015](<../../原始問答/S183_設定 RDP 遠端連線.md#q15>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-b24b9a10-8e39-41c7-85cb-38ff0420fc4b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b24b9a10-8e39-41c7-85cb-38ff0420fc4b.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，**Select Active Video 已選為 `IGD`**，符合內顯優先的需求，等最後儲存才會正式套用。

下面兩項保持目前數值即可：

- **Pre-Allocated Memory Size：32MB**
- **Total Graphics Memory：Maximum**

接著按左上角 **←** 返回，進入 **Advanced**，拍一下畫面，我們確認剩下的 CSM 和錯誤等待 F1 選項。

<a id="qa22"></a>

### 22. 圖片／附件提問

來源：[S183-Q016](<../../原始問答/S183_設定 RDP 遠端連線.md#q16>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-1c47b527-d964-40cc-b2c0-e7058e0fce56.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1c47b527-d964-40cc-b2c0-e7058e0fce56.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張仍是 **Devices 頁面**，你已經從 Video Setup 返回了。

接著請點**左側選單的 Advanced**（在 Devices 下方、Power 上方），再拍一張畫面給我。

<a id="qa23"></a>

### 23. 圖片／附件提問

來源：[S183-Q017](<../../原始問答/S183_設定 RDP 遠端連線.md#q17>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-fcde6348-3cf8-44b3-b52c-001d710bc089.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-fcde6348-3cf8-44b3-b52c-001d710bc089.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這頁 **Advanced 沒有看到 CSM 或等待 F1 的選項**，目前這些項目不用改。

目前確認的設定是：

- **After Power Loss：Last State**
- **Fast Boot：Disabled**
- **Select Active Video：IGD**（已選，尚待儲存）

剩下 **CSM、Wait for F1 if Error 尚未確認**，不能直接當作已關閉。

請再點左側 **Security**，拍一下選單；我們確認是否有開機錯誤相關設定。

<a id="qa24"></a>

### 24. 圖片／附件提問

來源：[S183-Q018](<../../原始問答/S183_設定 RDP 遠端連線.md#q18>)；2026-09-17

**問題**

# Files mentioned by the user:

## codex-clipboard-57edb9e3-5eaf-4bc6-82f2-1153ba4ad69d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-57edb9e3-5eaf-4bc6-82f2-1153ba4ad69d.png

## codex-clipboard-d2da1365-290e-4fc1-a8d1-5025155c69fc.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d2da1365-290e-4fc1-a8d1-5025155c69fc.png

## codex-clipboard-6b521b65-6cc1-4e8d-976e-1cd6a3ef4f53.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6b521b65-6cc1-4e8d-976e-1cd6a3ef4f53.png

## codex-clipboard-9e24725d-a6a3-4fb0-85dc-dc9e13c3b5e6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9e24725d-a6a3-4fb0-85dc-dc9e13c3b5e6.png

## codex-clipboard-e283e972-024a-4fb6-9502-03ab4bbad7e2.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e283e972-024a-4fb6-9502-03ab4bbad7e2.png



## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這幾張 **Security 頁面沒有看到通用的「Wait for F1 if Error」開關**。`Password Count Exceeded Error` 只針對密碼錯誤，不是同一項，保持原樣。

目前進度：

| 原本要設定的項目 | 目前狀態 |
|---|---|
| 斷電恢復狀態 | **Last State** ✓ |
| Fast Boot | **Disabled** ✓ |
| 內顯優先 | **IGD**，待儲存 ✓ |
| Wait for F1 if Error | 已查看的頁面未找到 |
| CSM | 已查看的頁面未找到，尚未確認狀態 |

**現在可以按 F10 → Yes，儲存並重新啟動**，先確認能正常進 Windows。

進入 Windows 後，按 `Win + R` → 輸入 `msinfo32`，告訴我「**BIOS 模式**」顯示什麼，我們再確認目前的開機模式。若開機停在要求按 F1 的錯誤畫面，拍下錯誤內容，就能針對原因處理。

<a id="qa25"></a>

### 25. 我還有一個是 WINDOWS的電源計畫

來源：[S183-Q019](<../../原始問答/S183_設定 RDP 遠端連線.md#q19>)；2026-09-17

**問題**

我還有一個是 WINDOWS的電源計畫

**歷史回答**

可以，先按 **F10 → Yes** 儲存 BIOS，進入 Windows 後再設定。

如果你是要讓電腦保持可供 **RDP 遠端連線**：

1. 按 **`Win + R`**，輸入 `powercfg.cpl`，按 Enter。
2. 在目前使用的電源計畫旁，點「**變更計畫設定**」。
3. 將「**讓電腦睡眠**」設為「**永不**」。
4. 「**關閉顯示器**」可自行設定，例如 10 分鐘；螢幕關閉不影響 RDP。
5. 點「**儲存變更**」。

**不需要為了 RDP 特別切成高效能，使用「平衡」即可。**

如果你有指定的電源計畫設定清單，或已開啟設定畫面，貼給我，我帶你逐項確認。

