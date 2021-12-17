---
title: Media Services データ参照の監視
description: Media Services を監視する際に必要となる重要なリファレンス資料
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: a9bc17528e1314a033cf2e45fee4b112eb088cc3
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715576"
---
# <a name="monitoring-media-services-data-reference"></a>Media Services データ参照の監視

この記事では、Media Services の監視に役立つデータについて説明します。 Azure Monitor でサポートされているすべてのプラットフォーム メトリックの詳細については、「[Azure Monitor のサポートされるメトリック](../../../azure-monitor/essentials/metrics-supported.md)」を参照してください。

## <a name="metrics"></a>メトリック

メトリックは、値の変化とは無関係に、一定の間隔で収集されます。 これらは頻繁にサンプリングでき、比較的単純なロジックですばやく起動できるため、アラートを発行するときに役に立ちます。


Media Services では、次のリソースの監視メトリックがサポートされています。

|メトリックの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| Media Services 全般 | [全般](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservices) |
| ライブ イベント | [Microsoft.Media/mediaservices/liveEvents](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesliveevents) 
| ストリーミング エンドポイント | [REST API ストリーミングエンド](/rest/api/media/streamingendpoints)ポイントに関連する [Windowsazure.mediaservices/streamingendpoints](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints)。 


[アカウントのクォータと制限事項](../limits-quotas-constraints-reference.md)もご確認ください。


## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

メディア サービスには、次のメトリック ディメンションがあります。  これらは、サポートするメトリックに基づいており自明です。  詳細については、上記の[メトリックリンク](#metrics)を参照してください。   
- OutputFormat
- HttpStatusCode 
- ErrorCode 
- TrackName 

## <a name="resource-logs"></a>リソース ログ

リソース ログからは、Azure リソースの操作に関するデータが豊富かつ頻繁に提供されます。 詳細については、[Azure リソースからのログ データを収集して使用する方法](../../../azure-monitor/essentials/platform-logs-overview.md)に関するページをご覧ください。

Media Services では、次のリソース ログがサポートされています。 [Microsoft.Media/mediaservices](../../../azure-monitor/essentials/resource-logs-categories.md#microsoftmediamediaservices)

## <a name="schemas"></a>スキーマ

診断ログの上位スキーマについて詳しくは、「[Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ](../../../azure-monitor/essentials/resource-logs-schema.md)」をご覧ください。

### <a name="key-delivery"></a>キー配信 

これらのプロパティは、キー配信ログのスキーマに固有です。

|名前|説明|
|---|---|
|keyId|要求されたキーの ID。|
|keyType|次のいずれかの値を指定できます:"Clear" (暗号化なし)、"FairPlay"、"PlayReady"、または"Widevine"。|
|policyName|ポリシーの Azure Resource Manager の名前。|
|tokenType|トークンの型。|
|statusMessage|状態メッセージ。|

### <a name="example"></a>例

キー配信の要求スキーマのプロパティ。

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
