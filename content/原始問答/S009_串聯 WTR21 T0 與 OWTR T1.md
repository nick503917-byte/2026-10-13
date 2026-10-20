# 串聯 WTR21 T0 與 OWTR T1

來源編號：S009  
聊天 ID：`019f875a-68c6-7872-8231-a247ba75868d`  
聊天起始日期（台北）：2026-07-22  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S009-Q001｜WTR21 T0 , OWTR T1 的這兩個有關聯 T0.[RefDocNum], T1.[DocNum] 幫我用SQL 串起來 然後SELECT 就先空著

日期：2026-07-22

**你的提問**

WTR21 T0 , OWTR T1 的這兩個有關聯 T0.[RefDocNum], T1.[DocNum] 幫我用SQL 串起來 然後SELECT 就先空著

**當時回答**

```sql
SELECT
    -- 欄位先保留空白
FROM WTR21 AS T0
INNER JOIN OWTR AS T1
    ON T0.[RefDocNum] = T1.[DocNum];
```

---

<a id="q2"></a>

## S009-Q002｜幫我把T0 WTR21 及 T1 OWTR 作對調

日期：2026-07-22

**你的提問**

幫我把T0 WTR21 及 T1 OWTR 作對調

**當時回答**

```sql
SELECT
    -- 欄位先保留空白
FROM OWTR AS T1
INNER JOIN WTR21 AS T0
    ON T1.[DocNum] = T0.[RefDocNum];
```

---

