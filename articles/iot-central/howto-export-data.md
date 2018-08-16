---
title: Azure IoT Central でデータをエクスポートする | Microsoft Docs
description: Azure IoT Central アプリケーションからデータをエクスポートする方法
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 3ca2bc56c03e5bbabbd9b2f17edc621bdd94b02f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622485"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central でデータをエクスポートする

この記事では、定期的にデータを Azure BLOB ストレージ アカウントにエクスポートする Azure IoT Central の連続データ エクスポート機能の使用方法について説明します。 **測定**、**デバイス**、**デバイス テンプレート**を [Apache AVRO](https://avro.apache.org/docs/current/index.html) 形式のファイルにエクスポートできます。 エクスポートしたデータは、Azure Machine Learning のトレーニング モデルや Microsoft Power BI の長期傾向分析などのコールド パス分析に使用できます。

> [!Note]
> 連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

- 30 日間に延長された無料試用版 IoT Central アプリケーション、または有料アプリケーション。
- Azure サブスクリプションが含まれる Azure アカウント。
- 同じ Azure アカウントが IoT Central アプリケーションの管理者であること。
- 同じ Azure アカウントに、同じ Azure サブスクリプション内でのストレージ アカウントの作成または既存ストレージ アカウントへのアクセスのためのアクセス許可が付与されていること。

## <a name="types-of-data-to-export"></a>エクスポートするデータの種類

### <a name="measurements"></a>測定

デバイスが送信する測定は、1 分ごとにストレージ アカウントにエクスポートされます。 データには、その間にすべてのデバイスから IoT Central が受信したすべての新しいメッセージが含まれます。 エクスポートされる AVRO ファイルでは、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)によって BLOB ストレージにエクスポートされるメッセージ ファイルと同じ形式が使われます。

> [!NOTE]
> 測定を送信するデバイスは、デバイス ID で表されます (以下のセクションを参照)。 デバイスの名前を取得するには、デバイスのスナップショットをエクスポートします。 デバイス ID と一致する **connectionDeviceId** を使って、各メッセージ レコードを関連付けます。

次の例では、デコードされた AVRO ファイル内のレコードを示します。

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>デバイス

連続データ エクスポートを初めて有効にしたときに、すべてのデバイスを含む単一のスナップショットがエクスポートされます。 スナップショットには次の値が含まれます。
- デバイス ID。
- デバイス名。
- デバイス テンプレート ID。
- プロパティ値。
- 設定値。

1 分に 1 回、新しいスナップショットが書き込まれます。 スナップショットには次の値が含まれます。

- 最後のスナップショット以降に追加された新しいデバイス。
- 最後のスナップショット以降にプロパティおよび設定の値が変更されたデバイス。

> [!NOTE]
> 最後のスナップショット以降に削除されたデバイスは、エクスポートされません。 現時点では、スナップショットに削除されたデバイスのインジケーターはありません。
>
> 各デバイスが属するデバイス テンプレートは、デバイス テンプレート ID によって表されます。 デバイス テンプレートの名前を取得するには、デバイス テンプレートのスナップショットをエクスポートします。

デコードされた AVRO ファイルの各レコードは次のようになります。

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>デバイス テンプレート

連続データ エクスポートを初めて有効にしたときに、すべてのデバイス テンプレートを含む単一のスナップショットがエクスポートされます。 スナップショットには次の値が含まれます。 
- デバイス テンプレート ID。
- 測定のデータ型および最小/最大値。
- プロパティのデータ型と既定値。
- 設定のデータ型と既定値。

1 分に 1 回、新しいスナップショットが書き込まれます。 スナップショットには次の値が含まれます。

- 最後のスナップショット以降に追加された新しいデバイス テンプレート。
- 最後のスナップショット以降に設定値、プロパティ、および設定の定義が変更されたデバイス テンプレート。

> [!NOTE]
> 最後のスナップショット以降に削除されたデバイス テンプレートは、エクスポートされません。 現時点では、スナップショットに削除されたデバイス テンプレートのインジケーターはありません。

デコードされた AVRO ファイルの各レコードは次のようになります。

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="set-up-continuous-data-export"></a>連続データ エクスポートを設定する

1. Azure ストレージ アカウントをお持ちでない場合は、Azure portal で[新しいストレージ アカウントを作成](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)してください。 **IoT Central アプリケーションを含む Azure サブスクリプション**でストレージ アカウントを作成します。
    - アカウントの種類として、**[汎用]** または **[BLOB ストレージ]** を選択します。
    - お使いの IoT Central アプリケーションがあるサブスクリプションを選択します。 サブスクリプションが表示されない場合は、必要に応じて、別の Azure アカウントにサインインするか、またはサブスクリプションへのアクセスを要求します。
    - 既存のリソース グループを選択するか、新しいリソース グループを作成します  [新しいストレージ アカウントの作成方法](https://aka.ms/blobdocscreatestorageaccount)を確認してください。

2. お使いのストレージ アカウントに、IoT Central データのエクスポート先となるコンテナーを作成します。 ストレージ アカウントに移動します。 **[Blob service]** で **[BLOB の参照]** を選択します。 **[コンテナー]** を選択して、新しいコンテナーを作成します。

   ![コンテナーを作成する](media/howto-export-data/createcontainer.png)

3. 同じ Azure アカウントを使用して、IoT Central アプリケーションにサインインします。

4. **[管理]** で、**[データのエクスポート]** を選択します。

   ![連続データ エクスポートを構成する](media/howto-export-data/continuousdataexport.PNG)

5. **[ストレージ アカウント]** ドロップダウン リスト ボックスで、お使いのストレージ アカウントを選択します。 **[コンテナー]** ドロップダウン リスト ボックスで、お使いのコンテナーを選択します。 **[Data to export]\(エクスポートするデータ\)** で、エクスポートするデータの種類を **[オン]** に設定して指定します。

6. 連続データ エクスポートを有効にするには、**[データのエクスポート]** を **[オン]** に設定します。 **[保存]** を選択します。

7. 数分後に、データがストレージ アカウントに表示されます。 ストレージ アカウントを参照します。 **[BLOB の参照]** を選択し、お使いのコンテナーを選択します。 エクスポート データの 3 つのフォルダーが表示されます。 エクスポート データの AVRO ファイルの既定のパスは次のとおりです。
    - メッセージ: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro
    - デバイス: {container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro
    - デバイス テンプレート: {container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro

## <a name="read-exported-avro-files"></a>エクスポートされた AVRO ファイルを読み取る

ファイルは生の状態では読み取りできないので、AVRO はバイナリ形式になります。 ファイルは、JSON 形式にデコードできます。 次の例では、測定、デバイス、およびデバイス テンプレートの AVRO ファイルを解析する方法を示します。 これらの例は、前のセクションで説明した例に対応しています。

### <a name="read-avro-files-by-using-python"></a>Python を使用して AVRO ファイルを読み取る

#### <a name="install-pandas-and-the-pandavro-package"></a>pandas および pandavro パッケージをインストールする

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>測定の AVRO ファイルを解析する

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>デバイスの AVRO ファイルを解析する

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["id"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>デバイス テンプレートの AVRO ファイルを解析する

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>C# を使用して AVRO ファイルを読み取る

#### <a name="install-the-microsofthadoopavro-package"></a>Microsoft.Hadoop.Avro パッケージをインストールする

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>測定の AVRO ファイルを解析する

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>デバイスの AVRO ファイルを解析する

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>デバイス テンプレートの AVRO ファイルを解析する

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Javascript を使用して AVRO ファイルを読み取る

#### <a name="install-the-avsc-package"></a>avsc パッケージをインストールする

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>測定の AVRO ファイルを解析する

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>デバイスの AVRO ファイルを解析する

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the id property.
        const deviceId = record.id;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>デバイス テンプレートの AVRO ファイルを解析する

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>次の手順

データをエクスポートする方法がわかったら、次の手順に進みます。

> [!div class="nextstepaction"]
> [Power BI でデータを視覚化する方法](howto-connect-powerbi.md)
