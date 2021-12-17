---
title: Azure IoT Central での REST API を使用したジョブの管理
description: IoT Central REST API を使用してアプリケーションでジョブの作成と管理を行う方法
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 47548b3c7e1fa7503e3f30c155755d6fe4f29031
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667048"
---
# <a name="how-to-use-the-iot-central-rest-api-to-create-and-manage-jobs"></a>IoT Central REST API を使用してジョブの作成と管理を行う方法

IoT Central REST API を使用して、IoT Central アプリケーションと統合するクライアント アプリケーションを開発できます。 REST API を使用して、IoT Central アプリケーションでジョブの作成と管理を行えます。 REST API を使用すると、次を行うことができます。

- アプリケーションでジョブを一覧表示し、ジョブの詳細を表示します。
- アプリケーションでジョブを作成して実行します。
- アプリケーションでジョブを停止、再開、再実行します。

> [!IMPORTANT]
> ジョブ API は現在、プレビュー中です。 この記事で説明されているすべての REST API 呼び出しには、`?api-version=preview` を含める必要があります。

この記事では、`/jobs/{job_id}` API を使用してデバイスを一括で制御する方法について説明します。 デバイスは個別に制御することもできます。

すべての IoT Central REST API 呼び出しに承認ヘッダーが必要です。 詳細については、「[IoT Central REST API 呼び出しを認証および承認する方法](howto-authorize-rest-api.md)」を参照してください。

IoT Central REST API のリファレンス ドキュメントについては、「[Azure IoT Central REST API リファレンス](/rest/api/iotcentral/)」をご覧ください。

> [!TIP]
> [プレビュー API](/rest/api/iotcentral/1.1-previewdataplane/jobs) には、新しい[組織機能](howto-create-organizations.md)のサポートが含まれています。

UI でジョブを作成および管理する方法については、「[Azure IoT Central アプリケーションでデバイスを一括管理する](howto-manage-devices-in-bulk.md)」を参照してください。

## <a name="job-payloads"></a>ジョブ ペイロード

この記事で説明する API の多くには、次の JSON スニペットのような定義が含まれています。

```json
{
  "id": "job-014",
  "displayName": "Set target temperature",
  "description": "Set target temperature for all thermostat devices",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "56"
    }
  ],
  "status": "complete"
}
```

次の表では、前の JSON スニペット内のフィールドについて説明します。

| フィールド | 説明 |
| ----- | ----------- |
| `id` | アプリケーション内のジョブの一意の ID。 |
| `displayName` | アプリケーション内のジョブの表示名。 |
| `description` | ジョブの説明。 |
| `group` | ジョブが適用されるデバイス グループの ID。 アプリケーション内のデバイス グループの一覧を取得するには、`deviceGroups` プレビュー REST API を使用します。 |
| `status` | ジョブの[状態](howto-manage-devices-in-bulk.md#view-job-status)。 `complete`、`cancelled`、`failed`、`pending`、`running`、`stopped` のいずれかです。 |
| `batch` | このセクションが存在する場合、ジョブ内のデバイスを[バッチ](howto-manage-devices-in-bulk.md#create-and-run-a-job)処理する方法を定義します。 |
| `batch/type` | 各バッチのサイズは、グループ内のデバイス合計の `percentage` か、デバイスの `number` です。 |
| `batch/value` | 各バッチ内のデバイスの割合またはデバイスの数のいずれか。 |
| `cancellationThreshold` | このセクションが存在する場合、ジョブの[キャンセルしきい値](howto-manage-devices-in-bulk.md#create-and-run-a-job)を定義します。 |
| `cancellationThreshold/batch` | `true` または `false`。 True の場合、キャンセルしきい値はバッチごとに設定されます。 `false` の場合、キャンセルしきい値はジョブ全体に適用されます。 |
| `cancellationThreshold/type` | ジョブのキャンセルしきい値は、デバイスの `percentage` または `number` のいずれかです。 |
| `cancellationThreshold/value` | キャンセルしきい値を定義するデバイスの割合またはデバイスの数のいずれか。 |
| `data` | ジョブによって実行される操作の配列。 |
| `data/type` | `PropertyJobData`、`CommandJobData`、または `CloudPropertyJobData` のいずれか |
| `data/target` | ターゲット デバイスのモデル ID。 |
| `data/path` | プロパティ、コマンド、またはクラウド プロパティの名前。 |
| `data/value` | 設定するプロパティ値、または送信するコマンド パラメーター。 |

## <a name="get-job-information"></a>ジョブ情報の取得

アプリケーション内のジョブの一覧を取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs?api-version=preview
```

この要求に対する応答は、次の例のようになります。

```json
{
  "value": [
    {
      "id": "job-006",
      "displayName": "Set customer name",
      "description": "Set the customer name cloud property",
      "group": "4fcbec3b-5ee8-4324-855d-0f03b56bcf7f",
      "data": [
        {
          "type": "CloudPropertyJobData",
          "target": "dtmi:modelDefinition:bojo9tfju:yfvu5gv2vl",
          "path": "CustomerName",
          "value": "Contoso"
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-005",
      "displayName": "Set target temperature",
      "description": "Set target temperature device property",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "data": [
        {
          "type": "PropertyJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "targetTemperature",
          "value": 56
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-004",
      "displayName": "Run device report",
      "description": "Call command to run the device reports",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "batch": {
        "type": "percentage",
        "value": 25
      },
      "cancellationThreshold": {
        "type": "percentage",
        "value": 10,
        "batch": false
      },
      "data": [
        {
          "type": "CommandJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "getMaxMinReport",
          "value": "2021-06-15T05:00:00.000Z"
        }
      ],
      "status": "complete"
    }
  ]
}
```

次の要求を使用して、ID で個々のジョブを取得します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004?api-version=preview
```

この要求に対する応答は、次の例のようになります。

```json
{
  "id": "job-004",
  "displayName": "Run device report",
  "description": "Call command to run the device reports",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "CommandJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "getMaxMinReport",
      "value": "2021-06-15T05:00:00.000Z"
    }
  ],
  "status": "complete"
}
```

次の要求を使用して、ジョブ内のデバイスの詳細を取得します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004/devices?api-version=preview
```

この要求に対する応答は、次の例のようになります。

```json
{
  "value": [
    {
      "id": "therm-01",
      "status": "completed"
    },
    {
      "id": "therm-02",
      "status": "completed"
    },
    {
      "id": "therm-03",
      "status": "completed"
    },
    {
      "id": "therm-04",
      "status": "completed"
    }
  ]
}
```

## <a name="create-a-job"></a>ジョブの作成

次の要求を使用して、ジョブ内のデバイスの詳細を取得します。

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006?api-version=preview
{
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ]
}
```

この要求に対する応答は、次の例のようになります。 最初のジョブの状況は、次に対する `pending` です:

```json
{
  "id": "job-006",
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ],
  "status": "pending"
}
```

## <a name="stop-resume-and-rerun-jobs"></a>ジョブの停止、再開、および再実行

次の要求を使用して、実行中のジョブを停止します。

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/stop?api-version=preview
```

要求が成功した場合は、 `204 - No Content` 応答が返されます。

次の要求を使用して、停止されたジョブを再開します。

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/resume?api-version=preview
```

要求が成功した場合は、 `204 - No Content` 応答が返されます。

次のコマンドを使用して、障害が発生したデバイス上の既存のジョブを再実行します。

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/rerun/rerun-001?api-version=preview
```

## <a name="next-steps"></a>次のステップ

REST API を使用してジョブを管理する方法を学習しました。お勧めする次のステップは、[REST API を使用した IoT Central アプリケーションの管理](/learn/modules/manage-iot-central-apps-with-rest-api/)です。
