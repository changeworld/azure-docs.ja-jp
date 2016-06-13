<properties
   pageTitle="Windows で C を使用してデバイスを接続する |Microsoft Azure"
   description="C で記述され、Windows で実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート管理ソリューションに接続する方法について説明します。"
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="dobett"/>


# デバイスをリモート監視構成済みソリューションに接続する (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Windows で C のサンプル ソリューションを作成する

次の手順では、Visual Studio で C プログラムを使用して、リモート監視が事前構成されたソリューションと通信する単純なクライアント アプリケーションを作成する方法を示します。

Visual Studio 2015 にスタート プロジェクトを作成し、IoT Hub デバイス クライアントの NuGet パッケージを追加します。

1. Visual Studio 2015 で、Visual C++ **Win32 コンソール アプリケーション** テンプレートを使用して、新しい C コンソール アプリケーションを作成します。プロジェクトの名前として「**RMDevice**」と入力します。

2. **Win32 アプリケーション ウィザード**の **[アプリケーション設定]** ページで、**[コンソール アプリケーション]** が選択されていることを確認し、**[プリコンパイル済みヘッダー]** と **[Security Development Lifecycle (SDL) checks (Security Development Lifecycle (SDL) チェック)]** をオフにします。

3. **ソリューション エクスプローラー**で、stdafx.h、targetver.h、stdafx.cpp の各ファイルを削除します。

4. **ソリューション エクスプローラー**で、RMDevice.cpp ファイルの名前を RMDevice.c に変更します。

5. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。**[参照]** をクリックし、次の NuGet パッケージを検索してプロジェクトにインストールします。

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

## シンプルな IoT Hub デバイスの動作を指定するコードを追加する

IoT Hub クライアント ライブラリでは、モデルを使用して、デバイスが IoT Hub に送信するメッセージの形式と、デバイスが応答する IoT Hub からのコマンドを指定します。

1. Visual Studio で RMDevice.c ファイルを開きます。既存の `#include` ステートメントを次のように置き換えます。

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    ```

2. `#include` ステートメントの後に次の変数宣言を追加します。リモート監視ソリューション ダッシュボードから、プレースホルダ [Device Id] と [Device Key] の値をデバイス用の値に置き換えます。ダッシュボードの IoT Hub ホスト名を使用して、[IoTHub Name] を置き換えます。たとえば、IoT Hub ホスト名が **contoso.azure-devices.net** である場合は、[IoTHub Name] を contoso に置き換えます。

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
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

## デバイスの動作を実装するコードを追加する

ここで、モデルに定義された動作を実装するコードを追加する必要があります。デバイスがハブからコマンドを受信したときに実行する関数と、シミュレートされたテレメトリをハブに送信するためのコードを追加します。

1. デバイスがモデルに定義された **SetTemperature** コマンドと **SetHumidity** コマンドを受信したときに実行する関数を追加します。

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
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
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
    }
    ```
    
    起動時に IoT Hub に送信される **DeviceInfo** メッセージの例:
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
    
    IoT Hub に送信される**テレメトリ** メッセージの例:
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
    
    IoT Hub から受信する**コマンド**の例:
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

5. **main** 関数を、**remote\_monitoring\_run** 関数を呼び出す次のコードに置き換えます。

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. **[ビルド]**、**[ソリューションのビルド]** の順にクリックして、デバイス アプリケーションをビルドします。

7. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックして、サンプルをビルドして実行します。アプリケーションがサンプル テレメトリを IoT Hub に 送信すると、コンソールにメッセージが表示されます。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0601_2016-->