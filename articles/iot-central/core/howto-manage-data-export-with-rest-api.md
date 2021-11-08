---
title: Azure IoT Central で REST API を使用してデータ エクスポートを管理する
description: IoT Central REST API を使用してアプリケーション内でデータ エクスポートを管理する方法
author: v-krishnag
ms.author: v-krishnag
ms.date: 10/18/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cb538b6c8777a9aeb1d02fe705a87831f7aaf5b9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433783"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-data-exports"></a>IoT Central REST API を使用してデータ エクスポートを管理する方法

IoT Central REST API を使用して、IoT Central アプリケーションと統合するクライアント アプリケーションを開発できます。 REST API を使用して、IoT Central アプリケーションで[データ エクスポート](howto-export-data.md)の作成と管理を行えます。

すべての IoT Central REST API 呼び出しに承認ヘッダーが必要です。 詳細については、「[IoT Central REST API 呼び出しを認証および承認する方法](howto-authorize-rest-api.md)」を参照してください。

IoT Central REST API のリファレンス ドキュメントについては、「[Azure IoT Central REST API リファレンス](/rest/api/iotcentral/)」をご覧ください。

## <a name="data-export"></a>データのエクスポート

IoT Central のデータ エクスポート機能を使用すると、テレメトリ、プロパティの変更、デバイス接続イベント、デバイス ライフサイクル イベント、デバイス テンプレート ライフサイクル イベントを、Azure Event Hubs、Azure Service Bus、Azure Blob Storage、Webhook エンドポイントなどの宛先にストリーミングできます。

各データ エクスポート定義では、1 つ以上の宛先にデータを送信できます。 エクスポート定義を作成する前に、宛先の定義を作成します。

### <a name="create-or-update-a-destination"></a>宛先を作成または更新する

次の要求を使用して、宛先定義を作成または更新します。

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

* destinationId - 宛先の一意の ID。

次の例は、BLOB ストレージの宛先を作成する要求本文を示しています。

```json
{
  "displayName": "Blob Storage Destination",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

要求本文には、いくつかの必須フィールドがあります。

* `displayName`: 宛先の表示名。
* `type`: 宛先オブジェクトの種類。`blobstorage@v1`、`dataexplorer@v1`、`eventhubs@v1`、`servicebusqueue@v1`、`servicebustopic@v1`、`webhook@v1` のいずれかを指定できます。
* `connectionString`: 宛先リソースにアクセスするための接続文字列。
* `containerName`: BLOB ストレージの宛先の場合、データを書き込むコンテナーの名前。

この要求に対する応答は、次の例のようになります。 

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="get-a-destination-by-id"></a>ID で宛先を取得する

アプリケーションから宛先の詳細を取得するには、次の要求を使用します。

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

この要求に対する応答は、次の例のようになります。

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-destinations"></a>宛先のリスト

アプリケーションから宛先のリストを取得するには、次の要求を使用します。

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations?api-version=1.1-preview
```

この要求に対する応答は、次の例のようになります。 

```json
{
    "value": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
            "displayName": "Blob Storage Destination",
            "type": "blobstorage@v1",
            "authorization": {
                "type": "connectionString",
                "connectionString": DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
                "containerName": "central-data"
            },
            "status": "waiting"
        },
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9",
            "displayName": "Webhook destination",
            "type": "webhook@v1",
            "url": "http://requestbin.net/r/f7x2i1ug",
            "headerCustomizations": {},
            "status": "error",
        }
        }
    ]
}
```

### <a name="patch-a-destination"></a>宛先の修正

```http
PATCH https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

これを使用して、エクスポートへの増分更新を実行できます。 サンプル要求本文は、次の例のようになります。ここでは、`displayName` が宛先に更新されます。

```json
{
  "displayName": "Blob Storage",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

この要求に対する応答は、次の例のようになります。

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}   
```

### <a name="delete-a-destination"></a>宛先の削除

宛先を削除するには、次の要求を使用します。

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

### <a name="create-or-update-an-export-definition"></a>エクスポート定義を作成または更新する

次の要求を使用して、データ エクスポート定義を作成または更新します。

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

次の例は、デバイス テレメトリ用のエクスポート定義を作成する要求本文を示しています。

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150"
        }
    ]
}
```

要求本文には、いくつかの必須フィールドがあります。

* `displayName`: エクスポートの表示名。
* `enabled`: エクスポートのデータ送信を開始/停止する切り替え。
* `source`: エクスポートするデータの種類。
* `destinations`: エクスポートのデータ送信先となる宛先の一覧。 宛先の ID は、アプリケーションに既に存在している必要があります。

エクスポートに詳細を追加するために使用できる、いくつかの省略可能なフィールドがあります。

* `enrichments`: 送信される各メッセージに含める追加の情報。 データはキーと値のペアのセットとして表されます。ここで、キーは出力メッセージに表示されるエンリッチメントの名前であり、値は送信するデータを識別します。
* `filter`: ソースからエクスポートするイベントを定義するクエリ。

この要求に対する応答は、次の例のようになります。 

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "starting"
}
```

### <a name="get-an-export-by-id"></a>ID でエクスポートを取得する

アプリケーションからエクスポート定義の詳細を取得するには、次の要求を使用します。

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

この要求に対する応答は、次の例のようになります。

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-export-definitions"></a>エクスポート定義のリスト

アプリケーションからエクスポート定義のリストを取得するには、次の要求を使用します。

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports?api-version=1.1-preview
```

この要求に対する応答は、次の例のようになります。 

```json
{
  "value": [
    {
      "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "starting"
    },
    {
      "id": "802894c4-33bc-4f1e-ad64-e886f315cece",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "healthy"
    }
  ]
}
```

### <a name="patch-an-export-definition"></a>エクスポート定義を更新する

```http
PATCH https://{subdomain}.{baseDomain}/dataExport/exports/{exportId}?api-version=1.1-preview
```

これを使用して、エクスポートへの増分更新を実行できます。 サンプル要求本文は、次の例のようになります。ここでは、エクスポートへの `enrichments` が更新されます。

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value 2"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ]
}
```

この要求に対する応答は、次の例のようになります。

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "healthy"
}
```

### <a name="delete-an-export-definition"></a>エクスポート定義を削除する

エクスポート定義を削除するには、次の要求を使用します。

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

## <a name="next-steps"></a>次の手順

REST API を使用してデータ エクスポートを管理する方法を学習したので、推奨される次のステップは「[IoT Central REST API を使用してデバイス テンプレートを管理する方法](howto-manage-device-templates-with-rest-api.md)」です。
