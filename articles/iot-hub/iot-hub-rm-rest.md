---
title: "リソース プロバイダー REST API を使用して IoT ハブを作成する | Microsoft Docs"
description: "このチュートリアルに従って、リソース プロバイダー REST API を使用した IoT ハブの作成を開始できます。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 52814ee5-bc10-4abe-9eb2-f8973096c2d8
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 44d73863ebe968a025654f501a820bdf0f9b3c49


---
# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-resource-provider-rest-api"></a>チュートリアル: C# プログラムとリソース プロバイダー REST API を使って IoT ハブを作成する
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに
[IoT Hub リソース プロバイダー REST API][lnk-rest-api] を使って、Azure IoT ハブをプログラムを使用して作成、管理できます。 このチュートリアルでは、IoT Hub Resource Provider REST API を使用して C# プログラムから IoT Hub を作成する方法を説明します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。  この記事では、Azure Resource Manager デプロイメント モデルの使用について説明します。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Microsoft Visual Studio 2015
* アクティブな Azure アカウント。 <br/>アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます。
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 以降。

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Visual Studio プロジェクトの準備
1. Visual Studio で、 **コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# Windows のプロジェクトを作成します。 プロジェクトに **CreateIoTHubREST**という名前を付けます。
2. ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]**をクリックします。
3. NuGet パッケージ マネージャーで、**[プレリリースを含める]** をオンにし、**Microsoft.Azure.Management.ResourceManager** を検索します。 **[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。
4. NuGet パッケージ マネージャーで、**Microsoft.IdentityModel.Clients.ActiveDirectory** を検索します。  **[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。
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
6. Program.cs で、次の静的変数を追加して、プレースホルダーの値を置き換えます。 このチュートリアルの前の手順で **ApplicationId**、**SubscriptionId**、**TenantId**、および**パスワード**を書き留めています。 **リソース グループ名** は、IoT Hub を作成するときに使用するリソース グループの名前で、既存のリソース グループまたは新しいリソース グループのいずれかになります。 **IoT Hub 名**は、作成する IoT Hub の名前です (**MyIoTHub** など。この名前はグローバルに一意でなければならないため、自分の名前やイニシャルを含めます)。 **デプロイ名**は、デプロイの名前です (**Deployment_01** など)。
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
   
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>リソース プロバイダー REST API を使用して IoT ハブを作成する
[IoT Hub リソース プロバイダー REST API][lnk-rest-api] を使用して、リソース グループで新しい IoT ハブを作成します。 また、リソース プロバイダー REST API を使用して、既存の IoT ハブに変更を加えることもできます。

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
3. 次のコードを **CreateIoTHub** メソッドに追加して IoT ハブを記述し、JSON 表記を生成します。 IoT Hub をサポートする場所の最新のリストについては、「[Azure の状態][lnk-status]」を参照してください。
   
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
    } while (body == "{\"status\":\"Running\"}");
    ```
6. 次のコードを **CreateIoTHub** メソッドの最後に追加します。作成した IoT Hub のキーを取得し、それをコンソールに出力するものです。
   
    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
   
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>アプリケーションの完了と実行
**CreateIoTHub** メソッドの呼び出しを追加したうえでアプリケーションをビルドし、実行すれば、アプリケーションは完成です。

1. 次のコードを **Main** メソッドの末尾に追加します。
   
    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
2. **[ビルド]**、**[ソリューションのビルド]** の順にクリックします。 すべてのエラーを修正します。
3. **[デバッグ]**、**[デバッグの開始]** の順にクリックし、アプリケーションを実行します。 デプロイメントが実行されるまでに数分かかる場合があります。
4. アプリケーションで新しい IoT ハブが追加されたことを確認するには、[Azure Portal][lnk-azure-portal] にアクセスし、リソースの一覧を表示するか、**Get-AzureRmResource** PowerShell コマンドレットを使用します。

> [!NOTE]
> このサンプル アプリケーションでは、課金の対象とする S1 Standard IoT Hub を追加します。 完了したら、IoT ハブを削除するには、[Azure Portal][lnk-azure-portal] を使うか、終了時に **Remove-AzureRmResource** PowerShell コマンドレットを使用します。
> 
> 

## <a name="next-steps"></a>次のステップ
ここでは、リソース プロバイダー REST API を使用して IoT ハブをデプロイしました。次の手順に進んでください。

* [IoT Hub リソース プロバイダー REST API][lnk-rest-api] の機能の詳細をご確認ください。
* Azure Resource Manager の機能の詳細については、「[Azure Resource Manager の概要][lnk-azure-rm-overview]」を参照してください。

IoT Hub の開発に関する詳細については、以下を参照してください

* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


