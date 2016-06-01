<properties
   pageTitle="mbed で C を使用してデバイスを接続する |Microsoft Azure"
   description="C で記述され、mbed デバイスで実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
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
   ms.date="04/26/2016"
   ms.author="dobett"/>


# デバイスをリモート監視構成済みソリューションに接続する (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## mbed で C のサンプル ソリューションをビルドして実行する

次に、[mbed 対応の Freescale FRDM-K64F][lnk-mbed-home] デバイスをリモート監視ソリューションに接続する手順を説明します。

### デバイスをネットワークおよびデスクトップ コンピューターに接続する

1. イーサネット ケーブルを使用して mbed デバイスをネットワークに接続する方法を説明します。サンプル アプリケーションでは、インターネットへのアクセスが必要なため、この手順は必須です。

2. mbed デバイスをデスクトップ PC に接続する方法の詳細については、「[mbed の概要][lnk-mbed-getstarted]」を参照してください。

3. デスクトップ PC で Windows を実行している場合は、「[PC の構成][lnk-mbed-pcconnect]」を参照して、mbed デバイスへのシリアル ポート アクセスを構成します。

### mbed プロジェクトを作成してサンプル コードをインポートする

1. Web ブラウザーで、mbed.org の[開発者向けサイト](https://developer.mbed.org/)に移動します。サインアップしていない場合は、新しいアカウントを作成するオプションが表示されます (アカウントの作成は無料です)。既にサインアップしている場合は、アカウントの資格情報を使用してログインします。次に、ページの右上隅の **[Compiler]** をクリックします。これにより、ワークスペース管理インターフェイスが表示されます。

2. 使用しているハードウェア プラットフォームがウィンドウの右上隅に表示されていることを確認するか、右上隅にあるアイコンをクリックしてハードウェア プラットフォームを選択します。

3. メイン メニューの **[インポート]** をクリックします。次に、mbed の地球ロゴの横にある **[ここをクリック]** をクリックし、URL リンクからインポートします。

    ![][6]

4. ポップアップ ウィンドウで、サンプル コード用のリンク https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ を入力し、**[インポート]** をクリックします。

    ![][7]

5. mbed コンパイラのウィンドウでは、このプロジェクトをインポートしたことでさまざまなライブラリがインポートされたことを確認できます。ライブラリには、Azure IoT チームが提供および管理するライブラリ ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[azure\_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)) もあれば、mbed ライブラリ カタログで入手可能なサード パーティのライブラリもあります。

    ![][8]

6. remote\_monitoring\\remote\_monitoring.c ファイルを開き、ファイル内で次のコードを見つけます。

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. サンプル プログラムが IoT Hub に接続できるように、[Device Id] と [Device Key] をデバイスのデータに置き換えます。IoT Hub ホスト名を使用して、プレースホルダーの [IoTHub Name] と [IoTHub Suffix, i.e. azure-devices.net] を置き換えます。たとえば、IoT Hub ホスト名が contoso.azure-devices.net である場合は、contoso が **hubName**、残りの部分が **hubSuffix** になります。

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### コードのウォーク スルー

プログラムのしくみに関心があるユーザー向けに、このセクションでは、サンプル コードの重要な部分について説明します。コードを実行するだけである場合は、「[プログラムをビルドして実行する](#buildandrun)」に進んでください。

#### モデルの定義

このサンプルでは、[serializer][lnk-serializer] ライブラリを使用して、デバイスが IoT Hub との間で送受信できるメッセージを指定するモデルを定義します。このサンプルでは、**Contoso** 名前空間で、**Temperature**、**ExternalTemperature**、**Humidity** の各テレメトリ データと、デバイス ID、デバイスのプロパティ、デバイスが応答するコマンドなどのメタデータを指定する**サーモスタット** モデルを定義します。

```
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

モデル定義に関連するのは、デバイスが応答する **SetTemperature** コマンドと **SetHumidity** コマンドの定義です。

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

#### ライブラリへのモデルの接続

**sendMessage** 関数と **IoTHubMessage** 関数は、デバイスからテレメトリを送信し、IoT Hub からのメッセージをコマンド ハンドラーに接続する定型コードです。

#### remote\_monitoring\_run 関数

プログラムの **main** 関数は、アプリケーションの起動時に **remote\_monitoring\_run** 関数を呼び出して、IoT Hub デバイス クライアントとしてデバイスの動作を実行します。この **remote\_monitoring\_run** 関数は、大半が関数の入れ子になったペアで構成されています。

- **platform\_init** と **platform\_deinit** は、プラットフォーム固有の初期化およびシャットダウン操作を実行します。
- **serializer\_init** と **serializer\_deinit** は、serializer ライブラリの初期化と初期化解除を実行します。
- **IoTHubClient\_Create** と **IoTHubClient\_Destroy** は、デバイスの資格情報を使用して IoT Hub に接続するクライアント ハンドル **iotHubClientHandle** を作成します。

**remote\_monitoring\_run** 関数のメイン セクションでは、プログラムは **iotHubClientHandle** ハンドルを使用して次の操作を実行します。

- Contoso サーモスタット モデルのインスタンスを作成し、2 つのコマンドのメッセージのコールバックを設定します。
- serializer ライブラリを使用して、デバイス自体に関する情報 (デバイスがサポートするコマンドなど) を IoT Hub に送信します。ハブはこのメッセージを受信すると、ダッシュボードのデバイスの状態を**保留中**から**実行中**に変更します。
- 温度、外部温度、湿度の値を 1 秒おきに IoT Hub に送信する **while** ループを開始します。

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

<a id="buildandrun"/>
### プログラムをビルドして実行する

1. **[コンパイル]** をクリックしてプログラムをビルドします。警告は無視してかまいません。ただし、ビルドでエラーが発生する場合は、続行する前にそのエラーを修正してください。

2. ビルドが成功した場合、mbed コンパイラの Web サイトによって、プロジェクト名の付いた .bin ファイルが生成され、ローカル コンピューターにダウンロードされます。.bin ファイルをデバイスにコピーします。.bin ファイルをデバイスに保存すると、デバイスが再起動し、.bin ファイルに含まれるプログラムが実行されます。mbed デバイスのリセット ボタンを押すことで、いつでもプログラムを手動で再起動することができます。

3. PuTTY などの SSH クライアント アプリケーションを使用して、デバイスに接続します。Windows デバイス マネージャーを確認すると、デバイスで使用されているシリアル ポートを特定できます。

    ![][11]

4. PuTTY で、接続タイプとして **[シリアル]** をクリックします。通常、デバイスは 9600 ボーで接続するため、**[速度]** ボックスに「9600」と入力します。次に、**[開く]** をクリックします。

5. プログラムの実行が開始されます。接続時にプログラムが自動的に開始されない場合は、ボードのリセットが必要になることがあります (Ctrl キーを押しながら Break キーを押すか、ボードのリセット ボタンを押します)。

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer

<!---HONumber=AcomDC_0518_2016-->