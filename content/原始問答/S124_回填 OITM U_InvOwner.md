# 回填 OITM U_InvOwner

來源編號：S124  
聊天 ID：`01a05bd7-56c7-7d93-ab66-c5f51e14a040`  
聊天起始日期（台北）：2026-09-01  
保存位置：一般本機紀錄  
問答段落：3

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S124-Q001｜外購原物料抓採購單建立人 OPOR.SlpCode 自製原物料抓工單建立人 OWOR.UserSign 然後OWOR只抓 OITM.ItmsGrpCod = 103 其餘不用 成品…

日期：2026-09-01

**你的提問**

外購原物料抓採購單建立人 OPOR.SlpCode
自製原物料抓工單建立人 OWOR.UserSign 然後OWOR只抓 OITM.ItmsGrpCod = 103 其餘不用
成品抓業務銷售訂單  ORDR.SlpCode
然後回填到 OITM.U\_InvOwner

**當時回答**

已建立 SAP B1 SQL Server 回填程式：

[OITM_庫存負責人回填.sql](<C:\Users\nick_yeh\Documents\維熹卡控\OITM_庫存負責人回填.sql>)

規則如下：

- 外購原物料：`OPOR.SlpCode` → `OITM.U_InvOwner`
  - 限 `ItmsGrpCod = 103`
  - 限 `PrcrmntMtd = 'B'`
- 自製原物料：`OWOR.UserSign` → `OITM.U_InvOwner`
  - 只處理 `ItmsGrpCod = 103`
- 成品：`ORDR.SlpCode` → `OITM.U_InvOwner`
  - 限 `ItmsGrpCod = 101`

程式可直接放進 `SBO_SP_TransactionNotification`，並只更新當次單據涉及的品號。

---

<a id="q2"></a>

## S124-Q002｜我現在寫入會是數字 但我想連結到 OSLP的 SlpName

日期：2026-09-01

**你的提問**

我現在寫入會是數字 但我想連結到 OSLP的 SlpName

**當時回答**

已修改 [OITM_庫存負責人回填.sql](<C:\Users\nick_yeh\Documents\維熹卡控\OITM_庫存負責人回填.sql>)：

- `OPOR.SlpCode` → `OSLP.SlpName`
- `ORDR.SlpCode` → `OSLP.SlpName`
- `OWOR.UserSign` → `OUSR.U_NAME`

其中 `OWOR.UserSign` 是使用者 ID，正確關聯是：

```sql
OUSR.USERID = OWOR.UserSign
```

它不能直接對應 `OSLP.SlpCode`。現在 `OITM.U_InvOwner` 寫入的會是姓名，不再是數字。

---

<a id="q3"></a>

## S124-Q003｜我沒辦法打開

日期：2026-09-01

**你的提問**

我沒辦法打開

**當時回答**

已經幫你在 Codex 右側開啟檔案。完整 SQL 如下：

```sql
-- 採購單：外購原物料
IF @object_type = '22' AND @transaction_type IN ('A', 'U')
BEGIN
    UPDATE I
       SET I.U_InvOwner = S.SlpName
    FROM OITM I
    INNER JOIN POR1 L
        ON L.ItemCode = I.ItemCode
    INNER JOIN OPOR H
        ON H.DocEntry = L.DocEntry
    INNER JOIN OSLP S
        ON S.SlpCode = H.SlpCode
    WHERE H.DocEntry =
          TRY_CONVERT(INT, @list_of_cols_val_tab_del)
      AND I.ItmsGrpCod = 103
      AND I.PrcrmntMtd = 'B';
END;


-- 生產訂單：自製原物料
IF @object_type = '202'
   AND @transaction_type IN ('A', 'U')
BEGIN
    UPDATE I
       SET I.U_InvOwner = U.U_NAME
    FROM OITM I
    INNER JOIN OWOR W
        ON W.ItemCode = I.ItemCode
    INNER JOIN OUSR U
        ON U.USERID = W.UserSign
    WHERE W.DocEntry =
          TRY_CONVERT(INT, @list_of_cols_val_tab_del)
      AND I.ItmsGrpCod = 103;
END;


-- 銷售訂單：成品
IF @object_type = '17'
   AND @transaction_type IN ('A', 'U')
BEGIN
    UPDATE I
       SET I.U_InvOwner = S.SlpName
    FROM OITM I
    INNER JOIN RDR1 L
        ON L.ItemCode = I.ItemCode
    INNER JOIN ORDR H
        ON H.DocEntry = L.DocEntry
    INNER JOIN OSLP S
        ON S.SlpCode = H.SlpCode
    WHERE H.DocEntry =
          TRY_CONVERT(INT, @list_of_cols_val_tab_del)
      AND I.ItmsGrpCod = 101;
END;
```

檔案位置：

`C:\Users\nick_yeh\Documents\維熹卡控\OITM_庫存負責人回填.sql`

---

