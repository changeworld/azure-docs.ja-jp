---
title: "C# と Resource Manager テンプレートを使用した VM のデプロイ | Microsoft Docs"
description: "C# および Resource Manager テンプレートを使用して Azure VM をデプロイする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: aeea0c65a3332197efcd823e29c8f0c4fe0426b3


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ
リソース グループとテンプレートを使用すると、アプリケーションをサポートするすべてのリソースをまとめて管理できます。 この記事では、Visual Studio と c# を使用して、認証を設定してテンプレートを作成し、その後に作成したテンプレートを使用して Azure リソースをデプロイする方法について説明します。

まず、以下の設定手順を行っているかどうかを確認する必要があります。

*  [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) のインストールの検証
*  [認証トークン](../resource-group-authenticate-service-principal.md)
* [Azure PowerShell](../resource-group-template-deploy.md)、[Azure CLI](../resource-group-template-deploy-cli.md)、または [Azure ポータル](../resource-group-template-deploy-portal.md)を使用してリソース グループを作成します。

これらの手順を実行するには約 30 分かかります。

## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>手順 1: Visual Studio プロジェクト、テンプレート ファイル、パラメーター ファイルを作成する
### <a name="create-the-template-file"></a>テンプレート ファイルを作成する
Azure Resource Manager テンプレートを使用すると、Azure リソースをまとめてデプロイおよび管理することができます。 テンプレートは、リソースや関連付けられているデプロイ パラメーターを JSON で記述したものです。

Visual Studio で、次の手順を実行します。

1. **[ファイル]** > **[新規]** > **[プロジェクト]** の順にクリックします。
2. **[テンプレート]** >  の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。
3. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[追加]** > **[新しいアイテム]** をクリックします。
4. [Web] をクリックして [JSON ファイル] を選択し、[名前] に「 *VirtualMachineTemplate.json* 」と入力して **[追加]**をクリックします。
5. VirtualMachineTemplate.json ファイルの開始と終了の角かっこ内で、必要なスキーマ要素、および必要な contentVersion 要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. [パラメーター](../azure-resource-manager/resource-group-authoring-templates.md#parameters)は必須ではありませんが、パラメーターを指定すると、テンプレートをデプロイする際に値を入力することができます。 contentVersion 要素の後に、parameters 要素とその子要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [変数](../azure-resource-manager/resource-group-authoring-templates.md#variables) をテンプレートで使用して、頻繁に変化する可能性がある値、またはパラメーター値の組み合わせから作成する必要のある値を指定できます。 parameters セクションの後に、variables 要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [リソース](../azure-resource-manager/resource-group-authoring-templates.md#resources) を定義します。 variables セクションの後に、resources セクションを追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. 作成したテンプレート ファイルを保存します。

### <a name="create-the-parameters-file"></a>パラメーター ファイルの作成
テンプレートで定義されているリソース パラメーターの値を指定するには、テンプレートがデプロイされるときに使用される値を含むパラメーター ファイルを作成します。 Visual Studio で、次の手順を実行します。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[追加]** > **[新しいアイテム]** をクリックします。
2. [Web] をクリックして [JSON ファイル] を選択し、[名前] に「 *Parameters.json* 」と入力して **[追加]**をクリックします。
3. Parameters.json ファイルを開き、次の JSON コンテンツを追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

   > [!NOTE]
   > この記事では、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事を参照してください。
   >
   >
4. 作成したパラメーター ファイルを保存します。

## <a name="step-2-install-the-libraries"></a>手順 2: ライブラリをインストールする
NuGet パッケージを使用すると、このチュートリアルを完了するために必要なライブラリを簡単にインストールできます。 リソースを作成するには、Azure リソース管理ライブラリと Azure Active Directory 認証ライブラリをインストールする必要があります。 Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[NuGet パッケージの管理]**をクリックして、[参照] をクリックします。
2. 検索ボックスに「 *Active Directory* 」と入力し、Active Directory Authentication Library パッケージの **[インストール]** をクリックして、パッケージのインストール手順に従います。
3. ページの上部で、 **[リリース前のパッケージを含める]**を選択します。 検索ボックスに「 *Microsoft.Azure.Management.ResourceManager* 」と入力し、Microsoft Azure リソース管理ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>手順 3. 要求の認証に使用される資格情報を作成する
Azure Active Directory アプリケーションが作成され、認証ライブラリがインストールされます。 次に、Azure Resource Manager に対する認証要求に使用する資格情報の形式にアプリケーション情報を書式設定します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. 資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

     private static async Task<AuthenticationResult> GetAccessTokenAsync()   {

       var cc = new ClientCredential("{client-id}", "{client-secret}");
       var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
       var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
       if (token == null)
       {
         throw new InvalidOperationException("Could not get the token.");
       }
       return token;
     }

   {client-id} を Azure Active Directory アプリケーションの ID に、{client-secret} を AD アプリケーションのアクセス キーに、および {tenant-id} をサブスクリプションのテナントID に置き換えます。 テナント ID は Get-AzureRmSubscription を実行して確認できます。 アクセス キーは、Azure ポータルで確認できます。
3. 資格情報を作成するには、Program.cs ファイルの Main メソッドに次のコードを追加します。

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Program.cs ファイルを保存します。

## <a name="step-4-deploy-the-template"></a>手順 4: テンプレートのデプロイ
この手順では、前に作成したリソース グループを使用しますが、[ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) クラスと [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) クラスを使用して、リソース グループを作成することもできます。

1. Program クラスの Main メソッドに変数を追加し、前に作成したリソースの名前、デプロイ開名、およびサブスクリプション ID を指定します。

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    groupName の値を、リソース グループの名前に置き換えます。 deploymentName の値をデプロイに使用する名前に置き換えます。 サブスクリプション ID は Get-AzureRmSubscription を実行して確認できます。
2. 定義したテンプレートを使用してリソースをリソース グループにデプロイするために、次のメソッドを Program クラスに追加します。

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    ストレージ アカウントからテンプレートをデプロイする場合は、Template プロパティを TemplateLink プロパティに置き換えることができます。
3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>手順 5: リソースを削除する
Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 リソース グループから各リソースを個別に削除する必要はありません。 リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。

1. リソース グループを削除するために、次のメソッドを Program クラスに追加します。

     public static async void DeleteResourceGroupAsync(

       TokenCredentials credential,
       string groupName,
       string subscriptionId)
     {

       Console.WriteLine("Deleting resource group...");
       var resourceManagementClient = new ResourceManagementClient(credential)
         { SubscriptionId = subscriptionId };
       await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
     }
2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

     DeleteResourceGroupAsync(

       credential,
       groupName,
       subscriptionId);
     Console.ReadLine();

## <a name="step-6-run-the-console-application"></a>手順 6. コンソール アプリケーションを実行する
1. コンソール アプリケーションを実行するために、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じ資格情報を使用して Azure AD にサインインします。
2. 承認されたことを示す状態が表示されたら、 **Enter** キーを押します。

   このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。 Enter キーを押してリソースの削除を開始する前に、Azure ポータルでリソースの作成状況を確認することもできます。
3. リソースの状況を確認するには、Azure ポータルで監査ログを参照します。

    ![Azure ポータルでの監査ログの参照](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>次のステップ
* デプロイに問題がある場合は、次の手順として、「[Azure Portal でのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。
* 「[Resource Manager と PowerShell を使用した Azure Virtual Machines の管理](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」で、作成した仮想マシンを管理する方法を確認します。



<!--HONumber=Dec16_HO1-->


