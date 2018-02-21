---
title: "Azure アクティビティ ログのイベント スキーマ | Microsoft Docs"
description: "アクティビティ ログに出力されるデータのイベント スキーマを理解する"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: johnkem
ms.openlocfilehash: a5c05466b21184a73d08190856e00ae95ee3727f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="azure-activity-log-event-schema"></a>Azure アクティビティ ログのイベント スキーマ
**Azure アクティビティ ログ**は、Azure で発生したあらゆるサブスクリプションレベルのイベントの分析に利用できるログです。 この記事では、データのカテゴリごとにイベント スキーマを説明します。

## <a name="administrative"></a>管理
このカテゴリには、Resource Manager で実行されるすべての作成、更新、削除、アクション操作のレコードが含まれています。 このカテゴリで表示されるイベントの種類として、"仮想マシンの作成"、"ネットワーク セキュリティ グループの削除" などがあります。ユーザーまたはアプリケーションが Resource Manager を使用して実行するすべてのアクションは、特定のリソースの種類に対する操作としてモデリングされます。 操作の種類が書き込み、削除、またはアクションの場合、その操作の開始のレコードと成功または失敗のレコードは、いずれも管理カテゴリに記録されます。 管理カテゴリには、サブスクリプション内のロールベースのアクセス制御に対する任意の変更も含まれています。

### <a name="sample-event"></a>サンプル イベント
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "dd042f02-6b3e-4f79-939a-6a381ffed3c0",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | [説明] |
| --- | --- |
| authorization |イベントの RBAC プロパティの BLOB。 通常は、"action"、"role"、"scope" の各プロパティが含まれます。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。 |
| channels |値として、"Admin" または "Operation" を指定します。 |
| claims |Resource Manager でこの操作を実行するユーザーまたはアプリケーションを認証するために Active Directory によって使用される JWT トークン。 |
| correlationId |通常は文字列形式の GUID。 correlationId を共有するイベントは、同じ uber アクションに属します。 |
| 説明 |イベントを説明する静的テキスト。 |
| eventDataId |イベントの一意の識別子。 |
| httpRequest |Http 要求を記述する BLOB。 通常、"clientRequestId"、"clientIpAddress"、"method" (HTTP メソッド、 たとえば PUT) が含まれます。 |
| level |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダーの名前。 |
| ResourceId |影響を受けるリソースのリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus |通常は対応する REST 呼び出しの HTTP 状態コードですが、副状態を示す他の文字列が含まれる場合もあります。たとえば、OK (HTTP Status Code: 200)、Created (HTTP Status Code: 201)、Accepted (HTTP Status Code: 202)、No Content (HTTP Status Code: 204)、Bad Request (HTTP Status Code: 400)、Not Found (HTTP Status Code: 404)、Conflict (HTTP Status Code: 409)、Internal Server Error (HTTP Status Code: 500)、Service Unavailable (HTTP Status Code: 503)、Gateway Timeout (HTTP Status Code: 504) などの一般的な値が含まれる場合があります。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="service-health"></a>サービス正常性
このカテゴリには、Azure で発生した任意のサービス正常性インシデントのレコードが含まれます。 このカテゴリで表示されるイベントの種類として、"SQL Azure in East US is experiencing downtime" (米国東部の SQL Azure でダウンタイムが発生しています) などがあります。 サービス正常性イベントには、要対応、支援復旧、インシデント、メンテナンス、情報、またはセキュリティという 6 種類があります。イベントの影響を受けるリソースがサブスクリプションにある場合、1 つのイベントのみが表示されます。

### <a name="sample-event"></a>サンプル イベント
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>プロパティの説明
要素名 | [説明]
-------- | -----------
channels | 値は "Admin" または "Operation" のいずれか
correlationId | 通常は文字列形式の GUID。 そのイベントは同じ uber アクションに属し、通常は同じ correlationID を共有します。
説明 | イベントの説明。
eventDataId | イベントの一意識別子。
eventName | イベントのタイトル。
level | イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose”
resourceProviderName | 影響を受けるリソースのリソース プロバイダーの名前。 不明な場合、これは null になります。
resourceType| 影響を受けるリソースの種類。 不明な場合、これは null になります。
subStatus | サービス正常性イベントの場合、通常は null です。
eventTimestamp | ログ イベントが生成され、アクティビティ ログに送信されたときのタイムスタンプ。
submissionTimestamp |   イベントがアクティビティ ログで使用できるようになったときのタイムスタンプ。
subscriptionId | このイベントが記録された Azure サブスクリプション。
status | 操作の状態を説明する文字列。 一般的な値の例: Active、Resolved。
operationName | 操作の名前。 通常は Microsoft.ServiceHealth/incident/action です。
カテゴリ | "ServiceHealth"
ResourceId | 既知の場合は、影響を受けるリソースのリソース ID。 それ以外の場合は、サブスクリプション ID が指定されます。
Properties.title | この通信のローカライズされたタイトル。 既定の言語は英語です。
Properties.communication | HTML マークアップによる通信のローカライズされた詳細。 既定は英語です。
Properties.incidentType | 使用可能な値: AssistedRecovery、ActionRequired、Information、Incident、Maintenance、Security
Properties.trackingId | このイベントに関連付けられているインシデントを識別します。 インシデントに関連するイベントを関連付けるために使用します。
Properties.impactedServices | インシデントの影響を受けるサービスやリージョンが記述されるエスケープされた JSON blob。 それぞれ ServiceName が指定された Services の一覧、およびそれぞれ RegionName が指定された ImpactedRegions の一覧です。
Properties.defaultLanguageTitle | 英語で行われる通信
Properties.defaultLanguageContent | HTML マークアップまたはプレーン テキストとして英語で行われる通信
Properties.stage | AssistedRecovery、ActionRequired、Information、Incident、Security で使用可能な値: Active、Resolved。 Maintenance で使用可能な値: Active、Planned、InProgress、Canceled、Rescheduled、Resolved、Complete
Properties.communicationId | このイベントが関連付けられている通信。

## <a name="alert"></a>アラート:
このカテゴリには、Azure アラートの全アクティビティのレコードが含まれています。 このカテゴリで表示されるイベントの種類として、"CPU % on myVM has been over 80 for the past 5 minutes" (過去 5 分間の myVM の CPU % が 80 を超えました) などがあります。 多様な Azure システムにアラートの概念があります。また、何らかのルールを定義し、条件がそのルールと一致するときに通知を受け取ることができます。 サポートされる Azure のアラートの種類が "アクティブになる" たびに、または通知を生成する条件を満たすたびに、アクティブ化のレコードもこのカテゴリのアクティビティ ログにプッシュされます。

### <a name="sample-event"></a>サンプル イベント

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | [説明] |
| --- | --- |
| caller | 常に Microsoft.Insights/alertRules |
| channels | 常に "Admin, Operation" |
| claims | アラート エンジンの SPN (サービス プリンシパル名) またはリソースの種類の JSON BLOB。 |
| correlationId | 文字列形式の GUID。 |
| 説明 |アラート イベントを説明する静的テキスト。 |
| eventDataId |アラート イベントの一意識別子。 |
| level |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| resourceGroupName |メトリック アラートである場合に影響を受けるリソースのリソース グループの名前。 その他のアラートの場合、これはアラート自体を含むリソース グループの名前です。 |
| resourceProviderName |メトリック アラートである場合に影響を受けるリソースのリソース プロバイダーの名前。 その他のアラートの場合、これはアラート自体のリソース プロバイダーの名前です。 |
| ResourceId | メトリック アラートである場合に影響を受けるリソースのリソース ID の名前。 その他のアラートの場合、これはアラート リソース自体のリソース ID です。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus | アラートの場合、通常は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

### <a name="properties-field-per-alert-type"></a>アラートの種類別のプロパティ フィールド
アラート イベントのソースに応じて、プロパティ フィールドには異なる値が格納されます。 アラート イベントの一般的なプロバイダーは、アクティビティ ログ アラートとメトリック アラートの 2 つです。

#### <a name="properties-for-activity-log-alerts"></a>アクティビティ ログ アラートのプロパティ
| 要素名 | [説明] |
| --- | --- |
| properties.subscriptionId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのサブスクリプション ID。 |
| properties.eventDataId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのイベント データ ID。 |
| properties.resourceGroup | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのリソース グループ。 |
| properties.resourceId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのリソース ID。 |
| properties.eventTimestamp | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのイベント タイムスタンプ。 |
| properties.operationName | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントの操作名。 |
| properties.status | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントの状態。|

#### <a name="properties-for-metric-alerts"></a>メトリック アラートのプロパティ
| 要素名 | [説明] |
| --- | --- |
| properties.RuleUri | メトリック アラート ルール自体のリソース ID。 |
| properties.RuleName | メトリック アラート ルールの名前。 |
| properties.RuleDescription | (アラート ルールで定義された) メトリック アラート ルールの説明。 |
| properties.Threshold | メトリック アラート ルールの評価で使用されるしきい値。 |
| properties.WindowSizeInMinutes | メトリック アラート ルールの評価で使用されるウィンドウ サイズ。 |
| properties.Aggregation | メトリック アラート ルールで定義されている集計の種類。 |
| properties.Operator | メトリック アラート ルールの評価で使用される条件演算子。 |
| properties.MetricName | メトリック アラート ルールの評価で使用されるメトリックのメトリック名。 |
| properties.MetricUnit | メトリック アラート ルールの評価で使用されるメトリックのメトリック単位。 |

## <a name="autoscale"></a>Autoscale
このカテゴリには、サブスクリプションで定義したすべての自動スケール設定に基づいて、自動スケール エンジンの操作に関連するすべてのイベントのレコードが含まれます。 このカテゴリで表示されるイベントの種類として、"Autoscale scale up action failed" (自動スケールのスケールアップ アクションに失敗しました) などがあります。 自動スケールを使用すると、自動スケール設定で指定した時刻や負荷 (メトリック) データに基づいて、サポートされるリソースの種類のインスタンス数を自動的にスケールアウトまたはスケールインすることができます。 スケールアップまたはスケールダウンの条件を満たした場合、開始イベントと、成功または失敗イベントがこのカテゴリに記録されます。

### <a name="sample-event"></a>サンプル イベント
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | [説明] |
| --- | --- |
| caller | 常に Microsoft.Insights/autoscaleSettings |
| channels | 常に "Admin, Operation" |
| claims | 自動スケール エンジンの SPN (サービス プリンシパル名) またはリソースの種類の JSON BLOB。 |
| correlationId | 文字列形式の GUID。 |
| 説明 |自動スケール イベントを説明する静的テキスト。 |
| eventDataId |自動スケール イベントの一意識別子。 |
| level |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| resourceGroupName |自動スケール設定のリソース グループの名前。 |
| resourceProviderName |自動スケール設定のリソース プロバイダーの名前。 |
| ResourceId |自動スケール設定のリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| properties.Description | 自動スケール エンジンが実行していた処理の詳細な説明。 |
| properties.ResourceName | 影響を受けるリソース (スケール アクションが実行されていたリソース) のリソース ID |
| properties.OldInstancesCount | 自動スケール アクションが有効になる前のインスタンスの数。 |
| properties.NewInstancesCount | 自動スケール アクションが有効になった後のインスタンスの数。 |
| properties.LastScaleActionTime | 自動スケール アクションが発生したときのタイムスタンプ。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus | 自動スケールの場合、通常は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="security"></a>セキュリティ
このカテゴリには、Azure Security Center によって生成されたアラートのレコードが含まれます。 このカテゴリで表示されるイベントの種類の例としては、"Suspicious double extension file executed" (拡張子が 2 つある不審なファイルが実行されました) などがあります。

### <a name="sample-event"></a>サンプル イベント
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "d4742bb8-c279-4903-9653-9858b17d0c2e",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | [説明] |
| --- | --- |
| channels | 常に "Operation" |
| correlationId | 文字列形式の GUID。 |
| 説明 |セキュリティ イベントを説明する静的テキスト。 |
| eventDataId |セキュリティ イベントの一意識別子。 |
| eventName |セキュリティ イベントのフレンドリ名。 |
| id |セキュリティ イベントの一意リソース識別子。 |
| level |イベントのレベル。 "Critical"、"Error"、"Warning"、"Informational"、"Verbose" のいずれかの値 |
| resourceGroupName |リソースのリソース グループの名前。 |
| resourceProviderName |Azure Security Center のリソース プロバイダーの名前。 常に "Microsoft.Security"。 |
| resourceType |セキュリティ イベントを生成したリソースの種類 (例: "Microsoft.Security/locations/alerts") |
| ResourceId |セキュリティ アラートのリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 これらのプロパティは、セキュリティ アラートの種類によって異なります。 Security Center から送られてくるアラートの種類について詳しくは、[こちらのページ](../security-center/security-center-alerts-type.md)をご覧ください。 |
| properties.Severity |重大度のレベル。 可能性のある値は、"High"、"Medium"、"Low" です。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus | 通常、セキュリティ イベントの場合は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="next-steps"></a>次の手順
* [アクティビティ ログ (以前の監査ログ) の詳細を確認する](monitoring-overview-activity-logs.md)
* [Azure アクティビティ ログを Event Hubs にストリーミングする](monitoring-stream-activity-logs-event-hubs.md)
