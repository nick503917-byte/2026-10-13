# 尋找好看的 Obsidian 模組

來源編號：S114  
聊天 ID：`01a04247-e9a2-79c2-8b5c-38e23c14b306`  
聊天起始日期（台北）：2026-08-27  
保存位置：一般本機紀錄  
問答段落：16

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S114-Q001｜OBSIDIAN 有畫面的模組嗎 好看的

日期：2026-08-27

**你的提問**

OBSIDIAN　有畫面的模組嗎　好看的

**當時回答**

有，而且可以弄得很像漂亮的個人管理系統，不只是文字筆記。

我最推薦這幾個：

- **Dashboard**：中文介面的視覺化首頁，有待辦事項、筆記統計、年度活動格、最近筆記，甚至有 3D 星系知識圖。整體完成度很高，但目前算較新的外掛。[外掛介紹](https://community.obsidian.md/plugins/aurora-dashboard)
- **Startpage**：比較簡潔穩重的卡片式首頁，顯示釘選筆記、最近編輯、搜尋與資料庫統計。[外掛介紹](https://community.obsidian.md/plugins/start-page)
- **Extended Graph**：把關聯圖譜大幅美化，可以加入圖片節點、不同形狀、顏色、篩選和固定節點；只支援桌面版。[外掛介紹](https://community.obsidian.md/plugins/extended-graph)
- **Fancy Charts**：直接把 Markdown 表格變成漂亮的長條圖、圓餅圖、折線圖、熱力圖、桑基圖，操作也比較親民。[外掛介紹](https://community.obsidian.md/plugins/fancy-charts)
- **Charts View**：功能更完整，適合做工作進度、工時、費用、設備統計等儀表板，可接 CSV 或 Dataview。[外掛介紹](https://community.obsidian.md/plugins/obsidian-chartsview-plugin)
- **Charts for Bases**：如果你已經使用 Obsidian 的 Bases 資料庫功能，這個可以直接做互動式長條圖及折線圖。[外掛介紹](https://community.obsidian.md/plugins/charts)

外觀主題則推薦：

- **Border**：乾淨、有卡片感、可調整程度非常高，搭配 Style Settings 很漂亮。[預覽與設定](https://github.com/Akifyss/obsidian-border)
- **AnuPpuccin**：色彩比較豐富，有彩虹資料夾、Catppuccin 配色和多種漂亮的待辦圖示。[預覽與設定](https://github.com/AnubisNekhet/AnuPpuccin)

如果是你這個 **FMS／工作管理用途**，我的首選組合會是：

**Border 主題＋Dashboard 首頁＋Dataview＋Charts View＋Calendar**

這樣可以做成有：

- 今日待辦
- 工單狀態
- 專案進度
- 設備／客戶統計
- 日曆
- 最近異動
- 圖表儀表板

安裝位置是：`設定 → 第三方外掛 → 瀏覽`，搜尋上述英文名稱即可。主題則在 `設定 → 外觀 → 主題 → 管理`。

---

<a id="q2"></a>

## S114-Q002｜Dataview 這是甚麼

日期：2026-08-27

**你的提問**

## My request:
Dataview 這是甚麼

**當時回答**

**Dataview 是 Obsidian 的資料查詢外掛**。

簡單說，它能把散落在不同筆記裡的資料，自動整理成：

- 表格
- 清單
- 待辦事項
- 分組統計
- 儀表板資料

例如每張工單筆記都寫：

```yaml
---
客戶: 維熹
狀態: 進行中
負責人: 王先生
期限: 2026-08-30
---
```

Dataview 就能自動產生工單總表：

```dataview
TABLE 客戶, 狀態, 負責人, 期限
FROM "工單"
SORT 期限 ASC
```

結果會顯示成類似這樣：

| 工單 | 客戶 | 狀態 | 負責人 | 期限 |
|---|---|---|---|---|
| 空調維修 | 維熹 | 進行中 | 王先生 | 2026-08-30 |
| 消防檢查 | A公司 | 已完成 | 李先生 | 2026-09-02 |

它的重點是：**原始資料仍然是普通 Markdown 筆記，Dataview 只是即時查詢及呈現，不會把資料鎖在特殊資料庫裡。**

很適合你的 FMS 用途，例如做：

- 尚未完成的工單
- 本週到期項目
- 各客戶設備清單
- 各專案執行進度
- 依負責人整理的工作
- 保養與合約到期提醒

不過 Dataview 本身偏向「整理資料」，畫面不算華麗；通常會再搭配 **Border 主題、Dashboard 或圖表外掛**，做成好看的首頁儀表板。

---

<a id="q3"></a>

## S114-Q003｜Dataview 怎麼用

日期：2026-08-27

**你的提問**

## My request:
Dataview  怎麼用

**當時回答**

Dataview 的使用邏輯只有兩步：

1. 在每篇筆記填入「屬性」
2. 在總表筆記寫 Dataview 查詢

## 1. 安裝 Dataview

在 Obsidian：

`設定 → 第三方外掛 → 關閉安全模式／開啟社群外掛 → 瀏覽`

搜尋 **Dataview**，依序按：

`安裝 → 啟用`

## 2. 建立工單資料夾

建立一個資料夾：

```text
工單
```

在裡面建立幾篇筆記，例如：

```text
工單/
├─ 空調異常.md
├─ 消防設備檢查.md
└─ 電梯定期保養.md
```

## 3. 填寫工單屬性

打開「空調異常.md」，在筆記最上方加入：

```yaml
---
customer: 維熹
status: 進行中
assignee: 王先生
priority: 高
due: 2026-08-30
---
```

下面可以繼續寫一般內容：

```markdown
# 空調異常

三樓會議室冷氣無法啟動。

## 處理事項

- [x] 確認異常
- [ ] 聯絡廠商
- [ ] 完成維修
```

其他工單也使用相同欄位，只需修改內容。

> 新版 Obsidian 也可以透過筆記上方的「新增屬性」按鈕填寫，不一定要手動輸入 YAML。

## 4. 建立工單總表

新增一篇「工單總表.md」，貼入：

````markdown
```dataview
TABLE
  customer AS "客戶",
  status AS "狀態",
  assignee AS "負責人",
  priority AS "優先程度",
  due AS "期限"
FROM "工單"
SORT due ASC
```
````

切換成「閱讀模式」後，就會自動顯示工單表格。

## 常用查詢

只顯示尚未完成的工單：

````markdown
```dataview
TABLE customer AS "客戶", assignee AS "負責人", due AS "期限"
FROM "工單"
WHERE status != "已完成"
SORT due ASC
```
````

只顯示高優先工單：

````markdown
```dataview
TABLE customer AS "客戶", status AS "狀態", due AS "期限"
FROM "工單"
WHERE priority = "高"
SORT due ASC
```
````

顯示資料夾內所有未完成待辦：

````markdown
```dataview
TASK
FROM "工單"
WHERE !completed
```
````

顯示七天內到期的工單：

````markdown
```dataview
TABLE customer AS "客戶", status AS "狀態", due AS "期限"
FROM "工單"
WHERE due >= date(today)
  AND due <= date(today) + dur(7 days)
  AND status != "已完成"
SORT due ASC
```
````

## 指令意思

- `TABLE`：產生表格
- `FROM "工單"`：從工單資料夾抓資料
- `WHERE`：設定篩選條件
- `SORT due ASC`：期限由近到遠排列
- `AS "客戶"`：把欄位標題顯示成中文
- `TASK`：收集筆記中的核取方塊待辦

Dataview 官方也支援 `LIST`、`TABLE`、`TASK`、`CALENDAR` 四種主要查詢形式。[官方說明](https://github.com/blacksmithgu/obsidian-dataview/blob/master/docs/docs/index.md)

建議欄位名稱先使用英文，例如 `status`、`due`，顯示時再透過 `AS "狀態"` 改成中文，日後撰寫查詢會比較穩定。

---

<a id="q4"></a>

## S114-Q004｜可以再推薦幾個外掛 讓我畫面好看一點嗎

日期：2026-08-27

**你的提問**

## My request:
可以再推薦幾個外掛 讓我畫面好看一點嗎

**當時回答**

可以。你現在既然準備使用 **Border 主題**，我推薦以下幾個，效果比較明顯。

## 最推薦安裝

### 1. Style Settings

這個幾乎是 Border 的必裝搭檔，可以直接調整：

- 背景及強調色
- 卡片式版面
- 圓角、陰影及透明度
- 標題和文字顏色
- 側邊欄自動隱藏
- 新分頁背景
- Callout 樣式

安裝後到：

`設定 → Style Settings → Border`

Border 官方也特別建議搭配這個外掛。[外掛介紹](https://community.obsidian.md/plugins/obsidian-style-settings)

### 2. Iconic

可以替資料夾、檔案、標籤、書籤和頁籤加上圖示及顏色，例如：

```text
🏠 首頁
📋 工單
🏢 客戶
🛠️ 設備
📅 保養計畫
📊 報表
📦 附件
```

操作方式通常是對資料夾或檔案按右鍵，更換圖示。它提供大量 Lucide 圖示和 Emoji。[外掛介紹](https://community.obsidian.md/plugins/iconic)

> 不太建議現在新裝 Iconize，因為其外掛頁面已標示停止維護；改裝較新的 Iconic。

### 3. Homepage Blocks

可以做成真正的視覺化首頁，而且不用自己寫複雜程式：

- 問候語、日期和時鐘
- 常用功能按鈕
- 最近開啟的筆記
- 圖片區塊
- 快速搜尋
- 番茄鐘
- 引用與書籤
- 嵌入 Dataview 工單總表
- 卡片拖曳及調整大小

還能調整每張卡片的漸層、透明度、陰影和毛玻璃效果。[外掛介紹](https://community.obsidian.md/plugins/homepage-blocks)

### 4. Cards View

把筆記從普通檔案清單變成卡片牆，可以：

- 顯示筆記預覽
- 按標籤篩選
- 釘選重要筆記
- 按建立或修改時間排列

很適合瀏覽「客戶、設備、專案、知識文件」。[外掛介紹](https://community.obsidian.md/plugins/cards-view)

### 5. Banners

在每篇筆記上方放置橫幅圖片和 Emoji，例如客戶頁面放大樓照片、設備頁面放設備照片。[外掛介紹](https://community.obsidian.md/plugins/obsidian-banners)

如果希望在編輯模式和閱讀模式都能顯示，也可以選比較新的 [Banner Images](https://community.obsidian.md/plugins/banner-images)。

### 6. Explorer

把資料夾內容顯示成漂亮的卡片或清單，包含：

- 筆記預覽
- 搜尋及排序
- 釘選常用筆記
- 圖片及標籤
- 拖曳整理
- 資料夾首頁

它很適合把「客戶」或「專案」資料夾變成類似 Notion 的圖庫頁面。[外掛介紹](https://community.obsidian.md/plugins/explorer)

### 7. Gallery View

如果工單或設備會附很多現場照片，這個可以把附件顯示成：

- 縮圖網格
- 瀑布流
- 輪播
- 點擊放大

支援 Vault 內的圖片資料夾，也能調整響應式排版。[外掛介紹](https://community.obsidian.md/plugins/gallery-view)

### 8. Supercharged Links

根據筆記的 `status` 或標籤，自動替連結加上顏色、背景或 Emoji。

例如：

- 🔴 緊急
- 🟡 進行中
- 🟢 已完成
- 🔵 等待廠商

很適合跟剛才的 Dataview 工單屬性一起使用，但設定比其他外掛稍微進階。[外掛介紹](https://community.obsidian.md/plugins/supercharged-links-obsidian)

## 我建議你的安裝組合

先安裝這四個就好：

1. **Border Theme**
2. **Style Settings**
3. **Iconic**
4. **Homepage Blocks**
5. **Dataview**

如果有很多設備或施工照片，再加 **Gallery View**；想把客戶和專案顯示成卡片，再加 **Explorer**。

這樣會是乾淨的「FMS 工作台」風格，不需要一次裝十幾個外掛，避免畫面變花、速度也比較穩。

---

<a id="q5"></a>

## S114-Q005｜如果我的資料夾裡面想要分 美國維熹 越南維熹 然後裡面各有待辦 這樣我該怎麼做比較好

日期：2026-08-27

**你的提問**

## My request:
如果我的資料夾裡面想要分

美國維熹
越南維熹

然後裡面各有待辦 這樣我該怎麼做比較好

**當時回答**

建議採用「**地區資料夾＋專案筆記＋地區待辦總覽**」的方式。這樣結構清楚，也方便 Dataview 自動彙整。

## 建議資料夾結構

```text
維熹FMS/
├─ 00 首頁/
│  └─ 全球待辦.md
│
├─ 美國維熹/
│  ├─ 美國維熹首頁.md
│  ├─ 一般待辦.md
│  ├─ 客戶/
│  ├─ 設備/
│  └─ 專案/
│     ├─ A廠空調改善.md
│     └─ B廠消防檢查.md
│
├─ 越南維熹/
│  ├─ 越南維熹首頁.md
│  ├─ 一般待辦.md
│  ├─ 客戶/
│  ├─ 設備/
│  └─ 專案/
│     ├─ 河內廠設備保養.md
│     └─ 胡志明廠修繕.md
│
└─ 99 附件/
   ├─ 美國/
   └─ 越南/
```

如果目前資料還不多，可以先簡化：

```text
├─ 美國維熹/
│  ├─ 美國待辦.md
│  └─ 美國維熹首頁.md
└─ 越南維熹/
   ├─ 越南待辦.md
   └─ 越南維熹首頁.md
```

## 待辦事項怎麼寫

在「美國待辦.md」裡：

```markdown
# 美國維熹待辦

## 緊急

- [ ] 確認 A 廠空調報價 📅 2026-08-28
- [ ] 回覆消防設備問題 📅 2026-08-29

## 一般

- [ ] 整理設備清單 📅 2026-09-03
- [ ] 安排下週視訊會議
```

越南待辦也使用相同格式：

```markdown
# 越南維熹待辦

- [ ] 確認河內廠保養日期 📅 2026-08-30
- [ ] 翻譯設備操作說明
- [ ] 更新胡志明廠報價 📅 2026-09-05
```

日期格式 `📅 2026-08-30` 主要適合搭配 **Tasks 外掛**。如果只使用 Dataview，也可以在待辦後加入 Dataview 欄位：

```markdown
- [ ] 確認河內廠保養日期 [due:: 2026-08-30] [assignee:: Nick]
```

不過我推薦直接安裝 **Tasks 外掛**，管理截止日期、優先程度及循環任務會更方便。

## 美國維熹首頁

在「美國維熹首頁.md」加入：

````markdown
# 🇺🇸 美國維熹

## 尚未完成

```dataview
TASK
FROM "美國維熹"
WHERE !completed
```

## 最近修改的文件

```dataview
TABLE file.mtime AS "最後修改"
FROM "美國維熹"
SORT file.mtime DESC
LIMIT 10
```
````

這會收集整個「美國維熹」資料夾裡的待辦，不只限於「美國待辦.md」。因此你在任何美國專案筆記中寫：

```markdown
- [ ] 確認客戶需求
```

它也會自動出現在美國首頁。

## 越南維熹首頁

````markdown
# 🇻🇳 越南維熹

## 尚未完成

```dataview
TASK
FROM "越南維熹"
WHERE !completed
```

## 最近修改的文件

```dataview
TABLE file.mtime AS "最後修改"
FROM "越南維熹"
SORT file.mtime DESC
LIMIT 10
```
````

## 全球待辦首頁

最後建立「全球待辦.md」：

````markdown
# 🌏 維熹全球工作台

## 🇺🇸 美國待辦

```dataview
TASK
FROM "美國維熹"
WHERE !completed
```

## 🇻🇳 越南待辦

```dataview
TASK
FROM "越南維熹"
WHERE !completed
```
````

這樣首頁可以一次看到兩地待辦，同時保留清楚的分類。

## 最實用的原則

- 簡單、零散的工作放在各地的「一般待辦.md」
- 屬於某個專案的工作，直接寫在專案筆記裡
- 不需要複製到總表，Dataview 會自動收集
- 完成後只要勾選，所有總覽都會同步更新
- 複雜任務才獨立建立一篇筆記；小任務直接用核取方塊

另外可以用 **Iconic** 將資料夾設成 `🇺🇸 美國維熹`、`🇻🇳 越南維熹`，再用 **Folder Notes／Explorer** 讓點擊資料夾時直接開啟該地區首頁，操作起來會很像 Notion 的工作空間。

---

<a id="q6"></a>

## S114-Q006｜如果我只想要 我的主畫面來更新像這樣的待辦 可以嗎 我不需要其他的

日期：2026-08-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-2918bcf6-025c-4e89-97cb-79bda0aa5945.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-2918bcf6-025c-4e89-97cb-79bda0aa5945.png



## My request:


如果我只想要 我的主畫面來更新像這樣的待辦 可以嗎 我不需要其他的

[原對話含圖片，本文未嵌入圖片]

**當時回答**

可以，而且這樣最簡單。你截圖中的 Dashboard 待辦區塊，本來就能指定**單一 Markdown 檔案**作為待辦來源，不需要另外建立美國、越南或專案結構。

建立一篇：

```text
待辦維熹.md
```

內容只寫核取方塊：

```markdown
# 待辦事項

- [ ] 會計科目表要重新選科目
- [ ] 警示 HUB 倉
- [ ] 銷售訂單匯入
- [ ] 科目餘額明細表
- [ ] 大宗原物料市場均價表格整理
- [ ] BOM 表結構版本調整
- [ ] 異動原因卡控測試
```

接著進入：

`設定 → 第三方外掛 → Dashboard`

把 Todo／待辦來源檔案設定成：

```text
待辦維熹.md
```

之後運作方式就是：

- 在主畫面新增或修改待辦
- 點左邊圓圈即可完成
- 完成項目會從未完成清單消失
- 點右側箭頭可回到原始筆記位置
- 新增的待辦會自動出現在主畫面
- 不需要 Dataview
- 不需要美國、越南子資料夾
- 不需要另外建立總表

如果仍想簡單區分美國和越南，也只要在同一檔案加文字即可：

```markdown
# 待辦事項

## 🇺🇸 美國維熹

- [ ] 確認美國銷售訂單
- [ ] 整理美國科目餘額

## 🇻🇳 越南維熹

- [ ] 確認越南庫存
- [ ] 更新越南 BOM 表
```

不過這個 Dashboard 的 Todo 卡片通常會把所有未完成項目依序列出，**不一定會顯示 Markdown 標題分類**。若想在主畫面也明確看到分類，可以直接寫在待辦名稱前面：

```markdown
- [ ] 🇺🇸 美國｜確認銷售訂單
- [ ] 🇺🇸 美國｜整理科目餘額
- [ ] 🇻🇳 越南｜確認庫存
- [ ] 🇻🇳 越南｜更新 BOM 表
```

對你現在的需求，我最推薦最後這種：**一個待辦檔案＋國旗前綴＋Dashboard 主畫面**。簡單、漂亮，也最好維護。

---

<a id="q7"></a>

## S114-Q007｜還有甚麼推薦的外掛嗎

日期：2026-08-27

**你的提問**

還有甚麼推薦的外掛嗎

**當時回答**

以你現在「**只用主畫面管理一份待辦**」的需求，我真正推薦的只有以下幾個，不必裝太多。

## 1. QuickAdd——最值得裝

用途：不必打開「待辦維熹.md」，按一個快捷鍵就能新增待辦。

設定完成後：

1. 按快捷鍵，例如 `Ctrl + Shift + A`
2. 輸入「確認美國訂單」
3. 自動在 `待辦維熹.md` 加入：

```markdown
- [ ] 確認美國訂單
```

新增後，Dashboard 主畫面便會顯示它。

QuickAdd 的 Capture 功能就是專門將內容快速加入指定檔案。[外掛介紹](https://community.obsidian.md/plugins/quickadd)

## 2. Tasks——需要日期時再裝

讓普通待辦支援：

- 到期日
- 優先程度
- 重複工作
- 完成日期
- 過期篩選

例如：

```markdown
- [ ] 🇺🇸 美國｜確認銷售訂單 📅 2026-08-30
- [ ] 🇻🇳 越南｜每週確認庫存 🔁 every week 📅 2026-08-31
- [ ] 🔺 緊急｜處理 HUB 倉警示
```

Tasks 提供視窗選擇日期，不需要自己背 Emoji 語法。[外掛介紹](https://community.obsidian.md/plugins/obsidian-tasks-plugin)

不過，如果你完全不需要日期與循環工作，可以先不裝。

## 3. Commander——做一顆「新增待辦」按鈕

可以把 QuickAdd 的「新增待辦」命令放到：

- 左側工具列
- 筆記標題列
- 狀態列
- 右鍵選單

這樣按一下 `＋` 就能輸入新待辦，不必記快捷鍵。

## 4. Buttons——在首頁放漂亮按鈕

可以在首頁製作：

- `＋新增待辦`
- `📋開啟待辦原始檔`
- `✅查看已完成`

並且能配合 Style Settings 調整按鈕外觀。[外掛介紹](https://community.obsidian.md/plugins/buttons)

不過你的 Dashboard 已經有圖形介面，所以 Buttons 屬於選配。

## 5. Iconic——純粹美化

替待辦檔案及首頁加圖示：

```text
🏠 Dashboard
✅ 待辦維熹
📦 附件
```

也能調整頁籤和側邊工具列圖示顏色。[外掛介紹](https://community.obsidian.md/plugins/iconic)

## 最適合你的精簡組合

建議只使用：

- **Dashboard**：顯示主畫面
- **QuickAdd**：快速新增待辦
- **Border＋Style Settings**：美化畫面
- **Iconic**：美化圖示
- **Tasks**：只有需要日期、優先程度、循環時才加

其中 **QuickAdd 最值得優先安裝**。你的操作最後可以簡化成：

```text
按快捷鍵 → 輸入待辦 → Enter → 主畫面自動出現
```

Dataview、Cards View、Explorer、Calendar 目前都不是必要的。

---

<a id="q8"></a>

## S114-Q008｜可以翻譯成中文嗎 QuickAdd

日期：2026-08-27

**你的提問**

## My request:
可以翻譯成中文嗎 QuickAdd

**當時回答**

可以。**QuickAdd** 中文可以理解成「**快速新增**」。

常見功能中文對照：

- **Choice**：操作項目
- **Capture**：快速寫入指定筆記
- **Template**：使用範本建立筆記
- **Macro**：巨集／連續執行多個動作
- **Multi**：操作選單群組
- **Manage Choices**：管理操作項目
- **Add Choice**：新增操作
- **Choice Name**：操作名稱
- **Capture To**：寫入至
- **Capture format**：寫入格式
- **Insert after**：插入在指定內容後方
- **Append link**：附加目前筆記連結
- **Open**：完成後開啟檔案

你的用途可以這樣設定：

1. 安裝並啟用 **QuickAdd**。
2. 進入 `設定 → QuickAdd`。
3. 在輸入框填入：

```text
新增待辦
```

4. 右側類型選擇 **Capture**。
5. 按 **Add Choice**。
6. 點「新增待辦」旁邊的齒輪。
7. **Capture To** 選擇：

```text
待辦維熹.md
```

8. **Capture format** 填入：

```markdown
- [ ] {{VALUE}}
```

9. 如果想讓新待辦每次都換行，啟用：

```text
Create line if not found
```

或與換行、附加至檔案底部相關的選項。

設定完成後，在命令面板執行：

```text
QuickAdd: 新增待辦
```

輸入內容，例如：

```text
🇺🇸 美國｜確認銷售訂單
```

QuickAdd 就會在 `待辦維熹.md` 自動加入：

```markdown
- [ ] 🇺🇸 美國｜確認銷售訂單
```

最後到：

`設定 → 快捷鍵`

搜尋：

```text
QuickAdd: 新增待辦
```

可以設定成 `Ctrl + Shift + A`。之後只要按快捷鍵、輸入內容、按 Enter，Dashboard 主畫面就會更新。

QuickAdd 外掛介面本身目前可能仍以英文為主，但你可以把自己建立的 Choice、按鈕及命令名稱全部命名成中文。

---

<a id="q9"></a>

## S114-Q009｜圖片／附件提問

日期：2026-08-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-61a19500-a37e-40a8-a668-1e4ef6d4bdbd.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-61a19500-a37e-40a8-a668-1e4ef6d4bdbd.png





## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

你這個畫面是「快捷鍵設定」，目前還看不到「新增待辦」，原因是還沒有把它註冊成獨立命令。

請照以下順序操作：

1. 先回到：

```text
設定 → QuickAdd
```

2. 在 **Choices／選項** 上方的輸入框輸入：

```text
新增待辦
```

3. 類型選擇：

```text
Capture
```

4. 按：

```text
Add Choice
```

5. 建立後，點「新增待辦」右邊的齒輪 ⚙，設定：

```text
Capture To：待辦維熹.md
```

開啟 **Capture format**，內容填入：

```markdown
- [ ] {{VALUE}}
```

6. 回到 QuickAdd 的 Choices 清單，找到「新增待辦」，點旁邊的 **閃電 ⚡ 圖示**。

這個閃電的意思是「加入 Obsidian 命令面板」。點亮後，才會成為獨立命令。

7. 再回到你截圖的：

```text
設定 → 快捷鍵
```

搜尋：

```text
新增待辦
```

現在應該會看到：

```text
QuickAdd: 新增待辦
```

點右邊 `＋`，設定快捷鍵，例如：

```text
Ctrl + Shift + A
```

如果暫時不設定獨立快捷鍵，也可以直接執行你畫面中的：

```text
QuickAdd: Run
```

然後從清單選擇「新增待辦」。但點亮 ⚡ 後，就能一步直接開啟新增待辦輸入框，使用上比較方便。[QuickAdd Capture 官方說明](https://quickadd.obsidian.guide/docs/Choices/CaptureChoice/)

---

<a id="q10"></a>

## S114-Q010｜圖片／附件提問

日期：2026-08-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-96a6a7ae-a16c-4945-9598-5c31552981bc.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-96a6a7ae-a16c-4945-9598-5c31552981bc.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，就是這個畫面。請選第二項：

```text
Capture — Add text to a note
```

中文意思是：

```text
快速寫入——將文字附加、插入或加到指定筆記前面
```

接下來：

1. 點選 **Capture**。
2. 名稱輸入：

```text
新增待辦
```

3. 建立後點「新增待辦」右側的齒輪 ⚙。
4. 在 **Capture To** 選擇：

```text
待辦維熹.md
```

5. 開啟 **Capture format**。
6. 格式輸入：

```markdown
- [ ] {{VALUE}}
```

7. 儲存或關閉設定。
8. 回到 Choices 清單，點「新增待辦」旁邊的 ⚡ 閃電。
9. 到 `設定 → 快捷鍵` 搜尋：

```text
QuickAdd: 新增待辦
```

另外兩個選項目前不需要：

- **Template**：用範本建立一整篇新筆記
- **Macro**：連續執行多個指令

你的需求只要選 **Capture** 就對了。

---

<a id="q11"></a>

## S114-Q011｜圖片／附件提問

日期：2026-08-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-ce02d3bb-7c93-4457-922e-8cf22ae01a7c.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-ce02d3bb-7c93-4457-922e-8cf22ae01a7c.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

你現在進入了 **Insert After（插入在指定文字後方）**的進階設定，但你的待辦不需要這些。

目前畫面中的選項都維持關閉即可：

- **Insert at end of section**：關閉
- **Consider subsections**：關閉
- **Create line if not found**：關閉
- **Blank lines after match**：維持預設

請往上捲，找到 **Position／插入位置**，改成：

```text
End of file
```

或：

```text
Append
```

中文意思是「附加到檔案最下方」。

完整設定應該是：

```text
Capture To：待辦維熹.md
Position：End of file／Append
Capture format：- [ ] {{VALUE}}
```

然後按右下角：

```text
Done
```

QuickAdd 會自動儲存，不需要另外找 Save。

完成後測試：

1. 按 `Ctrl + P`
2. 執行 `QuickAdd: Run`
3. 選擇「新增待辦」
4. 輸入：

```text
測試新增待辦
```

確認 `待辦維熹.md` 最下方是否出現：

```markdown
- [ ] 測試新增待辦
```

如果出現，就代表設定成功。之後再點「新增待辦」旁邊的 ⚡，便能設定專用快捷鍵。

---

<a id="q12"></a>

## S114-Q012｜這樣嗎

日期：2026-08-27

**你的提問**

這樣嗎

**當時回答**

對，只要最後設定符合這三項就正確：

```text
Capture To：待辦維熹.md
Position：End of file／Append
Capture format：- [ ] {{VALUE}}
```

你剛才截圖中的進階選項全部保持關閉即可，然後按右下角 **Done**。

接著用 `Ctrl + P → QuickAdd: Run → 新增待辦` 測試。若 `待辦維熹.md` 最下面自動出現：

```markdown
- [ ] 你輸入的內容
```

就設定成功了。

---

<a id="q13"></a>

## S114-Q013｜圖片／附件提問

日期：2026-08-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-aa543981-8a7d-4751-8271-41053a1bc4d7.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-aa543981-8a7d-4751-8271-41053a1bc4d7.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

對，這個位置設定正確：

```text
美國維熹/待辦維熹.md
```

畫面中的 **Preview** 也顯示相同路徑，代表 QuickAdd 能正確辨識。

注意：

- 如果這篇筆記已經存在，**Create file if it doesn't exist** 保持關閉即可。
- 如果尚未建立，請將它開啟，QuickAdd 才會自動建立檔案。

接著在這個視窗繼續往下捲，設定：

```text
Position：End of file／Bottom／Append
Capture format：- [ ] {{VALUE}}
```

最後按 **Done**。測試時新增的內容就會寫到：

```text
美國維熹/待辦維熹.md
```

並顯示成：

```markdown
- [ ] 測試待辦
```

---

<a id="q14"></a>

## S114-Q014｜這樣?

日期：2026-08-27

**你的提問**

這樣?

**當時回答**

對，這一頁設定正確。

目前確認：

```text
目標檔案：美國維熹/待辦維熹.md
Capture to active file：關閉
```

如果 `待辦維熹.md` 已經存在，**Create file if it doesn't exist** 保持關閉即可。

再往下確認：

```text
Capture format：- [ ] {{VALUE}}
位置：End of file／Append
```

然後按 **Done**，就可以測試新增待辦了。

---

<a id="q15"></a>

## S114-Q015｜圖片／附件提問

日期：2026-08-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-68ff3bf5-b7d0-440e-a5b5-c3f167706242.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-68ff3bf5-b7d0-440e-a5b5-c3f167706242.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這裡要修改一下。

請把：

```text
Choose heading when capturing
```

切成**關閉（灰色）**。

它的意思是「每次新增待辦時，都先詢問要放在哪一個標題下面」。你只想快速加入同一份待辦，不需要多這一步。

下面的：

```text
Insert at end of section
```

也保持關閉。

你需要的效果是：

```text
輸入待辦 → 直接加入檔案最下方
```

所以設定應為：

- `Choose heading when capturing`：關閉
- `Insert at end of section`：關閉
- `Position`：End of file／Append
- `Capture format`：`- [ ] {{VALUE}}`

---

<a id="q16"></a>

## S114-Q016｜圖片／附件提問

日期：2026-08-27

**你的提問**

# Files mentioned by the user:

## codex-clipboard-3ce76b22-b873-42ed-8968-23e8d78a76ba.png: C:/Users/nick_yeh/AppData/Local/Temp/codex-clipboard-3ce76b22-b873-42ed-8968-23e8d78a76ba.png



## My request:


[原對話含圖片，本文未嵌入圖片]

**當時回答**

這個錯誤表示目前仍然選到：

```text
Insert after（插入在指定文字後面）
```

但「指定文字」是空白，所以 QuickAdd 中止執行。

請回到：

```text
設定 → QuickAdd → 新增待辦 → ⚙
```

找到 **Position／Placement** 區域，把放置方式從：

```text
Insert after
```

改成：

```text
Bottom of file
```

或介面可能顯示：

```text
End of file
Append
```

三者都是「放在檔案最下方」的意思。

同時確認這些選項：

```text
Choose heading when capturing：關閉
Insert after：不要選
Capture format：- [ ] {{VALUE}}
```

儲存後重新執行：

```text
QuickAdd: 新增待辦
```

就不會再出現 `Insert-after target is empty`。重點是：**不要停留在 Insert after 模式，必須改成 Bottom／End of file。**

---

