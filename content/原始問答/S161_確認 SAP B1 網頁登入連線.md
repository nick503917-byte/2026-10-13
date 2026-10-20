# 確認 SAP B1 網頁登入連線

來源編號：S161  
聊天 ID：`01a088ef-b061-7651-956e-879e261916c8`  
聊天起始日期（台北）：2026-09-10  
保存位置：一般本機紀錄  
問答段落：3

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S161-Q001｜當我在sapb1 用網頁版登入後 我可以在server 端 的 SSMS 我可以看到連線的人嗎

日期：2026-09-10

**你的提問**

當我在sapb1 用網頁版登入後

我可以在server 端 的 SSMS 我可以看到連線的人嗎

**當時回答**

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

---

<a id="q2"></a>

## S161-Q002｜所以我ssms 沒辦法看到我的sapb1 某一個資料庫有連線資訊嗎

日期：2026-09-10

**你的提問**

所以我ssms 沒辦法看到我的sapb1 某一個資料庫有連線資訊嗎

**當時回答**

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

---

<a id="q3"></a>

## S161-Q003｜我要怎麼看我自己的權限

日期：2026-09-10

**你的提問**

我要怎麼看我自己的權限

**當時回答**

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

---

