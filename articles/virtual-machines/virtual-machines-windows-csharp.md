<properties
	pageTitle="C# を使用して Azure リソースをデプロイする | Microsoft Azure"
	description="C# および Azure Resource Manager を使用して Microsoft Azure リソースを作成する方法について説明します。"
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

# C を使用した Azure リソースのデプロイ# 

この記事では、Azure PowerShell を使用して認証とストレージを設定し、C# を使用して Azure リソースを作成する方法について説明します。

このチュートリアルを完了するには、以下に示すものも必要です。

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [認証トークン](../resource-group-authenticate-service-principal.md)

これらの手順を実行するには約 30 分かかります。

## 手順 1. Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択して、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## 手順 2. Visual Studio プロジェクトを作成し、ライブラリをインストールする

NuGet パッケージを使用すると、このチュートリアルを完了するために必要なライブラリを簡単にインストールできます。Azure リソース管理ライブラリ、Azure Active Directory 認証ライブラリ、およびコンピューター リソース プロバイダー ライブラリをインストールする必要があります。Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1. **[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。

2. **[テンプレート]** の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

3. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

4. 検索ボックスに「*Active Directory*」と入力し、Active Directory Authentication Library パッケージの **[インストール]** をクリックして、パッケージのインストール手順に従います。

5. ページの上部で、**[リリース前のパッケージを含める]** を選択します。検索ボックスに「*Microsoft.Azure.Management.Compute*」と入力し、Compute .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

6. 検索ボックスに「*Microsoft.Azure.Management.Network*」と入力し、Network .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

7. 検索ボックスに「*Microsoft.Azure.Management.Storage*」と入力し、Storage .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

8. 検索ボックスに「*Microsoft.Azure.ResourceManager*」と入力し、Resource Management ライブラリの **[インストール]** をクリックします。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

## 手順 3. 要求の認証に使用される資格情報を作成する

Azure Active Directory アプリケーションを作成し、認証ライブラリをインストールしたので、次にアプリケーションの情報を使用して、Azure Resource Manager への要求の認証に使用される資格情報を作成します。これを行うには、次の手順を実行します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

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
        using Microsoft.Rest;

2. 資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

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

	{application-id} を前に記録したアプリケーション ID に、{password} を AD アプリケーション用に選択したパスワードに、{tenant-id} をサブスクリプションのテナント ID に、それぞれ置き換えます。テナント ID は Get-AzureRmSubscription を実行して確認できます。

3. 資格情報を作成するには、Program.cs ファイルの Main メソッドに次のコードを追加します。

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Program.cs ファイルを保存します。

## 手順 4. プロバイダーを登録してリソースを作成するコードを追加する

### プロバイダーを登録し、リソース グループを作成する

すべてのリソースは、リソース グループに含まれる必要があります。リソースをグループに追加する前に、リソース プロバイダーにサブスクリプションを登録する必要があります。

1. Program クラスの Main メソッドに変数を追加し、リソースに使用する名前、リソースの場所 ("Central US" など)、管理者アカウントの情報、サブスクリプション ID を指定します。

        var groupName = "resource group name";
        var ipName = "public ip name";
        var avSetName = "availability set name";
        var nicName = "network interface name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var vnetName = "virtual network name";
        var subnetName = "subnet name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    すべての変数の値を、使用する名前と ID で置き換えます。サブスクリプション ID は Get-AzureRmSubscription を実行して確認できます。

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

          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
        }

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### ストレージ アカウントの作成

ストレージ アカウントは、仮想マシン用に作成される仮想ハード ディスク ファイルに格納する必要があります。

1. ストレージ アカウントを作成するために、次のメソッドを Program クラスに追加します。

        public static void CreateStorageAccount(
          TokenCredentials credential,         
          string storageName,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential);
          storageManagementClient.SubscriptionId = subscriptionId;
          var saResult = storageManagementClient.StorageAccounts.Create(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              AccountType = AccountType.StandardLRS,
              Location = location
            }
          );
          Console.WriteLine(saResult.ProvisioningState);
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        CreateStorageAccount(
          credential,
          storageName,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### パブリック IP アドレスの作成

仮想マシンと通信するには、パブリック IP アドレスが必要です。

1. 仮想マシンのパブリック IP アドレスを作成するには、次のメソッドを Program クラスに追加します。

        public static void CreatePublicIPAddress(
          TokenCredentials credential,
          string ipName,  
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential);
          networkManagementClient.SubscriptionId = subscriptionId;
          var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
          Console.WriteLine(ipResult.ProvisioningState);
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        CreatePublicIPAddress(
          credential,
          ipName,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### 仮想ネットワークの作成

リソース マネージャーのデプロイ モデルで作成された仮想マシンは、仮想ネットワーク内に存在する必要があります。

1. 次のメソッドを Program クラスに追加して、サブネットと仮想ネットワークを作成します。

        public static void CreateNetwork(
          TokenCredentials credential,
          string vnetName,
          string subnetName,
          string nicName,
          string ipName,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential);
          networkManagementClient.SubscriptionId = subscriptionId;
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
          
          Console.WriteLine(vnResult.ProvisioningState);
          
          var subnetResponse = networkManagementClient.Subnets.Get(
            groupName,
            vnetName,
            subnetName
          );

          var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

          Console.WriteLine("Updating the network with the nic...");
          var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = "nicConfig1",
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
          Console.WriteLine(vnResult.ProvisioningState);
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        CreateNetwork(
          credential,
          vnetName,
          subnetName,
          nicName,
          ipName,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### 可用性セットの作成

可用性セットを使うと、アプリケーションで使用する仮想マシンのメンテナンスの管理が容易になります。

1. 可用性セットを作成するには、次のメソッドを Program クラスに追加します。

        public static void CreateAvailabilitySet(
          TokenCredentials credential,
          string avsetName,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        CreateAvailabilitySet(
          credential,
          avSetName,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### 仮想マシンの作成

すべての関連リソースを作成したので、仮想マシンを作成できます。

1. 仮想マシンを作成するには、次のメソッドを Program クラスに追加します。

        public static void CreateVirtualMachine(
          TokenCredentials credential,
          string vmName,
          string groupName,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string subscriptionId,
          string location)
        {
          var networkManagementClient = new NetworkManagementClient(credential);
          networkManagementClient.SubscriptionId = subscriptionId;
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
          Console.WriteLine(vm.ProvisioningState);
        }

	>[AZURE.NOTE] このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。他のイメージの選択の詳細については、「[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md)」を参照してください。

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        CreateVirtualMachine(
          credential,
          vmName,
          groupName,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          subscriptionId,
          location);
        Console.ReadLine();

##手順 5. リソースを削除するコードを追加する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

1. リソース グループを削除するために、次のメソッドを Program クラスに追加します。

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
            Console.WriteLine("Deleting resource group...");
            var resourceGroupClient = new ResourceManagementClient(credential);
            resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

## 手順 6. コンソール アプリケーションを実行する

1. コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

2. 各状態コードが返されたら **Enter** キーを押して各リソースを作成します。仮想マシンが作成されたら、次の手順を実行した後、Enter キーを押してすべてのリソースを削除します。

	このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。Enter キーを押してリソースの削除を開始する前に、Azure ポータルでリソースの作成状況を確認することもできます。

3. Azure ポータルで監査ログを参照し、リソースの状況を確認します。

	![Browse audit logs in Azure portal](./media/virtual-machines-windows-csharp/crpportal.png)
    
## 次のステップ

テンプレートを使用して仮想マシンを作成する方法については、「[Deploy an Azure Virtual Machine using C# and a Resource Manager template (C# と Resource Manager テンプレートを使用した Azure 仮想マシンのデプロイ)](virtual-machines-windows-csharp-template.md)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->