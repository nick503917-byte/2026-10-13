# 掛接FMS抓取項目號碼

來源編號：S135  
聊天 ID：`01a079a0-7ec8-7703-8f7c-bd448b45f3c3`  
聊天起始日期（台北）：2026-09-07  
保存位置：一般本機紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S135-Q001｜我要在這個欄位掛FMS 我要抓 項目號碼主檔 欄位 OITM.U\_CustItemNo

日期：2026-09-07

**你的提問**

# Files mentioned by the user:

## codex-clipboard-019c6462-a256-4f1c-9a63-ce9f3f25826b.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-019c6462-a256-4f1c-9a63-ce9f3f25826b.png



## My request:


我要在這個欄位掛FMS 我要抓 項目號碼主檔 欄位 OITM.U\_CustItemNo

[原對話含圖片，本文未嵌入圖片]

**當時回答**

可以，將 FMS 掛在圖片中的「客戶料號」欄位（`RDR1.SubCatNum`），依同一列的「項目號碼」抓取 `OITM.U_CustItemNo`。

**SQL Server 版：**

```sql
SELECT T0.[U_CustItemNo]
FROM OITM T0
WHERE T0.[ItemCode] = $[$38.1.0]
```

**SAP HANA 版：**

```sql
SELECT T0."U_CustItemNo"
FROM OITM T0
WHERE T0."ItemCode" = $[$38.1.0]
```

`$[$38.1.0]` 代表目前列的「項目號碼」，例如圖片中的 `0764-0091`。[SAP FMS 語法說明](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/syntax-of-formatted-search-in-sap-business-one-version-for-sap-hana/ba-p/13270247)

設定方式：

1. 將對應資料庫版本的 SQL 存到「查詢管理員」。
2. 游標放在「客戶料號」欄位，按 **Alt + Shift + F2** 開啟 FMS 設定，選取剛才儲存的查詢。
3. 啟用欄位變更時自動重新整理，觸發欄位選 **「項目號碼」**。

完成後，輸入或變更項目號碼，就會帶入該項目主檔的客戶料號。

---

