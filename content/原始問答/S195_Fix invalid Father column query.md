# Fix invalid Father column query

來源編號：S195  
聊天 ID：`01a0c848-0c1e-7c00-9f34-ce4688d7bac5`  
聊天起始日期（台北）：2026-09-22  
保存位置：一般本機紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S195-Q001｜SQL／程式碼調整與說明

日期：2026-09-22

**你的提問**

SELECT T0.[Code],T2.[ItemName], T0.[TreeType], T0.[Qauntity], T0.[ToWH], T1.[ChildNum], T1.[Code], T1.[Quantity], T1.[Warehouse], T1.[IssueMthd] FROM [dbo].[OITT]  T0 INNER JOIN [dbo].[ITT1]  T1 ON T0.[Father] = T1.[Code] INNER JOIN OITM T2 ON T0.[Code] = T2.[ItemCode]

1\). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Invalid column name 'Father'. 2). [Microsoft][ODBC Driver 18 for SQL Server][SQL Server]Statement '後端排程工作' (OBSJ) (s) could not be prepared.

**當時回答**

錯誤在 JOIN 條件：`Father` 是明細表 `ITT1` 的欄位，不在主檔 `OITT`。應改成 **`T0.[Code] = T1.[Father]`**。[SAP 表格關聯說明](https://community.sap.com/t5/enterprise-resource-planning-q-a/relationship-between-the-oitm-and-oitt-tables/qaq-p/12714057)

修正後 SQL：

```sql
SELECT
    T0.[Code],
    T2.[ItemName],
    T0.[TreeType],
    T0.[Qauntity],
    T0.[ToWH],
    T1.[ChildNum],
    T1.[Code],
    T1.[Quantity],
    T1.[Warehouse],
    T1.[IssueMthd]
FROM [dbo].[OITT] T0
INNER JOIN [dbo].[ITT1] T1
    ON T0.[Code] = T1.[Father]
INNER JOIN [dbo].[OITM] T2
    ON T0.[Code] = T2.[ItemCode];
```

注意：`T0.[Qauntity]` 雖然看起來拼錯，但這是 SAP 的實際欄位名稱，請保留。[OITT 官方欄位定義](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKhelp/OITT.html)

第二個 `OBSJ ... could not be prepared` 訊息可能是第一個欄位錯誤引發的連帶結果，先執行以上修正版確認。

---

