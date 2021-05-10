---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 51f66a674f9e730670084a43bd7bf059a0742cc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582793"
---
## <a name="model-id-announcement"></a>モデル ID のアナウンス

モデル ID をアナウンスするには、デバイスが接続情報に含まれている必要があります。

```java
ClientOptions options = new ClientOptions();
options.setModelId(MODEL_ID);
deviceClient = new DeviceClient(deviceConnectionString, protocol, options);
```

`ClientOptions` のオーバーロードは、接続を初期化するために使用されるすべての `DeviceClient` メソッドで使用できます。

> [!TIP]
> モジュールと IoT Edge には、`DeviceClient` ではなく `ModuleClient` を使用します。

> [!TIP]
> デバイスでモデル ID を設定できるのは、このときだけです。デバイスを接続した後に更新することはできません。

## <a name="dps-payload"></a>DPS ペイロード

[Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) を使用するデバイスには、次の JSON ペイロードを使用して、プロビジョニング プロセス中に使用される `modelId` を含めることができます。

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>テレメトリ、プロパティ、およびコマンドを実装する

[IoT プラグ アンド プレイ モデルのコンポーネントの概要](../articles/iot-pnp/concepts-modeling-guide.md)に関するページで説明されているように、デバイス ビルダーは、コンポーネントを使用してデバイスを記述するかどうかを決定する必要があります。 コンポーネントを使用する場合、デバイスはこのセクションで説明されている規則に従う必要があります。

### <a name="telemetry"></a>テレメトリ

既定のコンポーネントには、特別なプロパティは必要ありません。

入れ子になったコンポーネントを使用する場合、デバイスでは、メッセージ プロパティにコンポーネント名を設定する必要があります。

```java
private static void sendTemperatureTelemetry(String componentName) {
  double currentTemperature = temperature.get(componentName);

  Map<String, Object> payload = singletonMap("temperature", currentTemperature);

  Message message = new Message(gson.toJson(payload));
  message.setContentEncoding("utf-8");
  message.setContentTypeFinal("application/json");

  if (componentName != null) {
      message.setProperty("$.sub", componentName);
  }
  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
}
```

### <a name="read-only-properties"></a>読み取り専用プロパティ

既定のコンポーネントからのプロパティの報告には、特別なコンストラクトは必要ありません。

```java
Property reportedProperty = new Property("maxTempSinceLastReboot", 38.7);

deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

入れ子になったコンポーネントを使用する場合、次のコンポーネントの名前内にプロパティを作成する必要があります。

```java
Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("maxTemperature", 38.7);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty)

deviceClient.sendReportedProperties(reportedProperty);
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>書き込み可能なプロパティ

これらのプロパティは、デバイスから設定するか、ソリューションから更新することができます。 ソリューションからプロパティを更新すると、クライアントでは `DeviceClient` または `ModuleClient` でコールバックとして通知を受け取ります。 IoT プラグ アンド プレイ規則に従うために、デバイスからサービスに対して、プロパティが正常に受信されたことを通知する必要があります。

#### <a name="report-a-writable-property"></a>書き込み可能なプロパティを報告する

デバイスから書き込み可能なプロパティが報告された場合は、規則に定義されている `ack` 値を含める必要があります。

既定のコンポーネントから書き込み可能なプロパティを報告するには、次のようにします。

```java
@AllArgsConstructor
private static class EmbeddedPropertyUpdate {
  @NonNull
  @SerializedName("value")
  public Object value;
  @NonNull
  @SerializedName("ac")
  public Integer ackCode;
  @NonNull
  @SerializedName("av")
  public Integer ackVersion;
  @SerializedName("ad")
  public String ackDescription;
}

EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(23.2, 200, 3, "Successfully updated target temperature");
Property reportedPropertyCompleted = new Property("targetTemperature", completedUpdate);
deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

入れ子になったコンポーネントから書き込み可能なプロパティを報告するには、ツインにマーカーを含める必要があります。

```java
Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
    put("value", 23.2);
    put("ac", 200);
    put("av", 3);
    put("ad", "complete");
}};

Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("targetTemperature", embeddedProperty);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty));

deviceClient.sendReportedProperties(reportedProperty);
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>必要なプロパティの更新をサブスクライブする

サービスでは、接続されたデバイスで通知をトリガーする目的のプロパティを更新できます。 この通知には、更新を識別するバージョン番号など、更新された目的のプロパティが含まれます。 デバイスでは、報告されたプロパティと同じ `ack` メッセージで応答する必要があります。

既定のコンポーネントでは、1 つのプロパティを参照し、受け取ったバージョンで報告される `ack` を作成します。

```java
private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, 200, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
  Collections.singletonMap(
    new Property("targetTemperature", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), null));
deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

デバイス ツインでは、目的のセクションと報告されるセクションにプロパティが表示されます。

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

入れ子になったコンポーネントでは、コンポーネント名でラップされた目的のプロパティを受け取り、`ack` で報告されたプロパティを報告する必要があります。

```java
private static final Map<String, Double> temperature = new HashMap<>();

private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        String componentName = (String) context;

        if (property.getKey().equalsIgnoreCase(componentName)) {
            double targetTemperature = (double) ((TwinCollection) property.getValue()).get(propertyName);

            Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
                put("value", temperature.get(componentName));
                put("ac", 200);
                put("av", property.getVersion().longValue());
                put("ad", "Successfully updated target temperature.");
            }};

            Map<String, Object> componentProperty = new HashMap<String, Object>() {{
                put("__t", "c");
                put(propertyName, embeddedProperty);
            }};

            Set<Property> completedPropertyPatch = new Property(componentName, componentProperty));

            deviceClient.sendReportedProperties(completedPropertyPatch);
        } else {
            log.debug("Property: Received an unrecognized property update from service.");
        }
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat1", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat1")),
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat2", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat2"))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

入れ子になったコンポーネントのデバイス ツインは、目的のセクションと報告されたセクションを次のように示します。

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>コマンド

既定のコンポーネントは、サービスから呼び出されたときに、コマンド名を受け取ります。

入れ子になったコンポーネントは、コンポーネント名と `*` 区切り記号が先頭に付けられたコマンド名を受け取ります。

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);

// ...
private static final Map<String, Double> temperature = new HashMap<>();

private static class MethodCallback implements DeviceMethodCallback {
  final String reboot = "reboot";
  final String getMaxMinReport1 = "thermostat1*getMaxMinReport";
  final String getMaxMinReport2 = "thermostat2*getMaxMinReport";

  @Override
  public DeviceMethodData call(String methodName, Object methodData, Object context) {
    String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);

    switch (methodName) {
      case reboot:
        int delay = gson.fromJson(jsonRequest, Integer.class);

        Thread.sleep(delay * 1000);

        temperature.put("thermostat1", 0.0d);
        temperature.put("thermostat2", 0.0d);

        return new DeviceMethodData(200, null);

      // ...

      default:
        log.debug("Command: command=\"{}\" is not implemented, no action taken.", methodName);
          return new DeviceMethodData(404, null);
    }
  }
}
```

#### <a name="request-and-response-payloads"></a>要求と応答のペイロード

コマンドでは、型を使用して、要求と応答のペイロードを定義します。 デバイスでは、受け取った入力パラメーターを逆シリアル化し、応答をシリアル化する必要があります。

ペイロードで定義された複合型を使用してコマンドを実装する例を次に示します。

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

次のコード スニペットは、デバイスでこのコマンド定義がどのように実装されるかを示しています。これには、シリアル化と逆シリアル化を有効にするために使用される型が含まれます。

```java
deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), "getMaxMinReport", new MethodIotHubEventCallback(), "getMaxMinReport");

// ...

private static class GetMaxMinReportMethodCallback implements DeviceMethodCallback {
    String commandName = "getMaxMinReport";

    @Override
    public DeviceMethodData call(String methodName, Object methodData, Object context) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = gson.fromJson(jsonRequest, Date.class);

        String responsePayload = String.format(
                "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                maxTemp,
                minTemp,
                avgTemp,
                since,
                endTime);

        return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
    }
}
```

> [!Tip]
> 要求と応答の名前は、ネットワーク経由で送信されるシリアル化されたペイロードには存在しません。
