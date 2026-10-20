# 規劃泛微OA與SAPB1串接溝通

來源編號：S080  
聊天 ID：`019ffdef-113c-7a51-9185-5b12d38b88a8`  
聊天起始日期（台北）：2026-08-14  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S080-Q001｜我想了解，，關於泛微OA如果要跟SAPB1串接，身為SAPB1工程師的我，應該怎麼跟對方談

日期：2026-08-14

**你的提問**

我想了解，，關於泛微OA如果要跟SAPB1串接，身為SAPB1工程師的我，應該怎麼跟對方談

**當時回答**

身為 SAP B1 工程師，你跟泛微 OA 團隊談串接時，不要先談「欄位怎麼對」，而要先確認：

> 哪個系統負責流程、哪個系統負責正式帳務，以及失敗時誰負責把資料救回來。

最常見、也比較健康的分工是：

- 泛微 OA：填單、簽核、附件、流程狀態、通知。
- SAP B1：主資料檢核、單據編號、庫存、價格、稅額、會計與正式過帳。
- 中介服務：轉換欄位、驗證、重送、記錄介接狀態。

## 第一次會議先談這六件事

### 1. 先把業務流程畫完整

不要只說「請購單要串 SAP」，要逐步確認：

1. OA 建單時要不要讀 SAP 主資料？
2. 送簽前是否檢查料號、供應商、成本中心及預算？
3. 哪個簽核節點才寫入 SAP？
4. 寫入 SAP 的是草稿、正式單據，還是自訂表？
5. SAP 建立成功後，要回傳哪些資料？
6. OA 退回、撤回、作廢時，SAP 要做什麼？
7. SAP 後續狀態變更，要不要同步回 OA？

你可以直接問：

> 「請提供完整流程圖，包含正常、退回、撤回、作廢、重送及 SAP 建立失敗的分支。」

### 2. 決定每一項資料的主系統

建議做一張表：

| 資料 | 建議主系統 | OA 的角色 |
|---|---|---|
| 客戶、供應商 | SAP B1 | 查詢或申請新增 |
| 品項、倉庫 | SAP B1 | 選取 |
| 科目、成本中心、專案 | SAP B1 | 選取 |
| 價格、稅額、匯率 | 原則上 SAP B1 | 顯示試算值 |
| 簽核人與流程 | OA | SAP 保留結果 |
| 正式單據號碼 | SAP B1 | 接收並顯示 |
| OA 表單號 | OA | 寫入 SAP UDF 供追蹤 |
| 附件 | 雙方議定 | 保存 URL、檔案或附件編號 |

核心原則是：不要讓 OA 自行複製一套 SAP 商業規則。否則價目表、稅碼、匯率、庫存與權限一改，兩邊結果就會不一致。

### 3. 確認寫入 SAP 的單據階段

這通常是會議中最重要的問題。

例如採購流程，可能有三種做法：

- OA 核准後建立 SAP 採購申請。
- OA 核准後建立 SAP 採購訂單草稿。
- OA 核准後直接建立正式採購訂單。

你應該請財務、採購和 OA 顧問共同確認，而不是由程式人員決定。尤其要問：

> 「如果 SAP 本身還有 Approval Procedure，OA 核准後是否還要再跑一次 SAP 簽核？」

SAP 官方文件也特別指出，透過 Service Layer 新增訂單時，如果 SAP 啟用了審批程序，有可能實際建立的是 Draft，而不是正式單據；因此這一點必須在設計及測試時明確驗證。[SAP Service Layer API Reference](https://help.sap.com/doc/056f69366b5345a386bb8149f1700c19/10.0/en-US/Service%20Layer%20API%20Reference.html)

## 技術架構怎麼談

如果是 SAP B1 10.0，通常優先評估 Service Layer：

```text
泛微 OA
   ↓ HTTPS／JSON
中介 API 或整合平台
   ↓ OData v4
SAP B1 Service Layer
   ↓
SAP B1 公司資料庫
```

Service Layer 是 SAP B1 官方提供的 HTTP/OData 介面，能操作 SAP 商業物件；目前官方建議以 OData v4 為主，路徑通常是 `/b1s/v2`。[SAP Service Layer 說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/60c7a0b745bd486589f05a1da77041f3.html?locale=en-US)

其他可能方式：

- Service Layer：適合跨平台、REST/JSON、HANA 或支援版本環境。
- DI API：適合既有 Windows/.NET 整合，但連線與部署方式較重。
- B1if：適合需要訊息轉換、排程、路由和整合情境管理；SAP 官方定位就是第三方系統整合框架。[SAP B1 Integration Framework](https://help.sap.com/docs/r/p/INTEGRATION_FRAMEWORK_FOR_SAP_BUSINESS_ONE)
- 直接寫 SAP 資料庫：應明確排除。SAP 標準交易表不應由 OA 直接 INSERT、UPDATE 或 DELETE。

先問清楚對方泛微的確切產品及版本，例如 e-cology 版本、部署方式、可用 API 模組與授權。不要接受只有一句「泛微支援 Web Service」；請對方實際提供：

- API 文件與版本。
- 認證方式。
- OA 主動呼叫外部 API 的能力。
- SAP 回呼 OA 的 API。
- 附件上傳、下載規格。
- Timeout、重試與併發限制。
- 測試環境及測試帳號。
- API 模組是否另需授權。

## 一定要談的可靠性問題

### 防止重複建單

OA 呼叫 SAP 超時，不代表 SAP 沒有成功。OA 如果直接重送，可能產生兩張採購單。

建議每筆交易帶固定的唯一鍵，例如：

```text
SourceSystem = FANWEI
OARequestId  = OA-20260814-00125
Operation    = CREATE_PO
```

SAP 端把 OA 表單號寫入 UDF；每次新增前先檢查該唯一鍵是否已存在。相同請求再次送來，應回傳原本的 `DocEntry/DocNum`，而不是再新增一張。

你可以直接要求：

> 「所有新增介面都必須支援冪等性，不能把人工查重當成防呆。」

### 錯誤處理

請對方區分：

- 業務錯誤：料號不存在、供應商停用、期間鎖定、成本中心無效。
- 權限錯誤：整合帳號沒有建立該單據的權限。
- 系統錯誤：SAP、Service Layer 或網路無法連線。
- 不確定結果：請求超時，但不知道 SAP 是否已建立。

OA 畫面不能只顯示「串接失敗」，至少要保存：

- OA 表單號。
- 請求時間與介面名稱。
- SAP 公司別。
- Request ID。
- SAP 回傳錯誤代碼與可閱讀訊息。
- 重送次數。
- SAP `DocEntry` 與 `DocNum`。
- 原始資料的遮罩版紀錄。

### 交易一致性

如果一張 OA 表單要建立多個 SAP 物件，要明確談清楚：

- 第一張成功、第二張失敗怎麼處理？
- 是否能整批回滾？
- 無法回滾時，補償動作是取消、關閉還是人工處理？

Service Layer 是無狀態介面；多項操作可評估使用 OData batch，但不能光靠一句「放在同一次 API」就假設具有你期待的跨流程交易效果，必須用實際情境驗證。[SAP Transaction APIs](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/8fd7bbb52f774afc8b58b1bd07e08a8b.html)

## 權限與資安要怎麼要求

至少包含：

- 使用獨立 SAP 技術帳號，不共用 manager。
- 依單據與功能配置最小權限。
- HTTPS 與有效憑證。
- 密碼、Token 不寫在程式碼或紀錄檔。
- 正式與測試環境分離。
- 限制來源 IP 或經由 API Gateway／反向代理。
- 個資、付款資料及附件內容需要遮罩。
- 明確定義 Token／Session 過期與重新登入策略。

SAP 官方說明也指出，技術使用者仍需在 SAP B1 中取得相應的功能權限。[SAP B1 IAM／Service Layer](https://help.sap.com/docs/SAP_BUSINESS_ONE_IAM/548d6202b2b6491b824a488cfc447343/850cec6c0ecc477f86a637632c5e5471.html)

## 你可以在會議上直接這樣說

> 「我們先不從 API 欄位開始。請先確認每個流程在什麼節點寫入 SAP、建立 Draft 還是正式單據，以及撤回和作廢時的處理方式。SAP 是帳務與商業規則的最終系統，OA 負責申請和簽核。技術上我方可以提供 Service Layer 或中介 API，但需要你們提供泛微版本、API 規格、認證方式、回呼能力及附件機制。所有新增交易都要有 OA 唯一單號、防重、完整日誌與可重送設計，最後雙方用欄位 mapping 和測試案例驗收。」

## 會後要求對方交付

至少取得以下文件後再估工：

1. 流程圖與例外流程。
2. 系統責任矩陣。
3. Interface List。
4. 欄位 Mapping 文件。
5. Code Mapping，例如稅碼、幣別、倉庫及成本中心。
6. API Request/Response 範例。
7. 錯誤碼及重送規則。
8. 權限與網路架構。
9. 測試案例及驗收標準。
10. 上線切換、監控及維運責任。

最需要守住的底線是：不直接寫 SAP 標準資料表、不由 OA 重新實作 SAP 計價與會計邏輯、不接受沒有防重機制的新增單據介面。這三件事守住，後面的整合風險會少很多。

---

<a id="q2"></a>

## S080-Q002｜About SAP Business One Service Layer SAP Business One Service Layer is a new generation of…

日期：2026-08-14

**你的提問**

## My request:
About SAP Business One Service Layer
SAP Business One Service Layer is a new generation of extension API for consuming SAP Business One data and services. It builds on core protocols such as HTTP and OData, and provides a uniform way to expose full-featured business objects on top of a highly scalable and high-availability Web server. Currently, Service Layer supports OData version 3, version 4, and a few selected OData client libraries, for example, WCF for .Net developers; data.js for JavaScript developers.


甚麼意思

**當時回答**

這段是在說：

> SAP Business One Service Layer 是 SAP B1 官方提供的一個 Web API。外部系統可以透過網路，用標準 HTTP 請求讀取或操作 SAP B1 的商業物件。

拆開來看：

- **new generation of extension API**  
  新一代的擴充介面。相較於傳統 DI API，它比較適合 Web、雲端及跨平台整合。

- **consuming SAP Business One data and services**  
  外部程式可以讀取或操作 SAP B1，例如：
  
  - 查詢品項、供應商、庫存
  - 新增業務夥伴
  - 建立銷售訂單、採購訂單
  - 更新或取消單據
  - 呼叫 SAP B1 的商業服務

- **HTTP**  
  使用一般 Web 通訊方式，例如 `GET`、`POST`、`PATCH`、`DELETE`。

- **OData**  
  一套標準化的 API 查詢規則，可以篩選、排序及指定欄位。

- **full-featured business objects**  
  它不是直接讓你修改資料表，而是透過 SAP B1 的商業物件處理資料，因此仍會執行 SAP 的驗證和商業邏輯。

- **highly scalable and high-availability Web server**  
  Service Layer 是 Web 服務架構，設計上可支援多個呼叫端、負載平衡及高可用部署；但不代表安裝後自然就會高可用，仍需要相應的伺服器架構與設定。

## 實際例子

泛微 OA 想取得 SAP B1 品項，可以送出：

```http
GET https://sap-server:50000/b1s/v2/Items?$select=ItemCode,ItemName
```

Service Layer 會以 JSON 回傳：

```json
{
  "value": [
    {
      "ItemCode": "A001",
      "ItemName": "筆記型電腦"
    }
  ]
}
```

OA 核准後想建立 SAP 採購訂單，可以送出：

```http
POST https://sap-server:50000/b1s/v2/PurchaseOrders
Content-Type: application/json
```

```json
{
  "CardCode": "V0001",
  "DocDueDate": "2026-08-31",
  "DocumentLines": [
    {
      "ItemCode": "A001",
      "Quantity": 10,
      "WarehouseCode": "01"
    }
  ]
}
```

SAP 成功建立後，會回傳包含 `DocEntry`、`DocNum` 等資料，再由 OA 記錄。

## OData v3、v4 是什麼？

它們是不同版本的 OData 規格：

- `/b1s/v1`：OData v3
- `/b1s/v2`：OData v4

新開發通常優先採用 **OData v4，也就是 `/b1s/v2`**。但實際支援程度仍要依客戶的 SAP B1 版本、Feature Package、資料庫類型及既有程式確認。

## WCF、data.js 又是什麼？

它們只是開發人員用來呼叫 OData 的程式庫範例：

- WCF：偏向 Microsoft .NET 技術。
- data.js：JavaScript 技術。
- 也可以直接用 C# `HttpClient`、Java、Python、Postman 或泛微本身的 HTTP 功能呼叫。

並不是串接 Service Layer 一定要使用 WCF 或 data.js。

## 跟 DI API 最大的差別

| Service Layer | DI API |
|---|---|
| HTTP／OData Web API | COM 元件 API |
| JSON 格式 | SAP COM Object |
| 適合跨平台、Web 整合 | 主要適合 Windows/.NET |
| 通常透過 HTTPS 連線 | 需要安裝相應 DI API 元件 |
| OA 可透過網路呼叫 | 通常需要中介程式 |
| Web 服務架構 | 長連線式程式架構 |

所以對泛微 OA 廠商可以簡單說：

> 「SAP B1 這邊提供 Service Layer，它是一套 HTTPS、OData、JSON 格式的官方 API。你們不用直接操作 SAP 資料庫，可以透過它查主資料及建立 SAP 單據；但登入、權限、防重、錯誤重送及單據商業規則仍需要雙方共同設計。」

---

