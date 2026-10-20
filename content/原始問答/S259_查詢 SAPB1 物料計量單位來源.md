# 查詢 SAPB1 物料計量單位來源

來源編號：S259  
聊天 ID：`01a156f2-e54e-7081-b0ab-40238ae5aefc`  
聊天起始日期（台北）：2026-10-20  
保存位置：一般本機紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S259-Q001｜SAPB1 物料表的計量單位是抓甚麼位置的

日期：2026-10-20

**你的提問**

# Files mentioned by the user:

## codex-clipboard-b377af90-67b7-45bc-be4a-bf11d58d8c87.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b377af90-67b7-45bc-be4a-bf11d58d8c87.png
Image attachment: true



## My request:
SAPB1 物料表的計量單位是抓甚麼位置的 

[原對話含圖片，本文未嵌入圖片]

**當時回答**

你截圖中，BOM 明細的「計量單位」（KGS、PCS、卷等），標準是帶入**各列子物料主檔的「庫存計量單位」**。

可以到這裡查看：

**存貨 → 物料主檔資料 → 查詢該子物料 →「存貨資料」頁籤 →「庫存計量單位」**  
也可以直接點 BOM 明細物料代碼旁的黃色箭頭，開啟該物料主檔。

資料庫對應如下：

| 位置 | 欄位 |
|---|---|
| 物料主檔的庫存計量單位 | `OITM.InvntryUom` |
| BOM 明細儲存的計量單位 | `ITT1.Uom` |
| 子物料關聯 | `ITT1.Code = OITM.ItemCode` |

SAP 官方欄位定義可參考：[物料主檔 OITM](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/OITM.html)、[BOM 明細 ITT1](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/ITT1.html)。

你圖中有些列是空白，**可先檢查那些子物料的庫存計量單位是否有填**；若主檔有值、BOM 卻空白，再比對上述兩個欄位。

---

