---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: d215da660929c66514f9d0cf5f3161c83a1c330c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582779"
---
## <a name="model-id-announcement"></a>モデル ID のアナウンス

モデル ID をアナウンスするには、デバイスが接続情報に含まれている必要があります。

```c
static const char g_ThermostatModelId[] = "dtmi:com:example:Thermostat;1";
IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceHandle = NULL;
deviceHandle = CreateDeviceClientLLHandle();
iothubResult = IoTHubDeviceClient_LL_SetOption(
    deviceHandle, OPTION_MODEL_ID, g_ThermostatModelId);
```

> [!TIP]
> モジュールと IoT Edge には、`IoTHubDeviceClient_LL` ではなく `IoTHubModuleClient_LL` を使用します。

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

```c
void PnP_ThermostatComponent_SendTelemetry(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    IOTHUB_CLIENT_RESULT iothubResult;

    char temperatureStringBuffer[32];

    if (snprintf(
        temperatureStringBuffer,
        sizeof(temperatureStringBuffer),
        g_temperatureTelemetryBodyFormat,
        pnpThermostatComponent->currentTemperature) < 0)
    {
        LogError("snprintf of current temperature telemetry failed");
    }
    else if ((messageHandle = PnP_CreateTelemetryMessageHandle(
        pnpThermostatComponent->componentName, temperatureStringBuffer)) == NULL)
    {
        LogError("Unable to create telemetry message");
    }
    else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(
        deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send telemetry message, error=%d", iothubResult);
    }

    IoTHubMessage_Destroy(messageHandle);
}

// ...

PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
```

### <a name="read-only-properties"></a>読み取り専用プロパティ

既定のコンポーネントからのプロパティの報告には、特別なコンストラクトは必要ありません。

```c
static const char g_maxTemperatureSinceRebootFormat[] = "{\"maxTempSinceLastReboot\":%.2f}";

char maxTemperatureSinceRebootProperty[256];

snprintf(
    maxTemperatureSinceRebootProperty,
    sizeof(maxTemperatureSinceRebootProperty),
    g_maxTemperatureSinceRebootFormat,
    38.7);

IOTHUB_CLIENT_RESULT iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)maxTemperatureSinceRebootProperty,
    strlen(maxTemperatureSinceRebootProperty), NULL, NULL));
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

```c
STRING_HANDLE PnP_CreateReportedProperty(
    const char* componentName,
    const char* propertyName,
    const char* propertyValue
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":%s}",
            propertyName, propertyValue);
    }
    else 
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":%s}}",
            componentName, propertyName, propertyValue);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

void PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent =
        (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    char maximumTemperatureAsString[32];
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;

    if (snprintf(maximumTemperatureAsString, sizeof(maximumTemperatureAsString),
        "%.2f", pnpThermostatComponent->maxTemperature) < 0)
    {
        LogError("Unable to create max temp since last reboot string for reporting result");
    }
    else if ((jsonToSend = PnP_CreateReportedProperty(
                pnpThermostatComponent->componentName,
                g_maxTempSinceLastRebootPropertyName,
                maximumTemperatureAsString)) == NULL)
    {
        LogError("Unable to build max temp since last reboot property");
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
                deviceClientLL,
                (const unsigned char*)jsonToSendStr,
                jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
        {
            LogError("Unable to send reported state, error=%d", iothubClientResult);
        }
        else
        {
            LogInfo("Sending maximumTemperatureSinceLastReboot property to IoTHub for component=%s",
                pnpThermostatComponent->componentName);
        }
    }

    STRING_delete(jsonToSend);
}

// ...

PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
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

```c
IOTHUB_CLIENT_RESULT iothubClientResult;
char targetTemperatureResponseProperty[256];

snprintf(
    targetTemperatureResponseProperty,
    sizeof(targetTemperatureResponseProperty),
    "{\"targetTemperature\":{\"value\":%.2f,\"ac\":%d,\"av\":%d,\"ad\":\"%s\"}}",
    23.2, 200, 3, "Successfully updated target temperature");

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)targetTemperatureResponseProperty,
    strlen(targetTemperatureResponseProperty), NULL, NULL);
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

```c
STRING_HANDLE PnP_CreateReportedPropertyWithStatus(const char* componentName,
    const char* propertyName, const char* propertyValue,
    int result, const char* description, int ackVersion
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}",
            propertyName, propertyValue,
            result, description, ackVersion);
    }
    else
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}}",
            componentName, propertyName, propertyValue,
            result, description, ackVersion);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

// ...

char targetTemperatureAsString[32];
IOTHUB_CLIENT_RESULT iothubClientResult;
STRING_HANDLE jsonToSend = NULL;

snprintf(targetTemperatureAsString,
    sizeof(targetTemperatureAsString),
    "%.2f",
    23.2);
jsonToSend = PnP_CreateReportedPropertyWithStatus(
    "thermostat1",
    "targetTemperature",
    targetTemperatureAsString,
    200,
    "complete",
    3);

const char* jsonToSendStr = STRING_c_str(jsonToSend);
size_t jsonToSendStrLen = strlen(jsonToSendStr);

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)jsonToSendStr,
    jsonToSendStrLen, NULL, NULL);

STRING_delete(jsonToSend);
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

```c
static void Thermostat_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback)
{
    // The device handle associated with this request is passed as the context,
    // since we will need to send reported events back.
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL =
        (IOTHUB_DEVICE_CLIENT_LL_HANDLE)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    JSON_Value* versionValue = NULL;
    JSON_Value* targetTemperatureValue = NULL;

    jsonStr = CopyTwinPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    targetTemperatureValue = json_object_get_value(desiredObject, "targetTemperature"));
    versionValue = json_object_get_value(desiredObject, "$version"));
    json_value_get_type(versionValue);
    json_value_get_type(targetTemperatureValue);

    double targetTemperature = json_value_get_number(targetTemperatureValue);
    int version = (int)json_value_get_number(versionValue);

    // ...

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureReport(deviceClientLL, targetTemperature, 200, version, "Successfully updated target temperature");

    json_value_free(rootValue);
    free(jsonStr);
}

// ...

IOTHUB_CLIENT_RESULT iothubResult;
iothubResult = IoTHubDeviceClient_LL_SetDeviceTwinCallback(
    deviceHandle, Thermostat_DeviceTwinCallback, (void*)deviceHandle))
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

```c
bool PnP_ProcessTwinData(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size, const char** componentsInModel,
    size_t numComponentsInModel,
    PnP_PropertyCallbackFunction pnpPropertyCallback,
    void* userContextCallback)
{
    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    bool result;

    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    
    result = VisitDesiredObject(
        desiredObject, componentsInModel,
        numComponentsInModel, pnpPropertyCallback,
        userContextCallback);


    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...
static const char g_thermostatComponent1Name[] = "thermostat1";
static const size_t g_thermostatComponent1Size = sizeof(g_thermostatComponent1Name) - 1;
static const char g_thermostatComponent2Name[] = "thermostat2";

static const char* g_modeledComponents[] = {g_thermostatComponent1Name, g_thermostatComponent2Name};
static const size_t g_numModeledComponents = sizeof(g_modeledComponents) / sizeof(g_modeledComponents[0]);

static void PnP_TempControlComponent_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback
)
{
    PnP_ProcessTwinData(
        updateState, payload,
        size, g_modeledComponents,
        g_numModeledComponents,
        PnP_TempControlComponent_ApplicationPropertyCallback,
        userContextCallback);
}
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

```c
void PnP_ParseCommandName(
    const char* deviceMethodName,
    unsigned const char** componentName,
    size_t* componentNameSize,
    const char** pnpCommandName
)
{
    const char* separator;

    if ((separator = strchr(deviceMethodName, "*")) != NULL)
    {
        *componentName = (unsigned const char*)deviceMethodName;
        *componentNameSize = separator - deviceMethodName;
        *pnpCommandName = separator + 1;
    }
    else
    {
        *componentName = NULL;
        *componentNameSize = 0;
        *pnpCommandName = deviceMethodName;
    }
}

static int PnP_TempControlComponent_DeviceMethodCallback(
    const char* methodName,
    const unsigned char* payload,
    size_t size,
    unsigned char** response,
    size_t* responseSize,
    void* userContextCallback)
{
    (void)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    int result;
    unsigned const char *componentName;
    size_t componentNameSize;
    const char *pnpCommandName;

    *response = NULL;
    *responseSize = 0;

    // Parse the methodName into its componentName and CommandName.
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);

    // Parse the JSON of the payload request.
    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    if (componentName != NULL)
    {
        if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
        }
        else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
        }
        else
        {
            LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }
    else
    {
        LogInfo("Received PnP command for TemperatureController component, command=%s", pnpCommandName);
        if (strcmp(pnpCommandName, g_rebootCommand) == 0)
        {
            result = PnP_TempControlComponent_InvokeRebootCommand(rootValue);
        }
        else
        {
            LogError("PnP command=s%s is not supported by TemperatureController", pnpCommandName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }

    if (*response == NULL)
    {
        SetEmptyCommandResponse(response, responseSize, &result);
    }

    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...

PNP_DEVICE_CONFIGURATION g_pnpDeviceConfiguration;
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

#### <a name="request-and-response-payloads"></a>要求と応答のペイロード

コマンドでは、型を使用して、要求と応答のペイロードを定義します。 デバイスでは、受け取った入力パラメーターを逆シリアル化し、応答をシリアル化する必要があります。 ペイロードで定義された複合型を使用してコマンドを実装する例を次に示します。

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

```c
static const char g_maxMinCommandResponseFormat[] = "{\"maxTemp\":%.2f,\"minTemp\":%.2f,\"avgTemp\":%.2f,\"startTime\":\"%s\",\"endTime\":\"%s\"}";

// ...

static bool BuildMaxMinCommandResponse(
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent,
    unsigned char** response,
    size_t* responseSize)
{
    int responseBuilderSize = 0;
    unsigned char* responseBuilder = NULL;
    bool result;
    char currentTime[TIME_BUFFER_SIZE];

    BuildUtcTimeFromCurrentTime(currentTime, sizeof(currentTime));
    responseBuilderSize = snprintf(NULL, 0, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures /
        pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime, currentTime));

    responseBuilder = calloc(1, responseBuilderSize + 1));

    responseBuilderSize = snprintf(
        (char*)responseBuilder, responseBuilderSize + 1, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures / pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime,
        currentTime));

    *response = responseBuilder;
    *responseSize = (size_t)responseBuilderSize;

    return true;
}
```

> [!Tip]
> 要求と応答の名前は、ネットワーク経由で送信されるシリアル化されたペイロードには存在しません。
