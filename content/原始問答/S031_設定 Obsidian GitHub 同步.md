# 設定 Obsidian GitHub 同步

來源編號：S031  
聊天 ID：`019fab88-1e99-70e2-b0fb-ae22f86f6531`  
聊天起始日期（台北）：2026-07-29  
保存位置：一般本機紀錄  
問答段落：1

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S031-Q001｜我想問 關於obsidian 怎麼跟 GitHub連動 然後我想要網站漂亮的一點

日期：2026-07-29

**你的提問**

我想問 關於obsidian 怎麼跟 GitHub連動 然後我想要網站漂亮的一點

**當時回答**

可以，而且我會建議把它設計成這條流程：

```text
Obsidian 寫筆記
      ↓
GitHub 備份與版本管理
      ↓
Quartz 轉成漂亮網站
      ↓
GitHub Pages 自動發布
```

Quartz 很適合 Obsidian，原生支援雙向連結、關聯圖、全文搜尋、反向連結、預覽視窗、LaTeX 與深色模式，比直接把 Markdown 丟到 GitHub Pages 好看很多。[Quartz 官方網站](https://quartz.jzhao.xyz/)

### 最容易入門的做法

1. 在 GitHub 建立一個 repository，例如 `my-notes`。
2. 用 GitHub Desktop 把 repository clone 到電腦。
3. 在 Obsidian 選擇「Open folder as vault」，開啟剛才的資料夾。
4. 寫完筆記後，用 GitHub Desktop Commit、Push。
5. 熟悉後再安裝 Obsidian Git 外掛，自動 Commit、Pull 和 Push。[Obsidian Git 專案](https://github.com/Vinzent03/obsidian-git)

建議加入 `.gitignore`：

```gitignore
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/cache
.trash/
.DS_Store
Thumbs.db
```

### 網站漂亮化方案

我最推薦「Quartz + GitHub Pages」：

- 保留 Obsidian 的 `[[雙向連結]]`
- 自動產生筆記關聯圖
- 支援搜尋、標籤、目錄與 backlinks
- 可以修改字型、主色、首頁和版面
- Push 到 GitHub 後自動更新網站
- GitHub Pages 可透過 Actions 建置各種靜態網站。[GitHub Pages 官方文件](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)

視覺上可以做成：

- 極簡知識庫風格
- 個人部落格風格
- 卡片式數位花園
- 深色科技風格
- 類似 Notion 的乾淨介面

### 很重要的隱私提醒

不要直接把整個私人 Vault 公開。比較安全的結構是：

```text
Private Vault
├── private/       不發布
├── journal/       不發布
└── publish/       只發布這裡
```

或者分成兩個 repository：

- `obsidian-private`：私人備份，設為 Private
- `my-digital-garden`：公開網站，只放可公開筆記

如果你希望我直接幫你建立，我可以在目前這個資料夾替你做好一套 Quartz 網站，包括首頁、顏色、中文字型、導覽列、GitHub Actions 和部署設定。你只需要告訴我你的 GitHub 帳號名稱，以及偏好的網站風格。

---

