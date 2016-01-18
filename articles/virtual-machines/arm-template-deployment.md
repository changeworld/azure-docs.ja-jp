<properties
	pageTitle="テンプレートを使用して Azure リソースをデプロイする | Microsoft Azure"
	description="Azure リソース管理ライブラリで利用可能ないくつかのクライアントを使用して、仮想マシン、仮想ネットワーク、ストレージ アカウントをデプロイする方法を学習します。"
	services="virtual-machines,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# .NET ライブラリとテンプレートを使用した Azure リソースのデプロイ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。


リソース グループとテンプレートを使用すると、アプリケーションをサポートするすべてのリソースをまとめて管理できます。このチュートリアルでは、Azure リソース管理ライブラリで利用可能なクライアントの一部を使用する方法と、仮想マシン、仮想ネットワーク、ストレージ アカウントをデプロイするためのテンプレートを作成する方法を示します。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

このチュートリアルを完了するには、以下に示すものも必要です。

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure ストレージ アカウント](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE]このときに作成したストレージ アカウントは、テンプレートの格納に使用されます。仮想マシンのディスクの格納に使用するテンプレートをデプロイするときに、もう 1 つのストレージ アカウントが作成されます。このストレージ アカウント名のテンプレートにコンテナーを作成します。

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

これらの手順を実行するには約 30 分かかります。

## 手順 1. Azure AD にアプリケーションを追加し、アクセス許可を設定する

Azure AD を使用して Azure リソース マネージャーへの要求を認証するには、アプリケーションを既定のディレクトリに追加する必要があります。アプリケーションを追加するには、次の手順に従います。

1. Azure PowerShell コマンド プロンプトを開き、このコマンドを実行し、メッセージが表示されたら、サブスクリプションの資格情報を入力します。

			Login-AzureRmAccount

2. 次のコマンド内の {password} を使用するパスワードに置き換え、このコマンドを実行してアプリケーションを作成します。

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]アプリケーションが作成された後で、返されたアプリケーション ID をメモしてください。これは次の手順で必要になります。アプリケーション ID は、Azure ポータルの Active Directory セクションにあるアプリケーションのクライアント ID フィールドでも確認できます。

3. {application-id} を記録しておいた ID に置き換えてから、次のようにアプリケーションのサービス プリンシパルを作成します。

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. アプリケーションを使用するためのアクセス許可を設定します。

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 手順 2: Visual Studio プロジェクト、テンプレート ファイル、パラメーター ファイルを作成する

### テンプレート ファイルを作成する

Azure リソース マネージャー テンプレートによって、リソースや関連するデプロイ パラメーターの JSON 記述を使用して、Azure リソースをまとめてデプロイし、管理することが可能になります。このチュートリアルで作成するテンプレートは、テンプレート ギャラリーにあるテンプレートと似ています。詳細については、「[Deploy a simple Windows VM in West US (米国西部での簡単な Windows VM のデプロイ)](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)」を参照してください。

Visual Studio で、次の手順を実行します。

1. **[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。

2. **[テンプレート]** の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

3. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックします。

4. [新しい項目の追加] ウィンドウで、**[テキスト ファイル]** を選択し、名前に「*VirtualMachineTemplate.json*」と入力して、**[追加]** をクリックします。

5. VirtualMachineTemplate.json ファイルを開き、開始と終了の角かっこ、必要なスキーマ要素、および必要な contentVersion 要素を追加します。

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. [パラメーター](../resource-group-authoring-templates.md#parameters)は常に必要なわけではありませんが、テンプレートの管理が容易になります。パラメーターでは、値の型、必要な場合の既定値、および場合によっては許容される値を記述します。このチュートリアルでは、仮想マシン、ストレージ アカウント、および仮想ネットワークの作成に使用されるパラメーターをテンプレートに追加します。

    contentVersion 要素の後に、parameters 要素とその子要素を追加します。

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. [変数](../resource-group-authoring-templates.md#variables)をテンプレートで使用して、頻繁に変化する可能性がある値、またはパラメーター値の組み合わせから作成する必要のある値を指定できます。

    parameters セクションの後に、variables 要素を追加します。

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
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
	```

8. 次に、仮想マシン、仮想ネットワーク、ストレージ アカウントなどの[リソース](../resource-group-authoring-templates.md#resources)を定義します。

    variables セクションの後に、resources セクションを追加します。

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
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
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
				"location": "[variables('location')]",
				"properties": {
					"accountType": "[variables('storageAccountType')]"
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-06-15",
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
							"sku": "[parameters('windowsOSVersion')]",
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
		```

9. 作成したテンプレート ファイルを保存します。

### パラメーター ファイルの作成

テンプレートで定義されているリソース パラメーターの値を指定するには、値を含むパラメーター ファイルを作成し、テンプレートと共にリソース マネージャーに送信します。Visual Studio で、次の手順を実行します。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックします。

2. [新しい項目の追加] ウィンドウで、**[テキスト ファイル]** を選択し、名前に「*Parameters.json*」と入力して、**[追加]** をクリックします。

3. Parameters.json ファイルを開き、次の JSON コンテンツを追加します。

	```
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
	```

    >[AZURE.NOTE]このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。他のイメージの選択の詳細については、「[Windows PowerShell と Azure CLI による Azure Virtual Machine イメージのナビゲーションと選択](resource-groups-vm-searching.md)」を参照してください。


4. 作成したパラメーター ファイルを保存します。

### ファイルのアップロード

テンプレート ファイルとパラメーター ファイルには、Azure ストレージ アカウントから Azure リソース マネージャーを使用してアクセスします。作成した最初のストレージにファイルを配置するには、以下の手順を実行します。

1. サーバー エクスプローラーを開き、ファイルを配置するストレージ アカウント内のコンテナーに移動します。このチュートリアルでは、テンプレートを配置するコンテナーの名前は templates です。

2. テンプレート コンテナー ウィンドウの右上隅にある BLOB アップロード アイコンをクリックし、作成した VirtualMachineTemplate.json ファイルを参照して、**[開く]** をクリックします。

3. BLOB アップロード アイコンを再びクリックし、作成した Parameters.json ファイルを参照して、**[開く]** をクリックします。

## 手順 3: ライブラリをインストールする

NuGet パッケージを使用すると、このチュートリアルを完了するために必要なライブラリを簡単にインストールできます。Azure リソース管理ライブラリと Azure Active Directory 認証ライブラリをインストールする必要があります。Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 検索ボックスに「*Active Directory*」と入力し、Active Directory Authentication Library パッケージの **[インストール]** をクリックして、パッケージのインストール手順に従います。

3. ページの上部で、**[リリース前のパッケージを含める]** を選択します。検索ボックスに「*Microsoft.Azure.Management.Resources*」と入力し、Microsoft Azure リソース管理ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

##手順 4: 要求の認証に使用する資格情報を作成する

Azure Active Directory アプリケーションを作成し、認証ライブラリをインストールしたので、次にアプリケーションの情報を使用して、Azure リソース マネージャーへの要求の認証に使用される資格情報を作成します。以下の手順を実行します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	{application-id} を前に記録したアプリケーション ID に、{password} を AD アプリケーション用に選択したパスワードに、{tenant-id} をサブスクリプションのテナント ID に、それぞれ置き換えます。テナント ID は Get-AzureSubscription を実行して確認できます。

3. 資格情報を作成するには、Program.cs ファイルの Main メソッドに次のコードを追加します。

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4. Program.cs ファイルを保存します。


## 手順 5: テンプレートをデプロイするためのコードを追加する

リソースは常にテンプレートからリソース グループにデプロイされます。リソースのデプロイ先のリソース グループを作成するには、[ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) クラスと [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) クラスを使用します。

1. リソース グループを作成するために、次のメソッドを Program クラスに追加します。

	```
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
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	{group-name} を、リソース グループに使用する名前に置き換えます。{subscription-id} を、サブスクリプション識別子に置き換えます。サブスクリプション識別子を取得するには、Get-AzureSubscription を実行します。{location} をリソースを、作成するリージョン ("West US" など) に置き換えます。

3. 定義したテンプレートを使用してリソースをリソース グループにデプロイするために、次のメソッドを Program クラスに追加します。

	```
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
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
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
	```

	{storage-name} を、前にファイルを配置したアカウントの名前に置き換えます。

4. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    {group-name} を、作成したリソース グループ名に置き換えます。{storage-name} を、テンプレート ファイルを配置したストレージ アカウント名に置き換えます。{deployment-name} を、リソースのデプロイメント セットの識別に使用する名前に置き換えます。{subscription-id} を、サブスクリプション識別子に置き換えます。サブスクリプション識別子を取得するには、Get-AzureSubscription を実行します。

##手順 6: リソースを削除するコードを追加する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。リソース グループから各リソースを個別に削除する必要はありません。リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。

1.	リソース グループを削除するために、次のメソッドを Program クラスに追加します。

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2.	追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    {group-name} を、作成したリソース グループ名に置き換えます。{subscription-id} を、サブスクリプション識別子に置き換えます。サブスクリプション識別子を取得するには、Get-AzureSubscription を実行します。

##手順 7: コンソール アプリケーションを実行する

1.	コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

2.	承認済みの状態が表示されたら、**Enter** キーを押します。

	このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。Enter キーを押してリソースの削除を開始する前に、Azure ポータルでリソースの作成状況を確認することもできます。

3. Azure ポータルで監査ログを参照し、リソースの状況を確認します。

	![AD アプリケーションの作成](./media/arm-template-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->