---
title: Azure Digital Twins でユーザー定義関数を使用する方法 |Microsoft Docs
description: Azure Digital Twins を使用してユーザー定義関数、マッチャー、役割の割り当てを作成する方法のガイドラインです。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636834"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins でユーザー定義関数を使用する方法

[ユーザー定義関数](./concepts-user-defined-functions.md) (UDF) を使用すると、ユーザーは受信テレメトリ メッセージおよび空間グラフのメタデータに対してカスタム ロジックを実行できます。 その後、ユーザーは定義済みのエンドポイントにイベントを送信できます。 このガイドでは、一定の温度を超えるすべての測定値を検出して警告する温度イベントへの対応例を見ていきます。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>クライアント ライブラリ リファレンス

ユーザー定義関数の実行時のヘルパー メソッドとして使用できる関数の一覧が、[クライアント リファレンス](#Client-Reference)に示されています。

## <a name="create-a-matcher"></a>マッチャーを作成する

マッチャーは、特定のテレメトリ メッセージに対して実行されるユーザー定義関数を決定するグラフ オブジェクトです。

- 有効なマッチャー条件の比較は次のとおりです。

  - `Equals`
  - `NotEquals`
  - `Contains`

- 有効なマッチャー条件のターゲットは次のとおりです。

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

次の例のマッチャーは、データ型の値が `"Temperature"` であるすべてのセンサー テレメトリ イベントに対して true と評価されます。 ユーザー定義関数に対して複数のマッチャーを作成できます。

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
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
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | インスタンスをホストするサーバーのリージョン |

## <a name="create-a-user-defined-function-udf"></a>ユーザー定義関数 (UDF) の作成

マッチャーが作成された後、次の **POST** 呼び出しで関数スニペットをアップロードします。

> [!IMPORTANT]
> - ヘッダーで、次の `Content-Type: multipart/form-data; boundary="userDefinedBoundary"` を設定します。
> - 本文は複数の部分から成ります。
>   - 最初の部分は、UDF に必要なメタデータの情報です。
>   - 2 番目の部分は、JavaScript の計算ロジックです。
> - **USER_DEFINED_BOUNDARY** セクションで、**SpaceId** および **Machers** の値を置き換えます。

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| パラメーター値 | 置換後の文字列 |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | 複数の部分からなるコンテンツ境界名 |

### <a name="body"></a>本文

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 空間識別子  |
| YOUR_MATCHER_IDENTIFIER | 使用するマッチャーの ID |

### <a name="example-functions"></a>関数の例

データ型 **Temperature** のセンサー (`sensor.DataType`) に対して、センサー テレメトリの測定値を直接設定します。

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

**telemetry** パラメーターは、**SensorId** 属性と **Message** 属性 (センサーから送信されたメッセージに対応) を公開します。 **executionContext** パラメーターは、次の属性を公開します。

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

次の例では、センサー テレメトリの測定値が定義済みしきい値を超えた場合に、メッセージを記録します。 Azure Digital Twins インスタンスで診断設定が有効になっている場合は、ユーザー定義関数からのログも転送されます。

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

次のコードでは、温度レベルが定義済み定数を超えた場合に通知がトリガーされます。

```JavaScript
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

さらに複雑な UDF のコード サンプルについては、[新鮮な空気の空き空間を確認する UDF をご確認ください](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)。

## <a name="create-a-role-assignment"></a>役割の割り当ての作成

ユーザー定義関数の実行に使われる役割の割り当てを作成する必要があります。 作成しない場合、ユーザー定義関数は、Management API とやり取りしてグラフ オブジェクトにアクションを実行するための適切なアクセス許可を得ることができません。 ユーザー定義関数が実行するアクションは、Azure Digital Twins Management API 内のロールベースのアクセス制御の対象から除外されません。 特定の役割または特定のアクセス制御パスを指定すると、これらのアクションの範囲を限定できます。 詳細については、[ロールベースのアクセス制御](./security-role-based-access-control.md)のドキュメントを参照してください。

1. 役割を照会するクエリを実行し、UDF に割り当てられる役割の ID を取得します。 それを **RoleId** に渡します。

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** は先ほど作成した UDF ID になります。
1. `fullpath` を使用して空間でクエリを実行して、**Path** の値を見つけます。
1. 返された `spacePaths` 値をコピーします。 次のコードでそれを使用します。

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | パラメーター値 | 置換後の文字列 |
    | --- | --- |
    | *YOUR_SPACE_NAME* | 使用する空間の名前 |

1. 返された `spacePaths` 値を **Path** に貼り付けて、UDF 役割の割り当てを作成します。

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | 値 | 置換後の文字列 |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | 目的の役割の識別子 |
    | YOUR_USER_DEFINED_FUNCTION_ID | 使用する UDF の ID |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | UDF タイプを指定する ID |
    | YOUR_ACCESS_CONTROL_PATH | アクセス制御パス |

## <a name="send-telemetry-to-be-processed"></a>処理するテレメトリを送信する

グラフに示されているセンサーによって生成されたテレメトリは、アップロードされたユーザー定義関数の実行をトリガーします。 データ プロセッサがテレメトリを取得します。 次に、ユーザー定義関数の呼び出し用の実行プランが作成されます。

1. 測定値の生成元であるセンサーのマッチャーを取得します。
1. どのマッチャーが正常に評価されたかに応じて、関連付けられているユーザー定義関数を取得します。
1. それぞれのユーザー定義関数を実行します。

## <a name="client-reference"></a>クライアント リファレンス

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

空間識別子が指定されると、この関数はグラフから空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 空間識別子 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

センサーの識別子が指定されると、この関数はグラフからセンサーを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | センサーの識別子 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

デバイスの識別子が指定されると、この関数はグラフからデバイスを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | デバイスの識別子 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

センサーの識別子とそのデータ型が指定されると、この関数はそのセンサーの現在の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | センサーの識別子 |
| *dataType*  | `string` | センサーのデータ型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

空間識別子と値名が指定されると、この関数はその空間プロパティの現在の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | 空間識別子 |
| *valueName* | `string` | 空間プロパティの名前 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

センサーの識別子とそのデータ型が指定されると、この関数はそのセンサーの履歴の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | センサーの識別子 |
| *dataType* | `string` | センサーのデータ型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

空間識別子と値名が指定されると、この関数は空間のそのプロパティの履歴の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |
| *valueName* | `string` | 空間プロパティの名前 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

空間識別子が指定されると、この関数はその親空間の子空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

空間識別子が指定されると、この関数はその親空間の子センサーを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

空間識別子が指定されると、この関数はその親空間の子デバイスを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

デバイスの識別子が指定されると、この関数はその親デバイスの子センサーを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | デバイスの識別子 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

空間識別子が指定されると、この関数はその親空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | 空間識別子 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

センサーの識別子が指定されると、この関数はその親空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | センサーの識別子 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

デバイスの識別子が指定されると、この関数はその親空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | デバイスの識別子 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

センサーの識別子が指定されると、この関数はその親デバイスを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | センサーの識別子 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

空間識別子が指定されると、この関数は空間からプロパティとその値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |
| *propertyName* | `string` | 空間プロパティの名前 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

センサーの識別子が指定されると、この関数はセンサーからプロパティとその値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | センサーの識別子 |
| *propertyName* | `string` | センサー プロパティの名前 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

デバイスの識別子が指定されると、この関数はデバイスからプロパティとその値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | デバイスの識別子 |
| *propertyName* | `string` | デバイス プロパティの名前 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

この関数は、指定されたデータ型のセンサー オブジェクトの値を設定します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | センサーの識別子 |
| *dataType*  | `string` | センサーのデータ型 |
| *value*  | `string` | 値 |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

この関数は、指定されたデータ型の空間オブジェクトの値を設定します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |
| *dataType* | `string` | データ型 |
| *value* | `string` | 値 |

### <a name="logmessage"></a>log(message)

この関数は、ユーザー定義関数内での次のメッセージをログに記録します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *message* | `string` | 記録されるメッセージ |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

この関数は、ディスパッチされるカスタム通知を送信します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | グラフのオブジェクト識別子。 例: 空間、センサー、デバイスの ID。|
| *topologyObjectType*  | `string` | 例: センサー、デバイス。|
| *payload*  | `string` | 通知と共に送信される JSON ペイロード。 |

## <a name="return-types"></a>戻り値の型

次のモデルは、前述のクライアント リファレンスからのリターン オブジェクトを記述しています。

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

この関数は、現在の空間の親空間を返します。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

この関数は、現在の空間の子センサーを返します。

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

この関数は、現在の空間の子デバイスを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

この関数は、現在の空間の拡張プロパティとその値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 拡張プロパティの名前 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

この関数は、現在の空間の値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 値の名前 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

この関数は、現在の空間の履歴の値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 値の名前 |

#### <a name="notifypayload"></a>Notify(payload)

この関数は、指定されたペイロードを含む通知を送信します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 通知に含める JSON ペイロード |

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

この関数は、現在のデバイスの親空間を返します。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

この関数は、現在のデバイスの子センサーを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

この関数は、現在のデバイスの拡張プロパティとその値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 拡張プロパティの名前 |

#### <a name="notifypayload"></a>Notify(payload)

この関数は、指定されたペイロードを含む通知を送信します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 通知に含める JSON ペイロード |

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

この関数は、現在のセンサーの親空間を返します。

#### <a name="device--device"></a>Device() ⇒ `device`

この関数は、現在のセンサーの親デバイスを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

この関数は、現在のセンサーの拡張プロパティとその値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 拡張プロパティの名前 |

#### <a name="value--value"></a>Value() ⇒ `value`

この関数は、現在のセンサーの値を返します。

#### <a name="history--value"></a>History() ⇒ `value[]`

この関数は、現在のセンサーの履歴の値を返します。

#### <a name="notifypayload"></a>Notify(payload)

この関数は、指定されたペイロードを含む通知を送信します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 通知に含める JSON ペイロード |

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

- イベントの送信先となる [Azure Digital Twins エンドポイントを作成する](how-to-egress-endpoints.md)方法を学習する。

- Azure Digital Twins エンドポイントの詳細については、[エンドポイントの詳細](concepts-events-routing.md)をご覧ください。
