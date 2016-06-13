<properties
	pageTitle="ARM テンプレートと C# を使用した IoT Hub の作成 | Microsoft Azure"
	description="このチュートリアルでは、リソース マネージャー テンプレートを使用して、C# プログラムで IoT Hub の作成を開始する方法について説明します。"
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
     ms.date="05/31/2016"
     ms.author="dobett"/>

# ARM テンプレートと C# プログラムを使用した IoT Hub の作成

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## はじめに

Azure リソース マネージャー (ARM) を使って、Azure IoT Hub をプログラムから作成、管理できます。このチュートリアルでは、リソース マネージャー テンプレートを使用して C# プログラムから IoT Hub を作成する方法を説明します。

> [AZURE.NOTE] Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[リソース マネージャー デプロイメント モデルとクラシック デプロイメント モデル](../resource-manager-deployment-model.md)です。この記事では、リソース マネージャーのデプロイメント モデルの使用について説明します。

このチュートリアルを完了するには、以下が必要になります。

- Microsoft Visual Studio 2015
- アクティブな Azure アカウント<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。
- テンプレート ファイルの保存先となる [Azure ストレージ アカウント][lnk-storage-account]。
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 以降

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Visual Studio プロジェクトの準備

1. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# Windows のプロジェクトを作成します。プロジェクトに **CreateIoTHub** という名前を付けます。

2. ソリューション エクスプローラーで、プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. NuGet パッケージ マネージャーで、**[プレリリースを含める]** をオンにし、**Microsoft.Azure.Management.ResourceManager** を検索します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

4. NuGet パッケージ マネージャーで、**Microsoft.IdentityModel.Clients.ActiveDirectory** を検索します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

5. NuGet パッケージ マネージャーで、**Microsoft.Azure.Common** を検索します。**[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

6. Program.cs で、既存の **using** ステートメントを以下に置き換えます。

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
7. Program.cs で、次の静的変数を追加して、プレースホルダーの値を置き換えます。このチュートリアルの前の手順で **ApplicationId**、**SubscriptionId**、**TenantId**、および**パスワード**を書き留めています。**Your storage account name** には、実際のテンプレート ファイルの保存先となる Azure ストレージ アカウントの名前を指定します。**リソース グループ名**は、IoT Hub を作成するときに使用するリソース グループの名前で、既存のリソース グループまたは新しいリソース グループのいずれかになります。**デプロイ名**は、デプロイの名前です (**Deployment\_01** など)。

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## テンプレートを送信して IoT Hub を作成する

JSON テンプレートとパラメーター ファイルを使用して、リソース グループに新しい IoT Hub を作成します。また、テンプレートを使用して、既存の IoT Hub に変更を加えることもできます。

1. ソリューション エクスプローラーで、目的のプロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順にクリックします。**template.json** という新しい JSON ファイルをプロジェクトに追加します。

2. **template.json** の内容を次のリソース定義に置き換えて、新しい Standard IoT Hub を **[米国東部]** リージョンに追加します。

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
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
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. ソリューション エクスプローラーで、目的のプロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順にクリックします。**parameters.json** という新しい JSON ファイルをプロジェクトに追加します。

4. **parameters.json** の内容を、新しい IoT Hub の名前 (**{your initials}mynewiothub** など) を設定する次のパラメーター情報に置き換えます (この名前はグローバルに一意でなければならないため、自分の名前やイニシャルを含めます)。

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. **サーバー エクスプローラー**から Azure サブスクリプションに接続し、自分のストレージ アカウントに、**templates** という新しいコンテナーを作成します。**[プロパティ]** パネルで、**[templates]** コンテナーの **[パブリック読み取りアクセス]** 権限を **[BLOB]** に設定します。

6. **サーバー エクスプローラー**で **[templates]** コンテナーを右クリックし、**[BLOB コンテナーの表示]** をクリックします。**[BLOB のアップロード]** ボタンをクリックして **parameters.json** と **templates.json** の 2 つのファイルを選択し、**[開く]** をクリックして、**[templates]** コンテナーに JSON ファイルをアップロードします。JSON データを含んだ BLOB の URL は次のとおりです。

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.windows.net/templates/template.json
    ```

7. 次のメソッドを Program.cs に追加します。
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. テンプレート ファイルとパラメーター ファイルを Azure リソース マネージャーに送信するために、次のコードを **CreateIoTHub** メソッドに追加します。

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. 次のコードを **CreateIoTHub** メソッドに追加します。新しい IoT Hub のステータスとキーを表示するコードです。

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## アプリケーションの完了と実行

**CreateIoTHub** メソッドの呼び出しを追加したうえでアプリケーションをビルドし、実行すれば、アプリケーションは完成です。

1. 次のコードを **Main** メソッドの末尾に追加します。

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. **[ビルド]**、**[ソリューションのビルド]** の順にクリックします。すべてのエラーを修正します。

3. **[デバッグ]**、**[デバッグの開始]** の順にクリックし、アプリケーションを実行します。デプロイメントが実行されるまでに数分かかる場合があります。

4. アプリケーションで新しい IoT Hub が追加されたことを確認するには、[ポータル][lnk-azure-portal]にアクセスし、リソースの一覧を表示するか、**Get-AzureRmResource** PowerShell コマンドレットを使用します。

> [AZURE.NOTE] このサンプル アプリケーションでは、課金の対象とする S1 Standard IoT Hub を追加します。IoT Hub を削除するには、[ポータル][lnk-azure-portal]を使うか、終了時に **Remove-AzureRmResource** PowerShell コマンドレットを使用します。

## 次のステップ

ここでは、C# プログラムと ARM テンプレートを使用して IoT Hub をデプロイしました。次の手順に進んでください。

- [IoT Hub Resource Provider REST API][lnk-rest-api] の機能の詳細をご確認ください。
- Azure リソース マネージャーの機能の詳細については、「[Azure リソース マネージャーの概要][lnk-azure-rm-overview]」を参照してください。

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

<!---HONumber=AcomDC_0601_2016-->