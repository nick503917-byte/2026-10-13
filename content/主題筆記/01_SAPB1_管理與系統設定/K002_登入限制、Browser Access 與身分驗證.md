---
title: "登入限制、Browser Access 與身分驗證"
category: "01_SAPB1_管理與系統設定"
updated: 2026-10-20
sources: ["S058","S161","S174","S221","S228"]
---

# 登入限制、Browser Access 與身分驗證

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 Browser Access 處理程序上限、同帳號多站台登入、殘留連線、SLD 與身分驗證管理議題。程序數量、授權限制與單一登入政策不能視為同一設定；歷史記錄提及 SP 2505，實際操作應依版本確認。另含 DTW 匯率匯入的後續討論。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S058｜查看BA網頁版連線上限](<../../原始問答/S058_查看BA網頁版連線上限.md>)：2026-08-07，5 段問答。
- [S161｜確認 SAP B1 網頁登入連線](<../../原始問答/S161_確認 SAP B1 網頁登入連線.md>)：2026-09-10，3 段問答。
- [S174｜檢查電腦健康狀況](<../../原始問答/S174_檢查電腦健康狀況.md>)：2026-09-16，5 段問答。
- [S221｜查找 SAP B1 網頁版登入互斥設定](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md>)：2026-10-05，9 段問答。
- [S228｜限制 SAP B1 單一登入](<../../原始問答/S228_限制 SAP B1 單一登入.md>)：2026-10-07，39 段問答。

## 問題索引

- [01｜我有設定 網頁版的BA連線 我想看 我開了允許多少人連線](#qa1)
- [02｜'](#qa2)
- [03｜這個嗎](#qa3)
- [04｜圖片／附件提問](#qa4)
- [05｜圖片／附件提問](#qa5)
- [06｜當我在sapb1 用網頁版登入後 我可以在server 端 的 SSMS 我可以看到連線的人嗎](#qa6)
- [07｜所以我ssms 沒辦法看到我的sapb1 某一個資料庫有連線資訊嗎](#qa7)
- [08｜我要怎麼看我自己的權限](#qa8)
- [09｜可以幫我健檢我的電腦嗎](#qa9)
- [10｜我覺得有點慢](#qa10)
- [11｜sap](#qa11)
- [12｜圖片／附件提問](#qa12)
- [13｜是一直轉圈／沒有回應](#qa13)
- [14｜我 SAPB1網頁版登入，會踢掉另一個 這個可以卡控嗎](#qa14)
- [15｜網頁版跟往葉板](#qa15)
- [16｜網頁版登入後，會把另一個網頁版踢掉](#qa16)
- [17｜我想問一下 DTW 可以匯入匯率嗎](#qa17)
- [18｜10.0](#qa18)
- [19｜圖片／附件提問](#qa19)
- [20｜但我找不到](#qa20)
- [21｜圖片／附件提問](#qa21)
- [22｜甚麼意思](#qa22)
- [23｜我在想 我有可能限制 SAPB1 帳號的登入嗎 就是一次只能一個](#qa23)
- [24｜已經有人登入時，直接拒絕第二個人，保留第一個人的操作：這和上述原生行為不同，需要依你們的版本、登入方式確認能否實現。[SAP 登入限制說明](〔連結〕 10.0 會希望是網頁版卡住](#qa24)
- [25｜也是網頁](#qa25)
- [26｜圖片／附件提問](#qa26)
- [27｜圖片／附件提問](#qa27)
- [28｜所以有辦法做到嗎](#qa28)
- [29｜客製](#qa29)
- [30｜協助設計實作](#qa30)
- [31｜1.可以 2.會有登入頁後會有這個畫面 3.〔連結〕](#qa31)
- [32｜圖片／附件提問](#qa32)
- [33｜這樣嗎](#qa33)
- [34｜圖片／附件提問](#qa34)
- [35｜圖片／附件提問](#qa35)
- [36｜圖片／附件提問](#qa36)
- [37｜圖片／附件提問](#qa37)
- [38｜圖片／附件提問](#qa38)
- [39｜圖片／附件提問](#qa39)
- [40｜圖片／附件提問](#qa40)
- [41｜圖片／附件提問](#qa41)
- [42｜圖片／附件提問](#qa42)
- [43｜圖片／附件提問](#qa43)
- [44｜圖片／附件提問](#qa44)
- [45｜圖片／附件提問](#qa45)
- [46｜這個會是甚麼密碼啊](#qa46)
- [47｜應該沒有人碰過](#qa47)
- [48｜圖片／附件提問](#qa48)
- [49｜圖片／附件提問](#qa49)
- [50｜圖片／附件提問](#qa50)
- [51｜圖片／附件提問](#qa51)
- [52｜圖片／附件提問](#qa52)
- [53｜圖片／附件提問](#qa53)
- [54｜我看不出](#qa54)
- [55｜目前操作的就是大家正在使用](#qa55)
- [56｜我比較好奇 通常設定這個的帳號密碼是甚麼ˊ時候](#qa56)
- [57｜所以這個密碼看不到嗎](#qa57)
- [58｜那可以試試看取得嗎](#qa58)
- [59｜圖片／附件提問](#qa59)
- [60｜服務設定可正常讀取](#qa60)
- [61｜依對應版本的程序恢復管理員存取。 這個會怎麼做](#qa61)

## 合併後歷史問答

<a id="qa1"></a>

### 1. 我有設定 網頁版的BA連線 我想看 我開了允許多少人連線

來源：[S058-Q001](<../../原始問答/S058_查看BA網頁版連線上限.md#q1>)；2026-08-07

**問題**

# Files mentioned by the user:

## codex-clipboard-0684a181-a066-41da-84e5-347160fdb0ac.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-0684a181-a066-41da-84e5-347160fdb0ac.png

## My request for Codex:
 我有設定 網頁版的BA連線 我想看 我開了允許多少人連線

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你目前在 SLD 的「Security」頁面，這裡看不到 Browser Access 開放人數。

請這樣查看：

1. 點上方的 **Services**
2. 找到服務類型為 **Browser Access**
3. 選取該服務後按 **Edit**
4. 查看 **Maximum Processes**

`Maximum Processes` 就是這台 Browser Access Server 設定的「最大同時連線人數」。例如設為 `20`，最多可同時讓 20 位使用者透過瀏覽器連線；SAP 的單台服務設定上限為 100 人。[SAP 官方設定說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_SQL/f6fb230cc90949d8b66586a39189992b/dd10de376348435aa638e10f7158ccfe.html)

另外要注意，實際可登入人數還會受 SAP B1 授權數限制。可在 SAP Business One 主程式查看：

**管理 → 授權 → 授權管理 → Components（元件）**

裡面會顯示各種授權的：

- Total：購買總數
- Available：目前可用數
- 已指派／使用數

因此，真正可同時使用的人數，通常取決於「Maximum Processes」與可用使用者授權兩者中較小的數字。[SAP 授權管理說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/4506da50d8696c30e10000000a114a6b.html)

<a id="qa2"></a>

### 2. '

來源：[S058-Q002](<../../原始問答/S058_查看BA網頁版連線上限.md#q2>)；2026-08-07

**問題**

# Files mentioned by the user:

## codex-clipboard-59155dac-27f0-400f-a3a5-bc6f955856a4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-59155dac-27f0-400f-a3a5-bc6f955856a4.png

## My request for Codex:
'

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

從畫面可以看到，你的 Browser Access 設定為：

- **Maximum Processes：200** → 設定最多同時 200 個網頁版連線程序
- **Initial Processes：50** → 服務啟動時先準備 50 個程序
- **Idle Processes：20** → 維持 20 個待命程序
- **Session Timeout：30 分鐘** → 閒置 30 分鐘後逾時

所以你目前「設定上」允許最多 **200 人同時連線**。

但實際能登入的人數仍受下列條件限制：

1. SAP B1 實際購買及分配的使用者授權數。
2. Browser Access 伺服器的 CPU、記憶體與效能。
3. 你的 SAP B1 版本是否真的支援超過 100 個並行連線；部分官方版本文件標示單台上限為 100。

因此畫面雖然填了 200，不一定代表實際能穩定登入 200 人。若要確認真正授權人數，請到 SAP B1：

**管理 → 授權 → 授權管理 → Components**

查看各授權的 **Total** 與 **Available**。

<a id="qa3"></a>

### 3. 這個嗎

來源：[S058-Q003](<../../原始問答/S058_查看BA網頁版連線上限.md#q3>)；2026-08-07

**問題**

這個嗎

**歷史回答**

對，就是這個畫面。

你目前設定的網頁版 BA 最大同時連線數是：

**Maximum Processes = 200 人**

其他欄位：

- `Initial Processes = 50`：啟動時預先開啟 50 個程序
- `Idle Processes = 20`：保持 20 個程序待命
- `Session Timeout = 30`：閒置 30 分鐘後逾時

但實際可登入人數仍不能超過你擁有並分配的 SAP B1 使用者授權數。

<a id="qa4"></a>

### 4. 圖片／附件提問

來源：[S058-Q004](<../../原始問答/S058_查看BA網頁版連線上限.md#q4>)；2026-08-07

**問題**

# Files mentioned by the user:

## codex-clipboard-43151a3e-fd9f-445c-b620-47197c234058.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-43151a3e-fd9f-445c-b620-47197c234058.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你現在在 **Global Settings** 頁面。

請點上方的 **Services**：

1. 點 **Services**
2. 找到 **Browser Access Service**
3. 選取該列
4. 按 **Edit**

打開後就會看到先前的 **Maximum Processes = 200**，這就是設定的最大連線程序數。你目前這個頁面不會顯示連線人數。

<a id="qa5"></a>

### 5. 圖片／附件提問

來源：[S058-Q005](<../../原始問答/S058_查看BA網頁版連線上限.md#q5>)；2026-08-07

**問題**

# Files mentioned by the user:

## codex-clipboard-19c9d49b-58c3-46bc-bbb3-f18d15c6b44c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-19c9d49b-58c3-46bc-bbb3-f18d15c6b44c.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

請點上方選單的 **Services**，位置在：

**Components 右邊、Security 左邊**

進去後：

1. 點選 **Browser Access Service** 那一列。
2. 按 **Edit**。
3. 查看 **Maximum Processes**。

你上一張畫面已顯示為 **200**，表示設定上限為 200 個同時連線程序。

<a id="qa6"></a>

### 6. 當我在sapb1 用網頁版登入後 我可以在server 端 的 SSMS 我可以看到連線的人嗎

來源：[S161-Q001](<../../原始問答/S161_確認 SAP B1 網頁登入連線.md#q1>)；2026-09-10

**問題**

當我在sapb1 用網頁版登入後

我可以在server 端 的 SSMS 我可以看到連線的人嗎

**歷史回答**

**可以在 SSMS 看到資料庫連線，但不一定能直接看出是哪個 SAP B1 使用者。**

SSMS 顯示的是 **SQL Server 的登入帳號與連線來源**；如果網頁版透過後端服務連接資料庫，看到的可能是服務帳號及伺服器，而不是你登入 SAP 的帳號。因此，**SQL 連線數不能直接當作 SAP 在線人數**。

你可以在 SSMS 開啟「新增查詢」，執行：

```sql
SELECT
    s.session_id AS [連線編號],
    s.login_name AS [SQL登入帳號],
    s.host_name AS [來源主機],
    s.program_name AS [連線程式],
    c.client_net_address AS [來源IP],
    DB_NAME(s.database_id) AS [目前資料庫],
    s.login_time AS [連線時間],
    s.status AS [狀態]
FROM sys.dm_exec_sessions AS s
LEFT JOIN sys.dm_exec_connections AS c
    ON s.session_id = c.session_id
WHERE s.is_user_process = 1
ORDER BY s.login_time DESC;
```

這裡的 `SQL登入帳號` **不等於 SAP 使用者帳號**。查看全部連線需要適當的伺服器權限；可以請 DBA 執行。[Microsoft 說明](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=sql-server-ver17)

如果目的是查「目前哪些 SAP 帳號登入」，可由 SAP B1 超級使用者查看 **Connection Management（連線管理）**，它提供目前公司資料庫的用戶端連線資訊；網頁連線的實際呈現仍需依你的版本確認。[SAP 說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/911304e1a70941acb19562a04a7e277f/d5f0c4538bc2792ae10000000a423f68.html)

你說的網頁版是 **SAP B1 Web Client**，還是瀏覽器裡顯示完整傳統 SAP 畫面的 **Browser Access**？這會影響判斷方式。

<a id="qa7"></a>

### 7. 所以我ssms 沒辦法看到我的sapb1 某一個資料庫有連線資訊嗎

來源：[S161-Q002](<../../原始問答/S161_確認 SAP B1 網頁登入連線.md#q2>)；2026-09-10

**問題**

所以我ssms 沒辦法看到我的sapb1 某一個資料庫有連線資訊嗎

**歷史回答**

**可以，你可以在 SSMS 查看某一個 SAP B1 資料庫目前的連線資訊。** 我前面指的是：能看到資料庫連線，但不一定能辨識 SAP 使用者姓名或帳號。

把下面的 `你的公司資料庫名稱` 改成實際名稱，再執行：

```sql
SELECT
    s.session_id AS [連線編號],
    DB_NAME(s.database_id) AS [目前資料庫],
    s.login_name AS [SQL登入帳號],
    s.host_name AS [來源主機],
    c.client_net_address AS [來源IP],
    s.program_name AS [連線程式],
    s.login_time AS [連線時間],
    s.status AS [狀態]
FROM sys.dm_exec_sessions AS s
LEFT JOIN sys.dm_exec_connections AS c
    ON s.session_id = c.session_id
WHERE s.is_user_process = 1
  AND s.database_id = DB_ID(N'你的公司資料庫名稱')
ORDER BY s.login_time DESC;
```

注意這查的是 **「目前資料庫」為該公司資料庫的 SQL 工作階段**，不是完整的 SAP 登入名單，也不包含歷史連線。網頁後端的共用連線可能無法逐一對應 SAP 使用者；`sleeping` 則表示連線目前閒置，並非已斷線。[Microsoft 說明](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=sql-server-ver17)

若你只看到自己的 SSMS 連線，可能是權限不足，需要請 DBA 用有權限的帳號查看。

<a id="qa8"></a>

### 8. 我要怎麼看我自己的權限

來源：[S161-Q003](<../../原始問答/S161_確認 SAP B1 網頁登入連線.md#q3>)；2026-09-10

**問題**

我要怎麼看我自己的權限

**歷史回答**

在 **SSMS → 新增查詢**，執行下面這段，就能查看你目前帳號是否有權限查全部連線。這只是查詢，不會修改權限。

```sql
SELECT
    SUSER_SNAME() AS [目前SQL帳號],
    SERVERPROPERTY('ProductVersion') AS [SQL版本],
    IS_SRVROLEMEMBER('sysadmin') AS [是否為系統管理員],
    HAS_PERMS_BY_NAME(NULL, 'SERVER', 'VIEW SERVER STATE')
        AS [查看伺服器狀態權限],
    HAS_PERMS_BY_NAME(NULL, 'SERVER', 'VIEW SERVER PERFORMANCE STATE')
        AS [查看伺服器效能狀態權限];
```

結果的意思：

- **1**：有。
- **0**：沒有。
- **NULL**：無法判定，或這個版本不支援該權限名稱。

能不能查看所有人的 SQL 連線，要看 **SQL Server 版本**：

| SQL Server 版本 | 需要的權限 |
|---|---|
| 2019 及以前 | `VIEW SERVER STATE` |
| 2022 及以後 | `VIEW SERVER PERFORMANCE STATE` |

若「是否為系統管理員」是 **1**，就具備查看權限。這裡指的是伺服器版本，不是 SSMS 版本。[Microsoft 說明](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=sql-server-ver17)

若想列出自己目前的伺服器層級有效權限，也可以執行：

```sql
SELECT permission_name AS [權限名稱]
FROM sys.fn_my_permissions(NULL, 'SERVER')
ORDER BY permission_name;
```

你可以把第一段的結果貼上來，帳號名稱可遮住，我幫你判斷。

<a id="qa9"></a>

### 9. 可以幫我健檢我的電腦嗎

來源：[S174-Q001](<../../原始問答/S174_檢查電腦健康狀況.md#q1>)；2026-09-16

**問題**

可以幫我健檢我的電腦嗎

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa10"></a>

### 10. 我覺得有點慢

來源：[S174-Q002](<../../原始問答/S174_檢查電腦健康狀況.md#q2>)；2026-09-16

**問題**

我覺得有點慢

**歷史回答**

初步健檢完成，**目前沒有看到 CPU 或磁碟滿載，但發現 SAP Business One 連續無回應，這是最值得追查的線索。**

| 檢查項目 | 結果 |
|---|---|
| CPU | 取樣使用率約 7～10%，未滿載 |
| 記憶體 | 可用總量 14.7 GB，剩餘約 4.7 GB |
| SSD | 系統回報健康，剩餘約 801 GB |
| 磁碟負載 | 取樣約 0～1%，沒有排隊堆積 |
| 防毒 | 有登錄 Trend Micro Apex One；Defender 未啟用 |

**今天 10:26、10:27、10:29，Windows 三次記錄 SAP Business One 停止回應。** 這能確認程式有卡住，但還無法判定是 SAP 本身、連線或伺服器造成。

另外有 Google 更新服務啟動逾時，以及重開機時的 BitLocker 錯誤，尚無證據顯示與這次卡頓有關。這輪沒有更改設定或刪除檔案。

你感覺慢的是 **SAP 操作時，還是連桌面、切換視窗和瀏覽器也一起慢？**

<a id="qa11"></a>

### 11. sap

來源：[S174-Q003](<../../原始問答/S174_檢查電腦健康狀況.md#q3>)；2026-09-16

**問題**

sap

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa12"></a>

### 12. 圖片／附件提問

來源：[S174-Q004](<../../原始問答/S174_檢查電腦健康狀況.md#q4>)；2026-09-16

**問題**

<send_user_message_question_reply>
[{"questionItemId":"[\"request_user_input_async\",\"call_ErTWmWdCU8qb10QDYv6joNkq\",0]","question":"SAP 主要在哪個步驟卡住？","answer":"啟動或登入"}]
</send_user_message_question_reply>

**歷史回答**

**已確認 SAP 在登入過程有異常，值得優先查登入流程。**

- 今天已記錄 **4 次 SAP 無回應並關閉**，最新是 10:59。
- SAP 日誌出現「公司登入失敗」「介面初始化失敗」，以及重複的授權檢查錯誤。
- 登入服務的兩個連接埠目前都能連通；這只證明基本連線可用，還不能確認後端服務正常。
- 先前取樣的 CPU、記憶體和磁碟沒有滿載。

目前還不能確定是本機 SAP、授權服務或公司資料庫造成，也不能直接認定授權不足。

**你按登入後，是一直轉圈／沒有回應，還是會跳出錯誤視窗？** 日誌顯示 10:56 左右曾出現「系統訊息」；如果記得內容，請貼給我，能進一步縮小原因。

<a id="qa13"></a>

### 13. 是一直轉圈／沒有回應

來源：[S174-Q005](<../../原始問答/S174_檢查電腦健康狀況.md#q5>)；2026-09-16

**問題**

是一直轉圈／沒有回應

**歷史回答**

**找到一個明確的登入阻擋原因：SAP 判定你的帳號已在其他兩個站台連線。**

今天 10:57 的日誌原文：
> 您無法從此站台登入，原因為您已從其他兩個站台連線；若要從此站台登入，請先從其中一個站台登出。[訊息 60070-25]

先處理這個問題：

1. 若其他電腦或遠端桌面有登入同一個 SAP 帳號，儲存工作後，從 **SAP 內登出**。
2. 若沒有其他登入，請 SAP 管理員確認並清除你帳號的殘留連線。
3. 再開啟一個 SAP 視窗測試登入。

這能解釋其中一次登入失敗；另外幾次紀錄已進到主功能表才卡住，所以**還不能認定所有轉圈都是同一原因**。

你有在其他電腦或遠端桌面，同時登入這個 SAP 帳號嗎？

<a id="qa14"></a>

### 14. 我 SAPB1網頁版登入，會踢掉另一個 這個可以卡控嗎

來源：[S221-Q001](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q1>)；2026-10-05

**問題**

我 SAPB1網頁版登入，會踢掉另一個 這個可以卡控嗎

**歷史回答**

你是想要 **「帳號已有人登入時，擋住後登入的人，不要把原本的人踢掉」** 嗎？

這要先確認你使用的「網頁版」是哪一種，才能判斷有沒有原生設定可以卡控：

- **Web Client**：介面是網頁磁磚式。
- **Browser Access**：用瀏覽器開啟，但畫面像傳統 SAP B1 桌面版。

兩者不能直接套用相同的登入限制。SAP 官方也說明，Web Client 可以和桌面版同時登入，所以不能單憑「網頁登入會踢人」就認定是正常限制。[SAP 官方說明](https://help.sap.com/doc/53c252d19f014edf88cd380c798514fe/10.0/en-US/WebClient_UserGuide.pdf)

你補充這三點，我才能確認卡控方式：

1. 被踢掉的是不是使用**同一個 SAP 帳號**？
2. 被踢掉的是**另一個網頁，還是桌面版**？
3. 你希望**擋住後登入者**，還是**允許兩邊同時使用**？

如果方便，也可以貼被踢掉時的訊息或畫面。

<a id="qa15"></a>

### 15. 網頁版跟往葉板

來源：[S221-Q002](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q2>)；2026-10-05

**問題**

網頁版跟往葉板

**歷史回答**

了解，你是指 **網頁版登入後，會把另一個網頁版踢掉**。

再確認一下：兩邊是用**同一個 SAP 帳號**嗎？你想要的卡控是「**保留先登入的人，擋住後登入的人**」對嗎？

<a id="qa16"></a>

### 16. 網頁版登入後，會把另一個網頁版踢掉

來源：[S221-Q003](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q3>)；2026-10-05

**問題**

**網頁版登入後，會把另一個網頁版踢掉**

**歷史回答**

了解，確定是**網頁版互踢**。但還不能確定能否設定成「擋後登入的人、保留原本登入的人」。

關鍵是：**兩邊用的是同一個 SAP 帳號，還是不同帳號？**

<a id="qa17"></a>

### 17. 我想問一下 DTW 可以匯入匯率嗎

來源：[S221-Q004](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q4>)；2026-10-05

**問題**

我想問一下 DTW 可以匯入匯率嗎

**歷史回答**

**可以，SAP B1 的 DTW 有透過 `BoBridge／oBridge` 匯入匯率的方式**，但你安裝的 DTW 版本是否提供對應物件與範本，需要確認。[SAP Community 匯入討論](https://community.sap.com/t5/enterprise-resource-planning-q-a/importing-the-exchange-rates-table-from-the-internet/qaq-p/3934188)

匯率資料主要包含：

| 日期 | 幣別 | 匯率 |
|---|---|---|
| 2026/10/05 | USD | 32.50 |
| 2026/10/05 | EUR | 35.80 |

這是資料示意，**不是可以直接上傳的 DTW 範本**。實際欄名、日期格式，以及匯率採直接或間接報價，要依範本與系統設定確認。

若 DTW 沒有對應項目，也可以用官方 DI API 的 `SBObob.SetCurrencyRate` 批次寫入。[SAP 官方文件](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~SBObob~SetCurrencyRate.html)

**你目前 SAP B1 是 9.3 還是 10.0？** 如果能貼 DTW 的物件選擇畫面，我可以幫你確認要選哪個。

<a id="qa18"></a>

### 18. 10.0

來源：[S221-Q005](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q5>)；2026-10-05

**問題**

10.0

**歷史回答**

了解，是 **SAP B1 10.0**。我先修正前面說法：**`BoBridge` 匯入匯率是舊版 SAP Community 的做法，目前查到的資料不足以保證你的 DTW 10.0 仍提供這個項目**，所以不能直接給你確定的操作路徑。

你可以先在 DTW 的 **Templates → Customize Template（自訂範本）** 查看是否有 `BoBridge／SBObob`，以及匯率相關項目；貼這個畫面，我就能幫你確認。

若沒有，10.0 官方明確支援用 **DI API `SBObob.SetCurrencyRate`** 批次寫入日期、幣別、匯率，也能指定是否更新既有匯率，但這需要小程式處理，不是直接上傳 DTW。[SAP 10.0 官方文件](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~SBObob~SetCurrencyRate.html)

<a id="qa19"></a>

### 19. 圖片／附件提問

來源：[S221-Q006](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q6>)；2026-10-05

**問題**

# Files mentioned by the user:

## codex-clipboard-e2937639-02bf-4902-b5b6-4d3136ab686f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e2937639-02bf-4902-b5b6-4d3136ab686f.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

有，你這個 DTW **確實有 `oBridge`**，就在畫面下方，`oRecordsetEx` 的下一列。

請選取 **`oBridge` → Next**，再貼下一個畫面，我幫你確認匯率的來源檔與欄位對應。

目前這張圖確認了物件存在，還需要看下一步，才能確定匯入格式。

<a id="qa20"></a>

### 20. 但我找不到

來源：[S221-Q007](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q7>)；2026-10-05

**問題**

# Files mentioned by the user:

## codex-clipboard-d6a89a46-7659-48ef-95cb-2d5d7eae9ba4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d6a89a46-7659-48ef-95cb-2d5d7eae9ba4.png
Image attachment: true



## My request:
但我找不到

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你找不到的是 **`oBridge` 的範本檔案**。這張圖的 `Others` 資料夾裡確實沒有；前一張看到的是 DTW 的「可選物件」，不代表一定附有同名範本。剛才我沒說清楚，抱歉。

先不用在資料夾繼續找，請回到 DTW：

1. 選取 **`oBridge`**。
2. 按 **Next**。
3. 貼出下一頁的「來源檔案選擇」畫面。

我們先確認它要求哪個資料結構，再準備匯率檔，避免拿錯範本。

<a id="qa21"></a>

### 21. 圖片／附件提問

來源：[S221-Q008](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q8>)；2026-10-05

**問題**

# Files mentioned by the user:

## codex-clipboard-7dd53f61-74a9-4e8d-8cf5-203e24d7f7b6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7dd53f61-74a9-4e8d-8cf5-203e24d7f7b6.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這頁確認了：DTW 要讀取的是 **`BoBridge` 來源檔案**，目前格式選的是 **Tab 分隔的 `.txt`**。右邊的 `…` 是選擇檔案，不是產生範本。

畫面上也提示，要先從 **Customize Template** 產生範本。下一步：

1. 按 **Cancel** 離開這次匯入。
2. 上方選 **Templates → Customize Template**。
3. 找到 **`oBridge／BoBridge`**，查看是否能展開並產生範本。

**請貼 Customize Template 中選到這個物件的畫面**，就能確認實際欄位。這張圖還沒顯示欄位，因此先不要直接拿前面示意的三欄資料匯入。

<a id="qa22"></a>

### 22. 甚麼意思

來源：[S221-Q009](<../../原始問答/S221_查找 SAP B1 網頁版登入互斥設定.md#q9>)；2026-10-05

**問題**

甚麼意思

**歷史回答**

意思是：**你已經選對 `BoBridge`，但還缺一份符合 DTW 格式的匯率檔案。** 這一頁只能選檔案，不能建立範本。

抱歉，前面讓你來回操作。現在先做這一步就好：

**按「Cancel」→ 上方「Templates」→「Customize Template」**。

這是要找出你這版 DTW 要求的欄位格式，才知道 Excel 該怎麼填。

<a id="qa23"></a>

### 23. 我在想 我有可能限制 SAPB1 帳號的登入嗎 就是一次只能一個

來源：[S228-Q001](<../../原始問答/S228_限制 SAP B1 單一登入.md#q1>)；2026-10-07

**問題**

我在想 我有可能限制 SAPB1 帳號的登入嗎  就是一次只能一個

**歷史回答**

可以限制，但要先分清楚你說的「一次只能一個」是哪一種：

- **同一個 SAP B1 帳號，不能在兩台電腦同時使用**：B1 本身已有相關機制。依 SAP 文件，同帳號從另一個 Windows 工作階段登入時，原本工作階段的 B1 會被鎖住。
- **同一台電腦也只能開一個 B1 視窗**：原生機制允許同一個 Windows 工作階段用同帳號開多個 B1，因此不等於嚴格的「只能有一個登入」。
- **已經有人登入時，直接拒絕第二個人，保留第一個人的操作**：這和上述原生行為不同，需要依你們的版本、登入方式確認能否實現。[SAP 登入限制說明](https://help.sap.com/doc/14c6d92982fd4a9dbf7df66b5108433b/9.3/en-US/LicenseGuide.pdf)

你想要的是第三種「**有人在用，第二個就不能登入**」嗎？另外，你們是 **B1 9.3 還是 10.0**，用電腦版程式還是網頁版？

<a id="qa24"></a>

### 24. 已經有人登入時，直接拒絕第二個人，保留第一個人的操作：這和上述原生行為不同，需要依你們的版本、登入方式確認能否實現。[SAP 登入限制說明](〔連結〕 10.0 會希望是網頁版卡住

來源：[S228-Q002](<../../原始問答/S228_限制 SAP B1 單一登入.md#q2>)；2026-10-07

**問題**

**已經有人登入時，直接拒絕第二個人，保留第一個人的操作**：這和上述原生行為不同，需要依你們的版本、登入方式確認能否實現。[SAP 登入限制說明](https://help.sap.com/doc/14c6d92982fd4a9dbf7df66b5108433b/9.3/en-US/LicenseGuide.pdf)   

10.0 會希望是網頁版卡住

**歷史回答**

了解，你要的是：**A 已登入 B1，B 再用同帳號從網頁登入時，直接顯示「帳號使用中」，而 A 繼續操作。**

**目前我查到的 SAP B1 10.0 官方文件，沒有確認能直接開啟這種限制的標準設定**，所以還不能跟你說勾選哪個選項就能做到。

另外，B1 的「網頁版」有兩種，處理方式不同：

| 類型 | 畫面特徵 |
|---|---|
| **Web Client** | 網頁式首頁，有磁磚、卡片等介面 |
| **Browser Access** | 在瀏覽器裡操作，畫面看起來和傳統 B1 電腦版很像 |

官方文件中的 Browser Access「Maximum Processes」限制的是**整台服務的連線容量**，不是每個帳號只能登入一次，不能用它達到你的需求。[SAP 官方文件](https://help.sap.com/doc/37bc0ffc0cdf48d08923360b4b43881b/10.0/en-US/How_to_Deploy_SAP_Business_One_with_Browser_Access.pdf)

如果原生功能不支援，就需要評估在登入入口加上帳號使用狀態檢查，而且必須能掌握既有登入、登出與逾時，才不會誤擋人。

**你們的網頁版是「磁磚卡片首頁」，還是「和電腦版一樣的畫面」？另外，先登入的 A 也用網頁，還是可能用電腦版？**

<a id="qa25"></a>

### 25. 也是網頁

來源：[S228-Q003](<../../原始問答/S228_限制 SAP B1 單一登入.md#q3>)；2026-10-07

**問題**

也是網頁

**歷史回答**

了解，**兩個人都是從網頁登入：第一個繼續使用，第二個同帳號登入就被拒絕。**

還需要確認你們是哪種網頁介面，才能判斷限制要加在哪裡：

- **磁磚／卡片式首頁** → Web Client。
- **跟傳統 B1 電腦版幾乎一樣的畫面** → Browser Access。

你們是哪一種？不確定的話，可以貼首頁截圖，遮住公司及帳號資訊即可。

<a id="qa26"></a>

### 26. 圖片／附件提問

來源：[S228-Q004](<../../原始問答/S228_限制 SAP B1 單一登入.md#q4>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-2a8a3edb-3d59-432f-9474-8bf8ea465b60.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2a8a3edb-3d59-432f-9474-8bf8ea465b60.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

從截圖看，這是**傳統 B1 用戶端介面**；配合你說是透過網頁操作，判斷應該是 **Browser Access**。

對你要的「**第一個人繼續操作，第二個同帳號登入直接被擋住**」，目前查到的官方文件**沒有提供可直接啟用的標準設定**。Browser Access 的連線數上限是限制整台服務，無法拿來限制單一帳號。[SAP 官方文件](https://help.sap.com/doc/37bc0ffc0cdf48d08923360b4b43881b/10.0/en-US/How_to_Deploy_SAP_Business_One_with_Browser_Access.pdf)

建議先請維護廠商依你們的完整版本確認支援方式，可以直接把這段交給他們：

> 我們使用 SAP Business One 10.0 Browser Access，希望同一帳號已有有效連線時，拒絕第二次登入，且不鎖定、不登出第一個連線。請確認目前 FP／PL 是否有 SAP 支援的設定或 Note；若需客製，必須在第二次 B1 登入生效前攔截，並處理異常斷線、逾時及管理員解除鎖定。

**下一步請看「輔助說明 → 關於 SAP Business One」的完整版本號（包含 FP／PL）**。有這個資訊，才能進一步確認，避免直接修改登入流程卻影響原本使用者。

<a id="qa27"></a>

### 27. 圖片／附件提問

來源：[S228-Q005](<../../原始問答/S228_限制 SAP B1 單一登入.md#q5>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-c258d15a-cbc5-4a8a-89a8-ec63096c0cea.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-c258d15a-cbc5-4a8a-89a8-ec63096c0cea.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

確認，你們的版本是 **SAP Business One 10.0 SP 2505（10.00.291）Security HOTFIX1，64 位元**。

我查到 Browser Access 的明確規則，也修正前面說得不夠完整的地方：**同帳號從另一個瀏覽器或另一台電腦登入時，原生會提示已有連線，讓第二個人選擇：**

- **繼續登入**：終止第一個人的連線。
- **取消登入**：保留第一個人的連線。

這是 SAP B1 10.0 官方授權指南第 7 頁明載的行為。[官方文件](https://help.sap.com/doc/14c6d92982fd4a9dbf7df66b5108433b/10.0/en-US/LicenseGuide.pdf#page=7)

所以，你的需求其實是：**禁止第二個人選「繼續登入／取代原連線」，強制拒絕登入。** 我目前沒有找到 SP 2505 可直接設定這項行為的公開官方說明，不能保證後台有開關可用。

可以請維護廠商針對這個明確問題確認：

> B1 10.0 SP 2505 Security HOTFIX1 的 Browser Access，是否有 SAP 支援的設定或 Note，能停用重複登入時的「終止既有連線並繼續登入」，改為一律拒絕新連線？若無，請評估受支援的實作方式。

**你們現在第二次登入時，有出現「已有連線，要不要繼續」的提示嗎？** 這可以確認實際行為是否符合文件。

<a id="qa28"></a>

### 28. 所以有辦法做到嗎

來源：[S228-Q006](<../../原始問答/S228_限制 SAP B1 單一登入.md#q6>)；2026-10-07

**問題**

所以有辦法做到嗎

**歷史回答**

**有機會透過客製做到，但目前不能確認用 B1 內建設定就能達成。**

你們的 Browser Access 原生會讓第二個登入者選擇「取代原連線」。要做到你要的**直接拒絕第二個、保留第一個**，需要：

- 先請維護廠商確認有沒有適用版本的 SAP 官方設定或修正。
- 如果沒有，就評估在登入入口加入單一連線管制，確認能在影響第一個連線之前攔截。

所以精確答案是：**不是已確認有現成開關；客製是可評估的方向，但還需要驗證你們環境的可行性。** 我前面沒有查到足以保證能做到的依據，不應直接跟你說一定可以。

<a id="qa29"></a>

### 29. 客製

來源：[S228-Q007](<../../原始問答/S228_限制 SAP B1 單一登入.md#q7>)；2026-10-07

**問題**

客製

**歷史回答**

可以，**那就以客製「同帳號單一登入管制」為方向**。關鍵是要在第二次登入送進 B1 **之前攔截**，才能保住第一個人的連線。

建議的流程是：

1. 第一個人登入成功，伺服器記錄該帳號使用中。
2. 第二個人用同帳號登入，直接顯示「此帳號使用中，請稍後再試」，不允許取代。
3. 第一個人登出後，釋放登入資格。
4. 斷線或直接關閉瀏覽器時，透過連線狀態與逾時機制處理，並提供管理員解除功能。

**不能只在網頁上隱藏「繼續登入」按鈕**；必須由伺服器強制檢查，而且所有 Browser Access 登入都要經過管制入口。

目前還需要驗證的是：你們這版 Browser Access 能否可靠地整合登入與連線狀態。確認後才能決定用登入閘道或其他整合方式，現在不能直接保證套一段程式就能用。

你是想**自己開發、由我協助設計實作**，還是**整理規格交給 SAP 維護廠商做**？

<a id="qa30"></a>

### 30. 協助設計實作

來源：[S228-Q008](<../../原始問答/S228_限制 SAP B1 單一登入.md#q8>)；2026-10-07

**問題**

協助設計實作

**歷史回答**

可以，我協助你設計和寫程式。**第一步先確認登入整合點，再做測試版**；目前還沒連到你們的伺服器，不能直接產出可部署的 SAP 攔截程式。

我建議先採用這個架構，實際是否適用要看你們登入流程：

```text
使用者瀏覽器
    ↓
登入管制閘道 ← 檢查帳號是否已有連線
    ↓
SAP Browser Access
```

SAP 有文件說明反向代理部署，但**反向代理本身沒有提供我們要的單帳號管制**，這部分需要自行實作與驗證。[SAP 部署說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_SQL/f6fb230cc90949d8b66586a39189992b/4eaeaad14eb841f2bda88107c45d6f70.html)

管制程式會依以下規則設計：

| 情況 | 處理方式 |
|---|---|
| 帳號尚未使用 | 驗證身分後取得登入資格，再放行 |
| 帳號已有連線 | 拒絕新登入，原連線繼續操作 |
| 兩人同時登入 | 透過不可分割的鎖定操作，只放行一人 |
| 登入失敗 | 撤銷這次預留的登入資格 |
| 正常登出 | 確認 SAP 連線結束後釋放 |
| 瀏覽器關閉或斷線 | 核對後端連線狀態；不能只因網頁心跳中斷就放行第二人 |

另外，原本 Browser Access 入口必須限制為只能經過閘道存取，否則可以繞過管制。**也不能用額外登入 B1 一次的方式驗證密碼，因為那可能先觸發取代原連線。**

請先提供這三項，我就能決定程式要接在哪裡：

1. **你可以遠端登入並管理那台 Windows 伺服器嗎？**
2. **目前是直接輸入 B1 公司、帳號、密碼，還是先經過 Microsoft／其他登入頁？**
3. **登入網址的格式**，主機名稱可遮掉，例如 `https://主機:8100/dispatcher`。不要提供密碼或網址內的權杖。

<a id="qa31"></a>

### 31. 1.可以 2.會有登入頁後會有這個畫面 3.〔連結〕

來源：[S228-Q009](<../../原始問答/S228_限制 SAP B1 單一登入.md#q9>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-33e8ecc5-0263-444a-9deb-c4bdeffd2356.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-33e8ecc5-0263-444a-9deb-c4bdeffd2356.png
Image attachment: true



## My request:
1.可以
2.會有登入頁後會有這個畫面
3.https://主機:8100/dispatcher

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張圖確認是 **Browser Access 的 B1 帳密登入頁**，入口為 `/dispatcher`。你也有伺服器管理權限，可以往登入閘道的方向實作。

**現在需要先找出真正的登入請求。** `/dispatcher` 是入口頁，不能直接假設它就是接收帳密的 API；攔錯位置就無法保護第一個人的連線。

請先在瀏覽器做這一步：

1. 開啟這個登入頁，按 **F12 → Network（網路）**。
2. 選 **Fetch/XHR**，清空現有紀錄。
3. 用**目前沒有人使用的測試帳號**登入一次。
4. 找出按下 **Log In** 時新增的請求，提供以下資訊：

| 資訊 | 範例 |
|---|---|
| Request URL | `https://主機:8100/某個路徑`，主機可遮掉 |
| Request Method | `POST` |
| Status Code | `200`／`302` 等 |
| Request Content-Type | 例如 `application/json` |
| Payload 的欄位名稱 | **只提供名稱，所有值遮掉** |

**不要貼密碼、Cookie、Authorization，也先不要匯出完整 HAR**，它可能包含登入憑證。若 Fetch/XHR 沒有請求，就切到 **All** 看。

拿到這些資訊後，我才能寫對應的攔截程式。第一版會先在獨立測試入口驗證「A 登入後，B 被拒絕、A 仍能操作」，再處理登出、斷線與重新啟動後的狀態恢復。

另外，截圖是 `manager`；測試請優先用一般測試帳號，之後再單獨驗證 `manager` 的行為。

<a id="qa32"></a>

### 32. 圖片／附件提問

來源：[S228-Q010](<../../原始問答/S228_限制 SAP B1 單一登入.md#q10>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-b4f037d5-9529-4a7d-92c5-dc006ec464f5.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b4f037d5-9529-4a7d-92c5-dc006ec464f5.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

你已經開對了，目前在「歡迎」頁。

1. 點上方的 **「網路」**（在「來源」右邊）。
2. 勾選 **「保留記錄／Preserve log」**，避免登入跳頁後紀錄消失。
3. 保持開發者工具開著，回到 SAP 登入頁，用沒有人使用的測試帳號登入一次。
4. **截圖「網路」的請求清單給我**，我再指給你看要點哪一筆。

先拍清單就好，不用展開 Cookie、密碼或其他登入資料。

<a id="qa33"></a>

### 33. 這樣嗎

來源：[S228-Q011](<../../原始問答/S228_限制 SAP B1 單一登入.md#q11>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-fb4786fb-7b72-4a21-abf5-7dad85a0c00a.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-fb4786fb-7b72-4a21-abf5-7dad85a0c00a.png
Image attachment: true



## My request:
這樣嗎

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，就是這個畫面。現在看到的主要是**登入頁載入時的請求**，還沒看到能確認是提交登入的那一筆。

接著：

1. 勾選上方 **Keep log**（你目前還沒勾）。
2. 點 **Fetch/XHR**，縮小清單範圍。
3. 保持這個視窗開著，用沒有人使用的測試帳號按 **Log In**。
4. 把登入後的請求清單截圖給我；如果清單沒有新增，就切回「全部」再拍。

網址裡若有 `saml2Token` 等權杖，請保持截斷或遮住，不要展開完整值。

<a id="qa34"></a>

### 34. 圖片／附件提問

來源：[S228-Q012](<../../原始問答/S228_限制 SAP B1 單一登入.md#q12>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-7ecbcbd7-d9ef-427a-9a32-27c5262281be.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7ecbcbd7-d9ef-427a-9a32-27c5262281be.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

找到了，**`LogonBySBOUser` 是目前最值得確認的登入請求**。它回傳 `200`，但還要看內容才能判斷登入結果。

請點那一列，接著提供：

1. **Headers／標頭 → General／一般**：Request URL、Request Method、Status Code。網址主機可遮掉，查詢參數的值也請遮掉。
2. **Payload／承載**：只留欄位名稱，**所有欄位值遮住**。
3. **Preview／預覽**：看回應有哪些欄位；密碼、Token、Session ID 等值請遮住。

先不要複製完整請求或 Cookie。這些資訊能幫我們確認它是否直接建立 B1 連線，以及應該在哪個步驟攔截第二次登入。

<a id="qa35"></a>

### 35. 圖片／附件提問

來源：[S228-Q013](<../../原始問答/S228_限制 SAP B1 單一登入.md#q13>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-f28ea813-774c-4286-875c-9f8d745ec2e8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f28ea813-774c-4286-875c-9f8d745ec2e8.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張圖確認了登入頁會送出：

```text
POST https://主機:40000/sld/sld.svc/LogonBySBOUser
```

**它送到的是 40000 的 SLD 服務，和 8100 的 Browser Access 入口不同。** 所以不能只在 8100 加一道檢查，就假設已經管住整個登入流程。

目前 `200 OK` 只代表 HTTP 請求成功，還無法確定這一步是驗證帳密、發出登入憑證，還是已建立 B1 連線。

接下來請在**同一筆 `LogonBySBOUser`** 點：

- **「承載／Payload」**：提供欄位名稱，所有值遮住。
- **「預覽／Preview」**：提供回應結構；Token、Session ID、其他憑證值遮住。

先不用重新登入。有這兩項，就能繼續判斷攔截點，避免把 SLD 身分驗證誤當成 B1 連線建立。

<a id="qa36"></a>

### 36. 圖片／附件提問

來源：[S228-Q014](<../../原始問答/S228_限制 SAP B1 單一登入.md#q14>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-f840bb0f-9c35-453f-a3de-08fa6777fc7d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f840bb0f-9c35-453f-a3de-08fa6777fc7d.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

看到了，這筆請求有四個欄位：

```text
Account
CompanyDB
DBInstance
EncodedPassword
```

這表示閘道有機會辨識「哪個資料庫執行個體、哪間公司、哪個帳號」正在登入。但**還不能確定這筆請求就是建立 B1 連線的時點**。

接著請點同一筆請求的 **「預覽／Preview」**，截圖回應結構給我。若是收合的物件，可以展開看欄位名稱；**Token、Session、Cookie 或其他憑證值請遮住**。

`EncodedPassword` 的值也不要提供，編碼後的密碼仍要當成密碼保護。

<a id="qa37"></a>

### 37. 圖片／附件提問

來源：[S228-Q015](<../../原始問答/S228_限制 SAP B1 單一登入.md#q15>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-1a656e09-7992-4419-9e28-295d83048696.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1a656e09-7992-4419-9e28-295d83048696.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

收到，回應是：

```json
{"d":{"LogonBySBOUser":0}}
```

這裡沒有直接回傳連線 ID；**單靠數字 `0`，還不能確定它代表驗證成功，或已完成 B1 連線建立**。登入狀態也可能透過 Cookie 傳遞。

下一步看你先前清單中，緊接著出現的 **`gsapi?ac=init`**。請點那筆，提供：

- **標頭 → 一般**：網址、Request Method。
- **承載／Payload**（若有）：只保留欄位名稱。
- **預覽／Preview**：回應欄位，憑證及連線識別值遮住。

不用重新登入。我們要確認 **SLD 驗證之後，哪一步啟動 Browser Access 連線**，才能把「拒絕第二個登入」放在正確位置。

<a id="qa38"></a>

### 38. 圖片／附件提問

來源：[S228-Q016](<../../原始問答/S228_限制 SAP B1 單一登入.md#q16>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-390939fd-9d2b-42db-98fd-058245028358.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-390939fd-9d2b-42db-98fd-058245028358.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

確認了，這筆是：

```text
GET https://主機:8100/dispatcher/gsapi?ac=init
```

目前看到的順序是 **40000 的 SLD 登入請求 → 8100 的 `init` 請求**。但僅憑名稱，還不能判斷第二個連線在哪一步會取代第一個。

請在**這一筆**切到 **「預覽／Preview」**：
- 若顯示物件，截圖最外層欄位即可。
- 若顯示 HTML 或大量文字，先截開頭幾行。

Token、Session ID 等值請遮住。這筆是 GET，目前網址只有 `ac=init`，**不用再找 Payload**。

<a id="qa39"></a>

### 39. 圖片／附件提問

來源：[S228-Q017](<../../原始問答/S228_限制 SAP B1 單一登入.md#q17>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-d7d0d1c0-11c3-49d3-82cf-2e11dfd8a7bd.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d7d0d1c0-11c3-49d3-82cf-2e11dfd8a7bd.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張是 **「承載／Payload」的查詢參數**，只顯示 `ac = init`。

請在右側詳情區上方，切到 **「預覽／Preview」**，不是「承載」。

如果找不到「預覽」，點 **「回應／Response」** 也可以，截開頭幾行給我；若有 Token 或 Session ID，請遮住值。

<a id="qa40"></a>

### 40. 圖片／附件提問

來源：[S228-Q018](<../../原始問答/S228_限制 SAP B1 單一登入.md#q18>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-d0c8e106-bce6-42b9-a694-538a7490dee2.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d0c8e106-bce6-42b9-a694-538a7490dee2.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，這次就是回應內容。它是一串介面指令，包含 `sbo.setOpt`、`Form.create…`，以及 `pid: 13096`；這個 PID 也和你先前「關於 SAP Business One」畫面一致。

**這表示 `ac=init` 已經在取得 B1 用戶端介面資料，不能把它當成「登入前」攔截點。** 但目前還無法確定更早哪一筆請求會建立或取代連線。

先不用展開這 496 筆資料。接下來要補齊的是**頁面跳轉請求**，因為之前選了 Fetch/XHR，這些可能被隱藏了：

1. 在網路清單上方切回 **「全部」**，保留目前紀錄。
2. 找到 `LogonBySBOUser` 到 `gsapi?ac=init` 之間的請求。
3. 截圖這一段清單即可，網址若含權杖請遮住。

這能幫我們找出 SLD 驗證成功後，瀏覽器如何進入 Browser Access。**先前提出的閘道仍是待驗證方案，目前還不能保證只攔 `LogonBySBOUser` 就能完成管制。**

<a id="qa41"></a>

### 41. 圖片／附件提問

來源：[S228-Q019](<../../原始問答/S228_限制 SAP B1 單一登入.md#q19>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-0d332b18-7dce-412b-b5db-6da1e524ea00.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-0d332b18-7dce-412b-b5db-6da1e524ea00.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張仍是 `gsapi?ac=init` 的「回應」內容，這部分已經夠了，不用再展開。

我們要回到**左側有「名稱、狀態、類型」的請求清單**：

1. 點清單上方的 **「全部」**，不是右側的「回應」。
2. 找到 `LogonBySBOUser` 那一列。
3. 截圖它到 `gsapi?ac=init` 之間的所有列。

如果清單被右側詳情擋住，可以先把開發者工具視窗放大。**不用重新登入，也不用點進任何一筆請求。**

<a id="qa42"></a>

### 42. 圖片／附件提問

來源：[S228-Q020](<../../原始問答/S228_限制 SAP B1 單一登入.md#q20>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-895d9025-7a8f-4300-8c87-f0d6abaa30b9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-895d9025-7a8f-4300-8c87-f0d6abaa30b9.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，這次是請求清單。可以看到 `LogonBySBOUser` 後面緊接著 `gsapi?ac=init`，後續還有 `keepAlive`。

但這張沒包含上方篩選列，**還無法確認是否已顯示「全部」請求**，所以不能認定中間沒有頁面跳轉。

請點網路面板上方的 **「全部」**，再截同一段，**把「全部／Fetch/XHR」那排按鈕一起拍進來**。如果已經選「全部」，告訴我即可，不用重複登入。

另外，`keepAlive` 目前只能視為待確認的連線維持請求；不能直接用它停止出現，就判定帳號已登出。

<a id="qa43"></a>

### 43. 圖片／附件提問

來源：[S228-Q021](<../../原始問答/S228_限制 SAP B1 單一登入.md#q21>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-432173ca-9f75-4bb6-93ba-9570665762ef.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-432173ca-9f75-4bb6-93ba-9570665762ef.png
Image attachment: true

## codex-clipboard-212e036e-7e69-4297-8893-ac54af38b8e6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-212e036e-7e69-4297-8893-ac54af38b8e6.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這次找到關鍵了：**登入中間確實有 SSO 與重新導向流程**。

```text
LogonBySBOUser → sso → endpoint
→ dispatcher/?state=… → dispatcher/ → gsapi?ac=init
```

因此，**只攔 `LogonBySBOUser` 不足以保證單一登入**；已有 SSO 登入狀態的瀏覽器，可能走不同路徑。實作還需要在進入 Browser Access 時，把已驗證的身分與登入資格綁定。

接下來不用重新登入，請一次提供這兩筆的資訊：

| 請求 | 要看的資訊 |
|---|---|
| `sso` | 「標頭」中的 Request URL、Request Method |
| `endpoint`（302） | Request URL、Request Method，以及回應標頭的 `Location` |

**網址只保留主機代稱、連接埠、路徑與參數名稱；參數值全部遮掉。** 例如：

```text
https://主機:40000/某路徑?code=***&state=***
```

這能確認 SSO 回到 Browser Access 的位置，作為下一步設計攔截點的依據；目前還沒證實哪一步會取代既有連線。

<a id="qa44"></a>

### 44. 圖片／附件提問

來源：[S228-Q022](<../../原始問答/S228_限制 SAP B1 單一登入.md#q22>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-74c4e78f-1142-4c06-82be-7a8568f97a15.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-74c4e78f-1142-4c06-82be-7a8568f97a15.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這張有幫助：它是**自動提交登入憑證的中繼頁**，會把 `SAMLResponse`、`RelayState` 用 POST 送往 **40020** 的驗證服務。這表示登入流程還涉及驗證服務，不能只管制 8100。

**先不要再貼這兩個欄位的值**，它們屬於登入憑證資料。

目前你開的是「回應」，接下來請：

1. 點左側 **`endpoint`（狀態 302）**。
2. 切到 **「標頭／Headers」**。
3. 提供 **Request URL** 與下方 **Response Headers → Location**。

只需要「連接埠＋完整路徑＋參數名稱」，參數值遮掉，例如：

```text
Request URL：https://主機:40020/完整路徑
Location：https://主機:8100/dispatcher/?state=***&code=***
```

這一步用來確認驗證完成後的回呼位置；**目前仍未確認可安全攔截的位置，還不能直接部署管制程式。**

<a id="qa45"></a>

### 45. 圖片／附件提問

來源：[S228-Q023](<../../原始問答/S228_限制 SAP B1 單一登入.md#q23>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-2affa865-cc74-46dc-8c0d-5225e9798a7c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2affa865-cc74-46dc-8c0d-5225e9798a7c.png
Image attachment: true

## codex-clipboard-715e3602-3a37-460d-90a6-8757f97c88aa.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-715e3602-3a37-460d-90a6-8757f97c88aa.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]
[原對話含圖片，本文未嵌入圖片]

**歷史回答**

**找到更直接的方向了，可能不必另寫登入閘道。** 你們的流程經過 SAP 驗證服務的 `b1-SLD` 身分提供者，再帶授權碼回到 Browser Access。

底層 **Keycloak 有「User Session Count Limiter」**，可以設定連線數上限，超過時選擇 **Deny new session（拒絕新連線）**。這正是我們要評估的機制。[Keycloak 官方文件](https://www.keycloak.org/docs/26.7.5/server_admin/)

但要先確認兩件事：

- 你們隨 SAP 安裝的版本是否提供這個功能。
- **SSO 連線是否能對應 B1 實際連線**；限制 SSO 不一定等於限制所有 B1 視窗。

我前面太早把方向放在客製閘道，也讓你反覆截圖了。現在先停止抓網路請求，改查驗證服務。

請在伺服器上開啟：

```text
https://主機:40020/auth/
```

進入 **Administration Console**，使用你們的驗證服務管理帳號登入，提供首頁或左側選單截圖即可，**先不要修改設定**。SAP 官方也以這個入口管理驗證服務。[SAP 說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_IAM/548d6202b2b6491b824a488cfc447343/3501455761c0443589ad3bc54184baad.html)

接下來會先確認版本與現有流程，再決定能否在測試環境套用「上限 1、拒絕新連線」；不直接更動正式環境的登入流程。

<a id="qa46"></a>

### 46. 這個會是甚麼密碼啊

來源：[S228-Q024](<../../原始問答/S228_限制 SAP B1 單一登入.md#q24>)；2026-10-07

**問題**

## My request:
這個會是甚麼密碼啊

**歷史回答**

這裡要用的是 **SAP Business One 驗證服務（Keycloak）的管理員帳號與密碼**，不一定和 B1 的 `manager` 相同，也不是 Windows 登入密碼。

通常需要找當初安裝／維護 SAP 的 IT 或廠商確認。你可以直接問：

> 請提供 `40020/auth` 的 Keycloak Administration Console 管理員登入方式，我們要先查看驗證流程設定。

**不要假設 `B1SiteUser` 一定可以登入**，要看當初的設定。如果沒有人知道密碼，我們可以接著確認安裝版本與官方恢復方式；先不要重設，以免影響現有登入。

<a id="qa47"></a>

### 47. 應該沒有人碰過

來源：[S228-Q025](<../../原始問答/S228_限制 SAP B1 單一登入.md#q25>)；2026-10-07

**問題**

## My request:
應該沒有人碰過

**歷史回答**

沒有人手動設定過，也**不代表它沒有密碼或有通用預設密碼**；驗證服務可能是在安裝或升級 SAP 時一起建立的。

先不用猜密碼，也不用重設。我們先確認你看到哪個登入入口：

**請貼開啟 `https://主機:40020/auth/`、點 Administration Console 後的登入畫面，連網址路徑一起截圖即可。** 密碼不要填、網址內若有權杖請遮住。

我前面把管理員登入方式說得太籠統了；要先區分 **SAP 的管理登入頁**與 **Keycloak 管理主控台**，才能確認應該用 `B1SiteUser`，還是另外建立的管理員帳號。

<a id="qa48"></a>

### 48. 圖片／附件提問

來源：[S228-Q026](<../../原始問答/S228_限制 SAP B1 單一登入.md#q26>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-b09ad945-91cb-4e67-b2f2-39399ae7e8b0.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b09ad945-91cb-4e67-b2f2-39399ae7e8b0.png
Image attachment: true





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

確認了，網址有 **`/auth/realms/master/`**，這是 **Keycloak 的 master 管理登入頁**。B1 的 `manager` 帳號不會自動擁有這裡的管理權限，因此不能直接沿用它登入。

**這個密碼無法從畫面判斷，也沒有可保證使用的預設密碼。** 若沒人知道管理帳號，下一步是確認伺服器上的驗證服務版本，再依 SAP 對應版本的方式建立或恢復管理員。

請在 SAP 伺服器：

1. 按 **Win + R**，輸入 `services.msc`。
2. 找到名稱包含 **SAP Business One** 和 **Authentication** 的服務。
3. 雙擊它，提供「**執行檔路徑**」的截圖或文字。

這一步只查看資訊，**不用停止或重啟服務**。有了安裝路徑，我們就能繼續確認管理員恢復工具。

<a id="qa49"></a>

### 49. 圖片／附件提問

來源：[S228-Q027](<../../原始問答/S228_限制 SAP B1 單一登入.md#q27>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-31fc01dc-a789-4449-868f-4ffabffcee81.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-31fc01dc-a789-4449-868f-4ffabffcee81.png
Image attachment: true





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，就是選中的 **SAP Business One Server Tools Authentication Service**。

請**雙擊它 →「一般」頁籤**，把「**執行檔路徑**」複製貼給我，或截圖整個內容視窗。

先保持服務運行，不用按停止或重新啟動。

<a id="qa50"></a>

### 50. 圖片／附件提問

來源：[S228-Q028](<../../原始問答/S228_限制 SAP B1 單一登入.md#q28>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-296c29ae-63f2-40d1-b580-8f125e5a5d8a.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-296c29ae-63f2-40d1-b580-8f125e5a5d8a.png
Image attachment: true





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這個路徑是 **NSSM 服務啟動工具**，還不是 Keycloak 本體；但已確認 SAP 安裝在 `C:\Program Files\SAP`。

請在伺服器開啟「檔案總管」，把下面路徑貼到網址列：

```text
C:\Program Files\SAP\SAP Business One SetupFiles\keycloak\tools
```

**截圖裡面的檔案名稱即可，先不要執行任何檔案。** 如果找不到資料夾，告訴我。

SAP 官方有提供建立驗證服務管理員的程序，但不同版本的方法不同；我們先確認你們附帶的工具，再給你正確指令。[SAP 官方程序](https://help.sap.com/docs/SAP_BUSINESS_ONE_IAM/548d6202b2b6491b824a488cfc447343/8bfe8ede33834b1388c3a6e7c718065c.html)

<a id="qa51"></a>

### 51. 圖片／附件提問

來源：[S228-Q029](<../../原始問答/S228_限制 SAP B1 單一登入.md#q29>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-b99f2af6-f190-4afe-a683-6bbe821bc236.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b99f2af6-f190-4afe-a683-6bbe821bc236.png
Image attachment: true





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

有看到 `startup`，它是 PowerShell 腳本。不過我核對了 **SP 2505 官方文件，沒有找到新版文件的 `startup.ps1 add-admin` 指令**，所以先不要直接執行。[SP 2505 官方文件](https://help.sap.com/doc/64cff8d4a2f041fb8cc6c2d5fe42a86c/10.0_SP_2505/en-US/2d099edcd3644a85ae328eab6e9167f6.pdf)

請在伺服器開啟 **PowerShell**，貼上下面這段。它只讀取腳本、搜尋支援的管理員參數，**不會修改或重啟服務**：

```powershell
Select-String `
  -LiteralPath 'C:\Program Files\SAP\SAP Business One SetupFiles\keycloak\tools\startup.ps1' `
  -Pattern 'add-admin|KEYCLOAK_ADMIN|bootstrap-admin' `
  -Context 2,2
```

把結果貼給我；如果完全沒輸出，也直接告訴我。這樣就能確認你們這份工具是否支援建立管理員，不用猜指令。

<a id="qa52"></a>

### 52. 圖片／附件提問

來源：[S228-Q030](<../../原始問答/S228_限制 SAP B1 單一登入.md#q30>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-1c5e1d76-7c08-446f-8106-5baeec35b694.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1c5e1d76-7c08-446f-8106-5baeec35b694.png
Image attachment: true





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這個結果有線索：**啟動腳本把 `KEYCLOAK_ADMIN` 設成 `b1admin`**，密碼則從受保護的設定讀取。

但這只能確認腳本的設定，**不能保證目前已存在的管理員帳號或密碼仍相同**，也不表示它就是 B1SiteUser 的密碼。

接下來要看這段程式的執行條件。請執行下面的唯讀指令，顯示腳本第 65～95 行：

```powershell
Get-Content -LiteralPath 'C:\Program Files\SAP\SAP Business One SetupFiles\keycloak\tools\startup.ps1' |
    Select-Object -Skip 64 -First 31
```

把結果貼給我即可；若有實際密碼或加密字串請遮住。**不用執行解密函式，也不用重啟服務。**

<a id="qa53"></a>

### 53. 圖片／附件提問

來源：[S228-Q031](<../../原始問答/S228_限制 SAP B1 單一登入.md#q31>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-60f5d1d7-5c01-424e-a118-4757b312446f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-60f5d1d7-5c01-424e-a118-4757b312446f.png
Image attachment: true





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa54"></a>

### 54. 我看不出

來源：[S228-Q032](<../../原始問答/S228_限制 SAP B1 單一登入.md#q32>)；2026-10-07

**問題**

## My request:
我看不出

**歷史回答**

對，**這張看不出密碼，不是你漏看了**。它只告訴我們：

- 程式預設使用的管理員名稱是 `b1admin`。
- 密碼從受保護的設定讀取，沒有直接寫在這段程式裡。
- 目前是否已建立這個帳號，仍無法確定。

先不用再翻程式或猜密碼。我也要修正前面的方向：**SAP 明確不建議修改文件未列出的 Keycloak 設定，可能影響整個 B1 環境**，因此不能把「連線限制器」當成已確認適用的解法。[SAP SP 2505 官方說明，第 73 頁](https://help.sap.com/doc/64cff8d4a2f041fb8cc6c2d5fe42a86c/10.0_SP_2505/en-US/2d099edcd3644a85ae328eab6e9167f6.pdf#page=73)

**你們有獨立的 SAP 測試環境嗎，還是目前操作的就是大家正在使用的那台？** 這會決定下一步如何驗證客製方案。

<a id="qa55"></a>

### 55. 目前操作的就是大家正在使用

來源：[S228-Q033](<../../原始問答/S228_限制 SAP B1 單一登入.md#q33>)；2026-10-07

**問題**

## My request:
目前操作的就是大家正在使用

**歷史回答**

了解，這是**正式環境**。目前我們只是查看畫面與讀取腳本，依照前面的操作，還沒有改動登入設定。

**先不要重啟驗證服務、重設管理員或修改 Keycloak 流程**，這些動作可能影響大家登入。

接下來應先準備**隔離的測試環境，包含獨立的驗證服務與 Browser Access**；只在同一台 B1 新增測試公司，仍會共用登入服務，不能隔離這次變更的風險。

我可以繼續協助設計與實作，但目前還沒有驗證完成、可直接部署的方案。測試時至少要確認：

- 第二個同帳號登入被拒絕，第一個仍能操作。
- 登出與斷線後能正常恢復登入。
- 已有 SSO 登入狀態也無法繞過限制。

**你能建立一台獨立測試 VM，還是需要維護廠商協助建立？**

<a id="qa56"></a>

### 56. 我比較好奇 通常設定這個的帳號密碼是甚麼ˊ時候

來源：[S228-Q034](<../../原始問答/S228_限制 SAP B1 單一登入.md#q34>)；2026-10-07

**問題**

## My request:
我比較好奇 通常設定這個的帳號密碼是甚麼ˊ時候

**歷史回答**

你是問「**這組管理員帳號密碼通常是在什麼時候設定的**」，對嗎？

通常是在 **SAP Server Tools 安裝、升級，或首次初始化驗證服務時**建立；也可能是維護人員後來依管理員建立程序設定的。

從你提供的腳本，只能確認：

- 啟動時若有 `ADMIN_PASSWD_PROTECTED`，就會讀取其中受保護的密碼。
- 再以 `b1admin` 作為初始化管理員名稱。
- **無法據此判斷是誰設定、哪天設定，也不能確定目前密碼是否改過。**

所以你說「應該沒有人碰過」是有可能的：**它可能隨安裝或升級程序處理，未必有人進過 Keycloak 管理畫面。** 可以先問當初安裝／升級的人，是否留有「Authentication Service／Keycloak 的 `b1admin`」帳密紀錄。

<a id="qa57"></a>

### 57. 所以這個密碼看不到嗎

來源：[S228-Q035](<../../原始問答/S228_限制 SAP B1 單一登入.md#q35>)；2026-10-07

**問題**

## My request:
所以這個密碼看不到嗎

**歷史回答**

**目前這段程式看不到明文密碼，但不代表技術上一定無法取得。**

腳本會解密 `ADMIN_PASSWD_PROTECTED` 供啟動使用，所以在具備相應權限與執行環境時，可能可以還原那份保存的密碼。不過，**它可能只是初始化密碼，不一定是現在有效的管理員密碼**。

因此我前面說「看不到」不夠精確：**你貼的畫面只有讀取密碼的程式，沒有密碼本身。**

<a id="qa58"></a>

### 58. 那可以試試看取得嗎

來源：[S228-Q036](<../../原始問答/S228_限制 SAP B1 單一登入.md#q36>)；2026-10-07

**問題**

## My request:
那可以試試看取得嗎

**歷史回答**

可以，我們先確認**受保護的密碼是否還存在**，不改設定、不重啟服務，也先不輸出密碼。

請在 SAP 伺服器的 PowerShell 執行：

```powershell
$svc = Get-CimInstance Win32_Service |
    Where-Object DisplayName -eq 'SAP Business One Server Tools Authentication Service'

if (-not $svc) {
    Write-Output '找不到指定服務'
} else {
    $regPath = "HKLM:\SYSTEM\CurrentControlSet\Services\$($svc.Name)\Parameters"
    $settings = Get-ItemProperty -LiteralPath $regPath -ErrorAction SilentlyContinue
    $entries = @($settings.AppEnvironment) + @($settings.AppEnvironmentExtra)

    [pscustomobject]@{
        ServiceName = $svc.Name
        ServiceAccount = $svc.StartName
        ProtectedPasswordInService = [bool](
            $entries | Where-Object { $_ -match '^ADMIN_PASSWD_PROTECTED=.+' }
        )
        ProtectedPasswordInMachine = -not [string]::IsNullOrEmpty(
            [Environment]::GetEnvironmentVariable('ADMIN_PASSWD_PROTECTED', 'Machine')
        )
    }
}
```

把這段的結果貼給我即可，**它只顯示服務資訊與是否存在，不會顯示密碼**。

接著還要確認 `KC_Decryption` 的實作，才能判斷能否在本機還原。不要直接執行或載入整份 `startup.ps1`，因為它包含啟動服務的動作。若能取得密碼，我們會讓它留在你的伺服器上，不用貼到聊天裡。

<a id="qa59"></a>

### 59. 圖片／附件提問

來源：[S228-Q037](<../../原始問答/S228_限制 SAP B1 單一登入.md#q37>)；2026-10-07

**問題**

# Files mentioned by the user:

## codex-clipboard-a4fbec0d-a0dc-475c-b947-ffa897e8abaa.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a4fbec0d-a0dc-475c-b947-ffa897e8abaa.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

結果是兩個 **`False`**：目前在服務的環境變數設定，以及 Windows 系統環境變數中，**都沒有找到 `ADMIN_PASSWD_PROTECTED` 的非空值**。

因此，**目前沒有找到可以交給解密函式還原的資料**。這不代表管理員沒有密碼，也不代表其他地方一定沒有保存；初始化用的設定可能已被移除。

另外，前面的指令會隱藏登錄讀取錯誤，所以還不能完全排除讀取權限問題。請在 **以系統管理員身分開啟的 PowerShell** 執行這個唯讀檢查：

```powershell
try {
    $null = Get-ItemProperty -LiteralPath `
      'HKLM:\SYSTEM\CurrentControlSet\Services\B1ServerToolsAuthentication\Parameters' `
      -ErrorAction Stop
    '服務設定可正常讀取'
} catch {
    $_.Exception.Message
}
```

如果顯示「服務設定可正常讀取」，就能確認：**這兩個位置確實找不到保存的密碼，不能靠前面那段解密程式取回。**

<a id="qa60"></a>

### 60. 服務設定可正常讀取

來源：[S228-Q038](<../../原始問答/S228_限制 SAP B1 單一登入.md#q38>)；2026-10-07

**問題**

服務設定可正常讀取

**歷史回答**

那就確認了：**服務設定與系統環境變數裡，都沒有保存可供那段腳本解密的密碼。這條取回途徑目前走不通。**

這不代表 `b1admin` 不存在，只是找不到它的初始化密碼。前面我說可能還原，是以受保護的密碼資料仍存在為前提。

接下來可行的方向是：

- 找安裝／升級紀錄或維護廠商保存的帳密。
- 若確定遺失，安排維護時段，依對應版本的程序恢復管理員存取。

**目前沒有修改設定，也不需要為這次檢查重啟服務。**

<a id="qa61"></a>

### 61. 依對應版本的程序恢復管理員存取。 這個會怎麼做

來源：[S228-Q039](<../../原始問答/S228_限制 SAP B1 單一登入.md#q39>)；2026-10-07

**問題**

依對應版本的程序恢復管理員存取。   這個會怎麼做

**歷史回答**

可以，**SP 2505 的官方程序是「建立新的管理員存取」；若舊管理員已存在但密碼遺失，則要先清除舊管理員，再重建。** 不是直接查出舊密碼。

不過，**在走重建之前，可以先試一個不需修改設定的正式入口**：

```text
https://主機:40020/auth/admin/sapb1/console
```

使用你們已知的 **`B1SiteUser` 帳密**登入。這和之前的 `/realms/master/` 管理登入頁不同；SAP 文件有記載此入口可使用 B1SiteUser。前面我應該先帶你確認這條路。[官方文件，第 77 頁](https://help.sap.com/doc/64cff8d4a2f041fb8cc6c2d5fe42a86c/10.0_SP_2505/en-US/2d099edcd3644a85ae328eab6e9167f6.pdf#page=77)

若仍無法登入，恢復程序大致是：

1. **安排維護時段並備份驗證資料庫 `B1AS`**。
2. 唯讀確認 `master` 裡現有的管理員。
3. 若有遺失密碼的管理員，依官方 SQL 清除相關帳號資料。**官方查詢涵蓋 master 中非服務帳號的使用者，不只指定的 `b1admin`**，必須先核對影響範圍。
4. 按文件設定 `KEYCLOAK_ADMIN`、`KEYCLOAK_ADMIN_PASSWORD`，搭配要求的服務重啟建立管理員。
5. 驗證登入成功後，移除臨時密碼環境變數，並完成恢復後的帳號清理。[官方文件，第 64–75 頁](https://help.sap.com/doc/64cff8d4a2f041fb8cc6c2d5fe42a86c/10.0_SP_2505/en-US/2d099edcd3644a85ae328eab6e9167f6.pdf#page=64)

**重啟期間驗證服務會暫時不可用；現有使用者是否受影響，不能保證。** 所以現在先確認上述 B1SiteUser 入口，不必立即動資料庫。

