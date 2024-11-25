---
title: REST API で Azure エンタープライズ加入契約請求データを確認する
description: Azure REST API を使用して、エンタープライズ加入契約の課金情報を確認する方法を説明します。
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: article
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 23c7b078b54d050aa687fe4bdb08fa908c13ed48
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464758"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>REST API を使用してエンタープライズ加入契約の請求書を確認する

Azure レポート API は Azure コストの確認や管理に役立ちます。

この記事では、Azure REST API シリーズを使用して、請求先アカウント、部門、またはマイクロソフト エンタープライズ契約 (EA) 登録アカウントに関連付けられた課金情報を取得する方法について説明します。

## <a name="individual-account-billing"></a>個別のアカウント課金

部門内のアカウントについて使用状況の詳細を取得するには:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` パラメーターは必須です。これには、アカウントの ID を指定する必要があります。

次のヘッダーは必須です｡

|要求ヘッダー|説明|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 `application/json` を設定します。|  
|*Authorization:*|必須。 有効な `Bearer` [API キー](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)に設定します。 |  

この例は、現在の請求期間の詳細を返す同期呼び出しを示しています。 パフォーマンスの理由から、同期呼び出しでは先月の情報が返されます。  また、[API を非同期に](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)呼び出すと、36 か月分のデータを返すこともできます。


## <a name="response"></a>Response  

応答に成功すると､状態コード 200 (OK) が返されます｡内容は､アカウントの詳しいコストの一覧です。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

この例は省略されたものです。各応答フィールドの詳しい説明とエラー処理については、[請求先アカウントの使用状況詳細の取得](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy)に関する記事をご覧ください。

## <a name="department-billing"></a>部門の課金

部門内のすべてのアカウントについて集計された、使用状況の詳細を取得します。

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` パラメーターは必須です。これには、登録アカウント内の部門の ID を指定する必要があります。

次のヘッダーは必須です｡

|要求ヘッダー|説明|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 `application/json` を設定します。|  
|*Authorization:*|必須。 有効な `Bearer` [API キー](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)に設定します。 |  

この例は、現在の請求期間の詳細を返す同期呼び出しを示しています。 パフォーマンスの理由から、同期呼び出しでは先月の情報が返されます。  また、[API を非同期に](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)呼び出すと、36 か月分のデータを返すこともできます。

### <a name="response"></a>Response  

正常な応答では、状態コード 200 (OK) が返されます。応答には、指定された請求期間における詳しい使用状況とコストの一覧、および部門の請求書 ID が含まれます。


次の例は、部門 `1234` についての REST API の出力を示したものです。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

この例は省略されたものです。各応答フィールドの詳しい説明とエラー処理については、[部門の使用状況詳細の取得](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy)に関する記事をご覧ください。

## <a name="enrollment-account-billing"></a>登録アカウントの課金

登録アカウントについて集計された使用状況の詳細を取得します。

```http
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` パラメーターは必須です。これには、登録アカウントの ID を指定する必要があります。

次のヘッダーは必須です｡

|要求ヘッダー|説明|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 `application/json` を設定します。|  
|*Authorization:*|必須。 有効な `Bearer` [API キー](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)に設定します。 |  

この例は、現在の請求期間の詳細を返す同期呼び出しを示しています。 パフォーマンスの理由から、同期呼び出しでは先月の情報が返されます。  また、[API を非同期に](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)呼び出すと、36 か月分のデータを返すこともできます。

### <a name="response"></a>Response  

正常な応答では、状態コード 200 (OK) が返されます。応答には、指定された請求期間における詳しい使用状況とコストの一覧、および部門の請求書 ID が含まれます。

次の例は、エンタープライズ加入契約 `1234` についての REST API の出力を示したものです。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```

この例は省略されたものです。各応答フィールドの詳しい説明とエラー処理については、[登録アカウントの使用状況詳細の取得](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
- 「[Enterprise Reporting の概要](./enterprise-api.md)」を参照してください。
- [Enterprise Billing REST API](/rest/api/billing/) について調べます。   
- [Azure Rest API の開始](/rest/api/azure/)