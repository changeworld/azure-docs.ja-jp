## <a name="specify-the-behavior-of-the-iot-device"></a>IoT デバイスの動作を指定する

IoT Hub シリアライザー クライアント ライブラリでは、モデルを使用して、デバイスが IoT Hub とやり取りするメッセージの形式を指定します。

<!-- TO DO This needs to be verified when we can access the UI -->

1. `#include` ステートメントの後に次の変数宣言を追加します。 リモート監視ソリューション ダッシュボードで、プレースホルダー `[Device Id]` と `[Device Key]` の値を、書き留めておいたデバイス用の値に置き換えます。 ソリューション ダッシュボードの IoT Hub ホスト名を使用して、`[IoTHub Name]` を置き換えます。 たとえば、IoT Hub ホスト名が **contoso.azure-devices.net** である場合は、[IoTHub Name] を **contoso** に置き換えます。

    ```c
    static const char* deviceId = "[Device Id]";
    static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
    static char propText[1024];
    ```

1. 次のコードを追加して、デバイスと IoT Hub との通信を可能にするモデルを定義します。 このモデルでは、デバイスで次の操作が可能であることを指定します。

    - 温度、気圧、外部温度、および湿度をテレメトリとして送信する。
    - 報告されたプロパティを IoT Hub でデバイス ツインに送信する。 報告されたプロパティには、テレメトリ スキーマおよびサポートされているメソッドの情報が含まれます。
    - IoT Hub のデバイス ツインで設定された必要なプロパティを受信し、そのプロパティを操作する。
    - UI から呼び出された **Reboot**、**FirmwareUpdate**、**EmergencyValveRelease**、および **IncreasePressure** ダイレクト メソッドに応答する。 デバイスは、報告されたプロパティを使用して、サポートするダイレクト メソッドに関する情報を送信します。

    ```c
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    DECLARE_STRUCT(MessageSchema,
    ascii_char_ptr, Name,
    ascii_char_ptr, Format,
    ascii_char_ptr_no_quotes, Fields
    )

    DECLARE_STRUCT(TelemetrySchema,
    ascii_char_ptr, Interval,
    ascii_char_ptr, MessageTemplate,
    MessageSchema, MessageSchema
    )

    DECLARE_STRUCT(TelemetryProperties,
    TelemetrySchema, TemperatureSchema,
    TelemetrySchema, HumiditySchema,
    TelemetrySchema, PressureSchema
    )

    DECLARE_DEVICETWIN_MODEL(Chiller,
    /* Telemetry (temperature, external temperature and humidity) */
    WITH_DATA(double, temperature),
    WITH_DATA(ascii_char_ptr, temperature_unit),
    WITH_DATA(double, pressure),
    WITH_DATA(ascii_char_ptr, pressure_unit),
    WITH_DATA(double, humidity),
    WITH_DATA(ascii_char_ptr, humidity_unit),

    /* Device twin properties */
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Protocol),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, SupportedMethods),
    WITH_REPORTED_PROPERTY(TelemetryProperties, Telemetry),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Type),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Firmware),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, FirmwareUpdateStatus),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Location),
    WITH_REPORTED_PROPERTY(double, Latitiude),
    WITH_REPORTED_PROPERTY(double, Longitude),

    WITH_DESIRED_PROPERTY(ascii_char_ptr, Interval, onDesiredInterval),

    /* Direct methods implemented by the device */
    WITH_METHOD(Reboot),
    WITH_METHOD(FirmwareUpdate),
    WITH_METHOD(EmergencyValveRelease),
    WITH_METHOD(IncreasePressure)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>デバイスの動作を実装する

ここで、モデルに定義された動作を実装するコードを追加します。

1. 次の関数を追加します。この関数は、ソリューション ダッシュボードで設定された必要なプロパティを処理します。 この必要なプロパティはモデルで定義されています。

    ```c
    void onDesiredInterval(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Chiller* chiller = argument;
      printf("Received a new desired Interval value: %s \r\n", chiller->Interval);
    }
    ```

1. 次の関数を追加します。この関数は、IoT Hub で呼び出されたダイレクト メソッドを処理します。 このダイレクト メソッドはモデルで定義されています。

    ```c
    /* Handlers for direct methods */
    METHODRETURN_HANDLE Reboot(Chiller* chiller)
    {
      (void)(chiller);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Rebooting\"");
      printf("Received reboot request\r\n");
      return result;
    }

    METHODRETURN_HANDLE FirmwareUpdate(Chiller* chiller)
    {
      (void)(chiller);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Updating Firmware\"");
      printf("Recieved firmware update request\r\n");
      return result;
    }

    METHODRETURN_HANDLE EmergencyValveRelease(Chiller* chiller)
    {
      (void)(chiller);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Releasing Emergency Valve\"");
      printf("Recieved emergency valve release request\r\n");
      return result;
    }

    METHODRETURN_HANDLE IncreasePressure(Chiller* chiller)
    {
      (void)(chiller);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Increasing Pressure\"");
      printf("Received increase pressure request\r\n");
      return result;
    }
    ```

1. 次の関数を追加します。この関数は、プロパティを、デバイスからクラウドへのメッセージに追加します。

    ```c
    /* Add message property */
    static void addProperty(MAP_HANDLE propMap, char* propName, char* propValue)
    {
      if (Map_AddOrUpdate(propMap, propName, propValue) != MAP_OK)
      {
        (void)printf("ERROR: Map_AddOrUpdate Failed on %s!\r\n", propName);
      }
    }
    ```

1. 次の関数を追加します。この関数は、メッセージとプロパティを、構成済みソリューションに送信します。

    ```c
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size, char* schema)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        // Add properties
        MAP_HANDLE propMap = IoTHubMessage_Properties(messageHandle);
        addProperty(propMap, "$$MessageSchema", schema);
        addProperty(propMap, "$$ContentType", "JSON");
        time_t now = time(0);
        struct tm* timeinfo;
        #pragma warning(disable: 4996)
        timeinfo = gmtime(&now);
        char timebuff[50];
        strftime(timebuff, 50, "%Y-%m-%dT%H:%M:%SZ", timeinfo);
        addProperty(propMap, "$$CreationTimeUtc", timebuff);

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

1. 次のコールバック ハンドラーを追加します。このハンドラーは、新しく報告されたプロパティ値を、デバイスが構成済みソリューションに送信するときに実行されます。

    ```c
    /* Callback after sending reported properties */
    void deviceTwinCallback(int status_code, void* userContextCallback)
    {
      (void)(userContextCallback);
      printf("IoTHub: reported properties delivered with status_code = %u\n", status_code);
    }
    ```

1. 次の関数を追加して、クラウドで構成済みソリューションにデバイスを接続し、データを交換します。 この関数では、次の手順を実行します。

    - プラットフォームを初期化する。
    - シリアル化ライブラリに Contoso の名前空間を登録する。
    - デバイスの接続文字列でクライアントを初期化する。
    - **Chiller** モデルのインスタンスを作成する。
    - 報告されたプロパティ値を作成して送信する。
    - 5 秒ごとにテレメトリを送信するループを作成する。
    - すべてのリソースの初期化を解除する。

    ```c
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\n");
      }
      else
      {
        if (SERIALIZER_REGISTER_NAMESPACE(Contoso) == NULL)
        {
          printf("Unable to SERIALIZER_REGISTER_NAMESPACE\n");
        }
        else
        {
          IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, MQTT_Protocol);
          if (iotHubClientHandle == NULL)
          {
            printf("Failure in IoTHubClient_CreateFromConnectionString\n");
          }
          else
          {
            Chiller* chiller = IoTHubDeviceTwin_CreateChiller(iotHubClientHandle);
            if (chiller == NULL)
            {
              printf("Failure in IoTHubDeviceTwin_CreateChiller\n");
            }
            else
            {
              /* Set values for reported properties */
              chiller->Protocol = "MQTT";
              chiller->SupportedMethods = "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure";
              chiller->Telemetry.TemperatureSchema.Interval = "00:00:05";
              chiller->Telemetry.TemperatureSchema.MessageTemplate = "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}";
              chiller->Telemetry.TemperatureSchema.MessageSchema.Name = "chiller-temperature;v1";
              chiller->Telemetry.TemperatureSchema.MessageSchema.Format = "JSON";
              chiller->Telemetry.TemperatureSchema.MessageSchema.Fields = "{\"temperature\":\"Double\",\"temperature_unit\":\"Text\"}";
              chiller->Telemetry.HumiditySchema.Interval = "00:00:05";
              chiller->Telemetry.HumiditySchema.MessageTemplate = "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}";
              chiller->Telemetry.HumiditySchema.MessageSchema.Name = "chiller-humidity;v1";
              chiller->Telemetry.HumiditySchema.MessageSchema.Format = "JSON";
              chiller->Telemetry.HumiditySchema.MessageSchema.Fields = "{\"humidity\":\"Double\",\"humidity_unit\":\"Text\"}";
              chiller->Telemetry.PressureSchema.Interval = "00:00:05";
              chiller->Telemetry.PressureSchema.MessageTemplate = "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}";
              chiller->Telemetry.PressureSchema.MessageSchema.Name = "chiller-pressure;v1";
              chiller->Telemetry.PressureSchema.MessageSchema.Format = "JSON";
              chiller->Telemetry.PressureSchema.MessageSchema.Fields = "{\"pressure\":\"Double\",\"pressure_unit\":\"Text\"}";
              chiller->Type = "Chiller";
              chiller->Firmware = "1.0.0";
              chiller->FirmwareUpdateStatus = "";
              chiller->Location = "Building 44";
              chiller->Latitiude = 47.638928;
              chiller->Longitude = -122.13476;

              /* Send reported properties to IoT Hub */
              if (IoTHubDeviceTwin_SendReportedStateChiller(chiller, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
              {
                printf("Failed sending serialized reported state\n");
              }
              else
              {
                /* Send telemetry */
                chiller->temperature = 50;
                chiller->temperature_unit = "F";
                chiller->pressure= 55;
                chiller->pressure_unit = "psig";
                chiller->humidity = 50;
                chiller->humidity_unit = "%";

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %f %s,\n", chiller->temperature, chiller->temperature_unit);

                  if (SERIALIZE(&buffer, &bufferSize, chiller->temperature, chiller->temperature_unit) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize, chiller->Telemetry.TemperatureSchema.MessageSchema.Name);
                  }

                  (void)printf("Sending sensor value Humidity = %f %s,\n", chiller->humidity, chiller->humidity_unit);

                  if (SERIALIZE(&buffer, &bufferSize, chiller->humidity, chiller->humidity_unit) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize, chiller->Telemetry.HumiditySchema.MessageSchema.Name);
                  }

                  (void)printf("Sending sensor value Pressure = %f %s,\n", chiller->pressure, chiller->pressure_unit);

                  if (SERIALIZE(&buffer, &bufferSize, chiller->pressure, chiller->pressure_unit) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize, chiller->Telemetry.PressureSchema.MessageSchema.Name);
                  }

                  ThreadAPI_Sleep(5000);
                }

                IoTHubDeviceTwin_DestroyChiller(chiller);
              }
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
      }
      platform_deinit();
    }
    ```

    構成済みソリューションに送信される**テレメトリ** メッセージの例を次に示します。

    ```
    Device: [myCDevice],
    Data:[{"humidity":50.000000000000000, "humidity_unit":"%"}]
    Properties:
    '$$MessageSchema': 'chiller-humidity;v1'
    '$$ContentType': 'JSON'
    '$$CreationTimeUtc': '2017-09-12T09:17:13Z'
    ```