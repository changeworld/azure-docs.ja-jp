---
title: クラウドアラート用の Microsoft Defender のスキーマ
description: この記事では、セキュリティアラート用に Microsoft Defender for Cloud で使用されるさまざまなスキーマについて説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: memildin
ms.openlocfilehash: 6554e451c1622e10ab7d06abfb4d36e309be0395
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433348"
---
# <a name="security-alerts-schemas"></a>セキュリティ アラートのスキーマ

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

サブスクリプションで拡張セキュリティ機能が有効になっている場合は、Defender for Cloud がリソースに対する脅威を検出したときにセキュリティの警告が表示されます。

これらのセキュリティアラートは、Microsoft Defender for Cloud のページ- [概要ダッシュボード](overview-page.md)、 [アラート](tutorial-security-incident.md)、 [リソース正常性ページ](investigate-resource-health.md)、 [ワークロードの保護のダッシュボード](workload-protections-dashboard.md) 、および次のような外部ツールで表示できます。

- [Microsoft Sentinel](../sentinel/index.yml)：Microsoft のクラウドネイティブ SIEM。 Sentinel コネクタは、Microsoft Defender for Cloud からアラートを取得し、Microsoft Sentinel の [Log Analytics ワークスペース](../azure-monitor/logs/quick-create-workspace.md) に送信します。
- サードパーティの SIEM - [Azure Event Hubs](../event-hubs/index.yml) にデータを送信します。 その後、Event Hub のデータをサードパーティの SIEM に統合します。 詳細については、「[SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)」を参照してください。
- [REST API](/rest/api/securitycenter/) - REST API を使用してアラートにアクセスする場合は、[Alerts API のオンライン ドキュメント](/rest/api/securitycenter/alerts)を参照してください。

プログラムを使った方法でアラートを取り込む場合は、対象となるフィールドを検索するための適切なスキーマが必要です。 また、イベント ハブへのエクスポートを実行する場合や、汎用的な HTTP コネクタでワークフロー オートメーションをトリガーしようとしている場合は、JSON オブジェクトを適切に解析するためにスキーマを使用します。

>[!IMPORTANT]
> そうしたシナリオごとにスキーマはわずかに異なりますので、以下の中から該当するタブを選択してください。


## <a name="the-schemas"></a>スキーマ 


### <a name="microsoft-sentinel"></a>[Microsoft Sentinel](#tab/schema-sentinel)

Sentinel コネクタは、Microsoft Defender for Cloud からアラートを取得し、Microsoft Sentinel の Log Analytics ワークスペースに送信します。

クラウドアラートに Defender を使用して Microsoft Sentinel ケースまたはインシデントを作成するには、以下に示すアラートのスキーマが必要です。 

Microsoft Sentinel の詳細については、 [のドキュメント](../sentinel/index.yml)を参照してください。

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]


### <a name="azure-activity-log"></a>[Azure アクティビティ ログ](#tab/schema-activitylog)

Microsoft Defender for Cloud は、生成されたセキュリティアラートを Azure アクティビティログのイベントとして監査します。

セキュリティ アラートのイベントは、アクティビティ ログで Activate Alert (アラートのアクティブ化) イベントを検索することによって確認できます。

[![アクティビティ ログから Activate Alert (アラートのアクティブ化) イベントを検索する。](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Azure のアクティビティ ログに送信されるアラートのサンプル JSON

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>スキーマのデータ モデル

|フィールド|説明|
|----|----|
|**channels**|定数 ("Operation")|
|**correlationId**|Microsoft Defender for Cloud のアラート ID|
|**description**|アラートの説明|
|**eventDataId**|correlationId を参照してください。|
|**eventName**|サブフィールドの value と localizedValue にアラートの表示名が格納されます。|
|**category**|サブフィールドの value と localizedValue は定数です ("Security")。|
|**eventTimestamp**|アラートが生成された時刻の UTC タイムスタンプ|
|**id**|完全修飾アラート ID|
|**level**|定数 ("Informational")|
|**operationId**|correlationId を参照してください。|
|**operationName**|value フィールドは定数 "Microsoft.Security/locations/alerts/activate/action" で、localizedValue は "Activate Alert" です (ユーザー ロケールごとにローカライズすることが可能)。|
|**resourceGroupName**|リソース グループの名前が含まれます。|
|**resourceProviderName**|サブフィールドの value と localizedValue は定数です ("Microsoft.Security")。|
|**resourceType**|サブフィールドの value と localizedValue は定数です ("Microsoft.Security/locations/alerts")。|
|**resourceId**|完全修飾 Azure リソース ID|
|**status**|サブフィールドの value と localizedValue は定数です ("Active")。|
|**subStatus**|サブフィールドの value と localizedValue は空です。|
|**submissionTimestamp**|イベントがアクティビティ ログに送信された時刻の UTC タイムスタンプ|
|**subscriptionId**|セキュリティ侵害を受けたリソースのサブスクリプション ID|
|**properties**|その他アラートに関するプロパティの JSON バッグ。 これらはアラートごとに異なる場合がありますが、次のフィールドについては、すべてのアラートに共通です。<br>- severity: 攻撃の重大度<br>- compromisedEntity: セキュリティ侵害を受けたリソースの名前<br>- remediationSteps: 講じるべきさまざまな修復手順<br>- intent: アラートのキルチェーンの意図。 想定される意図については、[「意図」の表](alerts-reference.md#intentions)に記載されています。|
|**relatedEvents**|定数 (空の配列)|
|||


### <a name="workflow-automation"></a>[ワークフローの自動化](#tab/schema-workflow-automation)

ワークフローの自動化を使用する場合のアラート スキーマについては、[コネクタのドキュメント](/connectors/ascalert/)を参照してください。


### <a name="continuous-export"></a>[連続エクスポート](#tab/schema-continuousexport)

クラウドの連続エクスポート機能の Defender は、アラートデータを次のように渡します。

- Azure Event Hub。その際、[アラート API](/rest/api/securitycenter/alerts) と同じスキーマを使用します。
- Log Analytics ワークスペース。その際、Azure Monitor データ リファレンス ドキュメントの [SecurityAlert スキーマ](/azure/azure-monitor/reference/tables/SecurityAlert)に従います。




### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph は、Microsoft 365 のデータとインテリジェンスにアクセスするための入口です。 Microsoft 365、Windows 10、Enterprise Mobility + Security の大量のデータにアクセスする目的で使用できる統合型プログラミング モデルを備えています。 Microsoft Graph の豊富なデータを使用して、数百万人のユーザーと対話する、組織やコンシューマー向けのアプリを作成できます。

MS Graph に送信されるセキュリティ アラートのスキーマと JSON 表記は、[Microsoft Graph のドキュメント](/graph/api/resources/alert)でご覧いただけます。

---


## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Defender for Cloud の脅威保護ツールがセキュリティ警告情報を送信するときに使用するスキーマについて説明します。

クラウドの外部 Defender からセキュリティアラートにアクセスする方法の詳細については、次のページを参照してください。

- [Microsoft Sentinel](../sentinel/index.yml)：Microsoft のクラウドネイティブ SIEM
- [Azure Event Hubs](../event-hubs/index.yml) - Microsoft のフル マネージドのリアルタイム データ インジェスト サービス
- [クラウドデータに対して Defender を継続的にエクスポートする](continuous-export.md)
- [Log Analytics ワークスペース](../azure-monitor/logs/quick-create-workspace.md) - Azure Monitor のログ データは、Log Analytics ワークスペースという、データと構成情報を含んだコンテナーに格納されます。