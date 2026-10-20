# 查找 SAPB1 資源主檔說明

來源編號：S171  
聊天 ID：`01a0a3f2-3506-7400-85c7-f11d7dff8340`  
聊天起始日期（台北）：2026-09-15  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S171-Q001｜SAPB1 資源主檔 原廠說明

日期：2026-09-15

**你的提問**

SAPB1 資源主檔 原廠說明

**當時回答**

以下是 **SAP Business One「資源主檔（Resource Master Data）」原廠說明**，以 **10.0 版**為準，連結為 SAP 官方英文文件。

### 原廠文件

- **[資源主檔建立與欄位說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f19b844f41b74a8c8ca26f4d501e032b/9862f0b3e7f74e9bb04af71685654f6a.html?locale=en-US&version=10.0)**：最直接對應資源主檔畫面的說明，包含各頁籤、欄位與範例。
- **[資源與生產操作手冊 PDF](https://help.sap.com/doc/10311afc90c643b3aaceeaa87a52bf5f/10.0/en-US/How_to_Work_with_Resources_and_Production_in_SAP_Business_One.pdf)**：完整原廠手冊，可搜尋章節 **Working with Resource Master Data**。

### 中文重點整理

進入路徑：**主選單 → 資源 → 資源主檔**。

| 區域 | 原廠說明重點 |
|---|---|
| 資源代碼 | 必須唯一，不能與其他資源或項目代碼重複。 |
| 資源類型 | 分為 **機器（Machine）、人工（Labor）、其他（Other）**。 |
| 資源群組 | 提供預設值，可在個別資源主檔調整。 |
| 計量單位 | 定義產能單位，例如小時、分鐘或機器循環次數。 |
| 時間與資源單位 | 定義產能數量與時間的換算關係，用於報表與生產訂單執行時間計算。 |
| 一般頁籤 | 設定發料方式：倒扣（Backflush）或手動（Manual），以及產能分配方式。 |
| 產能資料頁籤 | 依倉庫及期間查看產能；**可用產能＝內部產能－已承諾產能－已耗用產能**。 |
| 計劃資料頁籤 | 設定每日標準產能，可直接輸入，或由最多四個產能因子相乘計算。 |

以上依據：[SAP 原廠欄位說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f19b844f41b74a8c8ca26f4d501e032b/9862f0b3e7f74e9bb04af71685654f6a.html?locale=en-US&version=10.0)。

**設定時要注意：**「計劃資料」中的每日標準產能，還需要透過 **資源 → 設定每日內部產能（Set Daily Internal Capacities）**，套用到指定日期與倉庫，才能建立該期間的內部產能。[原廠設定步驟](https://help.sap.com/docs/SAP_BUSINESS_ONE/f19b844f41b74a8c8ca26f4d501e032b/12d01a4bcad94b289e801da60f4f80ef.html?locale=en-US)

---

<a id="q2"></a>

## S171-Q002｜我要中文版

日期：2026-09-15

**你的提問**

## My request:
我要中文版

**當時回答**

找到 **SAP 官方繁體中文版**：

### [建立資源－SAP Business One 官方中文說明](https://help.sap.com/docs/SAP_BUSINESS_ONE_WEB_CLIENT/2554bf7e9aa347729b0547a737e123ac/5dee10fb86284dd4b30e6faf2a9461a7.html?locale=zh-TW&version=10.0_SP_2602)

內容包含：

- 資源類型：機器、人力、其他
- 資源群組、發貨方法、資源標準成本
- 產能與規劃資料
- 每日產能係數、單一執行產能
- 固定資產與員工關聯

**這份是 Web Client 版的原廠繁體中文文件。** 前面提供的傳統桌面版「資源主檔」詳細指南，我目前未找到可確認的官方中文版。

---

