# 修正 OITM.OitmName SQL

來源編號：S162  
聊天 ID：`01a0891d-e8a1-7130-a8d6-b3d24a34c9f1`  
聊天起始日期（台北）：2026-09-10  
保存位置：一般本機紀錄  
問答段落：4

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S162-Q001｜SQL／程式碼調整與說明

日期：2026-09-10

**你的提問**

SELECT\
    CASE\
        WHEN T0.ItmsGrpCod = 103\
        THEN\
            CONCAT\_WS(' ',\
                NULLIF(T0.U\_Seg1Desc, ''),\
                NULLIF(T0.U\_Seg2Desc, ''),\
                NULLIF(T0.U\_Seg3Desc, ''),\
                NULLIF(T0.U\_Seg4Desc, ''),\
                NULLIF(T0.U\_Seg5Desc, ''),\
                NULLIF(T0.U\_Seg6Desc, ''),\
                NULLIF(T0.U\_Seg7Desc, ''),\
                NULLIF(T0.U\_Seg8Desc, ''),\
                NULLIF(T0.U\_Seg9Desc, ''),\
                NULLIF(T0.U\_Seg10Desc, ''),\
                NULLIF(T0.U\_Seg11Desc, ''),\
                NULLIF(T0.U\_Seg12Desc, ''),\
                NULLIF(T0.U\_Seg13Desc, '')\
            )\
        ELSE ''\
    END\
FROM OITM T0\
WHERE T0.ItemCode = $[OITM.ItemCode]

我要放在OITM.OitmName

**當時回答**

可以，品項名稱的正確欄位是 **`OITM.ItemName`**。將查詢綁定到畫面的「品項名稱」格式化搜尋，查詢結果就會填入該欄位，不需要使用 `UPDATE`。[SAP 欄位說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/OITM.html)

沿用你原本的查詢，移除貼上時產生的跳脫字元即可：

```sql
SELECT
    CASE
        WHEN T0.ItmsGrpCod = 103 THEN
            CONCAT_WS(' ',
                NULLIF(T0.U_Seg1Desc, ''),
                NULLIF(T0.U_Seg2Desc, ''),
                NULLIF(T0.U_Seg3Desc, ''),
                NULLIF(T0.U_Seg4Desc, ''),
                NULLIF(T0.U_Seg5Desc, ''),
                NULLIF(T0.U_Seg6Desc, ''),
                NULLIF(T0.U_Seg7Desc, ''),
                NULLIF(T0.U_Seg8Desc, ''),
                NULLIF(T0.U_Seg9Desc, ''),
                NULLIF(T0.U_Seg10Desc, ''),
                NULLIF(T0.U_Seg11Desc, ''),
                NULLIF(T0.U_Seg12Desc, ''),
                NULLIF(T0.U_Seg13Desc, '')
            )
        ELSE ''
    END
FROM OITM T0
WHERE T0.ItemCode = $[OITM.ItemCode]
```

儲存查詢後，在「品項名稱」欄位設定格式化搜尋，指定這支查詢。[SAP 格式化搜尋說明](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~FormattedSearches.html)

這版有兩個行為要留意：

- **非 103 群組會回傳空白**；若想保留原名稱，將 `ELSE ''` 改為 `ELSE T0.ItemName`。
- 它讀取的是**資料庫已儲存的分段描述**。若要在新增品項或修改描述尚未儲存時立即組合名稱，需要改成讀取畫面欄位的版本。

---

<a id="q2"></a>

## S162-Q002｜跑步出來欸

日期：2026-09-10

**你的提問**

跑步出來欸

**當時回答**

你是說「跑不出來」對嗎？請貼一下執行時的**完整錯誤訊息**；如果沒有錯誤，請說明是「結果空白」還是「名稱沒有更新」，並告訴我你用的是 **SQL Server 還是 SAP HANA**。

另外，你是在「新增、尚未儲存的品項」上執行嗎？原查詢從 `OITM` 讀取已儲存資料，新品項尚未存入資料庫時會查不到結果。

---

<a id="q3"></a>

## S162-Q003｜空白

日期：2026-09-10

**你的提問**

空白

**當時回答**

空白可能是原查詢讀取到尚未更新的資料，或群組不是 `103`。改成直接讀取**畫面上的欄位值**試試看，並綁定在「品項名稱」`OITM.ItemName`：

```sql
SELECT
    CASE
        WHEN $[OITM.ItmsGrpCod] = 103 THEN
            CONCAT_WS(' ',
                NULLIF($[OITM.U_Seg1Desc], ''),
                NULLIF($[OITM.U_Seg2Desc], ''),
                NULLIF($[OITM.U_Seg3Desc], ''),
                NULLIF($[OITM.U_Seg4Desc], ''),
                NULLIF($[OITM.U_Seg5Desc], ''),
                NULLIF($[OITM.U_Seg6Desc], ''),
                NULLIF($[OITM.U_Seg7Desc], ''),
                NULLIF($[OITM.U_Seg8Desc], ''),
                NULLIF($[OITM.U_Seg9Desc], ''),
                NULLIF($[OITM.U_Seg10Desc], ''),
                NULLIF($[OITM.U_Seg11Desc], ''),
                NULLIF($[OITM.U_Seg12Desc], ''),
                NULLIF($[OITM.U_Seg13Desc], '')
            )
        ELSE $[OITM.ItemName]
    END
```

這版沿用你原本的 **SQL Server 語法**，非 `103` 群組會保留畫面上的名稱。

如果仍然空白，先將格式化搜尋暫時改成下面這段，確認第一段描述是否讀得到：

```sql
SELECT $[OITM.U_Seg1Desc]
```

測試前，先確認畫面上的 `U_Seg1Desc` 有填內容。

---

<a id="q4"></a>

## S162-Q004｜新品項尚未存入資料庫時會查不到結果。

日期：2026-09-10

**你的提問**

新品項尚未存入資料庫時會查不到結果。

**當時回答**

對，這就是空白的原因：**新品項尚未儲存，`OITM` 裡還沒有這筆資料**，所以原本的 `FROM OITM ... WHERE ...` 查不到。

請使用上一則「直接讀取畫面欄位」的版本：

- `$[OITM.U_Seg1Desc]` 等欄位讀取畫面目前填入的值。
- 不使用 `FROM OITM` 和 `WHERE`。
- 綁定到 `OITM.ItemName`，即可在儲存新品項前組合名稱。

---

