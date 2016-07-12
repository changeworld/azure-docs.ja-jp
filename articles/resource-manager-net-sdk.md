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
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK for .NET  
Azure Resource Manager の SDK は、複数の言語とプラットフォームで利用できます。これらの言語実装はそれぞれ、エコシステム パッケージ マネージャーと GitHub を介して入手できます。

これらの SDK のコードはそれぞれ、[Azure REST ベースの API 仕様](https://github.com/azure/azure-rest-api-specs)から生成されます。これらの仕様はオープン ソースであり、Swagger 2.0 仕様に基づいています。SDK コードは、[AutoRest](https://github.com/azure/autorest) と呼ばれるオープン ソース プロジェクトを使用して生成されています。AutoRest では、これらの REST ベースの API 仕様が、複数の言語でクライアント ライブラリに変換されます。SDK で生成されたコードに改善する箇所がある場合、広範に適用されている API 仕様形式に基づく、SDK を作成するためのツール全体が自由に使用できます。

[Azure SDK for .NET](https://azure.microsoft.com/downloads/) に含まれている一連の NuGet パッケージを使用すると、Azure Resource Manager API の大半を呼び出すことができます。必要な機能が SDK にない場合は、バックグラウンドで SDK と通常の Resource Manager REST API の呼び出しを簡単に組み合わせることができます。

この記事の目的は、Azure SDK for .NET、Azure Resource Manager API、Visual Studio についての情報をあらゆる角度から網羅することではありません。読者がすぐに取り組めることを重視して執筆しています。

以降のコード スニペットはすべて、[ダウンロード可能なサンプル プロジェクト](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)からの抜粋です。

## NuGet パッケージのインストール

この記事の例は、Azure SDK for .NET の他に 2 つの NuGet パッケージを必要とします。そのインストール手順は次のとおりです。

1. Visual Studio でプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **Microsoft.IdentityModel.Clients.ActiveDirectory** を探し、最新の安定したバージョンのパッケージをインストールします。
3. **Microsoft.Azure.Management.ResourceManager** を探し、**[リリース前のパッケージを含める]** を選択します。最新のプレビュー バージョン (1.1.2-preview など) をインストールしてください。

## 認証
Resource Manager の認証は、Azure Active Directory (Azure AD) によって管理されます。API に接続するには、最初に Azure AD で認証を行ってアクセス トークンを受け取る必要があります。すべての要求には、このアクセス トークンを渡すことができます。このトークンを取得するには、まず Azure AD アプリケーションと、サインインに使用するサービス プリンシパルを作成する必要があります。詳しい手順については、次のいずれかの記事を参照してください。

- [リソースにアクセスできる Active Directory アプリケーションを Azure PowerShell で作成する](resource-group-authenticate-service-principal.md)
- [リソースにアクセスできる Active Directory アプリケーションを Azure CLI で作成する](resource-group-authenticate-service-principal-cli.md)
- [リソースにアクセスできる Active Directory アプリケーションを Azure ポータルで作成する](resource-group-create-service-principal-portal.md)

サービス プリンシパルを作成すると、次の項目が準備できたことになります。

- クライアント ID またはアプリケーション ID (GUID)
- クライアント シークレットまたはパスワード (文字列)
- テナント ID (GUID) またはドメイン名 (文字列)

### アクセス トークンを受け取るためのコード
アクセス トークンは、以下のコードに引数として、Azure AD テナント ID、Azure AD アプリケーション クライアント ID、Azure AD アプリケーション クライアント シークレットを渡すことによって取得できます。取得したトークンは、複数の要求で使用できるように保存します。既定ではトークンが 1 時間有効です。

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

サインインには、テナント ID を使わずに、次のコードのように Azure AD ドメインを使用することができます。この方法を使用する場合、テナント ID の代わりにドメイン名を受け取るようにメソッドのシグネチャを変更する必要があります。

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

証明書を使って認証を行う Azure AD アプリケーションのアクセス トークンは、次のコードで取得できます。

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### 認証したアプリケーションに関連付けられた Azure サブスクリプションを照会する
認証したアプリケーションに関連付けられている Azure サブスクリプションを照会してみましょう。対象となるサブスクリプションのサブスクリプション ID は、今後実行する各 API 呼び出しに渡す必要があります。

次のサンプル コードでは、REST API を使用して直接 Azure API を照会します。つまり、Azure SDK for .NET の機能は一切使用していません。

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

Azure からは、JSON 形式の応答が返されることに注意してください。この応答からサブスクリプション ID を抽出することによって、一連の ID を取得することになります。以降この記事で行う Azure Resource Manager API 呼び出しはすべて、単一の Azure サブスクリプション ID を使用しています。そのため、アプリケーションが複数のサブスクリプションに関連付けられている場合は、適切なサブスクリプションを 1 つ選び、パラメーターとして渡したうえで後続の手順を実行してください。

ここからは、Azure API に対して行うすべての呼び出しで Azure SDK for .NET を使用します。そのためコードの体裁が若干異なります。

### TokenCredentials オブジェクトとしてトークンをラップする
以降の API 呼び出しすべてで、TokenCredentials オブジェクト形式の Azure AD から受け取ったトークンが必要になります。このようなオブジェクトは、未処理のトークンをパラメーターとしてクラスのコンストラクターに渡すことで簡単に作成できます。

```csharp
var credentials = new TokenCredentials(token);
```

所有している Resource Manager NuGet パッケージが以前のバージョン (名前が Microsoft.Azure.Management.Resources) である場合、次のコードを使用する必要があります。

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## リソース グループの作成
Azure であらゆる物事の中心となるのはリソース グループです。そこでまず、リソース グループを作成してみましょう。一般的なリソースとリソース グループは、*ResourceManagementClient* によって管理されます。この後、より目的に特化した管理クライアントを使用しますが、それらの管理クライアントにはいずれも、作業対象のサブスクリプションを識別するために資格情報とサブスクリプション ID を指定する必要があります。

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
Azure Resource Manager API を操作する方法はいくつかありますが、そのうちの主な 2 つの方法を次に示します。

* 手動による方法 (特定のリソース プロバイダーを手動で呼び出す)
* Azure Resource Manager テンプレートを使用する方法

Resource Manager テンプレートを使用する利点は次のとおりです。

* 最終的な結果をどのようにしたいかを宣言によって指定します。最終的な結果をどのように出すかではありません。
* デプロイの並列実行を手動で管理する必要はありません。Resource Manager によって自動的に管理されます。
* Resource Manager テンプレートをデプロイするために C# などの言語を学習する必要はありません。任意の言語を使用してテンプレート化されたデプロイを開始できます。
* テンプレートで使用されているドメイン固有言語 (DSL) は JSON で構築されています。JSON の使用経験があればだれでも簡単に理解することができます。

このようにテンプレートにはさまざまな利点がありますが、まずは手動で API を呼び出す方法について説明します。

### 仮想マシンを最初から作成する
サブスクリプションとリソース グループは既に準備が整っています。仮想マシン (VM) をデプロイするには、その成り立ちをよく理解しておく必要があります。

* 1 つ以上のストレージ アカウント (永続ディスクの格納用)
* 1 つ以上のパブリック IP アドレス (DNS 名を含む) (Azure 内のリソースにインターネットからアクセスできるようにするため)
* 1 つ以上の仮想ネットワーク (リソース間の内部通信用)
* 1 つ以上のネットワーク インターフェイス カード (NIC) (VM が通信できるようにするため)
* 1 つ以上の仮想マシン (VM) (ソフトウェアを実行するため)

これらのリソースは並列的に作成できるものもありますが、そうでないものもあります。次に例を示します。

* NIC。パブリック IP アドレスと仮想ネットワークに依存しています。
* VM。NIC とストレージ アカウントに依存しています。

必要な依存関係が満たされるまでは、リソースをインスタンス化しないでください。完全な[サンプル](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)では、リソースを並行して効率的に作成しながら、作成済みのリソースを追跡する方法を示しています。

#### ストレージ アカウントの作成
仮想マシンの仮想ハード ディスクを格納するには、ストレージ アカウントが必要です。既にストレージ アカウントをお持ちの方は、そのアカウントを複数の VM に使用できます。ただし、制限に達することのないよう複数のストレージ アカウントに負荷を分散してください。また、すべてのリージョンやすべてのストレージ アカウントの種類ですべての VM サイズを利用できるとは限らないため、ストレージ アカウントの種類とその場所に応じて、選択できる VM サイズが制限される場合があります。

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

#### パブリック IP アドレスの作成
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
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### 仮想ネットワークの作成
Resource Manager API を使用して作成されたすべての VM は、仮想ネットワークに含める必要があります (仮想ネットワーク内にあるのがその VM だけの場合でも)。仮想ネットワークに最低限含める必要のあるサブネットは 1 つですが、複数のサブネットを使用することで、リソースを複数のサブネットに分けて保護することができます。

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

#### ネットワーク インターフェイス カードの作成
NIC は、VM を、その VM が存在する仮想ネットワークに接続します。1 つの VM は、複数の NIC を割り当てることができるため、複数の仮想ネットワークに関連付けることができます。このサンプルは、VM を関連付ける仮想ネットワークが 1 つだけであることを想定しています。

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
最後にいよいよ仮想マシンを作成します。VM は、これまでの手順で作成したすべてのリソースに直接または間接的に依存するため、VM をプロビジョニングする前に、上記のリソースすべてが利用できるようになるまで待つ必要があります。VM のプロビジョニングは、他のリソースの作成よりも時間がかかるため、アプリケーションは処理が完了するまで待機する必要があります。

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

### テンプレートのデプロイ
テンプレートをデプロイする詳しい手順については、[.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ](./virtual-machines/virtual-machines-windows-csharp-template.md)に関する記事を参照してください。

テンプレートのデプロイは、手作業でリソースをプロビジョニングするよりもはるかに簡単です。その方法を示したのが次のコードです。テンプレートとパラメーター ファイルが置かれている URI を指定しています。

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

<!---HONumber=AcomDC_0629_2016-->