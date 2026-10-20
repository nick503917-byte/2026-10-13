# 整理美國正式區 Markdown

來源編號：S092  
聊天 ID：`01a01cf5-8746-77f0-9963-9d717dfceb80`  
聊天起始日期（台北）：2026-08-20  
保存位置：一般本機紀錄  
問答段落：3

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S092-Q001｜幫我整理成md 檔 檔案名稱叫 美國正式區

日期：2026-08-20

**你的提問**

# Files pasted by the user:

## "[2026/8/20 上午 10:15] Nick: USE [WellShin_US_Official] GO /****** Object: Stored…": C:\Users\nick_yeh\.codex/attachments/72bdb4e5-e91c-438f-aa73-fd638dcd4ec0/pasted-text.txt

## My request:
幫我整理成md 檔 檔案名稱叫 美國正式區

附加文字內容（原提問附檔，未執行）：

````text
[2026/8/20 上午 10:15] Nick: USE [WellShin_US_Official]
GO
/****** Object:  StoredProcedure [dbo].[SBO_SP_TransactionNotification]    Script Date: 2026/8/20 上午 10:14:17 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER proc [dbo].[SBO_SP_TransactionNotification] 

@object_type nvarchar(30),     -- SBO Object Type
@transaction_type nchar(1),   -- [A]dd, [U]pdate, [D]elete, [C]ancel, C[L]ose
@num_of_cols_in_key int,
@list_of_key_cols_tab_del nvarchar(255),
@list_of_cols_val_tab_del nvarchar(255)

AS

begin

-- Return values
declare @error  int    -- Result (0 for no error)
declare @error_message nvarchar (200)   -- Error string to be displayed
select @error = 0
select @error_message = N'Ok'

--------------------------------------------------------------------------------------------------------------------------------

-- ADD YOUR CODE HERE


----------------------------------------------------------------
-- 銷售訂單 (17) 禁止來源為已過期的報價單
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        INNER JOIN OQUT Q ON Q.DocEntry = L.BaseEntry
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType = '17'         -- 草稿銷售訂單
          AND L.BaseType = '23'        -- 來源為銷售報價
          AND Q.DocDueDate IS NOT NULL
          AND Q.DocDueDate < CAST(GETDATE() AS DATE)
    )
    BEGIN
        SET @error = 1701;
        SET @error_message = N'Sales order creation blocked: Source quotation expired.';
    END
END

------------------------------------------------------------------
---- 規則：
---- 草稿（112）之 銷售訂單(17) 
---- 表頭 U_Incoterm、TrnspCode不可空白
---- 草稿表：ODRF；文件型別以 ODRF.ObjType 區分
------------------------------------------------------------------

IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM ODRF T0
   WHERE T0.DocEntry = @list_of_cols_val_tab_del 
    AND T0.ObjType = ('17') 
     and T0.TrnspCode = '-1'
     and ISNULL(T0.U_Incoterm, '') = '' )
  
 BEGIN
  SET @error = '1703'
  SET @error_message = N'Shipping charge、Shipping Type field is required!!'
 END
END

----------------------------------------------------------------
-- 規則：
-- 銷售報價單草稿(23)，檢查明細項目是否限定客戶
-- 若項目主檔 U_CardCode 有設定，該項目只能被對應客戶使用
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    DECLARE @QuoteCardCode NVARCHAR(20)
    DECLARE @InvalidItem NVARCHAR(50)

    -- 取得草稿表頭的客戶代號
    SELECT @QuoteCardCode = CardCode
    FROM ODRF
    WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT)

    -- 檢查明細是否有違規項目
    IF EXISTS (
        SELECT 1
        FROM DRF1 L
        INNER JOIN OITM I ON L.ItemCode = I.ItemCode
        WHERE L.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND I.U_CardCode IS NOT NULL
          AND I.U_CardCode <> @QuoteCardCode
    )
    BEGIN
        SET @error = 2301
        SET @error_message = N'Item restricted: Cannot be selected for this customer.'
    END
END


----------------------------------------------------------------
-- Sales-Module-銷售模組-正式單必填欄位
-- 1.Sales-Quotation-銷售報價-23-OQUT
-- 2.Sales-Order-銷售訂單-17-ORDR
----------------------------------------------------------------
IF (@object_type IN ('17', '23') AND @transaction_type IN ('A', 'U'))
BEGIN
    IF EXISTS (
         --1. 檢查 23 銷售報價單 (OQUT)
        SELECT 1 FROM OQUT WITH (NOLOCK)
        WHERE @object_type = '23' 
          AND DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_Incoterm,'') = '')

        UNION ALL

        -- 2. 檢查 17 銷售訂單 (ORDR)
        SELECT 1 FROM ORDR WITH (NOLOCK)
        WHERE @object_type = '17' 
          AND DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_Incoterm,'') = '')
    )
    BEGIN
        SET @error = 1901
        SET @error_message = N'Applicant，
[2026/8/20 上午 10:15] Nick: Applicants Department，Shipping Charge Field are required'
    END
END


----收貨採購單建立帳號填入申請人欄位--
--if @object_type in('20') and (@transaction_type IN('A','U'))
--BEGIN 
--update T0
--SET T0.U_ReqPerson = T1.U_NAME
--FROM OPDN T0
--INNER JOIN OUSR T1 ON T0.UserSign = T1.USERID
--WHERE T0.DocEntry = @list_of_cols_val_tab_del 
--END
--20260605 Rachel 後續有填寫申請人，這個其實不需要

----------------------------------------------------------------
-- 收貨採購單 (20)
-- QCnum、Qcpic 必須核准才可建立單據
----------------------------------------------------------------
IF (@object_type = '20' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPDN T0
        WHERE T0.DocEntry = @list_of_cols_val_tab_del
          AND (
                ISNULL(T0.U_QCnum,'N') <> 'Y'
             OR ISNULL(T0.U_QCpic,'N') <> 'Y'
          )
    )
    BEGIN
        SET @error = 9002
        SET @error_message = N'Item & Quantity Verified, Drawing Verified is required!'
    END

END

--401固定資產主檔欄位必填--
if @object_type in('4') and (@transaction_type IN('A','U'))
if EXISTS 
(SELECT 1 FROM OITM T0 
WHERE T0.[ItemCode] LIKE @list_of_cols_val_tab_del 
and T0.ItemType = 'F'
and ((T0.U_AssetSubCategory is null or T0.U_AssetSubCategory = '')
or (T0.Location is null or T0.Location = '')
or (T0.U_AstChangeReason is null or T0.U_AstChangeReason = '')
or (T0.Employee is null or T0.Employee = '')))
BEGIN 
       SET @error = '401'
       SET @error_message = N'Missing required fixed asset fields: Asset Class, Location, Custodian, Transaction Reason.'
END

--固定資產主檔欄位必填--

----交貨時批次需選擇對應銷售單號批次(批次屬性1)--

IF @object_type IN ('15') AND (@transaction_type IN ('A'))
BEGIN
    IF EXISTS (
        SELECT 1 
        FROM ODLN T0 
        INNER JOIN DLN1 T1 ON T0.DocEntry = T1.DocEntry
        INNER JOIN ORDR T2 ON T1.BaseEntry = T2.DocEntry
        INNER JOIN IBT1 T3 ON T3.BaseEntry = T0.DocEntry AND T3.BaseType = '15'
        INNER JOIN OIBT T4 ON T3.ItemCode = T4.ItemCode AND T3.BatchNum = T4.BatchNum
        WHERE CAST(T0.[DocEntry] AS NVARCHAR(20)) LIKE @list_of_cols_val_tab_del 
          -- Safely compare Integer DocNum to NVARCHAR SuppSerial
          AND CAST(T2.DocNum AS NVARCHAR(20)) != T4.SuppSerial 
    )
    BEGIN 
        SET @error = '1503'
        SET @error_message = N'Please select the designated order batch!!!!' --Jerry有翻譯這裡的錯誤訊息
    END
END
----交貨時批次需選擇對應銷售單號批次(批次屬性1)--
--Rachel 20260622 這個是卡轉pick list 轉交貨單的卡控，再檢查
--Rachel 20260628 Vincent已修改

--有圖面和SOP才能建立工單--
if @object_type in('202') and (@transaction_type IN('A','U'))
if EXISTS 
(SELECT 1 FROM WOR1 T0 
INNER JOIN OWOR T1 ON T1.DocEntry = T0.DocEntry
INNER JOIN OITM T2 ON T0.ItemCode = T2.ItemCode
INNER JOIN OITM T3 ON T1.ItemCode = T3.ItemCode
WHERE T0.[DocEntry] LIKE @list_of_cols_val_tab_del and (T2.AtcEntry IS NULL or T3.AtcEntry IS NULL))
BEGIN 
       SET @error = '20201'
       SET @error_message = N'Cannot create work order: Drawing and SOP missing!'
END
--有圖面和SOP才能建立工單--



----交貨時若信用額度不足則不可出貨--
--if @object_type in('15') and (@transaction_type IN('A'))
--if EXISTS 
--(SELECT 1 FROM ODLN T0 
--INNER JOIN OCRD T1 ON T0.CardCode = T1.CardCode
--WHERE T0.[DocEntry] LIKE @list_of_cols_val_tab_del 
--and T1.[CreditLine] - T1.[OrdersBal] - T1.[DNotesBal] - T1.[Balance] < 0)
--BEGIN 
--       SET @error = '1502'
--       SET @error_message = N'Cannot ship due to insufficient credit limit!!!!' -- Jerry有翻譯這裡的錯誤訊息
--END
--交貨時若信用額度不足則不可出貨--
--20260605 Rachel 補單期間先註解，完成後再打開

--付款條件為T000時需訂金收款完成才能交貨--
if @object_type in('15') and (@transaction_type IN('A'))
if EXISTS 
(SELECT 1 FROM ODLN T0 
INNER JOIN DLN1 T1 ON T0.DocEntry = T1.DocEntry
INNER JOIN DPI1 T2 ON T1.BaseEntry = T2.BaseEntry and T2.BaseType = '17'
WHERE T0.[DocEntry] LIKE @list_of_cols_val_tab_del and T0.GroupNum = '1' and T2.LineStatus = 'O')
BEGIN 
       SET @error = '1501'
       SET @error_message = 'Delivery blocked: Deposit payment required for term T000.'
END
---------------------------------------------------
--銷售退貨請求、銷售退貨：限制表身退貨原因 NICK 銷售請求退貨理由限制
---------------------------------------------------
IF @object_type IN
[2026/8/20 上午 10:15] Nick: ('234000031', '16')
   AND @transaction_type IN ('A', 'U')
BEGIN
    IF
    (
        (
            @object_type = '234000031'
            AND EXISTS
            (
                SELECT 1
                FROM RRR1 T0
                WHERE T0.DocEntry = @list_of_cols_val_tab_del
                  AND T0.U_ReturnReason IN
                      ('RR11', 'RR12', 'RR13', 'RR14', 'RR15', 'RR16')
            )
        )
        OR
        (
            @object_type = '16'
            AND EXISTS
            (
                SELECT 1
                FROM RDN1 T0
                WHERE T0.DocEntry = @list_of_cols_val_tab_del
                  AND T0.U_ReturnReason IN
                      ('RR11', 'RR12', 'RR13', 'RR14', 'RR15', 'RR16')
            )
        )
    )
    BEGIN
        SET @error = 1601
        SET @error_message = N'退貨原因不可選擇 RR11、RR12、RR13、RR14、RR15、RR16。'
    END
END




--付款條件為T000時需訂金收款完成才能交貨--

----------------------------------------------------------------
-- 草稿必填卡控：發貨單、收貨單、庫存調撥單
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType IN ('59','60','67','14','21')  -- 15=發貨單 16=收貨單 67=庫存調撥單
          AND (
                ISNULL(H.U_ReqPerson,'')   = '' OR
                ISNULL(H.U_ChangeType,'')  = '' OR
                ISNULL(H.U_ChangeReason,'')= '' OR
                ISNULL(H.U_TypeName,'')    = '' OR
    ISNULL(H.U_ReqDept,'')    = ''
              )
    )
    BEGIN
        SET @error = 3001
        SET @error_message = N'Applicant， Applicants Department，Change Type， Change Reason，Type Name is required'
    END
END

----銷售和採購退貨原因欄位必填

--IF (@object_type = '112' AND @transaction_type IN ('A','U'))
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM ODRF H
--        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
--        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--        AND
--        (
--            -- 銷售採購退貨(請求)草稿
--            (H.ObjType IN ('16','234000031','21','234000032')
--             AND ISNULL(L.ItemCode,'') <> ''
--             AND (
--                    LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                 OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                 )
--            )

--            OR

--            -- AR AP Credit Memo 草稿
--            (H.ObjType IN ('14','19')
--             AND ISNULL(L.ItemCode,'') <> ''
--             AND ISNULL(L.NoInvtryMv,'N') = 'N'
--             AND (
--                    LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                 OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                 )
--            )

            

--        )
--    )

--    BEGIN
--        SET @error = 2203
--        SET @error_message = N'Return Reason and Return Description are required fields. Please complete the line item information.'
--    END

--END

----------------------------------
---- 銷售和採購退貨原因欄位必填 (正式單)
----------------------------------
--IF @transaction_type IN ('A','U')
--BEGIN

--    IF
--       -- Sales Return (ORDN)
--       (@object_type = '16' AND EXISTS
--       (
--            SELECT 1
--            FROM ORDN H
--            INNER JOIN RDN1 L ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND ISNULL(L.ItemCode,'') <> ''
--              AND (
--                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                  )
--       ))

--       OR

--       -- Sales Return Request (ORRR)
--       (@object_type = '234000031' AND EXISTS
--       (
--            SELECT 1
--            FROM ORRR H
--            INNER JOIN RRR1 L ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND
[2026/8/20 上午 10:15] Nick: ISNULL(L.ItemCode,'') <> ''
--              AND (
--                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                  )
--       ))

--       OR

--       -- Goods Return (ORPD)
--       (@object_type = '21' AND EXISTS
--       (
--            SELECT 1
--            FROM ORPD H
--            INNER JOIN PDN1 L ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND ISNULL(L.ItemCode,'') <> ''
--              AND (
--                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                  )
--       ))

--       OR

--       -- Goods Return Request (OPRR)
--       (@object_type = '234000032' AND EXISTS
--       (
--            SELECT 1
--            FROM OPRR H
--            INNER JOIN PRR1 L ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND ISNULL(L.ItemCode,'') <> ''
--              AND (
--                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                  )
--       ))

--       OR

--       -- A/R Credit Memo (ORIN)
--       (@object_type = '14' AND EXISTS
--       (
--            SELECT 1
--            FROM ORIN H
--            INNER JOIN RIN1 L ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND ISNULL(L.ItemCode,'') <> ''
--              AND ISNULL(L.NoInvtryMv,'N') = 'N'
--              AND (
--                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                  )
--       ))

--       OR

--       -- A/P Credit Memo (ORPC)
--       (@object_type = '19' AND EXISTS
--       (
--            SELECT 1
--            FROM ORPC H
--            INNER JOIN RPC1 L ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND ISNULL(L.ItemCode,'') <> ''
--              AND ISNULL(L.NoInvtryMv,'N') = 'N'
--              AND (
--                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
--                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
--                  )
--       ))

--    BEGIN
--        SET @error = 2204
--        SET @error_message = N'Return Reason and Return Description are required fields. Please complete the line item information'
--    END

--END


--------------------------------
--銷售和採購退貨原因欄位必填 Nick V1版本
------------------------------------
--IF (@object_type = '112' AND @transaction_type IN ('A', 'U'))
--BEGIN
--    IF EXISTS
--    (
--        SELECT 1
--        FROM ODRF H
--        INNER JOIN DRF1 L
--            ON H.DocEntry = L.DocEntry
--        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--          AND
--          (
--              /* Sales Return / Sales Return Request
--                 Goods Return / Goods Return Request */
--              (
--                  H.ObjType IN ('16', '234000031', '21', '234000032')
--                  AND H.DocType IN ('I', 'S')
--                  AND
--                  (
--                      H.DocType = 'S'
--                      OR ISNULL(L.ItemCode, '') <> ''
--                  )
--                  AND
--                  (
--                      LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--                      OR
--                      LEN(LTRIM(RTRIM(ISNULL(
--                          CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                      )))) = 0
--                  )
--              )

--              OR

--              /* A/R Credit Memo / A/P Credit Memo */
--              (
--                  H.ObjType IN ('14', '19')
--
[2026/8/20 上午 10:15] Nick: AND H.DocType IN ('I', 'S')
--                  AND
--                  (
--                      H.DocType = 'S'
--                      OR
--                      (
--                          H.DocType = 'I'
--                          AND ISNULL(L.ItemCode, '') <> ''
--                          AND ISNULL(L.NoInvtryMv, 'N') = 'N'
--                      )
--                  )
--                  AND
--                  (
--                      LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--                      OR
--                      LEN(LTRIM(RTRIM(ISNULL(
--                          CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                      )))) = 0
--                  )
--              )
--          )
--    )
--    BEGIN
--        SET @error = 2203
--        SET @error_message = N'退貨原因與退貨說明為必填欄位，請完整填寫各明細列資料。'
--    END
--END

--/* ------------------------------------------------------------
--   Posted documents
--   ------------------------------------------------------------ */
--IF (@transaction_type IN ('A', 'U'))
--BEGIN
--    IF
--       /* Sales Return (ORDN / RDN1) */
--       (@object_type = '16' AND EXISTS
--       (
--            SELECT 1
--            FROM ORDN H
--            INNER JOIN RDN1 L
--                ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND H.DocType IN ('I', 'S')
--              AND
--              (
--                  H.DocType = 'S'
--                  OR ISNULL(L.ItemCode, '') <> ''
--              )
--              AND
--              (
--                  LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--                  OR
--                  LEN(LTRIM(RTRIM(ISNULL(
--                      CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                  )))) = 0
--              )
--       ))

--       OR

--       /* Sales Return Request (ORRR / RRR1) */
--       (@object_type = '234000031' AND EXISTS
--       (
--            SELECT 1
--            FROM ORRR H
--            INNER JOIN RRR1 L
--                ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND H.DocType IN ('I', 'S')
--              AND
--              (
--                  H.DocType = 'S'
--                  OR ISNULL(L.ItemCode, '') <> ''
--              )
--              AND
--              (
--                  LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--                  OR
--                  LEN(LTRIM(RTRIM(ISNULL(
--                      CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                  )))) = 0
--              )
--       ))

--       OR
--/* Goods Return (ORPD / RPD1) */
--       (@object_type = '21' AND EXISTS
--       (
--            SELECT 1
--            FROM ORPD H
--            INNER JOIN RPD1 L
--                ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND H.DocType IN ('I', 'S')
--              AND
--              (
--                  H.DocType = 'S'
--                  OR ISNULL(L.ItemCode, '') <> ''
--              )
--              AND
--              (
--                  LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--                  OR
--                  LEN(LTRIM(RTRIM(ISNULL(
--                      CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                  )))) = 0
--              )
--       ))

--       OR

--       /* Goods Return Request (OPRR / PRR1) */
--       (@object_type = '234000032' AND EXISTS
--       (
--            SELECT 1
--            FROM OPRR H
--            INNER JOIN PRR1 L
--                ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND H.DocType IN ('I', 'S')
--              AND
--              (
--                  H.DocType = 'S'
--                  OR ISNULL(L.ItemCode, '') <> ''
--              )
--              AND
--              (
--                  LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--
[2026/8/20 上午 10:15] Nick: OR
--                  LEN(LTRIM(RTRIM(ISNULL(
--                      CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                  )))) = 0
--              )
--       ))

--       OR

--       /* A/R Credit Memo (ORIN / RIN1) */
--       (@object_type = '14' AND EXISTS
--       (
--            SELECT 1
--            FROM ORIN H
--            INNER JOIN RIN1 L
--                ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND H.DocType IN ('I', 'S')
--              AND
--              (
--                  H.DocType = 'S'
--                  OR
--                  (
--                      H.DocType = 'I'
--                      AND ISNULL(L.ItemCode, '') <> ''
--                      AND ISNULL(L.NoInvtryMv, 'N') = 'N'
--                  )
--              )
--              AND
--              (
--                  LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--                  OR
--                  LEN(LTRIM(RTRIM(ISNULL(
--                      CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                  )))) = 0
--              )
--       ))

--       OR

--       /* A/P Credit Memo (ORPC / RPC1) */
--       (@object_type = '19' AND EXISTS
--       (
--            SELECT 1
--            FROM ORPC H
--            INNER JOIN RPC1 L
--                ON H.DocEntry = L.DocEntry
--            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND H.DocType IN ('I', 'S')
--              AND
--              (
--                  H.DocType = 'S'
--                  OR
--                  (
--                      H.DocType = 'I'
--                      AND ISNULL(L.ItemCode, '') <> ''
--                      AND ISNULL(L.NoInvtryMv, 'N') = 'N'
--                  )
--              )
--              AND
--              (
--                  LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason, '')))) = 0
--                  OR
--                  LEN(LTRIM(RTRIM(ISNULL(
--                      CAST(L.U_ReturnRemark AS NVARCHAR(MAX)), ''
--                  )))) = 0
--              )
--       ))
--    BEGIN
--        SET @error = 2204
--        SET @error_message = N'退貨原因與退貨說明為必填欄位，請完整填寫各明細列資料。'
--    END
--END
----------------------------------------------------------------
-- 草稿必填卡控：申請人和申請部門必填
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType IN ('23','17','16','234000031','1470000113','22','234000032','14','19','60','59','13','15')  --Jerry在這裡加(13,15)
          AND (
                ISNULL(H.U_ReqPerson,'')   = '' OR
    ISNULL(H.U_ReqDept,'')    = ''
              )
    )
    BEGIN
        SET @error = 3002
        SET @error_message = N'Applicant， Applicants Department Field is required'
    END
END

----------------------------------------------------------------
-- 交貨單正式單必填
----------------------------------------------------------------
IF (@object_type = '15' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODLN H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 3003
        SET @error_message = N'Applicant， Applicants Department Field is required'
    END

END

----------------------------------------------------------------
-- AR Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '13' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OINV H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 3004
        SET @error_message = N'Applicant， Applicants Department
[2026/8/20 上午 10:15] Nick: Field is required'
    END

END

----------------------------------------------------------------
-- AR Down Payment Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '203' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODPI H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 20305
        SET @error_message = N'Applicant， Applicants Department Field is required'
    END

END

------------------------------------------------------------------
---- 採購訂單草稿：單價高於採購報價 → U_IsHigher = 'Y'
------------------------------------------------------------------
--IF @object_type = '112' AND @transaction_type IN ('A','U')
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM DRF1 T0
--        INNER JOIN PQT1 T1
--            ON T0.BaseEntry = T1.DocEntry
--            AND T0.BaseLine = T1.LineNum
--        WHERE T0.DocEntry = @list_of_cols_val_tab_del
--          AND T0.ObjType = '22'
--          AND T0.BaseType = 540000006
--          AND ISNULL(T0.Price,0) > ISNULL(T1.Price,0)
--    )
--    BEGIN
--        UPDATE ODRF
--        SET U_PurchStatus = 'Y'
--        WHERE DocEntry = @list_of_cols_val_tab_del
--    END
--    ELSE
--    BEGIN
--        UPDATE ODRF
--        SET U_PurchStatus = 'N'
--        WHERE DocEntry = @list_of_cols_val_tab_del
--    END

--END

----------------------------------------------------------------
-- AR Credit Memo 正式單必填
----------------------------------------------------------------
IF (@object_type = '14' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ORIN H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 1405
        SET @error_message = N'Applicant， Applicants Department Field is required'
    END

END

----------------------------------------------------------------
-- 收貨採購單正式單必填
----------------------------------------------------------------
IF (@object_type = '20' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPDN H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
    OR ISNULL(H.U_ChangeType,'')   = ''
    OR ISNULL(H.U_ChangeReason,'')   = ''
    OR ISNULL(H.U_TypeName,'')   = ''
          )
    )
    BEGIN
        SET @error = 2005
        SET @error_message = N'Applicant， Applicants Department，Purchase Type，Purchase Request Type, Change Type, Change Reason, Type name Field are required'
    END

END


----------------------------------------------------------------
-- 收貨採購單表身 Invoice No 必填 -- 20260606 Rachel
----------------------------------------------------------------

IF (@object_type = '20' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPDN H
        INNER JOIN PDN1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND ISNULL(L.U_InvoiceNo,'') = ''
    )
    BEGIN
        SET @error = 2006
        SET @error_message = N'Invoice No field is required.'
    END

END

----------------------------------------------------------------
-- AP Down Payment Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '204' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODPO H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR
[2026/8/20 上午 10:15] Nick: ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 3005
        SET @error_message = N'Applicant， Applicants Department，Purchase Type，Purchase Request Type Field are required'
    END

END

----------------------------------------------------------------
-- AP Credit Memo 正式單必填
----------------------------------------------------------------
IF (@object_type = '19' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ORPC H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 1902
        SET @error_message = N'Applicant， Applicants Department，Purchase Type，Purchase Request Type Field are required'
    END

END



----------------------------------------------------------------
-- AP Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '18' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPCH H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 1903
        SET @error_message = N'Applicant， Applicants Department，Purchase Type，Purchase Request Type Field are required'
    END

END


----------------------------------------------------------------
-- 採購報價 正式單必填
----------------------------------------------------------------
IF (@object_type = '540000006' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPQT H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 540605
        SET @error_message = N'Applicant， Applicants Department Field，Purchase Type，Purchase Request Type Field are required is required'
    END

END

----------------------------------------------------------------
-- 草稿必填卡控：採購類型和請購類型必填
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType IN ('1470000113','22','234000032','21','204','19','540000006','18','20') --Jerry 加(540000006,18,20)
          AND (
                ISNULL(H.U_PRType,'')   = '' OR
    ISNULL(H.U_Purch_Type,'')    = ''
              )
    )
    BEGIN
        SET @error = 4002
        SET @error_message = N'Purchase Type，Purchase Request Type Field are required'
    END
END

------------------------------------------------------------------
---- 收貨採購數量不可超過採購訂單數量
------------------------------------------------------------------
--IF (@object_type = '20' AND @transaction_type IN ('A','U'))
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM PDN1 GR
--        INNER JOIN POR1 PO
--            ON GR.BaseEntry = PO.DocEntry
--            AND GR.BaseLine = PO.LineNum
--            AND GR.BaseType = 22
--        WHERE GR.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--        AND (
--                (
--                    SELECT SUM(T1.Quantity)
--                    FROM PDN1 T1
--                    WHERE T1.BaseEntry = PO.DocEntry
--                    AND T1.BaseLine = PO.LineNum
--                    AND T1.BaseType = 22
--                ) > PO.Quantity
--            )
--    )

--    BEGIN
--        SET @error = 5001
--        SET @error_message = N'Goods Received quantity cannot exceed the purchase order quantity.' -- Jerry有翻譯這裡的錯誤訊息
--
[2026/8/20 上午 10:15] Nick: END

--END

----------------------------------------------------------------
-- 採購訂單草稿
-- Series = 109 時，表身 U_ReqRemark 必填
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND H.ObjType = '22'
          AND H.Series = 109
          AND ISNULL(L.ItemCode,'') <> ''
          AND LEN(ISNULL(CAST(L.U_ReqRemark AS NVARCHAR(MAX)),'')) = 0
    )
    BEGIN
        SET @error = 2205
        SET @error_message = N'Requisition description is required for Fixed Asset Purchase Orders.'
    END

END

----------------------------------------------------------------
-- 草稿：銷售報價 / 銷售訂單
-- NumAtCard、CntctCode 必填
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND ObjType IN ('23','17')
          AND (
                ISNULL(NumAtCard,'') = ''
             OR ISNULL(CntctCode,0) = 0
          )
    )
    BEGIN
        SET @error = 6003
        SET @error_message = N'PO No. and Rep field are required'
    END

END

----未完成生產發貨投料不可進行生產收貨--

IF @object_type IN ('59') and @transaction_type IN ('A') --生產收貨，新增
    BEGIN
    IF EXISTS (
        SELECT 1
        FROM OIGN T0
  INNER JOIN IGN1 T1 ON T0.DocEntry = T1.DocEntry
  INNER JOIN OWOR T2 ON T1.BaseEntry = T2.DocEntry and T1.BaseType = '202'
  INNER JOIN WOR1 T3 ON T2.DocEntry = T3.DocEntry
  WHERE T0.DocEntry = @list_of_cols_val_tab_del
  AND T3.[IssuedQty] < T3.[PlannedQty]
    )
    BEGIN
        SET @error = '9999'
        SET @error_message = N'Production goods receipt is blocked: Materials must be issued and fed first.'
    END
END

----------------------------------------------------------------
-- 正式單必填卡控：發貨單、收貨採購、AR貸項、AP貸項、
--              庫存收貨、庫存發貨、庫存調撥
----------------------------------------------------------------
IF @transaction_type IN ('A','U')
BEGIN

    DECLARE @MissingFields3 NVARCHAR(250) = N'';


    ----------------------------------------------------------------
    -- Goods Return (ORPD)
    ----------------------------------------------------------------
    IF @object_type = '21'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「Applicant」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「Applicants department」  ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「Change Type」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「Change Reason」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「Type Name」 ' ELSE N'' END
        FROM ORPD
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- A/R Credit Memo (ORIN)
    ----------------------------------------------------------------
    ELSE IF @object_type = '14'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「Applicant」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「Applicants department」  ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「Change Type」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「Change Reason」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「Type Name」 ' ELSE N'' END
        FROM ORIN
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Goods Receipt (OIGN)
    ----------------------------------------------------------------
    ELSE IF @object_type = '59'
[2026/8/20 上午 10:15] Nick: BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「Applicant」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「Applicants department」  ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「Change Type」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「Change Reason」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「Type Name」 ' ELSE N'' END
        FROM OIGN
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Goods Issue (OIGE)
    ----------------------------------------------------------------
    ELSE IF @object_type = '60'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「Applicant」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「Applicants department」  ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「Change Type」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「Change Reason」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「Type Name」 ' ELSE N'' END
        FROM OIGE
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Inventory Transfer (OWTR)
    ----------------------------------------------------------------
    ELSE IF @object_type = '67'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「Applicant」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「Applicants department」  ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「Change Type」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「Change Reason」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「Type Name」 ' ELSE N'' END
        FROM OWTR
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Error Message
    ----------------------------------------------------------------
    IF @MissingFields3 <> N''
    BEGIN
        SET @error = 4003;
        SET @error_message = @MissingFields3 + N' is required，please fill in.';
    END

END
 /* AP 貸項通知書驗證 */
IF @object_type = '19'
   AND @transaction_type IN ('A', 'U')
BEGIN
    DECLARE @APCreditMemoDocEntry INT

    SET @APCreditMemoDocEntry =
        TRY_CONVERT(INT, @list_of_cols_val_tab_del)

    IF EXISTS
    (
        SELECT 1
        FROM dbo.RPC1
        WHERE DocEntry = @APCreditMemoDocEntry
          AND ISNULL(NoInvtryMv, 'N') = 'Y'
    )
    AND EXISTS
    (
        SELECT 1
        FROM dbo.RPC1
        WHERE DocEntry = @APCreditMemoDocEntry
          AND ISNULL(NoInvtryMv, 'N') <> 'Y'
    )
    BEGIN
        SET @error = 19001
        SET @error_message =
            N'表身「不含數量過帳」必須全部勾選或全部不勾選。'
    END


END

--------------------------------------------------------------------------------------------------------------------------------

-- Select the return values
select @error, @error_message

end
````


**當時回答**

已整理完成，保留完整 SQL 並移除聊天時間與姓名前綴：

[下載「美國正式區.md」](C:\Users\nick_yeh\Documents\維熹卡控\美國正式區.md)

---

<a id="q2"></a>

## S092-Q002｜[2026/8/20 上午 10:14] Nick: USE [WellShin_US_Official] GO / Object: StoredProcedure [dbo].[…

日期：2026-08-20

**你的提問**

[2026/8/20 上午 10:14] Nick: USE [WellShin_US_Official]
GO
/****** Object:  StoredProcedure [dbo].[SBO_SP_PostTransactionNotice]    Script Date: 2026/8/20 上午 10:14:15 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER proc [dbo].[SBO_SP_PostTransactionNotice]

@object_type nvarchar(30),     -- SBO Object Type
@transaction_type nchar(1),   -- [A]dd, [U]pdate, [D]elete, [C]ancel, C[L]ose
@num_of_cols_in_key int,
@list_of_key_cols_tab_del nvarchar(255),
@list_of_cols_val_tab_del nvarchar(255)

AS

begin

-- Return values
declare @error  int    -- Result (0 for no error)
declare @error_message nvarchar (200)   -- Error string to be displayed
select @error = 0
select @error_message = N'Ok'

--------------------------------------------------------------------------------------------------------------------------------

-- ADD YOUR CODE HERE
--Nick新增發票號碼自動帶到表身
/*IF @object_type = '13' AND @transaction_type = 'A'
BEGIN
    EXEC dbo.Cmsp_UpdateInvoiceNo
        @DocEntry = @list_of_cols_val_tab_del;
END*/







-- RMA Auto Generate
IF @object_type IN ('16','14')  -- 16=Sales Return, 14=AR Credit Memo
AND @transaction_type IN ('A','U')
BEGIN

    DECLARE 
        @DocEntry INT,
        @CustomerID NVARCHAR(50),
        @Reason NVARCHAR(50),
        @YYMM NVARCHAR(4),
        @Prefix NVARCHAR(200),
        @MaxSeq INT,
        @NewSeq NVARCHAR(4),
        @NewRMA NVARCHAR(200),
        @DocDate DATE;

    SET @DocEntry = CAST(@list_of_cols_val_tab_del AS INT);

    /* 1. 依單據類型取得資料 */
    IF @object_type = '16'
    BEGIN
        SELECT 
            @CustomerID = T0.CardCode,
            @DocDate    = T0.DocDate
        FROM ORDN T0
        WHERE T0.DocEntry = @DocEntry;

        SELECT TOP 1
            @Reason = ISNULL(T1.U_ReturnReason,'')
        FROM RDN1 T1
        WHERE T1.DocEntry = @DocEntry;
    END

    IF @object_type = '14'
    BEGIN
        SELECT 
            @CustomerID = T0.CardCode,
            @DocDate    = T0.DocDate
        FROM ORIN T0
        WHERE T0.DocEntry = @DocEntry;

        SELECT TOP 1
            @Reason = ISNULL(T1.U_ReturnReason,'')
        FROM RIN1 T1
        WHERE T1.DocEntry = @DocEntry;
    END

    /* 2. 只有指定理由才產生 RMA */
    IF @Reason IN ('RR03','RR04','RR05')
    BEGIN

        /* 3. 產生 YYMM */
        SET @YYMM = RIGHT(CONVERT(VARCHAR(4),YEAR(@DocDate)),2) 
                  + RIGHT('0' + CONVERT(VARCHAR(2),MONTH(@DocDate)),2);

        SET @Prefix = 'RMA-' + @CustomerID + '-' + @Reason + '-' + @YYMM + '-';

        /* 4. 找最大流水號 (兩種單據共用序號) */
        SELECT 
            @MaxSeq = MAX(CAST(RIGHT(U_RMA,4) AS INT))
        FROM (
            SELECT U_RMA FROM ORDN
            UNION ALL
            SELECT U_RMA FROM ORIN
        ) T
        WHERE U_RMA LIKE @Prefix + '%';

        IF @MaxSeq IS NULL
            SET @MaxSeq = 0;

        /* 5. 新流水號 */
        SET @NewSeq = RIGHT('0000' + CAST(@MaxSeq + 1 AS VARCHAR),4);
        SET @NewRMA = @Prefix + @NewSeq;

        /* 6. 更新對應單據 */
        IF @object_type = '16'
        BEGIN
            UPDATE ORDN
            SET U_RMA = @NewRMA
            WHERE DocEntry = @DocEntry;
        END

        IF @object_type = '14'
        BEGIN
            UPDATE ORIN
            SET U_RMA = @NewRMA
            WHERE DocEntry = @DocEntry;
        END

    END
END

--項目主檔建立更新紀錄填入--
if @object_type in('4') and (@transaction_type IN('A','U'))
BEGIN 
update T0
SET 
T0.U_CreateUser = T1.U_NAME,
T0.U_CreateDate = T0.CreateDate,
T0.U_UpdateUser = T2.U_NAME,
T0.U_UpdateDate = T0.UpdateDate
FROM OITM T0
LEFT JOIN OUSR T1 ON T0.UserSign = T1.USERID
LEFT JOIN OUSR T2 ON T0.UserSign2 = T1.USERID
WHERE T0.ItemCode = @list_of_cols_val_tab_del 
END
--項目主檔建立更新紀錄填入--
--業務夥伴主黨建立更新紀錄填入--
if @object_type in('2') and (@transaction_type IN('A','U'))
BEGIN 
update T0
SET 
T0.U_CreateUser = T1.U_NAME,
T0.U_CreateDate = T0.CreateDate,
T0.U_UpdateUser = T2.U_NAME,
T0.U_UpdateDate = T0.UpdateDate
FROM OCRD T0
LEFT JOIN OUSR T1 ON T0.UserSign = T1.USERID
LEFT JOIN OUSR T2 ON T0.UserSign2 = T1.USERID
WHERE T0.CardCode = @list_of_cols_val_tab_del
[2026/8/20 上午 10:14] Nick: END
--業務夥伴主檔建立更新紀錄填入--


--------------------------------------------------------------------------------------------------------------------------------

-- Select the return values
select @error, @error_message

end 


並再一起 但標註 這是不同程序

**當時回答**

已合併至同一份 Markdown，並清楚標註為不同程序：

- 程序一：`SBO_SP_TransactionNotification`
- 程序二：`SBO_SP_PostTransactionNotice`

[開啟「美國正式區.md」](C:\Users\nick_yeh\Documents\維熹卡控\美國正式區.md)

---

<a id="q3"></a>

## S092-Q003｜這個是越南正式區

日期：2026-08-20

**你的提問**

# Files pasted by the user:

## "[2026/8/20 上午 10:19] Nick: USE [WellShin_VN_Official_1] GO /****** Object: Stor…": C:\Users\nick_yeh\.codex/attachments/85225a3b-37a7-448b-ab99-c2a81bd0c4de/pasted-text.txt

## My request:
這個是越南正式區

附加文字內容（原提問附檔，未執行）：

````text
[2026/8/20 上午 10:19] Nick: USE [WellShin_VN_Official_1]
GO
/****** Object:  StoredProcedure [dbo].[SBO_SP_TransactionNotification]    Script Date: 2026/8/20 上午 10:18:56 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER proc [dbo].[SBO_SP_TransactionNotification] 

@object_type nvarchar(30),     -- SBO Object Type
@transaction_type nchar(1),   -- [A]dd, [U]pdate, [D]elete, [C]ancel, C[L]ose
@num_of_cols_in_key int,
@list_of_key_cols_tab_del nvarchar(255),
@list_of_cols_val_tab_del nvarchar(255)

AS

begin

-- Return values
declare @error  int    -- Result (0 for no error)
declare @error_message nvarchar (200)   -- Error string to be displayed
select @error = 0
select @error_message = N'Ok'

--------------------------------------------------------------------------------------------------------------------------------

-- ADD YOUR CODE HERE

----------------------------------------------------------------
-- 銷售訂單 (17) 禁止來源為已過期的報價單
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        INNER JOIN OQUT Q ON Q.DocEntry = L.BaseEntry
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType = '17'         -- 草稿銷售訂單
          AND L.BaseType = '23'        -- 來源為銷售報價
          AND Q.DocDueDate IS NOT NULL
          AND Q.DocDueDate < CAST(GETDATE() AS DATE)
    )
    BEGIN
        SET @error = 1701;
        SET @error_message = N'來源銷售報價已超過有效期限，禁止建立銷售訂單。';
    END
END


----------------------------------------------------------------
-- 規則：
-- 草稿（112）之 銷售報價(23)
-- 表頭 U_DeliveryTerm 不可空白
-- 草稿表：ODRF；文件型別以 ODRF.ObjType 區分
----------------------------------------------------------------

IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM ODRF T0
   WHERE T0.DocEntry = @list_of_cols_val_tab_del 
    AND T0.ObjType IN ('23') 
    AND ISNULL(T0.U_DeliveryTerm, '') = ''    
  )
 BEGIN
  SET @error = '1702'
  SET @error_message = N'運送條件為必填欄位!!'
 END
END

----------------------------------------------------------------
-- 規則：
-- 草稿（112）之 銷售訂單(17) 
-- 表頭 U_DeliveryTerm 、U_Incoterm、TrnspCode不可空白
-- 草稿表：ODRF；文件型別以 ODRF.ObjType 區分
----------------------------------------------------------------

IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM ODRF T0
   WHERE T0.DocEntry = @list_of_cols_val_tab_del 
    AND T0.ObjType IN ('17') 
    AND ( ISNULL(T0.U_DeliveryTerm, '') = '' 
     OR T0.TrnspCode = '-1'
      )
  )
 BEGIN
  SET @error = '1703'
  SET @error_message = N'運送條件、出貨類型為必填欄位!!'
 END
END

----------------------------------------------------------------
-- 規則：
-- 銷售報價單草稿(23)，檢查明細項目是否限定客戶
-- 若項目主檔 U_CardCode 有設定，該項目只能被對應客戶使用
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    DECLARE @QuoteCardCode NVARCHAR(20)
    DECLARE @InvalidItem NVARCHAR(50)

    -- 取得草稿表頭的客戶代號
    SELECT @QuoteCardCode = CardCode
    FROM ODRF
    WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT)

    -- 檢查明細是否有違規項目
    IF EXISTS (
        SELECT 1
        FROM DRF1 L
        INNER JOIN OITM I ON L.ItemCode = I.ItemCode
        WHERE L.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND I.U_CardCode IS NOT NULL
          AND I.U_CardCode <> @QuoteCardCode
    )
    BEGIN
        SET @error = 2301
        SET @error_message = N'明細項目受限於指定客戶，該客戶無法選擇此項目!'
    END
END



----------------------------------------------------------------
-- 庫存調撥草稿 (112)
-- 若調撥目的倉庫為 Hub 倉，且已選擇業務夥伴
-- 則業務夥伴必須是 Hub 客戶
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    IF EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        INNER JOIN OWHS W ON W.WhsCode = L.WhsCode
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType = '67'
[2026/8/20 上午 10:19] Nick: -- 草稿內容是庫存調撥
          AND ISNULL(W.U_IsHubWh,'N') = 'Y'        -- Hub 倉
          AND ISNULL(H.CardCode,'') <> ''         -- 已選業務夥伴
          AND NOT EXISTS (                        -- 但不是 Hub 客戶
                SELECT 1
                FROM OCRD C
                WHERE C.CardCode = H.CardCode
                  AND ISNULL(C.U_IsHubCust,'N') = 'Y'
          )
    )
    BEGIN
        SET @error = 6701
        SET @error_message = N'非 Hub 倉客戶，不能調撥到 Hub 倉。'
    END
END



--運送條件卡控--20260114 Tommy新增
--銷售報價運送條件優先權不可低於原有優先權(數字越大，優先權越低)
if @object_type in('112') and (@transaction_type IN('A','U'))
if EXISTS 
(SELECT 1 FROM ODRF T0 
INNER JOIN [dbo].[@DELIVERY_TERM] T1 ON T0.U_DeliveryTerm = T1.Code
WHERE T0.[DocEntry] = @list_of_cols_val_tab_del AND T0.ObjType IN ('23') and CAST(T1.U_Deli_rank AS INT) > CAST(T0.U_DeliveryRank AS INT))
BEGIN 
       SET @error = '2302'
       SET @error_message = N'運送條件優先權不可低於原有優先權!!!!'
END
--銷售訂單運送條件優先權不可低於原有優先權(數字越大，優先權越低)
if @object_type in('112') and (@transaction_type IN('A','U'))
if EXISTS 
(SELECT 1 FROM ODRF T0 
INNER JOIN [dbo].[@DELIVERY_TERM] T1 ON T0.U_DeliveryTerm = T1.Code
WHERE T0.[DocEntry] = @list_of_cols_val_tab_del AND T0.ObjType IN ('17') and  CAST(T1.U_Deli_rank AS INT) > CAST(T0.U_DeliveryRank AS INT))
BEGIN 
       SET @error = '1704'
       SET @error_message = N'運送條件優先權不可低於原有優先權!!!!'
END


--交貨單運送條件優先權不可低於原有優先權(數字越大，優先權越低)
if @object_type in('15') and (@transaction_type IN('A','U'))
if EXISTS 
(SELECT 1 FROM ODLN T0 
INNER JOIN [dbo].[@DELIVERY_TERM] T1 ON T0.U_DeliveryTerm = T1.Code
WHERE T0.[DocEntry] = @list_of_cols_val_tab_del and  CAST(T1.U_Deli_rank AS INT) > CAST(T0.U_DeliveryRank AS INT))
BEGIN 
       SET @error = '1501'
       SET @error_message = N'運送條件優先權不可低於原有優先權!!!!'
END

----交貨時批次需選擇對應銷售單號批次(批次屬性1)--
IF @object_type IN ('15') AND (@transaction_type IN ('A'))
BEGIN
    IF EXISTS (
        SELECT 1 
        FROM ODLN T0 
        INNER JOIN DLN1 T1 ON T0.DocEntry = T1.DocEntry
        INNER JOIN ORDR T2 ON T1.BaseEntry = T2.DocEntry
        INNER JOIN IBT1 T3 ON T3.BaseEntry = T0.DocEntry AND T3.BaseType = '15'
        INNER JOIN OIBT T4 ON T3.ItemCode = T4.ItemCode AND T3.BatchNum = T4.BatchNum
        WHERE CAST(T0.[DocEntry] AS NVARCHAR(20)) LIKE @list_of_cols_val_tab_del 
          -- Safely compare Integer DocNum to NVARCHAR SuppSerial
          AND CAST(T2.DocNum AS NVARCHAR(20)) != T4.SuppSerial 
    )
    BEGIN 
        SET @error = '1502'
        SET @error_message = N'批次需選擇指定訂單批次!!!!'
    END
END
--交貨時批次需選擇對應銷售單號批次(批次屬性1)--

--401固定資產主檔欄位必填--
if @object_type in('4') and (@transaction_type IN('A','U'))
if EXISTS 
(SELECT 1 FROM OITM T0 
WHERE T0.[ItemCode] LIKE @list_of_cols_val_tab_del 
and T0.ItemType = 'F'
and ((T0.U_AssetSubCategory is null or T0.U_AssetSubCategory = '')
or (T0.Location is null or T0.Location = '')
or (T0.Employee is null or T0.Employee = '')
or (T0.U_AstChangeReason is null or T0.U_AstChangeReason = '')))
BEGIN 
       SET @error = '401'
       SET @error_message = N'固定資產必填欄位未填(資產細類別、存放地點、保管人員、異動原因)!!!!'
END

--固定資產主檔欄位必填--

--交貨時批次需選擇對應銷售單號批次(批次屬性1)--
if @object_type in('15') and (@transaction_type IN('A'))
if EXISTS 
(SELECT 1 FROM ODLN T0 
INNER JOIN DLN1 T1 ON T0.DocEntry = T1.DocEntry
INNER JOIN ORDR T2 ON T1.BaseEntry = T2.DocEntry
INNER JOIN IBT1 T3 ON T3.BaseEntry = T0.DocEntry AND T3.BaseType = '15'
INNER JOIN OIBT T4 ON T3.ItemCode = T4.ItemCode AND T3.BatchNum = T4.BatchNum
WHERE T0.[DocEntry] LIKE @list_of_cols_val_tab_del and T2.DocNum != T4.SuppSerial)
BEGIN 
       SET @error = '1504'
       SET @error_message = N'批次需選擇指定訂單批次!!!!'
END
--交貨時批次需選擇對應銷售單號批次(批次屬性1)--

----有圖面和SOP才能建立工單--
--if @object_type in('202') and (@transaction_type IN('A','U'))
--if EXISTS 
--(SELECT 1 FROM WOR1 T0 
--INNER JOIN OWOR T1 ON T1.DocEntry = T0.DocEntry
--INNER JOIN OITM T2 ON T0.ItemCode = T2.ItemCode
--INNER JOIN OITM T3 ON T1.ItemCode = T3.ItemCode
--WHERE T0.[DocEntry] LIKE @list_of_cols_val_tab_del and (T2.AtcEntry IS NULL or T3.AtcEntry IS NULL))
--BEGIN 
--       SET @error = '20201'
--       SET @error_message =
[2026/8/20 上午 10:19] Nick: N'有圖面和SOP才能建立工單!!!!'
--END
----有圖面和SOP才能建立工單--



--交貨時若信用額度不足則不可出貨--
if @object_type in('15') and (@transaction_type IN('A'))
if EXISTS 
(SELECT 1 FROM ODLN T0 
INNER JOIN OCRD T1 ON T0.CardCode = T1.CardCode
WHERE T0.[DocEntry] LIKE @list_of_cols_val_tab_del 
and T1.[CreditLine] - T1.[OrdersBal] - T1.[DNotesBal] - T1.[Balance] < 0)
BEGIN 
       SET @error = '1505'
       SET @error_message = N'信用額度不足則不可出貨!!!!'
END
--交貨時若信用額度不足則不可出貨--

--付款條件為T000時需訂金收款完成才能交貨--
if @object_type in('15') and (@transaction_type IN('A'))
if EXISTS 
(SELECT 1 FROM ODLN T0 
INNER JOIN DLN1 T1 ON T0.DocEntry = T1.DocEntry
INNER JOIN DPI1 T2 ON T1.BaseEntry = T2.BaseEntry and T2.BaseType = '17'
WHERE T0.[DocEntry] LIKE @list_of_cols_val_tab_del and T0.GroupNum = '1' and T2.LineStatus = 'O')
BEGIN 
       SET @error = '1503'
       SET @error_message = '付款條件為T000時需訂金收款完成才能交貨!!!!'
END
--付款條件為T000時需訂金收款完成才能交貨--

----------------------------------------------------------------
-- 草稿必填卡控：發貨單、收貨單、庫存調撥單
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    -- 宣告用來裝錯誤名稱的變數
    DECLARE @MissingFields NVARCHAR(250) = N'';

    SELECT TOP 1 
        @MissingFields = 
            CASE WHEN ISNULL(H.U_ReqPerson,'') = ''    THEN N'「申請人」' ELSE N'' END +
            CASE WHEN ISNULL(H.U_ReqDept,'') = ''      THEN N'「申請部門」' ELSE N'' END +
            CASE WHEN ISNULL(H.U_ChangeType,'') = ''   THEN N'「異動區分」' ELSE N'' END +
            CASE WHEN ISNULL(H.U_ChangeReason,'') = '' THEN N'「異動原因」' ELSE N'' END +
            CASE WHEN ISNULL(H.U_TypeName,'') = ''     THEN N'「類別名稱」' ELSE N'' END
    FROM ODRF H
    WHERE H.DocEntry = @list_of_cols_val_tab_del
      AND H.ObjType IN ('59','60','67','14','21','20');

    -- 如果有任何一個欄位是空的，@MissingFields 就會有文字
    IF @MissingFields <> N''
    BEGIN
        SET @error = 3001
        SET @error_message = @MissingFields + N' 欄位未填寫，請完整填寫。'
    END
END



--銷售和採購退貨原因欄位必填

IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
        AND
        (
            -- 銷售採購退貨(請求)草稿
            (H.ObjType IN ('16','234000031','21','234000032')
             AND ISNULL(L.ItemCode,'') <> ''
             AND (
                    LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                 OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                 )
            )

            OR

            -- AR AP Credit Memo 草稿
            (H.ObjType IN ('14','19')
             AND ISNULL(L.ItemCode,'') <> ''
             AND ISNULL(L.NoInvtryMv,'N') = 'N'
             AND (
                    LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                 OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                 )
            )        
        )
    )

    BEGIN
        SET @error = 2203
        SET @error_message = N'「退貨理由」、「退貨說明」皆為必填欄位'
    END
END

----------------------------------------------------------------
-- 草稿必填卡控：申請人和申請部門必填
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType IN ('23','17','16','234000031','1470000113','22','234000032','14','18','19','13','203','204','15','540000006') ----Jerry加15,540000006
          AND (
                ISNULL(H.U_ReqPerson,'')   = '' OR
    ISNULL(H.U_ReqDept,'')    = ''
              )
    )
    BEGIN
        SET @error = 3002
        SET @error_message = N'「申請人」、「申請部門」皆為必填欄位'
    END
END

--------------------------------
-- 銷售和採購退貨原因欄位必填 (正式單)
--------------------------------
IF @transaction_type IN ('A','U')
BEGIN

    IF
       -- Sales Return (ORDN)
       (@object_type = '16' AND EXISTS
       (
            SELECT 1
            FROM ORDN H
[2026/8/20 上午 10:19] Nick: INNER JOIN RDN1 L ON H.DocEntry = L.DocEntry
            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
              AND ISNULL(L.ItemCode,'') <> ''
              AND (
                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                  )
       ))

       OR

       -- Sales Return Request (ORRR)
       (@object_type = '234000031' AND EXISTS
       (
            SELECT 1
            FROM ORRR H
            INNER JOIN RRR1 L ON H.DocEntry = L.DocEntry
            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
              AND ISNULL(L.ItemCode,'') <> ''
              AND (
                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                  )
       ))

       OR

       -- Goods Return (ORPD)
       (@object_type = '21' AND EXISTS
       (
            SELECT 1
            FROM ORPD H
            INNER JOIN PDN1 L ON H.DocEntry = L.DocEntry
            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
              AND ISNULL(L.ItemCode,'') <> ''
              AND (
                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                  )
       ))

       OR

       -- Goods Return Request (OPRR)
       (@object_type = '234000032' AND EXISTS
       (
            SELECT 1
            FROM OPRR H
            INNER JOIN PRR1 L ON H.DocEntry = L.DocEntry
            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
              AND ISNULL(L.ItemCode,'') <> ''
              AND (
                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                  )
       ))

       OR

       -- A/R Credit Memo (ORIN)
       (@object_type = '14' AND EXISTS
       (
            SELECT 1
            FROM ORIN H
            INNER JOIN RIN1 L ON H.DocEntry = L.DocEntry
            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
              AND ISNULL(L.ItemCode,'') <> ''
              AND ISNULL(L.NoInvtryMv,'N') = 'N'
              AND (
                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                  )
       ))

       OR

       -- A/P Credit Memo (ORPC)
       (@object_type = '19' AND EXISTS
       (
            SELECT 1
            FROM ORPC H
            INNER JOIN RPC1 L ON H.DocEntry = L.DocEntry
            WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
              AND ISNULL(L.ItemCode,'') <> ''
              AND ISNULL(L.NoInvtryMv,'N') = 'N'
              AND (
                     LEN(LTRIM(RTRIM(ISNULL(L.U_ReturnReason,'')))) = 0
                  OR LEN(LTRIM(RTRIM(ISNULL(CAST(L.U_ReturnRemark AS NVARCHAR(MAX)),'')))) = 0
                  )
       ))

    BEGIN
        SET @error = 2204
        SET @error_message = N'「退貨理由」、「退貨說明」皆為必填欄位'
    END

END

----------------------------------------------------------------
-- 交貨單正式單必填
----------------------------------------------------------------
IF (@object_type = '15' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODLN H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 3003
        SET @error_message = N'「申請人」、「申請部門」皆為必填欄位'
    END

END

----------------------------------------------------------------
-- AR Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '13' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OINV H
        WHERE H.DocEntry =
[2026/8/20 上午 10:19] Nick: @list_of_cols_val_tab_del
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 3004
        SET @error_message = N'「申請人」、「申請部門」皆為必填欄位'
    END

END

----------------------------------------------------------------
-- AR Down Payment Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '203' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODPI H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 20305
        SET @error_message = N'「申請人」、「申請部門」皆為必填欄位'
    END

END

----------------------------------------------------------------
-- AR Credit Memo 正式單必填
----------------------------------------------------------------
IF (@object_type = '14' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ORIN H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
          )
    )
    BEGIN
        SET @error = 1405
        SET @error_message = N'「申請人」、「申請部門」皆為必填欄位'
    END

END

----------------------------------------------------------------
-- AP Credit Memo 正式單必填
----------------------------------------------------------------
IF (@object_type = '19' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ORPC H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 1901
        SET @error_message = N'「申請人」、「申請部門」、「請購類別」、「採購類別」皆為必填欄位'
    END

END


----------------------------------------------------------------
-- AP Down Payment Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '204' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODPO H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 3005
        SET @error_message = N'「申請人」、「申請部門」、「請購類別」、「採購類別」皆為必填欄位'
    END

END


----------------------------------------------------------------
-- AP Invoice 正式單必填
----------------------------------------------------------------
IF (@object_type = '18' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPCH H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 1902
        SET @error_message = N'「申請人」、「申請部門」、「請購類別」、「採購類別」皆為必填欄位'
    END

END

----------------------------------------------------------------
-- 採購報價 正式單必填
----------------------------------------------------------------
IF (@object_type = '540000006' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPQT H
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND (
                ISNULL(H.U_ReqPerson,'') = ''
             OR ISNULL(H.U_ReqDept,'')   = ''
    OR ISNULL(H.U_PRType,'')   = ''
    OR ISNULL(H.U_Purch_Type,'')   = ''
          )
    )
    BEGIN
        SET @error = 540605
        SET @error_message = N'「申請人」、「申請部門」、「請購類別」、「採購類別」皆為必填欄位'
[2026/8/20 上午 10:19] Nick: END
END




----------------------------------------------------------------
-- 草稿必填卡控：採購類型和請購類型必填
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        WHERE H.DocEntry = @list_of_cols_val_tab_del
          AND H.ObjType IN ('1470000113','22','234000032','21','204','19','20','18','540000006') --Jerry有加(540000006)
          AND (
                ISNULL(H.U_PRType,'')   = '' OR
    ISNULL(H.U_Purch_Type,'')    = ''
              )
    )
    BEGIN
        SET @error = 4002
        SET @error_message = N'「請購類別」、「採購類別」皆為必填欄位'
    END
END
--20260720 Rachel 移除 ('13','14','203') AR相關單據

----------------------------------------------------------------
-- 採購訂單草稿
-- Series = 109 時，表身 U_ReqRemark 必填
----------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

    IF EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND H.ObjType = '22'
          AND H.Series = 109
          AND ISNULL(L.ItemCode,'') <> ''
          AND LEN(ISNULL(CAST(L.U_ReqRemark AS NVARCHAR(MAX)),'')) = 0
    )
    BEGIN
        SET @error = 2205
        SET @error_message = N'採購單別為固定資產採購時，表身申請說明為必填'
    END

END

----------------------------------------------------------------
-- 草稿：銷售報價 / 銷售訂單
-- NumAtCard、CntctCode 必填
----------------------------------------------------------------

--20260512 教育訓練中先拿掉
--IF (@object_type = '112' AND @transaction_type IN ('A','U'))
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM ODRF
--        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--          AND ObjType IN ('23','17')
--          AND (
--                ISNULL(NumAtCard,'') = ''
--             OR ISNULL(CntctCode,0) = 0
--          )
--    )
--    BEGIN
--        SET @error = 6003
--        SET @error_message = N'請填寫採購訂單編號與業務代表欄位' -- Jerry有翻譯這裡的錯誤訊息
--    END

--END

------未完成生產發貨投料不可進行生產收貨--
IF @object_type IN ('59') AND @transaction_type IN ('A') -- 生產收貨，新增
BEGIN
    IF EXISTS (
        SELECT 1
        FROM IGN1 T1 
        INNER JOIN OWOR T2 ON T1.BaseEntry = T2.DocEntry AND T1.BaseType = '202'
        WHERE T1.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          -- 檢查這張生產單：是否「所有組件」的已發貨數量加總等於 0
          AND (
              SELECT SUM(ISNULL(W1.IssuedQty, 0))
              FROM WOR1 W1
              WHERE W1.DocEntry = T2.DocEntry
          ) = 0
    )
    BEGIN
        SET @error = '5901'
        SET @error_message = N'該生產訂單尚未進行任何生產發貨，不可進行生產收貨！'
    END
END

----------------------------------------------------------------
-- 規則：
-- 草稿（112）之 銷售訂單(17) & 銷售報價(23)
-- 表頭 是否銅補(U_IsCopperAdd)不可空白
-- 建立時間：20260424
-- 更新時間：
-- 更新人：Tommy
----------------------------------------------------------------

IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM ODRF T0
   WHERE T0.DocEntry = @list_of_cols_val_tab_del 
    AND T0.ObjType IN ('17','23') 
    AND ( ISNULL(T0.U_IsCopperAdd, '') = '' )
  )
 BEGIN
  SET @error = '99991'
  SET @error_message = N'是否銅補為必填欄位!!'
 END
END

----------------------------------------------------------------
-- 規則：
-- 銷售訂單(17) & 銷售報價(23)
-- 表頭 是否銅補(U_IsCopperAdd)不可空白
-- 建立時間：20260424
-- 更新時間：
-- 更新人：Tommy
----------------------------------------------------------------

IF (@object_type = '17' AND @transaction_type IN ('A','U'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM ORDR T0
   WHERE T0.DocEntry = @list_of_cols_val_tab_del 
    AND ( ISNULL(T0.U_IsCopperAdd, '') = '' )
  )
 BEGIN
  SET @error = '99992'
  SET @error_message = N'是否銅補為必填欄位!!'
 END
END

IF (@object_type = '23' AND @transaction_type IN ('A','U'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM OQUT T0
   WHERE T0.DocEntry = @list_of_cols_val_tab_del 
    AND ( ISNULL(T0.U_IsCopperAdd, '') = '' )
  )
 BEGIN
  SET @error = '99993'
  SET @error_message =
[2026/8/20 上午 10:19] Nick: N'是否銅補為必填欄位!!'
 END
END

----------------------------------------------------------------
-- 規則：
-- 草稿（112）之 銷售訂單(17) & 銷售報價(23)
-- 表頭 是否銅補為是(U_IsCopperAdd)，合約(U_ContractNo)、鎖銅(U_LockCopper)不可空白
-- 建立時間：20260424
-- 更新時間：
-- 更新人：Tommy
----------------------------------------------------------------

IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    IF EXISTS (
        SELECT 1 
        FROM ODRF T0
        WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
            AND T0.ObjType IN ('17','23') 
            AND T0.U_IsCopperAdd = 'Y'
            AND (
                ISNULL(CAST(T0.U_ContractNo AS NVARCHAR(MAX)), '') = ''
                OR ISNULL(CAST(T0.U_LockCopper AS NVARCHAR(MAX)), '') = ''
            )
    )
    BEGIN
        SET @error = '99994'
        SET @error_message = N'是否銅補為是，合約與鎖銅必填欄位!!'
    END
END

----------------------------------------------------------------
-- 規則：
--  銷售訂單(17) & 銷售報價(23)
-- 表頭 是否銅補為是(U_IsCopperAdd)，合約(U_ContractNo)、鎖銅(U_LockCopper)不可空白
-- 建立時間：20260424
-- 更新時間：
-- 更新人：Tommy
----------------------------------------------------------------

IF (@object_type = '17' AND @transaction_type IN ('A','U'))
BEGIN
 IF EXISTS (
   SELECT 1 FROM ORDR T0
   WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
    AND T0.U_IsCopperAdd = 'Y'
                AND (
                ISNULL(CAST(T0.U_ContractNo AS NVARCHAR(MAX)), '') = ''
                OR ISNULL(CAST(T0.U_LockCopper AS NVARCHAR(MAX)), '') = ''
            )
    )
    BEGIN
        SET @error = '99995'
        SET @error_message = N'是否銅補為是，合約與鎖銅必填欄位!!'
    END
END
IF (@object_type = '23' AND @transaction_type IN ('A','U'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM OQUT T0
   WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
    AND T0.U_IsCopperAdd = 'Y'
                AND (
                ISNULL(CAST(T0.U_ContractNo AS NVARCHAR(MAX)), '') = ''
                OR ISNULL(CAST(T0.U_LockCopper AS NVARCHAR(MAX)), '') = ''
            )
    )
    BEGIN
        SET @error = '99996'
        SET @error_message = N'是否銅補為是，合約與鎖銅必填欄位!!'
    END
END



----------------------------------------------------------------
-- 規則：
-- 報廢(1470000094)
-- 抵減(U_InvestDiscount)、抵押(U_PledgeType)、保險期間内(U_InsurStartDate)(U_InsurEndDate)不能進行處分
-- 建立時間：20260424
-- 更新時間：
-- 更新人：Tommy
----------------------------------------------------------------

IF (@object_type = '1470000094' AND @transaction_type IN ('A'))
BEGIN

 IF EXISTS (
   SELECT 1 FROM ORTI T0
            INNER JOIN RTI1 T1 ON T0.DocEntry = T1.DocEntry
            INNER jOIN OITM T2 ON T1.ItemCode = T2.ItemCode
   WHERE T0.DocEntry = @list_of_cols_val_tab_del 
            and (T2.U_InvestDiscount is not null or T2.U_InvestDiscount != '')
            and (T2.U_PledgeType is not null or T2.U_PledgeType != '')
            and (T0.DocDate >= T2.U_InsurStartDate and T0.DocDate <= T2.U_InsurEndDate)
  )
 BEGIN
  SET @error = '99997'
  SET @error_message = N'抵減、抵押、保險期間内不能進行處分!!'
 END
END


------------------------------------------------------------------
---- 生產發貨數量不可大於生產單剩餘數量
------------------------------------------------------------------
--IF (@object_type = '60' AND @transaction_type IN ('A','U'))
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM IGE1 T0
--        INNER JOIN OWOR W 
--            ON W.DocEntry = T0.BaseEntry
--        WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--          AND T0.BaseType = 202
--          AND T0.BaseEntry IS NOT NULL
--          AND (
--                T0.Quantity >
--                (ISNULL(W.PlannedQty,0) - ISNULL(W.CmpltQty,0))
--              )
--    )
--    BEGIN
--        SET @error = 5501
--        SET @error_message = N'生產發貨數量不可大於生產訂單剩餘數量'
--        RETURN
--    END

--END

--IF (@object_type = '60' AND @transaction_type IN ('A','U'))
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM (
--            -- 1. 先計算「本次發貨單」中，每個生產單組件的加總數量
--            SELECT BaseEntry, BaseLine, SUM(Quantity) AS ThisDocQty
--            FROM IGE1
--            WHERE DocEntry = CAST(@list_of_cols_val_tab_del
[2026/8/20 上午 10:19] Nick: AS INT)
--              AND BaseType = 202
--              AND BaseEntry IS NOT NULL
--            GROUP BY BaseEntry, BaseLine
--        ) T0
--        -- 2. 關聯生產單組件計劃量
--        INNER JOIN WOR1 W1 ON W1.DocEntry = T0.BaseEntry AND W1.LineNum = T0.BaseLine
--        WHERE 
--            -- 3. 比對邏輯：(本次發貨量) + (這張單據以外的歷史已發貨總量) > 計劃量
--            T0.ThisDocQty + ISNULL((
--                SELECT SUM(A.Quantity)
--                FROM IGE1 A
--                WHERE A.BaseType = 202
--                  AND A.BaseEntry = T0.BaseEntry
--                  AND A.BaseLine = T0.BaseLine
--                  AND A.DocEntry <> CAST(@list_of_cols_val_tab_del AS INT) -- 排除本次單據，避免重複計算
--            ), 0) > ISNULL(W1.PlannedQty, 0)
--    )
--    BEGIN
--        SET @error = 5501
--        SET @error_message = N'生產發貨數量不可大於生產訂單剩餘未發貨數量！'
--    END

--END


--IF (@object_type = '60' AND @transaction_type IN ('A','U'))
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM (
--            -- 1. 先計算「本次發貨單」中，每個生產單組件的加總數量
--            SELECT BaseEntry, BaseLine, SUM(Quantity) AS ThisDocQty
--            FROM IGE1
--            WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--              AND BaseType = 202
--              AND BaseEntry IS NOT NULL
--            GROUP BY BaseEntry, BaseLine
--        ) T0
--        -- 2. 關聯生產單組件計劃量
--        INNER JOIN WOR1 W1 ON W1.DocEntry = T0.BaseEntry AND W1.LineNum = T0.BaseLine
--        WHERE 
--            -- 【修改重點】如果該組件在歷史上就已經發滿了，而本次完全是「額外手動補發」的，則不卡控。
--            -- 換句話說：只有當「歷史已發貨量 < 計劃量」時，代表它還在走正常扣料流程，才需要卡控不能超發。
--            ISNULL((
--                SELECT SUM(A.Quantity)
--                FROM IGE1 A
--                WHERE A.BaseType = 202
--                  AND A.BaseEntry = T0.BaseEntry
--                  AND A.BaseLine = T0.BaseLine
--                  AND A.DocEntry <> CAST(@list_of_cols_val_tab_del AS INT)
--            ), 0) < ISNULL(W1.PlannedQty, 0) -- 歷史發貨還沒滿，才進入卡控邏輯
            
--            AND
            
--            -- 3. 比對邏輯：(本次發貨量) + (這張單據以外的歷史已發貨總量) > 計劃量
--            (T0.ThisDocQty + ISNULL((
--                SELECT SUM(A.Quantity)
--                FROM IGE1 A
--                WHERE A.BaseType = 202
--                  AND A.BaseEntry = T0.BaseEntry
--                  AND A.BaseLine = T0.BaseLine
--                  AND A.DocEntry <> CAST(@list_of_cols_val_tab_del AS INT)
--            ), 0) > ISNULL(W1.PlannedQty, 0))
--    )
--    BEGIN
--        SET @error = 5501
--        SET @error_message = N'生產發貨數量不可大於生產訂單剩餘未發貨數量！(補發除外)'
--    END

--END
--Rachel 20260708 先修改新版


IF @object_type = '60'
AND @transaction_type IN ('A','U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM IGE1 L
        INNER JOIN WOR1 W
            ON W.DocEntry = L.BaseEntry
           AND W.LineNum  = L.BaseLine
        WHERE L.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND L.BaseType = 202
          AND (
                ISNULL(W.IssuedQty,0)
               -- + ISNULL(L.Quantity,0)
              ) > ISNULL(W.PlannedQty,0)
    )
    BEGIN
        SET @error = 5501
        SET @error_message = N'本次發貨後，原料累計發貨數量不可大於生產訂單計劃用量。'
    END

END
--Rachel 20260708 修改新版

------------------------------------------------------------------
---- 銷售報價：建單人部門需等於銷售員部門
------------------------------------------------------------------
--IF (@object_type = '23' AND @transaction_type IN ('A','U'))
--BEGIN

--    IF EXISTS (
--        SELECT 1
--        FROM OQUT Q
--        INNER JOIN OUSR U ON U.USERID = Q.UserSign
--        INNER JOIN OHEM E1 ON E1.userId = U.USERID
--        INNER JOIN OSLP S ON S.SlpCode = Q.SlpCode
--        INNER JOIN OHEM E2 ON E2.salesPrson = S.SlpCode
--        WHERE Q.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
--          AND ISNULL(E1.dept,'') <> ISNULL(E2.dept,'')
--    )
--    BEGIN
--        SET @error = 7001
--        SET @error_message = N'建單人部門需與銷售員部門一致'
--        RETURN
--
[2026/8/20 上午 10:19] Nick: END

--END






------------------------------------------------------------------------------------------------------------------
--Sales-Module-銷售模組-採購模組-存貨模組-草稿表頭(ODRF)-表身(DRF1)
--1.「文件類型」-「服務(S)」-「單位(U_Unit)」欄位設定為必填項目
--2.「文件類型」-「項目(I)|服務(S)」-「申請說明(U_ReqRemark)」欄位設定為必填項目
---------------------------------------------------------------------------------------------------------
-- [ 銷售模組 - Sales Module ]
-- 1. Sales Quotation - 銷售報價 - 23 - OQUT
-- 2. Sales Order - 銷售訂單 - 17 - ORDR
-- 3. Delivery - 交貨 - 15 - ODLN (註：原清單未列，常與退貨搭配)
-- 4. Return - 退貨 - 16 - ORDN
-- 5. A/R Invoice - A/R 發票 - 13 - OINV (註：原清單未列，常與貸項通知單搭配)
-- 6. A/R Credit Memo - A/R 貸項通知單 - 14 - ORIN
-- 7. A/R Down Payment Invoice - A/R 預付款發票 - 204 - ODPI

-- [ 採購模組 - Purchase Module ]
-- 8. Purchase Order - 採購訂單 - 22 - OPOR
-- 9. Goods Receipt PO - 收貨採購單 - 20 - OPDN
-- 10. Goods Return - 採購退貨 - 21 - ORPD
-- 11. A/P Invoice - A/P 發票 - 18 - OPCH
-- 12. A/P Credit Memo - A/P 貸項通知單 - 19 - ORPC

-- [ 庫存 / 生產 / 平台模組 - Inventory & Production & Platform ]
-- 13. Goods Receipt - 收貨 - 59 - OIGN
-- 14. Goods Issue - 發料 - 60 - OIGE
-- 15. Inventory Counting - 庫存盤點 - 1470000065 / 147 - OINC
-- 16. Purchase Request - 採購申請 - 1470000113 - OPRQ
-- 17. Return Request - 退貨請求 - 234000031 - ORRD
-- 18. Goods Return Request - 採購退貨請求 - 234000032 - ORRD
-- 19. Blanket Agreement - 總體合約 - 1250000025 (註：540000006/540000005 為採購/銷售總體合約之子類型)
-- 20. Production Order - 生產訂單 - 202 - OWOR (註：234000004 為其相關變形或工作單類型)
---------------------------------------------------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    -- 宣告用來裝錯誤名稱的變數
    DECLARE @MissingFieldsSO NVARCHAR(250) = N'';

    -- 同時關聯表頭(ODRF)與表身(DRF1)進行檢查
    SELECT TOP 1 
        @MissingFieldsSO = 
            -- 條件 1：當 DocType = 'S' (服務) 且 單位(U_Unit) 為空
            -- 預防萬一，將 U_Unit 轉換為 NVARCHAR 處理
            CASE WHEN H.DocType = 'S' AND ISNULL(CAST(D.U_Unit AS NVARCHAR(100)), '') = '' 
                 THEN N'「單位」' ELSE N'' END +
            
            -- 條件 2：當 DocType 為 'I' 或 'S' 且 申請說明(U_ReqRemark) 為空
            -- 使用 CAST 解決 ntext 欄位無法直接與 '' 進行 = 比較的問題
            CASE WHEN H.DocType IN ('I', 'S') AND ISNULL(CAST(D.U_ReqRemark AS NVARCHAR(MAX)), '') = '' 
                 THEN N'「申請說明」' ELSE N'' END
    FROM ODRF H
    INNER JOIN DRF1 D ON H.DocEntry = D.DocEntry
    WHERE H.DocEntry = @list_of_cols_val_tab_del
      AND H.ObjType IN ('14','16','17','19','20','21','22','23','203','204','1470000113','234000031','234000032','147','540000006','540000005','234000004','204','18','59','60'); 

--Jerry(這裡有多19,20,21,22)已刪除

    -- 如果有缺欄位，拋出錯誤訊息
    IF @MissingFieldsSO <> N''
    BEGIN
        SET @error = 3006
        SET @error_message = @MissingFieldsSO + N' 欄位未填寫，請完整填寫。'
    END
END


--------------------------------------------------------------------------------------------------------------------------------------
-- Sales-Module-銷售模組-正式單必填欄位 
-- 1.「申請人(U_ReqPerson)」、「申請部門(U_ReqDept)」、「運輸條件(U_Incoterm)」-欄位設定為必填項目
-- 1. Sales Quotation - 銷售報價 - 23 - OQUT
-- 2. Sales Order - 銷售訂單 - 17 - ORDR
-- 3. Delivery - 交貨 - 15 - ODLN
-- 4. Return Request - 銷售退貨請求 - 231000000 - ORRR
-- 5. Returns - 銷售退貨 - 16 - ORDN
-- 6. A/R Down Payment Request - A/R訂金請求 - 203 - ODPI
-- 7. A/R Down Payment Invoice - A/R訂金發票 - 203 - ODPI
-- 8. A/R Invoice - A/R發票 - 13 - OINV
-- 9. A/R Invoice - A/R發票+付款 - 13 - OINV
-- 10. A/R Credit Memo - 應收貸項通知單 - 14 - ORIN
-- 11. A/R Invoice + Payment - 應收發票和付款 - 13 - OINV
--------------------------------------------------------------------------------------------------------------------------------------
IF @transaction_type IN ('A', 'U')
BEGIN
    -- 使用分支判斷，雖然代碼較長，但執行效率最高且最安全
    IF (@object_type = '23' AND EXISTS (SELECT 1 FROM OQUT WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = ''))) OR
       (@object_type = '17' AND EXISTS (SELECT 1 FROM ORDR WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND
[2026/8/20 上午 10:19] Nick: (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = ''))) OR
       (@object_type = '15' AND EXISTS (SELECT 1 FROM ODLN WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = ''))) OR
       (@object_type = '231000000' AND EXISTS (SELECT 1 FROM ORRR WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = ''))) OR
       (@object_type = '16' AND EXISTS (SELECT 1 FROM ORDN WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = ''))) OR
       (@object_type = '203' AND EXISTS (SELECT 1 FROM ODPI WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = ''))) OR
       (@object_type = '14' AND EXISTS (SELECT 1 FROM ORIN WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = ''))) OR
       (@object_type = '13' AND EXISTS (SELECT 1 FROM OINV WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' OR ISNULL(U_DeliveryTerm,'') = '')))     
       --OR (@object_type = '202' AND EXISTS (SELECT 1 FROM OWOR WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND ISNULL(U_ReqDept,'') = '' )))
      -- 依此類推加入 OOAT, ORRR, ORDN, ODPI, ORIN...
    BEGIN
        SET @error = 1903
        SET @error_message = N'申請人、申請部門、運送條件欄位必填'
    END
END

--------------------------------------------------------------------------------------------------------------------------------------
-- Sales-Module-採購模組-正式單必填欄位 
-- 1.「申請人(U_ReqPerson)」、「申請部門(U_ReqDept)」、「運輸條件(U_Incoterm)」-欄位設定為必填項目
-- 1. Purchase Request - 採購請求 - 540000006 - OPRQ
-- 2. Purchase Quotation - 採購報價 - 540000005 - OPQT
-- 3. Purchase Order - 採購單 - 22 - OPOR
-- 4. Goods Receipt PO - 收貨採購訂單 - 20 - OPDN
-- 5. Goods Return Request - 採購退貨請求 - 234000004 - OPRR
-- 6. Goods Return - 採購退貨 - 21 - ORPD
-- 7. A/P Down Payment Invoice - A/P訂金請求 - 204 - ODPO
-- 8. A/P Down Payment Invoice - A/P訂金發票 - 204 - ODPO
-- 9. A/P Invoice - A/P發票 - 18 - OPCH
-- 10. A/P Credit Memo - A/P貨項通知單 - 19 - ORPC
-- 11. A/P Invoice - A/P預開發票 - 18 - OPCH
--------------------------------------------------------------------------------------------------------------------------------------
IF @transaction_type IN ('A', 'U')
BEGIN
    -- 使用分支判斷，雖然代碼較長，但執行效率最高且最安全
    IF (@object_type = '22' AND EXISTS (SELECT 1 FROM OPOR WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) OR
       (@object_type = '20' AND EXISTS (SELECT 1 FROM OPDN WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) OR
       (@object_type = '234000004' AND EXISTS (SELECT 1 FROM OPRR WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = ''))) OR
       (@object_type = '21' AND EXISTS (SELECT 1 FROM ORPD WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) OR
       (@object_type = '204' AND EXISTS (SELECT 1 FROM ODPO WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) OR
       (@object_type = '18' AND EXISTS (SELECT 1 FROM OPCH WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) OR
       (@object_type = '19' AND EXISTS (SELECT 1 FROM ORPC WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND
[2026/8/20 上午 10:19] Nick: (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) OR
       (@object_type = '1470000113' AND EXISTS (SELECT 1 FROM OPRQ WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) OR
       (@object_type = '540000005' AND EXISTS (SELECT 1 FROM OPQT WITH (NOLOCK) WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT) AND (ISNULL(U_ReqPerson,'') = '' OR ISNULL(U_ReqDept,'') = '' ))) 
    
           --Jerry(刪除了多的204，加了147000013,540000005)

    -- 依此類推加入 OOAT, ORRR, ORDN, ODPI, ORIN... 

    BEGIN
        SET @error = 1904
        SET @error_message = N'申請人、 申請部門欄位必填'
    END
END


---------------------------------------------------------------------------------------------------------
-- 檢查機制：同一張「銷售報價單」所有項目料號之「成本毛利」欄位 (U_GrossMarginPct)
-- 卡控邏輯：所有項目必須位於同一個毛利區間內，不可跨區間輸入！
-- 1. [ 5% 區間內 ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
-- 2. [ 5%-10%區間 ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
-- 3. [10%-20%區間 ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
-- 4. [ 20% 以上  ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
---------------------------------------------------------------------------------------------------------
IF (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    -- 宣告用來記錄這張單據橫跨了幾個毛利區間的變數
    DECLARE @GroupCount INT = 0;

    -- 計算這張單據中，所有「項目類型」的料號共分佈在幾個不同的毛利區間
    SELECT @GroupCount = COUNT(DISTINCT 
        CASE 
            WHEN ISNULL(D.U_GrossMarginPct, 0) <= 5 THEN 1                -- 區間 1: 5%以內
            WHEN ISNULL(D.U_GrossMarginPct, 0) > 5 AND ISNULL(D.U_GrossMarginPct, 0) <= 10 THEN 2  -- 區間 2: 5-10%
            WHEN ISNULL(D.U_GrossMarginPct, 0) > 10 AND ISNULL(D.U_GrossMarginPct, 0) <= 20 THEN 3 -- 區間 3: 10-20%
            WHEN ISNULL(D.U_GrossMarginPct, 0) > 20 THEN 4               -- 區間 4: 20%以上
        END
    )
    FROM ODRF H
    INNER JOIN DRF1 D ON H.DocEntry = D.DocEntry
    WHERE H.DocEntry = @list_of_cols_val_tab_del
      AND H.ObjType = '23'          -- 銷售報價單草稿
      AND H.DocType = 'I';          -- 僅針對「項目」類型的報價單檢查

    -- 如果橫跨的區間數量大於 1 個（例如同時存在 3%[區間1] 與 33%[區間4]）
    IF @GroupCount > 1
    BEGIN
        SET @error = 3007;
        SET @error_message = N'同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入！';
    END
END



--------------------------------------------------------------------------------------------------------------------------------------
-- Sales-Module-銷售模組-正式單必填欄位 
-- 1. Sales Quotation - 銷售報價 - 23 - OQUT
--------------------------------------------------------------------------------------------------------------------------------------
-- 檢查機制：同一張「銷售報價單」所有項目料號之「成本毛利」欄位 (U_GrossMarginPct)
-- 卡控邏輯：所有項目必須位於同一個毛利區間內，不可跨區間輸入！
-- 1. [ 5% 區間內 ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
-- 2. [ 5%-10%區間 ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
-- 3. [10%-20%區間 ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
-- 4. [ 20% 以上  ] - 同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入
---------------------------------------------------------------------------------------------------------
IF (@object_type = '23' AND @transaction_type IN ('A', 'U'))
BEGIN
    IF EXISTS (
        SELECT 1 
        FROM QUT1 D WITH (NOLOCK)
        INNER JOIN OQUT H WITH (NOLOCK) ON H.DocEntry = D.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND H.DocType = 'I' -- 僅針對「項目」類型的單據檢查
        GROUP BY D.DocEntry
        -- 核心逻辑：利用 COUNT(DISTINCT ...) 計算這張單據裡總共橫跨了幾個毛利區間
        HAVING COUNT(DISTINCT 
            CASE 
                WHEN ISNULL(D.U_GrossMarginPct, 0) <= 5 THEN 1
                WHEN ISNULL(D.U_GrossMarginPct, 0) > 5 AND ISNULL(D.U_GrossMarginPct, 0) <= 10 THEN 2
                WHEN ISNULL(D.U_GrossMarginPct, 0) > 10 AND ISNULL(D.U_GrossMarginPct, 0) <= 20 THEN 3
                WHEN ISNULL(D.U_GrossMarginPct, 0) > 20 THEN 4
            END
        ) > 1 -- 如果區間數量大於 1，代表有跨區間輸入
    )
    BEGIN
        SET @error = 1905;
        SET @error_message = N'【系統卡控】同一張銷售報價單內的所有項目，成本毛利必須在同一個區間內，不可跨區間輸入！';
[2026/8/20 上午 10:19] Nick: END
END


---------------------------------------------------------------------------------------------------------
-- 檢查機制：同一張「採購報價單」所有項目料號之「上次採購價格」欄位 (實為 U_LastPurPrice)
-- 卡控邏輯：同一張採購報價單內的所有項目號碼，其「上次採購價格」欄位如果不全為 0（即存在大於 0 或小於 0 的紀錄），則予以卡控！
---------------------------------------------------------------------------------------------------------
IF (@object_type = '540000006' AND @transaction_type IN ('A', 'U'))
BEGIN
    -- 檢查是否存在任何一個項目，其上次採購價格不等於 0
    IF EXISTS (
        SELECT 1 
        FROM PQT1 D WITH (NOLOCK)
        INNER JOIN OPQT H WITH (NOLOCK) ON H.DocEntry = D.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND H.DocType = 'I'                      -- 僅針對「項目」類型的單據檢查
          AND ISNULL(D.U_VsLastPurPri, 0) <> 0     -- 核心條件：只要有任何一筆不等於 0 就抓出來
    )
    BEGIN
        SET @error = 1907;
        SET @error_message = N'【系統卡控】同一張採購報價單內的所有項目號碼，其「與上次採購價格的差別」必須無差異，不然請開立新「採購報價單」！!';
    END
END





--卡控銷售報價和銷售訂單，單頭的業務夥伴幣別和表身的幣別要一致

----------------------------------------------------------------
-- 銷售報價單 & 銷售報價草稿：單頭幣別與表身幣別需一致
----------------------------------------------------------------
IF (@object_type = '23' AND @transaction_type IN ('A','U')) 
   OR (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    -- 1. 正式銷售報價單卡控 (Object 23)
    IF @object_type = '23' AND EXISTS (
        SELECT 1
        FROM OQUT H
        INNER JOIN QUT1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND ISNULL(L.Currency,'') <> ''
          AND H.DocCur <> L.Currency
    )
    BEGIN
        SET @error = 2303
        SET @error_message = N'銷售報價單頭幣別與表身幣別需一致'
    END

    -- 2. 銷售報價草稿卡控 (Object 112 + ObjType = '23')
    IF @object_type = '112' AND EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND H.ObjType = '23' -- 修正：確保此草稿是銷售報價單
          AND ISNULL(L.Currency,'') <> ''
          AND H.DocCur <> L.Currency
    )
    BEGIN
        SET @error = 2304
        SET @error_message = N'銷售報價草稿單頭幣別與表身幣別需一致'
    END
END

----------------------------------------------------------------
-- 銷售訂單 & 銷售訂單草稿：單頭幣別與表身幣別需一致
----------------------------------------------------------------
IF (@object_type = '17' AND @transaction_type IN ('A','U')) 
   OR (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    -- 1. 正式銷售訂單卡控 (Object 17)
    IF @object_type = '17' AND EXISTS (
        SELECT 1
        FROM ORDR H
        INNER JOIN RDR1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND ISNULL(L.Currency,'') <> ''
          AND H.DocCur <> L.Currency
    )
    BEGIN
        SET @error = 1705
        SET @error_message = N'銷售訂單單頭幣別與表身幣別需一致'
    END

    -- 2. 銷售訂單草稿卡控 (Object 112 + ObjType = '17')
    IF @object_type = '112' AND EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND H.ObjType = '17' -- 修正：確保此草稿是銷售訂單
          AND ISNULL(L.Currency,'') <> ''
          AND H.DocCur <> L.Currency
    )
    BEGIN
        SET @error = 1706
        SET @error_message = N'銷售訂單草稿單頭幣別與表身幣別需一致'
    END
END

----------------------------------------------------------------
-- 銷售訂單 & 銷售訂單草稿：單價不可低於來源銷售報價單單價
----------------------------------------------------------------
IF (@object_type = '17' AND @transaction_type IN ('A','U')) 
   OR (@object_type = '112' AND @transaction_type IN ('A','U'))
BEGIN
    -- 1. 正式銷售訂單卡控 (Object 17)
    IF @object_type = '17' AND EXISTS (
        SELECT 1
        FROM RDR1 L
        -- 關聯來源銷售報價單的明細表
        INNER JOIN QUT1 Q ON L.BaseEntry = Q.DocEntry 
                         AND L.BaseLine = Q.LineNum 
                         AND L.BaseType = '23'
        WHERE L.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          -- 比對價格：訂單單價 < 報價單單價
          AND ISNULL(L.Price, 0) < ISNULL(Q.Price, 0)
    )
    BEGIN
[2026/8/20 上午 10:19] Nick: SET @error = 1707
        SET @error_message = N'銷售訂單物料單價不可低於原銷售報價單單價！'
    END

    -- 2. 銷售訂單草稿卡控 (Object 112 + ObjType = '17')
    IF @object_type = '112' AND EXISTS (
        SELECT 1
        FROM ODRF H
        INNER JOIN DRF1 L ON H.DocEntry = L.DocEntry
        -- 關聯來源銷售報價單的明細表
        INNER JOIN QUT1 Q ON L.BaseEntry = Q.DocEntry 
                         AND L.BaseLine = Q.LineNum 
                         AND L.BaseType = '23'
        WHERE H.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND H.ObjType = '17' -- 確保此草稿是銷售訂單
          -- 比對價格：訂單草稿單價 < 報價單單價
          AND ISNULL(L.Price, 0) < ISNULL(Q.Price, 0)
    )
    BEGIN
        SET @error = 1708
        SET @error_message = N'銷售訂單草稿物料單價不可低於原銷售報價單單價！'
    END



END


------------------------------------------------------------------
---- 項目主檔(非固定資產) 必填欄位檢查
------------------------------------------------------------------
--IF (@object_type = '4' AND @transaction_type IN ('A','U'))
--BEGIN

--    DECLARE @MissingFields2 NVARCHAR(MAX) = N'';

--    SELECT
--        @MissingFields2 =
--            CASE WHEN ISNULL(T0.U_CardCode,'')      = '' THEN N'「客戶代號」、'        ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_DrawVer,'')       = '' THEN N'「圖號版本」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_ReqDept,'')       = '' THEN N'「申請部門」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_ItemCatName,'')   = '' THEN N'「產品類別名稱」、'     ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_ReqPlant,'')      = '' THEN N'「申請廠別」、'        ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_CustItemNo,'')    = '' THEN N'「客人料號」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_OriginPlant,'')   = '' THEN N'「生產產地」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_ItemStatus,'')    = '' THEN N'「料號狀態」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_HSCode,'')        = '' THEN N'「海關編碼」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_HSName,'')        = '' THEN N'「海關商品名稱」、'      ELSE N'' END +
--            CASE WHEN T0.U_AvgCost IS NULL          THEN N'「加權平均單價」、'         ELSE N'' END +
--            CASE WHEN T0.U_LastPOPrice IS NULL      THEN N'「最近採購單價」、'         ELSE N'' END +
--            CASE WHEN T0.U_FGLeadTime IS NULL       THEN N'「成品交期」、'             ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_BondType,'')      = '' THEN N'「保稅別」、'           ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_PlannedOrder,'')  = '' THEN N'「計劃訂購」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_OrderPolicy,'')   = '' THEN N'「訂購策略」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_QtyPolicy,'')     = '' THEN N'「數量政策」、'         ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_PackMethod,'')    = '' THEN N'「包裝方式」、'         ELSE N'' END +
--            CASE WHEN T0.U_Decimals IS NULL         THEN N'「小數位數」、'             ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_AppStatus,'')     = '' THEN N'「承認完成狀態」、'     ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_KeyItem,'')       = '' THEN N'「關鍵料號」、'         ELSE N'' END +
--            CASE WHEN T0.U_CuNetWt IS NULL          THEN N'「銅淨重」、'               ELSE N'' END +
--            CASE WHEN T0.U_UnitNetWt IS NULL        THEN N'「單位淨重」、'             ELSE N'' END +
--            CASE WHEN ISNULL(T0.U_SeriesType,'')    = '' THEN N'「系列別」、'           ELSE N'' END

--    FROM OITM T0
--    WHERE T0.ItemCode = @list_of_cols_val_tab_del
--      AND ISNULL(T0.AssetItem,'N') = 'N'
--      -- 只有啟用項目才檢查
--      AND ISNULL(T0.FrozenFor,'N') = 'N';


--    IF @MissingFields2 <> N''
--    BEGIN
--        SET @error = 4001;
--        SET @error_message =
--            LEFT(@MissingFields2, LEN(@MissingFields2)-1)
--            + N' 欄位未填寫，請完整填寫。';
--    END

--END
--Rachel 20260730 已更新為啟用時才卡控

----------------------------------------------------------------
-- 正式單必填卡控：發貨單、收貨採購、AR貸項、AP貸項、
--              庫存收貨、庫存發貨、庫存調撥
----------------------------------------------------------------
IF @transaction_type IN ('A','U')
BEGIN

    DECLARE
[2026/8/20 上午 10:19] Nick: @MissingFields3 NVARCHAR(250) = N'';

    ----------------------------------------------------------------
    -- Delivery (ODLN)
    ----------------------------------------------------------------
    IF @object_type = '15'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「申請人」' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「申請部門」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「異動區分」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「異動原因」' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「類別名稱」' ELSE N'' END
        FROM ODLN
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Goods Receipt PO (OPDN)
    ----------------------------------------------------------------
    ELSE IF @object_type = '20'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「申請人」' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「申請部門」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「異動區分」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「異動原因」' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「類別名稱」' ELSE N'' END
        FROM OPDN
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Goods Return (ORPD)
    ----------------------------------------------------------------
    ELSE IF @object_type = '21'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「申請人」' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「申請部門」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「異動區分」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「異動原因」' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「類別名稱」' ELSE N'' END
        FROM ORPD
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- A/R Credit Memo (ORIN)
    ----------------------------------------------------------------
    ELSE IF @object_type = '14'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「申請人」' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「申請部門」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「異動區分」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「異動原因」' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「類別名稱」' ELSE N'' END
        FROM ORIN
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Goods Receipt (OIGN)
    ----------------------------------------------------------------
    ELSE IF @object_type = '59'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「申請人」' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「申請部門」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「異動區分」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「異動原因」' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「類別名稱」' ELSE N'' END
        FROM OIGN
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Goods Issue (OIGE)
    ----------------------------------------------------------------
    ELSE IF @object_type = '60'
    BEGIN
[2026/8/20 上午 10:19] Nick: SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「申請人」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「申請部門」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「異動區分」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「異動原因」 ' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「類別名稱」 ' ELSE N'' END
        FROM OIGE
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Inventory Transfer (OWTR)
    ----------------------------------------------------------------
    ELSE IF @object_type = '67'
    BEGIN
        SELECT
            @MissingFields3 =
                  CASE WHEN ISNULL(U_ReqPerson,'')=''    THEN N'「申請人」' ELSE N'' END
                + CASE WHEN ISNULL(U_ReqDept,'')=''      THEN N'「申請部門」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeType,'')=''   THEN N'「異動區分」' ELSE N'' END
                + CASE WHEN ISNULL(U_ChangeReason,'')='' THEN N'「異動原因」' ELSE N'' END
                + CASE WHEN ISNULL(U_TypeName,'')=''     THEN N'「類別名稱」' ELSE N'' END
        FROM OWTR
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT);
    END


    ----------------------------------------------------------------
    -- Error Message
    ----------------------------------------------------------------
    IF @MissingFields3 <> N''
    BEGIN
        SET @error = 4003;
        SET @error_message = @MissingFields3 + N' 欄位未填寫，請完整填寫。';
    END

-- ----------------------------------------------------------------
--    -- Nick 新增無法在13字段有特殊字元及空白
--    ----------------------------------------------------------------

IF @object_type = N'RAWITEMCREA'
   AND @transaction_type IN (N'A', N'U')
BEGIN
    IF EXISTS (
        SELECT 1
        FROM dbo.[@RAWITEMCREA1] AS T1
        WHERE T1.Code = LTRIM(RTRIM(@list_of_cols_val_tab_del))
          AND (
                 T1.U_ItemCode IS NULL
              OR LTRIM(RTRIM(T1.U_ItemCode)) = N''
              OR T1.U_ItemCode COLLATE Latin1_General_100_BIN2
                   LIKE N'%[^一-龥A-Za-z0-9]%'
          )
    )
    BEGIN
        SET @error = -1;
        SET @error_message = N'U_ItemCode 只能輸入中文、英文及數字';
    END
END
----------------------------------------------------------------
-- Series 對應 UDO 組合驗證
-- 類別 / 異動區分 / 異動原因 必須符合 UDO 設定
----------------------------------------------------------------
IF @object_type = '20' AND @transaction_type IN ('A','U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OPDN T0
        WHERE T0.DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND NOT EXISTS (
                SELECT 1
                FROM [@FORM_NUM] T1
                WHERE T1.U_SystemCode = CAST(T0.Series AS NVARCHAR(20))
                  AND T1.U_CategoryName = T0.U_TypeName
                  AND T1.U_ChangeType = T0.U_ChangeType
                  AND T1.U_ChangeReason = T0.U_ChangeReason
          )
    )
    BEGIN
        SET @error = 2006
        SET @error_message = N'類別名稱、異動區分或異動原因與該單據系列別設定不符，請重新確認。'
    END

END

-- /* AP 貸項通知書驗證 */
--IF @object_type = '19'
--   AND @transaction_type IN ('A', 'U')
--BEGIN
--    DECLARE @APCreditMemoDocEntry INT

--    SET @APCreditMemoDocEntry =
--        TRY_CONVERT(INT, @list_of_cols_val_tab_del)

--    IF EXISTS
--    (
--        SELECT 1
--        FROM dbo.RPC1
--        WHERE DocEntry = @APCreditMemoDocEntry
--          AND ISNULL(NoInvtryMv, 'N') = 'Y'
--    )
--    AND EXISTS
--    (
--        SELECT 1
--        FROM dbo.RPC1
--        WHERE DocEntry = @APCreditMemoDocEntry
--          AND ISNULL(NoInvtryMv, 'N') <> 'Y'
--    )
--    BEGIN
--        SET @error = 19001
--        SET @error_message =
--            N'表身「不含數量過帳」必須全部勾選或全部不勾選。'
--    END


--END
--Rachel 20260730 越南不需要此卡控

END

----------------------------------------------------------------
-- 生產訂單：申請部門必填
----------------------------------------------------------------
IF @object_type = '202'
AND
[2026/8/20 上午 10:19] Nick: @transaction_type IN ('A','U')
BEGIN

    IF EXISTS (
        SELECT 1
        FROM OWOR
        WHERE DocEntry = CAST(@list_of_cols_val_tab_del AS INT)
          AND ISNULL(U_ReqDept,'') = ''
    )
    BEGIN
        SET @error = 20201
        SET @error_message = N'申請部門欄位必填。'
    END

END




/*
    庫存調撥單與草稿卡控：
    當 Series = 21 時，必須填寫有效的參考文件---NICK 2026/08/13新增
*/

/* 一、正式庫存調撥單 */
IF @object_type = '67'
   AND @transaction_type IN ('A', 'U')
BEGIN
    IF EXISTS
    (
        SELECT 1
        FROM OWTR T0
        WHERE T0.DocEntry =
              TRY_CONVERT(INT, @list_of_cols_val_tab_del)
          AND T0.Series = 110
          AND NOT EXISTS
          (
              SELECT 1
              FROM WTR21 T1
              WHERE T1.DocEntry = T0.DocEntry
                AND T1.ObjectType = '67'
                AND
                (
                    /* 系統參考文件 */
                    (
                        T1.RefType = 'S'
                        AND T1.RefDocEntr IS NOT NULL
                        AND T1.RefObjType IS NOT NULL
                    )
                    OR
                    /* 外部參考文件 */
                    (
                        T1.RefType = 'E'
                        AND ISNULL(
                            LTRIM(RTRIM(T1.ExtDocNum)),
                            ''
                        ) <> ''
                    )
                )
          )
    )
    BEGIN
        SET @error = -5001
        SET @error_message =
            N'庫存調撥單的「參考文件」為必填，請輸入有效的系統或外部參考文件。'
    END
END


/* 二、庫存調撥草稿 */
IF @object_type = '112'
   AND @transaction_type IN ('A', 'U')
BEGIN
    IF EXISTS
    (
        SELECT 1
        FROM ODRF T0
        WHERE T0.DocEntry =
              TRY_CONVERT(INT, @list_of_cols_val_tab_del)
          AND T0.ObjType = '67'
          AND T0.Series = 110
          AND NOT EXISTS
          (
              SELECT 1
              FROM DRF21 T1
              WHERE T1.DocEntry = T0.DocEntry
                AND T1.ObjectType = '112'
                AND
                (
                    /* 系統參考文件 */
                    (
                        T1.RefType = 'S'
                        AND T1.RefDocEntr IS NOT NULL
                        AND T1.RefObjType IS NOT NULL
                    )
                    OR
                    /* 外部參考文件 */
                    (
                        T1.RefType = 'E'
                        AND ISNULL(
                            LTRIM(RTRIM(T1.ExtDocNum)),
                            ''
                        ) <> ''
                    )
                )
          )
    )
    BEGIN
        SET @error = -5002
        SET @error_message =
            N'庫存調撥草稿的「參考文件」為必填，請輸入有效的系統或外部參考文件。'
    END
END

-- Select the return values
select @error, @error_message

end
[2026/8/20 上午 10:22] Nick: ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
[2026/8/20 上午 10:22] Nick: USE [WellShin_VN_Official_1]
GO
/****** Object:  StoredProcedure [dbo].[SBO_SP_PostTransactionNotice]    Script Date: 2026/8/20 上午 10:18:54 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER proc [dbo].[SBO_SP_PostTransactionNotice]

@object_type nvarchar(30),     -- SBO Object Type
@transaction_type nchar(1),   -- [A]dd, [U]pdate, [D]elete, [C]ancel, C[L]ose
@num_of_cols_in_key int,
@list_of_key_cols_tab_del nvarchar(255),
@list_of_cols_val_tab_del nvarchar(255)

AS

begin

-- Return values
declare @error  int    -- Result (0 for no error)
declare @error_message nvarchar (200)   -- Error string to be displayed
select @error = 0
select @error_message = N'Ok'

--------------------------------------------------------------------------------------------------------------------------------

-- ADD YOUR CODE HERE


--RMA

IF @object_type = '16'   -- ORDN (退貨單 / RMA，請依你系統實際 ObjectType 確認)
AND @transaction_type IN ('A','U')
BEGIN
    DECLARE 
        @DocEntry INT,
        @CustomerID NVARCHAR(50),
        @Reason NVARCHAR(50),
        @YYMM NVARCHAR(4),
        @Prefix NVARCHAR(200),
        @MaxSeq INT,
        @NewSeq NVARCHAR(4),
        @NewRMA NVARCHAR(200),
        @DocDate DATE;

    SET @DocEntry = CAST(@list_of_cols_val_tab_del AS INT);

    /* 1. 取得資料 */
    SELECT 
        @CustomerID = T0.CardCode,
        @DocDate    = T0.DocDate
    FROM ORDN T0
    WHERE T0.DocEntry = @DocEntry;

    SELECT TOP 1
        @Reason = ISNULL(T1.U_ReturnReason,'NA')
    FROM RDN1 T1
    WHERE T1.DocEntry = @DocEntry;   -- ⚠ 若實際關聯不是 DocEntry，這裡需調整

    /* 2. 產生 YYMM */
    SET @YYMM = RIGHT(CONVERT(VARCHAR(4),YEAR(@DocDate)),2) 
              + RIGHT('0' + CONVERT(VARCHAR(2),MONTH(@DocDate)),2);

    SET @Prefix = 'RMA-' + @CustomerID + '-' + @Reason + '-' + @YYMM + '-';

    /* 3. 找同 YYMM 最大流水號 */
    SELECT 
        @MaxSeq = MAX(CAST(RIGHT(U_RMA,4) AS INT))
    FROM ORDN
    WHERE U_RMA LIKE @Prefix + '%';

    IF @MaxSeq IS NULL
        SET @MaxSeq = 0;

    /* 4. 新流水號 */
    SET @NewSeq = RIGHT('0000' + CAST(@MaxSeq + 1 AS VARCHAR),4);
    SET @NewRMA = @Prefix + @NewSeq;

    /* 5. 更新 ORDN */
    UPDATE ORDN
    SET U_RMA = @NewRMA
    WHERE DocEntry = @DocEntry;
END

----收貨採購單建立帳號填入申請人欄位--
--if @object_type in('20') and (@transaction_type IN('A','U'))
--BEGIN 
--update T0
--SET T0.U_ReqPerson = T1.U_NAME
--FROM OPDN T0
--INNER JOIN OUSR T1 ON T0.UserSign = T1.USERID
--WHERE T0.DocEntry = @list_of_cols_val_tab_del 
--END

--項目主檔建立更新紀錄填入--
if @object_type in('4') and (@transaction_type IN('A','U'))
BEGIN 
update T0
SET 
T0.U_CreateUser = T1.U_NAME,
T0.U_CreateDate = T0.CreateDate,
T0.U_UpdateUser = T2.U_NAME,
T0.U_UpdateDate = T0.UpdateDate
FROM OITM T0
LEFT JOIN OUSR T1 ON T0.UserSign = T1.USERID
LEFT JOIN OUSR T2 ON T0.UserSign2 = T1.USERID
WHERE T0.ItemCode = @list_of_cols_val_tab_del 
END
--項目主檔建立更新紀錄填入--
--業務夥伴主黨建立更新紀錄填入--
if @object_type in('2') and (@transaction_type IN('A','U'))
BEGIN 
update T0
SET 
T0.U_CreateUser = T1.U_NAME,
T0.U_CreateDate = T0.CreateDate,
T0.U_UpdateUser = T2.U_NAME,
T0.U_UpdateDate = T0.UpdateDate
FROM OCRD T0
LEFT JOIN OUSR T1 ON T0.UserSign = T1.USERID
LEFT JOIN OUSR T2 ON T0.UserSign2 = T1.USERID
WHERE T0.CardCode = @list_of_cols_val_tab_del 
END
--業務夥伴主檔建立更新紀錄填入--


----------------------------------------------------------------
-- 規則：
-- 項目主檔(4) 成品
-- 產品形式：頭部+尾部 (產品形式欄位還沒建需要建立)
-- 產品規格描述欄位：線材種類+ 線材規格+ 芯線數+溫度+顔色+長度(MM) + 如果有長度資料會加”MM” 
-- 項目說明欄位：由產品形式+產品規格描述 組成
-- 建立時間：20260424
-- 建立人：Tommy
-- 更新時間：20260604
-- 更新人：Rachel
----------------------------------------------------------------

IF @object_type = '4' AND @transaction_type in ('A','U')
BEGIN
    UPDATE OITM SET
        OITM.U_ProductForm = ISNULL(OITM.U_HeadType,'') + '' + ISNULL(OITM.U_TailType,'')
    FROM OITM
 WHERE OITM.ItmsGrpCod = 101
END -- IF @object_type = '4' AND @transaction_type in ('A','U')
--20260604 Rachel 在各欄位前加了 ISNULL() 避免全部被判斷為 ISNULL

IF @object_type = '4' AND @transaction_type in ('A','U')
BEGIN
    UPDATE OITM SET
        OITM.U_ItemSpec = ISNULL(OITM.U_WireType,'') + ' ' + ISNULL(OITM.U_WireSpec,'') +
[2026/8/20 上午 10:22] Nick: ' ' + ISNULL(OITM.U_CoreCount,'') + ' ' + ISNULL(OITM.U_TempRating,'') + ' ' +ISNULL(OITM.U_Color,'') + ' ' +ISNULL(OITM.U_Length,'') + 
            CASE WHEN ISNULL(U_Length,'') <> '' THEN 'MM' ELSE '' END --CASE
    FROM OITM
 WHERE OITM.ItmsGrpCod = 101
END -- IF @object_type = '4' AND @transaction_type in ('A','U')
--20260515 Tommy此段有問題無法新增更新OITM，先註解待確認
--20260604 Rachel 在各欄位前加了 ISNULL() 避免全部被判斷為 ISNULL，已正常運作

------20260602 Tommy demo董事長先註解
IF @object_type = '4' AND @transaction_type in ('A','U')
BEGIN
    UPDATE OITM SET
        OITM.ItemName =ISNULL(U_ProductForm,'') + '' + ISNULL(U_ItemSpec,'')
    FROM OITM
 WHERE OITM.ItmsGrpCod = 101
END -- IF @object_type = '4' AND @transaction_type in ('A','U')
----20260602 Tommy demo董事長先註解
--20260604 Rachel 在各欄位前加了 ISNULL() 避免全部被判斷為 ISNULL

----------------------------------------------------------------
-- 規則：
-- 項目主檔(4) 零部件
-- 項目說明欄位：由13字段組成
-- 建立時間：20260615
-- 建立人：Rachel
-- 更新時間：
-- 更新人：
----------------------------------------------------------------


IF @object_type = '4' AND @transaction_type IN ('A','U')
BEGIN
    UPDATE OITM
    SET
        OITM.ItemName =
            ISNULL(U_Seg1Desc,'') + ' '
          + ISNULL(U_Seg2Desc,'') + ' '
          + ISNULL(U_Seg3Desc,'') + ' '
          + ISNULL(U_Seg4Desc,'') + ' '
          + ISNULL(U_Seg5Desc,'') + ' '
          + ISNULL(U_Seg6Desc,'') + ' '
          + ISNULL(U_Seg7Desc,'') + ' '
          + ISNULL(U_Seg8Desc,'') + ' '
          + ISNULL(U_Seg9Desc,'') + ' '
          + ISNULL(U_Seg10Desc,'') + ' '
          + ISNULL(U_Seg11Desc,'') + ' '
          + ISNULL(U_Seg12Desc,'') + ' '
          + ISNULL(U_Seg13Desc,'')
    FROM OITM
    WHERE OITM.ItmsGrpCod = 103
END


--------------------------------------------------------------------------------------------------------------------------------

-- Select the return values
select @error, @error_message

end
````


**當時回答**

已整理完成「越南正式區.md」，並分成兩支不同的預存程序：

- `SBO_SP_TransactionNotification`
- `SBO_SP_PostTransactionNotice`

聊天時間與姓名前綴已移除，SQL 內容完整保留。

[開啟「越南正式區.md」](C:\Users\nick_yeh\Documents\維熹卡控\越南正式區.md)

---

