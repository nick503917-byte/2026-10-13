# 安裝 SAP B1 Client

來源編號：S173  
聊天 ID：`01a0a7aa-b2f2-7290-aa4b-dd368e7ff2ac`  
聊天起始日期（台北）：2026-09-16  
保存位置：一般本機紀錄  
問答段落：31

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S173-Q001｜我要安裝 sapb1 client在我的電腦。 我該如何安裝 我已經從server把安裝包拉出來了

日期：2026-09-16

**你的提問**

我要安裝 sapb1 client在我的電腦。 我該如何安裝 我已經從server把安裝包拉出來了

**當時回答**

可以，你已經把安裝包從 Server 複製出來，接下來主要是 **安裝 Client，再連回公司的 SAP Server**。不同版本畫面會略有差異，通常照以下流程：

1. **先連上公司內網或 VPN**
   安裝完成後，需要連得到公司的 SAP Server 才能登入。

2. **確認安裝包完整**
   如果是 ZIP，先完整解壓縮；不要只複製或執行單獨一個 `setup.exe`，它可能需要同資料夾內的其他檔案。安裝包應與 Server 使用的 SAP B1 版本及修補版本一致。

3. **啟動 Client 安裝程式**
   找看看是否有 `Client`、`Client.x64` 等資料夾，在裡面的 `setup.exe` 按右鍵 →「以系統管理員身分執行」。
   如果你拿的是完整產品安裝包，請選 **SAP Business One Client**；SAP 官方也建議搭配安裝 **Data Interface API（DI API）**。[官方安裝說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_SQL/f6fb230cc90949d8b66586a39189992b/8911eedf866b4c7c85a38b009710bf8a.html)

4. **填入 SLD 連線資訊**
   安裝過程可能要求 **System Landscape Directory（SLD）** 的主機名稱及連接埠。這裡要填公司現有 SLD Server 的資料，請向 IT 取得，或參考另一台正常使用的電腦。**不要填你自己電腦的 `localhost` 或 `127.0.0.1`。**

5. **完成後開啟 SAP Business One**
   選擇公司資料庫，再用公司提供的 SAP B1 帳號、密碼登入。如果看不到公司清單，先確認 VPN／內網及 SLD 設定。

**你可以先貼上安裝包資料夾的截圖，或直接告訴我它在電腦上的完整路徑**，我就能幫你確認應該執行哪個檔案。如果知道 SAP B1 版本，以及公司用 SQL Server 還是 HANA，也一起告訴我；不知道也沒關係。

---

<a id="q2"></a>

## S173-Q002｜'

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-d51c4320-d117-4288-ae3c-2583b4d5f795.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d51c4320-d117-4288-ae3c-2583b4d5f795.png



## My request:
'

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個資料夾裡有 **SAP Business One Client（64-bit）安裝程式**。

接下來這樣操作：

1. 在圖片靠下方的 **`setup.exe`** 按右鍵 → **「以系統管理員身分執行」**。
2. 若 Windows 詢問是否允許變更，確認發行者及來源可信後按「是」。
3. 依照安裝精靈按「Next／下一步」，安裝路徑通常保留預設即可。
4. 如果出現 **SLD／Server／伺服器位址** 的畫面，先停在那一頁，需填入公司的 SAP Server 連線資料。

請先從 **`setup.exe`** 啟動，不要直接點 `.msi`，也保留旁邊所有資料夾和檔案。

**開啟後把下一個畫面貼給我，我可以接著帶你填。**

---

<a id="q3"></a>

## S173-Q003｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-32486509-25ca-472f-98ec-7cb5d6f70904.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-32486509-25ca-472f-98ec-7cb5d6f70904.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這一頁是 **安裝使用者資訊**，不是 SAP 登入畫面。

- **User ID**：填你的姓名或公司使用者名稱，也可以保留目前的 `qwe`。
- **Company Name**：填你的公司名稱。

填好兩欄後，按 **Next >** 繼續。這裡不需要輸入 SAP 密碼或 Server 位址。

---

<a id="q4"></a>

## S173-Q004｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-7c92290d-d6af-4107-98f2-8f1d4722dac5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7c92290d-d6af-4107-98f2-8f1d4722dac5.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這頁是設定「開始功能表」中的捷徑資料夾名稱。

保留上方預設的 **`SAP Business One`**，不用選下面的資料夾，直接按 **Next >** 繼續。

---

<a id="q5"></a>

## S173-Q005｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-73fc5cc6-4159-4efc-9d66-cb3cec282425.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-73fc5cc6-4159-4efc-9d66-cb3cec282425.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個錯誤表示 **DI API 安裝失敗，導致 Client 安裝中止**。從畫面中的路徑看，可能只複製了 `Client.x64`，缺少旁邊的 `DI API` 資料夾。

安裝程式正在找：
```text
C:\Users\nick_yeh\Desktop\Client.x64\DI API\setup.exe
```

請這樣處理：

1. 按「確定」，退出這次安裝。
2. 回到 Server 原本放 `Client.x64` 的位置，找同一層的 **`DI API`** 資料夾，將整個資料夾複製過來。
3. 依你目前的路徑，資料夾應排列成：
   ```text
   桌面\Client.x64\
   ├─ Client.x64\
   │  └─ setup.exe
   └─ DI API\
      └─ setup.exe
   ```
4. 再對內層 `Client.x64\setup.exe` 按右鍵 →「以系統管理員身分執行」。

**如果上述位置已經有 `DI API\setup.exe`**，就不是單純缺少檔案；請告訴我，我們再查看 DI API 本身的安裝錯誤。

---

<a id="q6"></a>

## S173-Q006｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-c7237b10-c0bd-4621-a786-77db4ab22b25.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c7237b10-c0bd-4621-a786-77db4ab22b25.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

現在已進入 **DI API 的 SLD 伺服器設定**，這裡需要填公司的 SAP 連線資料：

- **SLD Server Name**：把 `localhost` 改成公司 **SLD 伺服器的主機名稱或完整網域名稱**，可向 IT 確認。
- **Port**：目前是 `40000`，若 IT 確認公司使用這個連接埠，就保持不變。
- **Enable certificate verification**：先保持勾選。
- 保持選取 **SLD Server Name**，不必改選下方的共用資料夾。

連上公司內網或 VPN，填好後再按 **Next >**。

**你從哪個 Server 路徑複製安裝包？** 例如 `\\SAPSERVER\B1_SHR`。這能提供查找線索，但該 Server 不一定就是 SLD 主機。

---

<a id="q7"></a>

## S173-Q007｜我可以去server查詢嗎

日期：2026-09-16

**你的提問**

我可以去server查詢嗎

**當時回答**

可以，直接去 Server 查詢。**最準確的是查看 SLD Control Center 的網址**：

1. 登入 Server，從開始功能表搜尋 **SLD** 或 **System Landscape Directory**，看看是否有 Control Center 捷徑；也可以查看瀏覽器中既有的 SLD 書籤。
2. 開啟後查看網址，例如：
   ```text
   https://SAPSERVER.company.local:40000/ControlCenter
   ```
   對應你目前的安裝畫面：
   - **SLD Server Name**：`SAPSERVER.company.local`
   - **Port**：`40000`

**如果網址顯示 `localhost`，不要直接照抄到你的電腦**，因為那代表 Server 自己。需確認該 Server 實際的主機名稱，最好使用與憑證相符的完整網域名稱。

你可以把找到的 **SLD 網址或捷徑內容截圖**貼給我，我幫你確認怎麼填；不需要提供密碼。

---

<a id="q8"></a>

## S173-Q008｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a84fafdd-12d1-4f09-a065-29ed74390483.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a84fafdd-12d1-4f09-a065-29ed74390483.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

找到了，依照你畫面上方的 SLD 網址，回到你電腦的安裝視窗填入：

| 欄位 | 填入內容 |
|---|---|
| **SLD Server Name** | `sapb1-aws.wellshin.com` |
| **Port** | `40000` |

主機名稱欄位**不用加 `https://` 或 `/ControlCenter/`**。

**Enable certificate verification 先保持勾選**，確認已連上公司內網或 VPN，再按 **Next >**。

你這張圖的瀏覽器顯示「不安全」，代表憑證可能有問題。如果下一步出現憑證或連線錯誤，請把錯誤畫面貼給我，先不要直接取消憑證驗證。

---

<a id="q9"></a>

## S173-Q009｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-91a6a5d5-01d9-446f-8185-34cb4df213c2.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-91a6a5d5-01d9-446f-8185-34cb4df213c2.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

現在已經開啟 SAP B1 Client，這頁是在選擇 **SLD 伺服器**。

依照剛才 Server 上的網址，請填：

- **伺服器名稱**：`sapb1-aws.wellshin.com`
- **Port**：`40000`

再按 **「確定」**。

目前欄位裡的 `172.16.100.9` 是內網 IP；目前還不能確認它是否就是同一台 SLD，先使用剛才確認的主機名稱。請保持公司內網或 VPN 連線。

如果出現錯誤，或進入公司選擇畫面，再貼給我。

---

<a id="q10"></a>

## S173-Q010｜''

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-b05bfe6f-f99a-41c0-8874-baa094e89cec.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b05bfe6f-f99a-41c0-8874-baa094e89cec.png



## My request:
''

[原對話含圖片，本文未嵌入圖片]

**當時回答**

下方紅字顯示：**「您無法儲存系統架構目錄位址；請檢查是否您具備權限，以及是否相關組態檔案毀損……」**

這個訊息先指向 **本機設定儲存問題**，還不能判定是 Server 位址錯誤。

請先試：

1. 完全關閉 SAP Business One。
2. 在 SAP Business One 捷徑按右鍵 → **「以系統管理員身分執行」**。
3. 重新輸入：
   - 伺服器：`sapb1-aws.wellshin.com`
   - Port：`40000`
4. 按「確定」。

如果仍出現同樣紅字，請告訴我；下一步再檢查本機設定檔權限，先不用重裝。

---

<a id="q11"></a>

## S173-Q011｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-335f96f6-9bcb-438b-93ba-1187463b32f9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-335f96f6-9bcb-438b-93ba-1187463b32f9.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

已進入 **「選擇／建立公司」**畫面。現在右邊的伺服器欄位還是空白，所以先選伺服器：

1. 點 **`MSSQL_2019` 右側空白欄位的下拉箭頭**。
2. 若有伺服器，選取後按 **「重新整理」**。
3. 公司清單出現後，選擇你的公司，輸入 **SAP B1 使用者 ID 和密碼**，再按「確定」。

**如果右側下拉選單完全空白**，請到剛才 Server 的 SLD 網頁，點 **「DB Instances and Companies」**，把該頁截圖貼給我。我們需要確認登記的資料庫類型與伺服器名稱；目前畫面的 `MSSQL_2019` 還不能證明公司使用的就是這個版本。

---

<a id="q12"></a>

## S173-Q012｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-b7ed87ec-956d-44fe-a2c0-a57c7e06d7ba.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b7ed87ec-956d-44fe-a2c0-a57c7e06d7ba.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

確認了，SLD 顯示公司使用的是 **SQL Server 2022**，伺服器名稱是 **`EC2AMAZ-IGRTDRC`**，狀態為 **Online**。

回到你電腦的 SAP B1：

1. 將左邊的 **`MSSQL_2019` 改選 `MSSQL_2022`**。
2. 在右邊下拉選單選 **`EC2AMAZ-IGRTDRC`**。
3. 按 **「重新整理」**。
4. 公司清單出現後，選擇要登入的公司，輸入自己的 **SAP B1 帳號及密碼**。

**如果左邊沒有 `MSSQL_2022`，或選了之後右邊仍空白，請把下拉選單展開的畫面貼給我。** SLD 這一頁目前不需要修改。

---

<a id="q13"></a>

## S173-Q013｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-3c20eb1b-199a-4fe7-8000-bec9f88b779c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3c20eb1b-199a-4fe7-8000-bec9f88b779c.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

你的選單確實只有 **MSSQL_2019**。先檢查電腦是否缺少 **Microsoft ODBC Driver 18 for SQL Server**；SAP 官方對 SQL Server 2022 要求使用 ODBC 18。[SAP 安裝需求](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_SQL/f6fb230cc90949d8b66586a39189992b/a3f76b099c294630a7b38c2aba1de758.html?locale=en-US)

請在**你的電腦**上操作：

1. 按 Windows 開始，搜尋並開啟 **「ODBC 資料來源（64 位元）」**。
2. 切到 **「驅動程式」**頁籤。
3. 查看清單是否有 **`ODBC Driver 18 for SQL Server`**。

**把這個驅動程式清單截圖貼給我。** 若缺少就補裝；若已存在，再核對 SAP Client 與 DI API 的版本。先不用重裝 SAP，也不用修改 Server。

---

<a id="q14"></a>

## S173-Q014｜沒˙有 我要安裝包

日期：2026-09-16

**你的提問**

沒˙有 我要安裝包

**當時回答**

可以，這是 **Microsoft 官方 ODBC Driver 18（x64）安裝包**：

**[點此下載安裝包 msodbcsql.msi](https://go.microsoft.com/fwlink/?linkid=2358430)**

下載後：

1. 先關閉 **SAP Business One**。
2. 執行 `msodbcsql.msi`，接受授權條款，保持預設選項完成安裝。
3. 重新開啟 SAP B1，查看是否出現 **`MSSQL_2022`**。
4. 若出現，選取後再選 **`EC2AMAZ-IGRTDRC`**，按「重新整理」。

如果安裝時提示缺少 **Visual C++**，把錯誤畫面貼給我。

---

<a id="q15"></a>

## S173-Q015｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-f94b2c52-1c5f-4b31-8cd5-9097ccfdd123.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f94b2c52-1c5f-4b31-8cd5-9097ccfdd123.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

**SQL Server 2022 和公司清單已經出現，缺少驅動的問題有進展了。** 但底部現在顯示：

> 資料庫連線遺失（ODBC -1102）[訊息 131-183]

能讀取公司清單，不代表已成功連上公司資料庫。這個錯誤有多種原因，需進一步確認。[SAP 官方錯誤說明](https://userapps.support.sap.com/sap/support/knowledge/en/3435609)

先檢查你的電腦能否連到清單中的 **`EC2AMAZ-IGRTDRC`**：

1. 保持公司 VPN 連線。
2. 開啟 **PowerShell**，貼上：
   ```powershell
   Test-NetConnection EC2AMAZ-IGRTDRC -Port 1433
   ```
3. 把執行結果截圖貼給我。

這會檢查主機名稱解析與 SQL 常用連接埠；**如果 SQL 使用自訂連接埠，還要依 Server 的實際設定再測**。先不用重裝或修改 SLD。

---

<a id="q16"></a>

## S173-Q016｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-fcac3178-652b-4f45-baf7-d2082d22c784.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-fcac3178-652b-4f45-baf7-d2082d22c784.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

找到一個明確問題：**你的電腦無法把 `EC2AMAZ-IGRTDRC` 解析成 IP 位址**。畫面中的 `Name resolution ... failed` 就是這個意思，目前還沒測到 SQL 連接埠。

請先確認 SQL Server 的內網 IP：

1. 在 **Server 上**開啟 PowerShell，執行：
   ```powershell
   hostname
   ipconfig
   ```
2. 確認主機名稱是 `EC2AMAZ-IGRTDRC`，並找到使用中網卡的 **IPv4 位址**。
3. 回到**你的電腦**，以該 IP 測試。例如確認為 `172.16.100.9` 才執行：
   ```powershell
   Test-NetConnection 172.16.100.9 -Port 1433
   ```

把結果貼給我。**如果 IP 可以連通，下一步就是修正 DNS 或本機 hosts 的名稱對應**；如果不通，再查 VPN 路由及 SQL 實際連接埠。

---

<a id="q17"></a>

## S173-Q017｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a58915a8-052b-4971-be1e-9cc492f1412c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a58915a8-052b-4971-be1e-9cc492f1412c.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

確認了，Server 的資料是：

- **主機名稱**：`EC2AMAZ-IGRTDRC`
- **內網 IP**：`172.16.100.9`

接下來回到**你自己的電腦**，保持 VPN 連線，在 PowerShell 執行：

```powershell
Test-NetConnection 172.16.100.9 -Port 1433
```

把結果貼給我，主要看 **`TcpTestSucceeded`**：

- **True**：IP 和連接埠可連通，接著修正主機名稱對應。
- **False**：需要再確認 VPN 路由、防火牆或 SQL 實際使用的連接埠。

---

<a id="q18"></a>

## S173-Q018｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-43ae73af-c6b1-4adb-aa0a-e015ba3681f0.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-43ae73af-c6b1-4adb-aa0a-e015ba3681f0.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

確認了：**IP 的 1433 連接埠可連通，但主機名稱解析失敗。** 可以先在你的電腦加上 hosts 對應。

1. 在開始功能表搜尋「記事本」，按右鍵 → **以系統管理員身分執行**。
2. 選「檔案 → 開啟」，輸入：
   ```text
   C:\Windows\System32\drivers\etc\hosts
   ```
   若看不到檔案，將檔案類型改成「所有檔案」。
3. 保留原有內容，在最後新增一行：
   ```text
   172.16.100.9    EC2AMAZ-IGRTDRC
   ```
4. 儲存，注意不要另存成 `hosts.txt`。
5. 回到 PowerShell 執行：
   ```powershell
   ipconfig /flushdns
   Test-NetConnection EC2AMAZ-IGRTDRC -Port 1433
   ```

如果這次 **`TcpTestSucceeded : True`**，完全關閉並重新開啟 SAP B1，再選公司登入。

這會修正目前已確認的名稱解析問題；若仍有登入錯誤，再把新畫面貼給我。

---

<a id="q19"></a>

## S173-Q019｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-4b6664bb-4781-43bb-b8e5-afd7aa0e8adb.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-4b6664bb-4781-43bb-b8e5-afd7aa0e8adb.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

畫面上有 **兩個地方要修正**：

1. **新增那行前面的 `#` 要刪除**，否則只是註解，不會生效。改成：
   ```text
   172.16.100.9    EC2AMAZ-IGRTDRC
   ```

2. 分頁名稱目前是 **`hosts.txt`**，需要存成沒有副檔名的 **`hosts`**：
   - 按「檔案 → 另存新檔」。
   - 儲存位置：`C:\Windows\System32\drivers\etc`
   - 存檔類型選 **「所有檔案」**。
   - 檔案名稱輸入 **`"hosts"`**（包含雙引號，避免自動加上 `.txt`）。
   - 確認保留原有內容後，儲存並取代原本的 hosts。若顯示權限不足，需以系統管理員身分開啟記事本。

完成後，在 PowerShell 執行：

```powershell
ipconfig /flushdns
Test-NetConnection EC2AMAZ-IGRTDRC -Port 1433
```

看到 **`TcpTestSucceeded : True`** 後，再重新開啟 SAP B1 登入。

---

<a id="q20"></a>

## S173-Q020｜這樣嗎

日期：2026-09-16

**你的提問**

這樣嗎

**當時回答**

這則訊息沒有附上新截圖，我還看不到修改結果。

請確認這行**前面沒有 `#`**：

```text
172.16.100.9    EC2AMAZ-IGRTDRC
```

而且存檔位置是 **`C:\Windows\System32\drivers\etc\hosts`**，不是 `hosts.txt`。

存好後執行：

```powershell
ipconfig /flushdns
Test-NetConnection EC2AMAZ-IGRTDRC -Port 1433
```

把結果貼給我；若 **`TcpTestSucceeded : True`**，就可以重新開啟 SAP B1 試登入。

---

<a id="q21"></a>

## S173-Q021｜我沒辦法儲存了

日期：2026-09-16

**你的提問**

我沒辦法儲存了

**當時回答**

很可能是記事本沒有系統管理員權限，請這樣操作：

1. **先複製你修改好的內容**，避免遺失。
2. 點 Windows 開始，搜尋 **「記事本」** → 按右鍵 → **「以系統管理員身分執行」** → 按「是」。
3. 在新開的記事本按 **Ctrl + O**，輸入完整路徑：
   ```text
   C:\Windows\System32\drivers\etc\hosts
   ```
4. 在原有內容最後新增下面這行，**前面不要加 `#`**：
   ```text
   172.16.100.9    EC2AMAZ-IGRTDRC
   ```
5. 按 **Ctrl + S** 直接儲存，不用另存新檔。

**如果還是不能儲存，請貼上儲存時的錯誤畫面。** 若 Windows 要求管理員帳號密碼，而你沒有，這一步需要請公司 IT 協助。

---

<a id="q22"></a>

## S173-Q022｜好了 下一步

日期：2026-09-16

**你的提問**

好了 下一步

**當時回答**

接下來在 **你自己的電腦 PowerShell** 執行：

```powershell
ipconfig /flushdns
Test-NetConnection EC2AMAZ-IGRTDRC -Port 1433
```

如果顯示 **`TcpTestSucceeded : True`**：

1. 完全關閉並重新開啟 **SAP Business One**。
2. 選擇 **`MSSQL_2022`** → **`EC2AMAZ-IGRTDRC`**。
3. 選擇要登入的公司，輸入 SAP 帳號、密碼，按 **「確定」**。

如果測試不是 `True`，或登入仍報錯，把結果截圖貼給我。

---

<a id="q23"></a>

## S173-Q023｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-bf1ec114-7084-4194-9a72-20524260e21a.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-bf1ec114-7084-4194-9a72-20524260e21a.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這次又出現 **「無法儲存系統架構目錄位址」**，和前面遇到的是同一個訊息。先處理本機設定的儲存權限：

1. **完全關閉 SAP Business One**。
2. 在 SAP Business One 捷徑按右鍵 → **「以系統管理員身分執行」**。
3. 保留以下設定，再按「確定」：
   - 伺服器：`sapb1-aws.wellshin.com`
   - Port：`40000`

你之前修改的 hosts 是處理資料庫主機名稱解析，這個儲存錯誤需要另外處理。

**如果這次已經是以系統管理員身分開啟，請直接告訴我**，下一步改查設定檔權限，不用再重複安裝。

---

<a id="q24"></a>

## S173-Q024｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a2dfec52-12ef-48df-8d52-c19ad08a4db9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a2dfec52-12ef-48df-8d52-c19ad08a4db9.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這次進入公司選擇畫面了，但**左側伺服器類型顯示 `99`，右側也沒有伺服器**，與之前正常顯示 `MSSQL_2022` 的狀態不同。先不要手動輸入 `99`。

請先確認：

1. 點左側 `99` 旁的下拉箭頭，看是否有 **`MSSQL_2022`**；若有，選取後再選右側的 **`EC2AMAZ-IGRTDRC`**，按「刷新」。
2. 如果沒有，請把**展開的選單**截圖貼給我。

**這張畫面是在你原本那台電腦，還是在 Server／另一台電腦上？** 因為之前同一流程已經顯示過 SQL 2022 和公司清單，需要先確認現在開啟的是哪台電腦上的 Client。

---

<a id="q25"></a>

## S173-Q025｜另一台電腦

日期：2026-09-17

**你的提問**

另一台電腦

**當時回答**

了解，**另一台電腦需要另外安裝驅動與設定 hosts**，之前那台的設定不會自動帶過來。`99` 的原因目前還不能只靠畫面確定。

先在這台電腦依序處理：

1. **關閉 SAP B1，安裝 ODBC Driver 18（x64）**  
   [Microsoft 官方安裝包](https://go.microsoft.com/fwlink/?linkid=2358430)

2. **連上公司內網或 VPN**，在 PowerShell 執行：
   ```powershell
   Test-NetConnection EC2AMAZ-IGRTDRC -Port 1433
   ```
   如果顯示名稱解析失敗，再以系統管理員開啟記事本，在 `C:\Windows\System32\drivers\etc\hosts` 最後加入：
   ```text
   172.16.100.9    EC2AMAZ-IGRTDRC
   ```
   儲存後執行：
   ```powershell
   ipconfig /flushdns
   ```

3. **以系統管理員身分重新開啟 SAP B1**
   - SLD：`sapb1-aws.wellshin.com`
   - Port：`40000`
   - 公司選擇畫面：選 `MSSQL_2022` → `EC2AMAZ-IGRTDRC`

**如果安裝驅動並重開後仍顯示 `99`**，下一步就核對這台的 SAP B1 Client 與 DI API 版本，請貼上「輔助說明 → 關於 SAP Business One」的畫面。

---

<a id="q26"></a>

## S173-Q026｜如果我沒有ODBC Driver 18（x64）

日期：2026-09-17

**你的提問**

如果我沒有ODBC Driver 18（x64）

**當時回答**

如果這台電腦沒有 **ODBC Driver 18（x64）**，就先補裝，因為你公司的 SAP B1 使用 **SQL Server 2022**，需要這個驅動。

**[下載 Microsoft 官方 ODBC Driver 18（x64）安裝包](https://go.microsoft.com/fwlink/?linkid=2358430)**

在這台電腦操作：

1. 關閉 SAP Business One。
2. 執行下載的 `msodbcsql.msi`，依預設選項完成安裝。
3. 重新開啟 SAP B1，檢查原本顯示 `99` 的選單是否出現 **`MSSQL_2022`**。

如果這台無法下載，可以從另一台下載後複製安裝包過來。**不需要在這台安裝整套 SQL Server。**

---

<a id="q27"></a>

## S173-Q027｜沒辦法安裝

日期：2026-09-17

**你的提問**

沒辦法安裝

**當時回答**

**安裝時出現什麼錯誤？請把錯誤視窗截圖貼給我**，才能判斷下一步。

也請告訴我是卡在哪裡：

- **無法下載**安裝包。
- **點開 `msodbcsql.msi` 沒反應**。
- **安裝途中報錯**，例如缺少 Visual C++。
- **要求管理員密碼**，或被公司政策封鎖。

先不用重裝 SAP B1。

---

<a id="q28"></a>

## S173-Q028｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-4fa7a47f-532d-4ab4-b2e7-f0b7785dc5b4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-4fa7a47f-532d-4ab4-b2e7-f0b7785dc5b4.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個錯誤是：**目前的作業系統不受這個安裝包支援**。不是缺少管理員權限，也不是 Visual C++ 的提示。

先確認這台電腦的 Windows 版本與架構：

1. 按 **Win + R**，輸入 `winver`，按 Enter，把視窗截圖貼給我。
2. 到 **設定 → 系統 → 關於**，查看「**系統類型**」，告訴我是：
   - x64 型處理器／64 位元作業系統
   - ARM 型處理器
   - 32 位元作業系統

確認後才能選擇適用的安裝包。**先不要改裝 x86 或用相容性模式強行安裝**，還需要符合 SAP B1 Client 的需求。

---

<a id="q29"></a>

## S173-Q029｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-206a2bba-0f5e-4dbd-b3a0-f64dcfa885e4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-206a2bba-0f5e-4dbd-b3a0-f64dcfa885e4.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這台是 **Windows 11 專業版 25H2**，但這個畫面還看不到處理器架構，還不能確定安裝包為什麼不支援。

請到 **設定 → 系統 → 關於 → 裝置規格**，把 **「系統類型」**那一行貼給我，確認是 **x64** 還是 **ARM 型處理器**。

另外，**這台 Windows 是在 Mac 上透過 Parallels 或其他虛擬機執行的嗎？** 如果是 Apple Silicon Mac，可能使用 Windows ARM，需要再確認驅動及 SAP B1 的相容性。

---

<a id="q30"></a>

## S173-Q030｜圖片／附件提問

日期：2026-09-17

**你的提問**

# Files mentioned by the user:

## codex-clipboard-cfdd658d-b063-477a-a965-e24c065f3e9b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cfdd658d-b063-477a-a965-e24c065f3e9b.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

原因確認了：這台是 **Apple Silicon Mac＋Parallels 的 Windows 11 ARM**，不是一般 x64 Windows。我前面直接叫你安裝 x64 驅動，沒有先確認架構，抱歉。

**也不能單純改裝 ARM64 驅動就解決。** Microsoft 說明 ARM64 ODBC 驅動僅供 ARM64 應用程式使用，不提供 x64 模擬程式所需的驅動；你現在安裝的 SAP B1 Client 是 x64 版本。[Microsoft 官方說明](https://learn.microsoft.com/en-us/sql/connect/odbc/windows/features-of-the-microsoft-odbc-driver-for-sql-server-on-windows?view=sql-server-ver17)

目前建議改用 **遠端桌面**，連到公司已安裝 SAP B1 的 x64 Windows 電腦或遠端桌面主機，在那裡執行 SAP。

**公司有提供給使用者登入 SAP 的遠端桌面主機嗎？** 如果有，下一步可以直接設定 Mac 連線，不必繼續在這台 ARM 虛擬機重裝。

---

<a id="q31"></a>

## S173-Q031｜我需要裝可以嗎

日期：2026-09-17

**你的提問**

我需要裝可以嗎

**當時回答**

**你要在這台 Mac 的 Windows 裡直接安裝使用，我了解。** 但目前不能保證這個組合能正常運作：

- SAP B1 Client 是 **x64 程式**。
- 你的 Parallels 是 **Windows ARM**。
- x64 ODBC 安裝程式已被拒絕，而 ARM64 ODBC 也不能直接替代 x64 程式需要的驅動。[Microsoft 說明](https://learn.microsoft.com/en-us/sql/connect/odbc/windows/features-of-the-microsoft-odbc-driver-for-sql-server-on-windows?view=sql-server-ver17)

所以，**Client 雖然已裝好、能開啟，仍卡在登入資料庫所需的驅動相容性**。我不能把「換成 ARM64 安裝包」說成可用的解法。

如果一定要本機執行，下一步應請公司的 **SAP 維護廠商確認這個 B1 版本是否有經驗證的 Windows ARM 安裝方式**。目前我們沒有確認可用的方法；先不要強制修改安裝包，否則即使裝上，也未必能登入。

---

