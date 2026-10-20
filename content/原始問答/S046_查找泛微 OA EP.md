# 查找泛微 OA EP

來源編號：S046  
聊天 ID：`019fca97-b5d4-7b53-beb2-f984865c1304`  
聊天起始日期（台北）：2026-08-04  
保存位置：一般本機紀錄  
問答段落：2

> 以下為當時的提問與回答，已去除工具呼叫、推理及環境注入資訊；不代表回答已重新查證或操作已完成。圖片與非文字附件未嵌入，原成果連結可能仍指向原電腦位置。

<a id="q1"></a>

## S046-Q001｜泛微 oa EP

日期：2026-08-04

**你的提問**

泛微 oa  EP

**當時回答**

你想了解泛微 OA 的哪一项？这里的「EP」是指：

- **E-Portal 门户**
- **Ecology 补丁（EP）**
- **二次开发接口**
- **部署、升级或故障排查**

请补充具体问题或贴出报错信息。

---

<a id="q2"></a>

## S046-Q002｜泛微 OA 跟 SAPB1 串接

日期：2026-08-04

**你的提問**

泛微 OA 跟 SAPB1 串接

**當時回答**

可以串接。建议采用「泛微 OA → 中间接口服务 → SAP Business One」架构，不要让 OA 直接写 SAP B1 数据库。

```text
泛微 OA 表单/审批
        ↓ 审批完成事件
    中间接口服务
（转换、校验、重试、日志）
        ↓
SAP B1 Service Layer / DI API
        ↓
回写 DocEntry、DocNum、状态或错误信息
```

推荐方案：

- **SAP B1 10.0：优先使用 Service Layer OData V4**
  - HTTPS + JSON，适合泛微 Java 环境调用
  - 可处理业务伙伴、采购订单、销售订单、付款等业务对象
  - SAP 自 FP 2405 起已将 OData V4 作为主要协议。[SAP Service Layer 官方说明](https://help.sap.com/doc/056f69366b5345a386bb8149f1700c19/10.0/en-US/Service%20Layer%20API%20Reference.html)
- 若现有 SAP B1 环境无法使用 Service Layer，可采用 **DI API**
  - 需要 Windows/.NET 与 SAP B1 DI API 组件
  - DI API 不支持多线程，通常应由独立接口服务封装。[SAP DI API 官方说明](https://help.sap.com/docs/SAP_BUSINESS_ONE/a2c93a1c88a24f1faeee3d3f62fbde7a/1c66e99da4864964ac11bb74e49786b8.html?locale=en-US)

常见串接流程：

1. OA 同步 SAP 的供应商、客户、物料、部门、项目及会计科目等基础资料。
2. 使用者在 OA 填写采购、费用或付款申请。
3. OA 审批完成后调用中间服务。
4. 中间服务把字段转换成 SAP B1 格式并建立单据。
5. SAP 返回 `DocEntry`、`DocNum`。
6. 回写 OA；失败则保存错误、允许重送。

实施时必须处理：

- OA 流程编号作为唯一键，防止重复建立 SAP 单据。
- 建立完整字段对照表，包括表头、明细、税码、币别、成本中心及项目代码。
- SAP 过账失败时保留原始请求与响应。
- 采用队列、重试与人工补送机制。
- SAP 账号仅授予必要权限，密码不要存放在 OA 表单。
- 不直接 `INSERT/UPDATE` SAP B1 数据表，应通过 Service Layer 或 DI API 执行业务规则。

例如建立采购订单时，中间服务通常调用：

```http
POST https://sap-server:50000/b1s/v2/PurchaseOrders
Content-Type: application/json
```

```json
{
  "CardCode": "V00001",
  "DocDate": "2026-08-04",
  "NumAtCard": "OA-20260804-001",
  "DocumentLines": [
    {
      "ItemCode": "ITEM001",
      "Quantity": 10,
      "UnitPrice": 100,
      "WarehouseCode": "01"
    }
  ]
}
```

SAP B1 的 Service Layer 使用 HTTP/OData，并以 JSON 收发数据，官方也提供实体和动作的完整定义。[Service Layer 使用说明](https://help.sap.com/docs/SAP_BUSINESS_ONE/f110a154dd0f4c20bf7f3ebca9eeb794/44fda5d585e94b0bbd9d711584db0b96.html?locale=en-US)

若要进一步产出接口规格，需要确认：

- 泛微版本：E-cology 8、9、10，还是 E9？
- SAP B1 版本及数据库：HANA 或 SQL Server？
- 第一阶段要串接哪种流程：采购订单、费用报销、付款申请还是主数据？

---

