# 銷售訂單 DTW 缺少必填欄位

來源編號：S120  
聊天 ID：`01a05579-c689-76a3-b70b-b9172bd0ee41`  
聊天起始日期（台北）：2026-08-31  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S120-Q001｜圖片／附件提問

日期：2026-08-31

**你的提問**

# Files mentioned by the user:

## codex-clipboard-6aa9b1bd-9dab-45cd-a2e6-51daae056799.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-6aa9b1bd-9dab-45cd-a2e6-51daae056799.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

The screenshot shows 182 failed record creations. Each failure reports:

> “(1901) Applicant, Applicants Department, Shipping Charge Field are required. Transaction commit unsuccessfully.”

In other words, the import is missing required values for **Applicant**, **Applicants Department**, and **Shipping Charge**.

Would you like me to troubleshoot the import, extract the error list, or explain how to fix these required fields?

---

<a id="q2"></a>

## S120-Q002｜我再會銷售訂單

日期：2026-08-31

**你的提問**

我再會銷售訂單

**當時回答**

了解，你是在**匯入銷售訂單**。畫面顯示 182 筆都建立失敗，原因是每張訂單缺少以下必填欄位：

- Applicant（申請人）
- Applicants Department（申請人部門）
- Shipping Charge（運費）

請回到銷售訂單匯入檔，找到對應欄位並補值；若這些是系統的自訂欄位（UDF），還要確認匯入範本使用的是正確欄位代碼，然後再按 **Re-Import Errors** 重匯失敗資料。

如果你把匯入用的 Excel／CSV 檔附上來，我可以直接幫你找出應填的欄位。

---

