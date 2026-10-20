# 查找 SAP B1 網頁版登入互斥設定

來源編號：S221  
聊天 ID：`01a10b2e-7ab8-73d2-8f63-25239a313f22`  
聊天起始日期（台北）：2026-10-05  
保存位置：一般本機紀錄  
問答段落：9

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S221-Q001｜我 SAPB1網頁版登入，會踢掉另一個 這個可以卡控嗎

日期：2026-10-05

**你的提問**

我 SAPB1網頁版登入，會踢掉另一個 這個可以卡控嗎

**當時回答**

你是想要 **「帳號已有人登入時，擋住後登入的人，不要把原本的人踢掉」** 嗎？

這要先確認你使用的「網頁版」是哪一種，才能判斷有沒有原生設定可以卡控：

- **Web Client**：介面是網頁磁磚式。
- **Browser Access**：用瀏覽器開啟，但畫面像傳統 SAP B1 桌面版。

兩者不能直接套用相同的登入限制。SAP 官方也說明，Web Client 可以和桌面版同時登入，所以不能單憑「網頁登入會踢人」就認定是正常限制。[SAP 官方說明](https://help.sap.com/doc/53c252d19f014edf88cd380c798514fe/10.0/en-US/WebClient_UserGuide.pdf)

你補充這三點，我才能確認卡控方式：

1. 被踢掉的是不是使用**同一個 SAP 帳號**？
2. 被踢掉的是**另一個網頁，還是桌面版**？
3. 你希望**擋住後登入者**，還是**允許兩邊同時使用**？

如果方便，也可以貼被踢掉時的訊息或畫面。

---

<a id="q2"></a>

## S221-Q002｜網頁版跟往葉板

日期：2026-10-05

**你的提問**

網頁版跟往葉板

**當時回答**

了解，你是指 **網頁版登入後，會把另一個網頁版踢掉**。

再確認一下：兩邊是用**同一個 SAP 帳號**嗎？你想要的卡控是「**保留先登入的人，擋住後登入的人**」對嗎？

---

<a id="q3"></a>

## S221-Q003｜網頁版登入後，會把另一個網頁版踢掉

日期：2026-10-05

**你的提問**

**網頁版登入後，會把另一個網頁版踢掉**

**當時回答**

了解，確定是**網頁版互踢**。但還不能確定能否設定成「擋後登入的人、保留原本登入的人」。

關鍵是：**兩邊用的是同一個 SAP 帳號，還是不同帳號？**

---

<a id="q4"></a>

## S221-Q004｜我想問一下 DTW 可以匯入匯率嗎

日期：2026-10-05

**你的提問**

我想問一下 DTW 可以匯入匯率嗎

**當時回答**

**可以，SAP B1 的 DTW 有透過 `BoBridge／oBridge` 匯入匯率的方式**，但你安裝的 DTW 版本是否提供對應物件與範本，需要確認。[SAP Community 匯入討論](https://community.sap.com/t5/enterprise-resource-planning-q-a/importing-the-exchange-rates-table-from-the-internet/qaq-p/3934188)

匯率資料主要包含：

| 日期 | 幣別 | 匯率 |
|---|---|---|
| 2026/10/05 | USD | 32.50 |
| 2026/10/05 | EUR | 35.80 |

這是資料示意，**不是可以直接上傳的 DTW 範本**。實際欄名、日期格式，以及匯率採直接或間接報價，要依範本與系統設定確認。

若 DTW 沒有對應項目，也可以用官方 DI API 的 `SBObob.SetCurrencyRate` 批次寫入。[SAP 官方文件](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~SBObob~SetCurrencyRate.html)

**你目前 SAP B1 是 9.3 還是 10.0？** 如果能貼 DTW 的物件選擇畫面，我可以幫你確認要選哪個。

---

<a id="q5"></a>

## S221-Q005｜10.0

日期：2026-10-05

**你的提問**

10.0

**當時回答**

了解，是 **SAP B1 10.0**。我先修正前面說法：**`BoBridge` 匯入匯率是舊版 SAP Community 的做法，目前查到的資料不足以保證你的 DTW 10.0 仍提供這個項目**，所以不能直接給你確定的操作路徑。

你可以先在 DTW 的 **Templates → Customize Template（自訂範本）** 查看是否有 `BoBridge／SBObob`，以及匯率相關項目；貼這個畫面，我就能幫你確認。

若沒有，10.0 官方明確支援用 **DI API `SBObob.SetCurrencyRate`** 批次寫入日期、幣別、匯率，也能指定是否更新既有匯率，但這需要小程式處理，不是直接上傳 DTW。[SAP 10.0 官方文件](https://help.sap.com/doc/089315d8d0f8475a9fc84fb919b501a3/10.0/en-US/SDKHelp/SAPbobsCOM~SBObob~SetCurrencyRate.html)

---

<a id="q6"></a>

## S221-Q006｜圖片／附件提問

日期：2026-10-05

**你的提問**

# Files mentioned by the user:

## codex-clipboard-e2937639-02bf-4902-b5b6-4d3136ab686f.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-e2937639-02bf-4902-b5b6-4d3136ab686f.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

有，你這個 DTW **確實有 `oBridge`**，就在畫面下方，`oRecordsetEx` 的下一列。

請選取 **`oBridge` → Next**，再貼下一個畫面，我幫你確認匯率的來源檔與欄位對應。

目前這張圖確認了物件存在，還需要看下一步，才能確定匯入格式。

---

<a id="q7"></a>

## S221-Q007｜但我找不到

日期：2026-10-05

**你的提問**

# Files mentioned by the user:

## codex-clipboard-d6a89a46-7659-48ef-95cb-2d5d7eae9ba4.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-d6a89a46-7659-48ef-95cb-2d5d7eae9ba4.png
Image attachment: true



## My request:
但我找不到

[原對話含圖片，本文未嵌入圖片]

**當時回答**

你找不到的是 **`oBridge` 的範本檔案**。這張圖的 `Others` 資料夾裡確實沒有；前一張看到的是 DTW 的「可選物件」，不代表一定附有同名範本。剛才我沒說清楚，抱歉。

先不用在資料夾繼續找，請回到 DTW：

1. 選取 **`oBridge`**。
2. 按 **Next**。
3. 貼出下一頁的「來源檔案選擇」畫面。

我們先確認它要求哪個資料結構，再準備匯率檔，避免拿錯範本。

---

<a id="q8"></a>

## S221-Q008｜圖片／附件提問

日期：2026-10-05

**你的提問**

# Files mentioned by the user:

## codex-clipboard-7dd53f61-74a9-4e8d-8cf5-203e24d7f7b6.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-7dd53f61-74a9-4e8d-8cf5-203e24d7f7b6.png
Image attachment: true



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這頁確認了：DTW 要讀取的是 **`BoBridge` 來源檔案**，目前格式選的是 **Tab 分隔的 `.txt`**。右邊的 `…` 是選擇檔案，不是產生範本。

畫面上也提示，要先從 **Customize Template** 產生範本。下一步：

1. 按 **Cancel** 離開這次匯入。
2. 上方選 **Templates → Customize Template**。
3. 找到 **`oBridge／BoBridge`**，查看是否能展開並產生範本。

**請貼 Customize Template 中選到這個物件的畫面**，就能確認實際欄位。這張圖還沒顯示欄位，因此先不要直接拿前面示意的三欄資料匯入。

---

<a id="q9"></a>

## S221-Q009｜甚麼意思

日期：2026-10-05

**你的提問**

甚麼意思

**當時回答**

意思是：**你已經選對 `BoBridge`，但還缺一份符合 DTW 格式的匯率檔案。** 這一頁只能選檔案，不能建立範本。

抱歉，前面讓你來回操作。現在先做這一步就好：

**按「Cancel」→ 上方「Templates」→「Customize Template」**。

這是要找出你這版 DTW 要求的欄位格式，才知道 Excel 該怎麼填。

---

