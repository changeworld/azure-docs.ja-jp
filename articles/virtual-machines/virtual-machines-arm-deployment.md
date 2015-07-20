<properties 
	pageTitle="Compute、Network、および Storage .NET ライブラリを使用した Azure リソースのデプロイ" 
	description="Compute、Network、および Storage .NET ライブラリで提供される一部のクライアントを使用して、Microsoft Azure のリソースを作成および削除する方法について説明します。" 
	services="virtual-machines,virtual-network,storage" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="davidmu"/>

# Compute、Network、および Storage .NET ライブラリを使用した Azure リソースのデプロイ

このチュートリアルでは、Compute、Network、および Storage .NET ライブラリで提供される一部のクライアントを使用して、Microsoft Azure のリソースを作成および削除する方法について示します。また、Azure Active Directory を使用して Azure リソース マネージャーへの要求を認証する方法についても示します。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

このチュートリアルを完了するには、以下に示すものも必要です。

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure ストレージ アカウント](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/ja-jp/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/ja-jp/download/details.aspx?id=40855)
- [Azure PowerShell](../install-configure-powershell.md)

これらの手順を実行するには約 30 分かかります。

## 手順 1. Azure AD にアプリケーションを追加し、アクセス許可を設定する

Azure AD を使用して Azure リソース マネージャーへの要求を認証するには、アプリケーションを既定のディレクトリに追加する必要があります。アプリケーションを追加するには、次の手順に従います。

1. Azure PowerShell プロンプトを開き、次のコマンドを実行します。

        Switch-AzureMode –Name AzureResourceManager

2. このチュートリアルに使用する Azure アカウントを設定します。このコマンドを実行し、メッセージが表示されたら、サブスクリプションの資格情報を入力します。

	    Add-AzureAccount

3. 次のコマンド内の {password} を使用するパスワードに置き換え、このコマンドを実行してアプリケーションを作成します。

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. 前の手順からの応答にある ApplicationId の値を記録しておきます。この情報は後で必要になります。

	![AD アプリケーションの作成](./media/virtual-machines-arm-deployment/azureapplicationid.png)

	>[AZURE.NOTE]アプリケーション ID は、管理ポータルのアプリケーションのクライアント ID フィールドでも確認できます。

5. {application-id} を記録しておいた ID に置き換えてから、次のようにアプリケーションのサービス プリンシパルを作成します。

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. アプリケーションを使用するためのアクセス許可を設定します。

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 手順 2. Visual Studio プロジェクトを作成し、ライブラリをインストールする

NuGet パッケージを使用すると、このチュートリアルを完了するために必要なライブラリを簡単にインストールできます。Azure リソース管理ライブラリ、Azure Active Directory 認証ライブラリ、およびコンピューター リソース プロバイダー ライブラリをインストールする必要があります。Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1. **[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。

2. **[テンプレート]** の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

3. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

4. 検索ボックスに「*Active Directory*」と入力し、Active Directory Authentication Library パッケージの **[インストール]** をクリックして、パッケージのインストール手順に従います。

5. ページの上部で、**[リリース前のパッケージを含める]** を選択します。検索ボックスに「*Azure Compute*」と入力し、Compute .NET ライブラリの**[インストール]** をクリックして、パッケージのインストール手順に従います。

6. 検索ボックスに「*Azure Network*」と入力し、Network .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

7. 検索ボックスに「 *Azure Storage*」と入力し、Storage .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

8. 検索ボックスに「*Azure Resource Management*」と入力し、Resource Management ライブラリの **[インストール]** をクリックします。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

## 手順 3. 要求の認証に使用される資格情報を作成する

Azure Active Directory アプリケーションを作成し、認証ライブラリをインストールしたので、次にアプリケーションの情報を使用して、Azure リソース マネージャーへの要求の認証に使用される資格情報を作成します。以下の手順を実行します。

1.	作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure.Management.Resources;
		using Microsoft.Azure.Management.Resources.Models;
		using Microsoft.Azure.Management.Storage;
		using Microsoft.Azure.Management.Storage.Models;
		using Microsoft.Azure.Management.Network;
		using Microsoft.Azure.Management.Network.Models;
		using Microsoft.Azure.Management.Compute;
		using Microsoft.Azure.Management.Compute.Models;


2. 資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	{application-id} を前に記録したアプリケーション ID に、{password} を AD アプリケーション用に選択したパスワードに、{tenant-id} をサブスクリプションのテナント ID に、それぞれ置き換えます。テナント ID は Get-AzureSubscription を実行して確認できます。

3.	資格情報を作成するには、Program.cs ファイルの Main メソッドに次のコードを追加します。

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	{subscription-id} を、サブスクリプション ID に置き換えます。

4.	Program.cs ファイルを保存します。

## 手順 4. プロバイダーを登録してリソースを作成するコードを追加する

### プロバイダーを登録し、リソース グループを作成する

リソースは常にリソース グループにデプロイされます。リソースのデプロイ先のリソース グループを作成するには、[ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) クラスと [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) クラスを使用します。

1.	リソース グループを作成するために、次のメソッドを Program クラスに追加します。

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  
          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

		CreateResourceGroup(credential);
		Console.ReadLine();

###ストレージ アカウントの作成

ストレージ アカウントは、仮想マシン用に作成される仮想ハード ディスク ファイルに格納する必要があります。

1.	ストレージ アカウントを作成するために、次のメソッドを Program クラスに追加します。

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");
          
          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
		
		CreateStorageAccount(credential);
		Console.ReadLine();

###仮想ネットワークの作成

仮想マシンは、仮想ネットワークに追加された場合に最も生産性が高くなります。

1.	サブネット、パブリック IP アドレス、および仮想ネットワークを作成するため、Program クラスに次のメソッドを追加します。

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

		CreateNetwork(credential);
		Console.ReadLine();

###仮想マシンの作成

すべての関連リソースを作成したので、仮想マシンを作成できます。

1.	仮想マシンを作成するために、次のメソッドを Program クラスに追加します。

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);
                
            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk 
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]イメージの vhd 名はイメージ ギャラリーで定期的に変更されるので、仮想マシンをデプロイするには現在のイメージ名を取得する必要があります。「[Windows PowerShell を使用してイメージを管理する](https://msdn.microsoft.com/library/azure/dn790330.aspx)」でその方法を参照し、{source-image-name} を使用する vhd ファイルの名前に置き換えます。たとえば、"a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd" などです。

	{subscription-id} を、サブスクリプションの ID に置き換えます。

2.	追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

		CreateVirtualMachine(credential);
        Console.ReadLine();

##手順 5. リソースを削除するコードを追加する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

1.	リソース グループを削除するために、次のメソッドを Program クラスに追加します。

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

		DeleteResourceGroup(credential);
        Console.ReadLine();

##手順 6. コンソール アプリケーションを実行する

1.	コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

2.	各状態コードが返されたら **Enter** キーを押して各リソースを作成します。仮想マシンが作成されたら、次の手順を実行した後、Enter キーを押してすべてのリソースを削除します。

	このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。Enter キーを押してリソースの削除を開始する前に、Azure プレビュー ポータルでリソースの作成状況を確認することもできます。

3. Azure プレビュー ポータルで監査ログを参照して、リソースの状態を確認します。

	![AD アプリケーションの作成](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=July15_HO2-->