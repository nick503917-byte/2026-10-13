# 分辨 DTW 匯入的付款方與收貨方

來源編號：S150  
聊天 ID：`01a0843f-9e48-7882-a70e-d05c443f6a75`  
聊天起始日期（台北）：2026-09-09  
保存位置：一般本機紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S150-Q001｜我用 DTW 匯入的話 我要怎麼分辨是付款方還是收貨方

日期：2026-09-09

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e29eaa9c-d453-4352-b745-646e69acf847.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e29eaa9c-d453-4352-b745-646e69acf847.png



## My request:
我用 DTW 匯入的話 我要怎麼分辨是付款方還是收貨方

[原對話含圖片，本文未嵌入圖片]

**當時回答**

在 DTW 匯入時，是用 **`BPAddresses` 地址範本裡的 `AddressType` 欄位**，區分「付款方」與「收貨方」。

| 畫面上的分類 | `AddressType` 填入值 |
|---|---|
| 付款方（Bill To） | `bo_BillTo` |
| 收貨方（Ship To） | `bo_ShipTo` |

這兩個值對應 SAP 的地址類型定義。[SAP 官方說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~Enumerations~BoAddressType_EN.html)

例如同一個客戶要匯入兩種地址，地址子表就各放一列：

| `AddressName`（地址 ID） | `AddressType` | `Street`（街道） |
|---|---|---|
| 帳單地址 | `bo_BillTo` | 帳單寄送地址 |
| 收貨倉庫 | `bo_ShipTo` | 實際送貨地址 |

**你截圖中的「Bill To」是地址 ID，也就是 `AddressName`，名稱可以自訂；真正決定歸在哪一類的是 `AddressType`。** 即使名稱寫「Bill To」，仍然要把類型填成 `bo_BillTo`。

另外，`AddressType` 不要留空，SAP 的預設地址類型是收貨方（Ship To）。[欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~BPAddresses~AddressType.html)

---

