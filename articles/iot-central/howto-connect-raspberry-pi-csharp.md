---
title: Raspberry Pi を Azure IoT Central アプリケーションに接続する (C#) | Microsoft Docs
description: デバイス開発者として、C# を使用して Raspberry Pi を Azure IoT Central アプリケーションに接続する方法。
author: dominicbetts
ms.author: dobett
ms.date: 01/22/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 63843797cca7fe84cdb9ce91d2282b1c0c288f0c
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205138"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Raspberry Pi を Azure IoT Central アプリケーションに接続する (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

この記事では、デバイス開発者として、C# プログラミング言語を使用して Raspberry Pi を Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

* 開発コンピューターにインストールされた [.NET Core 2](https://www.microsoft.com/net)。 [Visual Studio Code](https://code.visualstudio.com/) などの適切なコード エディターも用意する必要があります。
* **サンプル Devkit** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、「[Azure IoT Central アプリケーションを作成する](howto-create-application.md)」を参照してください。
* Raspbian オペレーティング システムを実行している Raspberry Pi デバイス。


## <a name="sample-devkits-application"></a>**サンプル Devkit** アプリケーション

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Raspberry Pi** デバイス テンプレートが含まれています。 

- デバイスの**湿度**、**温度**、**圧力**、**磁力計** (X、Y、Z 軸に沿って測定)、**加速度計** (X、Y、Z 軸に沿って測定)、**ジャイロスコープ** (X、Y、Z 軸に沿って測定) の測定値を含むテレメトリ。
- **電圧**、**電流**、**ファン速度**、**IR** のトグルを示す設定。
- デバイス プロパティ**ダイ番号**および**場所**クラウド プロパティを含むプロパティ。


デバイス テンプレートの構成について詳しくは、「[Raspberry PI デバイス テンプレートの詳細](howto-connect-raspberry-pi-csharp.md#raspberry-pi-device-template-details)」をご覧ください。


## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションでは、**Raspberry Pi** デバイス テンプレートから実デバイスを追加し、デバイスの接続文字列を書きとめます。 詳細については、「[Azure IoT Central アプリケーションに実デバイスを追加する](tutorial-add-device.md)」を参照してください。

### <a name="create-your-net-application"></a>.NET アプリケーションを作成する

デスクトップ コンピューター上でデバイス アプリケーションを作成してテストします。

次の手順を完了するには、Visual Studio Code を使用できます。 詳細については、[C# の操作](https://code.visualstudio.com/docs/languages/csharp)に関するページを参照してください。

> [!NOTE]
> 必要に応じて、別のコード エディターを使用して、次の手順を完了できます。

1. .NET プロジェクトを初期化し、必要な NuGet パッケージを追加するには、次のコマンドを実行します。

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Visual Studio Code で `pisample` フォルダーを開きます。 次に、**pisample.csproj** プロジェクト ファイルを開きます。 次のスニペットに示されている `<RuntimeIdentifiers>` タグを追加します。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.5.2" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > **Microsoft.Azure.Devices.Client** パッケージのバージョン番号が、示されているものより大きいことがあります。

1. **pisample.csproj** を保存します。 Visual Studio Code から復元コマンドを実行するよう求められた場合は、**[復元]** を選択します。

1. **Program.cs** を開き、その内容を次のコードに置き換えます。

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > 次の手順で、プレースホルダー `{your device connection string}` を更新します。

## <a name="run-your-net-application"></a>.NET アプリケーションを実行する

デバイスが Azure IoT Central で認証されるためのコードにデバイス固有の接続文字列を追加します。 この接続文字列は、実デバイスを Azure IoT Central アプリケーションに追加したときに書きとめました。

1. **Program.cs** ファイル内の `{your device connection string}` を、前に書きとめた接続文字列に置き換えます。

1. コマンドライン環境で次のコマンドを実行します。

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` フォルダーを Raspberry Pi デバイスにコピーします。 たとえば、**scp** コマンドを使用してファイルをコピーできます。

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    詳細については、[Raspberry Pi のリモート アクセス](https://www.raspberrypi.org/documentation/remote-access/)に関するページを参照してください。

1. Raspberry Pi デバイスにサインインし、シェルで次のコマンドを実行します。

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Raspberry Pi 上で、次のコマンドを実行します。

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![プログラムが開始される](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Azure IoT Central アプリケーションでは、Raspberry Pi 上で実行されているコードがアプリケーションとどのように対話するかを確認できます。

    * 実デバイスの **[Measurements] (測定)** ページで、テレメトリを表示できます。
    * **[プロパティ]** ページで、報告された **[Die Number] (サイコロの数字)** プロパティの値を表示できます。
    * **[設定]** ページで、電圧やファン速度などの Raspberry Pi 上のさまざまな設定を変更できます。

    次のスクリーンショットは、Raspberry Pi が設定の変更を受信するようすを示しています。

    ![Raspberry Pi が設定の変更を受信する](./media/howto-connect-raspberry-pi-csharp/device_switch.png)


## <a name="raspberry-pi-device-template-details"></a>Raspberry PI デバイス テンプレートの詳細

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Raspberry Pi** デバイス テンプレートが含まれています。

### <a name="telemetry-measurements"></a>テレメトリ測定

| フィールド名     | Units  | 最小値 | 最大値 | 小数点以下の桁数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1,000    | 0              |
| magnetometerY  | mgauss | -1000   | 1,000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1,000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>設定

数値設定

| 表示名 | フィールド名 | Units | 小数点以下の桁数 | 最小値 | 最大値 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電圧      | setVoltage | ボルト | 0              | 0       | 240     | 0       |
| Current      | setCurrent | アンペア  | 0              | 0       | 100     | 0       |
| ファン速度    | fanSpeed   | RPM   | 0              | 0       | 1,000    | 0       |

トグル設定

| 表示名 | フィールド名 | オンテキスト | オフテキスト | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | オフ     |

### <a name="properties"></a>Properties

| Type            | 表示名 | フィールド名 | データ型 |
| --------------- | ------------ | ---------- | --------- |
| デバイス プロパティ | サイコロの数字   | dieNumber  | number    |
| Text            | Location     | location   | 該当なし       |

## <a name="next-steps"></a>次の手順

ここでは、Raspberry Pi を Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は以下のとおりです。

* [汎用の Node.js クライアント アプリケーションを Azure IoT Central に接続する](howto-connect-nodejs.md)
