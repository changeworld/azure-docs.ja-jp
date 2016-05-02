<properties
	pageTitle="Azure Resource Manager と C# を使用した VM の管理 | Microsoft Azure"
	description="Azure Resource Manager と C# を使用して、仮想マシンを管理します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/17/2016"
	ms.author="davidmu"/>

# Azure Resource Manager と C を使用した Azure 仮想マシンの管理#  

この記事のタスクを完了するには、以下が必要です。

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)。
- [認証トークン](../resource-group-authenticate-service-principal.md)

## Visual Studio プロジェクトの作成とパッケージのインストール

NuGet パッケージを使用すると、この記事のタスクを完了するために必要なライブラリを簡単にインストールできます。Azure Active Directory 認証ライブラリとコンピューター リソース プロバイダー ライブラリをインストールする必要があります。Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1. **[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。

2. **[テンプレート]** の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

3. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

4. 検索ボックスに「*Active Directory*」と入力し、Active Directory Authentication Library パッケージの **[インストール]** をクリックして、パッケージのインストール手順に従います。

5. ページの上部で、**[リリース前のパッケージを含める]** を選択します。検索ボックスに「*Microsoft.Azure.Management.Compute*」と入力し、Compute .NET ライブラリの **[インストール]** をクリックして、パッケージのインストール手順に従います。

これで、ライブラリを使用して仮想マシンを管理する準備が整いました。

## プロジェクトのセットアップ

Azure Active Directory アプリケーションを作成し、認証ライブラリをインストールしたので、次にアプリケーションの情報を使用して、Azure リソース マネージャーへの要求の認証に使用される資格情報を作成します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Program クラスの Main メソッドに変数を追加し、管理するリソースの名前、リソースの場所 ("Central US" など)、およびサブスクリプション ID を指定します。

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var location = "location name";
        var subscriptionId = "subsciption id";

    すべての変数の値を、使用する名前と ID で置き換えます。サブスクリプション ID は Get-AzureRmSubscription を実行して確認できます。
    
3. 資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

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
	
    {application-id} を前に記録したアプリケーション ID に、{password} を AD アプリケーション用に選択したパスワードに、{tenant-id} をサブスクリプションのテナント ID に、それぞれ置き換えます。
    
4. 資格情報を作成するには、Program.cs の Main メソッドに次のコードを追加します。

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

5. Program.cs ファイルを保存します。

## 仮想マシンに関する情報の表示

1. 前に作成したプロジェクトの Program クラスに、次のメソッドを追加します。

        public static void GetVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var vmResult = computeManagementClient.VirtualMachines.Get(
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
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
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

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        GetVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Program.cs ファイルを保存します。

4. Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

	このメソッドを実行すると、次のように表示されます。
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## 仮想マシンの起動

1. 前に Main メソッドに追加したコードを、資格情報を取得するコードを除いて、すべてコメントアウトします。

2. Program クラスに次のメソッドを追加します。

        public static void StartVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.Start(groupName, vmName);
        }

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        StartVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs ファイルを保存します。

5. Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

	仮想マシンの状態が [実行中] に変化することを確認します。

## 仮想マシンの停止

仮想マシンを停止するには、2 つの方法があります。仮想マシンを停止し、すべての設定を保持して、課金も継続する方法と、仮想マシンを停止し、割り当て解除する方法です。割り当て解除の場合、関連付けられているすべてのリソースも割り当て解除され、仮想マシンへの課金は終了します。

1. 前に Main メソッドに追加したコードを、資格情報を取得するコードを除いて、すべてコメントアウトします。

2. Program クラスに次のメソッドを追加します。

        public static void StopVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.PowerOff(groupName, vmName);
        }

	仮想マシンの割り当てを解除する場合は、PowerOff 呼び出しを次のように変更します。

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        StopVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs ファイルを保存します。

5. Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

    仮想マシンの状態が [停止済み] に変化することを確認します。Deallocate を呼び出すメソッドを実行した場合は、状態が [停止済み (割り当て解除)] になります。

## 仮想マシンの実行の再開

1. 前に Main メソッドに追加したコードを、資格情報を取得するコードを除いて、すべてコメントアウトします。

2. Program クラスに次のメソッドを追加します。

        public static void RestartVirtualMachine(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.Restart(groupName, vmName);
        }

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        RestartVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs ファイルを保存します。

5. Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

## 仮想マシンの削除

1. 前に Main メソッドに追加したコードを、資格情報を取得するコードを除いて、すべてコメントアウトします。

2. Program クラスに次のメソッドを追加します。

        public static void DeleteVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.Delete(groupName, vmName);
        }

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        DeleteVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs ファイルを保存します。

5. Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

## 仮想マシンの更新

この例では、実行中の仮想マシンのサイズを変更する方法を示します。

1. 前に Main メソッドに追加したコードを、資格情報を取得するコードを除いて、すべてコメントアウトします。

2. Program クラスに次のメソッドを追加します。

        public static void UpdateVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var vmResult = computeManagementClient.VirtualMachines.Get(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          computeManagementClient.VirtualMachines.CreateOrUpdate(groupName, vmName, vmResult);
        }

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

        UpdateVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs ファイルを保存します。

5. Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

    仮想マシンのサイズが [Standard\_A1] に変化することを確認します。
    
## 次のステップ

デプロイに問題がある場合は、「[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->