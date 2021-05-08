---
title: チュートリアル - Azure IoT Hub に対してデバイスを認証する X.509 証明書の機能をテストする | Microsoft Docs
description: チュートリアル - Azure IoT Hub に対する X.509 証明書の認証をテストする
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379436"
---
# <a name="tutorial-testing-certificate-authentication"></a>チュートリアル: 証明書の認証をテストする

以下の C# コード例を使用すると、証明書が IoT ハブに対してデバイスを認証できることをテストできます。 テスト コードを実行する前に、次の作業を行う必要があることに注意してください。

* ルート CA または下位 CA 証明書を作成する。
* CA 証明書を IoT ハブにアップロードする。
* CA 証明書の所有証明を行う。
* IoT ハブにデバイスを追加する。
* 自分のデバイスと同じデバイス ID でデバイス証明書を作成する。

## <a name="code-example"></a>コード例

次のコード例は、IoT ハブに登録された X.509 デバイスをシミュレートする C# アプリケーションの作成方法を示しています。 この例では、シミュレートされたデバイスからハブに温度と湿度の値を送信します。 このチュートリアルではデバイス アプリケーションのみを作成します。 このシミュレートされたデバイスから送信されたイベントに応答を送信する IoT Hub サービス アプリケーションを作成する作業は、読者のための演習として残されています。

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択して、 **[コンソール アプリ (.NET Framework)]** プロジェクト テンプレートを選択します。 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに *SimulateX509Device* という名前を付け、 **[作成]** を選択します。

   ![Visual Studio で X.509 デバイス プロジェクトを作成する](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. ソリューション エクスプローラーで **SimulateX509Device** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージ マネージャー]** で **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を検索して選択します。 **[インストール]** を選択します。

   ![Visual Studio でデバイス SDK NuGet パッケージを追加する](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    この手順により、Azure IoT device SDK NuGet パッケージのダウンロードとインストールが実行され、それとその依存関係への参照が追加されます。

    次のコードを入力して実行します。

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```