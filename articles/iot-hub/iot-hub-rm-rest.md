---
title: REST API を使用して IoT Hub を作成する | Microsoft Docs
description: このチュートリアルに従って、REST API を使用した IoT Hub の作成を開始できます。
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: dobett

---
# チュートリアル: C# プログラムと REST API を使って IoT Hub を作成する
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## はじめに
[IoT Hub Resource Provider REST API][lnk-rest-api] を使って、Azure IoT Hub をプログラムを使用して作成、管理できます。このチュートリアルでは、Resource Provider REST API を使用して C# プログラムから IoT Hub を作成する方法を説明します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[リソース マネージャー デプロイメント モデルとクラシック デプロイメント モデル](../resource-manager-deployment-model.md)です。この記事では、リソース マネージャーのデプロイメント モデルの使用について説明します。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Microsoft Visual Studio 2015
* アクティブな Azure アカウント。<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 以降

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Visual Studio プロジェクトの準備
1. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# Windows のプロジェクトを作成します。プロジェクトに **CreateIoTHubREST** という名前を付けます。
2. ソリューション エクスプローラーで、プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. NuGet パッケージ マネージャーで、**[プレリリースを含める]** をオンにし、**Microsoft.Azure.Management.ResourceManager** を検索します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。
4. NuGet パッケージ マネージャーで、**Microsoft.IdentityModel.Clients.ActiveDirectory** を検索します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。
5. Program.cs で、既存の **using** ステートメントを以下に置き換えます。
   
    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
6. Program.cs で、次の静的変数を追加して、プレースホルダーの値を置き換えます。このチュートリアルの前の手順で **ApplicationId**、**SubscriptionId**、**TenantId**、および**パスワード**を書き留めています。**リソース グループ名**は、IoT Hub を作成するときに使用するリソース グループの名前で、既存のリソース グループまたは新しいリソース グループのいずれかになります。**IoT Hub 名**は、**MyIoTHub** など、作成する IoT Hub の名前です (この名前はグローバルに一意でなければならないため、自分の名前やイニシャルを含めます)。**デプロイ名**は、デプロイの名前です (**Deployment\_01** など)。
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
   
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## REST API を使用して IoT Hub を作成する
[IoT Hub REST API][lnk-rest-api] を使用して、リソース グループで IoT Hub を作成します。また、REST API を使用して、既存の IoT Hub に変更を加えることもできます。

1. 次のメソッドを Program.cs に追加します。
   
    ```
    static void CreateIoTHub(string token)
    {
   
    }
    ```
2. 以下に示したのは、**HttpClient** オブジェクトとそのヘッダー内の認証トークンを作成するコードです。このコードを **CreateIoTHub** メソッドに追加してください。
   
    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```
3. 次のコードを **CreateIoTHub** メソッドに追加して IoT Hub を記述し、JSON 表記を生成します (現在の IoT Hub をサポートする場所の一覧については、[Azure の状態][lnk-status] を参照してください)。
   
    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
   
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```
4. **CreateIoTHub** メソッドに次のコードを追加します。これは、REST 要求を Azure に送信し、応答を確認して、デプロイ タスクの状態を監視するための URL を取得するコードです。
   
    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
   
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
   
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```
5. 直前のステップで取得した **asyncStatusUri** アドレスを使用してデプロイの完了を待機するコードを **CreateIoTHub** メソッドの最後に追加します。
   
    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{"status":"Running"}");
    ```
6. 次のコードを **CreateIoTHub** メソッドの最後に追加します。作成した IoT Hub のキーを取得し、それをコンソールに出力するものです。
   
    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
   
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## アプリケーションの完了と実行
**CreateIoTHub** メソッドの呼び出しを追加したうえでアプリケーションをビルドし、実行すれば、アプリケーションは完成です。

1. 次のコードを **Main** メソッドの末尾に追加します。
   
    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
2. **[ビルド]**、**[ソリューションのビルド]** の順にクリックします。すべてのエラーを修正します。
3. **[デバッグ]**、**[デバッグの開始]** の順にクリックし、アプリケーションを実行します。デプロイメントが実行されるまでに数分かかる場合があります。
4. アプリケーションで新しい IoT Hub が追加されたことを確認するには、[ポータル][lnk-azure-portal]にアクセスし、リソースの一覧を表示するか、**Get-AzureRmResource** PowerShell コマンドレットを使用します。

> [!NOTE]
> このサンプル アプリケーションでは、課金の対象とする S1 Standard IoT Hub を追加します。完了したら、IoT Hub を削除するには、[ポータル][lnk-azure-portal]を使うか、終了時に **Remove-AzureRmResource** PowerShell コマンドレットを使用します。
> 
> 

## 次のステップ
ここでは、REST API を使用して IoT Hub をデプロイしました。次の手順に進んでください。

* [IoT Hub Resource Provider REST API][lnk-rest-api] の機能の詳細をご確認ください。
* Azure リソース マネージャーの機能の詳細については、「[Azure リソース マネージャーの概要][lnk-azure-rm-overview]」を参照してください。

IoT Hub の開発に関する詳細については、以下を参照してください

* [C SDK の概要][lnk-c-sdk]
* [IoT Hub SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [ソリューションの設計][lnk-design]
* [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
* [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
* [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->