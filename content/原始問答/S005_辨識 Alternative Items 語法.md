# 辨識 Alternative Items 語法

來源編號：S005  
聊天 ID：`019f8347-287e-75f0-a506-117e7fc3c024`  
聊天起始日期（台北）：2026-07-21  
保存位置：一般本機紀錄  
問答段落：4

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S005-Q001｜住==' Alternative Items Code Sample ' This sample assumes you have SBODemo_US database inst…

日期：2026-07-21

**你的提問**

住==' Alternative Items Code Sample

' This sample assumes you have SBODemo_US database installed



Dim oCompanyService As SAPbobsCOM.CompanyService

Dim AltItemsService As SAPbobsCOM.AlternativeItemsService

Dim OriItem As SAPbobsCOM.OriginalItem

Dim OriItemParams As SAPbobsCOM.OriginalItemParams

Dim AltItem As SAPbobsCOM.AlternativeItem



oCompanyService = oCompany.GetCompanyService

AltItemsService = oCompanyService.GetBusinessService(SAPbobsCOM.ServiceTypes.AlternativeItemsService)

OriItem = AltItemsService.GetDataInterface(SAPbobsCOM.AlternativeItemsServiceDataInterfaces.aisOriginalItem)

OriItemParams = AltItemsService.GetDataInterface(SAPbobsCOM.AlternativeItemsServiceDataInterfaces.aisOriginalItemParams)



'Update Alternative Item

OriItemParams.ItemCode = "A00001"

OriItem = AltItemsService.GetItem(OriItemParams)



'Getting the first alternative item

AltItem = OriItem.AlternativeItems.Item(0)



'Updating Alternative Item A00004 to have match factor 400

AltItem.AlternativeItemCode = "A00004"

AltItem.MatchFactor = 300

AltItemsService.UpdateItem(OriItem)


這是甚麼語法

**當時回答**

這是 **VB.NET（Visual Basic .NET）** 語法，使用 SAP Business One 的 DI API（`SAPbobsCOM`）操作「替代品項／Alternative Items」。

不過程式第一行的 `住==` 看起來像是誤貼文字，並非 VB.NET 語法。

這段程式的作用是：

1. 從 `oCompany` 取得 SAP B1 的 `CompanyService`
2. 取得 `AlternativeItemsService`
3. 查詢原始品項 `A00001`
4. 取出它的第一筆替代品項（`Item(0)`）
5. 將替代品項設為 `A00004`，並將 `MatchFactor` 設為 `300`
6. 呼叫 `UpdateItem` 儲存更新

VB.NET 的辨識特徵包括：

- `Dim 變數 As 型別`
- 註解以單引號 `'` 開頭
- 不用分號結尾
- 物件存取使用 `.`

---

<a id="q2"></a>

## S005-Q002｜' Alternative Items Code Sample ' This sample assumes you have SBODemo_US database install…

日期：2026-07-21

**你的提問**

' Alternative Items Code Sample

' This sample assumes you have SBODemo_US database installed



Dim oCompanyService As SAPbobsCOM.CompanyService

Dim AltItemsService As SAPbobsCOM.AlternativeItemsService

Dim OriItem As SAPbobsCOM.OriginalItem

Dim OriItemParams As SAPbobsCOM.OriginalItemParams

Dim AltItem As SAPbobsCOM.AlternativeItem



oCompanyService = oCompany.GetCompanyService

AltItemsService = oCompanyService.GetBusinessService(SAPbobsCOM.ServiceTypes.AlternativeItemsService)

OriItem = AltItemsService.GetDataInterface(SAPbobsCOM.AlternativeItemsServiceDataInterfaces.aisOriginalItem)

OriItemParams = AltItemsService.GetDataInterface(SAPbobsCOM.AlternativeItemsServiceDataInterfaces.aisOriginalItemParams)



'Update Alternative Item

OriItemParams.ItemCode = "A00001"

OriItem = AltItemsService.GetItem(OriItemParams)



'Getting the first alternative item

AltItem = OriItem.AlternativeItems.Item(0)



'Updating Alternative Item A00004 to have match factor 400

AltItem.AlternativeItemCode = "A00004"

AltItem.MatchFactor = 300

AltItemsService.UpdateItem(OriItem)

**當時回答**

這段是 **VB.NET**，並透過 **SAP Business One DI API (`SAPbobsCOM`)** 更新替代品項資料。

它會：

- 取得 `AlternativeItemsService`
- 讀取原始品項 `A00001`
- 取得該品項的第一個替代品項：`Item(0)`
- 將其替代品項代碼設定為 `A00004`
- 將匹配係數 `MatchFactor` 設為 `300`
- 呼叫 `UpdateItem` 寫回 SAP B1

注意註解寫的是「match factor 400」，但實際程式是：

```vb
AltItem.MatchFactor = 300
```

若要設定成 400，應改成：

```vb
AltItem.MatchFactor = 400
```

---

<a id="q3"></a>

## S005-Q003｜如果我是要自動化 就是有個按鈕的那種呢

日期：2026-07-21

**你的提問**

如果我是要自動化 就是有個按鈕的那種呢

**當時回答**

如果你要「按一個按鈕就自動更新」，通常會做成 **SAP Business One Add-On**，用 **VB.NET / C# + SAP B1 UI API + DI API**。

概念是：

```text
使用者按 SAP B1 表單上的按鈕
        ↓
UI API 捕捉按鈕事件
        ↓
DI API 呼叫 AlternativeItemsService
        ↓
更新替代品與 MatchFactor
```

按鈕事件裡可放類似這段 VB.NET：

```vb
Private Sub UpdateAlternativeItem()

    Dim companyService As SAPbobsCOM.CompanyService =
        oCompany.GetCompanyService()

    Dim service As SAPbobsCOM.AlternativeItemsService =
        companyService.GetBusinessService(
            SAPbobsCOM.ServiceTypes.AlternativeItemsService)

    Dim itemParams As SAPbobsCOM.OriginalItemParams =
        service.GetDataInterface(
            SAPbobsCOM.AlternativeItemsServiceDataInterfaces.aisOriginalItemParams)

    itemParams.ItemCode = "A00001"

    Dim originalItem As SAPbobsCOM.OriginalItem =
        service.GetItem(itemParams)

    Dim alternativeItem As SAPbobsCOM.AlternativeItem =
        originalItem.AlternativeItems.Item(0)

    alternativeItem.AlternativeItemCode = "A00004"
    alternativeItem.MatchFactor = 300

    service.UpdateItem(originalItem)

    SBO_Application.MessageBox("替代品項已更新。")

End Sub
```

在 UI API 中，當按鈕被按下時呼叫：

```vb
If pVal.ItemUID = "btnUpdate" AndAlso
   pVal.EventType = SAPbouiCOM.BoEventTypes.et_ITEM_PRESSED AndAlso
   pVal.BeforeAction = False Then

    UpdateAlternativeItem()

End If
```

`btnUpdate` 是你新增到 SAP B1 表單上的按鈕 ID。

實務上不要把 `A00001`、`A00004`、`300` 寫死；較好的方式是讓使用者從表單欄位輸入或選擇，再讀取欄位值後更新。這樣才是真正能操作的自動化按鈕。

---

<a id="q4"></a>

## S005-Q004｜This email address is already registered with a Natural8 account. Log In via the link belo…

日期：2026-08-03

**你的提問**

This email address is already registered with a Natural8 account. Log In via the link below.

**當時回答**

這句意思是：

「此電子郵件地址已註冊 Natural8 帳戶。請透過下方連結登入。」

---

