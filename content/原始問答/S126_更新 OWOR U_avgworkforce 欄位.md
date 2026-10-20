# 更新 OWOR U_avgworkforce 欄位

來源編號：S126  
聊天 ID：`01a06001-95f9-7340-906a-638ffb59c93d`  
聊天起始日期（台北）：2026-09-02  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S126-Q001｜OWOR U\_avgworkforce 的欄位 要帶入 U\_energy/ U\_workforce

日期：2026-09-02

**你的提問**

OWOR U\_avgworkforce 的欄位 要帶入 U\_energy/ U\_workforce

**當時回答**

目前工作目錄只有兩個 CSS 檔，找不到 `OWOR` 相關程式或 SQL，無法直接修改。

請提供程式／SQL 檔案位置。預期公式會寫成：

```sql
U_avgworkforce = U_energy / NULLIF(U_workforce, 0)
```

`NULLIF` 可避免 `U_workforce = 0` 時發生除以零錯誤。

---

<a id="q2"></a>

## S126-Q002｜我要寫在 OWOR U\_avgworkforce欄位裡面

日期：2026-09-02

**你的提問**

我要寫在 OWOR U\_avgworkforce欄位裡面

**當時回答**

如果是 SAP Business One 的「格式化搜尋」，請把下面查詢指定到 `OWOR.U_avgworkforce` 欄位：

```sql
SELECT
    $[OWOR.U_energy.NUMBER] /
    NULLIF($[OWOR.U_workforce.NUMBER], 0)
```

設定自動重新整理，觸發欄位選擇 `U_energy` 與 `U_workforce`。如此 `U_avgworkforce` 就會帶入：

```text
U_energy ÷ U_workforce
```

當 `U_workforce` 為 0 時會回傳空值，避免除以零錯誤。

---

