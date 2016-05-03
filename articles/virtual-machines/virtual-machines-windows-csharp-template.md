<properties
	pageTitle="C# と Resource Manager テンプレートを使用した VM のデプロイ | Microsoft Azure"
	description="C# および Resource Manager テンプレートを使用して Azure VM をデプロイする方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ

リソース グループとテンプレートを使用すると、アプリケーションをサポートするすべてのリソースをまとめて管理できます。この記事では、Azure PowerShell を使用して認証とストレージを設定し、C# を使用して Azure リソースを作成するテンプレートを構築してデプロイする方法について説明します。

このチュートリアルを完了するには、次のものが必要です。

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [認証トークン](../resource-group-authenticate-service-principal.md)

これらの手順を実行するには約 30 分かかります。

## 手順 1. Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択して、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。
    
## 手順 2: テンプレート ストレージのリソース グループを作成する

すべてのリソースをリソース グループにデプロイする必要があります。詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。

1. リソースの作成に使用できる場所の一覧を取得します。

	    Get-AzureLocation | sort Name | Select Name

2. **$locName** の値を一覧の場所に置き換えます (例: **Central US**)。変数を作成します。

        $locName = "location name"
        
3. **$rgName** の値を、新しいリソース グループの名前に置き換えます。変数とリソース グループを作成します。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    次のような結果が表示されます。
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## 手順 3: ストレージ アカウントとテンプレート コンテナーを作成します。

作成およびデプロイするテンプレートを格納するために、ストレージ アカウントが必要です。

1. $stName の値を、ストレージ アカウントの名前 (小文字と数字のみ) に置き換えます。名前の一意性をテストします。

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    このコマンドで **False** が返された場合は、提案した名前は一意です。
    
2. 次のコマンドを実行して、ストレージ アカウントを作成します。
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
3. {blob-storage-endpoint} を、アカウントの BLOB ストレージのエンドポイントに置き換えます。{storage-account-name} をストレージ アカウントの名前に置き換えます。{primary-storage-key} をプライマリ アクセス キーに置き換えます。これらのコマンドを実行して、ファイルが格納されるコンテナーを作成します。エンドポイントとキーの値は、Azure ポータルから取得できます。

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## 手順 3: Visual Studio プロジェクト、テンプレート ファイル、パラメーター ファイルを作成する

### テンプレート ファイルを作成する

Azure リソース マネージャー テンプレートによって、リソースや関連するデプロイ パラメーターの JSON 記述を使用して、Azure リソースをまとめてデプロイし、管理することが可能になります。このチュートリアルで作成するテンプレートは、テンプレート ギャラリーにあるテンプレートと似ています。詳細については、「[Deploy a simple Windows VM in West US (米国西部での簡単な Windows VM のデプロイ)](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/)」を参照してください。

Visual Studio で、次の手順を実行します。

1. **[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。

2. **[テンプレート]** の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

3. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックします。

4. [Web] をクリックし、[JSON File] を選択して、[名前] に「*VirtualMachineTemplate.json*」と入力して **[追加]** をクリックします。

5. VirtualMachineTemplate.json ファイルの開始と終了の角かっこ内で、必要なスキーマ要素、および必要な contentVersion 要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [パラメーター](../resource-group-authoring-templates.md#parameters)は常に必要なわけではありませんが、テンプレートの管理が容易になります。パラメーターでは、値の型、必要な場合の既定値、および場合によっては許容される値を記述します。このチュートリアルでは、仮想マシン、ストレージ アカウント、および仮想ネットワークの作成に使用するパラメーターをテンプレートに追加します。contentVersion 要素の後に、parameters 要素とその子要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. [変数](../resource-group-authoring-templates.md#variables)をテンプレートで使用して、頻繁に変化する可能性がある値、またはパラメーター値の組み合わせから作成する必要のある値を指定できます。parameters セクションの後に、variables 要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdiskforwindowssimple",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"  
          },
        }

8. 次に、仮想マシン、仮想ネットワーク、ストレージ アカウントなどの[リソース](../resource-group-authoring-templates.md#resources)を定義します。variables セクションの後に、resources セクションを追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [ "[variables('addressPrefix')]" ]
                },
                "subnets": [ {
                  "name": "[variables('subnetName')]",
                  "properties": {
                    "addressPrefix": "[variables('subnetPrefix')]"
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
                    },
                    "subnet": {
                      "id": "[variables('subnetRef')]"
                    }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computerName": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]",
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku": "[variables('windowsOSVersion')]",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                  } ]
                }
              }
            } ]
          }
      
9. 作成したテンプレート ファイルを保存します。

### パラメーター ファイルの作成

テンプレートで定義されているリソース パラメーターの値を指定するには、値を含むパラメーター ファイルを作成し、テンプレートと共にリソース マネージャーに送信します。Visual Studio で、次の手順を実行します。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックします。

2. [Web] をクリックし、[JSON File] を選択して、[名前] に「*Parameters.json*」と入力して **[追加]** をクリックします。

3. Parameters.json ファイルを開き、次の JSON コンテンツを追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] この記事では、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。他のイメージの選択の詳細については、[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md)に関する記事を参照してください。

4. 作成したパラメーター ファイルを保存します。

### ファイルのアップロードおよびこれらを使用するためのアクセス許可の設定 

テンプレート ファイルとパラメーター ファイルには、Azure ストレージ アカウントから Azure リソース マネージャーを使用してアクセスします。作成した最初のストレージにファイルを配置するには、以下の手順を実行します。

1. [Cloud Explorer] を開き、作成済みのストレージ アカウントのテンプレート コンテナーに移動します。

2. テンプレート コンテナー ウィンドウで、ウィンドウの右上隅にある BLOB アップロード アイコンをクリックし、作成した VirtualMachineTemplate.json ファイルを参照して、**[開く]** をクリックします。

3. BLOB アップロード アイコンを再びクリックし、作成した Parameters.json ファイルを参照して、**[開く]** をクリックします。

## 手順 4: ライブラリをインストールする

NuGet パッケージを使用すると、このチュートリアルを完了するために必要なライブラリを簡単にインストールできます。Azure リソース管理ライブラリと Azure Active Directory 認証ライブラリをインストールする必要があります。Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 検索ボックスに「*Active Directory*」と入力し、Active Directory Authentication Library パッケージの **[インストール]** をクリックして、パッケージのインストール手順に従います。

4. ページの上部で、**[リリース前のパッケージを含める]** を選択します。検索ボックスに「*Microsoft.Azure.ResourceManager*」と入力し、Microsoft Azure リソース管理ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

##手順 5: 要求の認証に使用する資格情報を作成する

Azure Active Directory アプリケーションを作成し、認証ライブラリをインストールしたので、次にアプリケーションの情報を使用して、Azure Resource Manager への要求の認証に使用される資格情報を作成します。これを行うには、次の手順を実行します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    {application-id} を前に記録したアプリケーション ID に、{password} を AD アプリケーション用に選択したパスワードに、{tenant-id} をサブスクリプションのテナント ID に、それぞれ置き換えます。テナント ID は Get-AzureRmSubscription を実行して確認できます。

3. 資格情報を作成するには、Program.cs ファイルの Main メソッドに次のコードを追加します。

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Program.cs ファイルを保存します。

## 手順 6: テンプレートをデプロイするためのコードを追加する

この手順では、[ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) クラスと [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) クラスを使用して、リソースのデプロイ先のリソース グループを作成します。

1. Program クラスの Main メソッドに変数を追加し、リソースに使用する名前、リソースの場所 ("Central US" など)、管理者アカウントの情報、サブスクリプション ID を指定します。

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    すべての変数の値を、使用する名前と ID で置き換えます。サブスクリプション ID は Get-AzureRmSubscription を実行して確認できます。StorageName 変数の値は、テンプレートが格納されているストレージ アカウントの名前です。
    
2. リソース グループを作成するために、次のメソッドを Program クラスに追加します。

        public static void CreateResourceGroup(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var resourceGroup = new ResourceGroup {
            Location = location
          };
          var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
          Console.WriteLine(rgResult.Properties.ProvisioningState);
        }

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. 定義したテンプレートを使用してリソースをリソース グループにデプロイするために、次のメソッドを Program クラスに追加します。

        public static void CreateTemplateDeployment(
          TokenCredentials credential,
          string groupName,
          string storageName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
            }
          };
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
            groupName,
            deploymentName,
            deployment);
          Console.WriteLine(dpResult.Properties.ProvisioningState);
        }

4. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName}",
          subscriptionId}");
        Console.ReadLine();

##手順 7: リソースを削除するコードを追加する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。リソース グループから各リソースを個別に削除する必要はありません。リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。

1.	リソース グループを削除するために、次のメソッドを Program クラスに追加します。

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##手順 8: コンソール アプリケーションを実行する

1.	コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

2.	承認済みの状態が表示されたら、**Enter** キーを押します。

	このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。Enter キーを押してリソースの削除を開始する前に、Azure ポータルでリソースの作成状況を確認することもできます。

3. Azure ポータルで監査ログを参照し、リソースの状況を確認します。

	![Azure ポータルでの監査ログの参照](./media/virtual-machines-windows-csharp-template/crpportal.png)

## 次のステップ

- デプロイに問題がある場合は、「[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。
- 「[Manage virtual machines using Azure Resource Manager and PowerShell](virtual-machines-windows-ps-manage.md)」 (Azure Resource Manager と PowerShell を使用した仮想マシンの管理) で、作成した仮想マシンを管理する方法を確認します。

<!---HONumber=AcomDC_0420_2016-->