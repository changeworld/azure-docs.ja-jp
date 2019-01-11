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
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 20e96ad7a99fdb8c90f3b7990965d7225aef8be0
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555015"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Azure Stack での .NET による API バージョンのプロファイルの使用

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack Resource Manager 向けの .NET SDK には､インフラストラクチャの構築と管理に役立つツールが用意されています。 SDK のリソース プロバイダーには、コンピューティング、ネットワーク、ストレージ、アプリ サービス、および [KeyVault](../../key-vault/key-vault-whatis.md) が含まれます。 .NET SDK には、14 個の NuGet パッケージが含まれています。 これらのパッケージは、プロファイル情報を組み込むたびにプロジェクト ソリューションにダウンロードする必要があります。 ただし、アプリケーションのメモリを最適化するために、2018-03-01-hybrid または 2017-03-09-profile に使用するリソース プロバイダーを特別にダウンロードすることができます。 各パッケージは、リソース プロバイダー、それぞれの API バージョン、それが属する API プロファイルで構成されます。 .NET SDK に含まれている API プロファイルを使用すると、グローバルな Azure リソースと Azure Stack 上のリソース間を切り替えることで、ハイブリッド クラウド開発を行うことができます。

## <a name="net-and-api-version-profiles"></a>.NET と API バージョンのプロファイル

API プロファイルは、リソース プロバイダーと API バージョンを組み合わせたものです。 API プロファイルを使用すると、リソース プロバイダーのパッケージに含まれる各リソースの種類の、最も安定した最新バージョンを取得できます。

-   すべてのサービスの最新バージョンを使用するには、パッケージの **latest** プロファイルを使用します。 このプロファイルは、**Microsoft.Azure.Management** NuGet パッケージの一部です。

-   Azure Stack と互換性のあるサービスを使用するには、**Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** または **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg** パッケージを使用します。

    -   各プロファイルのリソース プロバイダーごとに 2 つのパッケージがあります。

    -   上記の NuGet パッケージの **ResourceProvider** 部分が適切なプロバイダーに変更されていることを確認します。

-   サービスの最新の API バージョンを使用するには、特定の NuGet パッケージの **Latest** プロファイルを使用します。 たとえば、コンピュート サービスの **latest-API** バージョンを単独で使用する場合は、**compute** パッケージの **latest** プロファイルを使用します。 **latest** プロファイルは、**Microsoft.Azure.Management** NuGet パッケージの一部です。

-   特定のリソース プロバイダーのリソースの種類に合わせて特定の API バージョンを使用するには、パッケージ内で定義されている特定の API バージョンを使用します。

すべてのオプションを同じアプリケーション内で組み合わせることができます。

## <a name="install-the-azure-net-sdk"></a>Azure .NET SDK のインストール

1.  GIT をインストールします。 手順については、「[はじめに - Git のインストール][]」 (はじめに - Git をインストールする) をご覧ください。

2.  適切な NuGet パッケージをインストールするには、[パッケージの検索とインストール][]に関するページを参照してください。

3.  インストールする必要があるパッケージは、使用したいプロファイル バージョンによって異なります。 プロファイル バージョンのパッケージ名は次のとおりです。

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**

4.  Visual Studio Code 用の適切な NuGet パッケージをインストールするには、次のリンクを参照して [NuGet パッケージ マネージャーについての指示][]をダウンロードしてください。

5.  使用できない場合は、サブスクリプションを作成し、サブスクリプション ID を保存して後で使用します。 サブスクリプションの作成手順については、「[Azure Stack でオファーのサブスクリプションを作成する][]」をご覧ください。

6.  サービス プリンシパルを作成し、クライアント ID とクライアント シークレットを保存します。 Azure Stack 用のサービス プリンシパルの作成方法については、「[Azure Stack へのアクセスをアプリケーションに提供する][]」を参照してください。 クライアント ID は、サービス プリンシパルの作成時にはアプリケーション ID とも呼ばれます。

7.  サブスクリプションでサービス プリンシパルのロールが共同作成者/所有者であることを確認します。 サービス プリンシパルへのロールの割り当て手順については、「[Azure Stack へのアクセスをアプリケーションに提供する][]」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Stack で .NET Azure SDK を使用するには、次の値を指定した後、環境変数に値を設定する必要があります。 環境変数を設定するには、使用しているオペレーティング システムの表の後にある手順を参照してください。

| 値                     | 環境変数   | 説明                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| テナント ID                 | AZURE_TENANT_ID       | Azure Stack の[*テナント ID*][] の値。                                                                          |
| クライアント ID                 | AZURE_CLIENT_ID       | この記事の前のセクションでサービス プリンシパルが作成されたときに保存した、サービス プリンシパル アプリケーション ID。 |
| サブスクリプション ID           | AZURE_SUBSCRIPTION_ID | [*サブスクリプション ID*][] は Azure Stack 内のオファーにアクセスするために必要です。                                                      |
| クライアント シークレット             | AZURE_CLIENT_SECRET   | サービス プリンシパルの作成時に保存した、サービス プリンシパル アプリケーション シークレット。                                      |
| Resource Manager エンドポイント | ARM_ENDPOINT           | 「[*Azure Stack Resource Manager エンドポイント*][]」をご覧ください。                                                                    |
| 場所                  | RESOURCE_LOCATION     | Azure Stack の場所。

Azure Stack のテナント ID を確認するには、[こちら](../azure-stack-csp-ref-operations.md)の手順に従ってください。 環境変数を設定するには、次の手順に従います。

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

- 統合システムの **ResourceManagerUrl** は `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` になります。必要なメタデータを取得するには、`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` になります。

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**:Azure Stack 用に作成された最新のプロファイル。 1808 以降のスタンプを使用している限り、Azure Stack との互換性に優れたサービスには、このプロファイルを使用します。

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**:スタンプが 1808 ビルドよりも低い場合は、このプロファイルを使用します。

3.  **Latest**:すべてのサービスの最新バージョンで構成されているプロンプトファイル。 すべてのサービスの最新バージョンを使用してください。 このプロファイルは、**Microsoft.Azure.Management** NuGet パッケージの一部です。

Azure Stack および API プロファイルについて詳しくは、「[API プロファイルの概要][]」をご覧ください。

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API プロファイルの使用

リソース管理クライアントをインスタンス化するには、次のコードを使用する必要があります。 同様のコードを使用して、他のリソース プロバイダー (コンピューティング、ネットワーク、ストレージなど) のクライアントをインスタンス化することができます。 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

上記のコードの `credentials` パラメーターは、クライアントをインスタンス化するために必要です。 次のコードでは、テナント ID とサービス プリンシパルによって認証トークンを生成します。

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
コードの `getActiveDirectoryServiceSettings` 呼び出しは、メタデータ エンドポイントから Azure Stack エンドポイントを取得します。 これは、この呼び出しからの環境変数を示しています。 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
これにより、API プロファイルの NuGet パッケージを使用して、アプリケーションを Azure Stack に正常にデプロイすることができます。

## <a name="samples-using-api-profiles"></a>API プロファイルを使用したサンプル

次のサンプルは、.NET と Azure Stack API のプロファイルを使用してソリューションを作成するための参考資料として使用できます。
- [リソース グループの管理](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [ストレージ アカウントの管理](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [仮想マシンの管理](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

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
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
