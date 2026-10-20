# Create personalized hatch pet

來源編號：S145  
聊天 ID：`01a0843c-0adb-7821-bbd8-744c848a02dd`  
聊天起始日期（台北）：2026-09-09  
保存位置：一般本機紀錄  
問答段落：11

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S145-Q001｜[$hatch-pet](C:\Users\nick_yeh\\.codex\skills\hatch-pet\SKILL.md) create a pet based on wh…

日期：2026-09-09

**你的提問**

[$hatch-pet](C:\Users\nick_yeh\\.codex\skills\hatch-pet\SKILL.md) create a pet based on what you know about me

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q2"></a>

## S145-Q002｜SAPB1 項目群組的會計科目設定，甚麼時候會用到 外國的設定 可以舉例情境給我嗎

日期：2026-09-11

**你的提問**

SAPB1 項目群組的會計科目設定，甚麼時候會用到 外國的設定 可以舉例情境給我嗎

**當時回答**

可以。你說的應該是「項目群組 → 會計」裡的 **「收入科目－外國」或「費用科目－外國」**。先用最常見的「收入科目－外國」舉例。

**它是讓系統把國外銷售入到指定科目；不是只要用美元交易，就會用到「外國」科目。** 標準收入判定會看單據「物流」頁籤的 **送貨地址（Ship-to）國家／地區**，以及資料庫的本地化版本。SAP Note 875150 的規則說明可見於 [SAP 社群中的原文轉載](https://community.sap.com/t5/enterprise-resource-planning-q-a/ship-to-country-affect-the-g-l-posting/qaq-p/12425334)。

假設你的公司使用**台灣本地化版本**，品項的「設定總帳科目依據」選擇「項目群組」，而「電子零件」群組設定如下：

| 設定欄位 | 指定科目（示例） |
|---|---|
| 收入科目 | 410100－內銷收入 |
| 收入科目－外國 | 410200－外銷收入 |

在沒有其他規則或人工改科目的情況下，可這樣理解：

| 交易情境 | 預期使用的收入科目 | 判斷原因 |
|---|---|---|
| 賣給台灣客戶，送貨台北，以台幣計價 | 410100－內銷收入 | 送貨國家為台灣 |
| 賣給台灣客戶，送貨台北，**以美元計價** | 410100－內銷收入 | 改用美元不會因此變成國外銷售 |
| 賣給日本客戶，送貨日本，以日圓計價 | 410200－外銷收入 | 送貨國家為日本 |
| 賣給日本客戶，送貨日本，**以台幣計價** | 410200－外銷收入 | 使用台幣仍可能屬國外銷售 |
| 台灣客戶下單，但指定送到日本工廠 | 410200－外銷收入 | 單據選用的送貨國家為日本 |

例如第三個情境，建立 **A/R 發票**，銷售金額折合台幣 30,000 元，簡化分錄、不列稅額及成本部分：

```text
借：應收帳款             30,000
    貸：410200 外銷收入          30,000
```

這樣設定的用途，就是讓同一個品項群組的**內銷與外銷收入自動分開入帳**，方便報表分析。

實際檢查時，留意三件事：

1. **品項是否真的依「項目群組」取科目？** 如果依倉庫或品項層級，就要看相應位置的設定。
2. **看這張單據的送貨國家**，不要只看客戶名稱、帳單地址或幣別。
3. **是否啟用進階總帳科目判定？** 進階規則也能依品項群組、倉庫、業務夥伴群組、送貨國家等條件指定科目，需一起檢查。[SAP 官方說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/4507edb6ae742461e10000000a1553f7.html)

你目前看的是「**收入科目－外國**」還是「**費用科目－外國**」？如果是費用科目，還要區分採購的是庫存品或非庫存品，不能直接套用上面的銷售分錄。

---

<a id="q3"></a>

## S145-Q003｜翻譯成中文

日期：2026-09-11

**你的提問**

翻譯成中文

**當時回答**

你想翻譯哪一段內容？上一則 SAP B1 說明已是中文；如果你指的是前面的英文寵物製作進度，或畫面上的英文欄位，請貼上原文或截圖，我會翻成繁體中文。

---

<a id="q4"></a>

## S145-Q004｜我系統設定是 本國幣別越南盾 系統幣別台幣

日期：2026-09-11

**你的提問**

我系統設定是 本國幣別越南盾 系統幣別台幣

**當時回答**

〔此份紀錄未保存回答，或該輪在回答前中斷。〕

---

<a id="q5"></a>

## S145-Q005｜我的交貨單客戶是美金 商品也是美金

日期：2026-09-11

**你的提問**

我的交貨單客戶是美金 商品也是美金

**當時回答**

依你目前提供的設定：

| 設定／單據 | 幣別 |
|---|---|
| 本國幣別 | 越南盾 VND |
| 系統幣別 | 台幣 TWD |
| 客戶、交貨單商品價格 | 美元 USD |

**美元在你的系統裡是外幣，但不代表會自動使用「收入科目－外國」。** 幣別主要影響金額換算；「收入科目－外國」的標準判定，要看**單據送貨地址的國家，以及資料庫的本地化版本**。[SAP 科目判定規則](https://community.sap.com/t5/enterprise-resource-planning-q-a/ship-to-country-affect-the-g-l-posting/qaq-p/12425334)

例如，**假設資料庫的本地化國家也是越南**：

- 送貨到越南，即使用美元計價，通常仍取一般「收入科目」。
- 送貨到美國，才可能取「收入科目－外國」。

另外，你目前說的是**交貨單**：一般庫存品在永續盤存下，交貨單主要產生「借：銷貨成本／貸：存貨」；收入科目通常到 **A/R 發票**才會用到，所以要連發票一起確認。

**你這張交貨單「物流 → 送貨地址」的國家填什麼？** 也請確認資料庫的本地化國家；只看本國幣別是越南盾，還不能確定它是越南本地化。

---

<a id="q6"></a>

## S145-Q006｜CHINA

日期：2026-09-11

**你的提問**

CHINA

**當時回答**

那你這筆是：**美元計價，送貨國家為 CHINA（中國）**。

**如果資料庫的本地化國家是越南，這筆銷售通常會取「收入科目－外國」**，因為送貨國家是中國；判斷重點是送貨國家，不是美元計價。[SAP 判定規則](https://community.sap.com/t5/enterprise-resource-planning-q-a/ship-to-country-affect-the-g-l-posting/qaq-p/12425334)

假設品項依「項目群組」取科目：

| 單據 | 一般庫存品、永續盤存下的分錄 |
|---|---|
| 交貨單 | 借：銷貨成本；貸：存貨 |
| 後續 A/R 發票 | 借：應收帳款；貸：項目群組設定的「收入科目－外國」 |

但**本國幣別設越南盾，不等於資料庫本地化一定是越南**。若本地化是中國，送貨 CHINA 通常會取一般收入科目；若有進階科目判定，也要一起確認。

你資料庫的「本地化／國家版本」是越南，還是其他國家？

---

<a id="q7"></a>

## S145-Q007｜科目代碼 科目名稱 費用科目\ 收入科目 4111 Doanh thu xuất khẩu – Sản phẩm/外銷收入-產品 存貨科目 1172 Thành phẩm/製成品 …

日期：2026-09-11

**你的提問**

# 科目代碼 科目名稱

費用科目\
收入科目 4111 Doanh thu xuất khẩu – Sản phẩm/外銷收入-產品
存貨科目 1172 Thành phẩm/製成品
銷貨成本 5111 Giá vốn hàng bán xuất khẩu – Sản phẩm/外銷成本-產品
分配科目 1550 Khoản tạm ứng/暫付款
差異科目\
價格差異科目 5224 Chênh lệch giá thành phẩm/製成品價格差異
負存貨調整科目\
存貨餘額 - 減少科目 5116 Giá vốn hàng bán xuất khẩu – Thừa/thiếu kiểm kê hàng tồn kho/外銷成本-存貨盤盈虧
存貨餘額 - 增加科目 5116 Giá vốn hàng bán xuất khẩu – Thừa/thiếu kiểm kê hàng tồn kho/外銷成本-存貨盤盈虧
銷售退貨科目 4211 Hàng bán bị trả lại – Sản phẩm/銷貨退回-產品
收入科目 - 外國 4111 Doanh thu xuất khẩu – Sản phẩm/外銷收入-產品
費用科目 - 外國\
匯率差異科目 6326 Phụ cấp khác/其他津貼
商品結清科目\
總帳減少科目\
總帳增加科目\
在製品存貨科目 1174 Sản phẩm dở dang/在製品
在製品存貨差異科目 1174 Sản phẩm dở dang/在製品
在製品沖銷損益表科目 1174 Sản phẩm dở dang/在製品
存貨餘額損益表科目\
費用結清科目\
在途庫存科目\
已出貨科目\
銷售貸項科目 4311 Giảm giá hàng bán – Sản phẩm/銷貨折讓-產品
採購貸項科目 5114 Giá vốn hàng bán xuất khẩu – Hàng tồn kho chậm luân chuyển/外銷成本-存貨呆滯
銷售貸項科目 - 外國\
採購貸項科目 - 外國





幫我找 用途

**當時回答**

可以。**你貼的是「系統在不同交易情境下，要使用哪個會計科目」的對照設定。科目名稱是你們自行命名的，真正的觸發用途由左邊的 SAP 欄位決定。**

先指出跟你前面問題直接相關的一點：**「收入科目」和「收入科目－外國」都設為 4111，所以即使系統判定路徑不同，最後仍會入同一個 4111。**

以下以**永續盤存、品項依「項目群組」取科目，且沒有進階規則覆寫**為前提；「空白」表示你貼出的欄位未指定。

**銷售、採購相關科目**

| 設定欄位 | 目前科目 | 用途／常見情境 |
|---|---|---|
| 費用科目 | 空白 | 非庫存品採購費用，例如用非庫存品項建立 A/P 發票。一般庫存製成品採購通常先入存貨。 |
| 收入科目 | 4111 外銷收入－產品 | A/R 發票的銷售收入。即使科目名稱叫「外銷收入」，一般收入判定仍會入這個科目。 |
| 收入科目－外國 | 4111 外銷收入－產品 | 收入判定屬外國交易時使用的收入科目。你目前與一般收入設為相同科目。 |
| 費用科目－外國 | 空白 | 外國採購情境中，適用費用入帳時的對應科目，例如非庫存品採購；不是所有進口存貨都直接入此科目。 |
| 銷售貸項科目 | 4311 銷貨折讓－產品 | A/R 貸項通知單的收入沖減端。用途涵蓋貸項交易，不一定只有「折讓」。 |
| 銷售貸項科目－外國 | 空白 | 外國銷售貸項交易的收入沖減科目，應測試你們的外銷退貨／折讓流程是否需要它。 |
| 採購貸項科目 | 5114 外銷成本－存貨呆滯 | 採購貸項的費用沖回端，常見於非庫存品的 A/P 貸項通知單。**它不是自動提列存貨呆滯的欄位。** |
| 採購貸項科目－外國 | 空白 | 外國採購貸項情境的對應科目；實際是否使用仍取決於品項及單據流程。 |

非庫存採購的費用處理可參考 [SAP 採購服務教材](https://help.sap.com/doc/download_multimedia_ebooks_businessone90_tb1000_02_04_story_html/9.0/en-US/story_html5.html)；採購貸項用途另有 [SAP Business One 論壇團隊說明](https://community.sap.com/t5/enterprise-resource-planning-q-a/account-types/qaq-p/3910619)。

**存貨、成本及差異科目**

| 設定欄位 | 目前科目 | 用途／常見情境 |
|---|---|---|
| 存貨科目 | 1172 製成品 | 記錄製成品庫存價值，例如入庫增加、交貨減少。 |
| 銷貨成本 | 5111 外銷成本－產品 | 交貨時認列商品成本；依交貨單開立發票，通常不再重複認列這筆成本。 |
| 分配科目 | 1550 暫付款 | **採購收貨與應付發票之間的過渡科目**。採購收貨時先掛帳，後續 A/P 發票再沖銷；不是付款預付金功能。 |
| 差異科目 | 空白 | 標準成本法下，標準成本與採購／存貨交易價格不同時，承接差異。 |
| 價格差異科目 | 5224 製成品價格差異 | 採購價格調整中，無法全部回補到現有庫存的差額，例如收貨後商品已賣出，再收到較高價格的發票。 |
| 負存貨調整科目 | 空白 | 移動平均或 FIFO 在負庫存區間產生的成本差額；不是「只要庫存負數就固定入一筆」。 |
| 存貨餘額－減少科目 | 5116 存貨盤盈虧 | 存貨發貨等庫存減少交易的平衡科目，例如非銷售用途領出；並非只限盤虧。 |
| 存貨餘額－增加科目 | 5116 存貨盤盈虧 | 存貨收貨等庫存增加交易的平衡科目，例如非採購入庫；並非只限盤盈。 |
| 銷售退貨科目 | 4211 銷貨退回－產品 | **客戶退貨入庫時，承接退回商品的庫存成本價值**，與收入沖減的「銷售貸項科目」不同。 |
| 匯率差異科目 | 6326 其他津貼 | 特定外幣採購流程中，來源與後續單據匯率不同所產生的本幣差額；不是所有收付款匯差都走這裡。 |
| 商品結清科目 | 空白 | 關閉未完成的採購收貨／採購退貨時，用來結清相關過渡帳；該關閉動作不改變實物庫存。 |
| 總帳減少科目 | 空白 | 存貨重估降低庫存價值時的平衡科目。 |
| 總帳增加科目 | 空白 | 存貨重估提高庫存價值時的平衡科目。 |

上述存貨欄位用途依據 [SAP 庫存科目判定說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/4506b8ab7d720487e10000000a155369.html)及 [永續盤存操作指南](https://help.sap.com/doc/b23e727e45434829a926e6ab06d4b69e/10.0/en-US/How_to_Set_Up_and_Manage_a_Perpetual_Inventory_System.pdf)。其中退貨科目的「庫存端」用途，SAP 也在 [退貨處理教材](https://help.sap.com/doc/saphelpiis_hc_b1_image_repository_consultant_training_basic_b1_90_tb1000_03_06_pdf/9.0/en-US/B1_90_TB1000_03_06.pdf)中明確區分。

**生產及特殊流程科目**

| 設定欄位 | 目前科目 | 用途／常見情境 |
|---|---|---|
| 在製品存貨科目 | 1174 在製品 | 生產過程中的成本暫存，例如生產發料轉入在製品，完工收貨再轉出。 |
| 在製品存貨差異科目 | 1174 在製品 | 生產訂單結案時承接投入與產出成本差異；部分差異是否轉回成品，取決於計價法及剩餘庫存。 |
| 在製品沖銷損益表科目 | 1174 在製品 | 生產採用「資產負債表科目＋損益表科目」模式時，在製品變動的損益對應科目。 |
| 存貨餘額損益表科目 | 空白 | 同一生產模式下，存貨變動的損益對應科目。 |
| 費用結清科目 | 空白 | 採購收貨與 A/P 發票間，運費等附加費用的過渡結清。 |
| 在途庫存科目 | 空白 | A/P 預留發票流程使用的過渡科目，替代該流程中的分配科目；不是看到「貨在路上」就一律用它。 |
| 已出貨科目 | 空白 | 啟用相關處理時，暫存已交貨、尚未認列銷貨成本的商品價值；常用於交貨與發票跨期的流程。 |

生產差異參考 [SAP 生產成本說明](https://help.sap.com/docs/PRODUCT_ID/68a2e87fb29941b5bf959a184d9c6727/689975f9caea438ba98206fce1573bcb.html)；生產損益對應科目的啟用条件見 [SAP 品項存貨資料說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/68a2e87fb29941b5bf959a184d9c6727/451fa75c8ffb4a2fe10000000a11466f.html)。

**你這份設定，我會優先核對以下幾項**

這些是依「欄位用途與你提供的科目名稱」發現的疑點，尚未看過你們的科目分類與實際分錄，因此不直接判定設定錯誤。

| 優先核對項目 | 原因 |
|---|---|
| **銷售退貨 → 4211 銷貨退回** | 此欄位處理的是退回商品的庫存成本。如果 4211 是收入減項，可能把庫存價值入到收入減項。這是最需要先確認的一項。 |
| **匯率差異 → 6326 其他津貼** | 若 6326 確實是人事津貼費用，採購匯差入此科目會混入津貼。 |
| **分配科目 → 1550 暫付款** | 它承接的是收貨未開票過渡金額。若同時拿來記實際預付款，對帳容易混在一起。 |
| **在製品差異、在製品損益沖銷 → 都用 1174** | 在製品資產、結案差異、損益對應各有不同用途，不宜只因名稱相近就設同一科目。 |
| **採購貸項 → 5114 存貨呆滯** | 若觸發採購貸項費用沖回，會混入存貨呆滯成本，需確認這是否符合你們的會計設計。 |

套回你目前的「美元銷售、送貨 CHINA」：在一般製成品交貨流程下，預期先看到 **借 5111 銷貨成本／貸 1172 製成品**；後續 A/R 發票的收入端，依你貼的設定，**一般收入或外國收入最後都會到 4111**。空白欄位則不代表一定錯，要看你們是否會使用對應流程。

---

<a id="q8"></a>

## S145-Q008｜銷貨退回我應該是要跟存貨科目依樣嗎

日期：2026-09-11

**你的提問**

銷貨退回我應該是要跟存貨科目依樣嗎

**當時回答**

**對，如果你們不需要把「退回商品庫存」另外分開管理，這個「銷售退貨科目」通常可以設成與存貨科目相同的 `1172 製成品`。** SAP 官方教材也明確說明可以這樣設定。[SAP 退貨處理教材](https://help.sap.com/doc/saphelpiis_hc_b1_image_repository_consultant_training_basic_b1_90_tb1000_03_06_pdf/9.0/en-US/B1_90_TB1000_03_06.pdf)

要分清楚這兩個欄位：

| SAP 設定欄位 | 處理什麼 | 你們可考慮的設定 |
|---|---|---|
| **銷售退貨科目** | 商品退回入庫的**成本價值** | **1172 製成品**；或另設退回商品存貨科目 |
| **銷售貸項科目** | 退貨／折讓造成的**收入沖減** | 4211 銷貨退回或 4311 銷貨折讓，依你們的會計分類與交易情境決定 |

例如原本售價 100、成本 60，客戶全部退回，簡化分錄如下：

```text
商品退回入庫：
借：1172 製成品         60
    貸：5111 銷貨成本       60

開立 A/R 貸項通知單沖減貨款：
借：銷貨退回／折讓      100
    貸：應收帳款           100
```

因此，**你目前把「銷售退貨科目」設為 `4211 銷貨退回－產品`，如果 4211 是收入減項，就應調整為適當的存貨科目。** 修改設定不會自動修正已過帳分錄；之前做過的退貨也要另外檢查。

---

<a id="q9"></a>

## S145-Q009｜可以協助我釐清 如果我的工廠剛開始，但我已經購入相關設備以及人工。 以及我使用SAPB1的軟體。 那我在生產工單有加入製成費跟人工費的資源。 我想詢問，在我一開始的時候，可能訂單術…

日期：2026-10-12

**你的提問**

可以協助我釐清 

如果我的工廠剛開始，但我已經購入相關設備以及人工。

以及我使用SAPB1的軟體。

那我在生產工單有加入製成費跟人工費的資源。

我想詢問，在我一開始的時候，可能訂單術以及閒置產能情況比較多。

通常建議企業是如何做帳。

**當時回答**

可以。你這個情況，是**新工廠已經開始負擔薪資、折舊等成本，但訂單還沒跟上，工單只能吸收一部分成本**。

通常建議的方向是：**正常生產所需的成本進產品；因訂單不足造成的閒置成本，認列當期損益。不要把整間工廠的支出全部攤進少量產品。**

以下先以 IFRS 的成本原則和 SAP B1 操作邏輯說明。你前面提到越南盾本國幣別，但尚未確認法定帳採用的準則；若是越南公司，最後的科目與稅務處理仍需依當地規範確認。

**1. 先把設備、正常生產、閒置支出分開**

| 支出 | 一般處理方向 |
|---|---|
| 購買機器設備 | 符合條件時列固定資產，不一次全部列入製造費 |
| 讓設備達到可使用狀態所必要的安裝、測試等成本 | 符合資本化條件的部分列入設備成本 |
| 設備已可使用後的折舊 | 開始提列；一般不因沒有訂單、暫時閒置而停止折舊 |
| 實際投入正常生產的人工 | 歸入相關工單成本 |
| 工廠折舊、固定管理人員薪資等固定製造費 | 依「正常產能」分攤到生產 |
| 因訂單不足而無法分攤的固定製造費、可辨認的異常待工成本 | 認列當期損益 |
| 一般開辦、教育訓練、初期營運虧損 | 通常列當期費用，不能只因工廠剛成立就全部資本化 |

設備達可使用狀態後的折舊，以及初期低產量、需求尚未建立所產生的營運損失，應分別處理。[IAS 16 官方說明](https://www.ifrs.org/content/dam/ifrs/publications/pdf-standards/english/2022/issued/part-a/ias-16-property-plant-and-equipment.pdf?bypass=on)

**2. 固定製造費率，應以正常產能計算**

假設以機器工時分攤：

| 項目 | 假設金額／數量 |
|---|---:|
| 每月固定製造費：折舊、廠租、固定間接人工等 | 300,000,000 越南盾 |
| 正常產能 | 1,000 機器小時／月 |
| 固定製造費率 | **300,000 越南盾／小時** |
| 本月因訂單少，實際生產 | 200 小時 |
| 工單吸收的固定製造費 | **60,000,000 越南盾** |
| 未吸收的固定製造費 | **240,000,000 越南盾** |

在這個簡化例子中：

- **6,000 萬**進入工單，之後隨產品轉入製成品、銷貨成本。
- **2.4 億**作為本期未分攤固定製造費，進當期損益。
- 不應為了把費用全部分完，改成 `3 億 ÷ 200 小時＝每小時 150 萬`，讓少量存貨承擔全部閒置成本。

IAS 2 明定，固定製造費依正常產能分攤，不因低產量或設備閒置而提高單位分攤額；未分攤部分於發生當期認列費用。[IAS 2 第 13 段](https://www.ifrs.org/content/dam/ifrs/meetings/2011/february/iasb/ic-0211b03fobs.pdf)

**正常產能也不是機器理論上限。** 新廠可依合理班次、人員配置、正常維修停機及可支持的營運預期建立基準，留下計算依據，定期檢討；不要每月直接跟著當月少量訂單調整分母。

**3. SAP B1 的資源成本，要和實際薪資、折舊對帳**

你在工單放「人工費、製造費」資源的方向可以繼續使用，但要分清楚：

> **薪資、折舊入帳＝記錄實際發生多少成本。**  
> **工單耗用資源＝把其中應由產品負擔的成本轉入在製品。**

SAP B1 資源可以設定標準成本及成本組成；資源耗用時，依設定產生在製品與對應費用／應計科目的分錄。**只是把資源加進工單，還不等於已完成成本過帳。** [SAP 資源主檔說明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f19b844f41b74a8c8ca26f4d501e032b/9862f0b3e7f74e9bb04af71685654f6a.html?locale=en-US&state=PRODUCTION&version=10.0)

以下是一種容易核對的「成本歸集＋工單吸收」設計，金額沿用上例，科目名稱為示意：

**① 實際製造費發生時**

```text
借：製造費用－實際發生        300,000,000
    貸：累計折舊／應付薪資／應付款等  300,000,000
```

**② 工單耗用 200 小時，按正常費率吸收**

```text
借：1174 在製品                60,000,000
    貸：製造費用－工單吸收          60,000,000
```

「工單吸收」是成本轉入存貨的抵減端，**不是另外創造一筆收入**。實際 SAP 分錄須配合你們資源科目及生產過帳模式設定。

**③ 月底分析剩餘差額**

```text
實際製造費 300,000,000
－工單吸收  60,000,000
＝未吸收   240,000,000
```

可將實際與吸收科目結清，差額轉入「未分攤固定製造費／閒置產能成本」損益科目。**如果差額已留在費用科目，重分類時不能再多認一次費用。**

另外，實務上的差額不一定全是閒置：還可能包含費率差、超支、效率差、漏報工時，月底應分開分析。

**4. 人工費建議另外記錄「生產」與「待工」**

例如員工本月薪資 2,000 萬，工時記錄顯示：

| 工時用途 | 處理方向 |
|---|---|
| 正常產品加工、必要換線及正常生產準備 | 依合理政策進工單或製造費分攤 |
| 因沒有訂單而待工 | 可辨認的閒置部分留在當期損益 |
| 一般教育訓練、行政工作 | 分別歸入訓練／管理費用等 |
| 符合設備資本化條件的安裝工作 | 另行評估列入設備成本 |

**不要把全部出勤時數都報到少數工單。** 也要確認你資源中的「製造費」是否已包含直接人工；如果包含，又另外放人工資源，就會重複吸收。

對你目前的新廠，我會建議先建立 **「直接人工、固定製造費、變動製造費、閒置／未吸收成本」** 四類，月結時核對實際發生與工單吸收。初期因閒置而出現虧損，可能正是應呈現的營運結果，不能藉由墊高存貨成本延後認列。

**你目前 B1 的人工、製造費資源，是以「每小時」還是「每件」設定？費率是用預估正常產能算的，還是每月用實際費用除以當月產量？**

---

<a id="q10"></a>

## S145-Q010｜[〔連結〕 翻譯

日期：2026-10-12

**你的提問**

## My request:
[https://www.ifrs.org/content/dam/ifrs/meetings/2011/february/iasb/ic-0211b03fobs.pdf](https://www.ifrs.org/content/dam/ifrs/meetings/2011/february/iasb/ic-0211b03fobs.pdf) 翻譯

**當時回答**

先更正我前面的引用方式：**這份 PDF 是 2011 年 IASB／FASB 討論「保險合約現金流量」的工作人員文件，不是 IAS 2 準則全文。** 與你工廠問題相關的內容，是它在**第 21～22 頁附錄 B 引用的 IAS 2 存貨規定**。前面沒有說清楚，抱歉。[原始文件](https://www.ifrs.org/content/dam/ifrs/meetings/2011/february/iasb/ic-0211b03fobs.pdf#page=21)

這份連結文件受著作權保護，我不能直接提供整份逐字翻譯；以下先提供相關內容的**繁體中文重點摘要，並非正式譯文**：

| 相關段落 | 中文重點 |
|---|---|
| **IAS 2 第 12 段：加工成本** | 產品成本可包含直接人工，以及合理分攤的固定與變動製造費。 |
| **第 13 段：正常產能** | 固定製造費以正常營運下、多期或季節的平均預期產能分攤，並考慮計畫維修停機。 |
| **第 13 段：低產量、閒置** | 不應因生產量低或工廠閒置，就提高每件產品分攤的固定製造費；未分攤部分列入發生當期費用。 |
| **第 13 段：高產量及變動費用** | 異常高產量時，應降低單位固定費用分攤，避免存貨高估；變動製造費依實際使用生產設施的程度分攤。 |
| **第 16 段：排除的成本** | 異常耗損的材料、人工及其他生產成本，應列當期費用，不納入存貨。 |

套用到你的問題：**新廠訂單少，不能因此把所有薪資、折舊都塞進少量工單；應辨認正常生產成本與未吸收的閒置成本。**

如果你需要**全文翻譯**，請直接上傳這份 PDF，我就可以依你提供的文件逐段翻成繁體中文。

---

<a id="q11"></a>

## S145-Q011｜The costs of conversion of inventories include costs directly related to the units of prod…

日期：2026-10-12

**你的提問**

## My request:
The costs of conversion of inventories include costs directly related to the units of
production, such as direct labour. They also include a systematic allocation of
fixed and variable production overheads that are incurred in converting materials
into finished goods. Fixed production overheads are those indirect costs of
production that remain relatively constant regardless of the volume of production,
such as depreciation and maintenance of factory buildings and equipment, and the
cost of factory management and administration. Variable production overheads
are those indirect costs of production that vary directly, or nearly directly, with the
volume of production, such as indirect materials and indirect labour.
13 The allocation of fixed production overheads to the costs of conversion is based
on the normal capacity of the production facilities. Normal capacity is the
production expected to be achieved on average over a number of periods or
seasons under normal circumstances, taking into account the loss of capacity
resulting from planned maintenance. The actual level of production may be used
if it approximates normal capacity. The amount of fixed overhead allocated to
each unit of production is not increased as a consequence of low production or
idle plant. Unallocated overheads are recognised as an expense in the period in
which they are incurred. In periods of abnormally high production, the amount of
fixed overhead allocated to each unit of production is decreased so that
inventories are not measured above cost. Variable production overheads are
allocated to each unit of production on the basis of the actual use of the
production facilities.
14 A production process may result in more than one product being produced
simultaneously. This is the case, for example, when joint products are produced or
when there is a main product and a by-product. When the costs of conversion of
each product are not separately identifiable, they are allocated between the
products on a rational and consistent basis. The allocation may be based, for
example, on the relative sales value of each product either at the stage in the
production process when the products become separately identifiable, or at the
completion of production. Most by-products, by their nature, are immaterial.
When this is the case, they are often measured at net realisable value and this
value is deducted from the cost of the main product. As a result, the carrying
amount of the main product is not materially different from its cost.
21
Agenda paper 3F/58F
IASB/FASB Staff paper
Other costs
15 Other costs are included in the cost of inventories only to the extent that they are
incurred in bringing the inventories to their present location and condition. For
example, it may be appropriate to include non-production overheads or the costs
of designing products for specific customers in the cost of inventories.
16 Examples of costs excluded from the cost of inventories and recognised as
expenses in the period in which they are incurred are:
(a) abnormal amounts of wasted materials, labour or other production costs;
(b) storage costs, unless those costs are necessary in the production process
before a further production stage;
(c) administrative overheads that do not contribute to bringing inventories to
their present location and condition; and
(d) selling costs.
17 IAS 23 Borrowing Costs identifies limited circumstances where borrowing costs
are included in the cost of inventories.
18 An entity may purchase inventories on deferred settlement terms. When the
arrangement effectively contains a financing element, that element, for example a
difference between the purchase price for normal credit terms and the amount
paid, is recognised as interest expense over the period of the financing.
Cost of inventories of a service provider
19 To the extent that service providers have inventories, they measure them at the
costs of their production. These costs consist primarily of the labour and other
costs of personnel directly engaged in providing the service, including supervisory
personnel, and attributable overheads. Labour and other costs relating to sales and
general administrative personnel are not included but are recognised as expenses
in the period in which they are incurred. The cost of inventories of a service
provider does not include profit margins or non-attributable overheads that are
often factored into prices charged by service providers. 


這樣可以翻譯了嗎

**當時回答**

可以，你已經貼上原文，以下是完整的繁體中文翻譯。這是你提供的歷史文件段落之翻譯，並非官方中文版。

**第 12 段｜加工成本**

存貨的加工成本包括與生產單位直接相關的成本，例如直接人工，也包括將原料加工為製成品過程中所發生之固定及變動製造費用的有系統分攤。

固定製造費用，是指不論產量如何變動，仍維持相對固定的間接生產成本，例如廠房及設備的折舊與維護費用，以及工廠管理與行政成本。

變動製造費用，是指隨產量變動而直接或幾乎直接變動的間接生產成本，例如間接材料及間接人工。

**第 13 段｜固定與變動製造費用的分攤**

固定製造費用應以生產設施的**正常產能**為基礎，分攤至加工成本。

正常產能，是指在正常情況下，考量計畫性維修所造成的產能損失後，預期於數個期間或季節平均可以達成的產量。如果實際產量接近正常產能，也可以採用實際產量作為分攤基礎。

**不得因產量偏低或工廠閒置，而增加分攤至每單位產品的固定製造費用。未分攤的製造費用，應於發生當期認列為費用。**

在產量異常偏高的期間，應降低分攤至每單位產品的固定製造費用，使存貨不致以高於成本的金額衡量。

變動製造費用則依生產設施的實際使用情況，分攤至每單位產品。

**第 14 段｜聯產品與副產品**

一個生產過程可能同時產出一種以上的產品，例如生產聯產品，或同時產出主產品與副產品。

當各項產品的加工成本無法個別辨認時，應採用合理且一致的基礎，將成本分攤至各項產品。例如，可以依各項產品在生產過程中得以個別辨認時，或在完工時的相對銷售價值，作為分攤基礎。

多數副產品依其性質而言並不重大。在這種情況下，通常以淨變現價值衡量副產品，並將該金額自主產品成本中扣除。因此，主產品的帳面金額與其成本不會有重大差異。

**其他成本**

**第 15 段｜可納入存貨的其他成本**

其他成本僅在其係為使存貨達到目前所在地點及狀態而發生的範圍內，才能計入存貨成本。

例如，將非生產性間接費用，或為特定客戶設計產品的成本，計入存貨成本，可能是適當的。

**第 16 段｜不得納入存貨成本的項目**

下列為不計入存貨成本，而應於發生當期認列為費用的例子：

（a）異常耗損的材料、人工或其他生產成本。

（b）儲存成本，但在進入下一生產階段之前，生產過程所必要的儲存成本除外。

（c）對使存貨達到目前所在地點及狀態沒有助益的行政管理間接費用。

（d）銷售成本〔此處指銷售活動相關費用，並非已售商品的存貨成本〕。

**第 17 段｜借款成本**

《國際會計準則第 23 號：借款成本》規定，在有限的特定情況下，借款成本應計入存貨成本。

**第 18 段｜延期付款購買存貨**

企業可能以延期付款條件購買存貨。

當此安排實質上包含融資成分時，該融資成分，例如正常信用條件下的購買價格與實際支付金額之間的差額，應於融資期間認列為利息費用。

**服務提供者的存貨成本**

**第 19 段｜服務提供者的存貨衡量**

服務提供者如有存貨，應以提供該服務所發生的生產成本衡量。

這些成本主要包括直接參與提供服務的人員之人工及其他成本，包括督導人員的成本，以及可歸屬的間接費用。

與銷售及一般行政管理人員有關的人工及其他成本，不計入存貨成本，而應於發生當期認列為費用。

服務提供者的存貨成本，不包括其服務收費中通常包含的利潤，以及不可歸屬的間接費用。

---

