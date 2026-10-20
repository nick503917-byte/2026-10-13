# 設定 RDP 遠端連線

來源編號：S183  
聊天 ID：`01a0adfd-8f09-74f3-9c8d-c10b92d7f74b`  
聊天起始日期（台北）：2026-09-17  
保存位置：一般本機紀錄  
問答段落：19

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S183-Q001｜我如果想讓別人連rdp進來我的電腦 我該如何設定

日期：2026-09-17

**你的提問**

我如果想讓別人連rdp進來我的電腦 我該如何設定

**當時回答**

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

---

<a id="q2"></a>

## S183-Q002｜我要怎麼建立

日期：2026-09-17

**你的提問**

我要怎麼建立

**當時回答**

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

---

<a id="q3"></a>

## S183-Q003｜那我的rdp 我給她甚麼連線資訊嗎

日期：2026-09-17

**你的提問**

那我的rdp 我給她甚麼連線資訊嗎

**當時回答**

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

---

<a id="q4"></a>

## S183-Q004｜BIOS 設定方式 - 開機後按 Delete or F2 進入 BIOS模式 - 進入 Boot - 找到 Wait For F1 if Error - 設定為 Disable …

日期：2026-09-17

**你的提問**

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

**當時回答**

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

---

<a id="q5"></a>

## S183-Q005｜我現在是THINKCENTRE

日期：2026-09-17

**你的提問**

我現在是THINKCENTRE

**當時回答**

ThinkCentre 是 Lenovo 的電腦，你貼的 BIOS 選單不一定適用，實際位置要看型號。

你可以先這樣進 BIOS：

1. 重新啟動電腦。
2. 出現 Lenovo 標誌時，連續按 **F1**；若無法進入，試試先按 **Enter**，再選 **F1 Setup**。

請提供 **完整型號**，例如 ThinkCentre M720q、M920q、M70q Gen 3。可以在機身貼紙或 `msinfo32` 的「系統型號」找到，不需要提供序號。

如果已經進入 BIOS，直接拍主畫面給我，我就能依照你的選單帶你設定。**先別改 CSM 或內顯選項**，需要先確認開機模式與硬體是否支援。

---

<a id="q6"></a>

## S183-Q006｜是桌電喔

日期：2026-09-17

**你的提問**

是桌電喔

**當時回答**

了解，是 **Lenovo ThinkCentre 桌上型電腦**。ThinkCentre 有多種桌機型號，BIOS 選單也會不同。

請按 **`Win + R` → 輸入 `msinfo32` → Enter**，把「**系統型號**」那一行貼給我，我就能依你的型號告訴你怎麼設定。

如果你已經進入 BIOS，也可以直接拍目前的畫面給我。

---

<a id="q7"></a>

## S183-Q007｜11DASOE300

日期：2026-09-17

**你的提問**

11DASOE300

**當時回答**

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

---

<a id="q8"></a>

## S183-Q008｜是按F1

日期：2026-09-17

**你的提問**

是按F1

**當時回答**

對，這台是按 **F1** 進 BIOS。重新啟動，出現 **Lenovo 標誌時連續按 F1**。

進去後拍一下 BIOS 畫面給我，我帶你逐項設定。

---

<a id="q9"></a>

## S183-Q009｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-3a0e877e-eb56-4cb5-8cba-2f761ef558ff.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3a0e877e-eb56-4cb5-8cba-2f761ef558ff.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

你已經成功進入 BIOS，畫面也確認是 **ThinkCentre M70t**。

先設定斷電後恢復狀態：

1. 選左側的 **Power**。
2. 找 **After Power Loss**。
3. 如果有，將它設為 **Last State**，就是你原本清單的 `Last Status`。

**先不用按 F10**，等其他項目確認完再一起儲存。請拍一下 **Power 頁面**，我帶你確認。

---

<a id="q10"></a>

## S183-Q010｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-85bca9d2-6f47-4726-a2a7-44871845630d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-85bca9d2-6f47-4726-a2a7-44871845630d.png

## codex-clipboard-32f8cb12-21d6-4b6b-a172-204ff9a62cd7.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-32f8cb12-21d6-4b6b-a172-204ff9a62cd7.png



## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**當時回答**

畫面中的 **After Power Loss 已經是 `Last State`**，符合你的設定需求，這項不用再改。

接著請點左側 **Startup**，拍一下裡面的選項，我們確認 **CSM、Fast Boot 和錯誤時等待 F1** 的設定。

先不用按 F10，等全部確認後再一起儲存。

---

<a id="q11"></a>

## S183-Q011｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-48accb53-7e2c-4880-ae5b-5c88b997acf5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-48accb53-7e2c-4880-ae5b-5c88b997acf5.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這張是 **Startup 頁面上半部**，右側還可以往下捲。

請**往下捲到最底，再拍一張**，確認下面有哪些設定。

目前看到的 **Primary／Automatic／Error Boot Sequence** 都是開機順序，先不用修改；其中 **Error Boot Sequence 並不是「錯誤時等待 F1」的開關**。

---

<a id="q12"></a>

## S183-Q012｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-8bd76384-733b-4850-ac7f-1eef44842982.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8bd76384-733b-4850-ac7f-1eef44842982.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

**Fast Boot 已經是 `Disabled`，符合你的需求。**

下方的 **Option Keys Display** 只是控制開機時是否顯示「按 F1」等提示，**不是錯誤時等待 F1 的設定**，保持原樣即可。

目前已確認：

- **After Power Loss：Last State** ✓
- **Fast Boot：Disabled** ✓

這個 Startup 頁面沒有看到 CSM 或錯誤等待選項。下一步請點左側 **Devices**，拍一下畫面，我們找內顯優先設定。

---

<a id="q13"></a>

## S183-Q013｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a5ffcdc1-fb34-44df-b9ea-d9dabd49d1c0.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a5ffcdc1-fb34-44df-b9ea-d9dabd49d1c0.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

找到顯示設定了。請選畫面中間的 **Video Setup**，按 **Enter** 進入，再拍一下裡面的選項，我們確認內顯優先要怎麼設。

---

<a id="q14"></a>

## S183-Q014｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-f1e3dcb1-c529-4a8a-a92d-1ef1e61252f4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f1e3dcb1-c529-4a8a-a92d-1ef1e61252f4.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這裡請選 **`IGD`**，就是你清單中 `iGP` 的對應選項，代表使用內建顯示晶片。

畫面也註明：CPU 不支援內顯就不會出現 IGD；你這台有提供此選項。

**若螢幕線目前插在獨立顯示卡上，儲存重開後可能需要改接主機板的顯示輸出孔。**

選好後，按左上角返回，再進左側 **Advanced** 拍一下畫面，我們繼續確認剩下的設定。先不用按 F10。

---

<a id="q15"></a>

## S183-Q015｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-b24b9a10-8e39-41c7-85cb-38ff0420fc4b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b24b9a10-8e39-41c7-85cb-38ff0420fc4b.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，**Select Active Video 已選為 `IGD`**，符合內顯優先的需求，等最後儲存才會正式套用。

下面兩項保持目前數值即可：

- **Pre-Allocated Memory Size：32MB**
- **Total Graphics Memory：Maximum**

接著按左上角 **←** 返回，進入 **Advanced**，拍一下畫面，我們確認剩下的 CSM 和錯誤等待 F1 選項。

---

<a id="q16"></a>

## S183-Q016｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-1c47b527-d964-40cc-b2c0-e7058e0fce56.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1c47b527-d964-40cc-b2c0-e7058e0fce56.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這張仍是 **Devices 頁面**，你已經從 Video Setup 返回了。

接著請點**左側選單的 Advanced**（在 Devices 下方、Power 上方），再拍一張畫面給我。

---

<a id="q17"></a>

## S183-Q017｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-fcde6348-3cf8-44b3-b52c-001d710bc089.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-fcde6348-3cf8-44b3-b52c-001d710bc089.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這頁 **Advanced 沒有看到 CSM 或等待 F1 的選項**，目前這些項目不用改。

目前確認的設定是：

- **After Power Loss：Last State**
- **Fast Boot：Disabled**
- **Select Active Video：IGD**（已選，尚待儲存）

剩下 **CSM、Wait for F1 if Error 尚未確認**，不能直接當作已關閉。

請再點左側 **Security**，拍一下選單；我們確認是否有開機錯誤相關設定。

---

<a id="q18"></a>

## S183-Q018｜圖片／附件提問

日期：2026-09-17

**你的提問**

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

**當時回答**

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

---

<a id="q19"></a>

## S183-Q019｜我還有一個是 WINDOWS的電源計畫

日期：2026-09-17

**你的提問**

我還有一個是 WINDOWS的電源計畫

**當時回答**

可以，先按 **F10 → Yes** 儲存 BIOS，進入 Windows 後再設定。

如果你是要讓電腦保持可供 **RDP 遠端連線**：

1. 按 **`Win + R`**，輸入 `powercfg.cpl`，按 Enter。
2. 在目前使用的電源計畫旁，點「**變更計畫設定**」。
3. 將「**讓電腦睡眠**」設為「**永不**」。
4. 「**關閉顯示器**」可自行設定，例如 10 分鐘；螢幕關閉不影響 RDP。
5. 點「**儲存變更**」。

**不需要為了 RDP 特別切成高效能，使用「平衡」即可。**

如果你有指定的電源計畫設定清單，或已開啟設定畫面，貼給我，我帶你逐項確認。

---

