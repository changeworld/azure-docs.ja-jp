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
    ms.date="10/06/2016"
    ms.author="davidmu"/>


# <a name="deploy-azure-resources-using-c#"></a>C を使用した Azure リソースのデプロイ# 

この記事では、C# を使用して Azure リソースを作成する方法について説明します。

まず、以下のタスクを行っているかどうかを確認する必要があります。

-  [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) のインストールの検証
-  [認証トークン](../resource-group-authenticate-service-principal.md)

これらの手順を実行するには約 30 分かかります。

## <a name="step-1:-create-a-visual-studio-project-and-install-the-libraries"></a>手順 1: Visual Studio プロジェクトの作成とライブラリのインストール

NuGet パッケージを使用すると、このチュートリアルを完了するために必要なライブラリを簡単にインストールできます。 Visual Studio で必要なライブラリを入手するには、次の手順に従います。

1. **[ファイル]** > **[新規]** > **[プロジェクト]** の順にクリックします。

2. **[テンプレート]** > の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

3. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[NuGet パッケージの管理]**をクリックします。

4. 検索ボックスに「 *Active Directory* 」と入力し、Active Directory Authentication Library パッケージの **[インストール]** をクリックして、パッケージのインストール手順に従います。

5. ページの上部で、 **[リリース前のパッケージを含める]**を選択します。 検索ボックスに「 *Microsoft.Azure.Management.Compute* 」と入力し、Compute .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

6. 検索ボックスに「 *Microsoft.Azure.Management.Network* 」と入力し、Network .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

7. 検索ボックスに「 *Microsoft.Azure.Management.Storage* 」と入力し、Storage .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

8. 検索ボックスに「 *Microsoft.Azure.Management.ResourceManager* 」と入力し、Resource Management ライブラリの **[インストール]** をクリックします。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

## <a name="step-2:-create-the-credentials-that-are-used-to-authenticate-requests"></a>手順 2: 要求の認証に使用する資格情報の作成

次に、形式に前に作成してアプリケーション情報を Azure Resource Manager に対する認証要求に使用する資格情報の形式に書式設定します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. 必要なトークンを作成するために、次のメソッドを Program クラスに追加します。

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    {client-id} を Azure Active Directory アプリケーションの ID に、{client-secret} を AD アプリケーションのアクセス キーに、および {tenant-id} をサブスクリプションのテナントID に置き換えます。 テナント ID は Get-AzureRmSubscription を実行して確認できます。 アクセス キーは、Azure ポータルで確認できます。

3. 前に作成したメソッドを呼び出すために、次のコードを Program.cs ファイルの Main メソッドに追加します。

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Program.cs ファイルを保存します。

## <a name="step-3:-register-the-resource-providers-and-create-the-resources"></a>手順 3: リソース プロバイダーの登録とリソースの作成

### <a name="register-the-providers-and-create-a-resource-group"></a>プロバイダーを登録し、リソース グループを作成する

すべてのリソースは、リソース グループに含まれる必要があります。 リソースをグループに追加する前に、リソース プロバイダーにサブスクリプションを登録する必要があります。

1. Program クラスの Main メソッドに変数を追加して、リソースに使用する名前を指定します。

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    すべての変数の値を、使用する名前と ID で置き換えます。 サブスクリプション ID は Get-AzureRmSubscription を実行して確認できます。

2. リソース グループを作成してプロバイダーを登録するために、次のメソッドを Program クラスに追加します。

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

仮想マシン用に作成される仮想ハード ディスク ファイルを保存するために、 [ストレージ アカウント](../storage/storage-create-storage-account.md) が必要です。

1. ストレージ アカウントを作成するために、次のメソッドを Program クラスに追加します。

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>パブリック IP アドレスの作成

仮想マシンと通信するには、パブリック IP アドレスが必要です。

1. 仮想マシンのパブリック IP アドレスを作成するために、次のメソッドを Program クラスに追加します。

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

リソース マネージャーのデプロイ モデルで作成された仮想マシンは、仮想ネットワーク内に存在する必要があります。

1. サブネットと仮想ネットワークを作成するために、次のメソッドを Program クラスに追加します。

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>ネットワーク インターフェイスの作成

仮想マシンが仮想ネットワーク上で通信するには、ネットワーク インターフェイスが必要です。

1. ネットワーク インターフェイスを作成するために、次のメソッドを Program クラスに追加します。

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>可用性セットの作成

可用性セットを使うと、アプリケーションで使用する仮想マシンのメンテナンスの管理が容易になります。

1. 可用性セットを作成するために、次のメソッドを Program クラスに追加します。

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. 追加したメソッドを呼び出すために、次のコードを Program クラスの Main メソッドに追加します。

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

すべての関連リソースを作成したので、仮想マシンを作成できます。

1. 仮想マシンを作成するために、次のメソッドを Program クラスに追加します。

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
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
        }

    >[AZURE.NOTE] このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md)に関する記事をご覧ください。

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4:-delete-the-resources"></a>手順 4: リソースを削除する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

1.  リソース グループを削除するために、次のメソッドを Program クラスに追加します。

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

2.  追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5:-run-the-console-application"></a>手順 5: コンソール アプリケーションの実行

1. コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

2. 各状態コードが返されたら **Enter** キーを押して各リソースを作成します。 仮想マシンが作成されたら、次の手順を実行した後、Enter キーを押してすべてのリソースを削除します。

    このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。 Enter キーを押してリソースの削除を開始する前に、Azure ポータルでリソースの作成状況を確認することもできます。

3. リソースの状況を確認するには、Azure ポータルで監査ログを参照します。

    ![Azure ポータルでの監査ログの参照](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>次のステップ

- テンプレートを使用して仮想マシンを作成する方法については、「 [C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ](virtual-machines-windows-csharp-template.md)」を参照してください。
- 「[Resource Manager と PowerShell を使用した Azure Virtual Machines の管理](virtual-machines-windows-csharp-manage.md)」で、作成した仮想マシンを管理する方法を確認します。



<!--HONumber=Oct16_HO2-->


