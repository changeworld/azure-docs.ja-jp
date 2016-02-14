<properties
	pageTitle="リソース マネージャー テンプレートを使用した IoT Hub の作成 | Microsoft Azure"
	description="このチュートリアルに従って、リソース マネージャー テンプレートを使用した IoT Hub の作成を開始できます。"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# チュートリアル: C# プログラムを使って IoT Hub を作成する

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## はじめに

Azure リソース マネージャーを使って、Azure IoT ハブをプログラムを使用して作成、管理できます。このチュートリアルでは、リソース マネージャー テンプレートを使用して C# プログラムから IoT Hub を作成する方法を説明します。

> [AZURE.NOTE] Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[リソース マネージャー デプロイメント モデルとクラシック デプロイメント モデル](../resource-manager-deployment-model.md)です。この記事では、リソース マネージャーのデプロイメント モデルの使用について説明します。

このチュートリアルを完了するには、以下が必要になります。

- Microsoft Visual Studio 2015
- アクティブな Azure アカウント<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 以降

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Visual Studio プロジェクトの準備

1. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# Windows のプロジェクトを作成します。プロジェクトに **CreateIoTHub** という名前を付けます。

2. ソリューション エクスプローラーで、プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. NuGet パッケージ マネージャーで、**Microsoft.Azure.Management.Resources** を検索します。バージョン **2.18.11-preview** を選択します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

4. NuGet パッケージ マネージャーで、**Microsoft.IdentityModel.Clients.ActiveDirectory** を検索します。バージョン **2.19.208020213** を選択します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

5. NuGet パッケージ マネージャーで、**Microsoft.Azure.Common** を検索します。バージョン **2.1.0** を選択します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

6. Program.cs で、既存の **using** ステートメントを以下に置き換えます。

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. Program.cs で、次の静的変数を追加して、プレースホルダーの値を置き換えます。このチュートリアルの前の手順で **ApplicationId**、**SubscriptionId**、**TenantId**、および**パスワード**を書き留めています。**リソース グループ名**は、IoT Hub を作成するときに使用するリソース グループの名前で、既存のリソース グループまたは新しいリソース グループのいずれかになります。**IoT Hub 名**は、作成する IoT Hub の名前です (**MyIoTHub** など)。**デプロイメント名**は、デプロイメントの名前です (**Deployment\_01** など)。

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## テンプレートを送信して IoT Hub を作成する

JSON テンプレートを使用して、リソース グループに新しい IoT Hub を作成します。また、テンプレートを使用して、既存の IoT Hub に変更を加えることもできます。

1. ソリューション エクスプローラーで、プロジェクトを右クリックして、**[追加]**、**[新しい項目]** の順にクリックします。**template.json** という新しい JSON ファイルをプロジェクトに追加します。

2. ソリューション エクスプローラーで **template.json** を選択し、**[プロパティ]** で **[出力ディレクトリにコピー]** を **[常にコピーする]** に設定します。

3. **template.json** の内容を次のリソース定義に置き換えて、新しい Standard IoT Hub を **[米国東部]** リージョンに追加します。

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
    ]
    }
    ```

4. 次のメソッドを Program.cs に追加します。
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. 次のコードを **CreateIoTHub** メソッドに追加してをテンプレート ファイルをロードし、IoT Hub の名前を追加して、Azure リソース マネージャーにテンプレートを送信します。

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. 次のコードを **CreateIoTHub** メソッドの末尾に追加して、デプロイメントの正常な完了を待機します。

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
      return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## アプリケーションの完了と実行

アプリケーションを完了するには、これをビルドおよび実行する前に、**CreateIoTHub** および **ShowIoTHubKeys** メソッドを呼び出します。

1. 次のコードを **Main** メソッドの末尾に追加します。

    ```
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. **[ビルド]**、**[ソリューションのビルド]** の順にクリックします。すべてのエラーを修正します。

3. **[デバッグ]**、**[デバッグの開始]** の順にクリックし、アプリケーションを実行します。デプロイメントが実行されるまでに数分かかる場合があります。

4. アプリケーションで新しい IoT Hub が追加されたことを確認するには、[ポータル][lnk-azure-portal]にアクセスし、リソースの一覧を表示するか、**Get-AzureRmResource** PowerShell コマンドレットを使用します。

> [AZURE.NOTE] このサンプル アプリケーションでは、課金の対象とする S1 Standard IoT Hub を追加します。IoT Hub を削除するには、[ポータル][lnk-azure-portal]を使うか、終了時に **Remove-AzureRmResource** PowerShell コマンドレットを使用します。

## 次のステップ

- [IoT Hub Resource Provider REST API][lnk-rest-api] の機能の詳細をご確認ください。
- Azure リソース マネージャーの機能の詳細については、「[Azure リソース マネージャーの概要][lnk-azure-rm-overview]」を参照してください。

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/ja-JP/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_0204_2016-->