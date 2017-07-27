---
title: "C# を使用した Azure 仮想マシンのデプロイ | Microsoft Docs"
description: "C# と Azure Resource Manager を使用して、仮想マシンとそれをサポートするすべてのリソースをデプロイします。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: f04783896eb36e9a8c3e5cc8422a1fb054b36dd2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017


---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>C# を使用して Azure で Windows VM を作成および管理する #

[Azure 仮想マシン](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) には、いくつかのサポート Azure リソースが必要です。 この記事では、C# を使って VM リソースを作成、管理、削除する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio プロジェクトを作成する
> * ライブラリのインストール
> * 資格情報を作成する
> * リソースを作成する
> * 管理タスクを実行する
> * リソースを削除する
> * アプリケーションの実行

これらの手順を実行するには約 20 分かかります。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. まだ [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio) をインストールしていない場合は、インストールを実行します。 [ワークロード] ページで **[.NET デスクトップ開発]** を選び、**[インストール]** をクリックします。 サマリーで、**[.NET Framework 4 から 4.6 の開発ツール]** が自動的に選択されていることが確認できます。 Visual Studio を既にインストールしてある場合は、Visual Studio 起動ツールを使って .NET ワークロードを追加できます。
2. Visual Studio で、**[ファイル]** > **[新規]** > **[プロジェクト]**をクリックします。
3. **[テンプレート]** > **[Visual C#]** で **[コンソール アプリ (.NET Framework)]** を選択し、プロジェクトの名前に「*myDotnetProject*」と入力して、プロジェクトの場所を選んだ後、**[OK]** をクリックします。

## <a name="install-libraries"></a>ライブラリのインストール

NuGet パッケージを使用すると、手順を完了するために必要なライブラリを簡単にインストールできます。 Visual Studio で必要なライブラリを入手するには、次の手順に従います。

1. ソリューション エクスプローラーで *[myDotnetProject]* を右クリックし、**[ソリューションの NuGet パッケージの管理]** をクリックして、**[参照]** をクリックします。
2. 検索ボックスに「*Microsoft.IdentityModel.Clients.ActiveDirectory*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
3. 検索ボックスに「*Microsoft.Azure.Management.Compute*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
4. 検索ボックスに「*Microsoft.Azure.Management.Network*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
5. 検索ボックスに「*Microsoft.Azure.Management.ResourceManager*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

## <a name="create-credentials"></a>資格情報を作成する

この手順を開始する前に、[Active Directory サービス プリンシパル](../../azure-resource-manager/resource-group-create-service-principal-portal.md)にアクセスできることを確認します。 また、後の手順で必要になるので、アプリケーション ID、認証キー、テナント ID を控えておく必要があります。

1. 作成した Program.cs ファイルを開き、次の using ステートメントをファイルの先頭の既存のステートメントに追加します。
   
    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Network;
    using Microsoft.Azure.Management.Network.Models;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. 次に、Program.cs ファイルの Main メソッドで、コードで使用する共通の値を指定する変数を追加します。

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciption-id";
    var location = "westus";
    var vmName = "myVM";
    ```

    **subscription-id** を実際のサブスクリプション ID で置き換えます。

3. 要求を行うために必要な Active Directory 資格情報を作成するために、Program クラスに次のメソッドを追加します。
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("application-id", "authentication-key");
      var context = new AuthenticationContext("https://login.windows.net/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    **application-id**、**authentication-key**、**tenant-id** は、Azure Active Directory サービス プリンシパルを作成するときに収集した値で置き換えます。

4. 前に作成したメソッドを呼び出すために、次のコードを Program.cs ファイルの Main メソッドに追加します。
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

## <a name="create-resources"></a>リソースを作成する

### <a name="initialize-management-clients"></a>管理クライアントを初期化する

Azure で .NET SDK を使ってリソースを作成および管理するには、管理クライアントが必要です。 管理クライアントを作成するには、Program.cs ファイルの Main メソッドに次のコードを追加します。

```
var resourceManagementClient = new ResourceManagementClient(credential)
    { SubscriptionId = subscriptionId };
var networkManagementClient = new NetworkManagementClient(credential)
    { SubscriptionId = subscriptionId };
var computeManagementClient = new ComputeManagementClient(credential)
    { SubscriptionId = subscriptionId };
```

### <a name="create-the-vm-and-supporting-resources"></a>VM とサポート リソースを作成する

すべてのリソースは、[リソース グループ](../../azure-resource-manager/resource-group-overview.md)に含まれる必要があります。

1. リソース グループを作成するために、次のメソッドを Program クラスに追加します。
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName,
      string location)
    {   
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      resourceManagementClient,
      groupName,
      location);
    Console.WriteLine("Resource group creation: " + 
      rgResult.Result.Properties.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

[可用性セット](tutorial-availability-sets.md)を使うと、アプリケーションで使う仮想マシンの管理が容易になります。

1. 可用性セットを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        "myAVSet",
        new AvailabilitySet()
        { 
          Sku = new Microsoft.Azure.Management.Compute.Models.Sku("Aligned"),
          PlatformFaultDomainCount = 3,
          Location = location 
        });
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Availability set created. Press enter to continue...");
    Console.ReadLine();
    ```

仮想マシンと通信するには、[パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)が必要です。

1. 仮想マシンのパブリック IP アドレスを作成するために、次のメソッドを Program クラスに追加します。
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync( 
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
        groupName,
        "myIpAddress",
        new PublicIPAddress
        {
          Location = location,
          PublicIPAllocationMethod = "Dynamic"
        });
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("IP address creation: " + 
      ipResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

仮想マシンは、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)のサブネット内に存在する必要があります。

1. サブネットと仮想ネットワークを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = new Subnet
      {
        Name = "mySubnet",
        AddressPrefix = "10.0.0.0/24"
      };
      var address = new AddressSpace 
      {
        AddressPrefixes = new List<string> { "10.0.0.0/16" }
      };
      return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
        groupName,
        "myVNet",
        new VirtualNetwork
        {
          Location = location,
          AddressSpace = address,
          Subnets = new List<Subnet> { subnet }
        });
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual network creation: " + 
      vnResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

仮想マシンが仮想ネットワーク上で通信するには、ネットワーク インターフェイスが必要です。

1. ネットワーク インターフェイスを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        "myVNet",
        "mySubnet");
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        "myIPaddress");
      return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
        groupName,
        "myNic",
        new NetworkInterface
        {
          Location = location,
          IpConfigurations = new List<NetworkInterfaceIPConfiguration>
          {
            new NetworkInterfaceIPConfiguration
            {
              Name = "myNic",
              PublicIPAddress = publicIP,
              Subnet = subnet
            }
          }
        });
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```
    var ncResult = CreateNetworkInterfaceAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Network interface creation: " + 
      ncResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

すべての関連リソースを作成したので、仮想マシンを作成できます。

1. 仮想マシンを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<VirtualMachine> CreateVirtualMachineAsync(
      NetworkManagementClient networkManagementClient,
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
 
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        "myNic");
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        "myAVSet");
      return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
        groupName,
        "myVM",
        new VirtualMachine
        {
          Location = location,
          AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
          {
            Id = avSet.Id
          },
          HardwareProfile = new HardwareProfile
          {
            VmSize = "Standard_DS1"
          },
          OsProfile = new OSProfile
          {
            AdminUsername = "azureuser",
            AdminPassword = "Azure12345678",
            ComputerName = "myVM",
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
            }
          }
        });
    }
    ```

    > [!NOTE]
    > このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。
    > 
    >

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      networkManagementClient,
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual machine creation: " + 
      vmResult.Result.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

## <a name="perform-management-tasks"></a>管理タスクを実行する

仮想マシンのライフサイクルで、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、コードを作成して自動化したい場合もあるでしょう。

### <a name="get-information-about-the-vm"></a>VM に関する情報を取得する

1. 仮想マシンに関する情報を取得するために、次のメソッドを Program クラスに追加します。

    ```
    public static async void GetVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Getting information about the virtual machine...");

      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
        groupName,
        vmName,
        InstanceViewTypes.InstanceView);

      Console.WriteLine("hardwareProfile");
      Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

      Console.WriteLine("\nstorageProfile");
      Console.WriteLine("  imageReference");
      Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
      Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
      Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
      Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
      Console.WriteLine("  osDisk");
      Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
      Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
      Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
      Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
      Console.WriteLine("\nosProfile");
      Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
      Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
      Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
      Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

      Console.WriteLine("\nnetworkProfile");
      foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
      {
        Console.WriteLine("  networkInterface id: " + nic.Id);
      }

      Console.WriteLine("\nvmAgent");
      Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
      Console.WriteLine("    statuses");
      foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
      {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    message: " + stat.Message);
        Console.WriteLine("    time: " + stat.Time);
      }

      Console.WriteLine("\ndisks");
      foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
      {
        Console.WriteLine("  name: " + idisk.Name);
        Console.WriteLine("  statuses");
        foreach (InstanceViewStatus istat in idisk.Statuses)
        {
          Console.WriteLine("    code: " + istat.Code);
          Console.WriteLine("    level: " + istat.Level);
          Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
          Console.WriteLine("    time: " + istat.Time);
        }
      }

      Console.WriteLine("\nVM general status");
      Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
      Console.WriteLine("  id: " + vmResult.Id);
      Console.WriteLine("  name: " + vmResult.Name);
      Console.WriteLine("  type: " + vmResult.Type);
      Console.WriteLine("  location: " + vmResult.Location);
      Console.WriteLine("\nVM instance status");
      foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
      {
        Console.WriteLine("\n  code: " + istat.Code);
        Console.WriteLine("  level: " + istat.Level);
        Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
      }
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    GetVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="stop-the-vm"></a>VM を停止する

仮想マシンを停止してそのすべての設定を保持する (引き続き課金されます)、または仮想マシンを停止して割り当てを解除します。 仮想マシンの割り当てを解除すると、それに関連付けられているすべてのリソースの割り当ても解除され、仮想マシンに対する課金が終了します。

1. 仮想マシンの割り当てを解除せずに仮想マシンを停止するには、次のメソッドを Program クラスに追加します。

    ```
    public static async void StopVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Stopping the virtual machine...");
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    仮想マシンの割り当てを解除する場合は、PowerOff 呼び出しを次のコードに変更します。

    ```
    await computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    StopVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="start-the-vm"></a>VM を起動する

1. 仮想マシンを起動するために、次のメソッドを Program クラスに追加します。

    ```
    public static async void StartVirtualMachineAsync(
      string groupName, 
      string vmName, 
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Starting the virtual machine...");
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    StartVirtualMachineAsync(
      groupName,
      vmName,
      ComputeManagementClient computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="resize-the-vm"></a>VM のサイズを変更する

仮想マシンのサイズを決定するときは、デプロイのさまざまな面を考慮する必要があります。 詳しくは、「[VM サイズ](sizes.md)」をご覧ください。  

1. 仮想マシンのサイズを変更するために、次のメソッドを Program クラスに追加します。

    ```
    public static async void UpdateVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Updating the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_DS3";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    UpdateVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="add-a-data-disk-to-the-vm"></a>VM にデータ ディスクを追加する

1. 仮想マシンにデータ ディスクを追加するために、次のメソッドを Program クラスに追加します。

    ```
    public static async void AddDataDiskAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
        {
          Lun = 0,
          Name = "myDataDisk1",
          CreateOption = DiskCreateOptionTypes.Empty,
          DiskSizeGB = 2,
          Caching = CachingTypes.ReadWrite
        });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    AddDataDiskAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

## <a name="delete-resources"></a>リソースを削除する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

1. リソース グループを削除するために、次のメソッドを Program クラスに追加します。
   
    ```
    public static async void DeleteResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName)
    {
      Console.WriteLine("Deleting resource group...");
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```   
    DeleteResourceGroupAsync(
      resourceManagementClient,
      groupName);
    Console.ReadLine();
    ```

3. プロジェクトを保存します。

## <a name="run-the-application"></a>アプリケーションの実行

1. コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

2. 各リソースの状態が返された後、**Enter** キーを押します。 状態の情報では、プロビジョニングが**成功**状態になっている必要があります。 仮想マシンが作成された後、作成したすべてのリソースを削除する機会があります。 **Enter** キーを押してリソースの削除を開始する前に、Azure Portal でリソースの作成状況を確認することもできます。 Azure Portal が開いたままになっている場合、新しいリソースを表示するにはブレードの更新が必要になる場合があります。  

    このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。 アプリケーションが終了してから、すべてのリソースとリソース グループが削除されるまで、数分かかる場合があります。

## <a name="next-steps"></a>次のステップ
* テンプレートを使用して仮想マシンを作成する方法については、「 [C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* [Azure libraries for .NET](https://docs.microsoft.com/dotnet/azure/index?view=azure-dotnet) の使用方法の詳細について学習します。


