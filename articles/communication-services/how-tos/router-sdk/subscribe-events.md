---
title: ジョブ ルーターでイベントをサブスクライブする
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して、Event Grid からのジョブ ルーター イベントをサブスクライブする
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 8e2cff056a6fb96f5cbcdbaea6be4c4257c3752b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129037"
---
# <a name="subscribe-to-job-router-events"></a>ジョブ ルーター イベントのサブスクライブ

このガイドでは、Azure Communication Services Event Grid サブスクリプションからジョブ ルーター イベントをサブスクライブする手順を説明します。 イベントの受信は、カスタム アプリケーションで実行する必要がある重要な機能です。 送信したジョブに対してジョブ ルーターが実行するアクションは非同期的に実行されます。SDK は、システムのオブジェクトのステータスと状態をクエリするためのエンドポイントを提供し、リアクティブなイベンドリブン カスタム アプリケーションを構築するうえで大きな利点があります。

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 省略可能: [ジョブ ルーターを開始する](../../quickstarts/router/get-started-router.md)ためのクイックスタートを完了する
- [Azure Resource Manager (ARM) クライアント](https://github.com/projectkudu/ARMClient) をインストールする
- [GitHub のサンプル プロジェクトを Event Grid ビューアー](https://github.com/Azure/communication-preview/tree/master/samples/Job-Router/Event-Grid-Viewer)で確認する

## <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する

> [!NOTE]
> 次のスクリプトは、PowerShell を使用して実行されています

**Azure アカウントにログインする**

```powershell
armclient azlogin
```

**サブスクリプションとリソース グループ名を設定する**
```powershell
$env:SUB = 'subscriptions/<insert_subscription_id>'
$env:RG = 'resourcegroups/<insert_resource_group_name>'
```

**(省略可能) サブスクリプション内のすべての ACS リソースを一覧表示する**
```powershell
armclient get "/$env:SUB/$env:RG/providers/Microsoft.Communication/communicationservices?api-version=2020-08-20"
```
**出力**

:::image type="content" source="media/create-subscription-output.png" alt-text="PowerShell コマンドの出力":::

ご覧のように、特定のサブスクリプションとリソース グループの下には現在、1 つの Azure Communication Services リソースしかありません。

**ACS リソース名の設定**

Azure Communication Services リソースの名前を設定します。 たとえば、リソースのエンドポイントが `https://contoso.communication.azure.net` の場合は、`ACS_RESOURCE_NAME` を DNS 名のプレフィックス `contoso` に設定します。

```powershell
$env:ACS_RESOURCE_NAME = "<insert_acs_resource_name>"
```

**(省略可能) 既存のイベント サブスクリプションを一覧表示する**

既存のイベント サブスクリプションがあるかどうかを確認する

```powershell
$env:ACS_RESOURCE_ARM_ID = "/$env:SUB/$env:RG/providers/Microsoft.Communication/CommunicationServices/$env:ACS_RESOURCE_NAME"
$env:API_VERSION = "?api-version=2020-06-01"
$env:EVENT_SUBSCRIPTIONS_PATH = "providers/Microsoft.EventGrid/eventSubscriptions"

armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```
:::image type="content" source="media/check-subscriptions.png" alt-text="既存のサブスクリプションを確認する":::

ご覧のように、このリソースに対してイベント サブスクリプションは作成されません。

**ルーター イベントの新しいイベント サブスクリプションを作成する**

次の json ペイロードをコピーして、`test.json` という名前のテキスト ファイルに貼り付けます。

*サンプル ペイロード*
```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>"
      }
    },
    "filter": {
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobReceived",
        "Microsoft.Communication.RouterJobClassified",
        "Microsoft.Communication.RouterJobLabelsUpdated",
        "Microsoft.Communication.RouterJobClassificationFailed",
        "Microsoft.Communication.RouterJobCompleted",
        "Microsoft.Communication.RouterJobClosed",
        "Microsoft.Communication.RouterJobCancelled",
        "Microsoft.Communication.RouterJobExceptionTriggered",
        "Microsoft.Communication.RouterJobExceptionCleared",
        "Microsoft.Communication.RouterWorkerOfferIssued",
        "Microsoft.Communication.RouterWorkerOfferAccepted",
        "Microsoft.Communication.RouterWorkerOfferDeclined",
        "Microsoft.Communication.RouterWorkerOfferRevoked",
        "Microsoft.Communication.RouterWorkerOfferExpired",
        "Microsoft.Communication.RouterWorkerRegistered",
        "Microsoft.Communication.RouterWorkerDeregistered"
      ],
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    }
  }
}
```

**イベントのサブスクリプション名を設定する**
```powershell
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_All"
```

**イベント サブスクリプションを作成する**
```powershell
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test.json
```
**出力**

:::image type="content" source="media/create-subscription.png" alt-text="イベント サブスクリプションの作成":::

ご覧のように、イベント サブスクリプションは作成中であり、現在 `Creating` の状態です。 作成には通常、数秒かかります。

**イベント サブスクリプションが正常に作成されたことを確認する**
```powershell
armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```

**出力**

:::image type="content" source="media/verify-subscription-created.png" alt-text="サブスクリプションが作成されたのを確認する":::

ご覧のように、すべてのルーター イベントに対してイベント サブスクリプションが正常に作成されています。

## <a name="creating-a-subscription-with-filters"></a>フィルターを使用してサブスクリプションを作成する

イベント サブスクリプションの設定中には、特定のサブスクリプションに送信する必要がある正確なイベントを制御する高度なフィルターを使用することもできます。 たとえば、次の例では、`RouterJobCancelled` イベントだけがサブスクライブされ、次の条件下で Webhook に送信されます。

- ジョブの **優先度** が `5` より大きくなっています
- ジョブがエスカレーション キューに割り当てられました
- 非アクティブが原因でジョブが取り消されました
- 取り消されたジョブの処理コードが `_JobCancelledDueToInactivity` で終わります
- キュー ID は `EscalationQueue` という名前で終わります

```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>",
        "maxEventsPerBatch": 1,
        "preferredBatchSizeInKilobytes": 64
      }
    },
    "filter": {
      "subjectEndsWith": "_JobCancelledDueToInactivity",
      "isSubjectCaseSensitive": true,
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobCancelled"
      ],
      "advancedFilters": [
        {
          "operatorType": "NumberGreaterThan",
          "key": "data.priority",
          "value": 5
        },
        {
          "operatorType": "StringEndsWith",
          "key": "data.queueId",
          "values": [
            "EscalationQueue"
          ]
        }
      ],
      "enableAdvancedFilteringOnArrays": true
    }
  }
}
```

上記の json ペイロードをコピーしてテキストに貼り付けて `test-with-advanced-filters.json` という名前を付け、次の PowerShell コードを実行します。

```powershell
$env:API_VERSION = "?api-version=2020-10-15-preview"
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_WithFilters"
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test-with-advanced-filters.json
```

**出力**

:::image type="content" source="media/advanced-filters.png" alt-text="高度なフィルター出力":::

> [!NOTE]
> サブスクリプションの作成時に使用できる演算子の完全な一覧については、「[Event Grid | イベント のフィルター処理 - 演算子](../../../event-grid/event-filtering.md)」を参照してください

## <a name="events-catalog"></a>イベント カタログ

| events | Subdomain | 説明 |
|------|:------------:| ---------- |
| [`RouterJobReceived`](#microsoftcommunicationrouterjobreceived) | `Job` | ルーティング用に新しいジョブが作成されました |
| [`RouterJobClassified`](#microsoftcommunicationrouterjobclassified)| `Job` | 分類ポリシーがジョブに適用されました |
| [`RouterJobLabelsUpdated`](#microsoftcommunicationrouterjoblabelsupdated) | `Job` | ジョブのラベルが変更されました |
| [`RouterJobClassificationFailed`](#microsoftcommunicationrouterjobclassificationfailed) | `Job` | ルーターは分類ポリシーを使用してジョブを分類できませんでした |
| [`RouterJobCompleted`](#microsoftcommunicationrouterjobcompleted) | `Job` | ジョブが完了し、ラップアップに入ります |
| [`RouterJobClosed`](#microsoftcommunicationrouterjobclosed) | `Job` | ジョブが閉じ、ラップアップが完了しました |
| [`RouterJobCancelled`](#microsoftcommunicationrouterjobcancelled) | `Job` | ジョブがキャンセルされました |
| [`RouterJobExceptionTriggered`](#microsoftcommunicationrouterjobexceptiontriggered) | `Job` | ジョブの例外がトリガーされました |
| [`RouterJobExceptionCleared`](#microsoftcommunicationrouterjobexceptioncleared) | `Job` | ジョブの例外がクリアされました |
| [`RouterWorkerOfferIssued`](#microsoftcommunicationrouterworkerofferissued) | `Worker` | ジョブがワーカーに提供されました |
| [`RouterWorkerOfferAccepted`](#microsoftcommunicationrouterworkerofferaccepted) | `Worker` | ワーカーに対するオファーが受理されました |
| [`RouterWorkerOfferDeclined`](#microsoftcommunicationrouterworkerofferdeclined) | `Worker` | ワーカーに対するオファーが拒否されました |
| [`RouterWorkerOfferRevoked`](#microsoftcommunicationrouterworkerofferrevoked)  | `Worker` | ワーカーに対するオファーが取り消されました |
| [`RouterWorkerOfferExpired`](#microsoftcommunicationrouterworkerofferexpired)  | `Worker` | ワーカーに対するオファーの有効期限が切れました |
| [`RouterWorkerRegistered`](#microsoftcommunicationrouterworkerregistered)  | `Worker` | ワーカーが登録されました |
| [`RouterWorkerDeregistered`](#microsoftcommunicationrouterworkerderegistered)  | `Worker` | ワーカーが登録解除されました |

## <a name="router-events"></a>ルーター イベント

### <a name="microsoftcommunicationrouterjobreceived"></a>Microsoft.Communication.RouterJobReceived

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "acdf8fa5-8ab4-4a65-874a-c1d2a4a97f2e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "PendingClassification",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "queue-id",
    "priority": 0,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | 設定可能な値 <ul> <li>PendingClassification</li> </ul> | このイベントが送信されても、分類プロセスはまだ実行されていません。 |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null`ジョブに対して `queueId` が指定されている場合 |
| queueId | `string` | ✔️ | | `null`ジョブに対して `classificationPolicyId` が指定されている場合 |
| priority | `int` | ✔️ | | `classificationPolicyId` が指定されている場合は null。 直接キューの割り当てがある場合は null 以外の値。 |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

### <a name="microsoftcommunicationrouterjobclassified"></a>Microsoft.Communication.RouterJobClassified

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/queue/{queue-id}",
  "data": {
    "queueInfo": {
      "id": "625fec06-ab81-4e60-b780-f364ed96ade1",
      "name": "Queue 1",
      "labels": {
        "Language": "en",
        "Product": "Office",
        "Geo": "NA"
      }
    },
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassified",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| queueInfo | `QueueInfo` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null`ジョブに `queueId` が指定されている場合 (直接キューの割り当て) |
| queueId | `string` | ✔️ | | `null`ジョブに対して `classificationPolicyId` が指定されている場合 |
| priority | `int` | ❌ |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

### <a name="microsoftcommunicationrouterjoblabelsupdated"></a>Microsoft.Communication.RouterJobLabelsUpdated

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "Queued",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labelsAddedOrChanged": {
      "English": "5",
      "Office": "7"
    },
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken",
      "English": "5",
      "Office": "7"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobLabelsUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | 設定可能な値 <ul> <li>PendingClassification</li> <li>キューに登録済み</li> <li>割り当て済み</li> <li>完了</li> <li>クローズ</li> <li>Canceled</li> <li>ClassificationFailed</li> </ul> |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null`ジョブに対して `queueId` が指定されている場合 |
| queueId | `string` | ✔️ | | `null`ジョブに対して `classificationPolicyId` が指定されている場合 |
| priority | `int` | ❌ |
| labelsAddedOrChanged | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づいて追加または変更されたラベル。 |
| labels | `Dictionary<string, object>` | ✔️ | | ジョブに関連付けられているラベルの完全なセット。 |

### <a name="microsoftcommunicationrouterjobclassificationfailed"></a>Microsoft.Communication.RouterJobClassificationFailed

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/classificationpolicy/{classificationpolicy-id}",
  "data": {
    "errors": [
      {
        "code": null,
        "message": "Classification failed due to <reason>",
        "target": null,
        "innerError": null,
        "details": null
      }
    ],
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassificationFailed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| エラー| `List<CommunicationError>` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ❌ | |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

### <a name="microsoftcommunicationrouterjobcompleted"></a>Microsoft.Communication.RouterJobCompleted

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "queue-id",
    "assignmentId": "6f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
    "workerId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCompleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| assignmentId| `string` | ❌ | |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |
| workerId | `string` | ❌ | |

### <a name="microsoftcommunicationrouterjobclosed"></a>Microsoft.Communication.RouterJobClosed

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "",
    "dispositionCode": "",
    "workerId": "",
    "assignmentId": "",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClosed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| dispositionCode| `string` | ✔️ | | ユーザー入力に基づく |
| workerId | `string` | ❌ | |
| assignmentId | `string` | ❌ | |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

### <a name="microsoftcommunicationrouterjobcancelled"></a>Microsoft.Communication.RouterJobCancelled

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/disposition/{disposition-code}",
  "data": {
    "note": "Cancelled due to <reason>",
    "dispositionCode": "100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "queueId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCancelled",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| note| `string` | ✔️ | | ユーザー入力に基づく |
| dispositionCode| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ✔️ | | 分類に成功した後でジョブが取り消された場合は null 以外。 |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

### <a name="microsoftcommunicationrouterjobexceptiontriggered"></a>Microsoft.Communication.RouterJobExceptionTriggered

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "exceptionRuleId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionTriggered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| exceptionRuleId| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

### <a name="microsoftcommunicationrouterjobexceptioncleared"></a>Microsoft.Communication.RouterJobExceptionCleared

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionCleared",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| labels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

## <a name="worker-events"></a>ワーカー イベント

### <a name="microsoftcommunicationrouterworkerofferissued"></a>Microsoft.Communication.RouterWorkerOfferIssued

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "525fec06-ab81-4e60-b780-f364ed96ade1",
    "offerTimeUtc": "2021-06-23T02:43:30.3847144Z",
    "expiryTimeUtc": "2021-06-23T02:44:30.3847674Z",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferIssued",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId| `string` | ❌ |
| offerTimeUtc | `DateTimeOffset` | ❌ |
| expiryTimeUtc| `DateTimeOffset` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |

### <a name="microsoftcommunicationrouterworkerofferaccepted"></a>Microsoft.Communication.RouterWorkerOfferAccepted

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "assignmentId": "765fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferAccepted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |
| assignmentId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferdeclined"></a>Microsoft.Communication.RouterWorkerOfferDeclined

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferDeclined",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferrevoked"></a>Microsoft.Communication.RouterWorkerOfferRevoked

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferRevoked",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferexpired"></a>Microsoft.Communication.RouterWorkerOfferExpired

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferExpired",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerregistered"></a>Microsoft.Communication.RouterWorkerRegistered

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
    "totalCapacity": 100,
    "queueAssignments": [
      {
        "id": "MyQueueId2",
        "name": "Queue 3",
        "labels": {
          "Language": "en",
          "Product": "Office",
          "Geo": "NA"
        }
      }
    ],
    "labels": {
      "x": "111",
      "y": "111"
    },
    "channelConfigurations": [
      {
        "channelId": "FooVoiceChannelId",
        "capacityCostPerJob": 10
      }
    ]
  },
  "eventType": "Microsoft.Communication.RouterWorkerRegistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| totalCapacity | `int` | ❌ |
| queueAssignments| `List<QueueInfo>` | ❌ |
| labels| `Dictionary<string, object>` | ✔️ | | ユーザー入力に基づく |
| channelConfigurations| `List<ChannelConfiguration>` | ❌ |

### <a name="microsoftcommunicationrouterworkerderegistered"></a>Microsoft.Communication.RouterWorkerDeregistered

[イベント カタログに戻る](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
  },
  "eventType": "Microsoft.Communication.RouterWorkerDeregistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```
**属性の一覧**

| 属性 | Type | Nullable |説明 | メモ |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
