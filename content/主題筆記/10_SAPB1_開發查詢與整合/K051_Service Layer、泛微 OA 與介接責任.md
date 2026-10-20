---
title: "Service Layer、泛微 OA 與介接責任"
category: "10_SAPB1_開發查詢與整合"
updated: 2026-10-20
sources: ["S043","S046","S080","S099","S100","S201"]
---

# Service Layer、泛微 OA 與介接責任

[回總覽](../../00_閱讀入口.md)

## 整合摘要

合併 OA 概念、泛微、Service Layer 部署／登入排查及串接分工。OA 負責流程，SAP 負責正式業務與帳務，中介服務處理映射、重送與紀錄；規劃、教學 Mock、實際連線與正式部署需分開標示。

> 整理原則：同主題合併收錄，完全相同的問答只列一次並保留來源；後續追問依日期保留。以下「歷史問答」不是全部可直接採用的最新操作手冊。沒有答案、缺少圖片或仍待測試的地方均不補寫結論。

## 來源與閱讀範圍

- [S043｜解釋 OA 系統](<../../原始問答/S043_解釋 OA 系統.md>)：2026-08-04，15 段問答。
- [S046｜查找泛微 OA EP](<../../原始問答/S046_查找泛微 OA EP.md>)：2026-08-04，2 段問答。
- [S080｜規劃泛微OA與SAPB1串接溝通](<../../原始問答/S080_規劃泛微OA與SAPB1串接溝通.md>)：2026-08-14，2 段問答。
- [S099｜查找 SAPB1 串接方法](<../../原始問答/S099_查找 SAPB1 串接方法.md>)：2026-08-21，39 段問答。
- [S100｜解釋這段內容](<../../原始問答/S100_解釋這段內容.md>)：2026-08-21，8 段問答。
- [S201｜SAP B1 Service Layer｜泛微 OA 資料串接](<../../原始問答/S201_SAP B1 Service Layer｜泛微 OA 資料串接.md>)：2026-09-24，1 段問答。

## 問題索引

- [01｜OA系統 解釋一下](#qa1)
- [02｜那如果有跨國分公司呢](#qa2)
- [03｜所以如果不同的分公司 就有不同的網址](#qa3)
- [04｜所以如果我跨國 是可以同地址](#qa4)
- [05｜如果依照資料庫的比例](#qa5)
- [06｜OA系統是哪個國家](#qa6)
- [07｜我想了解 如果要把 OA系統跟SAPB1 串接 是有可能的嗎](#qa7)
- [08｜可以幫我多做解釋這個中介平台嗎](#qa8)
- [09｜可以給我舉例幾個中介平台的程式或是](#qa9)
- [10｜B1IF是甚麼](#qa10)
- [11｜b1if很難嗎](#qa11)
- [12｜B1直接用service layer](#qa12)
- [13｜如果我想練習這部分 可以幫我排課程 我是完全零基礎](#qa13)
- [14｜建立一套簡易採購申請 API。申請核准後，自動在 SAP B1 測試公司建立採購單草稿，回寫 SAP 文件編號；失敗時保存原因並允許重新傳送。 可以寫一次給我看嗎](#qa14)
- [15｜先確認目前工作區與可用的 .NET 環境。 這個是甚麼](#qa15)
- [16｜泛微 oa EP](#qa16)
- [17｜泛微 OA 跟 SAPB1 串接](#qa17)
- [18｜我想了解，，關於泛微OA如果要跟SAPB1串接，身為SAPB1工程師的我，應該怎麼跟對方談](#qa18)
- [19｜About SAP Business One Service Layer SAP Business One Service Layer is a new generation of…](#qa19)
- [20｜SAPB1 串接方法](#qa20)
- [21｜API](#qa21)
- [22｜可以產生一個 API的教學嗎](#qa22)
- [23｜可以寫給我 我從 API 寫 業務夥伴主檔嗎 可以寫一個示範給我嗎](#qa23)
- [24｜Program.cs 這是甚麼意思](#qa24)
- [25｜圖片／附件提問](#qa25)
- [26｜圖片／附件提問](#qa26)
- [27｜圖片／附件提問](#qa27)
- [28｜圖片／附件提問](#qa28)
- [29｜通常 這個會是甚麼密碼](#qa29)
- [30｜所以 .NET是在幹嘛的](#qa30)
- [31｜圖片／附件提問](#qa31)
- [32｜dotnet new console -n SapB1ApiDemo cd SapB1ApiDemo notepad Program.cs 然後呢](#qa32)
- [33｜\](#qa33)
- [34｜圖片／附件提問](#qa34)
- [35｜圖片／附件提問](#qa35)
- [36｜這裡也要修改嗎](#qa36)
- [37｜那我寫對](#qa37)
- [38｜但還是不行](#qa38)
- [39｜圖片／附件提問](#qa39)
- [40｜var companyDb = "WellShin_VN_Test_260428"; var userName = "manager";](#qa40)
- [41｜圖片／附件提問](#qa41)
- [42｜Console.Write("請輸入 manager 的 SAP B1 密碼："); var password = Console.ReadLine() ?? ""; 改一個完整版…](#qa42)
- [43｜還是失敗](#qa43)
- [44｜還是失敗](#qa44)
- [45｜圖片／附件提問](#qa45)
- [46｜dk3u3](#qa46)
- [47｜可以登入](#qa47)
- [48｜可以登入 我是說桌面板](#qa48)
- [49｜一樣失敗](#qa49)
- [50｜Monitor。 這是甚麼](#qa50)
- [51｜4. 打開該筆錯誤的 Detail，將錯誤畫面貼給我；請遮住密碼、Session及敏感資料。 也可請 SAP 管理員檢查： 空白得](#qa51)
- [52｜圖片／附件提問](#qa52)
- [53｜空的](#qa53)
- [54｜圖片／附件提問](#qa54)
- [55｜圖片／附件提問](#qa55)
- [56｜圖片／附件提問](#qa56)
- [57｜圖片／附件提問](#qa57)
- [58｜我要怎麼檢查 因為我就是管理員](#qa58)
- [59｜這是甚麼意思](#qa59)
- [60｜這個好像是示範](#qa60)
- [61｜[〔連結〕 學習這個後 給我一個新的中文說明文件](#qa61)
- [62｜給我md檔](#qa62)
- [63｜[〔連結〕](#qa63)
- [64｜[下載 SAP Business One Service Layer 系統需求與部署指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08…](#qa64)
- [65｜[〔連結〕 這個呢](#qa65)
- [66｜[〔連結〕](#qa66)
- [67｜〔此紀錄未包含對應提問〕](#qa67)

## 合併後歷史問答

<a id="qa1"></a>

### 1. OA系統 解釋一下

來源：[S043-Q001](<../../原始問答/S043_解釋 OA 系統.md#q1>)；2026-08-04

**問題**

OA系統 解釋一下

**歷史回答**

OA 系統通常指「辦公自動化系統」（Office Automation System），把公司日常行政與審批流程集中到線上處理。

常見功能包括：

- 電子簽核：請假、加班、採購、付款、報銷等
- 人事管理：出勤、假勤、員工資料與薪資流程
- 文件管理：公文、合約、檔案版本及權限控管
- 工作協作：公告、行事曆、會議室預約、任務追蹤
- 流程管理：依金額、部門或職級自動安排審批人
- 系統整合：串接 ERP、CRM、電子郵件、財務及打卡系統

例如，員工申請購買電腦時：

```text
填寫申請 → 主管審核 → IT 確認規格 → 財務確認預算 → 採購下單
```

OA 系統可以自動將申請送到正確的人、提醒逾期簽核，並保留完整紀錄。

主要好處是減少紙本和人工傳遞、加快審批、降低遺漏，也方便查詢「目前卡在哪一關」。需要注意的是，若流程設計得太複雜，OA 反而可能讓簽核變慢，因此導入時應先整理及簡化公司的實際流程。

<a id="qa2"></a>

### 2. 那如果有跨國分公司呢

來源：[S043-Q002](<../../原始問答/S043_解釋 OA 系統.md#q2>)；2026-08-04

**問題**

那如果有跨國分公司呢

**歷史回答**

如果有跨國分公司，OA 系統就不能只處理「線上簽核」，還必須支援不同國家、語言、時區、法規和組織架構。通常會採用「全球統一平台＋各地彈性配置」。

```text
全球總部
├─ 共用：帳號、權限、核心流程、稽核標準、報表
├─ 台灣：中文介面、台灣假勤與簽核規則
├─ 日本：日文介面、日本行事曆與當地法規
└─ 美國：英文介面、美元、當地人資與隱私規範
```

需要特別考慮：

- 多語言：介面、表單及通知可以切換語言。
- 多時區：截止時間、出勤與通知按照使用者所在地計算。
- 多幣別：採購、報銷及預算要記錄原幣、匯率與換算幣別。
- 跨國簽核：例如台灣提出申請，由新加坡區域主管和美國總部財務共同核准。
- 彈性組織：同一名主管可能管理不同國家的團隊，權限不能只看部門。
- 在地法規：各國勞動、稅務、電子簽章及個資規定不同。
- 資料治理：決定哪些資料能跨境、存放在哪裡，以及誰能查看。
- 系統整合：各分公司可能使用不同的 ERP、薪資、打卡或身分驗證系統。
- 稽核追蹤：保留申請人、簽核人、時間、修改內容及登入紀錄。

例如跨國採購流程可以設計成：

```text
當地員工申請
→ 當地主管核准
→ 當地財務確認稅務與預算
→ 區域主管核准
→ 總部依金額門檻覆核
→ 回傳當地 ERP 執行採購
```

導入時，建議先統一全球共通的核心規則，例如權限原則、採購級距和稽核要求；假勤、稅務及法定表單則交由各國配置。最常見的失敗原因，是總部強迫所有國家使用完全相同的流程，忽略當地實務與法規。

<a id="qa3"></a>

### 3. 所以如果不同的分公司 就有不同的網址

來源：[S043-Q003](<../../原始問答/S043_解釋 OA 系統.md#q3>)；2026-08-04

**問題**

所以如果不同的分公司 就有不同的網址

**歷史回答**

不一定。跨國 OA 常見有三種網址設計：

1. 共用同一個網址（最常見）

例如 `oa.company.com`。員工登入後，系統依帳號自動顯示所屬分公司、語言、時區和當地流程。

優點是維護簡單、資料與報表容易整合，也方便跨國簽核。

2. 各分公司使用不同子網域

例如：

- 台灣：`tw.oa.company.com`
- 日本：`jp.oa.company.com`
- 美國：`us.oa.company.com`

外觀看似不同網址，但背後可能仍是同一套系統。適合需要各地品牌、登入方式、資料隔離或管理設定的企業。

3. 各分公司使用完全不同網址與系統

例如台灣使用自建 OA，日本使用當地供應商。自主性最高，但跨國簽核、帳號管理、資料整合及全球報表會比較困難。

一般建議採用「單一入口」：

```text
oa.company.com
      ↓
統一登入（SSO）
      ↓
依員工身分導向所屬分公司與對應流程
```

只有在各國法規要求資料隔離、網路環境差異大，或分公司必須獨立營運時，才比較需要不同網址甚至不同系統。換句話說，**分公司不同，不代表網址一定不同**；網址設計主要取決於系統架構、資料隔離和管理需求。

<a id="qa4"></a>

### 4. 所以如果我跨國 是可以同地址

來源：[S043-Q004](<../../原始問答/S043_解釋 OA 系統.md#q4>)；2026-08-04

**問題**

所以如果我跨國 是可以同地址

**歷史回答**

可以。跨國企業完全可以共用同一個 OA 網址，例如：

`https://oa.company.com`

不論員工在台灣、日本或美國，都從這個地址登入。系統登入後再根據帳號自動判斷：

- 所屬國家與分公司
- 顯示語言與時區
- 可使用的表單
- 當地簽核流程及法規
- 幣別與假勤制度
- 資料查看權限

因此可以做到「同一網址、不同內容與權限」。

不過，如果某些國家要求資料必須存放在境內，仍可維持同一個入口網址，再由後端連接不同地區的伺服器。實際設計時，還需要確認當地個資法、跨境資料傳輸、連線速度及資安要求。

<a id="qa5"></a>

### 5. 如果依照資料庫的比例

來源：[S043-Q005](<../../原始問答/S043_解釋 OA 系統.md#q5>)；2026-08-04

**問題**

如果依照資料庫的比例

**歷史回答**

如果你的意思是「依照資料庫來區分不同國家的分公司」，即使資料庫不同，仍然可以使用同一個 OA 網址。網址與資料庫不需要一對一。

例如：

```text
oa.company.com
      ↓
登入後判斷員工所屬分公司
      ├─ 台灣帳號 → 台灣資料庫
      ├─ 日本帳號 → 日本資料庫
      └─ 美國帳號 → 美國資料庫
```

常見資料庫架構有三種：

- 共用資料庫：所有分公司放在同一資料庫，用 `company_id` 或 `country_code` 區分。成本較低，適合規模較小、流程相近的分公司。
- 分公司獨立資料庫：每個國家一個資料庫。資料隔離較好，但跨國報表與維護比較複雜。
- 混合模式：員工帳號、組織架構等全球資料共用；薪資、個資、財務資料則存放於各國資料庫。跨國企業通常較適合這種方式。

所以，「同一網址」是登入入口；「不同資料庫」是後端如何儲存與隔離資料，兩者可以分開設計。若資料量比例差距很大，例如美國占 70%、台灣占 20%、日本占 10%，可以依實際負載配置不同容量，但不需要因此改變網址。

<a id="qa6"></a>

### 6. OA系統是哪個國家

來源：[S043-Q006](<../../原始問答/S043_解釋 OA 系統.md#q6>)；2026-08-04

**問題**

OA系統是哪個國家

**歷史回答**

OA 系統不是某一個國家的系統。

「OA」是英文 **Office Automation** 的縮寫，中文通常叫「辦公自動化系統」。它是一種系統類型，就像 ERP、CRM 一樣，世界各國的企業都會使用。

不過，「OA 系統」這個稱呼在中國大陸、台灣及其他華語地區較常見；歐美企業通常會稱為：

- Workflow Management System（工作流程管理系統）
- Business Process Management，BPM（商業流程管理）
- Digital Workplace（數位工作平台）
- Intranet／Employee Portal（企業內網／員工入口）

所以 OA 沒有特定國籍；實際是哪個國家的產品，要看提供系統的廠商。例如日本廠商、中國廠商、美國廠商或台灣廠商都可能提供 OA 系統。

<a id="qa7"></a>

### 7. 我想了解 如果要把 OA系統跟SAPB1 串接 是有可能的嗎

來源：[S043-Q007](<../../原始問答/S043_解釋 OA 系統.md#q7>)；2026-08-04

**問題**

我想了解 如果要把 OA系統跟SAPB1 串接 是有可能的嗎

**歷史回答**

可以，OA 系統與 SAP Business One（SAP B1）串接是很常見且可行的。通常讓 OA 負責「申請與簽核」，SAP B1 負責「正式的財務、採購、庫存與會計資料」。

典型流程如下：

```text
OA 提出採購申請
→ 主管／財務簽核
→ 核准後自動建立 SAP B1 採購單
→ SAP B1 回傳單號與處理狀態
→ OA 顯示查詢結果
```

可以串接的資料包括：

- 員工、部門及成本中心
- 客戶與供應商主檔
- 商品、庫存及價格
- 採購申請、採購單
- 銷售訂單
- 報價單
- 費用報銷及付款申請
- 發票、會計傳票
- SAP 單號、核准與付款狀態

常見串接方式有：

1. API 串接  
OA 透過 SAP B1 提供的介面讀寫資料。通常是較標準、可靠的方式，但可用介面會受 SAP B1 版本及資料庫環境影響。

2. 中介平台  
在 OA 與 SAP B1 之間放一個整合服務：

```text
OA → 中介平台 → SAP B1
```

中介平台負責欄位轉換、權限、錯誤重試、紀錄與不同國家資料的路由。跨國企業通常較適合這種方式。

3. 直接連接資料庫  
OA 直接讀取 SAP B1 資料庫可用於查詢或報表，但通常不建議直接新增、修改 SAP B1 資料，容易破壞商業邏輯或造成資料不一致。寫入應優先使用正式介面。

跨國情境也能處理。例如各分公司分別有自己的 SAP B1 公司資料庫：

```text
共用 OA 網址
      ↓
依申請人的公司代碼判斷
      ├─ 台灣 → SAP B1 台灣公司資料庫
      ├─ 日本 → SAP B1 日本公司資料庫
      └─ 美國 → SAP B1 美國公司資料庫
```

導入前需要先確認：

- 每個國家是否使用同一套 SAP B1
- SAP B1 的版本、資料庫類型及部署位置
- 各公司使用的科目、幣別、稅碼及成本中心
- 哪一套系統是每種資料的唯一來源
- OA 核准後建立的是草稿還是正式單據
- 建立 SAP 單據失敗時如何通知與重新處理
- 如何避免重複建立同一張單據
- 帳號權限、操作紀錄及跨境資料要求

我會建議第一階段先做「OA 採購申請核准後，在 SAP B1 建立採購單草稿」，風險相對較低；確認資料對應與流程穩定後，再逐步串接正式單據、報銷、付款及跨國報表。

<a id="qa8"></a>

### 8. 可以幫我多做解釋這個中介平台嗎

來源：[S043-Q008](<../../原始問答/S043_解釋 OA 系統.md#q8>)；2026-08-04

**問題**

# Response annotations:
Each item contains text selected from an earlier Codex response and may include a user comment. Treat items as Annotation 1, Annotation 2, and so on in array order. Use every selection as context and address every comment. When addressing multiple comments, label each answer with its annotation number (for example, `Annotation 1`) so the user can match it to the numbered annotation.
<response-annotations>
[{"text":"OA → 中介平台 → SAP B1","annotation":"這個的意思是?"}]
</response-annotations>

## My request for Codex:
可以幫我多做解釋這個中介平台嗎

**歷史回答**

### Annotation 1：`OA → 中介平台 → SAP B1` 的意思

「中介平台」可以理解成 OA 與 SAP B1 之間的翻譯員、交通警察和紀錄中心。

OA 不直接操作 SAP B1，而是先把核准資料交給中介平台；中介平台檢查、轉換後，再透過 SAP B1 的正式介面建立單據。

```text
OA 系統
  │ 提交已核准的申請
  ▼
中介平台
  │ 檢查、轉換、記錄、重試、選擇公司
  ▼
SAP Business One
```

例如員工在 OA 申請購買電腦：

1. OA 完成主管及財務簽核。
2. OA 將申請內容送給中介平台。
3. 中介平台確認供應商、商品、金額和公司代碼。
4. 中介平台把 OA 欄位轉換成 SAP B1 需要的格式。
5. 中介平台在正確的 SAP 公司中建立採購單草稿。
6. SAP B1 回傳單號，例如 `PO-2026-00125`。
7. 中介平台將 SAP 單號和狀態回寫至 OA。

### 為什麼需要中介平台？

OA 與 SAP B1 對相同資料可能使用不同格式。例如：

| OA 資料 | SAP B1 資料 |
|---|---|
| 台灣分公司 | 公司資料庫 `TW_PROD` |
| 資訊設備 | 成本中心 `IT001` |
| 新台幣 | 幣別代碼 `TWD` |
| 含稅 5% | SAP 稅碼 `V5` |
| 王小明 | SAP 使用者或員工代碼 `E1025` |

中介平台會保存這些對照規則，讓兩套系統能理解彼此。

它還會負責：

- 驗證資料：確認供應商、稅碼、成本中心是否存在。
- 轉換格式：將 OA 表單欄位轉成 SAP B1 單據格式。
- 選擇系統：依分公司把資料送到台灣、日本或美國的 SAP B1。
- 避免重複：同一張 OA 申請只能建立一次 SAP 單據。
- 錯誤重試：SAP 暫時無法連線時，保留資料並稍後重送。
- 狀態同步：把 SAP 單號、處理狀態或付款結果回傳 OA。
- 保存紀錄：記錄誰送出、何時傳送、結果及錯誤原因。
- 保護權限：OA 不需要直接取得 SAP 資料庫的完整權限。

### 發生錯誤時的差異

若沒有中介平台：

```text
OA → SAP B1 失敗
```

OA 可能只顯示「建立失敗」，難以判斷原因，也可能因使用者再次點擊而重複建立單據。

若有中介平台：

```text
OA → 中介平台已收件
          ↓
     SAP B1 暫時無法連線
          ↓
     記錄錯誤並自動重試
          ↓
     成功後回寫 SAP 單號
```

### 中介平台是不是一套大型軟體？

不一定。它可以是：

- 一個自行開發的小型整合服務
- 企業既有的 API 管理或系統整合平台
- 訊息佇列加上資料轉換服務
- 專門整合 SAP B1 的工具

如果只有一個公司、單一流程、每天資料量不多，中介程式可以很精簡；如果有多國分公司、多套 SAP、不同幣別和大量交易，就需要較完整的監控、權限、排程及錯誤處理功能。

簡單來說：**OA 管申請與簽核，SAP B1 管正式營運資料，中介平台負責安全、準確地把兩邊接起來。**

<a id="qa9"></a>

### 9. 可以給我舉例幾個中介平台的程式或是

來源：[S043-Q009](<../../原始問答/S043_解釋 OA 系統.md#q9>)；2026-08-04

**問題**

可以給我舉例幾個中介平台的程式或是

**歷史回答**

### Annotation 1：中介平台有哪些實際選擇？

可以，下面是幾個常見例子。但要先區分：「中介平台」是完整的整合工具；SAP B1 的 Service Layer／DI API 則是讓中介平台進入 SAP B1 的接口。

```text
OA
 ↓
中介平台：B1if／自建程式／雲端整合工具
 ↓
SAP B1 接口：Service Layer／DI API
 ↓
SAP B1
```

#### 1. SAP Business One Integration Framework（B1if）

這是 SAP Business One 原廠的整合框架，也是最直接的候選方案。

它可以：

- 接收 OA 傳來的資料
- 轉換欄位格式
- 呼叫 SAP B1 建立單據
- 依公司代碼連到不同分公司
- 定時同步資料
- 記錄執行結果和錯誤

SAP 官方說明顯示，B1if 2.0 是 SAP Business One 的組成部分，並支援 HTTP、Web Service、檔案及 Service Layer 等整合方式。[SAP B1 Integration Framework 官方文件](https://help.sap.com/docs/r/p/INTEGRATION_FRAMEWORK_FOR_SAP_BUSINESS_ONE)

適合：希望以 SAP 原廠架構為主，且有熟悉 SAP B1 的顧問或開發商。

#### 2. 自行開發中介程式

可使用：

- C#／ASP.NET Core
- Java
- Node.js
- Python

例如用 C# 建立一個服務：

```text
OA 核准採購申請
→ 呼叫中介程式的 API
→ 中介程式驗證資料
→ 呼叫 SAP B1 Service Layer
→ 建立採購單草稿
→ 將 SAP 單號回寫 OA
```

SAP B1 Service Layer 使用 HTTP、OData 和 JSON，因此 Java、JavaScript、.NET 等程式都能呼叫。[SAP B1 Service Layer 官方說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/60c7a0b745bd486589f05a1da77041f3.html?locale=en-US)

適合：流程明確、希望高度客製、公司本身有資訊人員。通常也是「一套 OA＋一套 SAP B1」最實際的做法。

#### 3. SAP Integration Suite

這是 SAP 的企業級雲端整合平台，可以串接多個 SAP 與非 SAP 系統，集中管理接口、權限、訊息轉換及監控。

適合：

- 多國分公司
- 多套 ERP 或 SAP 系統
- OA 以外還要串 CRM、電商、銀行等
- 需要企業級管理與監控

但對只有單一 OA 與 SAP B1 的公司而言，成本和導入複雜度可能偏高，而且仍須確認實際 SAP B1 連接方式。

#### 4. Microsoft Azure Logic Apps／Power Automate

使用圖形化流程設計，例如：

```text
收到 OA 核准通知
→ 轉換資料
→ 呼叫 SAP B1 Service Layer
→ 寄送結果通知
→ 更新 OA 狀態
```

適合：公司主要使用 Microsoft Azure、Microsoft 365，且整合流程不算太複雜。

要注意，一般「SAP Connector」不應直接假設完整支援 SAP Business One；實務上很可能需要利用 HTTP 功能呼叫 SAP B1 Service Layer，導入前要先驗證。

#### 5. MuleSoft、Boomi、Make、n8n

這些也是整合平台：

- MuleSoft、Boomi：偏大型企業，治理及監控功能完整。
- Make、n8n：較輕量，適合概念驗證或簡單流程。

它們不一定內建完整的 SAP B1 模組，但只要能呼叫 HTTPS API，通常便能透過 Service Layer 整合。正式上線前仍要補上安全性、錯誤重試、防重複和交易紀錄。

### SAP B1 Service Layer 是什麼？

它不是完整的中介平台，而是 SAP B1 開放給其他程式使用的門口。例如中介程式可以送出：

```text
建立供應商
查詢商品
取得庫存
建立採購單
更新訂單
```

Service Layer 會按照 SAP B1 的商業邏輯處理資料，比直接修改資料庫安全。SAP 官方也說明，它以 REST／OData 形式提供 SAP B1 商業物件。[Service Layer 使用說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/44fda5d585e94b0bbd9d711584db0b96.html?locale=en-US)

### 怎麼選？

| 公司情境 | 建議方案 |
|---|---|
| 單一 OA、單一 SAP B1 | 自建小型中介程式 |
| 已有熟悉 B1if 的 SAP 顧問 | B1if |
| 多國、多系統、大量整合 | SAP Integration Suite、MuleSoft 或 Boomi |
| Microsoft／Azure 環境為主 | Azure Logic Apps |
| 先做低成本概念驗證 | n8n 或簡單自建服務 |

如果只是把「OA 核准的採購申請」送進 SAP B1，我通常會先比較 **B1if** 和 **自建 C# 中介程式＋Service Layer**；這兩種通常最貼近 SAP B1 的實際應用。

<a id="qa10"></a>

### 10. B1IF是甚麼

來源：[S043-Q010](<../../原始問答/S043_解釋 OA 系統.md#q10>)；2026-08-04

**問題**

B1IF是甚麼

**歷史回答**

**B1if** 是 **Integration Framework for SAP Business One** 的簡稱，中文可以理解為：

> SAP Business One 的「系統整合框架」。

它安裝在 SAP B1 環境中，負責讓 SAP B1 與 OA、電商、CRM、銀行或其他系統交換資料。

```text
OA 系統
   ↓
B1if：接收、檢查、轉換、傳送
   ↓
SAP Business One
```

### 實際例子

員工在 OA 完成採購申請：

1. OA 將核准資料傳給 B1if。
2. B1if 判斷是哪一家分公司。
3. 將 OA 欄位轉換成 SAP B1 格式。
4. 連接相應的 SAP B1 公司資料庫。
5. 建立採購單或採購單草稿。
6. 將 SAP 單號回傳給 OA。
7. 如果失敗，留下錯誤紀錄供管理人員處理。

例如欄位轉換：

| OA 欄位 | B1if 轉換後的 SAP B1 欄位 |
|---|---|
| 供應商編號 | `CardCode` |
| 申請日期 | `DocDate` |
| 商品編號 | `ItemCode` |
| 數量 | `Quantity` |
| 成本中心 | `CostingCode` |

### B1if 能做什麼？

- 接收 HTTP、Web Service 或檔案資料
- 呼叫 SAP B1 Service Layer 或其他接口
- 轉換 XML、JSON及欄位格式
- 設定定時同步
- 根據公司代碼路由到不同 SAP B1 公司
- 記錄訊息、執行結果和錯誤
- 將 SAP B1 的資料傳給外部系統

SAP 官方文件將 B1if 2.0 描述為 SAP Business One 的整合組件，並提供 HTTP、Web Service、檔案和 Service Layer 等整合情境。[SAP B1if 官方文件](https://help.sap.com/docs/r/p/INTEGRATION_FRAMEWORK_FOR_SAP_BUSINESS_ONE)

### B1if 和 Service Layer 的差別

| 項目 | B1if | Service Layer |
|---|---|---|
| 定位 | 中介整合平台 | SAP B1 API 接口 |
| 欄位轉換 | 可以 | 通常由呼叫程式處理 |
| 流程及路由 | 可以 | 不負責完整整合流程 |
| 排程同步 | 可以 | 需由其他程式安排 |
| 錯誤流程 | 可集中管理 | 主要回傳成功或失敗 |
| 建立 SAP 單據 | 透過 SAP 接口執行 | 直接提供建立接口 |

簡單比喻：

- **SAP B1**：倉庫
- **Service Layer**：倉庫的大門
- **B1if**：負責收件、檢查地址、轉換格式和安排送貨的物流中心

### 是否一定要使用 B1if？

不一定。OA 也可以透過自建程式直接呼叫 Service Layer。

- 流程少、需求單純：自建小型中介程式可能更直觀。
- 系統多、分公司多、需要資料轉換及集中監控：B1if 通常更有價值。
- 公司已有熟悉 B1if 的 SAP 顧問：採用 B1if會比較容易維護。

因此，B1if 不是 SAP B1 本身，也不是 OA；它是放在兩套系統之間，負責可靠交換資料的整合工具。

<a id="qa11"></a>

### 11. b1if很難嗎

來源：[S043-Q011](<../../原始問答/S043_解釋 OA 系統.md#q11>)；2026-08-04

**問題**

## My request for Codex:
b1if很難嗎

**歷史回答**

B1if **不算容易入門**，但也不是完全學不會。難度主要取決於串接流程的複雜程度，以及你是否熟悉 SAP B1。

大約可以這樣評估：

| 情境 | 難度 |
|---|---|
| 定時匯入簡單檔案 | 中低 |
| OA 核准後建立一種 SAP 單據 | 中等 |
| 雙向同步、錯誤重試 | 中高 |
| 多國、多公司、多幣別及多種單據 | 高 |
| 即時大量交易、複雜流程控制 | 很高 |

比較困難的部分通常是：

- B1if 的介面和概念需要時間熟悉。
- 資料轉換經常使用 XML、XPath、XSLT。
- 必須了解 SAP B1 的商業物件和欄位。
- 需要設定 SAP 公司、登入及接口連線。
- 發生錯誤時，訊息可能不夠直觀。
- 跨國環境還要處理公司、稅碼、幣別和成本中心對照。

真正的難點通常不是「如何把資料送出去」，而是如何確保：

```text
資料正確
＋ 不會重複建立
＋ 失敗能重新處理
＋ 可以查到錯誤原因
＋ 不會傳到錯誤的分公司
```

如果你的第一個需求只是：

> OA 採購申請核准後，在 SAP B1 建立採購單草稿，並回傳 SAP 單號。

這是合理的入門專案，熟悉 SAP B1 且有整合經驗的工程師應可處理。建議先做單一分公司、單一單據的測試，再擴展到跨國。

如果你的團隊以現代程式開發為主，熟悉 C#、JavaScript 和 REST API，但不熟 XML／XSLT，使用「自建中介程式＋SAP B1 Service Layer」有時會比 B1if 更容易維護；如果已有 SAP 顧問或既有 B1if 環境，繼續使用 B1if會比較合適。

<a id="qa12"></a>

### 12. B1直接用service layer

來源：[S043-Q012](<../../原始問答/S043_解釋 OA 系統.md#q12>)；2026-08-04

**問題**

## My request for Codex:
B1直接用service layer

**歷史回答**

可以，**OA 可以直接使用 SAP B1 Service Layer，不一定需要 B1if**。

架構會變成：

```text
OA 後端程式
   ↓ HTTPS／JSON
SAP B1 Service Layer
   ↓
SAP Business One
```

例如 OA 採購申請核准後，OA 後端直接呼叫 Service Layer：

1. 登入 SAP B1 公司資料庫。
2. 查詢供應商、商品與成本中心。
3. 建立採購單草稿。
4. SAP B1 回傳文件編號。
5. OA 保存 SAP 單號與執行狀態。

SAP 官方說明，Service Layer 是以 HTTP、OData及 JSON 操作 SAP B1 商業物件的接口。[SAP Service Layer 官方文件](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/60c7a0b745bd486589f05a1da77041f3.html?locale=en-US)

### 直接使用的優點

- 架構比較簡單。
- 不需要學習 B1if 的操作方式。
- C#、Java、Node.js 等都能呼叫。
- 使用 JSON，通常比 B1if 的 XML／XSLT容易開發。
- 適合少量、明確的串接流程。

### 需要自己完成的部分

不用 B1if後，以下責任會落到 OA 後端或自建中介程式：

- SAP 登入與連線管理
- OA 與 SAP 欄位轉換
- 公司資料庫選擇
- 資料驗證
- 防止重複建立單據
- 失敗重試
- 錯誤紀錄與通知
- SAP 單號回寫
- 帳密與憑證保護

也就是說，B1if不是消失，而是它原本負責的功能要由你的程式承擔。

### 建議的安全架構

不要讓使用者瀏覽器直接連 SAP B1：

```text
使用者瀏覽器
     ↓
OA 後端
     ↓
Service Layer
     ↓
SAP B1
```

Service Layer 應放在內部網路，不宜直接暴露於公網；OA 後端保存 SAP 憑證並控制存取權限。

### 什麼情況適合直接使用？

適合：

- 只有 OA 與 SAP B1 需要串接
- 單據種類不多
- 公司有 C#、Java或 Node.js 開發能力
- 希望程式邏輯容易閱讀及版本管理
- 例如只需要建立採購單草稿、同步供應商和查詢庫存

B1if較適合：

- 多個外部系統同時整合
- 需要大量排程及資料轉換
- 多國、多公司路由複雜
- 公司已有 B1if顧問和既有流程

對「OA 核准後建立 SAP B1 採購單」這類需求，我會優先考慮：

```text
OA 後端＋小型整合服務＋Service Layer
```

它比完整導入 B1if更直觀。不過正式開發前，要先確認目前的 SAP B1 版本、資料庫環境，以及 Service Layer 是否已安裝並可用。

<a id="qa13"></a>

### 13. 如果我想練習這部分 可以幫我排課程 我是完全零基礎

來源：[S043-Q013](<../../原始問答/S043_解釋 OA 系統.md#q13>)；2026-08-04

**問題**

## My request for Codex:
如果我想練習這部分 可以幫我排課程 我是完全零基礎

**歷史回答**

可以。我建議你先走「**OA 後端直接串 SAP B1 Service Layer**」這條路，不先學 B1if。對零基礎來說，學習路線會比較直觀。

主要技術選擇：

- 程式語言：C#
- 開發平台：.NET
- API 測試：Postman
- 資料格式：JSON
- SAP 接口：SAP B1 Service Layer
- 練習環境：SAP B1 測試公司，絕對不要使用正式公司資料庫

## 12 週零基礎課程

每週建議投入 5～7 小時，包括講解、練習和小作業。

### 第 1 週：程式設計入門

學習：

- 程式與後端服務是什麼
- 安裝 Visual Studio或 VS Code
- 變數、文字、數字
- `if` 條件判斷
- `for`、`foreach` 迴圈
- 方法（Method）

練習成果：

```text
輸入採購金額
→ 低於 10,000：主管核准
→ 高於 10,000：主管＋財務核准
```

### 第 2 週：C# 資料結構

學習：

- Class與 Object
- List
- Dictionary
- `null`與錯誤處理
- 建立採購申請資料模型

練習成果：

```csharp
PurchaseRequest
- RequestNumber
- VendorCode
- ItemCode
- Quantity
- Price
- CompanyCode
```

### 第 3 週：JSON 與資料轉換

學習：

- JSON 是什麼
- C# 物件轉 JSON
- JSON 轉回 C# 物件
- OA 與 SAP 欄位如何對應
- 必填欄位驗證

練習成果：

```json
{
  "requestNumber": "OA-2026-001",
  "vendorCode": "V1001",
  "itemCode": "ITM001",
  "quantity": 2
}
```

### 第 4 週：HTTP 與 REST API

學習：

- API 是什麼
- URL、Endpoint
- GET、POST、PATCH、DELETE
- Request與 Response
- HTTP 狀態碼
- Header與 Cookie
- 使用 Postman 測試公開練習 API

目標是看懂：

```text
POST /api/purchase-requests
Content-Type: application/json
```

### 第 5 週：建立自己的 OA API

使用 ASP.NET Core Web API，建立：

- 新增採購申請
- 查詢採購申請
- 核准採購申請
- 基本資料驗證
- Swagger 測試頁面

此時先不連 SAP，使用假資料練習。

### 第 6 週：SAP B1 基礎概念

學習：

- Company Database
- Business Partner
- Item Master Data
- Warehouse
- Cost Center
- Tax Code
- Draft與正式單據
- 採購流程及單據關係
- `CardCode`、`ItemCode`、`DocEntry`、`DocNum` 的差別

這一週很重要：串接失敗往往不是程式問題，而是不了解 SAP B1 的單據規則。

### 第 7 週：第一次連接 Service Layer

在 SAP B1 測試環境中：

- 確認 Service Layer 已安裝
- 用 Postman登入測試公司
- 保存登入 Session
- 查詢供應商
- 查詢商品
- 查詢採購單
- 登出

練習流程：

```text
登入
→ 查詢 Items
→ 查看 JSON 回應
→ 登出
```

### 第 8 週：用 C# 連接 Service Layer

學習：

- `HttpClient`
- 非同步操作 `async`／`await`
- HTTPS 請求
- SAP 登入與 Session
- 解析 SAP 回應
- 封裝 `SapService` 類別

預期程式結構：

```text
OA Controller
    ↓
PurchaseService
    ↓
SapService
    ↓
SAP B1 Service Layer
```

### 第 9 週：建立 SAP 採購單草稿

完成第一個真正的整合：

```text
OA 採購申請核准
→ 檢查供應商
→ 檢查商品
→ 建立 SAP 採購單草稿
→ 取得 DocEntry
→ 回寫 OA
```

先建立「草稿」，不要直接建立正式採購單，這樣比較安全。

### 第 10 週：錯誤處理與防止重複

學習：

- SAP 連線失敗如何處理
- 供應商或商品不存在
- 稅碼、倉庫或成本中心錯誤
- Timeout
- Retry重試
- 防止重複送單
- 保存請求與回應紀錄

建議每張 OA 申請保存：

```text
OA 申請編號
SAP 公司代碼
SAP DocEntry
SAP DocNum
同步狀態
嘗試次數
最後錯誤
同步時間
```

### 第 11 週：資料庫與背景工作

學習：

- SQL 基礎
- 建立 OA 練習資料庫
- Entity Framework Core
- 保存申請與同步紀錄
- 背景佇列
- 失敗後自動重試

狀態可以設計成：

```text
Pending → Processing → Success
                    ↘ Failed → Retry
```

### 第 12 週：安全、測試與結業專案

學習：

- SAP 帳密不能寫死在程式碼
- HTTPS與憑證
- 最小權限
- 操作紀錄
- 測試環境與正式環境分離
- 不要直接修改 SAP B1 資料庫
- 如何部署 OA 整合服務

結業專案：

> 建立一套簡易採購申請 API。申請核准後，自動在 SAP B1 測試公司建立採購單草稿，回寫 SAP 文件編號；失敗時保存原因並允許重新傳送。

## 需要準備的環境

前五週不需要 SAP B1，只需要：

- Windows 電腦
- Visual Studio Community 或 VS Code
- .NET SDK
- Postman
- SQL Server Express 或 SQLite

第六週以後需要：

- SAP Business One 測試環境
- 測試公司資料庫
- Service Layer 已安裝並啟用
- 可供練習的 SAP 帳號
- 測試供應商、商品、倉庫、稅碼與成本中心
- 能協助確認環境的 SAP B1 顧問或管理員

如果暫時沒有 SAP B1 測試環境，也可以先做一個「假的 Service Layer」，模擬 SAP 的登入、查詢及建立單據；等取得環境後再切換到真正的 SAP B1。

## 建議學習順序

不要一開始同時學 OA、B1if、Service Layer、資料庫與部署。依序完成：

```text
C# 基礎
→ JSON
→ HTTP／API
→ 建立自己的 API
→ SAP B1 業務概念
→ Postman 操作 Service Layer
→ C# 串接
→ 錯誤處理與正式化
```

完成這套課程後，你不會立刻成為 SAP 顧問，但應該能獨立做出一個安全的入門串接原型，並且看懂開發商提出的整合架構與報價。

<a id="qa14"></a>

### 14. 建立一套簡易採購申請 API。申請核准後，自動在 SAP B1 測試公司建立採購單草稿，回寫 SAP 文件編號；失敗時保存原因並允許重新傳送。 可以寫一次給我看嗎

來源：[S043-Q014](<../../原始問答/S043_解釋 OA 系統.md#q14>)；2026-08-04

**問題**

## My request for Codex:
建立一套簡易採購申請 API。申請核准後，自動在 SAP B1 測試公司建立採購單草稿，回寫 SAP 文件編號；失敗時保存原因並允許重新傳送。

可以寫一次給我看嗎

**歷史回答**

已經建立一套完整的教學版 API，包含：

- 建立採購申請
- 核准後傳送 SAP B1
- 建立採購單草稿
- 回寫 `DocEntry`／`DocNum`
- 保存錯誤原因及傳送次數
- 失敗後手動重新傳送
- Mock 模式：沒有 SAP 環境也能先練習
- 真實 Service Layer 模式

主要檔案：

- [操作教學](C:/Users/nick_yeh/Documents/Codex/2026-08-04/new-chat/outputs/SapB1PurchaseApi/README.md)
- [完整程式碼](C:/Users/nick_yeh/Documents/Codex/2026-08-04/new-chat/outputs/SapB1PurchaseApi/Program.cs)
- [API 練習請求](C:/Users/nick_yeh/Documents/Codex/2026-08-04/new-chat/outputs/SapB1PurchaseApi/SapB1PurchaseApi.http)
- [SAP 連線設定](C:/Users/nick_yeh/Documents/Codex/2026-08-04/new-chat/outputs/SapB1PurchaseApi/appsettings.json)
- [專案設定](C:/Users/nick_yeh/Documents/Codex/2026-08-04/new-chat/outputs/SapB1PurchaseApi/SapB1PurchaseApi.csproj)

執行流程是：

```text
建立申請
→ PendingApproval
→ 核准
→ Sending
→ 呼叫 SAP Service Layer
   ├─ 成功 → SentToSap＋保存 SAP 文件編號
   └─ 失敗 → Failed＋保存錯誤原因
                         ↓
                      Retry
```

預設 `UseMock` 是 `true`，所以不會真的連接 SAP。安裝 .NET 8 SDK 後，在專案資料夾執行：

```powershell
dotnet run --urls http://localhost:5000
```

要測試失敗與重新傳送，可以建立一張 `VendorCode` 為 `FAIL` 的申請。

目前這台電腦只有 .NET 8 執行環境，沒有 .NET SDK，因此檔案已完成靜態檢查，但尚未實際編譯。接上真實 SAP 前，也需要讓 SAP 顧問確認測試公司的 Service Layer、供應商、商品、倉庫與成本中心代碼。

<a id="qa15"></a>

### 15. 先確認目前工作區與可用的 .NET 環境。 這個是甚麼

來源：[S043-Q015](<../../原始問答/S043_解釋 OA 系統.md#q15>)；2026-08-04

**問題**

## My request for Codex:
先確認目前工作區與可用的 .NET 環境。 這個是甚麼

**歷史回答**

這句話是在說：**開始寫程式前，先檢查你的電腦目前有哪些工具可以使用。**

### 「工作區」是什麼？

工作區就是這次專案檔案存放的位置。這次使用的是：

```text
C:\Users\nick_yeh\Documents\Codex\2026-08-04\new-chat
```

完成的 API 專案放在：

[開啟 SAP B1 API 專案說明](C:/Users/nick_yeh/Documents/Codex/2026-08-04/new-chat/outputs/SapB1PurchaseApi/README.md)

檢查工作區是為了確認：

- 是否已有其他程式碼
- 檔案應該放在哪裡
- 避免覆蓋原有檔案
- 是否有現成的專案設定可以沿用

### 「.NET 環境」是什麼？

.NET 是執行及開發 C# 程式的平台。我寫給你的 API 使用：

```text
C#＋ASP.NET Core＋.NET 8
```

.NET 主要分成兩部分：

| 工具 | 用途 |
|---|---|
| .NET Runtime | 只能執行已經編譯好的程式 |
| .NET SDK | 可以編寫、編譯及執行開發中的程式 |

檢查結果顯示你的電腦目前有：

```text
.NET 8 Runtime：有
.NET 8 SDK：沒有
```

這就像：

- Runtime：可以觀看已經製作完成的影片。
- SDK：可以拍攝、剪輯並輸出影片。

因為目前缺少 SDK，所以我能建立程式碼檔案，但暫時不能在這台電腦上編譯並測試它。

### 接下來需要什麼？

若要實際執行這個教學專案，需要安裝 **.NET 8 SDK**。安裝後便可以在專案資料夾執行：

```powershell
dotnet run --urls http://localhost:5000
```

然後 API 才會真正啟動，讓 Postman或瀏覽器向它送出採購申請。這一步目前只是在準備開發工具，還不會連接或修改 SAP B1。

<a id="qa16"></a>

### 16. 泛微 oa EP

來源：[S046-Q001](<../../原始問答/S046_查找泛微 OA EP.md#q1>)；2026-08-04

**問題**

泛微 oa  EP

**歷史回答**

你想了解泛微 OA 的哪一项？这里的「EP」是指：

- **E-Portal 门户**
- **Ecology 补丁（EP）**
- **二次开发接口**
- **部署、升级或故障排查**

请补充具体问题或贴出报错信息。

<a id="qa17"></a>

### 17. 泛微 OA 跟 SAPB1 串接

來源：[S046-Q002](<../../原始問答/S046_查找泛微 OA EP.md#q2>)；2026-08-04

**問題**

泛微 OA 跟 SAPB1 串接

**歷史回答**

可以串接。建议采用「泛微 OA → 中间接口服务 → SAP Business One」架构，不要让 OA 直接写 SAP B1 数据库。

```text
泛微 OA 表单/审批
        ↓ 审批完成事件
    中间接口服务
（转换、校验、重试、日志）
        ↓
SAP B1 Service Layer / DI API
        ↓
回写 DocEntry、DocNum、状态或错误信息
```

推荐方案：

- **SAP B1 10.0：优先使用 Service Layer OData V4**
  - HTTPS + JSON，适合泛微 Java 环境调用
  - 可处理业务伙伴、采购订单、销售订单、付款等业务对象
  - SAP 自 FP 2405 起已将 OData V4 作为主要协议。[SAP Service Layer 官方说明](https://help.sap.com/doc/056f69366b5345a386bb8149f1700c19/10.0/en-US/Service%20Layer%20API%20Reference.html)
- 若现有 SAP B1 环境无法使用 Service Layer，可采用 **DI API**
  - 需要 Windows/.NET 与 SAP B1 DI API 组件
  - DI API 不支持多线程，通常应由独立接口服务封装。[SAP DI API 官方说明](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/1c66e99da4864964ac11bb74e49786b8.html?locale=en-US)

常见串接流程：

1. OA 同步 SAP 的供应商、客户、物料、部门、项目及会计科目等基础资料。
2. 使用者在 OA 填写采购、费用或付款申请。
3. OA 审批完成后调用中间服务。
4. 中间服务把字段转换成 SAP B1 格式并建立单据。
5. SAP 返回 `DocEntry`、`DocNum`。
6. 回写 OA；失败则保存错误、允许重送。

实施时必须处理：

- OA 流程编号作为唯一键，防止重复建立 SAP 单据。
- 建立完整字段对照表，包括表头、明细、税码、币别、成本中心及项目代码。
- SAP 过账失败时保留原始请求与响应。
- 采用队列、重试与人工补送机制。
- SAP 账号仅授予必要权限，密码不要存放在 OA 表单。
- 不直接 `INSERT/UPDATE` SAP B1 数据表，应通过 Service Layer 或 DI API 执行业务规则。

例如建立采购订单时，中间服务通常调用：

```http
POST https://sap-server:50000/b1s/v2/PurchaseOrders
Content-Type: application/json
```

```json
{
  "CardCode": "V00001",
  "DocDate": "2026-08-04",
  "NumAtCard": "OA-20260804-001",
  "DocumentLines": [
    {
      "ItemCode": "ITEM001",
      "Quantity": 10,
      "UnitPrice": 100,
      "WarehouseCode": "01"
    }
  ]
}
```

SAP B1 的 Service Layer 使用 HTTP/OData，并以 JSON 收发数据，官方也提供实体和动作的完整定义。[Service Layer 使用说明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/44fda5d585e94b0bbd9d711584db0b96.html?locale=en-US)

若要进一步产出接口规格，需要确认：

- 泛微版本：E-cology 8、9、10，还是 E9？
- SAP B1 版本及数据库：HANA 或 SQL Server？
- 第一阶段要串接哪种流程：采购订单、费用报销、付款申请还是主数据？

<a id="qa18"></a>

### 18. 我想了解，，關於泛微OA如果要跟SAPB1串接，身為SAPB1工程師的我，應該怎麼跟對方談

來源：[S080-Q001](<../../原始問答/S080_規劃泛微OA與SAPB1串接溝通.md#q1>)；2026-08-14

**問題**

我想了解，，關於泛微OA如果要跟SAPB1串接，身為SAPB1工程師的我，應該怎麼跟對方談

**歷史回答**

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

<a id="qa19"></a>

### 19. About SAP Business One Service Layer SAP Business One Service Layer is a new generation of…

來源：[S080-Q002](<../../原始問答/S080_規劃泛微OA與SAPB1串接溝通.md#q2>)；2026-08-14

**問題**

## My request:
About SAP Business One Service Layer
SAP Business One Service Layer is a new generation of extension API for consuming SAP Business One data and services. It builds on core protocols such as HTTP and OData, and provides a uniform way to expose full-featured business objects on top of a highly scalable and high-availability Web server. Currently, Service Layer supports OData version 3, version 4, and a few selected OData client libraries, for example, WCF for .Net developers; data.js for JavaScript developers.


甚麼意思

**歷史回答**

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

<a id="qa20"></a>

### 20. SAPB1 串接方法

來源：[S099-Q001](<../../原始問答/S099_查找 SAPB1 串接方法.md#q1>)；2026-08-21

**問題**

SAPB1 串接方法

**歷史回答**

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

<a id="qa21"></a>

### 21. API

來源：[S099-Q002](<../../原始問答/S099_查找 SAPB1 串接方法.md#q2>)；2026-08-21

**問題**

API

**歷史回答**

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

<a id="qa22"></a>

### 22. 可以產生一個 API的教學嗎

來源：[S099-Q003](<../../原始問答/S099_查找 SAPB1 串接方法.md#q3>)；2026-08-21

**問題**

## My request:
可以產生一個 API的教學嗎

**歷史回答**

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

<a id="qa23"></a>

### 23. 可以寫給我 我從 API 寫 業務夥伴主檔嗎 可以寫一個示範給我嗎

來源：[S099-Q004](<../../原始問答/S099_查找 SAPB1 串接方法.md#q4>)；2026-08-21

**問題**

## My request:
可以寫給我 我從 API 寫 業務夥伴主檔嗎 可以寫一個示範給我嗎

**歷史回答**

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

<a id="qa24"></a>

### 24. Program.cs 這是甚麼意思

來源：[S099-Q005](<../../原始問答/S099_查找 SAPB1 串接方法.md#q5>)；2026-08-21

**問題**

## My request:
Program.cs 這是甚麼意思

**歷史回答**

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

<a id="qa25"></a>

### 25. 圖片／附件提問

來源：[S099-Q006](<../../原始問答/S099_查找 SAPB1 串接方法.md#q6>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-85195f5a-d0bb-43c9-8fb0-afe89cd6e7f8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-85195f5a-d0bb-43c9-8fb0-afe89cd6e7f8.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa26"></a>

### 26. 圖片／附件提問

來源：[S099-Q007](<../../原始問答/S099_查找 SAPB1 串接方法.md#q7>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-e0fa4d6f-c2b7-4fe7-8e79-300b42d3a16b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e0fa4d6f-c2b7-4fe7-8e79-300b42d3a16b.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa27"></a>

### 27. 圖片／附件提問

來源：[S099-Q008](<../../原始問答/S099_查找 SAPB1 串接方法.md#q8>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-bdcc73ec-9226-4b09-9be9-f12f49e3d81d.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-bdcc73ec-9226-4b09-9be9-f12f49e3d81d.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa28"></a>

### 28. 圖片／附件提問

來源：[S099-Q009](<../../原始問答/S099_查找 SAPB1 串接方法.md#q9>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-ca34adbb-33da-478c-84c4-5f87ed4030ad.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-ca34adbb-33da-478c-84c4-5f87ed4030ad.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa29"></a>

### 29. 通常 這個會是甚麼密碼

來源：[S099-Q010](<../../原始問答/S099_查找 SAPB1 串接方法.md#q10>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-dada00e4-361a-4633-ba2b-6657bfff23e4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-dada00e4-361a-4633-ba2b-6657bfff23e4.png





## My request:
 通常 這個會是甚麼密碼

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa30"></a>

### 30. 所以 .NET是在幹嘛的

來源：[S099-Q011](<../../原始問答/S099_查找 SAPB1 串接方法.md#q11>)；2026-08-21

**問題**

## My request:
所以 .NET是在幹嘛的

**歷史回答**

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

<a id="qa31"></a>

### 31. 圖片／附件提問

來源：[S099-Q012](<../../原始問答/S099_查找 SAPB1 串接方法.md#q12>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-60c2b62a-9637-4880-8373-b23c4849f512.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-60c2b62a-9637-4880-8373-b23c4849f512.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa32"></a>

### 32. dotnet new console -n SapB1ApiDemo cd SapB1ApiDemo notepad Program.cs 然後呢

來源：[S099-Q013](<../../原始問答/S099_查找 SAPB1 串接方法.md#q13>)；2026-08-21

**問題**

## My request:
dotnet new console -n SapB1ApiDemo
cd SapB1ApiDemo
notepad Program.cs 然後呢

**歷史回答**

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

<a id="qa33"></a>

### 33. \

來源：[S099-Q014](<../../原始問答/S099_查找 SAPB1 串接方法.md#q14>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-f603f7c2-76dd-4109-9737-4e194a753755.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-f603f7c2-76dd-4109-9737-4e194a753755.png





## My request:
\

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa34"></a>

### 34. 圖片／附件提問

來源：[S099-Q015](<../../原始問答/S099_查找 SAPB1 串接方法.md#q15>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-7e20eed8-c1ec-4019-9861-d785f8530fe4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7e20eed8-c1ec-4019-9861-d785f8530fe4.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa35"></a>

### 35. 圖片／附件提問

來源：[S099-Q016](<../../原始問答/S099_查找 SAPB1 串接方法.md#q16>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-da01c6d3-0495-4be3-96c2-f83d5cc96a9b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-da01c6d3-0495-4be3-96c2-f83d5cc96a9b.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa36"></a>

### 36. 這裡也要修改嗎

來源：[S099-Q017](<../../原始問答/S099_查找 SAPB1 串接方法.md#q17>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-e9b269d6-13b0-4f1f-a459-794f930ebd61.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e9b269d6-13b0-4f1f-a459-794f930ebd61.png





## My request:
這裡也要修改嗎

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa37"></a>

### 37. 那我寫對

來源：[S099-Q018](<../../原始問答/S099_查找 SAPB1 串接方法.md#q18>)；2026-08-21

**問題**

## My request:
那我寫對

**歷史回答**

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

<a id="qa38"></a>

### 38. 但還是不行

來源：[S099-Q019](<../../原始問答/S099_查找 SAPB1 串接方法.md#q19>)；2026-08-21

**問題**

## My request:
但還是不行

**歷史回答**

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

<a id="qa39"></a>

### 39. 圖片／附件提問

來源：[S099-Q020](<../../原始問答/S099_查找 SAPB1 串接方法.md#q20>)；2026-08-21

**問題**

## My request:

**歷史回答**

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

<a id="qa40"></a>

### 40. var companyDb = "WellShin_VN_Test_260428"; var userName = "manager";

來源：[S099-Q021](<../../原始問答/S099_查找 SAPB1 串接方法.md#q21>)；2026-08-21

**問題**

## My request:
var companyDb = "WellShin_VN_Test_260428";
var userName = "manager";

**歷史回答**

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

<a id="qa41"></a>

### 41. 圖片／附件提問

來源：[S099-Q022](<../../原始問答/S099_查找 SAPB1 串接方法.md#q22>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-109deb32-2a1c-40c8-98e9-c1cc67548263.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-109deb32-2a1c-40c8-98e9-c1cc67548263.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa42"></a>

### 42. Console.Write("請輸入 manager 的 SAP B1 密碼："); var password = Console.ReadLine() ?? ""; 改一個完整版…

來源：[S099-Q023](<../../原始問答/S099_查找 SAPB1 串接方法.md#q23>)；2026-08-21

**問題**

## My request:
Console.Write("請輸入 manager 的 SAP B1 密碼：");
var password = Console.ReadLine() ?? "";

改一個完整版的給我

**歷史回答**

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

<a id="qa43"></a>

### 43. 還是失敗

來源：[S099-Q024](<../../原始問答/S099_查找 SAPB1 串接方法.md#q24>)；2026-08-21

**問題**

## My request:
還是失敗

**歷史回答**

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

<a id="qa44"></a>

### 44. 還是失敗

來源：[S099-Q025](<../../原始問答/S099_查找 SAPB1 串接方法.md#q25>)；2026-08-21

**問題**

## My request:
還是失敗

**歷史回答**

目前不用再改程式。`v1`、`v2` 都回傳 `206 Invalid login credential`，代表 Service Layer 明確拒絕這組公司與帳密。

請只確認一件事：

**你能否在 SAP Business One 桌面用戶端，選擇 `WellShin_VN_Test_260428`，並用 `manager`＋同一組密碼手動登入成功？**

- 如果不能：請 SAP 管理員重設 `manager` 密碼或解除帳號鎖定。
- 如果可以：請貼出 SLD → **DB Instances and Companies** 中該公司的畫面，我再幫你確認正確的 `CompanyDB`。請勿提供密碼。

沒有這項結果，再繼續改 C# 程式也不會解決 `206`。

<a id="qa45"></a>

### 45. 圖片／附件提問

來源：[S099-Q026](<../../原始問答/S099_查找 SAPB1 串接方法.md#q26>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-1b259610-a17e-48e5-bb30-4248e45cff74.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-1b259610-a17e-48e5-bb30-4248e45cff74.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa46"></a>

### 46. dk3u3

來源：[S099-Q027](<../../原始問答/S099_查找 SAPB1 串接方法.md#q27>)；2026-08-21

**問題**

## My request:
dk3u3

**歷史回答**

如果這是 SAP B1 密碼，請不要貼在聊天室。由於已經暴露，建議立即請管理員變更密碼。

執行：

```powershell
dotnet run
```

看到密碼提示後，直接在 PowerShell 輸入新密碼並按 Enter，不要寫入 `Program.cs`，也不要再貼給我。

<a id="qa47"></a>

### 47. 可以登入

來源：[S099-Q028](<../../原始問答/S099_查找 SAPB1 串接方法.md#q28>)；2026-08-21

**問題**

## My request:
可以登入

**歷史回答**

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

<a id="qa48"></a>

### 48. 可以登入 我是說桌面板

來源：[S099-Q029](<../../原始問答/S099_查找 SAPB1 串接方法.md#q29>)；2026-08-21

**問題**

## My request:
可以登入 我是說桌面板

**歷史回答**

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

<a id="qa49"></a>

### 49. 一樣失敗

來源：[S099-Q030](<../../原始問答/S099_查找 SAPB1 串接方法.md#q30>)；2026-08-21

**問題**

## My request:
一樣失敗

**歷史回答**

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

<a id="qa50"></a>

### 50. Monitor。 這是甚麼

來源：[S099-Q031](<../../原始問答/S099_查找 SAPB1 串接方法.md#q31>)；2026-08-21

**問題**

## My request:
Monitor。 這是甚麼

**歷史回答**

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

<a id="qa51"></a>

### 51. 4. 打開該筆錯誤的 Detail，將錯誤畫面貼給我；請遮住密碼、Session及敏感資料。 也可請 SAP 管理員檢查： 空白得

來源：[S099-Q032](<../../原始問答/S099_查找 SAPB1 串接方法.md#q32>)；2026-08-21

**問題**

## My request:
4. 打開該筆錯誤的 Detail，將錯誤畫面貼給我；請遮住密碼、Session及敏感資料。
也可請 SAP 管理員檢查： 空白得

**歷史回答**

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

<a id="qa52"></a>

### 52. 圖片／附件提問

來源：[S099-Q033](<../../原始問答/S099_查找 SAPB1 串接方法.md#q33>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-6908923f-571b-4113-a1d2-a94afc501b64.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6908923f-571b-4113-a1d2-a94afc501b64.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa53"></a>

### 53. 空的

來源：[S099-Q034](<../../原始問答/S099_查找 SAPB1 串接方法.md#q34>)；2026-08-21

**問題**

## My request:
空的

**歷史回答**

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

<a id="qa54"></a>

### 54. 圖片／附件提問

來源：[S099-Q035](<../../原始問答/S099_查找 SAPB1 串接方法.md#q35>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-9f12bc7f-8edc-4d35-9d24-cd59ad2e89b8.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-9f12bc7f-8edc-4d35-9d24-cd59ad2e89b8.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa55"></a>

### 55. 圖片／附件提問

來源：[S099-Q036](<../../原始問答/S099_查找 SAPB1 串接方法.md#q36>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-4d61ce18-f1b8-451c-b465-1b67b991d496.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-4d61ce18-f1b8-451c-b465-1b67b991d496.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa56"></a>

### 56. 圖片／附件提問

來源：[S099-Q037](<../../原始問答/S099_查找 SAPB1 串接方法.md#q37>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-fef992b4-0288-4f16-b279-32172da79457.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-fef992b4-0288-4f16-b279-32172da79457.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa57"></a>

### 57. 圖片／附件提問

來源：[S099-Q038](<../../原始問答/S099_查找 SAPB1 串接方法.md#q38>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-09df1f9c-00da-443e-b842-d865f20b31a9.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-09df1f9c-00da-443e-b842-d865f20b31a9.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**歷史回答**

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

<a id="qa58"></a>

### 58. 我要怎麼檢查 因為我就是管理員

來源：[S099-Q039](<../../原始問答/S099_查找 SAPB1 串接方法.md#q39>)；2026-08-21

**問題**

我要怎麼檢查 因為我就是管理員

**歷史回答**

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

<a id="qa59"></a>

### 59. 這是甚麼意思

來源：[S100-Q001](<../../原始問答/S100_解釋這段內容.md#q1>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-6acf47b9-b950-40be-b534-00a217994dc4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6acf47b9-b950-40be-b534-00a217994dc4.png



## My request:
這是甚麼意思

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

這是「負載平衡／叢集節點管理」畫面。系統目前有兩個後端服務節點：

- `node1`：`http://127.0.0.1:50001/b1s`
- `node2`：`http://127.0.0.1:50002/b1s`
- `Status = Init Ok`：兩個節點都已成功初始化。
- `Factor = 1.00`：兩者權重相同，理論上會平均分配流量。
- `Sticky Session = On`：同一位使用者建立工作階段後，後續請求會盡量導向同一節點。
- `Busy = 0`：目前沒有顯示忙碌中的請求。
- `Elected`：各節點被負載平衡器選中的累計次數。
- `To / From`：送往節點及從節點收到的累計資料量。
- `Max Members = 0`：通常代表未設定上限，實際含義仍取決於這套管理工具。

比較值得注意的是 `Load`：

- node1：`3615900`
- node2：`-3615900`

負載值一正一負且數字很大，通常不像正常的即時負載百分比，比較可能是管理介面的計算、顯示或計數器溢位問題；不一定表示 node2 故障。由於兩個節點都是 `Init Ok`，單看這張圖，服務節點目前應該是可用的。

<a id="qa60"></a>

### 60. 這個好像是示範

來源：[S100-Q002](<../../原始問答/S100_解釋這段內容.md#q2>)；2026-08-21

**問題**

# Files mentioned by the user:

## codex-clipboard-40d8c79a-0822-4681-a60c-3ca7e089344c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-40d8c79a-0822-4681-a60c-3ca7e089344c.png



## My request:


這個好像是示範

[原對話含圖片，本文未嵌入圖片]

**歷史回答**

對，這是 **SAP Business One Service Layer API 的登入範例**，不是要求你照抄的正式登入資料。

範例會送出：

```http
POST https://localhost:50000/b1s/v2/Login
```

並附上 JSON：

```json
{
  "CompanyDB": "SBODEMOUS",
  "UserName": "manager",
  "Password": "1234"
}
```

各欄位含義：

- `SBODEMOUS`：示範公司資料庫名稱
- `manager`：示範帳號
- `1234`：示範密碼
- `localhost:50000`：示範環境的 Service Layer 位址

實際使用時，要替換成你們的公司資料庫、SAP B1 帳密及伺服器位址。登入成功後，伺服器會回傳 `SessionId`，並設定：

- `B1SESSION`：後續 API 呼叫用來證明已登入，必要。
- `ROUTEID`：讓後續請求固定送到同一個節點，與上一張圖的 **Sticky Session** 有關；通常建議保留。

例如你上一張圖有 `50001`、`50002` 兩個工作節點，而 `50000` 很可能是前面的負載平衡入口；通常應呼叫 `50000`，再由系統分配至其中一個節點。瀏覽器會自動帶 Cookie；若是用程式、Postman 或其他 HTTP 客戶端，就要確保後續請求仍帶著登入後取得的 Cookie。

<a id="qa61"></a>

### 61. [〔連結〕 學習這個後 給我一個新的中文說明文件

來源：[S100-Q003](<../../原始問答/S100_解釋這段內容.md#q3>)；2026-08-21

**問題**

[https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/0eb91aedfced483d9ee14a6acb2ffcfb.html](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/0eb91aedfced483d9ee14a6acb2ffcfb.html)

學習這個後 給我一個新的中文說明文件

**歷史回答**

〔原紀錄沒有保存回答，請勿視為已解決。〕

<a id="qa62"></a>

### 62. 給我md檔

來源：[S100-Q004](<../../原始問答/S100_解釋這段內容.md#q4>)；2026-08-21

**問題**

給我md檔

**歷史回答**

已完成繁體中文 Markdown 說明文件：

[下載 SAP Business One Service Layer 系統需求與部署指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08-21/new-chat-2/outputs/SAP_Business_One_Service_Layer_系統需求與部署指南.md)

內容包括：

- Service Layer 基本概念
- 支援的作業系統
- 整合式與分散式部署比較
- 硬體與容量規劃
- 多節點、`B1SESSION`、`ROUTEID` 與負載平衡
- 導入檢查清單
- 常見誤解與驗證流程
- SAP 官方參考來源

<a id="qa63"></a>

### 63. [〔連結〕

來源：[S100-Q005](<../../原始問答/S100_解釋這段內容.md#q5>)；2026-08-21

**問題**

[https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/b0ff6ae522cd4ffb83bc3b7c6199f142.html?locale=en-US](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/b0ff6ae522cd4ffb83bc3b7c6199f142.html?locale=en-US)

**歷史回答**

已根據新連結製作獨立的繁體中文 Markdown 指南：

[下載 SAP Business One Service Layer UDT CRUD 操作指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08-21/new-chat-2/outputs/SAP_Business_One_Service_Layer_UDT_CRUD_操作指南.md)

內容涵蓋 UDT 的 Entity 命名規則、`POST`、`GET`、`$select`、`$filter`、`PATCH`、`DELETE`、完整 URL 與 cURL 範例，以及 Session、權限和常見錯誤排查。

<a id="qa64"></a>

### 64. [下載 SAP Business One Service Layer 系統需求與部署指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08…

來源：[S100-Q006](<../../原始問答/S100_解釋這段內容.md#q6>)；2026-08-21

**問題**

[下載 SAP Business One Service Layer 系統需求與部署指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08-21/new-chat-2/outputs/SAP_Business_One_Service_Layer_系統需求與部署指南.md)這個在一個

**歷史回答**

已合併成同一份文件，現在包含：

- 系統需求與部署模式
- 硬體容量規劃
- 登入、Cookie、Session 與負載平衡
- 多節點管理畫面說明
- UDT 的新增、查詢、修改與刪除
- API 範例、常見問題及正式環境檢查清單

[下載合併後的 SAP Business One Service Layer 中文整合指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08-21/new-chat-2/outputs/SAP_Business_One_Service_Layer_系統需求與部署指南.md)

<a id="qa65"></a>

### 65. [〔連結〕 這個呢

來源：[S100-Q007](<../../原始問答/S100_解釋這段內容.md#q7>)；2026-08-21

**問題**

[https://help.sap.com/doc/0d2533ad95ba4ad7a702e83570a21c32/9.3/en-US/Working_with_SAP_Business_One_Service_Layer.pdf](https://help.sap.com/doc/0d2533ad95ba4ad7a702e83570a21c32/9.3/en-US/Working_with_SAP_Business_One_Service_Layer.pdf)

這個呢

**歷史回答**

這是 2019 年發布、共 172 頁的 SAP Business One 9.1／9.2／9.3 for SAP HANA 完整 Service Layer 手冊，內容很全面，但系統需求和安裝步驟已較舊。[SAP 官方 PDF](https://help.sap.com/doc/0d2533ad95ba4ad7a702e83570a21c32/9.3/en-US/Working_with_SAP_Business_One_Service_Layer.pdf)

我已將適合沿用的內容加入同一份中文整合指南，包括：

- 三層式架構與元件職責
- OData v3／v4
- Session Stickiness 與多節點高可用性
- 完整手冊涵蓋的 API 主題
- 值得後續學習的章節
- 9.3 舊版與 10.0 現行環境的版本警告

[下載更新後的中文整合指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08-21/new-chat-2/outputs/SAP_Business_One_Service_Layer_系統需求與部署指南.md)

<a id="qa66"></a>

### 66. [〔連結〕

來源：[S100-Q008](<../../原始問答/S100_解釋這段內容.md#q8>)；2026-08-21

**問題**

[https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/894666ccf6bc4b8a9fb681b23b5a8da8.html?locale=en-US](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/894666ccf6bc4b8a9fb681b23b5a8da8.html?locale=en-US)

**歷史回答**

這個連結是目前應優先參考的 Service Layer 官方主文件。文件歷程已更新到 1.28（2026-01-07），並新增 Webhook 支援。[SAP 官方主文件](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/894666ccf6bc4b8a9fb681b23b5a8da8.html?locale=en-US)

我已更新同一份中文指南，加入：

- 現行主文件與 9.3 PDF 的定位差異
- 10.0、SQL Server、SQL Query、ETag 等版本更新
- Upload、Log Monitor、Query Allowlist 與 Webhook
- 現行文件的內容架構
- 建議學習順序
- 新舊文件的使用原則

[下載更新後的 SAP Business One Service Layer 中文整合指南.md](C:/Users/nick_yeh/Documents/Codex/2026-08-21/new-chat-2/outputs/SAP_Business_One_Service_Layer_系統需求與部署指南.md)

<a id="qa67"></a>

### 67. 〔此紀錄未包含對應提問〕

來源：[S201-Q001](<../../原始問答/S201_SAP B1 Service Layer｜泛微 OA 資料串接.md#q1>)；2026-09-24

**問題**

〔此紀錄未包含對應提問〕

**歷史回答**

這個任務先以 **「泛微 OA 將資料傳入 SAP Business One」** 為目標，後續用繁體中文、少量且循序漸進地進行。

目前規劃：
- 在你目前的電腦使用 Visual Studio 開發。
- 完成後部署到 SAP B1 的 Windows 主機。
- 暫時不建立專案、不安裝環境，也不連線正式系統。

下次開始時，先準備以下資訊；不知道的項目可以先留白：

| 項目 | 需要確認的內容 |
|---|---|
| SAP B1 環境 | 版本、使用 SQL Server 還是 HANA |
| Service Layer | 是否已安裝並啟用，可請 SAP 維護廠商確認 |
| 泛微 OA 環境 | 產品名稱、版本，以及是否有 API 文件 |
| 第一個串接流程 | 要傳什麼資料，例如採購單；何時傳，例如簽核通過後 |

先選定一個流程，再逐步處理欄位對應、資料驗證、SAP API、回覆 OA 結果，以及重複送件與失敗重試。

**目前不需要提供帳號或密碼。** 等你準備好，我們再從上述資訊開始。

