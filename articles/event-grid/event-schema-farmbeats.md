---
title: Event Grid ソースとしての Azure FarmBeats (プレビュー)
description: Azure Event Grid で Azure FarmBeats イベント用に提供されているプロパティとスキーマについて説明します
ms.topic: conceptual
ms.date: 06/06/2021
ms.openlocfilehash: 552a676fcfc457a662276aa7b5948670f700d208
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726734"
---
# <a name="azure-farmbeats-as-event-grid-source-preview"></a>Event Grid ソースとしての Azure FarmBeats (プレビュー)
この記事では、Azure FarmBeats イベントのプロパティとスキーマについて説明します。  イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 

## <a name="available-event-types"></a>使用可能なイベントの種類

|イベント名 | 説明|
|-----|----|
|Microsoft.AgFoodPlatform.FarmerChanged|農家が作成、更新、削除されたときに発行されます。 
|Microsoft.AgFoodPlatform.FarmChanged| 農場が作成、更新、削除されたときに発行されます。
|Microsoft.AgFoodPlatform.BoundaryChanged|境界が作成、更新、削除されたときに発行されます。
|Microsoft.AgFoodPlatform.FieldChanged|田畑が作成、更新、削除されたときに発行されます。
|Microsoft.AgFoodPlatform.SeasonalFieldChanged|季節限定の田畑が作成、更新、削除されたときに発行されます。
|Microsoft.AgFoodPlatform.SeasonChanged|季節が作成、更新、削除されたときに発行されます。
|Microsoft.AgFoodPlatform.CropChanged|作物が作成、更新、削除されたときに発行されます。
|Microsoft.AgFoodPlatform.CropVarietyChanged|作物の品種が作成、更新、削除されたときに発行されます。
|Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged| 衛星データ インジェスト ジョブの状態が変化したときに発行されます。たとえば、ジョブが作成された、進行した、完了したときです。
|Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged|気象データ インジェスト ジョブの状態が変化したときに発行されます。たとえば、ジョブが作成された、進行した、完了したときです。
|Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged| 農作業データ インジェスト ジョブの状態が変化したときに発行されます。たとえば、ジョブが作成された、進行した、完了したときです。
|Microsoft.AgFoodPlatform.ApplicationDataChanged|アプリケーション データが作成、更新、削除されたときに発行されます。 このイベントは、農作業データに関連付けられます。
|Microsoft.AgFoodPlatform.HarvestingDataChanged|収穫データが作成、更新、削除されたときに発行されます。このイベントは、農作業データに関連付けられます。
|Microsoft.AgFoodPlatform.TillageDataChanged|耕作地データが作成、更新、削除されたときに発行されます。 このイベントは、農作業データに関連付けられます。
|Microsoft.AgFoodPlatform.PlantingDataChanged|植え付けデータが作成、更新、削除されたときに発行されます。このイベントは、農作業データに関連付けられます。

## <a name="event-properties"></a>イベントのプロパティ
各 FarmBeats イベントは 2 つの部分で構成されます。1 つはイベント間で共通で、もう 1 つ (データ オブジェクト) は各イベントに固有のプロパティが含まれます。 

イベント間で共通する部分について、次のスキーマで詳しく説明します。

### <a name="event-grid-event-schema"></a>Event Grid イベント スキーマ
イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | App Configuration イベント データ。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |


次の表に、各イベントのデータ オブジェクト内のプロパティについて詳しく説明します。

*FarmerChanged、FarmChanged、SeasonChanged、CropChanged、CropVarietyChanged FarmBeats イベントの場合、データ オブジェクトには次のプロパティが含まれます。*

|プロパティ | Type| 説明|
|----| ----| ----|
id| string| リソースのユーザー定義 ID (農場 ID、農家 ID など)
actionType| string| イベントの発行中にトリガーされた変更を示します。 適用可能な値は、作成済み、更新済み、削除済みです
status| string| リソースのユーザー定義の状態が格納されます。
properties| object| ユーザー定義のキー/値のペアが格納されます
modifiedDateTime| date-time|リソースが最後に変更された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
createdDateTime|date-time|リソースが作成された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
eTag|   string| オプティミスティック同時実行制御を実装します
description| string|    リソースの説明テキスト


*BoundaryChanged FarmBeats イベントには、次のデータ オブジェクトが含まれます。*

|プロパティ | Type| 説明|
|----| ----| ----|
id| string| 境界のユーザー定義 ID
actionType| string| イベントの発行中にトリガーされた変更を示します。 適用可能な値は、作成済み、更新済み、削除済みです。
parentId|   string| 境界が属している親の ID。
parentType| string| 境界が属している親の種類。
isPrimary|  boolean|    境界がプライマリかどうかを示します。
farmerId|   string| 境界に関連付けられている農家の ID が格納されます。
properties| object| ユーザー定義のキー/値のペアが格納されます。
modifiedDateTime| date-time|リソースが最後に変更された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
createdDateTime|date-time|リソースが作成された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
status| string| リソースのユーザー定義の状態が格納されます。
eTag|   string| オプティミスティック同時実行制御を実装します。
description| string|    リソースの説明テキスト。

*FieldChanged FarmBeats イベントには、次のデータ オブジェクトが含まれます。*

プロパティ|   Type|   説明
|----| ----| ----|
id| string| 田畑のユーザー定義 ID
farmId| string| 田畑が関連付けられている農場のユーザー定義 ID
farmerId|   string| 田畑が関連付けられている農家のユーザー定義 ID
name|   string| 田畑のユーザー定義名
actionType| string| イベントの発行中にトリガーされた変更を示します。 適用可能な値は、作成済み、更新済み、削除済みです
properties| object| ユーザー定義のキー/値のペアが格納されます
modifiedDateTime| date-time|リソースが最後に変更された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
createdDateTime|date-time|リソースが作成された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
status| string| リソースのユーザー定義の状態が格納されます。
eTag|   string| オプティミスティック同時実行制御を実装します
description|string| リソースの説明テキスト

*SeasonalFieldChanged FarmBeats イベントには、次のデータ オブジェクトが含まれます。*

プロパティ|   Type|   説明
|----| ----| ----|
id| string| 季節限定の田畑のユーザー定義 ID
farmId| string| 季節限定の田畑が関連付けられている農場のユーザー定義 ID
farmerId|   string| 季節限定の田畑が関連付けられている農家のユーザー定義 ID
seasonId|   string| 季節限定の田畑が関連付けられている季節のユーザー定義 ID
fieldId|    string| 季節限定の田畑が関連付けられている田畑のユーザー定義 ID
name|   string| 季節限定の田畑のユーザー定義名
actionType| string| イベントの発行中にトリガーされた変更を示します。 適用可能な値は、作成済み、更新済み、削除済みです
properties| object| ユーザー定義のキー/値のペアが格納されます
modifiedDateTime| date-time|リソースが最後に変更された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
createdDateTime|date-time|リソースが作成された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
status| string| リソースのユーザー定義の状態が格納されます。
eTag|   string| オプティミスティック同時実行制御を実装します
description| string|    リソースの説明テキスト

*SatelliteDataIngestionJobChanged、WeatherDataIngestionJobChanged、および FarmOperationsDataIngestionJobChanged FarmBeats イベントには、次のデータ オブジェクトが含まれます。*

プロパティ|   Type|   説明
|----|----|----|
id|String| ジョブの一意の ID。
name| string| ジョブのユーザー定義名。
status|string|ジョブがなり得るさまざまな状態。
isCancellationRequested| boolean|ジョブの取り消しが要求されたときに設定されるフラグ。
description|string| ジョブの説明テキスト。
farmerId|string| ジョブが作成された農家の ID。
message|string| ジョブの詳細をキャプチャするステータス メッセージ。
lastActionDateTime|date-time|ジョブで最後のアクションが実行された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
createdDateTime|date-time|リソースが作成された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。


*FarmBeats 農作業データ変更イベント (ApplicationDataChanged、HarvestingDataChanged、PlantingDataChanged、TillageDataChanged など) には、次のデータ オブジェクトが含まれます。*

プロパティ|   Type|   説明
|----|----|----|
id| string| リソースのユーザー定義 ID (農場 ID、農家 ID など)
status| string| ジョブの状態が格納されます。 
actionType|string|
source| string| ジョブに関する詳細を示す FarmBeats からのメッセージ。    
modifiedDateTime| date-time|リソースが最後に変更された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
createdDateTime|date-time|リソースが作成された日時 (サンプル形式: yyyy-MM-ddTHH:mm:ssZ)。
eTag|   string| オプティミスティック同時実行制御を実装します
description|string| リソースの説明テキスト


## <a name="sample-events"></a>サンプル イベント 
次のイベント サンプルは、イベント通知を表します。

**イベントの種類: Microsoft.AgFoodPlatform.FarmerChanged**

```json
{
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:53:28Z",
      "eTag": "860197cc-0000-0700-0000-60420da80000",
      "id": "UNIQUE-FARMER-ID",
      "name": "sample farmer",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:53:28Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "81fbe1de-4ae4-4284-964f-59da80a6bfe7",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmerChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:53:28.2783745Z"
  }
```

**イベントの種類: Microsoft.AgFoodPlatform.FarmChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:55:57Z",
      "eTag": "8601e3d5-0000-0700-0000-60420e3d0000",
      "id": "UNIQUE-FARM-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:55:57Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "31a31be7-51fb-48f3-adfd-6fb4400be002",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farms/UNIQUE-FARM-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:55:57.6026173Z"
  }
```
**イベントの種類: Microsoft.AgFoodPlatform.BoundaryChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "parentId": "OPTIONAL-UNIQUE-FIELD-ID",
      "isPrimary": true,
      "actionType": "Created",
      "modifiedDateTime": "2021-03-05T11:15:29Z",
      "eTag": "860109f7-0000-0700-0000-604212d10000",
      "id": "UNIQUE-BOUNDARY-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:15:29Z"
    },
    "id": "3d3453b2-5a94-45a7-98eb-fc2979a00317",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/boundaries/UNIQUE-BOUNDARY-ID",
    "eventType": "Microsoft.AgFoodPlatform.BoundaryChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:15:29.4797354Z"
  }
  ```

**イベントの種類: Microsoft.AgFoodPlatform.FieldChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:58:43Z",
      "eTag": "860124dc-0000-0700-0000-60420ee30000",
      "id": "UNIQUE-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:58:43Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "1ad04ed0-ac05-4c4e-aa3d-87facb3cc97c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/fields/UNIQUE-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.FieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:58:43.3222921Z"
  }
  ```
**イベントの種類: Microsoft.AgFoodPlatform.SeasonalFieldChanged**
```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "seasonId": "UNIQUE-SEASON-ID",
      "fieldId": "UNIQUE-FIELD-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:24:56Z",
      "eTag": "8701300b-0000-0700-0000-604215080000",
      "id": "UNIQUE-SEASONAL-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:24:56Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ff59a0a3-6226-42c0-9e70-01da55efa797",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/seasonalFields/UNIQUE-SEASONAL-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonalFieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:24:56.4210287Z"
  }
```
**イベントの種類: Microsoft.AgFoodPlatform.SeasonChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:18:38Z",
      "eTag": "86019afd-0000-0700-0000-6042138e0000",
      "id": "UNIQUE-SEASON-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:18:38Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "63989475-397b-4b92-8160-8743bf8e5804",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/seasons/UNIQUE-SEASON-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:18:38.5804699Z"
  }
  ```

  **イベントの種類: Microsoft.AgFoodPlatform.CropChanged**

```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:03:48Z",
      "eTag": "8601c4e5-0000-0700-0000-604210150000",
      "id": "UNIQUE-CROP-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:03:48Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "4c59a797-b76d-48ec-8915-ceff58628f35",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/crops/UNIQUE-CROP-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:03:49.0590658Z"
  }
  ```

**イベントの種類: Microsoft.AgFoodPlatform.CropVarietyChanged**

```json
  {
    "data": {
      "cropId": "UNIQUE-CROP-ID",
      "actionType": "Created",
      "status": "string",
      "modifiedDateTime": "2021-03-05T11:10:21Z",
      "eTag": "860130ef-0000-0700-0000-6042119d0000",
      "id": "UNIQUE-CROP-VARIETY-ID",
      "name": "Sample status",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:10:21Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "29aefdb9-d648-442c-81f8-694f3f47583c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/cropVarieties/UNIQUE-CROP-VARIETY-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropVarietyChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:10:21.4572495Z"
  }
```
**イベントの種類: Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE - FARMER - ID",
      "message": "Created job 'job1' to fetch satellite data for boundary 'boundary1' from startDate '06/01/2021' to endDate '06/01/2021' (both inclusive).",
      "status": "Waiting",
      "lastActionDateTime": "2021-06-01T11:25:37.8634096Z",
      "isCancellationRequested": false,
      "id": "UNIQUE - JOB - ID",
      "name": "samplejob",
      "description": "Sample for testing events",
      "createdDateTime": "2021-06-01T11:25:32.3421173Z",
      "properties": {
        "key1": "testvalue1",
        "key2": 123.45
      }
    },
    "id": "925c6be2-6561-4572-b7dd-0f3084a54567",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/{UNIQUE-FARMER-ID}/satelliteDataIngestionJobs/{UNIQUE-JOB-ID}",
    "eventType": "Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:25:37.8634764Z"
  }
]
```
**イベントの種類: Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Created job to fetch weather data for job name 'job2', farmer id 'farmer2' and boundary id 'boundary2'.",
      "status": "Running",
      "lastActionDateTime": "2021-06-01T11:22:27.9031003Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "createdDateTime": "2021-06-01T07:13:54.8843617Z"
    },
    "id": "ec30313a-ff2f-4b50-882b-31188113c15b",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/weatherDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:22:27.9031302Z"
  }
]

```
**イベントの種類: Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Job completed successfully. Data statistics:{ Processed operations count = 6, Organizations count = 1, Processed organizations count = 1, Processed fields count = 2, Operations count = 6, ShapefileAttachmentsCount = 0, Fields count = 2 }",
      "status": "Succeeded",
      "lastActionDateTime": "2021-06-01T11:30:54.733625Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "name": "sample-job",
      "description": "sample description",
      "createdDateTime": "2021-06-01T11:30:39.0905288Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ebdbb7a1-ad28-4af7-b3a2-a4a3a2dd1b4f",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farmOperationDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:30:54.733671Z"
  }
]

```
**イベントの種類: Microsoft.AgFoodPlatform.ApplicationDataChanged**

```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:27:24Z",
      "eTag": "87011311-0000-0700-0000-6042159c0000",
      "id": "UNIQUE-APPLICATION-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:27:24Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "e499f6c4-63ba-4217-8261-0c6cb0e398d2",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/applicationData/UNIQUE-APPLICATION-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.ApplicationDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:27:24.164612Z"
  }
```

**イベントの種類: Microsoft.AgFoodPlatform.HarvestDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:33:41Z",
      "eTag": "8701141b-0000-0700-0000-604217150000",
      "id": "UNIQUE-HARVEST-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:33:41Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "dc3837c0-1eed-4bfa-88b6-d018cf6af4db",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/harvestData/UNIQUE-HARVEST-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.HarvestDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:33:41.3434992Z"
  }
```
**イベントの種類: Microsoft.AgFoodPlatform.TillageDataChanged**
```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "sample source",
      "modifiedDateTime": "2021-06-15T10:31:07Z",
      "eTag": "6405f027-0000-0100-0000-60c8816b0000",
      "id": "c9858c3f-fb94-474a-a6de-103b453df976",
      "createdDateTime": "2021-06-15T10:31:07Z",
      "name": "sample name",
      "description":"sample description"
      "properties": {
        "_orgId": "498221",
        "_fieldId": "e61b83f4-3a12-431e-8010-596f2466dc27",
        "_cropSeason": "2010"
      }
    },
    "id": "f06f6686-1fa8-41fd-be99-46f40f495cce",
    "topic": "/subscriptions/da9091ec-d18f-456c-9c21-5783ee7f4645/resourceGroups/internal-farmbeats-resources/providers/Microsoft.AgFoodPlatform/farmBeats/internal-eus",
    "subject": "/farmers/10e3d7bf-c559-48be-af31-4e00df83bfcd/tillageData/c9858c3f-fb94-474a-a6de-103b453df976",
    "eventType": "Microsoft.AgFoodPlatform.TillageDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-15T10:31:07.6778047Z"
  }
```

**イベントの種類: Microsoft.AgFoodPlatform.PlantingDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:41:18Z",
      "eTag": "8701242a-0000-0700-0000-604218de0000",
      "id": "UNIQUE-PLANTING-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:41:18Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "42589c7f-4e16-4a4d-9314-d611c822f7ac",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/plantingData/UNIQUE-PLANTING-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.PlantingDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:41:18.1744322Z"
  }
```



## <a name="next-steps"></a>次のステップ
* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
