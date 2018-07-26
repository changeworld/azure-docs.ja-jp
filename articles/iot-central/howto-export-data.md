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
ms.openlocfilehash: 6d35e3cfefcefef0b4ff40364cbdab92d486b769
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011460"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central でデータをエクスポートする

連続データ エクスポートを使用して、Azure Blob Storage アカウントにデータを定期的にエクスポートします。 **測定**、**デバイス**、および**デバイス テンプレート**を [Apache AVRO](https://avro.apache.org/docs/current/index.html) 形式のファイルにエクスポートすることを選択します。 Azure Machine Learning のトレーニング モデルや Power BI の長期傾向分析などのコールド パス分析に、エクスポートしたデータを使用します。

> [!Note]
> 連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 連続データ エクスポートを無効にしているときに、データを取得する方法は現在ありません。 より多くの履歴データを保持するために、連続データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

- 30 日間に延長された無料試用版アプリ、または有料のアプリ
- Azure サブスクリプションを備えた Azure アカウント
- 同じ Azure アカウントがお使いの IoT Central アプリの管理者である
- 同じ Azure アカウントが、ストレージ アカウントを作成したり、同じ Azure サブスクリプションで既存のストレージ アカウントにアクセスしたりするための権限を備えている

## <a name="types-of-data-to-export"></a>エクスポートするデータの種類

### <a name="measurements"></a>測定

デバイスが送信する測定は、ストレージ アカウントにエクスポートされます。 測定データは約 1 分ごとにエクスポートされ、IoT Central でその時間枠内に全デバイスから受信した新しいメッセージがすべて含まれます。 エクスポートされる AVRO ファイルは、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)によって Blob ストレージにエクスポートされるメッセージ ファイルと同じ形式です。

> [!NOTE]
> 測定を送信したデバイスは、デバイス ID で表されます (以下を参照)。 デバイスの名前を取得するには、デバイス スナップショットもエクスポートする必要があります。 デバイスの ID と一致する connectionDeviceId を使用して、各メッセージ レコードを関連付けできます。

以下は、デコードされた AVRO ファイルのレコードの例です。

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

連続データ エクスポートを最初に有効にするとき、すべてのデバイスを含む単一のスナップショットがエクスポートされます。 次のトピックがあります。
- デバイス ID
- デバイス名
- デバイス テンプレート ID
- プロパティ値
- 設定値

約 1 分ごとに、次の内容を含む新しいスナップショットが作成されます。

- 最後のスナップショット以降に追加された新しいデバイス
- 最後のスナップショット以降に変更されたプロパティおよび設定値を含むデバイス

> [!NOTE]
> 最後のスナップショット以降に削除されたデバイスは、エクスポートされません。 スナップショットには、この時点で削除されたデバイスを示す指標はありません。

> [!NOTE]
> 各デバイスが属するデバイス テンプレートは、デバイス テンプレート ID によって表されます。 デバイス テンプレートの名前を取得するには、デバイス テンプレートのスナップショットもエクスポートする必要があります。

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

連続データ エクスポートを最初に有効にするとき、すべてのデバイス テンプレートを含む単一のスナップショットがエクスポートされます。 次のトピックがあります。 
- デバイス テンプレート ID
- 測定のデータ型および最小/最大値
- プロパティのデータ型と既定値
- 設定のデータ型と既定値

約 1 分ごとに、次の内容を含む新しいスナップショットが作成されます。

- 最後のスナップショット以降に追加された新しいデバイス テンプレート
- 最後のスナップショット以降に変更された設定値、プロパティ、および設定の定義を含んだデバイス テンプレート

> [!NOTE]
> 最後のスナップショット以降に削除されたデバイス テンプレートは、エクスポートされません。 スナップショットには、この時点で削除されたデバイス テンプレートを示す指標はありません。

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

## <a name="how-to-set-up-data-export"></a>データ エクスポートの設定方法

1. まだ持っていない場合は、**お使いのアプリがある Azure サブスクリプションに** Azure Storage アカウントを作成します。 [ここをクリック](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)して Azure Portal に移動し、新しい Azure Storage アカウントを作成します。

    - *[汎用]* または *[BLOB ストレージ]* アカウントの種類を選択します。
    - お使いの IoT Central アプリがあるサブスクリプションを選択します。 サブスクリプションが表示されない場合は、必要に応じて、別の Azure アカウントにサインインするか、またはサブスクリプションへのアクセスを要求します。
    - 既存のリソース グループを選択するか、新しいリソース グループを作成できます。 [新しいストレージ アカウントの作成方法](https://aka.ms/blobdocscreatestorageaccount)に関する手順を確認してください。

2. お使いのストレージ アカウントに、IoT Central データのエクスポート先となるコンテナーを作成します。 [ストレージ アカウント] -> [Browse Blobs]\(BLOB を参照する\) へ移動して、新しいコンテナーを作成します。 ![コンテナー イメージを作成する](media/howto-export-data/createcontainer.png)

3. 同じ Azure アカウントを使用して、IoT Central アプリケーションにサインインします。
1. [管理] -> [Continuous Data Export]\(連続データ エクスポート\) の順に移動します。
![IoT Central CDE](media/howto-export-data/continuousdataexport.PNG)
1. ドロップダウンを使用して、お使いのストレージ アカウントとコンテナーを選択します。 次に、トグルを使用して、さまざまな種類のデータのエクスポートを有効または無効にします。
1. 最後に、トグルを使用して連続データ エクスポートを有効にし、[保存] をクリックします。
1. 数分待機して、ストレージ アカウントにデータが表示されることを確認する必要があります。 ストレージ アカウントに移動し、[Browse Blobs]\(BLOB を参照する\) を選択し、[コンテナー] を選択すると、3 つのフォルダーが表示されます。 各種のデータを含む AVRO ファイルへの既定のパスを次に示します。
- メッセージ: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- デバイス: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- デバイス テンプレート: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>エクスポートされた AVRO ファイルを読み取る方法

ファイルは生の状態では読み取りできないので、AVRO はバイナリ形式になります。 これらのファイルは、JSON 形式にデコードできます。 上記の例を使用して測定、デバイス、およびデバイス テンプレートの AVRO ファイルを解析する方法を、次の例に示します。

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Pandas および PandaAvro パッケージのインストール:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>測定の AVRO ファイルの解析

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>デバイスの AVRO ファイルの解析

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>デバイス テンプレートの AVRO ファイルの解析

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Microsoft.Hadoop.Avro のインストール

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>測定の AVRO ファイルの解析

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
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
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

### <a name="parse-devices-avro-file"></a>デバイスの AVRO ファイルの解析

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
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
### <a name="parse-device-templates-avro-file"></a>デバイス テンプレートの AVRO ファイルの解析

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
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

## <a name="javascript"></a>JavaScript

### <a name="install-avsc"></a>avsc のインストール

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>測定の AVRO ファイルの解析

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>デバイスの AVRO ファイルの解析

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>デバイス テンプレートの AVRO ファイルの解析

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>次の手順

デバイス エクスプローラーで[デバイスを管理する](howto-manage-devices.md)方法を確認してください。 
