---
title: Event Grid ソースとしての Azure App Service
description: この記事では、Event Grid イベント ソースとして Azure App Service を使用する方法について説明します。 スキーマと、チュートリアルおよび操作方法に関する記事へのリンクを提供します。
author: jasonfreeberg
ms.topic: conceptual
ms.date: 03/06/2021
ms.author: jafreebe
ms.openlocfilehash: 404fac634a628da49dee72b10b52785820fe1bf6
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443779"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Event Grid ソースとしての Azure App Service

この記事では、Azure App Service イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 また、Azure App Service をイベント ソースとして使用するためのクイック スタートとチュートリアルの一覧も示されています。

## <a name="available-event-types"></a>使用可能なイベントの種類

Azure App Service から出力されるイベントの種類は次のとおりです。

|    イベントの種類                                             |    説明                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    バックアップが開始されたときにトリガーされます                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    バックアップが完了したときにトリガーされます                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    バックアップが失敗したときにトリガーされます                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    バックアップからの復元が開始されたときにトリガーされます        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    バックアップからの復元が完了したときにトリガーされます      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    バックアップからの復元に失敗したときにトリガーされます         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    スロットのスワップが開始されたときにトリガーされます                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    スロットのスワップが完了したときにトリガーされます                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    スロットのスワップに失敗したときにトリガーされます                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    プレビューでのスロットのスワップが開始されたときにトリガーされます           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    プレビューでのスロットのスワップが取り消されたときにトリガーされます    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    サイトが再起動されたときにトリガーされます                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    サイトが停止されたときにトリガーされます                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    サイトのアプリ設定が変更されたときにトリガーされます             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    App Service プランが更新されたときにトリガーされます                 |

## <a name="properties-common-to-all-events"></a>すべてのイベントに共通のプロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。
このセクションには、各イベントでそのデータがどのように見えるかの例が含まれています。 各イベントのトップレベルのデータを次に示します。

|     プロパティ          |     種類     |     説明                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    string    |    イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。                                      |
|    `subject`            |    string    |    発行元が定義したイベントの対象のパス。                                                                                              |
|    `eventType`          |    string    |    このイベント ソース用に登録されたイベントの種類のいずれか。                                                                                  |
|    `eventTime`          |    string    |    プロバイダーの UTC 時刻に基づくイベントの生成時刻。                                                                         |
|    `id`                 |    string    |    イベントの一意識別子。                                                                                                            |
|    `data`               |    object    |    Blob Storage イベントのデータ。                                                                                                                    |
|    `dataVersion`        |    string    |    データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。                                                          |
|    `metadataVersion`    |    string    |    イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。    |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。
このセクションには、各イベントでそのデータがどのように見えるかの例が含まれています。 各イベントのトップレベルのデータを次に示します。

|     プロパティ          |     種類     |     説明                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    string    |    イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。                                      |
|    `subject`            |    string    |    発行元が定義したイベントの対象のパス。                                                                                              |
|    `type`          |    string    |    このイベント ソース用に登録されたイベントの種類のいずれか。                                                                                  |
|    `time`          |    string    |    プロバイダーの UTC 時刻に基づくイベントの生成時刻。                                                                         |
|    `id`                 |    string    |    イベントの一意識別子。                                                                                                            |
|    `data`               |    object    |    Blob Storage イベントのデータ。                                                                                                                    |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---

## <a name="example-events"></a>イベントの例

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted、BackupOperationCompleted、BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

データ オブジェクトには次のプロパティが含まれます。

|    プロパティ                |    種類      |    説明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    アプリでのアクションの詳細                                                                                       |
|    `action`                  |    string    |    操作のアクションの種類                                                                                   |
|    `name`                    |    string    |    このイベントが発生した Web サイトの名前                                                                          |
|    `clientRequestId`         |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成されたクライアント要求 ID         |
|    `correlationRequestId`    |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された相関要求 ID    |
|    `requestId`               |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された要求 ID                |
|    `address`                 |    string    |    この操作の HTTP 要求 URL                                                                                |
|    `verb`                    |    string    |    この操作の HTTP 動詞                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted、RestoreOperationCompleted、RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

データ オブジェクトには次のプロパティが含まれます。

|    プロパティ                |    種類      |    説明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    アプリでのアクションの詳細                                                                                       |
|    `action`                  |    string    |    操作のアクションの種類                                                                                   |
|    `name`                    |    string    |    このイベントが発生した Web サイトの名前                                                                          |
|    `clientRequestId`         |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成されたクライアント要求 ID         |
|    `correlationRequestId`    |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された相関要求 ID    |
|    `requestId`               |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された要求 ID                |
|    `address`                 |    string    |    この操作の HTTP 要求 URL                                                                                |
|    `verb`                    |    string    |    この操作の HTTP 動詞                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted、SlotSwapCompleted、SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

データ オブジェクトには次のプロパティが含まれます。

|    プロパティ                |    種類      |    説明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    アプリでのアクションの詳細                                                                                       |
|    `action`                 |    string    |    操作のアクションの種類                                                                                   |
|    `name`                    |    string    |    このイベントが発生した Web サイトの名前                                                                          |
|    `clientRequestId`         |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成されたクライアント要求 ID         |
|    `correlationRequestId`    |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された相関要求 ID    |
|   `requestId`               |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された要求 ID                |
|    `address`                 |    string    |    この操作の HTTP 要求 URL                                                                                |
|    `verb`                    |    string    |    この操作の HTTP 動詞                                                                                       |
|    `sourceSlot`              |    string    |    スワップのソース スロット                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted、SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

データ オブジェクトには次のプロパティが含まれます。

|    プロパティ                |    種類      |    説明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    アプリでのアクションの詳細                                                                                       |
|    `action`                 |    string    |    操作のアクションの種類                                                                                   |
|    `name`                    |    string    |    このイベントが発生した Web サイトの名前                                                                          |
|    `clientRequestId`         |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成されたクライアント要求 ID         |
|    `correlationRequestId`    |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された相関要求 ID    |
|    `requestId`               |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された要求 ID                |
|    `address`                 |    string    |    この操作の HTTP 要求 URL                                                                                |
|    `verb`                    |    string    |    この操作の HTTP 動詞                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted、AppUpdated.Stopped、AppUpdated.ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

データ オブジェクトには、次のプロパティがあります。

|    プロパティ                |    種類      |    説明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    アプリでのアクションの詳細                                                                                       |
|    `action`                  |    string    |    操作のアクションの種類                                                                                   |
|    `name`                    |    string    |    このイベントが発生した Web サイトの名前                                                                          |
|    `clientRequestId`         |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成されたクライアント要求 ID         |
|    `correlationRequestId`    |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された相関要求 ID    |
|    `requestId`               |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された要求 ID                |
|    `address`                 |    string    |    この操作の HTTP 要求 URL                                                                                |
|    `verb`                    |    string    |    この操作の HTTP 動詞                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

データ オブジェクトには、次のプロパティがあります。

|    プロパティ                         |    種類      |    説明                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    object    |    App Service プランのアクションの詳細                                                                          |
|    `stampKind`                        |    string    |    App Service プランがある環境の種類                                                                     |
|    `action`                           |    string    |    App Service プランでのアクションの種類                                                                            |
|    `status`                           |    string    |    App Service プランでの操作の状態                                                                   |
|    `sku`                              |    object    |    App Service プランの SKU                                                                                       |
|    `name`                             |    string    |    App Service プランの名前                                                                                      |
|    `Tier`                             |    string    |    App Service プランのサービス レベル                                                                                      |
|    `Size`                             |    string    |    App Service プランのサイズ                                                                                      |
|    `Family`                           |    string    |    App Service プランのファミリ                                                                                        |
|    `Capacity`                         |    string    |    App Service プランの容量                                                                                      |
|    `action`                           |    string    |    操作のアクションの種類                                                                                   |
|    `name`                             |    string    |    このイベントが発生した Web サイトの名前                                                                          |
|    `clientRequestId`                  |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成されたクライアント要求 ID         |
|    `correlationRequestId`             |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された相関要求 ID    |
|    `requestId`                        |    string    |    このイベントをトリガーしたサイト API 操作に対してアプリ サービスによって生成された要求 ID                |
|    `address`                         |    string    |    この操作の HTTP 要求 URL                                                                                |
|    `verb`                             |    string    |    この操作の HTTP 動詞                                                                                       |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、「[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)」を参照してください。