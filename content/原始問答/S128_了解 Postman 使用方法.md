# 了解 Postman 使用方法

來源編號：S128  
聊天 ID：`01a06134-93c0-74e3-a546-0b86e6ce0d1e`  
聊天起始日期（台北）：2026-09-02  
保存位置：一般本機紀錄  
問答段落：12

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S128-Q001｜POSTMAN 如何使用

日期：2026-09-02

**你的提問**

POSTMAN  如何使用

**當時回答**

Postman 是用来测试 API（接口）的工具，不必先写前端页面。

最常见的使用流程：

1. 新建请求：点击 **New → HTTP Request**
2. 选择方法：`GET`、`POST`、`PUT`、`DELETE`
3. 输入 API 地址，例如：
   ```text
   https://jsonplaceholder.typicode.com/posts/1
   ```
4. 点击 **Send**
5. 在下方查看状态码、响应时间和 JSON 数据

发送 POST JSON 的例子：

- 方法选择 `POST`
- URL：
  ```text
  https://jsonplaceholder.typicode.com/posts
  ```
- 打开 **Body → raw → JSON**
- 输入：
  ```json
  {
    "title": "測試文章",
    "body": "Hello Postman",
    "userId": 1
  }
  ```
- 点击 **Send**

常用位置：

- **Params**：网址查询参数，例如 `?page=1`
- **Authorization**：Bearer Token、Basic Auth 等身份验证
- **Headers**：请求标头，例如：
  ```text
  Content-Type: application/json
  Authorization: Bearer YOUR_TOKEN
  ```
- **Body**：提交的数据
  - `raw / JSON`：JSON API
  - `form-data`：上传文件
  - `x-www-form-urlencoded`：传统表单

如果 API 需要 Token，可在 **Authorization** 中选择 **Bearer Token**，填入 Token；Postman 会自动产生 `Authorization` 标头。

建议先记住这几个状态码：

- `200`：成功
- `201`：建立成功
- `400`：请求内容错误
- `401`：尚未认证或 Token 无效
- `403`：没有权限
- `404`：地址或资源不存在
- `500`：服务器内部错误

---

<a id="q2"></a>

## S128-Q002｜我如果要跟SAPB1

日期：2026-09-02

**你的提問**

我如果要跟SAPB1

**當時回答**

如果你要用 **Postman 連接 SAP Business One（SAP B1）**，一般是呼叫 **Service Layer API**。Service Layer 使用 HTTP/OData，SAP B1 10.0 可使用 `/b1s/v1`（OData v3）或 `/b1s/v2`（OData v4）。[SAP 官方說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/60c7a0b745bd486589f05a1da77041f3.html)

### 1. 確認連線資訊

你需要：

- SAP B1 Service Layer 伺服器 IP 或網域
- Service Layer Port，常見為 `50000`
- 公司資料庫名稱 `CompanyDB`
- SAP B1 帳號及密碼
- 電腦能連到該伺服器

基本網址通常是：

```text
https://SAP伺服器IP:50000/b1s/v1
```

例如：

```text
https://192.168.1.100:50000/b1s/v1
```

### 2. 登入 SAP B1

Postman 新增請求：

```http
POST https://192.168.1.100:50000/b1s/v1/Login
```

在 **Body → raw → JSON** 輸入：

```json
{
  "CompanyDB": "SBODEMOUS",
  "UserName": "manager",
  "Password": "你的密碼"
}
```

Headers：

```text
Content-Type: application/json
```

成功時會回傳：

```json
{
  "SessionId": "...",
  "Version": "1000...",
  "SessionTimeout": 30
}
```

同時產生 `B1SESSION` 與 `ROUTEID` Cookie。Postman 通常會自動保存並在後續請求中帶上 Cookie；若沒有，就到 Postman 的 **Cookies** 检查。SAP 官方要求後續請求帶上這些 Cookie。[SAP Service Layer 指南](https://help.sap.com/doc/fc2f5477516c404c8bf9ad1315a17238/10.0/en-US/Working_with_SAP_Business_One_Service_Layer.pdf)

### 3. 查詢資料

查詢前 10 筆客戶：

```http
GET https://192.168.1.100:50000/b1s/v1/BusinessPartners?$filter=CardType eq 'cCustomer'&$top=10
```

查詢指定客戶：

```http
GET https://192.168.1.100:50000/b1s/v1/BusinessPartners('C00001')
```

查詢前 10 張銷售訂單：

```http
GET https://192.168.1.100:50000/b1s/v1/Orders?$top=10
```

只取指定欄位：

```http
GET https://192.168.1.100:50000/b1s/v1/Items?$select=ItemCode,ItemName&$top=10
```

### 4. 新增客戶

```http
POST https://192.168.1.100:50000/b1s/v1/BusinessPartners
```

Body：

```json
{
  "CardCode": "C00099",
  "CardName": "Postman 測試客戶",
  "CardType": "cCustomer",
  "Phone1": "0912345678"
}
```

### 5. 新增銷售訂單

```http
POST https://192.168.1.100:50000/b1s/v1/Orders
```

Body：

```json
{
  "CardCode": "C00001",
  "DocDate": "2026-09-02",
  "DocumentLines": [
    {
      "ItemCode": "A00001",
      "Quantity": 2,
      "UnitPrice": 100
    }
  ]
}
```

這會真的在 SAP B1 建立訂單，建議先在測試公司操作。

### 6. 登出

```http
POST https://192.168.1.100:50000/b1s/v1/Logout
```

成功通常回傳：

```text
204 No Content
```

如果遇到錯誤：

- `401 Unauthorized`：登入失敗、Session 過期或 Cookie 沒有帶上
- `404 Not Found`：網址、Port 或資源名稱錯誤
- SSL certificate error：測試環境可能使用自簽憑證；可暫時關閉 Postman 的 **SSL certificate verification**，正式環境應安裝有效憑證
- 無法連線：確認 Service Layer 已啟動、防火牆與 `50000` Port 是否開放
- 若系統沒有 Service Layer，可能要改走 **DI API**；DI API 不是一般 REST API，不能直接用 Postman 呼叫。

---

<a id="q3"></a>

## S128-Q003｜這個嗎

日期：2026-09-02

**你的提問**

# Files mentioned by the user:

## codex-clipboard-daa7cfd9-7ece-4e53-ac54-3112a0d6526a.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-daa7cfd9-7ece-4e53-ac54-3112a0d6526a.png



## My request:
這個嗎

[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，這是 SAP B1 **Service Layer 的 Worker 設定**：

- `node1`：`http://127.0.0.1:50001/b1s`
- `node2`：`http://127.0.0.1:50002/b1s`

不過 `127.0.0.1` 只能在 **SAP 伺服器本機**存取。Postman 若裝在你的電腦，不能直接使用這兩個網址，應該使用 SAP 伺服器的 IP／網域及 Service Layer 對外 Port，通常類似：

```text
https://SAP伺服器IP:50000/b1s/v1/Login
```

若 Postman 就在 SAP 伺服器本機，可以先測試：

```text
http://127.0.0.1:50001/b1s/v1/Login
```

或：

```text
http://127.0.0.1:50002/b1s/v1/Login
```

方法選 `POST`，Body 選 **raw → JSON**：

```json
{
  "CompanyDB": "你的公司資料庫名稱",
  "UserName": "你的SAP帳號",
  "Password": "你的密碼"
}
```

圖片中的 `50001`、`50002` 是內部 Worker Port；一般應優先透過前端負載平衡器的對外網址連線，而不是固定連某個 node。你可以在 Postman 先測：

```text
https://SAP伺服器IP:50000/b1s/v1/Login
```

若連不上，再確認 Service Layer 管理頁面中的負載平衡器對外 Port、防火牆，以及 Postman 是否與 SAP 伺服器位於同一網路。

---

<a id="q4"></a>

## S128-Q004｜這個嗎

日期：2026-09-02

**你的提問**

# Files mentioned by the user:

## codex-clipboard-6eca8613-8ef1-4780-a1e4-4a18d8dcb5f4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6eca8613-8ef1-4780-a1e4-4a18d8dcb5f4.png



## My request:
 這個嗎

[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，就是這個。畫面顯示 Service Layer 對外網址為：

```text
https://EC2AMAZ-IGRTDRC:50000
```

在 **SAP 伺服器本機**的 Postman，可使用：

```text
https://EC2AMAZ-IGRTDRC:50000/b1s/v1/Login
```

設定如下：

- Method：`POST`
- Header：`Content-Type: application/json`
- Body → raw → JSON：

```json
{
  "CompanyDB": "你的公司資料庫名稱",
  "UserName": "manager",
  "Password": "你的密碼"
}
```

如果 Postman 在另一台電腦，`EC2AMAZ-IGRTDRC` 這個主機名稱未必能解析。這時改用 SAP 伺服器的區域網路 IP：

```text
https://SAP伺服器IP:50000/b1s/v1/Login
```

例如：

```text
https://192.168.1.100:50000/b1s/v1/Login
```

若是 AWS EC2 外部連線，則使用可連到該機器的私有 IP、VPN 網址或 DNS。**不建議直接把 Port 50000 開放給整個網際網路**；至少應限制來源 IP或經由 VPN。

第一次測試可能遇到自簽憑證錯誤。測試階段可在 Postman：

```text
Settings → General → SSL certificate verification → Off
```

然後重新送出請求。正式環境則應配置可信任的 HTTPS 憑證。

---

<a id="q5"></a>

## S128-Q005｜Looking at your 登入 request, the error is clear: Root cause: DNS resolution failure The hos…

日期：2026-09-02

**你的提問**

Looking at your **登入** request, the error is clear:

**Root cause: DNS resolution failure**

The hostname `ec2amaz-igrtdrc` in your URL `https://ec2amaz-igrtdrc:50000/b1s/v1/Login` cannot be resolved. This is a Windows EC2 instance hostname (the `ec2amaz-` prefix is typical of AWS EC2 Windows machines), and your computer cannot find it on the network.

This happens for one of these reasons:

1. **You're not on the same network as the server** — This hostname is a private/internal hostname that is only resolvable within a specific network (e.g., a corporate VPN or AWS VPC). If you're not connected to that network, DNS won't find it.
2. **The server is down or unreachable** — The EC2 instance may be stopped or the hostname may have changed.
3. **Missing hosts file entry** — If this server doesn't have a proper DNS record, it may need a manual entry in your system's `hosts` file.

**What to try:**

- **Connect to VPN** — If this is a corporate or AWS internal server, connect to the VPN first, then retry the request.
- **Use the IP address instead** — If you know the server's IP address, replace `ec2amaz-igrtdrc` in the URL with the actual IP (e.g., `https://192.168.x.x:50000/b1s/v1/Login`). You can store it as a variable like `{{base_url}}` for easy management.
- **Verify the server is running** — Confirm the EC2 instance is started and accessible.

The request body and credentials look structurally correct for a SAP Business One Service Layer login — once the network connectivity issue is resolved, the request should reach the server.

What would you like to do next?

**Replace hostname with IP addressSet up a base\_url variableCheck if SSL verification is an issue**

**當時回答**

對，這個判斷正確：目前請求甚至還沒到 SAP B1，問題在於你的電腦無法解析 `EC2AMAZ-IGRTDRC`。

請依序處理：

1. 在 SAP B1 伺服器開啟 PowerShell，執行：

```powershell
ipconfig
```

找到伺服器的 **IPv4 Address**，例如 `192.168.1.100`。

2. 在你的電腦測試 Port：

```powershell
Test-NetConnection 192.168.1.100 -Port 50000
```

如果顯示：

```text
TcpTestSucceeded : True
```

Postman 改用：

```text
POST https://192.168.1.100:50000/b1s/v1/Login
```

Body：

```json
{
  "CompanyDB": "你的公司資料庫名稱",
  "UserName": "你的SAP帳號",
  "Password": "你的密碼"
}
```

如果 `TcpTestSucceeded : False`，需要檢查：

- 是否連上公司 VPN／同一內部網路
- AWS Security Group 是否允許你的來源 IP 連入 TCP `50000`
- Windows Firewall 是否允許 Port `50000`
- SAP B1 Service Layer 是否正在運行
- 使用的是不是只有 AWS VPC 內才能連線的私有 IP

若 IP 可以連線，但 Postman 顯示憑證錯誤，測試時可暫時關閉：

```text
Postman → Settings → General
→ SSL certificate verification → Off
```

你也可以在 Postman 建立變數：

```text
base_url = https://192.168.1.100:50000
```

請求網址改成：

```text
{{base_url}}/b1s/v1/Login
```

不要把 SAP B1 的 `50000` Port 直接開放給所有網際網路來源；建議透過 VPN，或在 AWS Security Group 限制允許的來源 IP。

---

<a id="q6"></a>

## S128-Q006｜我要怎麼看我自己的電腦的ipv4

日期：2026-09-02

**你的提問**

我要怎麼看我自己的電腦的ipv4

**當時回答**

在你自己的 Windows 電腦查看 IPv4：

1. 按 `Win + R`
2. 輸入：
   ```text
   cmd
   ```
3. 按 Enter，再輸入：
   ```cmd
   ipconfig
   ```
4. 找目前使用中的網路介面：
   - 使用 Wi-Fi：看「無線區域網路介面卡 Wi-Fi」
   - 使用網路線：看「乙太網路卡 Ethernet」
5. 找到類似：
   ```text
   IPv4 位址 . . . . . . . . . . : 192.168.1.25
   ```

`192.168.1.25` 就是你電腦的區域網路 IPv4。

也可以用 PowerShell：

```powershell
Get-NetIPAddress -AddressFamily IPv4 |
Where-Object {$_.IPAddress -notlike "127.*"}
```

注意：你自己的 IPv4 不一定能拿來連 SAP。Postman 網址需要填的是 **SAP B1 伺服器的 IPv4**：

```text
https://SAP伺服器IPv4:50000/b1s/v1/Login
```

你電腦的 IPv4 主要用來確認是否與 SAP 伺服器位於同一網段，或提供給管理員设置防火牆允許規則。

---

<a id="q7"></a>

## S128-Q007｜甚麼U4N

日期：2026-09-02

**你的提問**

# Files mentioned by the user:

## codex-clipboard-8d10e095-c2c3-49d5-ab32-abdeb920f5e9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-8d10e095-c2c3-49d5-ab32-abdeb920f5e9.png



## My request:
甚麼U4N 

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這是別人已經建立好的 **Postman SAP B1 Service Layer API Collection**，可以直接使用。

畫面內容：

- **登入登出**
  - `POST 登入`：登入 SAP B1，取得 Session Cookie
- **業務夥伴主檔**
  - `GET 取得業務夥伴主檔`：查詢客戶／供應商
  - `POST 建立業務夥伴主檔`：新增客戶或供應商
  - `PATCH 更新業務夥伴主檔`：修改資料
  - `DEL 刪除業務夥伴主檔`：刪除資料
- **項目主檔**
  - 用來查詢或維護 SAP B1 商品／物料

HTTP 方法的意思：

- `GET`：查詢
- `POST`：登入或新增
- `PATCH`：修改
- `DEL/DELETE`：刪除

你現在應該先點擊：

```text
POST 登入
```

然後查看右側的：

1. Request URL
2. Body
3. Variables
4. Authorization 或 Cookies

登入成功後，再測試：

```text
GET 取得業務夥伴主檔
```

先不要測試 `POST`、`PATCH` 或 `DELETE`，因為它們會實際新增、修改或刪除 SAP B1 資料。

---

<a id="q8"></a>

## S128-Q008｜我成功新增了

日期：2026-09-02

**你的提問**

我成功新增了

**當時回答**

很好，表示以下環節都已經正常：

- Service Layer 網路連線成功
- SAP B1 登入成功
- `B1SESSION` Cookie 正常
- 帳號具備新增權限
- JSON 格式及欄位有效

接著建議到 SAP B1 的「業務夥伴主檔資料」中，用你新增的 `CardCode` 查詢，確認名稱、類型與地址等資料正確。

也可以在 Postman 驗證：

```http
GET {{base_url}}/b1s/v1/BusinessPartners('你的CardCode')
```

例如：

```http
GET {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

若回傳 `200 OK` 和該筆 JSON，就表示資料確實新增完成。

後續要修改資料，可以使用：

```http
PATCH {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

Body 範例：

```json
{
  "Phone1": "0912345678"
}
```

成功通常回傳 `204 No Content`。正式資料操作前，建議先持續使用測試公司。

---

<a id="q9"></a>

## S128-Q009｜你可以出一個 postman的基本操作嗎

日期：2026-09-02

**你的提問**

你可以出一個 postman的基本操作嗎

**當時回答**

可以，以下是一份以 **SAP Business One Service Layer** 為例的 Postman 基本操作指南。

# Postman 基本操作

## 1. 建立環境變數

右上角開啟 **Environments**，新增環境，例如：

```text
SAP B1 測試環境
```

加入變數：

| Variable | Initial value 範例 |
|---|---|
| `base_url` | `https://192.168.1.100:50000` |
| `company_db` | `SBODEMOUS` |
| `username` | `manager` |
| `password` | SAP B1 密碼 |
| `card_code` | `C00099` |

網址便可寫成：

```text
{{base_url}}/b1s/v1/Login
```

密碼屬於敏感資料，不要匯出或分享給別人。

---

## 2. 登入 SAP B1

建立 HTTP Request：

```http
POST {{base_url}}/b1s/v1/Login
```

選擇：

```text
Body → raw → JSON
```

輸入：

```json
{
  "CompanyDB": "{{company_db}}",
  "UserName": "{{username}}",
  "Password": "{{password}}"
}
```

Header：

```text
Content-Type: application/json
```

點擊 **Send**。

成功時：

- 狀態碼為 `200 OK`
- 回傳 `SessionId`
- Postman 會保存 `B1SESSION`、`ROUTEID` Cookie

如果後續出現 `401 Unauthorized`，通常是登入逾時，重新執行登入即可。

---

## 3. GET：查詢資料

### 查詢業務夥伴

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10
```

### 查詢指定業務夥伴

```http
GET {{base_url}}/b1s/v1/BusinessPartners('{{card_code}}')
```

### 只取得需要的欄位

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$select=CardCode,CardName,CardType&$top=10
```

### 使用條件篩選

查詢客戶：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$filter=CardType eq 'cCustomer'
```

常用 OData 參數：

| 參數 | 功能 |
|---|---|
| `$top=10` | 只取前10筆 |
| `$skip=10` | 跳過前10筆 |
| `$select` | 指定回傳欄位 |
| `$filter` | 篩選資料 |
| `$orderby` | 排序 |

---

## 4. POST：新增資料

建立業務夥伴：

```http
POST {{base_url}}/b1s/v1/BusinessPartners
```

Body：

```json
{
  "CardCode": "C00099",
  "CardName": "Postman 測試客戶",
  "CardType": "cCustomer",
  "Phone1": "0912345678"
}
```

成功通常會收到：

```text
201 Created
```

新增後，可用以下請求確認：

```http
GET {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

---

## 5. PATCH：修改資料

```http
PATCH {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

Body 只需填入要修改的欄位：

```json
{
  "CardName": "修改後的客戶名稱",
  "Phone1": "0223456789"
}
```

成功通常回傳：

```text
204 No Content
```

沒有回傳內容是正常的，可再用 `GET` 確認。

---

## 6. DELETE：刪除資料

```http
DELETE {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

成功通常回傳：

```text
204 No Content
```

刪除會真正影響 SAP B1 資料，而且有交易記錄的業務夥伴可能無法刪除。建議只在測試公司操作。

---

## 7. 登出

```http
POST {{base_url}}/b1s/v1/Logout
```

成功通常回傳：

```text
204 No Content
```

登出後原本的 Session Cookie 會失效。

---

## 8. 儲存與整理請求

建議建立 Collection：

```text
SAP B1 Service Layer
├── 登入登出
│   ├── POST 登入
│   └── POST 登出
├── 業務夥伴
│   ├── GET 查詢
│   ├── POST 新增
│   ├── PATCH 修改
│   └── DELETE 刪除
└── 項目主檔
```

每次設定完 Request，記得按 **Save**。

---

## 9. 常見狀態碼

| 狀態碼 | 意思 |
|---|---|
| `200` | 查詢或登入成功 |
| `201` | 新增成功 |
| `204` | 修改、刪除或登出成功 |
| `400` | JSON、欄位或資料內容錯誤 |
| `401` | 尚未登入或 Session 過期 |
| `403` | SAP B1 帳號權限不足 |
| `404` | URL 或資料不存在 |
| `409` | 資料衝突或代碼重複 |
| `500` | SAP Service Layer 內部錯誤 |

建議操作順序是：

```text
登入 → GET查詢 → POST新增 → GET確認
     → PATCH修改 → GET確認 → 登出
```

正式操作前，先確認目前連接的是測試公司，尤其是 `POST`、`PATCH` 和 `DELETE`。

---

<a id="q10"></a>

## S128-Q010｜這個嗎

日期：2026-09-02

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e4599101-6afa-4629-868f-c4ad8e0d9b32.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e4599101-6afa-4629-868f-c4ad8e0d9b32.png



## My request:
這個嗎

[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，這個：

```json
"odata.nextLink": "BusinessPartners?$skip=20"
```

代表目前這次只回傳了前 **20 筆業務夥伴資料**，還有下一頁資料可以查詢。

下一頁可以使用：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$skip=20
```

再下一頁可能會看到：

```json
"odata.nextLink": "BusinessPartners?$skip=40"
```

表示接著查：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$skip=40
```

你也可以自行控制每頁筆數，例如每次取10筆：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10&$skip=0
```

第二頁：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10&$skip=10
```

第三頁：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10&$skip=20
```

因此這不是錯誤訊息，而是 SAP B1 Service Layer 提供的「下一頁網址」。

---

<a id="q11"></a>

## S128-Q011｜可以整理教學給我嗎

日期：2026-09-02

**你的提問**

可以整理教學給我嗎

**當時回答**

# Postman 串接 SAP Business One 基礎教學

以下以 SAP B1 **Service Layer** 為例，包含登入、查詢、新增、修改、分頁及登出。

## 一、準備資料

需要先取得：

- SAP B1 Service Layer IP／網址
- Service Layer Port，常見為 `50000`
- 公司資料庫名稱 `CompanyDB`
- SAP B1 帳號
- SAP B1 密碼

基本網址範例：

```text
https://192.168.1.100:50000
```

請使用 SAP B1 伺服器的 IP，不是自己電腦的 IP。

---

## 二、建立 Postman Environment

在 Postman 左側選擇：

```text
Environments → Create Environment
```

名稱可設定為：

```text
SAP B1 測試環境
```

建立以下變數：

| Variable | Value 範例 |
|---|---|
| `base_url` | `https://192.168.1.100:50000` |
| `company_db` | `SBODEMOUS` |
| `username` | `manager` |
| `password` | SAP B1 密碼 |
| `card_code` | `C00099` |

之後便能用：

```text
{{base_url}}
```

代替完整網址。

記得在 Postman 右上角選取剛建立的環境。

---

## 三、建立 Collection

新增一個 Collection：

```text
SAP B1 Service Layer
```

建議資料夾結構：

```text
SAP B1 Service Layer
├── 登入登出
│   ├── POST 登入
│   └── POST 登出
├── 業務夥伴主檔
│   ├── GET 取得業務夥伴
│   ├── GET 取得指定業務夥伴
│   ├── POST 建立業務夥伴
│   └── PATCH 更新業務夥伴
└── 項目主檔
    ├── GET 取得項目
    └── GET 取得指定項目
```

---

# 四、登入 SAP B1

建立 Request：

```http
POST {{base_url}}/b1s/v1/Login
```

### Headers

```text
Content-Type: application/json
```

### Body

選擇：

```text
Body → raw → JSON
```

輸入：

```json
{
  "CompanyDB": "{{company_db}}",
  "UserName": "{{username}}",
  "Password": "{{password}}"
}
```

按下 **Send**。

登入成功會收到：

```text
200 OK
```

回傳內容類似：

```json
{
  "SessionId": "abcdef123456",
  "Version": "1000291",
  "SessionTimeout": 30
}
```

Postman 通常會自動保存：

```text
B1SESSION
ROUTEID
```

後續請求會自動帶上 Cookie。

---

# 五、查詢業務夥伴

## 取得前10筆

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10
```

不需要填写 Body，直接按 **Send**。

成功會收到：

```text
200 OK
```

主要資料位於：

```json
{
  "value": [
    {
      "CardCode": "C00001",
      "CardName": "測試客戶",
      "CardType": "cCustomer"
    }
  ]
}
```

---

## 查詢指定業務夥伴

```http
GET {{base_url}}/b1s/v1/BusinessPartners('{{card_code}}')
```

例如：

```http
GET {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

注意 `CardCode` 外面需要有單引號。

---

## 只取得需要的欄位

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$select=CardCode,CardName,Phone1&$top=10
```

這樣回傳資料會比較精簡。

---

## 篩選客戶

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$filter=CardType eq 'cCustomer'&$top=10
```

篩選供應商：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$filter=CardType eq 'cSupplier'&$top=10
```

依名稱排序：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$orderby=CardName asc&$top=10
```

---

# 六、分頁查詢

如果回傳結果下方出現：

```json
"odata.nextLink": "BusinessPartners?$skip=20"
```

這不是錯誤，表示還有下一頁。

查詢下一頁：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$skip=20
```

如果接著看到：

```json
"odata.nextLink": "BusinessPartners?$skip=40"
```

下一頁便是：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$skip=40
```

自行設定每頁10筆：

第一頁：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10&$skip=0
```

第二頁：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10&$skip=10
```

第三頁：

```http
GET {{base_url}}/b1s/v1/BusinessPartners?$top=10&$skip=20
```

---

# 七、新增業務夥伴

建立 Request：

```http
POST {{base_url}}/b1s/v1/BusinessPartners
```

### Headers

```text
Content-Type: application/json
```

### Body

```json
{
  "CardCode": "C00099",
  "CardName": "Postman 測試客戶",
  "CardType": "cCustomer",
  "Phone1": "0912345678"
}
```

成功通常會收到：

```text
201 Created
```

新增完成後，使用 GET 確認：

```http
GET {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

### CardType 對照

| 值 | 意思 |
|---|---|
| `cCustomer` | 客戶 |
| `cSupplier` | 供應商 |
| `cLid` | 潛在客戶 |

新增會實際寫入 SAP B1，請先在測試公司操作。

---

# 八、修改業務夥伴

```http
PATCH {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

Body 只需要放要修改的欄位：

```json
{
  "CardName": "修改後的客戶名稱",
  "Phone1": "0223456789"
}
```

成功通常回傳：

```text
204 No Content
```

沒有回傳 JSON 是正常的。可以再次執行 GET 確認：

```http
GET {{base_url}}/b1s/v1/BusinessPartners('C00099')
```

---

# 九、查詢項目主檔

取得前10筆項目：

```http
GET {{base_url}}/b1s/v1/Items?$select=ItemCode,ItemName&$top=10
```

查詢指定項目：

```http
GET {{base_url}}/b1s/v1/Items('A00001')
```

依項目名稱查詢：

```http
GET {{base_url}}/b1s/v1/Items?$filter=contains(ItemName,'測試')
```

部分 SAP B1／OData 版本對函數支援不同；若 `contains` 發生錯誤，可改用其他篩選方式。

---

# 十、登出 SAP B1

```http
POST {{base_url}}/b1s/v1/Logout
```

不需要 Body。

成功通常回傳：

```text
204 No Content
```

登出後，原本的 Session 便不能繼續使用。

---

# 十一、常見錯誤

## 找不到主機

例如：

```text
getaddrinfo ENOTFOUND EC2AMAZ-IGRTDRC
```

代表電腦無法解析主機名稱。請改用 SAP B1 伺服器 IP：

```text
https://192.168.1.100:50000
```

---

## SSL 憑證錯誤

測試環境可能使用自簽憑證，可暫時設定：

```text
Postman → Settings → General
→ SSL certificate verification → Off
```

正式環境應安裝有效的 HTTPS 憑證。

---

## 401 Unauthorized

常見原因：

- 尚未登入
- Session 已經過期
- Cookie 沒有帶上
- 帳號或密碼錯誤

解决方式：重新執行 `POST Login`。

---

## 400 Bad Request

常見原因：

- JSON 格式錯誤
- 欄位名稱錯誤
- 必填欄位沒有提供
- 欄位值不符合 SAP B1 規則

詳細原因通常可在回傳內容的 `error.message.value` 中看到。

---

# 十二、狀態碼對照

| 狀態碼 | 意思 |
|---|---|
| `200 OK` | 登入或查詢成功 |
| `201 Created` | 新增成功 |
| `204 No Content` | 修改、刪除或登出成功 |
| `400 Bad Request` | JSON、欄位或資料錯誤 |
| `401 Unauthorized` | 尚未登入或 Session 過期 |
| `403 Forbidden` | SAP B1 權限不足 |
| `404 Not Found` | 網址或資料不存在 |
| `409 Conflict` | 代碼重複或資料衝突 |
| `500 Internal Server Error` | Service Layer 或 SAP B1 錯誤 |

建議練習順序：

```text
登入
→ GET 查詢
→ POST 新增測試資料
→ GET 確認
→ PATCH 修改
→ GET 再次確認
→ 登出
```

在正式公司資料庫中，執行 `POST`、`PATCH` 或 `DELETE` 前，務必確認資料內容與 SAP B1 權限。

---

<a id="q12"></a>

## S128-Q012｜整理成 md檔給我 以及pdf

日期：2026-09-02

**你的提問**

整理成 md檔給我 以及pdf

**當時回答**

已整理完成，包含登入、CRUD、分頁、環境變數與錯誤排查：

- [下載 Markdown 教學](C:/Users/nick_yeh/Documents/Codex/2026-09-02/new-chat/outputs/Postman_SAP_B1_基礎操作教學.md)
- PDF 共 7 頁，已檢查中文字體與排版：[原成果：Postman_SAP_B1_基礎操作教學.pdf](<C:\Users\nick_yeh\Documents\Codex\2026-09-02\new-chat\outputs\Postman_SAP_B1_基礎操作教學.pdf>)

---

