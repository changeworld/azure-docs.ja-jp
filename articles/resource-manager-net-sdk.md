<properties
   pageTitle="Resource Manager SDK for .NET| Microsoft Azure"
   description="Resource Manager SDK for .NET の認証の概要と使用例"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK for .NET  
Azure Resource Manager (ARM) プレビューの SDK は、複数の言語とプラットフォームで利用できます。これらの言語実装はそれぞれ、エコシステム パッケージ マネージャーと GitHub を介して入手できます。

これらの SDK のコードはそれぞれ、[Azure REST ベースの API 仕様](https://github.com/azure/azure-rest-api-specs)から生成されます。これらの仕様はオープン ソースであり、Swagger v2 仕様に基づいています。SDK コードは、[AutoRest](https://github.com/azure/autorest) と呼ばれるオープン ソース プロジェクトを使用して生成されたコードです。AutoRest では、これらの REST ベースの API 仕様が、複数の言語でクライアント ライブラリに変換されます。SDK で生成されたコードに改善する箇所がある場合、広範に適用されている API 仕様形式に基づく、SDK を作成するためのツール全体が自由に使用できます。

Azure SDK for .NET は、Azure Resource Manager によって公開された API の大半を呼び出す際に役立つ、NuGet パッケージのセットとして提供されています。必要な機能が SDK で公開されていない場合は、バックグラウンドで SDK と通常の ARM REST API の呼び出しを簡単に組み合わせることができます。

このドキュメントは、Azure SDK for .NET、Azure ARM API、または Visual Studio を包括的に説明するものではなく、これらを使い始めるための入門編的な内容になっています。

以降で使用するすべてのコード スニペットの引用元である完全なサンプル プロジェクトは、[こちら](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)からダウンロードできます。

## 認証
ARM の認証は、Azure Active Directory (AD) によって処理されます。任意の API に接続するには、最初に Azure AD で認証を行って認証トークンを受信する必要があります。この認証トークンは、すべての要求に対して渡すことができます。このトークンを取得するには、まず Azure AD アプリケーションと、ログインに使用するサービス プリンシパルを作成する必要があります。詳しい手順については、「[ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](./resource-group-create-service-principal-portal.md)」を参照してください。

サービス プリンシパルを作成すると、次の項目が準備できたことになります。
* クライアント ID (GUID)
* クライアント シークレット (文字列)
* テナント ID (GUID) またはドメイン名 (文字列)

### コードから AccessToken を受け取る
認証トークンは、次のコード行を使用して簡単に取得できます。このコード行では、Azure AD テナント ID、Azure AD アプリケーション クライアント ID、および Azure AD アプリケーション クライアント シークレットのみを渡します。トークンは、既定で 1 時間有効なため、いくつかの要求のために取っておきます。

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### 認証したアプリケーションに関連付けられた Azure サブスクリプションを照会する
最初に行うことの 1 つとして、認証したアプリケーションに関連付けられている Azure サブスクリプションを照会します。対象となるサブスクリプションのサブスクリプション ID は、今後実行する各 API 呼び出しに渡す必要があります。

次のサンプル コードでは、Azure SDK for .NET の機能を使わずに、REST API を使用して直接 Azure API を照会します。

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

ここでは、Azure から JSON 応答を取得することに注目してください。これは、その後、ID の一覧を返すためにサブスクリプション ID を抽出します。このドキュメントでこの後に続く Azure ARM API の呼び出しでは 1 つの Azure サブスクリプション ID のみを使用します。そのため、アプリケーションが複数のサブスクリプションに関連付けられている場合は、その中から適切なサブスクリプションを選択し、パラメーターとして渡してください。

これ以降、Azure API に対して行うすべての呼び出しでは Azure SDK for .NET を使用するため、コードは若干異なって見えます。

### TokenCredentials オブジェクトとしてトークンをラップする
以降の API 呼び出しすべてで、"TokenCredentials" オブジェクト形式の Azure AD から受け取ったトークンが必要になります。このようなオブジェクトは、未処理のトークンをパラメーターとしてクラスのコンストラクターに渡すだけで簡単に作成できます。

```csharp
var credentials = new TokenCredentials(token);
```

## リソース グループの作成
Azure では常にリソース グループが中心になるため、リソース グループを作成することから始めましょう。一般的なリソースおよびリソース グループは *ResourceManagementClient* によって処理されます。ここで使用するより特化した管理クライアントのように、作業対象のサブスクリプションを識別するには、サブスクリプション ID のほかに資格情報を提供する必要があります。

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## 手動またはテンプレートの使用によるリソースの作成
Azure Resource Manager API を操作する方法はいくつかあり、そのうちの主な 2 つの方法を次に示します。

* 手動による方法 (特定のリソース プロバイダーを手動で呼び出す)
* Azure Resource Manager テンプレート (ARM テンプレート) を使用する方法

ARM テンプレートを使用することには、次の利点があります。

* 最終的な結果をどのようにしたいかを宣言によって指定します。最終的な結果をどのように出すかではありません。
* デプロイの並列実行を手動で処理する必要はありません。ARM によって自動的に処理されます。
* ARM テンプレートをデプロイするために C# または他の言語を学習する必要はありません。任意の言語を使用してテンプレート化されたデプロイを開始できます。
* テンプレートで使用されているドメイン固有言語 (DSL) は JSON を使用して作成されているため、JSON の使用経験があればだれでも簡単に理解できます。

テンプレートにはこれら利点がありますが、まずは手動で API を呼び出す方法について説明します。

### 仮想マシンを最初から作成する
ここまでに、サブスクリプションとリソース グループを準備できました。仮想マシンをデプロイする場合は、仮想マシンが実際にどのような要素から構成されているかを確認する必要があります。次に示すように、仮想マシンの構成要素は数多くあることがわかります。

* 1 つ以上のストレージ アカウント (永続ディスクの格納用)
* 1 つ以上のパブリック IP アドレス (PIP) (インターネットからのアクセス用、DNS 名を含む)
* 1 つ以上の仮想ネットワーク (VNET) (リソース間の内部通信用)
* 1 つ以上のネットワーク インターフェイス カード (NIC) (VM が通信できるようにするため)
* 1 つ以上の仮想マシン (VM) (ソフトウェアを実行するため)

もう 1 つの興味深いこととして、これらのリソースには、並行して作成できるものもあれば、並行して作成できないものもあります。次に例を示します。

* NIC。PIP と VNet に依存します。
* VM。NIC とストレージ アカウントに依存します。

必要な依存関係が作成される前にリソースをインスタンス化しようとしないことが必要です。このドキュメントで提供する完全な[サンプル](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)では、リソースを並行して効率的に作成しながら、作成済みのリソースを追跡する方法を示しています。

#### ストレージ アカウントの作成
仮想マシンの仮想 VHD を格納するには、ストレージ アカウントが必要です。既存のストレージ アカウントがある場合はそれを複数の VM に使用できますが、限度に達しないように負荷を複数のストレージ アカウントに忘れずに分散してください。すべてのリージョンやすべてのストレージ アカウントの種類ですべての VM サイズを利用できるとは限らないため、ストレージ アカウントの種類とその場所に応じて、選択できる VM サイズが制限される場合があります。

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### パブリック IP アドレス (PIP) の作成
パブリック IP アドレスにより、Azure のリソースにインターネットからアクセスできるようになります。IP アドレスと共に完全修飾ドメイン名 (FQDN) が割り当てられ、これを使用することによってより簡単にアクセスできます。

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### 仮想ネットワーク (VNET) の作成
ARM API を使用して作成されたすべての VM は、仮想ネットワークに含める必要があります (仮想ネットワーク内にあるのがその VM だけの場合でも)。仮想ネットワークには少なくとも 1 つのサブネットを含める必要がありますが、複数のサブネットがある場合は、リソースを複数のサブネットに分けて保護することもできます。

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### ネットワーク インターフェイス カード (NIC) の作成
ネットワーク インターフェイス カード (NIC) は、VM を、その VM が存在する仮想ネットワークに接続します。1 つの VM は、複数の NIC を割り当てることができるため、複数の仮想ネットワークに関連付けることができます。このサンプルでは、VM を 1 つの VNET に接続することだけを想定しています。

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### 仮想マシンの作成
最後に、実際の仮想マシンを作成します。VM は、これまでの手順で作成したすべてのリソースに直接または間接的に依存するため、VM をプロビジョニングする前に、上記のリソースすべてが利用できるようになるまで待つ必要があります。VM のプロビジョニングは、上記のリソースの中で最も時間のかかる処理のため、アプリケーションは処理が完了するまで待機する必要があります。

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### テンプレート化されたデプロイの使用
テンプレートをデプロイする詳しい手順については、「[.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ](./arm-template-deployment/#step-4-create-the-credentials-that-are-used-to-authenticate-requests)」チュートリアルを参照してください。

簡単に言えば、テンプレートのデプロイは、手動によるリソースのプロビジョニングよりもはるかに簡単です。次のコードでは、テンプレートとパラメーター ファイルのある URI を指定してこれを行う方法を示しています。

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```


 
   

<!---HONumber=AcomDC_0316_2016-->