# Fix SQL Server certificate trust

來源編號：S002  
聊天 ID：`019f8339-2db9-7f21-8fd6-b25f12a5cd55`  
聊天起始日期（台北）：2026-07-21  
保存位置：封存紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S002-Q001｜TITLE: Connect to Server ------------------------------ Cannot connect to EC2AMAZ-IGRTDRC.…

日期：2026-07-21

**你的提問**

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

**當時回答**

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

---

<a id="q2"></a>

## S002-Q002｜給我中文

日期：2026-07-21

**你的提問**

給我中文

**當時回答**

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

---

