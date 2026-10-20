# 確認FMS帶入退貨理由

來源編號：S049  
聊天 ID：`019fcf8f-f19d-78c1-a3b8-d9c9b22d6768`  
聊天起始日期（台北）：2026-08-05  
保存位置：一般本機紀錄  
問答段落：12

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S049-Q001｜如果我表身的 自定義欄位退貨理由 是下拉式選單 ，當我選擇的時候 我的另一個欄位 自定義欄位叫申請說明，我想要用FMS 直接帶退貨理由的字段 這可以嗎

日期：2026-08-05

**你的提問**

如果我表身的 自定義欄位退貨理由 是下拉式選單 ，當我選擇的時候 我的另一個欄位 自定義欄位叫申請說明，我想要用FMS 直接帶退貨理由的字段 這可以嗎

**當時回答**

可以做到，邏輯是：當「退貨理由」下拉選單變更時，透過 FMS 將所選值寫入表身的「申請說明」欄位。

不過實際語法取決於你使用的系統與 FMS 版本，而且要確認：

- 「退貨理由」儲存的是顯示文字，還是選項代碼。
- 「申請說明」是否允許由公式／事件自動寫入。
- 觸發時機設定為「退貨理由欄位變更」。
- 若是表身多筆資料，需抓取目前所在列的欄位，不能抓表頭或第一列。

概念上會類似：

```text
申請說明 = 目前列.退貨理由
```

如果下拉欄位存的是代碼，則要先轉成顯示名稱：

```text
申請說明 = 取得選項文字(目前列.退貨理由)
```

請告訴我「FMS」是哪一套系統（也可以貼設定畫面或目前的 FMS 語法），我就能直接幫你寫出可使用的公式。

---

<a id="q2"></a>

## S049-Q002｜SAPB1的fms

日期：2026-08-05

**你的提問**

SAPB1的fms

**當時回答**

可以。SAP Business One 的 FMS 可以在你变更表身「退貨理由」后，自动把当前列的值带入「申请说明」。

假设：

- 表身资料表：`RDN1`（销售退货）
- 退货理由：`U_ReturnReason`
- 申请说明：`U_ApplyDesc`

SQL Server 查询：

```sql
SELECT $[RDN1.U_ReturnReason]
```

SAP HANA 查询：

```sql
SELECT $[RDN1."U_ReturnReason"] FROM DUMMY
```

设置步骤：

1. 在「查询管理器」保存上述查询。
2. 回到单据，将光标放在表身的「申请说明」字段。
3. 按 `Alt + Shift + F2`。
4. 选择「根据已保存查询搜索现有用户定义值」。
5. 选择刚保存的查询。
6. 勾选「字段更改时自动刷新」。
7. 触发字段选择表身的「退货理由」。
8. 触发方式选择「退出已更改栏位时」。
9. 更新并测试。

如果下拉选单显示的是说明、实际储存的是代码，例如：

- 储存值：`01`
- 显示值：`商品损坏`

上述简单查询可能会带出 `01`。若你希望带出「商品损坏」，SQL Server 可用：

```sql
SELECT T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T1.TableID = T0.TableID
   AND T1.FieldID = T0.FieldID
WHERE T0.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnReason'
  AND T0.FldValue = $[RDN1.U_ReturnReason]
```

其中 `AliasID` 通常不包含 `U_`。

你的表身资料表不一定是 `RDN1`；销售退货通常是 `RDN1`，退货申请则可能是其他资料表。可以开启 SAP B1 的「查看 → 系统信息」，把鼠标移到两个栏位上，将显示的表名和字段名贴给我，我可以替你改成完全对应的 FMS。SAP 官方也确认 FMS 能根据触发字段变化，自动把查询结果写入目标字段：[SAP Business One—设置查询型用户定义值](https://help.sap.com/docs/SAP_BUSINESS_ONE/df269580ff9a49f5922598346b756252/b1a4654d388b44e190ddeb0c015f99dc.html?locale=en-US)。

---

<a id="q3"></a>

## S049-Q003｜SQL Server

日期：2026-08-05

**你的提問**

SQL Server

**當時回答**

SQL Server 的做法如下。假设表身是销售退货明细 `RDN1`，退货理由字段为 `U_ReturnReason`：

```sql
SELECT $[RDN1.U_ReturnReason]
```

把这条查询保存后，将 FMS 绑定到表身的「申请说明」字段，并设置：

- 勾选「字段更改时自动刷新」
- 触发字段：「退货理由」
- 刷新条件：「退出已更改栏位时」

如果你希望带入下拉选单的显示说明，而不是代码，则使用：

```sql
SELECT T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T1.TableID = T0.TableID
   AND T1.FieldID = T0.FieldID
WHERE T0.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnReason'
  AND T0.FldValue = $[RDN1.U_ReturnReason]
```

注意：

- `RDN1` 请替换成系统信息显示的实际表名。
- `U_ReturnReason` 请替换成「退货理由」实际字段名。
- `AliasID` 通常不含 `U_`，所以这里写 `ReturnReason`。
- 如果下拉选单的代码是文字型态，最后一行可能需要加单引号：

```sql
AND T0.FldValue = '$[RDN1.U_ReturnReason]'
```

若你提供「退货理由」与「申请说明」在系统信息中显示的 `Table` 和 `Field`，我可以直接给你一条可复制使用的完整查询。

---

<a id="q4"></a>

## S049-Q004｜但我抓出來的是 變成是值 但我想要說明

日期：2026-08-05

**你的提問**

# Files mentioned by the user:

## codex-clipboard-00f98101-c3a6-4eab-9e71-621764226d8e.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-00f98101-c3a6-4eab-9e71-621764226d8e.png

## My request for Codex:
但我抓出來的是 變成是值 但我想要說明 

[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，畫面中的「值」是 `RR01`，而你要的是右邊「說明」，所以不能直接複製 `U_ReturnReason`，要到 SAP B1 的 `UFD1` 表查 `Descr`。

假設表身資料表是 `RDN1`，請用：

```sql
SELECT T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T0.TableID = T1.TableID
   AND T0.FieldID = T1.FieldID
WHERE T1.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnReason'
  AND T0.FldValue = $[RDN1.U_ReturnReason]
```

效果會是：

```text
RR01 → 銷售遲點折讓
RR02 → 價格異議折讓
RR03 → 品質因素折讓
```

注意：

- 你畫面上的標題是 `ReturnReason`，因此数据库字段一般是 `U_ReturnReason`。
- `CUFD.AliasID` 要写 `ReturnReason`，不加 `U_`。
- `RDN1` 必须换成「退货理由」实际所属的表身资料表。

如果执行时出现 SQL 语法错误，可以将最后一行改成：

```sql
AND T0.FldValue = '$[RDN1.U_ReturnReason]'
```

若你现在直接抓值的 FMS 是类似：

```sql
SELECT $[$38.U_ReturnReason.0]
```

也可以沿用这个当前栏位语法：

```sql
SELECT T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T0.TableID = T1.TableID
   AND T0.FieldID = T1.FieldID
WHERE T1.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnReason'
  AND T0.FldValue = $[$38.U_ReturnReason.0]
```

最准确的方式是把你目前「可以抓出 RR01」的那条 FMS 查询贴给我，我可以直接改成抓右边的说明。

---

<a id="q5"></a>

## S049-Q005｜發生內部錯誤 (-1004) [訊息 131-183]

日期：2026-08-05

**你的提問**

發生內部錯誤 (-1004)  [訊息 131-183]

**當時回答**

`-1004` 通常是 FMS 引用的表名／栏位名不属于当前画面，或文字值 `RR01` 没有加单引号造成的。

先把最后一行改成带单引号：

```sql
SELECT T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T0.TableID = T1.TableID
   AND T0.FieldID = T1.FieldID
WHERE T1.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnReason'
  AND T0.FldValue = '$[RDN1.U_ReturnReason]'
```

但 `RDN1` 和 `U_ReturnReason` 只是示例，必须与实际栏位完全一致。

先在查询管理器执行以下查询，找出正确的表名与 Alias：

```sql
SELECT
    TableID,
    FieldID,
    AliasID,
    Descr
FROM CUFD
WHERE AliasID = 'ReturnReason'
   OR Descr = 'ReturnReason'
```

取得结果后，先用固定值测试说明查询，例如：

```sql
SELECT T0.Descr
FROM UFD1 T0
WHERE T0.TableID = '实际TableID'
  AND T0.FieldID = 实际FieldID
  AND T0.FldValue = 'RR01'
```

如果这个查询能得到「销售迟点折让」，代表说明资料没有问题，剩下只是当前表身栏位的 FMS 引用方式。

请把两项资料贴给我：

1. 上面 `CUFD` 查询的结果。
2. 你原本能够抓出 `RR01` 的完整 FMS 查询。

我就可以直接改成可执行的版本；目前最可能的问题是实际表身并不是 `RDN1`，或数据库 Alias 并不是 `U_ReturnReason`。

---

<a id="q6"></a>

## S049-Q006｜發生內部錯誤 (-1004) [訊息 131-183]

日期：2026-08-05

**你的提問**

發生內部錯誤 (-1004)  [訊息 131-183]

**當時回答**

这个 `-1004` 很可能是因为在「查询管理器」里直接执行了含有 FMS 变量的查询：

```sql
$[RDN1.U_ReturnReason]
```

这种变量只有在对应单据画面中触发 FMS 时才有值，不能直接在查询管理器里按「执行」测试，否则可能出现 `-1004`。

请按以下顺序测试。

先在查询管理器执行纯 SQL：

```sql
SELECT
    T0.TableID,
    T0.FieldID,
    T0.FldValue,
    T0.Descr
FROM UFD1 T0
WHERE T0.FldValue = 'RR01'
```

如果能找到 `RR01` 及其说明，代表资料正常。

然后把你原先能够成功取得 `RR01` 的表达式放进查询。例如原查询是：

```sql
SELECT $[$38.10000123.0]
```

则改为：

```sql
SELECT TOP 1 T0.Descr
FROM UFD1 T0
WHERE T0.TableID = '实际表名'
  AND T0.FieldID = 实际FieldID
  AND T0.FldValue = $[$38.10000123.0]
```

如果当前值必须加引号，则使用：

```sql
SELECT TOP 1 T0.Descr
FROM UFD1 T0
WHERE T0.TableID = '实际表名'
  AND T0.FieldID = 实际FieldID
  AND T0.FldValue = '$[$38.10000123.0]'
```

重点是：表身下拉栏位建议使用 `$[$项目编号.栏位编号.0]`，因为它会抓当前列；直接写 `$[RDN1.U_ReturnReason]` 可能因实际单据表不同而产生 `-1004`。

请直接贴出你目前「能够成功带出 RR01」的原始查询。没有那条查询中的项目编号和栏位编号，我无法可靠地替换成抓说明的版本。

---

<a id="q7"></a>

## S049-Q007｜圖片／附件提問

日期：2026-08-05

**你的提問**

# Files mentioned by the user:

## codex-clipboard-b9dc38e8-b3c9-4f9a-a614-441333828e2c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-b9dc38e8-b3c9-4f9a-a614-441333828e2c.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

畫面確認你要的是 `RR04` 右側的「其他折讓」。先不要連接 `CUFD`，改用最簡單的查询：

```sql
SELECT TOP 1 T0.[Descr]
FROM [UFD1] T0
WHERE T0.[FldValue] = $[RDN1.U_ReturnReason]
```

如果你原本這條可以成功取得 `RR04`：

```sql
SELECT $[RDN1.U_ReturnReason]
```

上面的查询就應該取得：

```text
其他折讓
```

注意：

- `$[RDN1.U_ReturnReason]` 外面不要加單引號。
- 含 `$[...]` 的查询只需要「保存」，不要在查询管理器直接执行。
- 回到单据画面，通过 FMS 自动刷新测试。
- FMS 要绑定在「申请说明」，触发栏位选择「退货理由」。

如果不同自定义栏位也使用相同的 `RR04`，则需增加 `TableID` 和 `FieldID`：

```sql
SELECT TOP 1 T0.[Descr]
FROM [UFD1] T0
WHERE T0.[TableID] = 'RDN1'
  AND T0.[FieldID] = 你的FieldID
  AND T0.[FldValue] = $[RDN1.U_ReturnReason]
```

你这张截图只显示「标题」，没有显示实际的 `TableID`、`FieldID` 和数据库栏位名称。若简化版仍出现 `-1004`，请贴出你目前可以成功带出 `RR04` 的完整查询；我会直接保留其 FMS 栏位变量，只加上取得 `[Descr]` 的部分。

---

<a id="q8"></a>

## S049-Q008｜SQL／程式碼調整與說明

日期：2026-08-05

**你的提問**

SELECT T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T1.TableID = T0.TableID
   AND T1.FieldID = T0.FieldID
WHERE T0.TableID = 'RDN1'
  ---AND T1.AliasID = 'ReturnReason' 
  --AND T0.FldValue = $[RDN1.U_ReturnReason]

這樣的話 我可以查出資料

**當時回答**

這表示 `UFD1`、`CUFD` 和 `RDN1` 都能正常查询。问题在于：

```sql
T1.AliasID = 'ReturnReason'
```

这里的 `ReturnReason` 是截图中的「标题」，不一定是实际的 `AliasID`。先执行：

```sql
SELECT DISTINCT
    T1.TableID,
    T1.FieldID,
    T1.AliasID,
    T1.Descr,
    T0.FldValue,
    T0.Descr AS ValueDescription
FROM CUFD T1
INNER JOIN UFD1 T0
    ON T0.TableID = T1.TableID
   AND T0.FieldID = T1.FieldID
WHERE T1.TableID = 'RDN1'
  AND T0.FldValue = 'RR01'
```

结果会显示真正的 `AliasID`。假设查到：

```text
AliasID = ReturnRsn
```

那么实际数据库栏位就是：

```text
U_ReturnRsn
```

最终 FMS 写法：

```sql
SELECT TOP 1 T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T1.TableID = T0.TableID
   AND T1.FieldID = T0.FieldID
WHERE T1.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnRsn'
  AND T0.FldValue = $[RDN1.U_ReturnRsn]
```

也就是这两个地方必须使用实际 Alias：

```sql
T1.AliasID = '实际AliasID'
$[RDN1.U_实际AliasID]
```

不要直接使用画面标题 `ReturnReason`，除非查询结果确认它确实是 `AliasID`。

另外，你目前的注释：

```sql
---AND ...
```

虽然 SQL Server 通常能够识别，但建议统一写成两个减号：

```sql
-- AND ...
```

先执行上面的 `RR01` 查询，把查到的 `AliasID` 告诉我，我就能给你最终可直接贴上的版本。

---

<a id="q9"></a>

## S049-Q009｜甚麼意思 〔程式碼〕

日期：2026-08-05

**你的提問**

甚麼意思
```
T1.AliasID = '实际AliasID'
$[RDN1.U_实际AliasID]
```

**當時回答**

「实际AliasID」只是占位文字，要替换成你的 SAP B1 自定义字段真正的数据库名称。

例如，如果查询结果显示：

```text
AliasID = ReturnReason
```

那么完整查询就是：

```sql
SELECT TOP 1 T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T1.TableID = T0.TableID
   AND T1.FieldID = T0.FieldID
WHERE T1.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnReason'
  AND T0.FldValue = $[RDN1.U_ReturnReason]
```

两种写法的关系是：

```text
AliasID             ReturnReason
数据库实际字段       U_ReturnReason
```

也就是 SAP B1 会自动在自定义字段的 Alias 前加上 `U_`：

```sql
T1.AliasID = 'ReturnReason'       -- 不加 U_
$[RDN1.U_ReturnReason]            -- 要加 U_
```

先执行这个查询查看你真正的 AliasID：

```sql
SELECT DISTINCT
    T1.FieldID,
    T1.AliasID,
    T1.Descr
FROM CUFD T1
INNER JOIN UFD1 T0
    ON T0.TableID = T1.TableID
   AND T0.FieldID = T1.FieldID
WHERE T1.TableID = 'RDN1'
  AND T0.FldValue = 'RR01'
```

如果结果确实显示 `ReturnReason`，就直接使用上面的完整查询。如果显示别的名称，例如 `RtnReason`，就应改为：

```sql
AND T1.AliasID = 'RtnReason'
AND T0.FldValue = $[RDN1.U_RtnReason]
```

---

<a id="q10"></a>

## S049-Q010｜我是要看哪一個欄位的

日期：2026-08-05

**你的提問**

我是要看哪一個欄位的

**當時回答**

你要看的是「退貨理由」這個自訂欄位的 `AliasID`，不是「申請說明」的。

請執行：

```sql
SELECT DISTINCT
    T1.FieldID,
    T1.AliasID,
    T1.Descr
FROM CUFD T1
INNER JOIN UFD1 T0
    ON T0.TableID = T1.TableID
   AND T0.FieldID = T1.FieldID
WHERE T1.TableID = 'RDN1'
  AND T0.FldValue = 'RR01'
```

結果可能像：

| FieldID | AliasID | Descr |
|---:|---|---|
| 12 | ReturnReason | ReturnReason |

你需要看的就是：

```text
AliasID = ReturnReason
```

那么 FMS 查询为：

```sql
SELECT TOP 1 T0.Descr
FROM UFD1 T0
INNER JOIN CUFD T1
    ON T1.TableID = T0.TableID
   AND T1.FieldID = T0.FieldID
WHERE T1.TableID = 'RDN1'
  AND T1.AliasID = 'ReturnReason'
  AND T0.FldValue = $[RDN1.U_ReturnReason]
```

简单区分：

- 来源栏位：退货理由——需要查它的 `AliasID`。
- 目标栏位：申请说明——将 FMS 绑定在这里。
- FMS 功能：读取退货理由的代码 `RR01`，查询对应的 `Descr`，再写入申请说明。

---

<a id="q11"></a>

## S049-Q011｜可以讓我先查詢嗎

日期：2026-08-05

**你的提問**

可以讓我先查詢嗎

**當時回答**

可以。先不要使用 FMS 变量，直接在 SAP B1 查询管理器执行下面这条查询：

```sql
SELECT DISTINCT
    T1.TableID,
    T1.FieldID,
    T1.AliasID,
    T1.Descr AS FieldTitle,
    T0.FldValue,
    T0.Descr AS ValueDescription
FROM CUFD T1
INNER JOIN UFD1 T0
    ON T0.TableID = T1.TableID
   AND T0.FieldID = T1.FieldID
WHERE T1.TableID = 'RDN1'
  AND T0.FldValue = 'RR01'
```

查询结果应该类似：

| TableID | FieldID | AliasID | FieldTitle | FldValue | ValueDescription |
|---|---:|---|---|---|---|
| RDN1 | 12 | ReturnReason | ReturnReason | RR01 | 销售迟点折让 |

你先看查询结果中的：

```text
AliasID
```

把这一列显示的内容贴给我即可。这个查询不含 `$[...]`，因此可以直接在查询管理器中执行。

---

<a id="q12"></a>

## S049-Q012｜圖片／附件提問

日期：2026-08-05

**你的提問**

# Files mentioned by the user:

## codex-clipboard-a90a2bf9-95ea-4ea9-99ee-ebaa56e9be96.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-a90a2bf9-95ea-4ea9-99ee-ebaa56e9be96.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

查詢結果已確認「退貨理由」欄位資料：

- 表格：`RDN1`
- FieldID：`6`
- AliasID：`ReturnReason`
- 資料庫欄位：`U_ReturnReason`

因為它是表身欄位，建議用目前列的矩陣語法。FMS 查询写成：

```sql
SELECT TOP 1 T0.[Descr]
FROM [UFD1] T0
WHERE T0.[TableID] = 'RDN1'
  AND T0.[FieldID] = 6
  AND T0.[FldValue] = $[$38.U_ReturnReason.0]
```

这个查询会把：

```text
RR01
```

转换成：

```text
销售退点折让
```

设置方式：

1. 在查询管理器中保存查询，不要直接执行，因为 `$[$38.U_ReturnReason.0]` 只能在单据画面内解析。
2. 光标放在表身「申请说明」。
3. 按 `Alt + Shift + F2`。
4. 选择这个已保存查询。
5. 勾选「字段更改时自动刷新」。
6. 触发字段选择「退货理由」。
7. 条件选择「退出已更改栏位时」。

这里的 `38` 是 SAP B1 行销文件表身矩阵常用的 Item ID。若仍出现 `-1004`，请开启「查看 → 系统信息」，将鼠标移到表身的「退货理由」栏位上，确认画面底部显示的 `Item` 与 `Column`；需要替换的是：

```text
$[$Item编号.Column编号.0]
```

---

