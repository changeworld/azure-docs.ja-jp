---
title: Azure Stack での .NET SDK による API バージョンのプロファイルの使用 | Microsoft Docs
description: Azure Stack での .NET による API バージョンのプロファイルの使用について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 35329468ee01d5b70d654c1eb4a908db9d3fcb5d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184404"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Azure Stack での .NET による API バージョンのプロファイルの使用

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack Resource Manager 向けの .NET SDK には､インフラストラクチャの構築と管理に役立つツールが用意されています。 SDK のリソース プロバイダーには、コンピューティング、ネットワーク、ストレージ、アプリ サービス、および [KeyVault](../../key-vault/key-vault-whatis.md) が含まれます。 .NET SDK には 14 の NuGet パッケージが含まれていて、プロファイル情報を組み込むたびにプロジェクト ソリューションにダウンロードする必要があります。 ただし、アプリケーションのメモリを最適化するために、2018-03-01-hybrid または 2017-03-09-profile に使用するリソース プロバイダーを特別にダウンロードすることができます。 各パッケージは、リソース プロバイダー、それぞれの API バージョン、それが属する API プロファイルで構成されます。 .NET SDK に含まれている API プロファイルを使用すると、グローバルな Azure リソースと Azure Stack 上のリソース間を切り替えることで、ハイブリッド クラウド開発を行うことができます。

## <a name="net-and-api-version-profiles"></a>.NET と API バージョンのプロファイル

API プロファイルは、リソース プロバイダーと API バージョンを組み合わせたものです。 API プロファイルを使用すると、リソース プロバイダーのパッケージに含まれる各リソースの種類の、最も安定した最新バージョンを取得できます。

-   すべてのサービスの最新バージョンを使用するには、パッケージの **latest** プロファイルを使用します。 このプロファイルは、**Microsoft.Azure.Management** NuGet パッケージの一部です。

-   Azure Stack と互換性のあるサービスを使用するには、**Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** または **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg** パッケージを使用します。

    -   各プロファイルのリソース プロバイダーごとに 2 つのパッケージがあります。

    -   上記の NuGet パッケージの **ResourceProvider** 部分が適切なプロバイダーに変更されていることを確認します。

-   サービスの最新の API バージョンを使用するには、特定の NuGet パッケージの **Latest** プロファイルを使用します。 たとえば、コンピュート サービスの **latest-API** バージョンを単独で使用する場合は、**compute** パッケージの **latest** プロファイルを使用します。 **latest** プロファイルは、**Microsoft.Azure.Management** NuGet パッケージの一部です。

-   特定のリソース プロバイダーのリソースの種類に合わせて特定の API バージョンを使用するには、パッケージ内で定義されている特定の API バージョンを使用します。

すべてのオプションを同じアプリケーション内で組み合わせることができることに注意してください。

## <a name="install-the-azure-net-sdk"></a>Azure .NET SDK のインストール

1.  GIT をインストールします。 手順については、「[はじめに - Git のインストール][]」 (はじめに - Git をインストールする) をご覧ください。

2.  適切な NuGet パッケージをインストールするには、[パッケージの検索とインストール][]に関するページを参照してください。

3.  インストールする必要があるパッケージは、使用したいプロファイル バージョンによって異なります。 プロファイル バージョンのパッケージ名は次のとおりです。

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**

4.  Visual Studio Code 用の適切な NuGet パッケージをインストールするには、次のリンクを参照して [NuGet パッケージ マネージャーについての指示][]をダウンロードしてください。

5.  使用できない場合は、サブスクリプションを作成し、サブスクリプション ID を保存して後で使用します。 サブスクリプションの作成手順については、「[Azure Stack でオファーのサブスクリプションを作成する][]」をご覧ください。

6.  サービス プリンシパルを作成し、クライアント ID とクライアント シークレットを保存します。 Azure Stack 用のサービス プリンシパルの作成方法については、「[Azure Stack へのアクセスをアプリケーションに提供する][]」を参照してください。 クライアント ID は、サービス プリンシパルの作成時にはアプリケーション ID とも呼ばれることに注意してください。

7.  サブスクリプションでサービス プリンシパルのロールが共同作成者/所有者であることを確認します。 サービス プリンシパルへのロールの割り当て手順については、「[Azure Stack へのアクセスをアプリケーションに提供する][]」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Stack で .NET Azure SDK を使用するには、次の値を指定した後、環境変数に値を設定する必要があります。 環境変数を設定するには、使用しているオペレーティングシステムの表の後にある手順を参照してください。

| 値                     | 環境変数   | 説明                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| テナント ID                 | AZURE_TENANT_ID       | Azure Stack の[*テナント ID*][] の値。                                                                          |
| クライアント ID                 | AZURE_CLIENT_ID       | この記事の前のセクションでサービス プリンシパルが作成されたときに保存した、サービス プリンシパル アプリケーション ID。 |
| サブスクリプション ID           | AZURE_SUBSCRIPTION_ID | [*サブスクリプション ID*][] は Azure Stack 内のオファーにアクセスするために必要です。                                                      |
| クライアント シークレット             | AZURE_CLIENT_SECRET   | サービス プリンシパルの作成時に保存した、サービス プリンシパル アプリケーション シークレット。                                      |
| Resource Manager エンドポイント | ARM_ENDPOINT           | 「[*Azure Stack Resource Manager エンドポイント*][]」をご覧ください。                                                                    |

Azure Stack のテナント ID を確認するには、[こちら](../azure-stack-csp-ref-operations.md)の手順に従ってください。 環境変数を設定するには、次の操作を実行します。

### <a name="microsoft-windows"></a>Microsoft Windows

環境変数を設定するには、Windows コマンド プロンプトで次の形式を使用します。

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS、Linux、および Unix ベースのシステム

Unix ベースのシステムでは、次のコマンドを使用できます。

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack Resource Manager エンドポイント

Microsoft Azure Resource Manager は、管理者が Azure リソースのデプロイ、管理、監視を行えるようにするための管理フレームワークです。 Azure Resource Manager では、これらのタスクを個別に処理するのではなく、グループとして単一の操作で処理することができます。

メタデータ情報は、Resource Manager エンドポイントから取得できます。 エンドポイントは、コードを実行するために必要な情報と共に、JSON ファイルを返します。

次の考慮事項に注意してください。

- Azure Stack Development Kit (ASDK) の **ResourceManagerUrl** は https://management.local.azurestack.external/ になります。

- 統合システムの **ResourceManagerUrl** は、次のようになります。`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` 必要なメタデータを取得するには、次のように指定します。`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

サンプルの JSON ファイルは、次のようになります。

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>既存の API プロファイル

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**: Azure Stack 用に作成された最新のプロファイル。 1808 以降のスタンプを使用している限り、Azure Stack との互換性に優れたサービスには、このプロファイルを使用します。

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**: スタンプが 1808 ビルドより小さい場合は、このプロファイルを使用します。

3.  **Latest**: すべてのサービスの最新バージョンで構成されているプロンプトファイル。 すべてのサービスの最新バージョンを使用してください。 このプロファイルは、**Microsoft.Azure.Management** NuGet パッケージの一部です。

Azure Stack および API プロファイルについて詳しくは、「[API プロファイルの概要][]」をご覧ください。

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API プロファイルの使用

プロファイル クライアントをインスタンス化するには、次のコードを使用します。 このパラメーターは、Azure Stack またはまたはその他のプライベート クラウドにのみ必要です。 グローバル Azure では、既定でこれらの設定が使用されます。

Azure Stack でサービス プリンシパルを認証するには、次のコードが必要です。 これにより、テナント ID と、Azure Stack に固有の認証基準によって、トークンが作成されます。

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

これにより、API プロファイルの NuGet パッケージを使用して、アプリケーションを Azure Stack に正常にデプロイすることができます。

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack 環境設定関数の定義

サービス プリンシパルを Azure Stack 環境に対して認証するには、次のコードを使用してください。

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

これにより、Azure Stack に対して認証するために初期化サービス クライアントがオーバーライドされます。

## <a name="samples-using-api-profiles"></a>API プロファイルを使用したサンプル

.NET と Azure Stack API プロファイルを使用してソリューションを作成する場合は、GitHub リポジトリにある次のサンプルを参考資料として使用できます。

-   [仮想マシン、vNet、リソース グループ、およびストレージ アカウントに対するテスト プロジェクト][]
-   .NET を使用した仮想マシンの管理

### <a name="sample-unit-test-project"></a>単体テスト プロジェクトのサンプル 

1.  次のコマンドを使用して、リポジトリを複製します。

    `git clone <https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject>`

2.  Azure サービス プリンシパルを作成し、サブスクリプションにアクセスするロールを割り当てます。 サービス プリンシパルの作成方法については、「[Azure PowerShell を使用して資格情報でのサービス プリンシパルを作成する][]」をご覧ください。

3.  次の必要な値を取得します。

    1.  テナント ID
    2.  クライアント ID
    3.  クライアント シークレット
    4.  サブスクリプション ID
    5.  Resource Manager エンドポイント

4.  コマンド プロンプトを使用して作成したサービス プリンシパルから取得した情報を使用して、次の環境変数を設定します。

    1.  export AZURE_TENANT_ID={ご使用のテナント ID}
    2.  export AZURE_CLIENT_ID={ご使用のクライアント ID}
    3.  export AZURE_CLIENT_SECRET={ご使用のクライアント シークレット}
    4.  export AZURE_SUBSCRIPTION_ID={ご使用のサブスクリプション ID}
    5.  export ARM_ENDPOINT={ご使用の Azure Stack Resource Manager URL}

   Windows では、**export** ではなく **set** を使用します。

5.  location 変数が Azure Stack の場所に設定されていることを確認します。 例: LOCAL = "local"。

6.  Azure Stack に対して認証できるようにするカスタム ログイン資格情報を設定します。 コードのこの部分は、[承認] フォルダーにあるこのサンプルに含まれていることに注意してください。

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Azure Stack に対して認証するために、Azure Stack を使用して初期化サービス クライアントをオーバーライドする場合は、次のコードを追加します。 このコードの一部は、[承認] フォルダーにあるこのサンプルに既に含まれていることに注意してください。

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  NuGet パッケージ マネージャーを使用して、「2018-03-01-hybrid」を検索し、コンピューティング、ネットワーク、ストレージ、KeyVault、App Services のリソース プロバイダー用の、このプロファイルに関連付けられているパッケージをインストールします。

2.  .cs ファイルの各タスク内で、Azure Stack を操作するために必要なパラメーターを設定します。 タスク `CreateResourceGroupTest` の例を次に示します。

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  各タスクを右クリックし、**[テストの実行]** を選択します。

    1.  作業ウィンドウの緑色のチェック マークは、各タスクが指定されたパラメーターに基づいて正常に作成されたことを通知します。 Azure Stack サブスクリプションをチェックして、リソースが正常に作成されたことを確認してください。

    2.  単体テストの実行方法の詳細については、「[テスト エクスプローラーを使用して単体テストを実行する][]」を参照してください。

## <a name="next-steps"></a>次の手順

API プロファイルの詳細については、以下を参照してください。

- [Azure Stack での API バージョン プロファイルの管理](azure-stack-version-profiles.md)
- [プロファイルでサポートされているリソース プロバイダー API バージョン](azure-stack-profiles-azure-resource-manager-versions.md)

  [はじめに - Git のインストール]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [パッケージの検索とインストール]: /nuget/tools/package-manager-ui
  [NuGet パッケージ マネージャーについての指示]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Azure Stack でオファーのサブスクリプションを作成する]: ../azure-stack-subscribe-plan-provision-vm.md
  [Azure Stack へのアクセスをアプリケーションに提供する]: ../azure-stack-create-service-principals.md
  [*テナント ID*]: ../azure-stack-identity-overview.md
  [*サブスクリプション ID*]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [*Azure Stack Resource Manager エンドポイント*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [API プロファイルの概要]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [仮想マシン、vNet、リソース グループ、およびストレージ アカウントに対するテスト プロジェクト]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Azure PowerShell を使用して資格情報でのサービス プリンシパルを作成する]: ../azure-stack-create-service-principals.md
  [テスト エクスプローラーを使用して単体テストを実行する]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017。
