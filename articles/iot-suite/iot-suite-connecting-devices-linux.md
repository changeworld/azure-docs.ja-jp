---
title: Linux で C を使用してデバイスを接続する | Microsoft Docs
description: C で記述され、Linux で実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2016
ms.author: dobett

---
# デバイスをリモート監視構成済みソリューションに接続する (Linux)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Linux で C のサンプル クライアントをビルドして実行する
次の手順では、リモート監視が事前構成されたソリューションと通信する単純なクライアント アプリケーションを C で記述し、Ubuntu Linux でビルドし実行する方法を示します。次の手順を完了するには、Ubuntu バージョン 15.04 または 15.10 が実行されているデバイスが必要です。次に進む前に、次のコマンドを使用して、前提条件となるパッケージを Ubuntu デバイスにインストールします。

```
sudo apt-get install cmake gcc g++
```

## デバイスにクライアント ライブラリをインストールする
Azure の IoT Hub クライアント ライブラリは、**apt-get** コマンドを使用してパッケージとして Ubuntu デバイスにインストールし、使用できます。IoT Hub クライアント ライブラリとヘッダー ファイルが含むまれるパッケージを Ubuntu コンピューターにインストールするには、次の手順を実行します。

1. AzureIoT リポジトリをコンピューターに追加します。
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. azure-iot-sdk-c-dev パッケージをインストールします
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## デバイスの動作を指定するコードを追加する
Ubuntu コンピューターで、**remote\_monitoring** という名前のフォルダーを作成します。**remote\_monitoring** フォルダーに、**main.c**、**remote\_monitoring.c**、**remote\_monitoring.h**、および **CMakeLists.txt** の 4 ファイルを作成します。

IoT Hub シリアライザー クライアント ライブラリでは、モデルを使用して、デバイスが IoT Hub に送信するメッセージの形式と、デバイスが応答する IoT Hub からのコマンドを指定します。

1. テキスト エディターで、**remote\_monitoring.c** ファイルを開きます。次の `#include` ステートメントを追加します。
   
    ```
    #include "iothubtransportamqp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```
2. `#include` ステートメントの後に次の変数宣言を追加します。リモート監視ソリューション ダッシュボードから、プレースホルダ [Device Id] と [Device Key] の値をデバイス用の値に置き換えます。ダッシュボードの IoT Hub ホスト名を使用して、[IoTHub Name] を置き換えます。たとえば、IoT Hub ホスト名が **contoso.azure-devices.net** である場合は、[IoTHub Name] を **contoso** に置き換えます。
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```
3. 次のコードを追加して、デバイスと IoT Hub との通信を可能にするモデルを定義します。このモデルでは、デバイスがテレメトリとして温度、外部温度、湿度、およびデバイス ID を送信することを指定します。デバイスは、自身についてのメタデータも IoT Hub に送信します。これには、自身がサポートするコマンドの一覧も含まれます。そして、**SetTemperature** コマンドと **SetHumidity** コマンドに応答します。
   
    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);
   
    DECLARE_STRUCT(SystemProperties,
      ascii_char_ptr, DeviceID,
      _Bool, Enabled
    );
   
    DECLARE_STRUCT(DeviceProperties,
      ascii_char_ptr, DeviceID,
      _Bool, HubEnabledState
    );
   
    DECLARE_MODEL(Thermostat,
   
      /* Event data (temperature, external temperature and humidity) */
      WITH_DATA(int, Temperature),
      WITH_DATA(int, ExternalTemperature),
      WITH_DATA(int, Humidity),
      WITH_DATA(ascii_char_ptr, DeviceId),
   
      /* Device Info - This is command metadata + some extra fields */
      WITH_DATA(ascii_char_ptr, ObjectType),
      WITH_DATA(_Bool, IsSimulatedDevice),
      WITH_DATA(ascii_char_ptr, Version),
      WITH_DATA(DeviceProperties, DeviceProperties),
      WITH_DATA(ascii_char_ptr_no_quotes, Commands),
   
      /* Commands implemented by the device */
      WITH_ACTION(SetTemperature, int, temperature),
      WITH_ACTION(SetHumidity, int, humidity)
    );
   
    END_NAMESPACE(Contoso);
    ```

### デバイスの動作を実装するコードを追加する
デバイスがハブからコマンドを受信したときに実行する関数と、シミュレートされたテレメトリをハブに送信するためのコードを追加します。

1. モデルに定義された **SetTemperature** コマンドと **SetHumidity** コマンドをデバイスが受信したときに実行される次の関数を追加します。
   
    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }
   
    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```
2. IoT Hub にメッセージを送信する関数を追加します。
   
    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }
   
        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```
3. SDK のシリアル化ライブラリを連結する関数を追加します。
   
    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```
4. IoT Hub に接続し、メッセージを送受信し、ハブから切断するための関数を追加します。デバイスは、接続後すぐに自身に関するメタデータ (サポートするコマンドを含む) を IoT Hub に送信します。これにより、ソリューションはダッシュボードのデバイスの状態を **[実行中]** に更新することができます。
   
    ```
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
      {
        if (serializer_init(NULL) != SERIALIZER_OK)
        {
          printf("Failed on serializer_init\r\n");
        }
        else
        {
          IOTHUB_CLIENT_CONFIG config;
          IOTHUB_CLIENT_HANDLE iotHubClientHandle;
   
          config.deviceId = deviceId;
          config.deviceKey = deviceKey;
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
          iotHubClientHandle = IoTHubClient_Create(&config);
          if (iotHubClientHandle == NULL)
          {
            (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
          }
          else
          {
            Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
            if (thermostat == NULL)
            {
              (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
            }
            else
            {
              STRING_HANDLE commandsMetadata;
   
              if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
              {
                printf("unable to IoTHubClient_SetMessageCallback\r\n");
              }
              else
              {
   
                /* send the device info upon startup so that the cloud app knows
                   what commands are available and the fact that the device is up */
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = false;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = true;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;
   
                commandsMetadata = STRING_new();
                if (commandsMetadata == NULL)
                {
                  (void)printf("Failed on creating string for commands metadata\r\n");
                }
                else
                {
                  /* Serialize the commands metadata as a JSON string before sending */
                  if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                  {
                    (void)printf("Failed serializing commands metadata\r\n");
                  }
                  else
                  {
                    unsigned char* buffer;
                    size_t bufferSize;
                    thermostat->Commands = (char*)STRING_c_str(commandsMetadata);
   
                    /* Here is the actual send of the Device Info */
                    if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                    {
                      (void)printf("Failed serializing\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize);
                    }
   
                  }
   
                  STRING_delete(commandsMetadata);
                }
   
                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;
   
                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;
   
                  (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);
   
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }
   
                  ThreadAPI_Sleep(1000);
                }
              }
   
              DESTROY_MODEL_INSTANCE(thermostat);
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
        platform_deinit();
      }
    }
    ```
   
    起動時に IoT Hub に送信される **DeviceInfo** メッセージの例を次に示します。
   
    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
   
    IoT Hub に送信される**テレメトリ** メッセージの例を次に示します。
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
   
    IoT Hub から受信する**コマンド**の例を次に示します。
   
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

### remote\_monitoring\_run 関数を呼び出すコードを追加する
テキスト エディターで、**remote\_monitoring.h** ファイルを開きます。次のコードを追加します。

```
void remote_monitoring_run(void);
```

テキスト エディターで、**main.c** ファイルを開きます。次のコードを追加します。

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## クライアント アプリケーションをビルドする CMake を使用する
次の手順では、*CMake* を使用してクライアント アプリケーションをビルドする方法について説明します。

1. テキスト エディターで、**remote\_monitoring** フォルダーの **CMakeLists.txt** ファイルを開きます。
2. 次の手順を追加して、クライアント アプリケーションをビルドする方法を定義します。
   
    ```
    cmake_minimum_required(VERSION 2.8.11)
   
    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot")
   
    include_directories(${AZUREIOT_INC_FOLDER})
   
    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )
   
    set(sample_application_h_files
        ./remote_monitoring.h
    )
   
    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})
   
    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
    )
    ```
3. **remote\_monitoring** フォルダーに、CMake によって生成される *make* ファイルを格納するフォルダーを作成し、次に示すように **cmake** コマンドと **make** コマンドを実行します。
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```
4. クライアント アプリケーションを実行し、テレメトリを IoT Hub に送信します。
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

<!---HONumber=AcomDC_0720_2016-->