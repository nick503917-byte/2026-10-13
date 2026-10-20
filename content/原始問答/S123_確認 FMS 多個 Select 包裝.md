# 確認 FMS 多個 Select 包裝

來源編號：S123  
聊天 ID：`01a05bd4-f8e2-7ed2-93a9-2843a6f25ebb`  
聊天起始日期（台北）：2026-09-01  
保存位置：一般本機紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S123-Q001｜我想問 fms 可寫三個select 然後我把她包在一起嗎 有點像 (A 查詢 B 查詢 C 查詢 ) 但我只有一個查詢會有結果

日期：2026-09-01

**你的提問**

我想問 fms 可寫三個select 然後我把她包在一起嗎 

有點像 (A 查詢 B 查詢 C 查詢 ) 但我只有一個查詢會有結果

**當時回答**

可以。若 A、B、C 三個 `SELECT` 的欄位數與型別一致，而且只有一個查詢會有資料，最直接是用 `UNION ALL` 包在一起：

```sql
SELECT 欄位1, 欄位2 FROM A查詢條件
UNION ALL
SELECT 欄位1, 欄位2 FROM B查詢條件
UNION ALL
SELECT 欄位1, 欄位2 FROM C查詢條件;
```

例如：

```sql
SELECT name, amount
FROM table_a
WHERE id = :id

UNION ALL

SELECT name, amount
FROM table_b
WHERE id = :id

UNION ALL

SELECT name, amount
FROM table_c
WHERE id = :id;
```

因為你確定只有一個查詢會有結果，建議用 `UNION ALL`，不需要讓資料庫額外去除重複資料。

如果三個查詢都是「回傳單一值」，也可以用 `COALESCE` 取第一個非 `NULL` 的結果：

```sql
SELECT COALESCE(
    (SELECT value FROM table_a WHERE id = :id),
    (SELECT value FROM table_b WHERE id = :id),
    (SELECT value FROM table_c WHERE id = :id)
) AS result;
```

差別是：

- 要回傳完整資料列：用 `UNION ALL`
- 只要一個值：用 `COALESCE`
- 三個查詢的欄位不同：需要先用別名、`NULL` 或型別轉換，把輸出欄位整理成相同格式

如果你說的 FMS 是 FileMaker，透過 `ExecuteSQL` 也能使用 `UNION ALL`；把你目前的三段查詢貼出來，我可以直接幫你合併。

---

