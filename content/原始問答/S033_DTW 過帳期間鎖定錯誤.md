# DTW 過帳期間鎖定錯誤

來源編號：S033  
聊天 ID：`019faca7-f3d7-7793-9351-8fc833197201`  
聊天起始日期（台北）：2026-07-29  
保存位置：封存紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S033-Q001｜圖片／附件提問

日期：2026-07-29

**你的提問**

# Files mentioned by the user:

## codex-clipboard-27f112ed-6de6-429f-bbfb-8fb04e305d87.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-27f112ed-6de6-429f-bbfb-8fb04e305d87.png

## My request for Codex:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

畫面顯示匯入失敗，錯誤為：

> **Posting period locked; specify an alternative date（錯誤碼 65171）**

意思是文件使用的日期落在已鎖定的過帳期間。請在 SAP Business One：

1. 檢查匯入資料的 **Posting Date／過帳日期**。
2. 將日期改到目前開放的過帳期間；或由有權限的人員暫時解除該期間的鎖定。
3. 重新匯入資料。

若同時有 **Document Date** 或 **Due Date**，也建議確認是否落在允許的日期範圍內。

---

