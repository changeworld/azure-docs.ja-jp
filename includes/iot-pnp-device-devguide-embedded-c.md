---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 09/07/2021
ms.openlocfilehash: 3f580069fc44f676bc6fcffa40bd74256a818a50
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521392"
---
## <a name="sdks"></a>SDK

この記事に含まれるコード スニペットは、[Azure RTOS 用の Azure IoT ミドルウェア アドオン](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)を使用するサンプルに基づいています。 このアドオンは、[Azure RTOS](/azure/rtos/overview-rtos) と[埋め込み C 用の Azure SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) の間のバインディング レイヤーです。

この記事に含まれるコードは、次のサンプルに基づいています。

- [IoT プラグ アンド プレイ サーモスタットのサンプル](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/samples/sample_azure_iot_embedded_sdk_pnp.c)
- [IoT プラグ アンド プレイ温度コントローラーのサンプル](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot/samples/sample_pnp_temperature_controller)

## <a name="model-id-announcement"></a>モデル ID のアナウンス

モデル ID をアナウンスするには、デバイスが接続情報に含まれている必要があります。

```c
#include "nx_azure_iot_hub_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"

// ...

status = nx_azure_iot_hub_client_model_id_set(iothub_client_ptr, (UCHAR *)SAMPLE_PNP_MODEL_ID, sizeof(SAMPLE_PNP_MODEL_ID) - 1);
```

> [!TIP]
> デバイスでモデル ID を設定できるのは、このときだけです。デバイスを接続した後に更新することはできません。

## <a name="dps-payload"></a>DPS ペイロード

[Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) を使用するデバイスには、次の JSON ペイロードを使用して、プロビジョニング プロセス中に使用される `modelId` を含めることができます。

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

サンプルでは、次のコードを使用してこのペイロードを送信します。

```c
#include "nx_azure_iot_provisioning_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"
#define SAMPLE_PNP_DPS_PAYLOAD "{\"modelId\":\"" SAMPLE_PNP_MODEL_ID "\"}"

// ...

status = nx_azure_iot_provisioning_client_registration_payload_set(prov_client_ptr, (UCHAR *)SAMPLE_PNP_DPS_PAYLOAD, sizeof(SAMPLE_PNP_DPS_PAYLOAD) - 1);
```

## <a name="use-components"></a>コンポーネントを使う

[IoT プラグ アンド プレイ モデルのコンポーネントの概要](../articles/iot-develop/concepts-modeling-guide.md)に関するページで説明されているように、デバイス ビルダーは、コンポーネントを使用してデバイスを記述するかどうかを決定する必要があります。 コンポーネントを使用する場合、デバイスは次のセクションで説明されている規則に従う必要があります。 コンポーネントに対する IoT プラグ アンド プレイ規則の操作を簡略化するために、サンプルでは [nx_azure_iot_hub_client.h](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/nx_azure_iot_hub_client.h) 内のヘルパー関数を使用しています。

## <a name="telemetry"></a>テレメトリ

既定のコンポーネントを使用する場合、テレメトリ メッセージに特別なプロパティを追加する必要はありません。

入れ子になったコンポーネントを使用する場合、デバイスでは、メッセージ プロパティにコンポーネント名を設定する必要があります。 次のスニペットで、`component_name_ptr` はコンポーネントの名前 (`thermostat1` など) です。 *nx_azure_iot_pnp_helpers.h* で定義されているヘルパー関数 `nx_azure_iot_pnp_helper_telemetry_message_create` によって、コンポーネント名を持つメッセージ プロパティが追加されます。

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR telemetry_name[] = "temperature";

// ...

UINT sample_pnp_thermostat_telemetry_send(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT status;
NX_PACKET *packet_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
UINT buffer_length;

    // ...

    /* Create a telemetry message packet. */
    if ((status = nx_azure_iot_pnp_helper_telemetry_message_create(iothub_client_ptr, handle -> component_name_ptr,
        handle -> component_name_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_hub_client_telemetry_send(iothub_client_ptr, packet_ptr,
        (UCHAR *)scratch_buffer, buffer_length, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    return(status);
}
```

## <a name="read-only-properties"></a>読み取り専用プロパティ

既定のコンポーネントからのプロパティの報告には、特別なコンストラクトは必要ありません。

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

// ...

static UINT sample_build_reported_property(NX_AZURE_IOT_JSON_WRITER *json_builder_ptr, double temp)
{
UINT ret;

    if (nx_azure_iot_json_writer_append_begin_object(json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(json_builder_ptr,
            (UCHAR *)reported_max_temp_since_last_reboot,
            sizeof(reported_max_temp_since_last_reboot) - 1,
            temp, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_end_object(json_builder_ptr))
    {
        ret = 1;
        printf("Failed to build reported property\r\n");
    }
    else
    {
        ret = 0;
    }

    return(ret);
}

// ...

if ((status = sample_build_reported_property(&json_builder, device_max_temp)))
{
    // ...
}

reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(&(context -> iothub_client),
    scratch_buffer,
    reported_properties_length,
    &request_id, &response_status,
    &reported_property_version,
    (5 * NX_IP_PERIODIC_RATE))))
{
    // ...
}
```

デバイス ツインは、次の reported プロパティで更新されます。

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

入れ子になったコンポーネントを使用する場合は、コンポーネントの名前内にプロパティを作成し、マーカーを含める必要があります。 次のスニペットで、`component_name_ptr` はコンポーネントの名前 (`thermostat1` など) です。 *nx_azure_iot_pnp_helpers.h* 内に定義されているヘルパー関数 `nx_azure_iot_pnp_helper_build_reported_property` によって、reported プロパティが正しい形式で作成されます。

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

UINT sample_pnp_thermostat_report_max_temp_since_last_reboot_property(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT reported_properties_length;
UINT status;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_builder;
ULONG reported_property_version;

    // ...

    if ((status = nx_azure_iot_pnp_helper_build_reported_property(handle -> component_name_ptr,
        handle -> component_name_length,
        append_max_temp, (VOID *)handle,
        &json_builder)))
    {
        // ...
    }

    reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
    if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(iothub_client_ptr,
        scratch_buffer,
        reported_properties_length,
        &request_id, &response_status,
        &reported_property_version,
        (5 * NX_IP_PERIODIC_RATE))))
    {
        // ...
    }

    // ...
}
```

デバイス ツインは、次の reported プロパティで更新されます。

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

## <a name="writable-properties"></a>書き込み可能なプロパティ

これらのプロパティは、デバイスから設定するか、ソリューションから更新することができます。 IoT プラグ アンド プレイ規則に従うために、デバイスからサービスに対して、プロパティが正常に受信されたことを通知する必要があります。

### <a name="report-a-writable-property"></a>書き込み可能なプロパティを報告する

デバイスから書き込み可能なプロパティが報告された場合は、規則に定義されている `ack` 値を含める必要があります。

既定のコンポーネントから書き込み可能なプロパティを報告するには、次のようにします。

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_temp_property_name[] = "targetTemperature";
static const CHAR reported_value_property_name[] = "value";
static const CHAR reported_status_property_name[] = "ac";
static const CHAR reported_version_property_name[] = "av";
static const CHAR reported_description_property_name[] = "ad";

// ...

static VOID sample_send_target_temperature_report(SAMPLE_CONTEXT *context, double current_device_temp_value,
    UINT status, UINT version, UCHAR *description_ptr,
    UINT description_len)
{
NX_AZURE_IOT_JSON_WRITER json_builder;
UINT bytes_copied;
UINT response_status;
UINT request_id;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_name(&json_builder,
            (UCHAR *)reported_temp_property_name,
            sizeof(reported_temp_property_name) - 1) ||
        nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_with_double_value(&json_builder,
            (UCHAR *)reported_value_property_name,
            sizeof(reported_value_property_name) - 1,
            current_device_temp_value, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_status_property_name,
            sizeof(reported_status_property_name) - 1,
            (int32_t)status) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_version_property_name,
            sizeof(reported_version_property_name) - 1,
            (int32_t)version) ||
        nx_azure_iot_json_writer_append_property_with_string_value(&json_builder,
            (UCHAR *)reported_description_property_name,
            sizeof(reported_description_property_name) - 1,
            description_ptr, description_len) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder))
    {
        // ...
    }
    else
    // ...
}
```

デバイス ツインは、次の reported プロパティで更新されます。

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
  }
}
```

入れ子になったコンポーネントから書き込み可能なプロパティを報告するには、ツインにマーカーを含める必要があると共に、コンポーネントの名前内にプロパティを作成する必要があります。 次のスニペットで、`component_name_ptr` はコンポーネントの名前 (`thermostat1` など) です。 *nx_azure_iot_pnp_helpers.h* 内に定義されているヘルパー関数 `nx_azure_iot_pnp_helper_build_reported_property_with_status` によって、reported プロパティのペイロードが作成されます。

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_send_target_temperature_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr, double temp,
    INT status_code, UINT version, const CHAR *description)
{
UINT bytes_copied;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_writer;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_pnp_helper_build_reported_property_with_status(handle -> component_name_ptr, handle -> component_name_length,
        (UCHAR *)target_temp_property_name,
        sizeof(target_temp_property_name) - 1,
        append_temp, (VOID *)&temp, status_code,
        (UCHAR *)description,
        strlen(description), version, &json_writer))
    {
        // ...
    }
    else
    {
        // ...
    }

    // ...
}
```

デバイス ツインは、次の reported プロパティで更新されます。

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>必要なプロパティの更新をサブスクライブする

サービスでは、接続されたデバイスで通知をトリガーする目的のプロパティを更新できます。 この通知には、更新された目的のプロパティと、更新を識別するバージョン番号が含まれます。 デバイスでは、サービスに返送される `ack` メッセージにこのバージョン番号を含める必要があります。

既定のコンポーネントでは、1 つのプロパティを参照し、受け取ったバージョンで報告される `ack` を作成します。

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR temp_response_description[] = "success";

// ...

static UINT sample_parse_desired_temp_property(SAMPLE_CONTEXT *context,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    UINT is_partial)
{
double parsed_value;
UINT version;
NX_AZURE_IOT_JSON_READER copy_json_reader;
UINT status;

    // ...

    copy_json_reader = *json_reader_ptr;
    if (sample_json_child_token_move(&copy_json_reader,
            (UCHAR *)desired_version_property_name,
            sizeof(desired_version_property_name) - 1) ||
        nx_azure_iot_json_reader_token_int32_get(&copy_json_reader, (int32_t *)&version))
    {
        // ...
    }

    // ...

    sample_send_target_temperature_report(context, current_device_temp, 200,
        (UINT)version, (UCHAR *)temp_response_description,
        sizeof(temp_response_description) - 1);

    // ...
}
```

入れ子になったコンポーネントでは、コンポーネント名でラップされた目的のプロパティを受け取り、受け取ったバージョンで報告される `ack` を作成します。

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR target_temp_property_name[] = "targetTemperature";
static const CHAR temp_response_description_success[] = "success";
static const CHAR temp_response_description_failed[] = "failed";

// ...

UINT sample_pnp_thermostat_process_property_update(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr,
    UCHAR *component_name_ptr, UINT component_name_length,
    UCHAR *property_name_ptr, UINT property_name_length,
    NX_AZURE_IOT_JSON_READER *property_value_reader_ptr, UINT version)
{
double parsed_value = 0;
INT status_code;
const CHAR *description;

    // ...

    if (property_name_length != (sizeof(target_temp_property_name) - 1) ||
        strncmp((CHAR *)property_name_ptr, (CHAR *)target_temp_property_name, property_name_length) != 0)
    {
        // ...
    }
    else if (nx_azure_iot_json_reader_token_double_get(property_value_reader_ptr, &parsed_value))
    {
        status_code = 401;
        description = temp_response_description_failed;
    }
    else
    {
        status_code = 200;
        description = temp_response_description_success;

        // ...
    }

    sample_send_target_temperature_report(handle, iothub_client_ptr, parsed_value,
                                          status_code, version, description);

    // ...
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
          "ad": "success"
      }
    }
  }
}
```

## <a name="commands"></a>コマンド

既定のコンポーネントは、サービスから呼び出されたときに、コマンド名を受け取ります。

入れ子になったコンポーネントは、コンポーネント名と `*` 区切り記号が先頭に付けられたコマンド名を受け取ります。 次のスニペットでは、*nx_azure_iot_pnp_helpers.h* 内に定義されているヘルパー関数 `nx_azure_iot_pnp_helper_command_name_parse` によって、サービスからデバイスに届いたメッセージからコンポーネント名とコマンド名が解析されます。

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_direct_method_action(SAMPLE_CONTEXT *sample_context_ptr)
{
NX_PACKET *packet_ptr;
UINT status;
USHORT method_name_length;
const UCHAR *method_name_ptr;
USHORT context_length;
VOID *context_ptr;
UINT component_name_length;
const UCHAR *component_name_ptr;
UINT pnp_command_name_length;
const UCHAR *pnp_command_name_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
NX_AZURE_IOT_JSON_READER json_reader;
NX_AZURE_IOT_JSON_READER *json_reader_ptr;
UINT status_code;
UINT response_length;

    // ...

    if ((status = nx_azure_iot_hub_client_direct_method_message_receive(&(sample_context_ptr -> iothub_client),
        &method_name_ptr, &method_name_length,
        &context_ptr, &context_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_pnp_helper_command_name_parse(method_name_ptr, method_name_length,
        &component_name_ptr, &component_name_length,
        &pnp_command_name_ptr,
        &pnp_command_name_length)) != NX_AZURE_IOT_SUCCESS)
    {
        // ...
    }
    
    // ...

    else
    {
        // ...

        if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_1, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_2, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if((status = sample_pnp_temp_controller_process_command(component_name_ptr, component_name_length,
            pnp_command_name_ptr, pnp_command_name_length,
            json_reader_ptr, &json_writer,
            &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else
        {
            printf("Failed to find any handler for method %.*s\r\n", method_name_length, method_name_ptr);
            status_code = SAMPLE_COMMAND_NOT_FOUND_STATUS;
            response_length = 0;
        }

        // ...
    }
}
```

### <a name="request-and-response-payloads"></a>要求と応答のペイロード

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

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR report_max_temp_name[] = "maxTemp";
static const CHAR report_min_temp_name[] = "minTemp";
static const CHAR report_avg_temp_name[] = "avgTemp";
static const CHAR report_start_time_name[] = "startTime";
static const CHAR report_end_time_name[] = "endTime";
static const CHAR fake_start_report_time[] = "2020-01-10T10:00:00Z";
static const CHAR fake_end_report_time[] = "2023-01-10T10:00:00Z";

// ...

static UINT sample_get_maxmin_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    NX_AZURE_IOT_JSON_WRITER *out_json_builder_ptr)
{
UINT status;
UCHAR *start_time = (UCHAR *)fake_start_report_time;
UINT start_time_len = sizeof(fake_start_report_time) - 1;
UCHAR time_buf[32];

    // ...

    /* Build the method response payload */
    if (nx_azure_iot_json_writer_append_begin_object(out_json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_max_temp_name,
            sizeof(report_max_temp_name) - 1,
            handle -> maxTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_min_temp_name,
            sizeof(report_min_temp_name) - 1,
            handle -> minTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_avg_temp_name,
            sizeof(report_avg_temp_name) - 1,
            handle -> avgTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_start_time_name,
            sizeof(report_start_time_name) - 1,
            (UCHAR *)start_time, start_time_len) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_end_time_name,
            sizeof(report_end_time_name) - 1,
            (UCHAR *)fake_end_report_time,
            sizeof(fake_end_report_time) - 1) ||
        nx_azure_iot_json_writer_append_end_object(out_json_builder_ptr))
    {
        status = NX_NOT_SUCCESSFUL;
    }
    else
    {
        status = NX_AZURE_IOT_SUCCESS;
    }

    return(status);
}
```

> [!Tip]
> 要求と応答の名前は、ネットワーク経由で送信されるシリアル化されたペイロードには存在しません。
