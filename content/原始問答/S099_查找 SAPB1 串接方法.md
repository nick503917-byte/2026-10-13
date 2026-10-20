# 查找 SAPB1 串接方法

來源編號：S099  
聊天 ID：`01a021fc-1ba1-7b52-a7a2-6283b84a6465`  
聊天起始日期（台北）：2026-08-21  
保存位置：一般本機紀錄  
問答段落：39

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S099-Q001｜SAPB1 串接方法

日期：2026-08-21

**你的提問**

SAPB1 串接方法

**當時回答**

SAP Business One（SAP B1）串接，現行新系統通常首選 **Service Layer REST API**；舊版 Windows/.NET 環境或 API 未涵蓋的功能，才考慮 DI API。

## 串接方式比較

| 方法 | 適用情境 | 特點 |
|---|---|---|
| **Service Layer** | Web、App、電商、MES、WMS、雲端服務 | REST/OData、JSON、跨平台；新案首選 |
| **DI API** | Windows/.NET、中介程式、Service Layer 未支援功能 | COM 元件、功能完整，但部署與連線管理較重，且不支援多執行緒 |
| **Integration Framework（B1if）** | 多系統整合、排程、格式轉換、流程編排 | SAP B1 內建整合平台，可串 HTTP、檔案、DI API、Service Layer |
| **資料庫查詢** | 報表、BI、唯讀資料倉儲 | 僅建議唯讀；不可直接修改 SAP B1 資料表 |
| **Webhooks** | 訂單建立、資料異動後即時通知外部系統 | 避免頻繁輪詢；新版 Service Layer 可建立事件訂閱 |

## 建議架構

```text
電商／MES／WMS／企業網站
          │
          ▼
   自建 Middleware API
   - 欄位轉換
   - 驗證與授權
   - 重試／排程
   - 紀錄與對帳
          │ HTTPS
          ▼
 SAP B1 Service Layer
          │
          ▼
 SAP Business One 公司資料庫
```

不建議讓外部網站直接存取 Service Layer。中介層可以集中保護 SAP 帳密、控制流量、處理錯誤及保存同步紀錄。

## Service Layer 基本流程

目前應優先採用 OData v4：

```text
https://SAP-SERVER:50000/b1s/v2
```

SAP 已從 FP 2405 起將 OData v3 列為 deprecated，`/b1s/v2` 是建議端點。[SAP Service Layer API Reference](https://help.sap.com/doc/056f69366b5345a386bb8149f1700c19/10.0/en-US/Service%20Layer%20API%20Reference.html)

### 1. 登入

```http
POST /b1s/v2/Login
Content-Type: application/json

{
  "CompanyDB": "SBODEMO",
  "UserName": "manager",
  "Password": "********"
}
```

成功後保存回應 Cookie：

```text
B1SESSION=<session-id>
ROUTEID=<route-id>
```

後續呼叫至少要帶 `B1SESSION`；`ROUTEID` 用於維持負載平衡的 session stickiness。

### 2. 查詢客戶

```http
GET /b1s/v2/BusinessPartners?$select=CardCode,CardName&$filter=CardType eq 'cCustomer'
Cookie: B1SESSION=...
```

### 3. 建立銷售訂單

```http
POST /b1s/v2/Orders
Content-Type: application/json
Cookie: B1SESSION=...

{
  "CardCode": "C00001",
  "DocDueDate": "2026-08-31",
  "Comments": "由外部系統建立",
  "DocumentLines": [
    {
      "ItemCode": "A00001",
      "Quantity": 2,
      "UnitPrice": 100
    }
  ]
}
```

建立成功通常會回傳訂單的 `DocEntry`。之後查詢：

```http
GET /b1s/v2/Orders(12345)
```

官方的端點、登入及 OData 查詢說明可參考 [Working with SAP Business One Service Layer](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/44fda5d585e94b0bbd9d711584db0b96.html?locale=en-US)。

## 實作重點

- 使用正式 CA 憑證及 HTTPS，不要在正式環境忽略憑證驗證。
- SAP 帳密放在 Secret Manager 或伺服器環境設定，不放前端或程式碼。
- 保存外部單號至 SAP UDF，例如 `U_ExternalId`，避免重複建單。
- 呼叫失敗要記錄 HTTP 狀態、SAP 錯誤碼、請求 ID及原始資料。
- 針對 `429`、`502`、`503`、逾時做有限次數的指數退避重試。
- 分頁讀取資料，處理 OData 回傳的下一頁連結。
- 多筆需共同成功或失敗時，可使用 Service Layer `$batch`。
- 區分建立、更新及取消；取消單據通常使用特定 action，不是直接刪除。
- 不直接對 `ORDR`、`RDR1`、`OINV` 等 SAP 資料表執行 INSERT、UPDATE 或 DELETE，否則可能破壞商業邏輯、庫存及會計一致性。
- 先在測試公司資料庫驗證稅碼、倉庫、幣別、價格、批號／序號及核准流程。

若是一般「外部系統把訂單、客戶、商品、庫存同步進 SAP B1」，建議採用：

**外部系統 → 自建中介 API → Service Layer v2 → SAP B1**

如果你提供 SAP B1 版本、資料庫類型（SQL Server/HANA）、要串接的系統，以及資料流向，我可以進一步整理完整 API mapping 與 C#、Node.js 或 Python 範例。

---

<a id="q2"></a>

## S099-Q002｜API

日期：2026-08-21

**你的提問**

API

**當時回答**

SAP B1 要用 API 串接，建議採用 **Service Layer API（REST／OData v4）**。

## 基本資訊

```text
Base URL：https://<SAP主機>:50000/b1s/v2
格式：JSON
驗證：登入後取得 B1SESSION Cookie
```

## 1. 登入

```http
POST https://<SAP主機>:50000/b1s/v2/Login
Content-Type: application/json

{
  "CompanyDB": "公司資料庫名稱",
  "UserName": "API_USER",
  "Password": "密碼"
}
```

成功後保存 Cookie：

```text
B1SESSION=<session-id>
ROUTEID=<route-id>
```

## 2. 查詢 API

查詢客戶：

```http
GET /b1s/v2/BusinessPartners?$select=CardCode,CardName&$filter=CardCode eq 'C00001'
Cookie: B1SESSION=...
```

查詢品項：

```http
GET /b1s/v2/Items('A00001')
Cookie: B1SESSION=...
```

查詢庫存：

```http
GET /b1s/v2/Items('A00001')?$select=ItemCode,ItemName,ItemWarehouseInfoCollection
Cookie: B1SESSION=...
```

查詢銷售訂單：

```http
GET /b1s/v2/Orders(12345)
Cookie: B1SESSION=...
```

其中 `12345` 是 `DocEntry`，不是畫面上的 `DocNum`。

## 3. 建立客戶

```http
POST /b1s/v2/BusinessPartners
Content-Type: application/json
Cookie: B1SESSION=...

{
  "CardCode": "C00001",
  "CardName": "測試客戶",
  "CardType": "cCustomer",
  "Phone1": "02-12345678",
  "EmailAddress": "customer@example.com"
}
```

## 4. 建立銷售訂單

```http
POST /b1s/v2/Orders
Content-Type: application/json
Cookie: B1SESSION=...

{
  "CardCode": "C00001",
  "DocDate": "2026-08-21",
  "DocDueDate": "2026-08-31",
  "NumAtCard": "WEB-20260821-001",
  "Comments": "API建立",
  "DocumentLines": [
    {
      "ItemCode": "A00001",
      "WarehouseCode": "01",
      "Quantity": 2,
      "UnitPrice": 100
    }
  ]
}
```

## 5. 更新訂單

Service Layer 通常使用 `PATCH`：

```http
PATCH /b1s/v2/Orders(12345)
Content-Type: application/json
Cookie: B1SESSION=...

{
  "Comments": "外部系統已更新"
}
```

## 6. 取消訂單

```http
POST /b1s/v2/Orders(12345)/Cancel
Cookie: B1SESSION=...
Content-Type: application/json

{}
```

## 7. 登出

```http
POST /b1s/v2/Logout
Cookie: B1SESSION=...
```

## 常用資源名稱

| SAP 資料 | API Entity |
|---|---|
| 客戶／供應商 | `BusinessPartners` |
| 品項 | `Items` |
| 銷售報價 | `Quotations` |
| 銷售訂單 | `Orders` |
| 出貨單 | `DeliveryNotes` |
| 應收發票 | `Invoices` |
| 採購單 | `PurchaseOrders` |
| 收貨採購單 | `PurchaseDeliveryNotes` |
| 應付發票 | `PurchaseInvoices` |
| 庫存發貨 | `InventoryGenExits` |
| 庫存收貨 | `InventoryGenEntries` |

API 完整欄位與動作可查閱 [SAP Business One Service Layer API Reference](https://help.sap.com/doc/056f69366b5345a386bb8149f1700c19/10.0/en-US/Service%20Layer%20API%20Reference.html)。

正式導入時，建議使用獨立的 SAP API 帳號、限制權限、透過中介 API 呼叫，並以外部單號或 UDF 實作防重複機制。

---

<a id="q3"></a>

## S099-Q003｜可以產生一個 API的教學嗎

日期：2026-08-21

**你的提問**

## My request:
可以產生一個 API的教學嗎

**當時回答**

可以。以下是一份可直接照著操作的「SAP Business One Service Layer API 入門教學」，先用 Postman 測試，不必先寫程式。

# SAP B1 Service Layer API 教學

## 一、準備資料

請先向 SAP B1 管理員取得：

```text
Service Layer 主機：sap-server.example.com
連接埠：50000
公司資料庫：SBODEMO
SAP 使用者：API_USER
SAP 密碼：********
```

Service Layer v2 基礎網址：

```text
https://sap-server.example.com:50000/b1s/v2
```

先用瀏覽器測試：

```text
https://sap-server.example.com:50000/b1s/v2/$metadata
```

能顯示 XML 代表網路與 Service Layer 基本正常。

> 正式環境應使用有效的 HTTPS 憑證，不要長期停用憑證驗證。

---

## 二、建立 Postman 環境

在 Postman 建立 Environment，加入：

| Variable | Initial value |
|---|---|
| `baseUrl` | `https://sap-server.example.com:50000/b1s/v2` |
| `companyDB` | `SBODEMO` |
| `username` | `API_USER` |
| `password` | SAP 密碼 |
| `docEntry` | 先留空 |

密碼不要匯出或提交到 Git。

---

## 三、登入 SAP B1

建立 Postman Request：

```http
POST {{baseUrl}}/Login
Content-Type: application/json
```

Body 選擇 `raw`、格式選 `JSON`：

```json
{
  "CompanyDB": "{{companyDB}}",
  "UserName": "{{username}}",
  "Password": "{{password}}"
}
```

成功通常回傳：

```json
{
  "@odata.context": "https://sap-server:50000/b1s/v2/$metadata#B1Sessions/$entity",
  "SessionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "Version": "1000...",
  "SessionTimeout": 30
}
```

同時伺服器會設定 Cookie：

```text
B1SESSION
ROUTEID
```

Postman 通常會自動保存並在後續請求送出 Cookie，不需要手動複製。

---

## 四、查詢客戶

### 查詢前 10 筆客戶

```http
GET {{baseUrl}}/BusinessPartners?$select=CardCode,CardName,Phone1&$filter=CardType eq 'cCustomer'&$top=10
```

回傳範例：

```json
{
  "value": [
    {
      "CardCode": "C00001",
      "CardName": "範例客戶",
      "Phone1": "02-12345678"
    }
  ]
}
```

### 查詢指定客戶

```http
GET {{baseUrl}}/BusinessPartners('C00001')
```

字串主鍵要使用單引號；如果值內含單引號，要依 OData 規則跳脫。

---

## 五、查詢品項

```http
GET {{baseUrl}}/Items?$select=ItemCode,ItemName&$top=10
```

查詢指定品項：

```http
GET {{baseUrl}}/Items('A00001')
```

依名稱篩選：

```http
GET {{baseUrl}}/Items?$select=ItemCode,ItemName&$filter=contains(ItemName,'螺絲')
```

特殊字元與中文條件應由 Postman或程式進行 URL encoding。

---

## 六、建立客戶

```http
POST {{baseUrl}}/BusinessPartners
Content-Type: application/json
```

```json
{
  "CardCode": "C_API_001",
  "CardName": "API 測試客戶",
  "CardType": "cCustomer",
  "Phone1": "02-12345678",
  "EmailAddress": "customer@example.com"
}
```

可能還需要填寫的欄位，取決於公司的 SAP 設定：

- 業務員
- 統一編號
- 付款條件
- 控制科目
- 幣別
- 帳單及送貨地址
- 自訂欄位 UDF

---

## 七、建立銷售訂單

確認下列資料已存在：

- 客戶 `CardCode`
- 品項 `ItemCode`
- 倉庫 `WarehouseCode`
- 稅碼及價格設定

發送：

```http
POST {{baseUrl}}/Orders
Content-Type: application/json
```

```json
{
  "CardCode": "C00001",
  "DocDate": "2026-08-21",
  "DocDueDate": "2026-08-31",
  "NumAtCard": "WEB-20260821-001",
  "Comments": "由 Service Layer API 建立",
  "DocumentLines": [
    {
      "ItemCode": "A00001",
      "WarehouseCode": "01",
      "Quantity": 2,
      "UnitPrice": 100
    },
    {
      "ItemCode": "A00002",
      "WarehouseCode": "01",
      "Quantity": 1,
      "UnitPrice": 250
    }
  ]
}
```

成功回傳中應包含：

```json
{
  "DocEntry": 12345,
  "DocNum": 1000123
}
```

兩者用途不同：

- `DocEntry`：系統內部主鍵，API URL 使用它
- `DocNum`：使用者在 SAP B1 畫面看到的單號

---

## 八、查詢剛建立的訂單

```http
GET {{baseUrl}}/Orders(12345)
```

只取得需要的欄位：

```http
GET {{baseUrl}}/Orders(12345)?$select=DocEntry,DocNum,CardCode,DocDate,DocTotal,DocumentStatus
```

用外部訂單編號查詢：

```http
GET {{baseUrl}}/Orders?$select=DocEntry,DocNum,NumAtCard&$filter=NumAtCard eq 'WEB-20260821-001'
```

建立訂單前先查詢外部單號，可以降低重複建單風險。更完整的做法是建立專用 UDF，例如 `U_ExternalId`，並在中介系統加上唯一性控制。

---

## 九、更新訂單

使用 `PATCH`，只送需要變更的欄位：

```http
PATCH {{baseUrl}}/Orders(12345)
Content-Type: application/json
```

```json
{
  "Comments": "API 更新測試"
}
```

成功可能回傳 `204 No Content`，這代表更新成功，沒有回應內容。

文件狀態、核准流程或已產生後續單據時，部分欄位可能無法修改。

---

## 十、取消訂單

```http
POST {{baseUrl}}/Orders(12345)/Cancel
Content-Type: application/json
```

Body：

```json
{}
```

不要用 `DELETE` 取代取消。SAP 業務單據需要經過正式的取消邏輯，且是否能取消仍受文件狀態及權限限制。

---

## 十一、登出

```http
POST {{baseUrl}}/Logout
```

Body 不需要內容。長時間執行的程式還要處理 Session 過期：重新登入、更新 Cookie，再重送安全的請求。

---

## 十二、C# 呼叫範例

以下示範登入後查詢客戶：

```csharp
using System.Net;
using System.Net.Http.Json;

var handler = new HttpClientHandler
{
    CookieContainer = new CookieContainer()
};

using var client = new HttpClient(handler)
{
    BaseAddress = new Uri(
        "https://sap-server.example.com:50000/b1s/v2/")
};

var loginResponse = await client.PostAsJsonAsync("Login", new
{
    CompanyDB = "SBODEMO",
    UserName = "API_USER",
    Password = "********"
});

loginResponse.EnsureSuccessStatusCode();

var customersResponse = await client.GetAsync(
    "BusinessPartners" +
    "?$select=CardCode,CardName" +
    "&$filter=CardType eq 'cCustomer'" +
    "&$top=10"
);

customersResponse.EnsureSuccessStatusCode();

var json = await customersResponse.Content.ReadAsStringAsync();
Console.WriteLine(json);

await client.PostAsync("Logout", null);
```

正式程式不要直接寫死帳號密碼，也不要用略過所有 HTTPS 憑證驗證的方式處理憑證錯誤。

---

## 常見錯誤

| 狀況 | 常見原因 |
|---|---|
| `401 Unauthorized` | 尚未登入、Session 過期或 Cookie 未送出 |
| `404 Not Found` | Entity 名稱、API 版本或 `DocEntry` 錯誤 |
| `400 Bad Request` | JSON 欄位、日期、列舉值或 OData 語法錯誤 |
| `Internal error (-xxxx)` | SAP 商業規則、必填欄位或基礎資料不完整 |
| 無法連線 `50000` | 防火牆、Service Layer 未啟動或網址錯誤 |
| SSL 錯誤 | 憑證過期、不受信任或主機名稱不符 |
| 訂單無法新增 | 客戶、品項、倉庫、價格、稅碼、庫存或核准流程問題 |

錯誤回應通常類似：

```json
{
  "error": {
    "code": "-5002",
    "message": "錯誤原因"
  }
}
```

應同時記錄 HTTP 狀態碼、SAP 錯誤碼、請求時間、外部單號與回應內容，但不要把密碼或 Session Cookie 寫入日誌。

完整 Entity、欄位與 Action 可查閱 [SAP Business One Service Layer API Reference](https://help.sap.com/doc/056f69366b5345a386bb8149f1700c19/10.0/en-US/Service%20Layer%20API%20Reference.html)。

---

<a id="q4"></a>

## S099-Q004｜可以寫給我 我從 API 寫 業務夥伴主檔嗎 可以寫一個示範給我嗎

日期：2026-08-21

**你的提問**

## My request:
可以寫給我 我從 API 寫 業務夥伴主檔嗎 可以寫一個示範給我嗎

**當時回答**

可以。以下用 **C# + SAP B1 Service Layer v2** 示範「從 API 新增業務夥伴主檔」，包含登入、檢查客戶是否存在、新增客戶及登出。

## 完整 C# 範例

適用於 .NET 6 以上的 Console 專案：

```csharp
using System.Net;
using System.Net.Http.Json;
using System.Text.Json;

internal class Program
{
    private static async Task Main()
    {
        // SAP B1 Service Layer 設定
        const string serviceLayerUrl =
            "https://sap-server.example.com:50000/b1s/v2/";

        const string companyDb = "SBODEMO";
        const string userName = "API_USER";
        const string password = "YOUR_PASSWORD";

        var cookieContainer = new CookieContainer();

        var handler = new HttpClientHandler
        {
            CookieContainer = cookieContainer,
            UseCookies = true
        };

        using var client = new HttpClient(handler)
        {
            BaseAddress = new Uri(serviceLayerUrl),
            Timeout = TimeSpan.FromSeconds(60)
        };

        try
        {
            // 1. 登入 SAP B1
            await LoginAsync(
                client,
                companyDb,
                userName,
                password
            );

            // 2. 要建立的業務夥伴資料
            var businessPartner = new
            {
                CardCode = "C_API_001",
                CardName = "API 測試客戶",
                CardForeignName = "API Test Customer",

                // cCustomer：客戶
                // cSupplier：供應商
                // cLid：潛在客戶
                CardType = "cCustomer",

                Phone1 = "02-12345678",
                Cellular = "0912-345-678",
                EmailAddress = "customer@example.com",

                // 幣別；依公司設定調整
                Currency = "TWD",

                // 統一編號；實際欄位及驗證會依 Localization 而異
                FederalTaxID = "12345678",

                // 地址資料
                BPAddresses = new[]
                {
                    new
                    {
                        AddressName = "帳單地址",
                        AddressType = "bo_BillTo",
                        Street = "忠孝東路一段100號",
                        Block = "中正區",
                        City = "台北市",
                        ZipCode = "100",
                        Country = "TW"
                    },
                    new
                    {
                        AddressName = "送貨地址",
                        AddressType = "bo_ShipTo",
                        Street = "中山北路二段200號",
                        Block = "中山區",
                        City = "台北市",
                        ZipCode = "104",
                        Country = "TW"
                    }
                },

                // 聯絡人
                ContactEmployees = new[]
                {
                    new
                    {
                        Name = "王小明",
                        FirstName = "小明",
                        LastName = "王",
                        Position = "採購",
                        Phone1 = "02-12345678",
                        MobilePhone = "0912-345-678",
                        E_Mail = "ming@example.com"
                    }
                },

                // 自訂欄位 UDF 範例
                // 必須先在 SAP B1 建立該欄位
                U_ExternalId = "CRM-000001"
            };

            // 3. 防止重複建立
            var exists = await BusinessPartnerExistsAsync(
                client,
                businessPartner.CardCode
            );

            if (exists)
            {
                Console.WriteLine(
                    $"業務夥伴 {businessPartner.CardCode} 已存在，不重複建立。"
                );

                return;
            }

            // 4. 建立業務夥伴
            var createdPartner = await CreateBusinessPartnerAsync(
                client,
                businessPartner
            );

            Console.WriteLine("業務夥伴建立成功：");
            Console.WriteLine(
                JsonSerializer.Serialize(
                    createdPartner,
                    new JsonSerializerOptions
                    {
                        WriteIndented = true
                    }
                )
            );
        }
        catch (SapB1ApiException ex)
        {
            Console.WriteLine(
                $"SAP B1 API 錯誤：HTTP {(int)ex.StatusCode}"
            );
            Console.WriteLine(ex.ResponseBody);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"程式錯誤：{ex.Message}");
        }
        finally
        {
            // 5. 登出
            await LogoutSafelyAsync(client);
        }
    }

    private static async Task LoginAsync(
        HttpClient client,
        string companyDb,
        string userName,
        string password
    )
    {
        var request = new
        {
            CompanyDB = companyDb,
            UserName = userName,
            Password = password
        };

        using var response = await client.PostAsJsonAsync(
            "Login",
            request
        );

        await EnsureSapSuccessAsync(response);

        Console.WriteLine("SAP B1 登入成功。");
    }

    private static async Task<bool> BusinessPartnerExistsAsync(
        HttpClient client,
        string cardCode
    )
    {
        // OData 字串中的單引號需要變成兩個單引號
        var escapedCardCode = cardCode.Replace("'", "''");

        var url =
            "BusinessPartners" +
            "?$select=CardCode" +
            $"&$filter=CardCode eq '{escapedCardCode}'" +
            "&$top=1";

        using var response = await client.GetAsync(url);

        await EnsureSapSuccessAsync(response);

        var json = await response.Content.ReadAsStringAsync();

        using var document = JsonDocument.Parse(json);

        return document.RootElement
            .GetProperty("value")
            .GetArrayLength() > 0;
    }

    private static async Task<JsonElement> CreateBusinessPartnerAsync(
        HttpClient client,
        object businessPartner
    )
    {
        using var response = await client.PostAsJsonAsync(
            "BusinessPartners",
            businessPartner
        );

        await EnsureSapSuccessAsync(response);

        var json = await response.Content.ReadAsStringAsync();

        using var document = JsonDocument.Parse(json);

        // Clone，避免 JsonDocument 釋放後資料失效
        return document.RootElement.Clone();
    }

    private static async Task LogoutSafelyAsync(HttpClient client)
    {
        try
        {
            using var response = await client.PostAsync(
                "Logout",
                null
            );

            if (response.IsSuccessStatusCode)
            {
                Console.WriteLine("SAP B1 已登出。");
            }
        }
        catch
        {
            // 登出失敗不覆蓋原本的例外
        }
    }

    private static async Task EnsureSapSuccessAsync(
        HttpResponseMessage response
    )
    {
        if (response.IsSuccessStatusCode)
        {
            return;
        }

        var responseBody =
            await response.Content.ReadAsStringAsync();

        throw new SapB1ApiException(
            response.StatusCode,
            responseBody
        );
    }
}

internal sealed class SapB1ApiException : Exception
{
    public HttpStatusCode StatusCode { get; }

    public string ResponseBody { get; }

    public SapB1ApiException(
        HttpStatusCode statusCode,
        string responseBody
    ) : base($"SAP B1 API 回傳 HTTP {(int)statusCode}")
    {
        StatusCode = statusCode;
        ResponseBody = responseBody;
    }
}
```

## 建立專案並執行

```powershell
dotnet new console -n SapB1BusinessPartnerDemo
cd SapB1BusinessPartnerDemo
```

把上述內容放進 `Program.cs`，修改：

```csharp
serviceLayerUrl
companyDb
userName
password
```

然後執行：

```powershell
dotnet run
```

## 實際送出的 API

核心 API 是：

```http
POST https://sap-server.example.com:50000/b1s/v2/BusinessPartners
Content-Type: application/json
Cookie: B1SESSION=...
```

```json
{
  "CardCode": "C_API_001",
  "CardName": "API 測試客戶",
  "CardType": "cCustomer",
  "Phone1": "02-12345678",
  "EmailAddress": "customer@example.com",
  "Currency": "TWD",
  "FederalTaxID": "12345678",
  "BPAddresses": [
    {
      "AddressName": "帳單地址",
      "AddressType": "bo_BillTo",
      "Street": "忠孝東路一段100號",
      "Block": "中正區",
      "City": "台北市",
      "ZipCode": "100",
      "Country": "TW"
    }
  ],
  "ContactEmployees": [
    {
      "Name": "王小明",
      "Position": "採購",
      "Phone1": "02-12345678",
      "E_Mail": "ming@example.com"
    }
  ],
  "U_ExternalId": "CRM-000001"
}
```

## 注意事項

- `CardCode` 必須唯一。
- `CardType` 可用 `cCustomer`、`cSupplier` 或 `cLid`。
- `Country` 必須使用 SAP B1 已設定的國家代碼。
- `Currency`、控制科目、付款條件等必須符合公司資料庫設定。
- `U_ExternalId` 是示範 UDF；若 SAP B1 沒有這個欄位，請刪除或先建立 UDF。
- 台灣統編欄位與驗證可能因 SAP B1 Localization、版本及公司的欄位設定不同，正式開發前請先從現有客戶執行 `GET BusinessPartners('客戶代碼')` 確認實際欄位。
- 正式系統應將帳密放在安全設定中，不要直接寫在原始碼。

---

<a id="q5"></a>

## S099-Q005｜Program.cs 這是甚麼意思

日期：2026-08-21

**你的提問**

## My request:
Program.cs 這是甚麼意思

**當時回答**

`Program.cs` 是 **C# 程式的主要入口檔案**。

簡單來說：

- `Program`：程式
- `.cs`：C# 原始碼檔案
- 執行 `dotnet run` 時，會從 `Program.cs` 開始執行

## 建立方式

先確認電腦已安裝 [.NET SDK](https://dotnet.microsoft.com/download)。

開啟 PowerShell，輸入：

```powershell
dotnet new console -n SapB1ApiDemo
cd SapB1ApiDemo
```

系統會產生：

```text
SapB1ApiDemo/
├── SapB1ApiDemo.csproj
└── Program.cs
```

預設的 `Program.cs` 通常只有：

```csharp
Console.WriteLine("Hello, World!");
```

把它替換成 SAP B1 API 程式碼，再執行：

```powershell
dotnet run
```

## 最簡單的 SAP B1 範例

先把 `Program.cs` 改成：

```csharp
using System.Net;
using System.Net.Http.Json;

var cookies = new CookieContainer();

var handler = new HttpClientHandler
{
    CookieContainer = cookies
};

using var client = new HttpClient(handler);

var baseUrl = "https://你的SAP主機:50000/b1s/v2/";

// 登入
var loginResponse = await client.PostAsJsonAsync(
    $"{baseUrl}Login",
    new
    {
        CompanyDB = "你的公司資料庫",
        UserName = "你的SAP帳號",
        Password = "你的SAP密碼"
    }
);

if (!loginResponse.IsSuccessStatusCode)
{
    var error = await loginResponse.Content.ReadAsStringAsync();

    Console.WriteLine("登入失敗：");
    Console.WriteLine(error);

    return;
}

Console.WriteLine("登入成功");

// 新增業務夥伴
var customerResponse = await client.PostAsJsonAsync(
    $"{baseUrl}BusinessPartners",
    new
    {
        CardCode = "C_API_001",
        CardName = "API測試客戶",
        CardType = "cCustomer",
        Phone1 = "02-12345678",
        EmailAddress = "customer@example.com"
    }
);

var result = await customerResponse.Content.ReadAsStringAsync();

if (customerResponse.IsSuccessStatusCode)
{
    Console.WriteLine("業務夥伴建立成功：");
    Console.WriteLine(result);
}
else
{
    Console.WriteLine("業務夥伴建立失敗：");
    Console.WriteLine(result);
}

// 登出
await client.PostAsync($"{baseUrl}Logout", null);
```

你需要修改這四個地方：

```csharp
"https://你的SAP主機:50000/b1s/v2/"
"你的公司資料庫"
"你的SAP帳號"
"你的SAP密碼"
```

因此，`Program.cs` 就像整個 C# 專案的「開始按鈕」：你把 SAP B1 串接程式放進去，再用 `dotnet run` 執行。

---

<a id="q6"></a>

## S099-Q006｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-85195f5a-d0bb-43c9-8fb0-afe89cd6e7f8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-85195f5a-d0bb-43c9-8fb0-afe89cd6e7f8.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

從畫面可以確認，你的 **SAP Business One Service Layer 已安裝**，API 位址是：

```text
https://EC2AMAZ-IGRTDRC:50000
```

因此程式中的 `baseUrl` 應寫成：

```csharp
var baseUrl = "https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";
```

## 先測試 API 是否可用

請在 SAP 伺服器的瀏覽器開啟：

```text
https://EC2AMAZ-IGRTDRC:50000/b1s/v2/$metadata
```

如果出現大量 XML 內容，代表 Service Layer API 可以連線。

也可以測試：

```text
https://EC2AMAZ-IGRTDRC:50000/b1s/v2/
```

出現 `Unauthorized` 或要求登入不一定是錯誤，因為 Service Layer 必須先呼叫 `Login`。

## Program.cs 設定範例

```csharp
var baseUrl = "https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";

var loginResponse = await client.PostAsJsonAsync(
    $"{baseUrl}Login",
    new
    {
        CompanyDB = "你的公司資料庫名稱",
        UserName = "你的SAP帳號",
        Password = "你的SAP密碼"
    }
);
```

`CompanyDB` 不是公司顯示名稱，而是實際資料庫名稱，例如：

```text
SBODEMOTW
SBO_COMPANY
WEISHI_PROD
```

可以在 SLD 上方的 **DB Instances and Companies** 頁籤確認。

## 畫面中兩個網址的差異

你看到的是：

```text
https://EC2AMAZ-IGRTDRC:50000
https://EC2AMAZ-IGRTDRC:40000/ServiceLayerController
```

用途分別是：

- `50000`：外部程式呼叫的 Service Layer API
- `40000/ServiceLayerController`：Service Layer 管理介面

C# 程式要使用 `50000`，不是管理介面的 `40000`。

## 若另一台電腦無法連線

`EC2AMAZ-IGRTDRC` 是伺服器的內部電腦名稱。若程式在其他電腦執行，可能無法解析這個名稱，需要改成：

```text
https://伺服器IP:50000/b1s/v2/
```

或正式 DNS：

```text
https://sap-api.你的公司網域:50000/b1s/v2/
```

同時確認：

- Windows／AWS 防火牆允許 TCP 50000
- 用戶端可以解析伺服器名稱
- HTTPS 憑證有效且名稱相符
- 不要將 50000 直接公開到網際網路；建議透過 VPN、內網或受控反向代理存取

下一步先開啟：

```text
https://EC2AMAZ-IGRTDRC:50000/b1s/v2/$metadata
```

能看到 XML 後，就可以進行登入與新增業務夥伴測試。

---

<a id="q7"></a>

## S099-Q007｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e0fa4d6f-c2b7-4fe7-8e79-300b42d3a16b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e0fa4d6f-c2b7-4fe7-8e79-300b42d3a16b.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個畫面表示 **Service Layer 已正常啟動，而且你的電腦可以成功連線**。目前只是開啟 Service Layer 的文件首頁，還沒有實際呼叫 API。

接下來可做以下測試。

## 1. 測試 API Metadata

在網址後面加上：

```text
/b1s/v2/$metadata
```

完整網址：

```text
https://EC2AMAZ-IGRTDRC:50000/b1s/v2/$metadata
```

如果出現大量 XML，表示 OData v4 API 正常。

## 2. 開啟 API Reference

畫面中的 **API Reference** 可以查看：

- `BusinessPartners`：業務夥伴
- `Items`：品項
- `Orders`：銷售訂單
- `Invoices`：應收發票
- 各 Entity 可使用的欄位與動作

你要新增業務夥伴時，可以在 API Reference 搜尋：

```text
BusinessPartners
```

## 3. 修改 C# 程式網址

你的 `Program.cs` 使用：

```csharp
var baseUrl =
    "https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";
```

然後登入：

```csharp
var loginResponse = await client.PostAsJsonAsync(
    $"{baseUrl}Login",
    new
    {
        CompanyDB = "實際公司資料庫名稱",
        UserName = "SAP帳號",
        Password = "SAP密碼"
    }
);
```

## 4. 注意：瀏覽器不能直接測試新增

瀏覽器網址列只能方便地測試 `GET`，新增業務夥伴需要送出 `POST` 和 JSON，所以應使用：

- Postman：最適合先測試
- C# `Program.cs`：之後正式開發
- PowerShell：也能快速測試

你的 Service Layer 連線目前看起來已經正常。下一步需要確認 `CompanyDB`，然後先做 `POST /b1s/v2/Login` 登入測試。

---

<a id="q8"></a>

## S099-Q008｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-bdcc73ec-9226-4b09-9be9-f12f49e3d81d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-bdcc73ec-9226-4b09-9be9-f12f49e3d81d.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個登入視窗是瀏覽器收到 `401 Unauthorized` 後顯示的，代表：

- Service Layer 可以連線。
- `/b1s/v2/$metadata` 需要先建立 SAP B1 Session。
- 不要在這個瀏覽器視窗輸入 SAP 帳號密碼；Service Layer 正常登入方式是呼叫 `POST /Login` 並傳送 JSON。

## 建議先用 PowerShell測試

開啟 PowerShell，執行以下內容，並替換三項資料：

```powershell
$baseUrl = "https://EC2AMAZ-IGRTDRC:50000/b1s/v2"

$loginBody = @{
    CompanyDB = "你的公司資料庫名稱"
    UserName  = "你的SAP帳號"
    Password  = "你的SAP密碼"
} | ConvertTo-Json

$session = New-Object Microsoft.PowerShell.Commands.WebRequestSession

$loginResult = Invoke-RestMethod `
    -Method Post `
    -Uri "$baseUrl/Login" `
    -ContentType "application/json" `
    -Body $loginBody `
    -WebSession $session

$loginResult
```

登入成功會看到類似：

```text
SessionId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Version       : 1000...
SessionTimeout: 30
```

## 登入後查詢業務夥伴

在同一個 PowerShell 視窗繼續執行：

```powershell
$customers = Invoke-RestMethod `
    -Method Get `
    -Uri "$baseUrl/BusinessPartners?`$select=CardCode,CardName&`$top=10" `
    -WebSession $session

$customers.value |
    Select-Object CardCode, CardName |
    Format-Table
```

請注意 PowerShell 裡的 OData `$select` 和 `$top` 前面需要加反引號：

```text
`$select
`$top
```

## 建立一筆測試客戶

確認測試代碼尚未存在，再執行：

```powershell
$customerBody = @{
    CardCode    = "C_API_001"
    CardName    = "API測試客戶"
    CardType    = "cCustomer"
    Phone1      = "02-12345678"
    EmailAddress = "customer@example.com"
} | ConvertTo-Json

$newCustomer = Invoke-RestMethod `
    -Method Post `
    -Uri "$baseUrl/BusinessPartners" `
    -ContentType "application/json" `
    -Body $customerBody `
    -WebSession $session

$newCustomer |
    Select-Object CardCode, CardName, CardType
```

成功後可至 SAP B1：

```text
業務夥伴 → 業務夥伴主檔資料
```

查詢：

```text
C_API_001
```

## 最後登出

```powershell
Invoke-RestMethod `
    -Method Post `
    -Uri "$baseUrl/Logout" `
    -WebSession $session
```

如果第一段登入失敗，請保留錯誤訊息，但遮住密碼後貼出來。最常見原因是 `CompanyDB` 填成公司名稱，而不是實際資料庫名稱。

---

<a id="q9"></a>

## S099-Q009｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-ca34adbb-33da-478c-84c4-5f87ed4030ad.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-ca34adbb-33da-478c-84c4-5f87ed4030ad.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

畫面表示 **Microsoft .NET SDK 10.0.400 正在安裝**，操作正確。先等待進度完成，不要按「取消」。

安裝完成後，按「關閉」，再依序操作：

1. 開啟新的 PowerShell 視窗。
2. 輸入：

```powershell
dotnet --version
```

正常應顯示：

```text
10.0.400
```

3. 建立 SAP B1 API 專案：

```powershell
cd C:\
dotnet new console -n SapB1ApiDemo
cd SapB1ApiDemo
```

4. 查看建立的檔案：

```powershell
dir
```

應該會看到：

```text
Program.cs
SapB1ApiDemo.csproj
```

5. 先測試 .NET：

```powershell
dotnet run
```

看到：

```text
Hello, World!
```

就表示 .NET 安裝及專案都正常。

接著使用記事本開啟程式：

```powershell
notepad Program.cs
```

將裡面的：

```csharp
Console.WriteLine("Hello, World!");
```

替換成前面提供的 SAP B1 API 程式碼，儲存後再執行：

```powershell
dotnet run
```

建議先不要直接新增正式客戶；第一階段只測試登入和查詢，確認公司資料庫、帳號及 Service Layer 都正常後，再建立 `C_API_001` 測試資料。

---

<a id="q10"></a>

## S099-Q010｜通常 這個會是甚麼密碼

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-dada00e4-361a-4633-ba2b-6657bfff23e4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-dada00e4-361a-4633-ba2b-6657bfff23e4.png





## My request:
 通常 這個會是甚麼密碼

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個視窗**沒有固定或預設密碼**。它是瀏覽器收到 Service Layer 的 `401 Unauthorized` 後，自動顯示的 HTTP 登入視窗。

通常不需要在這裡輸入任何資料，直接按「取消」即可。

SAP B1 Service Layer 正確的驗證方式，是透過 API 呼叫：

```http
POST /b1s/v2/Login
```

並傳入三個資料：

```json
{
  "CompanyDB": "公司資料庫名稱",
  "UserName": "SAP B1使用者帳號",
  "Password": "該SAP B1帳號的密碼"
}
```

這裡的帳密是平常登入 **SAP Business One 用戶端**的帳密，例如：

```text
UserName：manager
Password：manager 帳號目前設定的密碼
```

但不是：

- Windows 登入密碼
- SQL Server `sa` 密碼
- SLD 的 `b1siteuser` 密碼
- License Manager 密碼
- SAP Service Layer 的固定預設密碼

如果不知道 SAP B1 密碼，需要請公司的 SAP B1 管理員確認或建立一個專用 API 使用者。建議不要使用 `manager` 做正式串接，應建立權限受限的 `API_USER`。

所以現在請按「取消」，後續在 C# 的登入內容中填寫 SAP B1 帳密：

```csharp
new
{
    CompanyDB = "實際資料庫名稱",
    UserName = "API_USER",
    Password = "API_USER的SAP B1密碼"
}
```

瀏覽器網址列不適合執行這個登入流程，應使用 C#、PowerShell 或 Postman。

---

<a id="q11"></a>

## S099-Q011｜所以 .NET是在幹嘛的

日期：2026-08-21

**你的提問**

## My request:
所以 .NET是在幹嘛的

**當時回答**

.NET 是用來**開發和執行 C# 程式的平台**。

在你的 SAP B1 串接案例中，角色如下：

```text
你寫的 C# 程式
      ↓
.NET 負責編譯與執行
      ↓ HTTPS／JSON
SAP B1 Service Layer API
      ↓
建立業務夥伴主檔
```

也就是說：

- **C#**：你使用的程式語言
- **.NET**：讓 C# 程式能夠建立、編譯及執行的平台
- **Service Layer**：SAP B1 提供的 API
- **Program.cs**：放置 C# 程式碼的主要檔案

安裝 **.NET SDK** 之後，你才能使用這些指令：

```powershell
dotnet new console
```

建立 C# 專案。

```powershell
dotnet build
```

檢查並編譯程式。

```powershell
dotnet run
```

執行程式。

你的 C# 程式會負責：

1. 登入 SAP B1 Service Layer。
2. 取得登入 Session。
3. 將客戶資料轉成 JSON。
4. 呼叫 `POST /BusinessPartners`。
5. 接收 SAP B1 的成功結果或錯誤訊息。
6. 登出 Service Layer。

.NET **不是 SAP B1 的必要元件**。Service Layer 本身已經可以運作；安裝 .NET 是因為我們選擇用 C# 寫串接程式。

你也可以使用其他工具：

- Postman：人工測試 API，不必寫程式
- PowerShell：快速測試
- Python：用 Python 寫串接程式
- Node.js：用 JavaScript 寫串接程式
- .NET/C#：適合 Windows 和企業內部系統，也是你目前採用的方法

簡單比喻：

```text
SAP B1 Service Layer = SAP 提供的窗口
C# 程式             = 去窗口辦事的人
.NET                 = 讓這個人能工作的環境
Program.cs           = 告訴他要辦哪些事的工作指令
```

---

<a id="q12"></a>

## S099-Q012｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-60c2b62a-9637-4880-8373-b23c4849f512.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-60c2b62a-9637-4880-8373-b23c4849f512.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這張圖表示電腦已經安裝 **Visual Studio 2019**。它是用來編輯、執行及除錯 C# 程式的開發工具，比記事本方便很多。

不過，你剛安裝的是 **.NET 10**，Visual Studio 2019 只正式支援到 .NET 5，因此不適合用它開啟這次的 .NET 10 專案。[Microsoft 官方相容性說明](https://learn.microsoft.com/en-us/visualstudio/releases/2019/compatibility)

建議選擇：

- 最簡單：繼續使用 PowerShell＋`dotnet` 指令，程式用記事本編輯。
- 圖形化開發：安裝最新版 Visual Studio，選擇「.NET 桌面開發」工作負載。
- 輕量方案：安裝 Visual Studio Code，再安裝 Microsoft 的 C# 擴充套件。

目前不用移除 Visual Studio 2019，它可能還有其他舊系統在使用。先確認 .NET 安裝：

```powershell
dotnet --version
```

看到：

```text
10.0.400
```

即可先用命令建立專案：

```powershell
dotnet new console -n SapB1ApiDemo
cd SapB1ApiDemo
notepad Program.cs
```

所以這兩個東西的差異是：

```text
.NET SDK             = 建立及執行 C# 程式的核心工具
Visual Studio 2019   = 編輯程式的圖形化開發環境，但版本較舊
```

---

<a id="q13"></a>

## S099-Q013｜dotnet new console -n SapB1ApiDemo cd SapB1ApiDemo notepad Program.cs 然後呢

日期：2026-08-21

**你的提問**

## My request:
dotnet new console -n SapB1ApiDemo
cd SapB1ApiDemo
notepad Program.cs 然後呢

**當時回答**

執行：

```powershell
notepad Program.cs
```

記事本開啟後，把原本內容全部刪除，貼上下面這段。第一步先做「登入＋查詢」，暫時不新增客戶，比較安全。

```csharp
using System.Net;
using System.Net.Http.Json;

// SAP B1 Service Layer 網址
var baseUrl = "https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";

// 請修改以下三項
var companyDb = "你的公司資料庫名稱";
var userName = "你的SAP帳號";
var password = "你的SAP密碼";

var cookies = new CookieContainer();

var handler = new HttpClientHandler
{
    CookieContainer = cookies,
    UseCookies = true
};

using var client = new HttpClient(handler)
{
    BaseAddress = new Uri(baseUrl),
    Timeout = TimeSpan.FromSeconds(60)
};

try
{
    Console.WriteLine("正在登入 SAP B1...");

    var loginResponse = await client.PostAsJsonAsync(
        "Login",
        new
        {
            CompanyDB = companyDb,
            UserName = userName,
            Password = password
        }
    );

    var loginResult =
        await loginResponse.Content.ReadAsStringAsync();

    if (!loginResponse.IsSuccessStatusCode)
    {
        Console.WriteLine("登入失敗：");
        Console.WriteLine(loginResult);
        return;
    }

    Console.WriteLine("登入成功！");
    Console.WriteLine();

    // 查詢前 10 筆業務夥伴
    Console.WriteLine("正在查詢業務夥伴...");

    var response = await client.GetAsync(
        "BusinessPartners" +
        "?$select=CardCode,CardName,CardType" +
        "&$top=10"
    );

    var result = await response.Content.ReadAsStringAsync();

    if (response.IsSuccessStatusCode)
    {
        Console.WriteLine("查詢成功：");
        Console.WriteLine(result);
    }
    else
    {
        Console.WriteLine("查詢失敗：");
        Console.WriteLine(result);
    }

    // 登出
    await client.PostAsync("Logout", null);

    Console.WriteLine();
    Console.WriteLine("SAP B1 已登出。");
}
catch (HttpRequestException ex)
{
    Console.WriteLine("連線失敗：");
    Console.WriteLine(ex.Message);
}
catch (Exception ex)
{
    Console.WriteLine("程式發生錯誤：");
    Console.WriteLine(ex.Message);
}
```

接著修改這三行：

```csharp
var companyDb = "你的公司資料庫名稱";
var userName = "你的SAP帳號";
var password = "你的SAP密碼";
```

例如：

```csharp
var companyDb = "SBO_COMPANY";
var userName = "manager";
var password = "實際密碼";
```

`companyDb` 必須是資料庫代碼，不是 SAP 畫面顯示的公司名稱。可以在 SLD 的「DB Instances and Companies」確認。

儲存記事本：

```text
檔案 → 儲存
```

然後關閉記事本，回到 PowerShell。確認目前路徑最後是：

```text
...\SapB1ApiDemo>
```

執行：

```powershell
dotnet run
```

成功時會顯示：

```text
正在登入 SAP B1...
登入成功！

正在查詢業務夥伴...
查詢成功：
{"value":[...]}

SAP B1 已登出。
```

如果看到紅色編譯錯誤，請貼出錯誤畫面。如果顯示「登入失敗」，請貼出 SAP 回傳內容，但先遮住公司帳號等敏感資料。

登入和查詢成功後，再把以下「新增業務夥伴」程式放在登出之前：

```csharp
var newPartner = new
{
    CardCode = "C_API_001",
    CardName = "API測試客戶",
    CardType = "cCustomer",
    Phone1 = "02-12345678",
    EmailAddress = "customer@example.com"
};

var createResponse = await client.PostAsJsonAsync(
    "BusinessPartners",
    newPartner
);

var createResult =
    await createResponse.Content.ReadAsStringAsync();

if (createResponse.IsSuccessStatusCode)
{
    Console.WriteLine("業務夥伴建立成功：");
}
else
{
    Console.WriteLine("業務夥伴建立失敗：");
}

Console.WriteLine(createResult);
```

先完成登入與查詢測試，再新增 `C_API_001`，可以避免在連線設定尚未確認時修改正式 SAP 資料。

---

<a id="q14"></a>

## S099-Q014｜\

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-f603f7c2-76dd-4109-9737-4e194a753755.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f603f7c2-76dd-4109-9737-4e194a753755.png





## My request:
\

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這不是 SAP 帳密錯誤，而是 **HTTPS 憑證驗證失敗**。常見原因是 Service Layer 使用自簽憑證，或憑證名稱與 `EC2AMAZ-IGRTDRC` 不一致。

先用「暫時測試」方式確認 API，其後再安裝正式憑證。

在 `Program.cs` 找到：

```csharp
var handler = new HttpClientHandler
{
    CookieContainer = cookies,
    UseCookies = true
};
```

暫時改成：

```csharp
var handler = new HttpClientHandler
{
    CookieContainer = cookies,
    UseCookies = true,

    // 僅供內部測試：暫時略過 HTTPS 憑證驗證
    ServerCertificateCustomValidationCallback =
        HttpClientHandler.DangerousAcceptAnyServerCertificateValidator
};
```

儲存後重新執行：

```powershell
dotnet run
```

如果接著顯示：

```text
登入成功！
```

就能確定 Service Layer 連線正常，只是憑證不受信任。

## 正式環境注意

這行有安全風險：

```csharp
HttpClientHandler.DangerousAcceptAnyServerCertificateValidator
```

它只適合目前的內部測試，不應用於正式系統。正式做法是：

- 替 Service Layer 安裝公司或公開 CA 簽發的 HTTPS 憑證。
- 憑證主機名稱需包含 `EC2AMAZ-IGRTDRC`，或改用憑證所對應的正式 DNS 名稱。
- 將公司的根憑證安裝到執行 API 程式之電腦的「受信任的根憑證授權單位」。
- 完成後移除略過憑證驗證的程式碼。

若修改後下一步顯示「登入失敗」，代表 SSL 問題已經通過，接下來再根據 SAP 回傳訊息檢查 `CompanyDB`、帳號或密碼。

---

<a id="q15"></a>

## S099-Q015｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-7e20eed8-c1ec-4019-9861-d785f8530fe4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7e20eed8-c1ec-4019-9861-d785f8530fe4.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

錯誤訊息：

```text
Program.cs(17,22): error CS1003：必須是 ','
```

表示 `Program.cs` 第 17 行附近少了一個逗號，通常是：

```csharp
UseCookies = true
```

後面漏了 `,`。

請執行：

```powershell
notepad Program.cs
```

找到 `var handler` 這一段，整段刪掉後，完整貼上：

```csharp
var handler = new HttpClientHandler
{
    CookieContainer = cookies,
    UseCookies = true,
    ServerCertificateCustomValidationCallback =
        HttpClientHandler.DangerousAcceptAnyServerCertificateValidator
};
```

請特別注意：

```csharp
UseCookies = true,
```

`true` 後面必須有逗號。

儲存記事本並關閉，重新執行：

```powershell
dotnet run
```

這次若編譯成功，就會繼續測試登入。這個略過 SSL 驗證的設定僅供目前內部測試，正式環境完成憑證設定後要移除。

---

<a id="q16"></a>

## S099-Q016｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-da01c6d3-0495-4be3-96c2-f83d5cc96a9b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-da01c6d3-0495-4be3-96c2-f83d5cc96a9b.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

現在 SSL 與程式都已正常，SAP Service Layer 已收到請求。錯誤：

```text
code: 206
Invalid login credential.
```

表示下面三項至少有一項不正確：

```csharp
CompanyDB
UserName
Password
```

## 1. 確認 CompanyDB

進入 SLD：

```text
DB Instances and Companies
```

找到要登入的公司，使用它的實際資料庫名稱，例如：

```text
SBO_TEST
SBODEMOTW
WEISHI_PROD
```

不要填公司畫面顯示名稱，例如：

```text
維熹實業股份有限公司
```

程式應填：

```csharp
var companyDb = "實際資料庫名稱";
```

## 2. 使用 SAP Business One 帳密

必須是平常登入 SAP Business One 用戶端的帳號和密碼：

```csharp
var userName = "manager";
var password = "manager的實際SAP密碼";
```

不是以下帳密：

- Windows 帳密
- SQL Server `sa` 帳密
- `b1siteuser` 帳密
- SLD 登入密碼
- AWS／伺服器密碼

請先用同一組帳密直接登入 SAP Business One 用戶端。如果用戶端也不能登入，就要請 SAP 管理員確認密碼、帳號是否鎖定或重設密碼。

## 3. 檢查程式內容

```powershell
notepad Program.cs
```

確認類似：

```csharp
var companyDb = "SBO_COMPANY";
var userName = "manager";
var password = "實際密碼";
```

注意：

- 不要保留「你的公司資料庫名稱」等示範文字。
- 英文大小寫要保持正確。
- 密碼前後不要多空格。
- 使用英文半形雙引號 `" "`。
- 若密碼含 `"` 或 `\`，C# 字串需要跳脫；可暫時換一個不含特殊符號的測試帳號驗證。

修改、儲存後重新執行：

```powershell
dotnet run
```

目前進度已經從「無法連線」進到「SAP 拒絕帳密」，所以網址、連接埠和 SSL 測試設定都已通過；接下來只需核對公司資料庫名稱與 SAP B1 使用者帳密。

---

<a id="q17"></a>

## S099-Q017｜這裡也要修改嗎

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e9b269d6-13b0-4f1f-a459-794f930ebd61.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e9b269d6-13b0-4f1f-a459-794f930ebd61.png





## My request:
這裡也要修改嗎

[原對話含圖片，本文未嵌入圖片]

**當時回答**

這裡**不用修改**，畫面中的程式是正確的。

這段：

```csharp
new
{
    CompanyDB = companyDb,
    UserName = userName,
    Password = password
}
```

意思是把前面設定的三個變數送給 SAP：

```csharp
var companyDb = "實際資料庫名稱";
var userName = "SAP B1帳號";
var password = "SAP B1密碼";
```

你只需要修改 `Program.cs` 上方的設定，例如：

```csharp
var baseUrl =
    "https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";

var companyDb = "SBO_COMPANY";
var userName = "manager";
var password = "實際密碼";
```

下面登入區域保留：

```csharp
CompanyDB = companyDb,
UserName = userName,
Password = password
```

可以把它理解成：

```text
左邊 CompanyDB = SAP API 要求的欄位名稱
右邊 companyDb = 你在程式上方輸入的實際資料
```

所以只改上方的值，不要把下面改成帳號密碼。修改完成後儲存，再執行：

```powershell
dotnet run
```

---

<a id="q18"></a>

## S099-Q018｜那我寫對

日期：2026-08-21

**你的提問**

## My request:
那我寫對

**當時回答**

對，從你貼的畫面來看，**登入程式那一段寫法正確**，不需要修改。

目前 `Invalid login credential` 不是語法問題，而是上方設定的其中一項不符合 SAP：

```csharp
var companyDb = "實際資料庫名稱";
var userName = "SAP B1登入帳號";
var password = "SAP B1登入密碼";
```

請優先確認 `companyDb` 是 SLD 顯示的實際資料庫名稱，不是公司中文名稱；帳密則必須能直接登入 SAP Business One 用戶端。

確認後：

```powershell
dotnet run
```

如果仍是錯誤 `206`，就表示帳密、資料庫名稱或帳號狀態仍需由 SAP B1 管理員確認。

---

<a id="q19"></a>

## S099-Q019｜但還是不行

日期：2026-08-21

**你的提問**

## My request:
但還是不行

**當時回答**

既然仍顯示 `206 Invalid login credential`，程式與網路已經正常，現在只需排查登入資料。不要再修改下方 `Login` 程式。

請依序確認：

1. 在 SAP Business One 用戶端，選擇同一家公司。
2. 不用 Windows SSO，改用 SAP B1 帳號與密碼登入。
3. 確認這組帳密真的能登入。
4. `UserName` 要填「使用者代碼」，不是使用者姓名。
5. 到 SLD 的 **DB Instances and Companies** 複製實際資料庫名稱。

程式上方應類似：

```csharp
var companyDb = "SBO_COMPANY";
var userName = "manager";
var password = "實際SAP密碼";
```

可在登入前加入下面兩行，以確認程式實際使用什麼值：

```csharp
Console.WriteLine($"CompanyDB = [{companyDb}]");
Console.WriteLine($"UserName  = [{userName}]");
```

不要輸出密碼。

## 密碼含特殊符號時

如果密碼含有雙引號或反斜線，C# 寫法需要處理：

```text
實際密碼：abc"123
C# 寫法：abc\"123
```

```text
實際密碼：abc\123
C# 寫法：abc\\123
```

更安全的做法是執行時輸入密碼。把：

```csharp
var password = "實際SAP密碼";
```

改為：

```csharp
Console.Write("請輸入 SAP B1 密碼：");
var password = Console.ReadLine() ?? "";
```

這樣可以避免特殊符號寫錯，也不會把密碼保存在 `Program.cs`。

如果確認同一個 `CompanyDB`、SAP B1 使用者及密碼能登入桌面用戶端，但 API 仍回傳 206，就需要請 SAP 管理員檢查：

- SAP B1 使用者是否鎖定或停用。
- 是否只使用 SSO，而沒有可用的 SAP B1 原生密碼。
- 公司資料庫與 `SBO-COMMON` 是否升級到相同版本。
- Service Layer 是否正確連接到這個 DB Instance。

SAP 官方登入格式確實是 `CompanyDB + UserName + Password`，所以你目前的 API 程式結構沒有問題。[SAP Service Layer Login 說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_VERSION_FOR_SAP_HANA/e83fb6dd8db7421486e0a2bb4e326720/c0dcc0ad861c4143b58494d4d02c04d6.html)

---

<a id="q20"></a>

## S099-Q020｜圖片／附件提問

日期：2026-08-21

**你的提問**

## My request:

**當時回答**

請把目前 `dotnet run` 的最新結果截圖貼上來，我可以繼續判斷。

若仍是：

```text
Invalid login credential
code: 206
```

請一併提供以下兩項，但不要提供密碼：

```text
CompanyDB：實際資料庫名稱
UserName：SAP B1 使用者代碼
```

也請確認該帳號能否直接登入同一家 SAP Business One 公司。

---

<a id="q21"></a>

## S099-Q021｜var companyDb = "WellShin_VN_Test_260428"; var userName = "manager";

日期：2026-08-21

**你的提問**

## My request:
var companyDb = "WellShin_VN_Test_260428";
var userName = "manager";

**當時回答**

這兩行的 C# 寫法正確：

```csharp
var companyDb = "WellShin_VN_Test_260428";
var userName = "manager";
```

接著確認密碼設定：

```csharp
var password = "manager帳號的SAP B1實際密碼";
```

請勿把密碼貼給我。

完整設定應是：

```csharp
var baseUrl =
    "https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";

var companyDb = "WellShin_VN_Test_260428";
var userName = "manager";
var password = "實際SAP密碼";
```

然後執行：

```powershell
dotnet run
```

如果仍回傳 `206`，請先在 SAP Business One 用戶端選擇公司 `WellShin_VN_Test_260428`，使用 `manager` 和相同密碼登入：

- SAP B1 用戶端也登入失敗：密碼錯誤、帳號鎖定或需要重設。
- SAP B1 用戶端登入成功，但 API 失敗：請確認 SLD 裡的資料庫名稱大小寫及字元完全相同，並請管理員檢查該公司是否已正確註冊到 Service Layer。

另外，不要使用 `b1siteuser`、Windows 或 SQL Server 密碼；這裡必須使用 `manager` 的 SAP B1 密碼。

---

<a id="q22"></a>

## S099-Q022｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-109deb32-2a1c-40c8-98e9-c1cc67548263.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-109deb32-2a1c-40c8-98e9-c1cc67548263.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

錯誤仍然是 `206`，所以可確定：

- Service Layer 網址正確
- SSL 已通過
- C# 程式成功送出登入請求
- SAP 拒絕了 `CompanyDB／UserName／Password` 組合

先排除密碼在程式碼中輸入錯誤。把原本這行：

```csharp
var password = "實際SAP密碼";
```

改成：

```csharp
Console.Write("請輸入 manager 的 SAP B1 密碼：");
var password = Console.ReadLine() ?? "";
```

儲存後執行：

```powershell
dotnet run
```

畫面要求密碼時，輸入平常登入 SAP B1 的密碼並按 Enter。

> PowerShell 輸入時會顯示密碼，請不要截圖或貼出密碼。

同時確認程式上方是：

```csharp
var companyDb = "WellShin_VN_Test_260428";
var userName = "manager";
```

最重要的驗證是：在 SAP Business One 桌面程式中，選擇 `WellShin_VN_Test_260428`，關閉 Windows SSO／使用者綁定登入，手動輸入：

```text
使用者代碼：manager
密碼：同一組密碼
```

判斷方式：

- 桌面版也失敗：`manager` 密碼不正確、已鎖定或需要重設。
- 桌面版成功、API 仍失敗：請到 SLD 的 **DB Instances and Companies** 確認 `WellShin_VN_Test_260428` 是「Database Name」，不是公司名稱，並確認該公司狀態正常。
- 公司與帳密都確定正確：請 SAP 管理員檢查該公司資料庫與 `SBO-COMMON` 是否為相同 SAP B1 Patch Level，以及 Service Layer 是否已註冊正確的 DB Instance。

也可以暫時把網址從 v2 改為 v1，確認是否為版本配置問題：

```csharp
var baseUrl =
    "https://EC2AMAZ-IGRTDRC:50000/b1s/v1/";
```

如果 v1 仍回 `206`，就完全可以排除 OData v2/v4 的差異，問題確實在 SAP 登入資料或公司註冊設定。

---

<a id="q23"></a>

## S099-Q023｜Console.Write("請輸入 manager 的 SAP B1 密碼："); var password = Console.ReadLine() ?? ""; 改一個完整版…

日期：2026-08-21

**你的提問**

## My request:
Console.Write("請輸入 manager 的 SAP B1 密碼：");
var password = Console.ReadLine() ?? "";

改一個完整版的給我

**當時回答**

請把 `Program.cs` 原本內容全部刪除，完整貼上以下程式。這個版本會在執行時要求輸入密碼，先做「登入＋查詢業務夥伴」，不會新增資料。

```csharp
using System.Net;
using System.Net.Http.Json;

// ========================================
// SAP Business One Service Layer 設定
// ========================================

var baseUrl =
    "https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";

var companyDb = "WellShin_VN_Test_260428";
var userName = "manager";

// 執行時輸入密碼，避免密碼留在 Program.cs
Console.Write("請輸入 manager 的 SAP B1 密碼：");
var password = Console.ReadLine() ?? "";

Console.WriteLine();
Console.WriteLine($"CompanyDB：{companyDb}");
Console.WriteLine($"UserName ：{userName}");
Console.WriteLine();

// ========================================
// 建立 HTTP 連線及 Cookie Session
// ========================================

var cookies = new CookieContainer();

var handler = new HttpClientHandler
{
    CookieContainer = cookies,
    UseCookies = true,

    // 僅供目前內部測試。
    // 正式環境必須安裝有效憑證，並移除此設定。
    ServerCertificateCustomValidationCallback =
        HttpClientHandler.DangerousAcceptAnyServerCertificateValidator
};

using var client = new HttpClient(handler)
{
    BaseAddress = new Uri(baseUrl),
    Timeout = TimeSpan.FromSeconds(60)
};

// ========================================
// 執行 SAP B1 API
// ========================================

try
{
    // 1. 登入
    Console.WriteLine("正在登入 SAP B1...");

    var loginResponse = await client.PostAsJsonAsync(
        "Login",
        new
        {
            CompanyDB = companyDb,
            UserName = userName,
            Password = password
        }
    );

    var loginResult =
        await loginResponse.Content.ReadAsStringAsync();

    if (!loginResponse.IsSuccessStatusCode)
    {
        Console.WriteLine("登入失敗：");
        Console.WriteLine(loginResult);
        return;
    }

    Console.WriteLine("登入成功！");
    Console.WriteLine();

    // 2. 查詢前 10 筆業務夥伴
    Console.WriteLine("正在查詢業務夥伴...");

    var businessPartnerResponse = await client.GetAsync(
        "BusinessPartners" +
        "?$select=CardCode,CardName,CardType" +
        "&$top=10"
    );

    var businessPartnerResult =
        await businessPartnerResponse.Content.ReadAsStringAsync();

    if (businessPartnerResponse.IsSuccessStatusCode)
    {
        Console.WriteLine("查詢成功：");
        Console.WriteLine(businessPartnerResult);
    }
    else
    {
        Console.WriteLine("查詢失敗：");
        Console.WriteLine(businessPartnerResult);
    }

    // 3. 登出
    var logoutResponse =
        await client.PostAsync("Logout", null);

    if (logoutResponse.IsSuccessStatusCode)
    {
        Console.WriteLine();
        Console.WriteLine("SAP B1 已登出。");
    }
    else
    {
        Console.WriteLine();
        Console.WriteLine("SAP B1 登出失敗。");
    }
}
catch (HttpRequestException ex)
{
    Console.WriteLine("HTTP 或 SSL 連線失敗：");
    Console.WriteLine(ex.ToString());
}
catch (TaskCanceledException)
{
    Console.WriteLine("連線逾時，請確認 Service Layer 是否正常。");
}
catch (Exception ex)
{
    Console.WriteLine("程式發生錯誤：");
    Console.WriteLine(ex.ToString());
}
```

操作步驟：

```powershell
notepad Program.cs
```

貼上後儲存並關閉記事本，再執行：

```powershell
dotnet run
```

出現：

```text
請輸入 manager 的 SAP B1 密碼：
```

輸入密碼並按 Enter。這個簡易版本輸入時會顯示密碼，所以不要截圖，也不要把密碼貼到對話中。

若登入成功，會接著顯示：

```text
登入成功！
正在查詢業務夥伴...
查詢成功：
```

如果完整版本仍回傳：

```text
code: 206
Invalid login credential.
```

就能排除 C# 密碼特殊字元問題，需要確認 `manager` 是否能以相同密碼直接登入 `WellShin_VN_Test_260428` 公司。

---

<a id="q24"></a>

## S099-Q024｜還是失敗

日期：2026-08-21

**你的提問**

## My request:
還是失敗

**當時回答**

既然改成執行時輸入密碼仍回 `206`，就已排除 C# 語法及密碼特殊字元問題。現在問題在 SAP 驗證端。

請先做唯一關鍵測試：

1. 開啟 SAP Business One 用戶端。
2. 選擇 `WellShin_VN_Test_260428`。
3. 不使用 Windows SSO。
4. 手動輸入 `manager` 和同一組密碼登入。

結果判斷：

- SAP B1 用戶端也登入失敗：請管理員重設 `manager` 密碼或解除鎖定。
- SAP B1 用戶端登入成功：請貼出 SLD → **DB Instances and Companies** 裡該公司的畫面，我要確認你填的是 Database Name，而不是 Company Name。
- 找不到該公司：表示它可能尚未正確註冊於 SLD／Service Layer。

也請再測一次 v1。把：

```csharp
"https://EC2AMAZ-IGRTDRC:50000/b1s/v2/";
```

改成：

```csharp
"https://EC2AMAZ-IGRTDRC:50000/b1s/v1/";
```

然後：

```powershell
dotnet run
```

若 v1 仍是 `206`，就不需再改程式；應由 SAP 管理員檢查帳號、公司註冊、`SBO-COMMON` 與公司資料庫版本。下一步請提供 **DB Instances and Companies 畫面**，不要提供密碼。

---

<a id="q25"></a>

## S099-Q025｜還是失敗

日期：2026-08-21

**你的提問**

## My request:
還是失敗

**當時回答**

目前不用再改程式。`v1`、`v2` 都回傳 `206 Invalid login credential`，代表 Service Layer 明確拒絕這組公司與帳密。

請只確認一件事：

**你能否在 SAP Business One 桌面用戶端，選擇 `WellShin_VN_Test_260428`，並用 `manager`＋同一組密碼手動登入成功？**

- 如果不能：請 SAP 管理員重設 `manager` 密碼或解除帳號鎖定。
- 如果可以：請貼出 SLD → **DB Instances and Companies** 中該公司的畫面，我再幫你確認正確的 `CompanyDB`。請勿提供密碼。

沒有這項結果，再繼續改 C# 程式也不會解決 `206`。

---

<a id="q26"></a>

## S099-Q026｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-1b259610-a17e-48e5-bb30-4248e45cff74.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1b259610-a17e-48e5-bb30-4248e45cff74.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

從這張 SLD 畫面看，資料庫名稱確實是：

```text
WellShin_VN_Test_260428
```

所以你的設定：

```csharp
var companyDb = "WellShin_VN_Test_260428";
```

是正確的，不需要修改。

現在 `206 Invalid login credential` 幾乎可以確定是：

- `manager` 密碼不正確；
- `manager` 被鎖定或停用；
- 平常使用 Windows SSO 登入，輸入的並非 SAP B1 原生密碼；
- 使用者代碼不是 `manager`。

請在 SAP Business One 桌面程式中：

1. 選擇 `WellShin_VN_Test_260428`。
2. 關閉／不要使用 Windows SSO。
3. 使用者代碼輸入 `manager`。
4. 手動輸入同一組密碼。

如果手動登入失敗，請 SAP 管理員重設 `manager` 的 SAP B1 密碼。若手動登入成功但 API 仍失敗，請管理員重新啟動 Service Layer，並確認公司資料庫與 `SBO-COMMON` 的 SAP B1 Patch Level 相同。

目前程式及 `CompanyDB` 都已確認，暫時不需要再修改 C#。

---

<a id="q27"></a>

## S099-Q027｜dk3u3

日期：2026-08-21

**你的提問**

## My request:
dk3u3

**當時回答**

如果這是 SAP B1 密碼，請不要貼在聊天室。由於已經暴露，建議立即請管理員變更密碼。

執行：

```powershell
dotnet run
```

看到密碼提示後，直接在 PowerShell 輸入新密碼並按 Enter，不要寫入 `Program.cs`，也不要再貼給我。

---

<a id="q28"></a>

## S099-Q028｜可以登入

日期：2026-08-21

**你的提問**

## My request:
可以登入

**當時回答**

很好，代表 Service Layer、公司資料庫及 SAP 帳密都正確，API 串接已成功。

如果 PowerShell 顯示：

```text
登入成功！
正在查詢業務夥伴...
查詢成功：
```

下一步就能建立測試業務夥伴。請先確認目前是測試資料庫，再把以下程式放在「登出」之前：

```csharp
Console.WriteLine();
Console.WriteLine("正在建立測試業務夥伴...");

var newBusinessPartner = new
{
    CardCode = "C_API_001",
    CardName = "API 測試客戶",
    CardType = "cCustomer",
    Phone1 = "02-12345678",
    EmailAddress = "customer@example.com"
};

var createResponse = await client.PostAsJsonAsync(
    "BusinessPartners",
    newBusinessPartner
);

var createResult =
    await createResponse.Content.ReadAsStringAsync();

if (createResponse.IsSuccessStatusCode)
{
    Console.WriteLine("業務夥伴建立成功！");
}
else
{
    Console.WriteLine("業務夥伴建立失敗：");
}

Console.WriteLine(createResult);
```

執行：

```powershell
dotnet run
```

成功後，進入 SAP B1 的「業務夥伴主檔資料」，查詢：

```text
C_API_001
```

如果先前貼出的字串是實際密碼，仍建議立即更換。

---

<a id="q29"></a>

## S099-Q029｜可以登入 我是說桌面板

日期：2026-08-21

**你的提問**

## My request:
可以登入 我是說桌面板

**當時回答**

了解：**SAP B1 桌面版可以登入，但 Service Layer API 仍回傳 206**。這表示密碼本身可能正確，但 API 驗證環境與桌面登入方式不同。

請先確認桌面版登入時：

- 沒有勾選「使用 Windows 帳戶／Single Sign-On」。
- 手動輸入的使用者代碼確實是 `manager`。
- 選擇的公司確實是 `WellShin_VN_Test_260428`。
- 使用的密碼與 PowerShell 輸入完全相同。

若以上都正確，下一個最有效的測試是請 SAP 管理員建立一個暫時的 SAP B1 原生使用者，例如：

```text
使用者代碼：API_TEST
密碼：設定一組新的測試密碼
```

並確認：

- 使用者未鎖定、未停用。
- 已指派可用的 SAP B1 License。
- 可以手動登入該測試公司。
- 不是只透過 Windows SSO／IAM 登入。

程式只需改：

```csharp
var userName = "API_TEST";
```

再執行：

```powershell
dotnet run
```

如果 `API_TEST` 可以登入，代表原本 `manager` 的密碼、帳號綁定或 SSO 設定有問題；如果仍回 `206`，則要由 SAP 管理員檢查 Service Layer 與 SLD 的公司驗證設定及日誌。現在不需要再修改 C# 登入程式。

---

<a id="q30"></a>

## S099-Q030｜一樣失敗

日期：2026-08-21

**你的提問**

## My request:
一樣失敗

**當時回答**

兩個帳號在桌面版可登入、API 都回 `206`，已可判定問題在 **Service Layer 伺服器端驗證設定**，不是 C#。先不要繼續修改程式或密碼。

請在伺服器開啟：

```text
https://EC2AMAZ-IGRTDRC:40000/ServiceLayerController
```

接著：

1. 進入 **Monitor**。
2. 再執行一次：

```powershell
dotnet run
```

3. 回到 Monitor 查看最新的錯誤請求：
   
```text
POST /b1s/v2/Login
```

4. 打開該筆錯誤的 Detail，將錯誤畫面貼給我；請遮住密碼、Session及敏感資料。

也可請 SAP 管理員檢查：

```text
C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer\logs
```

重點檔案：

```text
Securityevent.YYYYMMDD.b1s.log
error_50000_log_<日期>
```

伺服器端需確認：

- Service Layer Node 是否連到正確 DB Instance。
- `SBO-COMMON` 與 `WellShin_VN_Test_260428` 是否為相同 Patch Level。
- Service Layer 使用的 SQL 資料庫帳號是否具有必要權限。
- 系統若已啟用 IAM／SSO，是否限制了傳統 `CompanyDB + UserName + Password` 登入。
- Service Layer Controller 中各 Node 狀態是否正常。

官方也建議透過 Controller 的 Monitor 或下載 Service Layer logs 排查請求。[SAP Service Layer 日誌監控](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3f62fbde7a/2d1760e8de294e5b83cd4ebe98972b08.html)

下一步需要的是 **Monitor 中該次 `/Login` 的詳細錯誤**，不是再嘗試不同 C# 寫法。

---

<a id="q31"></a>

## S099-Q031｜Monitor。 這是甚麼

日期：2026-08-21

**你的提問**

## My request:
Monitor。 這是甚麼

**當時回答**

`Monitor` 是 **Service Layer Controller 裡的 API 請求監控頁面**，可以查看登入請求成功或失敗的伺服器端原因。

它不是 Windows 的「工作管理員」，也不是 SAP B1 桌面版功能。

請開啟：

```text
https://EC2AMAZ-IGRTDRC:40000/ServiceLayerController
```

登入後，上方可能會看到類似頁籤：

```text
Service Layer Settings
Monitor
Download Logs
```

進入 `Monitor` 後：

1. 在 PowerShell 再執行一次：

```powershell
dotnet run
```

2. 回到瀏覽器重新整理 Monitor。
3. 尋找最新的錯誤請求：

```text
POST /b1s/v2/Login
```

4. 點開錯誤詳細內容。

如果你的 Controller 沒有 `Monitor`，可能是 SAP B1 版本或權限不同。請直接截圖 `ServiceLayerController` 登入後的完整畫面，我可以告訴你應該點哪裡。不要在截圖中顯示任何密碼。

---

<a id="q32"></a>

## S099-Q032｜4. 打開該筆錯誤的 Detail，將錯誤畫面貼給我；請遮住密碼、Session及敏感資料。 也可請 SAP 管理員檢查： 空白得

日期：2026-08-21

**你的提問**

## My request:
4. 打開該筆錯誤的 Detail，將錯誤畫面貼給我；請遮住密碼、Session及敏感資料。
也可請 SAP 管理員檢查： 空白得

**當時回答**

Detail 顯示空白，通常代表 **Service Layer 的詳細 Request／Response Log 尚未啟用**，不是你的操作錯誤。

先不要重啟服務，直接查看現有日誌。在 SAP 伺服器按 `Win + R`，貼上：

```text
C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer\logs
```

尋找今天或最新修改的檔案，例如：

```text
Securityevent.20260821.b1s.log
error_50000_log_20260821
```

用記事本開啟後搜尋：

```text
manager
```

或：

```text
Login
```

請貼出登入失敗前後幾行，但遮住：

- 密碼
- `B1SESSION`
- `ROUTEID`
- Token
- 伺服器外部 IP 等敏感資料

如果資料夾內沒有相關記錄，請由 SAP 管理員在 Service Layer Controller 的設定中暫時啟用：

```text
Request & Response Logs
Enable Access Log
```

設定通常要重新啟動 Service Layer 才生效；重新啟動會中斷現有 API Session，因此要先確認沒有正式串接正在使用，並安排適當時間。啟用後再執行一次：

```powershell
dotnet run
```

取得日誌後應關閉詳細 Request／Response Logs，避免日誌快速增長或記錄敏感內容。

---

<a id="q33"></a>

## S099-Q033｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-6908923f-571b-4113-a1d2-a94afc501b64.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6908923f-571b-4113-a1d2-a94afc501b64.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這張圖確認了：`Monitor` 完全沒有資料，代表 Service Layer 的請求日誌目前沒有啟用，因此不是你找錯地方。

下一步先不要重新啟動或儲存任何設定：

1. 點左上角 **Service Layer Settings**。
2. 往下找以下選項：

```text
Request & Response Logs
Enable Access Log
Log Level
```

3. 把包含這些選項的畫面截圖貼給我。

通常需要暫時勾選：

```text
Request & Response Logs
Enable Access Log
```

並套用、重新啟動 Service Layer 後，Monitor 才會開始顯示：

```text
POST /b1s/v2/Login
```

但重啟可能中斷正在使用的 API，所以請先提供 Settings 畫面，確認選項及目前 Service Layer Node 狀態後再操作。

---

<a id="q34"></a>

## S099-Q034｜空的

日期：2026-08-21

**你的提問**

## My request:
空的

**當時回答**

如果 **Service Layer Settings 也完全空白**，表示 Controller 頁面沒有正常載入設定資料，暫時不用再操作 Controller。

先直接查看伺服器日誌。請在 PowerShell 執行：

```powershell
Get-ChildItem `
  "C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer\logs" |
  Sort-Object LastWriteTime -Descending |
  Select-Object -First 20 Name, Length, LastWriteTime
```

請把執行結果截圖貼上來，不要開啟或貼出含密碼、Token、Cookie 的內容。

如果顯示「找不到路徑」，再執行：

```powershell
Get-ChildItem `
  "C:\Program Files\SAP" `
  -Directory |
  Select-Object FullName
```

另外可先嘗試：

- 按 `Ctrl + F5` 強制重新整理 Controller。
- 使用 Edge 或 Chrome 的無痕視窗重新登入。
- 從 SLD → Services → Service Layer 的 `ServiceLayerController` 連結重新進入。

Controller 空白不影響我們判斷 C#；目前要確認的是伺服器是否產生 Service Layer 錯誤日誌。

---

<a id="q35"></a>

## S099-Q035｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-9f12bc7f-8edc-4d35-9d24-cd59ad2e89b8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9f12bc7f-8edc-4d35-9d24-cd59ad2e89b8.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這表示你已找到正確的 Service Layer 日誌，但目前 Windows 帳號沒有讀取權限。這不是 SAP API 的新錯誤。

請用系統管理員身分開啟記事本：

1. 按 Windows 開始。
2. 搜尋「記事本」。
3. 右鍵 → **以系統管理員身分執行**。
4. 在記事本選擇「檔案 → 開啟」。
5. 開啟：

```text
C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer\logs\error_50000_log_2026_08_21
```

開啟視窗右下角的檔案類型要改成：

```text
所有檔案 (*.*)
```

也可以用「系統管理員 PowerShell」讀取最後 100 行：

```powershell
Get-Content `
  -LiteralPath "C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer\logs\error_50000_log_2026_08_21" `
  -Tail 100
```

請先執行一次：

```powershell
dotnet run
```

讓錯誤重新產生，再立刻讀取日誌。貼出與 `Login`、`206`、`error` 有關的幾行即可，並遮住：

- 密碼
- Cookie／Session ID
- Token
- 公開 IP
- 其他敏感資訊

請只讀取日誌，不要編輯或儲存這個檔案。

---

<a id="q36"></a>

## S099-Q036｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-4d61ce18-f1b8-451c-b465-1b67b991d496.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-4d61ce18-f1b8-451c-b465-1b67b991d496.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這三行不是 API 登入失敗原因。它們是 Apache `proxy_balancer` 對 Controller／Balancer Manager 跨站存取參數的警告：

```text
AH0187: ignoring params in balancer-manager cross-site access
```

這可能解釋 Controller 頁面為何空白，但沒有記錄 `POST /Login` 或錯誤 `206`。

請用系統管理員 PowerShell 執行下面指令，尋找真正的 Service Layer 驗證日誌：

```powershell
Get-ChildItem `
  "C:\ProgramData\SAP\SAP Business One\Log" `
  -Recurse `
  -File `
  -ErrorAction SilentlyContinue |
Where-Object {
    $_.Name -match "b1logger|Securityevent"
} |
Sort-Object LastWriteTime -Descending |
Select-Object -First 20 FullName, LastWriteTime
```

也搜尋 Service Layer 安裝目錄：

```powershell
Get-ChildItem `
  "C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer" `
  -Recurse `
  -File `
  -ErrorAction SilentlyContinue |
Where-Object {
    $_.Name -match "b1logger|Securityevent"
} |
Sort-Object LastWriteTime -Descending |
Select-Object -First 20 FullName, LastWriteTime
```

接著：

1. 執行一次 `dotnet run`，重現錯誤。
2. 找修改時間最新的 `b1logger` 或 `Securityevent` 檔案。
3. 用系統管理員記事本開啟。
4. 搜尋 `manager`、`Login`、`206` 或 `Invalid`。

請貼出相關幾行並遮住敏感資訊。現在看到的 `AH0187` 可以先忽略，它不是帳密被拒絕的具體原因。

---

<a id="q37"></a>

## S099-Q037｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-fef992b4-0288-4f16-b279-32172da79457.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-fef992b4-0288-4f16-b279-32172da79457.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

畫面沒有任何結果，原因可能有兩個：

1. PowerShell 不是以系統管理員身分開啟；標題列沒有顯示「系統管理員」。
2. 日誌檔名不包含 `b1logger` 或 `Securityevent`。

請先關閉這個 PowerShell，再搜尋「Windows PowerShell」，右鍵選：

```text
以系統管理員身分執行
```

標題列應顯示：

```text
系統管理員: Windows PowerShell
```

然後執行較完整的列檔指令：

```powershell
$logRoot = "C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer\logs"

Get-ChildItem `
  -LiteralPath $logRoot `
  -Recurse `
  -File |
Sort-Object LastWriteTime -Descending |
Select-Object -First 30 FullName, Length, LastWriteTime |
Format-List
```

如果仍找不到登入相關檔案，再執行：

```powershell
$logRoot = "C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer\logs"

Get-ChildItem `
  -LiteralPath $logRoot `
  -Recurse `
  -File |
Select-String `
  -Pattern "Invalid login credential","Login","206" |
Select-Object -Last 30 Path, LineNumber, Line |
Format-List
```

執行第二段前先跑一次：

```powershell
cd C:\Users\CU02\SapB1ApiDemo
dotnet run
```

讓最新登入錯誤寫入日誌。請將搜尋結果截圖貼出來，並遮住密碼、Session、Cookie 或 Token。

---

<a id="q38"></a>

## S099-Q038｜圖片／附件提問

日期：2026-08-21

**你的提問**

# Files mentioned by the user:

## codex-clipboard-09df1f9c-00da-443e-b842-d865f20b31a9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-09df1f9c-00da-443e-b842-d865f20b31a9.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這次沒有任何結果，表示目前伺服器沒有產生 `b1logger` 或 `Securityevent` 日誌。結合目前狀況：

- Service Layer `50000` 可以連線。
- C# 能送出 `/Login`。
- 多個可登入桌面版的帳號都收到 `206`。
- Service Layer Controller 的 Settings／Monitor 都是空白。
- Apache 日誌出現 `AH0187 balancer-manager cross-site access`。
- 驗證日誌沒有產生。

問題已確定在 **Service Layer Controller／Service Layer 伺服器配置**，不是你的 C# 程式。接下來需要 SAP 系統管理員處理。

可以直接將以下內容交給管理員：

> SAP B1 Service Layer `https://EC2AMAZ-IGRTDRC:50000/b1s/v1/Login` 與 `/b1s/v2/Login`，使用可登入桌面版 `WellShin_VN_Test_260428` 的原生 SAP B1 帳號，皆回傳 `206 Invalid login credential`。Service Layer Controller 的 Settings 與 Monitor 為空白，Apache error log 出現 `AH0187 ignoring params in balancer-manager cross-site access`，且沒有產生 Securityevent／b1logger 日誌。請檢查 Service Layer Node、SLD DB Instance、服務帳號資料庫權限、SBO-COMMON 與公司資料庫 Patch Level，並修復 Controller／啟用詳細日誌後重啟 Service Layer。

重啟會影響現有 API Session，應由管理員安排時間執行。修復完成後，你目前的 `Program.cs` 可以直接重新測試，不需要再改登入程式。另請移除正式環境中的憑證略過設定，並更換先前可能已暴露的密碼。

---

<a id="q39"></a>

## S099-Q039｜我要怎麼檢查 因為我就是管理員

日期：2026-08-21

**你的提問**

我要怎麼檢查 因為我就是管理員

**當時回答**

既然你就是管理員，我們可以直接從伺服器端檢查。先做「唯讀檢查」，暫時不要重啟或修改資料庫。

## 第一步：確認 Service Layer 程序與連接埠

用「系統管理員身分」開啟 PowerShell，執行：

```powershell
Test-NetConnection EC2AMAZ-IGRTDRC -Port 50000
```

正常應顯示：

```text
TcpTestSucceeded : True
```

再執行：

```powershell
Get-NetTCPConnection `
  -LocalPort 50000 `
  -State Listen `
  -ErrorAction SilentlyContinue |
Select-Object LocalAddress, LocalPort, OwningProcess
```

接著找出該程序：

```powershell
$slProcessId = (
    Get-NetTCPConnection `
      -LocalPort 50000 `
      -State Listen `
      -ErrorAction SilentlyContinue |
    Select-Object -First 1 -ExpandProperty OwningProcess
)

Get-Process -Id $slProcessId |
Select-Object Id, ProcessName, Path
```

請把結果截圖給我。

## 第二步：查看 Service Layer 服務

執行：

```powershell
Get-CimInstance Win32_Service |
Where-Object {
    $_.Name -match "SAP|B1|ServiceLayer" -or
    $_.DisplayName -match "SAP|Business One|Service Layer"
} |
Select-Object Name, DisplayName, State, StartMode |
Format-Table -AutoSize
```

確認相關服務是否為：

```text
State     : Running
StartMode : Auto
```

請把結果截圖給我。

## 第三步：定位設定檔

執行：

```powershell
$slRoot =
    "C:\Program Files\SAP\SAP Business One ServerTools\ServiceLayer"

Get-ChildItem `
  -LiteralPath $slRoot `
  -Recurse `
  -File `
  -Include "b1s.conf","httpd-b1s-lb.conf","httpd-b1s-lb-member*.conf" |
Select-Object FullName, LastWriteTime |
Format-List
```

如果找到 `b1s.conf`，只讀取非敏感設定：

```powershell
$b1sConfig = Get-ChildItem `
  -LiteralPath $slRoot `
  -Recurse `
  -File `
  -Filter "b1s.conf" |
Select-Object -First 1

Select-String `
  -LiteralPath $b1sConfig.FullName `
  -Pattern "LogLevel|EnableAccessLog|SessionTimeout|SLD|License" |
Select-Object LineNumber, Line
```

貼出結果前，請遮住帳號、密碼、Token及連線字串。

## 第四步：確認版本一致

進入 SLD：

```text
https://EC2AMAZ-IGRTDRC:40000/ControlCenter
```

依序檢查：

```text
Components
DB Instances and Companies
```

請確認：

- SAP Business One Server Tools 版本。
- Service Layer 版本。
- 公司 `WellShin_VN_Test_260428` 的版本。
- `SBO-COMMON` 與公司資料庫是否已用同一套 SAP B1 Server Components 升級。

SAP 官方要求 `SBO-COMMON` 與公司資料庫升級至相同版本，Service Layer 才能正常工作。[SAP Service Layer 管理說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_ADMIN_GUIDE_SQL/f6fb230cc90949d8b66586a39189992b/9b01e69e7c714025a46fa7ad22e28f62.html?locale=en-US)

## 第五步：檢查 Controller 空白問題

先在伺服器本機使用 Edge 開啟：

```text
https://localhost:40000/ServiceLayerController
```

如果憑證警告通過後仍是空白，再試：

```text
https://EC2AMAZ-IGRTDRC:40000/ServiceLayerController
```

避免混用：

```text
localhost
IP 位址
EC2AMAZ-IGRTDRC
```

因為你目前的 Apache 日誌有：

```text
AH0187 ... balancer-manager cross-site access
```

這可能是 Controller 透過不同主機名稱載入內部 Balancer Manager，導致畫面資料被拒絕。

也請按 `F12` → `Console`，重新整理頁面，查看是否有紅色 JavaScript、`401`、`403` 或 CORS 錯誤。

## 第六步：之後才考慮重新啟動

先完成前五步。如果版本和設定都正確，可以在維護時間透過 Service Layer Controller 執行重新啟動；官方說明也建議在 Controller 管理 Node、設定及下載日誌。[SAP Service Layer Settings](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/fca234d647c04d458726e0806c73c371.html)

先把「第一步與第二步」的 PowerShell 結果貼給我，我可以接著判斷是哪一個服務或 Node 出問題。

---

