---
title: Azure IoT Central データをエクスポートする | Microsoft Docs
description: Azure IoT Central アプリケーションから Azure Event Hubs、Azure Service Bus、および Azure Blob Storage にデータをエクスポートする方法
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 058fe9aea87879fe85dcbc6dcb864fd841fcb049
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026423"
---
# <a name="export-your-azure-iot-central-data"></a>Azure IoT Central データをエクスポートする



*このトピックでは、管理者に適用されます。*

この記事では、Azure IoT Central で連続データ エクスポートを使用して、**Azure Event Hubs**、**Azure Service Bus**、または **Azure Blob Storage** のインスタンスにデータをエクスポートする方法について説明します。 データは JSON 形式でエクスポートされ、テレメトリ、デバイス情報、デバイス テンプレート情報を含めることができます。 エクスポートされたデータは次のことに使用します。

- ウォーム パスの分析情報と分析。 このオプションには、Azure Stream Analytics でのカスタム ルールのトリガー、Azure Logic Apps でのカスタム ワークフローのトリガー、または変換するための Azure Functions を介した受け渡しなどが含まれます。
- Azure Machine Learning でのモデルのトレーニングや Microsoft Power BI での長期傾向分析などのコールド パス分析。

> [!Note]
> 連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

ご利用の IoT Central アプリケーションの管理者であるか、データ エクスポートのアクセス許可を持っている必要があります。

## <a name="set-up-export-destination"></a>エクスポート先の設定

連続データ エクスポートを構成する前に、エクスポート先が存在している必要があります。

### <a name="create-event-hubs-namespace"></a>Event Hubs 名前空間の作成

エクスポート先となる既存の Event Hubs 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Event Hubs 名前空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](../../event-hubs/event-hubs-create.md)を参照してください。

2. サブスクリプションを選択します。 ご使用の IoT Central アプリケーションと同じサブスクリプションではない、他のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

3. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

### <a name="create-service-bus-namespace"></a>Service Bus 名前空間の作成

エクスポート先となる既存の Service Bus 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Service Bus 名前空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](../../service-bus-messaging/service-bus-create-namespace-portal.md)を参照してください。
2. サブスクリプションを選択します。 ご使用の IoT Central アプリケーションと同じサブスクリプションではない、他のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

3. Service Bus 名前空間に移動し、上部の **[+ キュー]** または **[+ トピック]** を選択して、エクスポート先のキューまたはトピックを作成します。

エクスポート先として Service Bus を選択する場合、キューとトピックでセッションまたは重複の検出が有効になっていてはいけません。 これらのオプションのいずれかが有効になっていると、一部のメッセージがキューやトピックに到着しません。

### <a name="create-storage-account"></a>ストレージ アカウントの作成

エクスポート先となる既存の Azure Storage アカウントがない場合は、次の手順に従います。

1. [Azure portal で新しいストレージ アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 新しい [Azure Blob Storage アカウント](https://aka.ms/blobdocscreatestorageaccount)または [Azure Data Lake Storage v2 ストレージ アカウント](../../storage/blobs/data-lake-storage-quickstart-create-account.md)の作成の詳細を確認できます。

    - データを Azure Data Lake Storage v2 ストレージ アカウントにエクスポートする場合は、 **[アカウントの種類]** として **[BlobStorage]** を選択する必要があります。
    - IoT Central アプリケーションとは異なるサブスクリプションのストレージ アカウントにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

2. ご自分のストレージ アカウントでコンテナーを作成します。 ストレージ アカウントに移動します。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

## <a name="set-up-continuous-data-export"></a>連続データ エクスポートを設定する

これでデータのエクスポート先ができたので、次の手順に従って連続データ エクスポートを設定します。

1. ご使用の IoT Central アプリケーションにサインインします。

2. 左側のペインで、 **[データのエクスポート]** を選択します。

    > [!Note]
    > 左側のペインに [データのエクスポート] が表示されない場合は、アプリでデータ エクスポートを構成するアクセス許可がありません。 データ エクスポートの設定について、管理者に問い合わせてください。

3. 右上の **[+ 新規]** ボタンを選択します。 エクスポート先として、**Azure Event Hubs**、**Azure Service Bus**、または **Azure Blob Storage** のいずれかを選択します。 アプリケーションごとのエクスポートの最大数は 5 です。

    ![新しい継続的データ エクスポートの作成](media/howto-export-data/new-export-definition.png)

4. ドロップダウン リスト ボックスで、**Event Hubs 名前空間**、**Service Bus 名前空間**、**Storage Account 名前空間**、または **[接続文字列を入力してください]** を選択します。

    - ご使用の IoT Central アプリケーションと同じサブスクリプション内の Storage Account、Event Hubs 名前空間、Service Bus 名前空間のみが表示されます。 このサブスクリプションとは異なる場所にエクスポートする場合は、 **[Enter a connection string]\(接続文字列を入力する\)** を選択して、手順 5 に進みます。
    - 無料プランを使用して作成されたアプリで連続データ エクスポートを構成する唯一の方法は、接続文字列を使用することです。 無料プランのアプリには、関連付けられた Azure サブスクリプションがありません。

    ![新しい Event Hub を作成する](media/howto-export-data/export-event-hub.png)

5. (省略可能) **[Enter a connection string]\(接続文字列を入力する\)** を選択すると、接続文字列を貼り付けるための新しいボックスが表示されます。 次の接続文字列を取得するには:
    - Event Hubs や Service Bus。Azure portal で名前空間に移動します。
        - **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        - 既定の **RootManageSharedAccessKey** を選択するか、新しいポリシーを作成します。
        - プライマリまたはセカンダリの接続文字列をコピーします。
    - ストレージ アカウント。Azure portal でストレージ アカウントに移動します。
        - **[設定]** で **[アクセス キー]** を選択します。
        - key1 接続文字列または key2 接続文字列のいずれかをコピーします。

6. ドロップダウン リスト ボックスから、イベント ハブ、キュー、トピック、またはコンテナーを選択します。

7. **[エクスポートするデータ]** で、種類を **[オン]** に設定して、エクスポートするデータの種類を選択します。

8. 連続データ エクスポートを有効にするには、 **[有効]** トグルが **[オン]** になっていることを確認します。 **[保存]** を選択します。

9. 数分後に、選択したエクスポート先にデータが表示されます。

## <a name="export-contents-and-format"></a>エクスポートの内容と形式

エクスポートされたテレメトリ データには、テレメトリ値自体だけでなく、デバイスが IoT Central に送信したメッセージ全体が含まれています。 エクスポートされたデバイス データには、すべてのデバイスのプロパティとメタデータに対する変更が含まれており、エクスポートされたデバイス テンプレートには、すべてのデバイス テンプレートに対する変更が含まれています。

Event Hubs と Service Bus では、データはほぼリアルタイムでエクスポートされます。 データは body プロパティに入り、JSON 形式になります (例については、以下を参照)。

Blob Storage の場合、データは 1 分に 1 回エクスポートされ、各ファイルには、最後にエクスポートされたファイル以降の変更のバッチが含まれます。 エクスポートされたデータは、JSON 形式で 3 つのフォルダーに配置されます。 ストレージ アカウントでの既定のパスは次のとおりです。

- テレメトリ: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- デバイス: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- デバイス テンプレート: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure portal でエクスポートされたファイルを参照するには、ファイルに移動し、 **[BLOB の編集]** タブを選択します。


## <a name="telemetry"></a>テレメトリ

Event Hubs と Service Bus の場合、IoT Central がデバイスからメッセージを受信すると新しいメッセージが直ちにエクスポートされ、エクスポートされた各メッセージには、デバイスが body プロパティで送信した完全なメッセージが JSON 形式で含まれています。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。 エクスポートされたファイルでは、[IoT Hub メッセージ ルーティング](../../iot-hub/tutorial-routing.md)によって Blob Storage にエクスポートされたメッセージ ファイルと同じ形式が使用されます。 

> [!NOTE]
> Blob Storage の場合、デバイスが `contentType: application/JSON` と `contentEncoding:utf-8` (または `utf-16`、`utf-32`) を持つメッセージを送信していることを確認してください。 例については、[IoT Hub のドキュメント](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)を参照してください。

テレメトリを送信したデバイスは、デバイス ID で表されます (以下のセクションを参照)。 デバイスの名前を取得するには、デバイスのデータをエクスポートし、デバイス メッセージの **deviceId** と一致する **connectionDeviceId** を使用して各メッセージを関連付けます。

これは、イベント ハブまたは Service Bus のキューまたはトピックで受信したメッセージの例です。

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

次に、Blob Storage にエクスポートされたレコードの例を示します。

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>デバイス

スナップショット内の各メッセージまたはレコードは、最後にエクスポートされたメッセージ以降に、そのデバイスとクラウドのプロパティに加えられた 1 つ以上の変更を表しています。 これには次のものが含まれます

- `id` IoT Central でデバイスの
- デバイスの `displayName`
- `instanceOf` のデバイス テンプレート ID
- `simulated` フラグ (デバイスがシミュレートされたデバイスの場合は true)
- `provisioned` フラグ (デバイスがプロビジョニングされている場合は true)
- `approved` フラグ (デバイスでデータの送信が承認されている場合は true)
- プロパティ値
- `properties` (デバイスとクラウドのプロパティ値を含む)

削除されたデバイスはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイスを示すものはありません。

Event Hubs と Service Bus の場合、デバイス データを含むメッセージは、IoT Central に表示されるように、ほぼリアルタイムでイベント ハブまたは Service Bus のキューまたはトピックに送信されます。 

Blob Storage の場合は、最後のものが書き込まれてから以降のすべての変更を含む新しいスナップショットが 1 分間に 1 回エクスポートされます。

次に示すのは、イベント ハブまたは Service Bus のキューまたはトピック内のデバイスとプロパティ データに関するメッセージの例です。

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Blob Storage のデバイスとプロパティ データを含むスナップショットの例を次に示します。 エクスポートされたファイルには、1 つのレコードにつき 1 行が含まれます。

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>デバイス テンプレート

各メッセージまたはスナップショット レコードは、最後にエクスポートされたメッセージ以降に公開されたデバイス テンプレートに加えられた 1 つ以上の変更を表しています。 各メッセージまたはレコードで送信される情報としては、次のものがあります。

- 上記のデバイス ストリームの `instanceOf` に一致するデバイス テンプレートの `id`
- デバイス テンプレートの`displayName`
- デバイスの `capabilityModel` (その `interfaces` を含む)、テレメトリ、プロパティ、およびコマンドの定義
- `cloudProperties` の定義
- `capabilityModel` に埋め込まれたオーバライドと初期値

削除されたデバイス テンプレートはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイス テンプレートを示すものはありません。

Event Hubs と Service Bus の場合、デバイス テンプレート データを含むメッセージは、IoT Central に表示されるように、ほぼリアルタイムでイベント ハブまたは Service Bus のキューまたはトピックに送信されます。 

Blob Storage の場合は、最後のものが書き込まれてから以降のすべての変更を含む新しいスナップショットが 1 分間に 1 回エクスポートされます。

次に示すのは、イベント ハブまたは Service Bus のキューまたはトピック内のデバイス テンプレート データに関するメッセージの例です。

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Blob Storage のデバイスとプロパティ データを含むスナップショットの例を次に示します。 エクスポートされたファイルには、1 つのレコードにつき 1 行が含まれます。

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>データ形式の変更に関する通知

> [!Note]
> テレメトリ ストリームのデータ形式はこの変更の影響を受けません。 デバイスとデバイス テンプレートのデータ ストリームのみが影響を受けます。

"*デバイス*" と "*デバイス テンプレート*" のストリームが有効になっているプレビュー アプリケーションに既存のデータ エクスポートがある場合は、**2020 年 6 月 30 日**までにエクスポートを更新する必要があります。 これは、Azure Blob Storage、Azure Event Hubs、および Azure Service Bus へのエクスポートに適用されます。

2020 年 2 月 3 日以降、デバイスとデバイス テンプレートが有効になっているアプリケーションの新しいすべてのエクスポートに、上記のデータ形式が使用されます。 これより前に作成されたすべてのエクスポートは、2020 年 6 月 30 日までは古いデータ形式のままになります。その後、これらのエクスポートは新しいデータ形式に自動的に移行されます。 新しいデータ形式は、IoT Central パブリック API 内の[デバイス](https://docs.microsoft.com/rest/api/iotcentral/devices/get)、[デバイス プロパティ](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)、[デバイス クラウド プロパティ](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties)および[デバイス テンプレート](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get)のオブジェクトと一致します。 
 
**デバイス**については、古いデータ形式と新しいデータ形式の間で主に次のような違いがあります。
- デバイスの `@id` は削除され、`deviceId` の名前は `id` に変更されます 
- デバイスのプロビジョニングの状態を示す `provisioned` フラグが追加されます
- デバイスの承認の状態を示す `approved` フラグが追加されます
- デバイスとクラウドのプロパティを含む `properties` が、パブリック API のエンティティと一致します

**デバイス テンプレート**については、古いデータ形式と新しいデータ形式の間で主に次のような違いがあります。

- デバイス テンプレートの `@id` の名前は `id` に変更されます
- デバイス テンプレートの `@type` の名前が `types` に変更され、配列になりました

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>デバイス (2020 年 2 月 3 日時点で非推奨とされた形式)
```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>デバイス テンプレート (2020 年 2 月 3 日時点で非推奨とされた形式)
```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```
## <a name="next-steps"></a>次のステップ

これで、Azure Event Hubs、Azure Service Bus、および Azure Blob Storage にデータをエクスポートする方法がわかったので、次の手順に進みます。

> [!div class="nextstepaction"]
> [Webhook の作成方法](./howto-create-webhooks.md)
