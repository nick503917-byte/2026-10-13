# 避免查詢顯示已刪除的 OUSR 使用者

來源編號：S258  
聊天 ID：`01a15342-f0e4-7cc1-90c4-f1bd0a74e8cc`  
聊天起始日期（台北）：2026-10-19  
保存位置：一般本機紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S258-Q001｜SAPB1 寫查詢 的查詢產生工具 OUSR 我查了之後，會跑出已經刪除的，我該怎麼避免

日期：2026-10-19

**你的提問**

SAPB1 寫查詢 的查詢產生工具  OUSR 我查了之後，會跑出已經刪除的，我該怎麼避免

**當時回答**

可以，在查詢條件加上 **`GROUPS <> 99`**，就能排除已刪除的使用者。SAP B1 的 `OUSR` 會保留這些資料，並以 `GROUPS = 99` 標記已刪除。[SAP 官方欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/ousr.html)

**如果你使用 SQL Server：**

```sql
SELECT T0.[USER_CODE], T0.[U_NAME]
FROM OUSR T0
WHERE T0.[GROUPS] <> 99
```

在「查詢產生器」的 **Where 欄位**，填入以下條件即可，不用再寫 `WHERE`：

```sql
T0.[GROUPS] <> 99
```

**如果你使用 SAP HANA：**

```sql
SELECT T0."USER_CODE", T0."U_NAME"
FROM "OUSR" T0
WHERE T0."GROUPS" <> 99
```

如果原本已有查詢條件，就在後面加 `AND T0.[GROUPS] <> 99`（HANA 改用雙引號）。

---

