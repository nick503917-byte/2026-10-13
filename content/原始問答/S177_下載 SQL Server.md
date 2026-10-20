# 下載 SQL Server

來源編號：S177  
聊天 ID：`01a0a910-4c8c-7942-88d4-b86b3df20073`  
聊天起始日期（台北）：2026-09-16  
保存位置：一般本機紀錄  
問答段落：6

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S177-Q001｜我要下載 SQL Server

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-c356fe84-2729-4d0c-be75-62e392534be4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c356fe84-2729-4d0c-be75-62e392534be4.png



## My request:
我要下載 SQL Server

[原對話含圖片，本文未嵌入圖片]

**當時回答**

你已經開啟 **SQL Server 2022 安裝中心**，可以直接開始安裝：

1. 點左側 **Installation（安裝）**。
2. 點 **New SQL Server stand-alone installation or add features to an existing installation**（新增 SQL Server 獨立安裝或新增功能）。
3. 到 **Feature Selection（功能選取）** 時，勾選 **Database Engine Services（資料庫引擎服務）**。
4. 到 **Database Engine Configuration（資料庫引擎設定）** 時，若是自己在這台電腦練習，可使用 **Windows authentication mode**，並點 **Add Current User（新增目前使用者）**。
5. 繼續按 **Next**，最後按 **Install**。[微軟官方安裝說明](https://learn.microsoft.com/en-us/sql/database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup)

先點左側 **Installation**，把下一個畫面貼給我，我可以接著帶你選。

---

<a id="q2"></a>

## S177-Q002｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-cbba229a-6850-4140-b69a-37fb86a948c7.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-cbba229a-6850-4140-b69a-37fb86a948c7.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這是 **版本選擇（Edition）** 畫面。如果你是用來學習、練習 SQL 或開發程式：

1. 點選 **Specify a free edition** 左邊的圓圈。
2. 把下拉選單的 **Evaluation** 改成 **Developer**。
3. 按右下角 **Next**。

**Developer 免費且無到期日，僅供開發與測試使用**；Evaluation 則有 180 天期限。選擇 Developer 不需要輸入產品金鑰。

下一頁是授權條款，閱讀並同意後，就能繼續安裝。

---

<a id="q3"></a>

## S177-Q003｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-398e68f4-eaa3-4dc8-996b-bb1dbb0627fb.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-398e68f4-eaa3-4dc8-996b-bb1dbb0627fb.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這頁是 **Azure 雲端服務擴充功能**。如果只是在自己電腦上學習 SQL：

1. **取消勾選**上方的 **Azure Extension for SQL Server**。
2. 下方 Azure 帳號等欄位都不用填。
3. 點右下角 **Next**。

下一頁是 **Feature Selection（功能選取）**，請勾選 **Database Engine Services（資料庫引擎服務）**。

---

<a id="q4"></a>

## S177-Q004｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-0e731ced-2494-4127-a0d3-58936fc2d579.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-0e731ced-2494-4127-a0d3-58936fc2d579.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這頁只要勾選第一項 **Database Engine Services（資料庫引擎服務）**，其他功能先不用勾，安裝路徑維持預設。

**但畫面上方顯示「Standard」版本**。如果你是要免費學習使用，請先按 **Back** 回到 **Edition**，確認選擇 **Specify a free edition → Developer**，再繼續。

如果你確定有 Standard 授權，就可以勾選資料庫引擎後按 **Next**。

---

<a id="q5"></a>

## S177-Q005｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-af49bf4f-3eba-4516-933f-ead4c7c1c739.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-af49bf4f-3eba-4516-933f-ead4c7c1c739.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這頁是 **Instance Configuration（執行個體設定）**，維持目前設定即可：

- 選擇 **Default instance**。
- **Instance ID** 保留 `MSSQLSERVER`。

另外，左側出現 **PolyBase Configuration**，表示你可能也勾選了 PolyBase。一般學習 SQL 不需要，可以按 **Back** 回到功能選取，只保留 **Database Engine Services**。

完成後按 **Next** 繼續。

---

<a id="q6"></a>

## S177-Q006｜圖片／附件提問

日期：2026-09-16

**你的提問**

# Files mentioned by the user:

## codex-clipboard-8a39aac8-13d7-480b-84b8-6ad4d83231c0.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8a39aac8-13d7-480b-84b8-6ad4d83231c0.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這頁是 **Server Configuration（伺服器設定）**，先維持預設即可：

- **Account Name／Password**：不用修改或填寫。
- **Startup Type**：維持目前設定。
- 下方 **Grant Perform Volume Maintenance Tasks…**：先不勾也可以正常使用。

按右下角 **Next**。

下一頁 **Database Engine Configuration**，選 **Windows authentication mode**，並記得按 **Add Current User（新增目前使用者）**，讓你的 Windows 帳號能管理資料庫。

---

