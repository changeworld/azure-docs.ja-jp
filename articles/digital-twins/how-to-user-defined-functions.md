---
title: Azure Digital Twins でユーザー定義関数を使用する方法 |Microsoft Docs
description: Azure Digital Twins を使用してユーザー定義関数、マッチャー、および役割の割り当てを作成する方法のガイドラインです。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 7fbaff5ed1b60a4434ba2eb0c78c6aa1f3fd6645
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323979"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins でユーザー定義関数を使用する方法

[ユーザー定義関数](./concepts-user-defined-functions.md)を使用すると、ユーザーは受信テレメトリ メッセージおよび空間グラフのメタデータに対してカスタム ロジックを実行して、事前定義されたエンドポイントにイベントを送信することができます。 このガイドでは、一定の温度を超えるすべての測定値を検出して警告する温度イベントへの対応例を見ていきます。

次の例では、`https://yourManagementApiUrl` は Digital Twins API の URI を参照しています。

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourInstanceName` | Azure Digital Twins インスタンスの名前 |
| `yourLocation` | インスタンスをホストするサーバーのリージョン |

## <a name="client-library-reference"></a>クライアント ライブラリ リファレンス

ユーザー定義関数の実行時のヘルパー メソッドとして使用できる関数は、次の[クライアント リファレンス](#Client-Reference)に列挙されています。

## <a name="create-a-matcher"></a>マッチャーを作成する

マッチャーは、指定されたテレメトリ メッセージに対して実行されるユーザー定義関数を決定するグラフ オブジェクトです。

有効なマッチャー条件の比較は次のとおりです。

- `Equals`
- `NotEquals`
- `Contains`

有効なマッチャー条件のターゲットは次のとおりです。

- `Sensor`
- `SensorDevice`
- `SensorSpace`

次の例のマッチャーは、データ型の値が `Temperature` であるすべてのセンサー テレメトリ イベントに対して true と評価されます。 ユーザー定義関数に対して複数のマッチャーを作成できます。

```text
POST https://yourManagementApiUrl/api/v1.0/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "yourSpaceIdentifier"
}
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourManagementApiUrl` | Management API の完全な URL パス  |
| `yourSpaceIdentifier` | インスタンスをホストするサーバーのリージョン |

## <a name="create-a-user-defined-function-udf"></a>ユーザー定義関数 (UDF) の作成

マッチャーを作成した後、次の POST 呼び出しで関数スニペットをアップロードします。

> [!IMPORTANT]
> - ヘッダーで、`Content-Type: multipart/form-data; boundary="userDefinedBoundary"` を設定します。
> - 本文は複数の部分から成ります。
>   - 最初の部分は、UDF に必要なメタデータの情報です。
>   - 2 番目の部分は、JavaScript の計算ロジックです。
> - `userDefinedBoundary` セクションの `SpaceId` と `Machers` GUID を置き換えます。

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourManagementApiUrl` | Management API の完全な URL パス  |

本文は次のようになります。

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourSpaceIdentifier` | 空間識別子  |
| `yourMatcherIdentifier` | 使用するマッチャーの ID |

### <a name="example-functions"></a>関数の例

データ型 `Temperature` のセンサー (sensor.DataType) に対して、センサー テレメトリの測定値を直接設定します。

```javascript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

センサー テレメトリの測定値が定義済みのしきい値を超えた場合は、メッセージを記録します。 Digital Twins インスタンスで診断設定が有効になっている場合は、ユーザー定義関数からのログが転送されます。

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

次のコードでは、温度レベルが定義済みの定数を超えた場合に通知がトリガーされます。

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

さらに複雑な UDF のコード サンプルについては、[新鮮な空気の UDF を使用して利用可能な空間を確認する](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)例を参照してください。

## <a name="create-a-role-assignment"></a>役割の割り当ての作成

ユーザー定義関数の実行に使用する役割の割り当てを作成する必要があります。 作成しない場合、ユーザー定義関数は、Management API とやり取りしてグラフ オブジェクトに対してアクションを実行するための適切なアクセス許可が得られません。 ユーザー定義関数が実行するアクションは、Digital Twins Management API 内の役割ベースのアクセス制御の対象から除外されません。 これらのアクションは、特定の役割または特定のアクセス制御パスを指定することによって、範囲を限定できます。 詳細については、[役割ベースのアクセス制御](./security-role-based-access-control.md)に関するページを参照してください。

- 役割に対してクエリを実行し、UDF に割り当てる役割の ID を取得して、それを次の RoleId に渡します。

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourManagementApiUrl` | Management API の完全な URL パス  |

- ObjectId は先ほど作成した UDF ID になります。
- 空間に対して完全なパスを使用してクエリを実行することで、`Path` を見つけ、`spacePaths` 値をコピーします。 UDF の役割の割り当てを作成するときに、それを次のパスに貼り付けます。

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourManagementApiUrl` | Management API の完全な URL パス  |
| `yourSpaceName` | 使用する空間の名前 |

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourManagementApiUrl` | Management API の完全な URL パス  |
| `yourDesiredRoleIdentifier` | 目的の役割の識別子 |
| `yourUserDefinedFunctionId` | 使用する UDF の ID |
| `yourAccessControlPath` | アクセス制御パス |

## <a name="send-telemetry-to-be-processed"></a>処理するテレメトリを送信する

グラフに示されているセンサーによって生成されたテレメトリでは、アップロードされたユーザー定義関数の実行をトリガーします。 テレメトリがデータ プロセッサによって取得されると、ユーザー定義関数の呼び出しの実行プランが作成されます。

1. 測定値の生成元であるセンサーのマッチャーを取得します。
1. どのマッチャーが正常に評価されたかに応じて、関連付けられているユーザー定義関数を取得します。
1. 各ユーザー定義関数を実行します。

## <a name="client-reference"></a>クライアント リファレンス

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

空間識別子が指定されると、グラフから空間を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| id  | `guid` | 空間識別子 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

センサーの識別子が指定されると、グラフからセンサーを取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| id  | `guid` | センサーの識別子 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

デバイスの識別子が指定されると、グラフからデバイスを取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| id  | `guid` | デバイスの識別子 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

センサーの識別子とそのデータ型が指定されると、そのセンサーの現在の値を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | センサーの識別子 |
| dataType  | `string` | センサーのデータ型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

空間識別子と値名が指定されると、その空間プロパティの現在の値を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別子 |
| valueName  | `string` | 空間プロパティの名前 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

センサーの識別子とそのデータ型が指定されると、そのセンサーの履歴の値を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | センサーの識別子 |
| dataType  | `string` | センサーのデータ型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

空間識別子と値名が指定されると、空間のそのプロパティの履歴の値を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別子 |
| valueName  | `string` | 空間プロパティの名前 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

空間識別子が指定されると、その親空間の子空間を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別子 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

空間識別子が指定されると、その親空間の子センサーを取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別子 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

空間識別子が指定されると、その親空間の子デバイスを取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別子 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

デバイスの識別子が指定されると、その親デバイスの子センサーを取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | デバイスの識別子 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

空間識別子が指定されると、その親空間を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| childSpaceId  | `guid` | 空間識別子 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

センサーの識別子が指定されると、その親空間を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | センサーの識別子 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

デバイスの識別子が指定されると、その親空間を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| childDeviceId  | `guid` | デバイスの識別子 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

センサーの識別子が指定されると、その親デバイスを取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | センサーの識別子 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

空間識別子が指定されると、空間からプロパティとその値を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別子 |
| propertyName  | `string` | 空間プロパティの名前 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

センサーの識別子が指定されると、センサーからプロパティとその値を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | センサーの識別子 |
| propertyName  | `string` | センサー プロパティの名前 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

デバイスの識別子が指定されると、デバイスからプロパティとその値を取得します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | デバイスの識別子 |
| propertyName  | `string` | デバイス プロパティの名前 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

指定されたデータ型でセンサー オブジェクトの値を設定します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | センサーの識別子 |
| dataType  | `string` | センサーのデータ型 |
| value  | `string` | value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

指定されたデータ型の空間オブジェクトの値を設定します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別子 |
| dataType  | `string` | データ型 |
| value  | `string` | value |

### <a name="logmessage"></a>log(message)

ユーザー定義関数内で次のメッセージを記録します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| message  | `string` | 記録されるメッセージ |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

ディスパッチするカスタム通知を送信します。

**種類**: グローバル関数

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| topologyObjectId  | `Guid` | グラフのオブジェクト識別子 (例:  空間/センサー/デバイス ID)|
| topologyObjectType  | `string` | (例:  空間/センサー/デバイス)|
| payload  | `string` | 通知と共に送信される json ペイロード |

## <a name="return-types"></a>戻り値の型

上記のクライアント リファレンスからのリターン オブジェクトを記述するモデルを次に示します。

### <a name="space"></a>スペース

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>空間メソッド

#### <a name="parent--space"></a>Parent() ⇒ `space`

現在の空間の親空間を返します。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

現在の空間の子センサーを返します。

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

現在の空間の子デバイスを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

現在の空間の拡張プロパティとその値を返します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 拡張プロパティの名前 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

現在の空間の値を返します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| valueName | `string` | 値の名前 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

現在の空間の履歴の値を返します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| valueName | `string` | 値の名前 |

#### <a name="notifypayload"></a>Notify(payload)

指定されたペイロードを含めて通知を送信します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| payload | `string` | 通知に含める json ペイロード |

### <a name="device"></a>Device

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>デバイスのメソッド

#### <a name="parent--space"></a>Parent() ⇒ `space`

現在のデバイスの親空間を返します。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

現在のデバイスの子センサーを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

現在のデバイスの拡張プロパティとその値を返します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 拡張プロパティの名前 |

#### <a name="notifypayload"></a>Notify(payload)

指定されたペイロードを含めて通知を送信します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| payload | `string` | 通知に含める json ペイロード |

### <a name="sensor"></a>センサー

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>センサーのメソッド

#### <a name="space--space"></a>Space() ⇒ `space`

現在のセンサーの親空間を返します。

#### <a name="device--device"></a>Device() ⇒ `device`

現在のセンサーの親デバイスを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

現在のセンサーの拡張プロパティとその値を返します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 拡張プロパティの名前 |

#### <a name="value--value"></a>Value() ⇒ `value`

現在のセンサーの値を返します。

#### <a name="history--value"></a>History() ⇒ `value[]`

現在のセンサーの履歴の値を返します。

#### <a name="notifypayload"></a>Notify(payload)

指定されたペイロードを含めて通知を送信します。

| Param  | type                | 説明  |
| ------ | ------------------- | ------------ |
| payload | `string` | 通知に含める json ペイロード |

### <a name="value"></a>値

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>拡張プロパティ

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>次の手順

イベントの送信先となる Digital Twins エンドポイントを作成する方法については、[Digital Twins エンドポイントの作成](how-to-egress-endpoints.md)に関するページを参照してください。

Digital Twins エンドポイントの詳細については、[エンドポイントの詳細](concepts-events-routing.md)に関するページを参照してください。
