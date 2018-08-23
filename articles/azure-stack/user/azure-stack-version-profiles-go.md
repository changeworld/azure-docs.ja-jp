---
title: Azure Stack での GO による API バージョンのプロファイルの使用 | Microsoft Docs
description: Azure Stack での GO による API バージョンのプロファイルの使用について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 9ad4402098e938f72cf4b8c61cce8d0d46b5a147
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41946413"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Azure Stack での GO による API バージョンのプロファイルの使用

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

## <a name="go-and-version-profiles"></a>GO と バージョン プロファイル

プロファイルとは、各種のサービスから異なるバージョンのさまざまなリソースの種類を組み合わせたものです。 プロファイルを使用すると、さまざまなリソースの種類を取り混ぜてマッチングできます。 プロファイルは、次の特性を提供できます。

 - 特定の API バージョンをロックすることによる、アプリケーションの安定性。
 - Azure Stack および地域の Azure データセンターでのアプリケーションの互換性。

Go SDK では、プロファイルは、**YYYY-MM-DD** 形式のバージョンと共に、profiles/ パスで使用可能です。 現在の最新の Azure Stack プロファイル バージョンは **2017-03-09** です。 指定されたサービスをプロファイルからインポートするには、プロファイルから該当するモジュールをインポートする必要があります。 たとえば、**2017-03-09** プロファイルから **Compute** サービスをインポートするには、次のように指定します。

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>GO 対応の Azure SDK をインストールする

  1. GIT をインストールします。 手順については、「[Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)」 (はじめに - Git をインストールする) をご覧ください。
  2. [Go プログラミング言語](https://golang.org/dl)をインストールします。  
  Azure の API プロファイルでは、Go のバージョン 1.9 以降が必要です。
  3. 次の bash コマンドを実行して、GO の Azure SDK とその依存関係をインストールします。
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>GO SDK

Azure での GO SDK の詳細については、以下で確認できます。
- 「[Azure SDK for Go のインストール](https://docs.microsoft.com/go/azure/azure-sdk-go-install)」にある Azure での Go SDK。
- Azure での Go SDK は、GitHub の [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) で一般利用可能。

### <a name="go-autorest-dependencies"></a>Go-AutoRest 依存関係

GO SDK は、Azure Go-AutoRest モジュールに依存し、Azure Resource Manager エンドポイントに REST 要求を送信します。 Azure Go-AutoRest モジュールの依存関係を [GitHub の Azure Go-AutoRest](https://github.com/Azure/go-autorest) からインポートする必要があります。 インストールの bash コマンドは、「**インストール**」セクションで検索できます。

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Azure Stack での GO SDK プロファイルの使用方法

Azure Stack でサンプルの Go コードを実行するには、次の手順に従います。
  1. Go 対応の Azure SDK とその依存関係をインストールします。 手順については、前のセクションの「[GO 対応の Azure SDK をインストールする](#install-azure-sdk-for-go)」をご覧ください。
  2. リソース マネージャー エンドポイントからメタデータ情報を取得します。 エンドポイントは、Go コードを実行するために必要な情報と共に、JSON ファイルを返します。

  > [!Note]  
  > Azure Stack Development Kit (ASDK) の **ResourceManagerUrl** は `https://management.local.azurestack.external/` になります。  
  > 統合システムの **ResourceManagerUrl** は `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` になります。  
  > 必要なメタデータを取得するには、`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` になります。
  
  サンプルの JSON ファイルは、次のようになります。

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. 使用できない場合は、サブスクリプションを作成し、サブスクリプション ID を保存して後で使用します。 サブスクリプションの作成方法に関する詳細については、「[Azure Stack でオファーのサブスクリプションを作成する](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)」をご覧ください。 
  4. "サブスクリプション" スコープと**所有者**ロールを使って、サービス プリンシパルを作成します。 サービス プリンシパルの ID とシークレットを保存します。 Azure Stack のサービス プリンシパルの作成に関する詳細については、[サービス プリンシパルの作成](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad)に関するページをご覧ください。 自分の Azure Stack 環境が設定されました。
  5. コード内で Go SDK プロファイルからサービス モジュールをインポートします。 Azure Stack プロファイルの現在のバージョンは **2017-03-09** です。 たとえば、**2017-03-09** プロファイルの種類からネットワーク モジュールをインポートするには、次のコードを記述します。 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. 関数内で、**New** クライアント関数呼び出しを使って、クライアントを作成して認証します。 仮想ネットワーク クライアントを作成するには、次のコードを使用できます。  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  `<baseURI>` に手順 2 で使用されている **ResourceManagerUrl** 値を設定します。
  `<subscriptionID>` に手順 3 で保存した **SubscriptionID** 値を設定します。
  トークンを作成するには、以下の「認証」セクションをご覧ください。  

  7. 前の手順で作成したクライアントを使用して、API のメソッドを呼び出します。 たとえば、前の手順のクライアントを使用して仮想ネットワークを作成するには、次のように記述します。 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Go SDK プロファイルを使用して Azure Stack に仮想ネットワークを作成する詳細な例については、「[例](#example)」をご覧ください。

## <a name="authentication"></a>Authentication

Go SDK を使って Azure Active Directory から Authorizer プロパティを取得するには、Go-AutoRest モジュールをインストールします。 これらのモジュールは、"Go SDK" のインストールで既にインストールされています。インストールされていない場合は、[GitHub の認証パッケージ](https://github.com/Azure/go-autorest/tree/master/autorest/adal)をインストールしてください。

Authorizer には、リソース クライアントの承認者を設定する必要があります。 Authorizer を取得する方法は複数あります。詳細な一覧が提供されています。

このセクションでは、クライアント資格情報を使用して Azure Stack に authorizer トークンを取得する一般的な方法を示します。

  1. サブスクリプションの所有者ロールを備えたサービス プリンシパルが使用可能な場合は、この手順を省略します。 それ以外の場合は、サービス プリンシパルの[指示]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)を作成し、サブスクリプションの[指示]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)にスコープ設定した "所有者" ロールを割り当てます。 サービス プリンシパルのアプリケーション ID とシークレットを保存します。 

  2. コード内で Go-AutoRest から **adal**パッケージをインポートします。 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. **adal** モジュールから NewOAuthConfig メソッドを使用して、oauthConfig を作成します。 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  このドキュメントの前のセクションで取得した ResourceManagerUrl メタデータの "loginEndpoint" プロパティの値を `<activeDirectoryEndpoint>` に 設定します。
  `<tenantID>` 値にお使いの Azure Stack テナント ID を設定します。 

  4. 最後に、adal モジュールの NewServicePrincipalToken メソッドを使用して、サービス プリンシパルのトークンを作成します。 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  このドキュメントの前のセクションで取得した ResourceManagerUrl メタデータの "audience" リストのいずれかの値を `<activeDirectoryResourceID>` に設定します。  
  このドキュメントの前のセクションでサービス プリンシパルが作成されたときに保存したサービス プリンシパル アプリケーション ID を `<clientID>` に設定します。  
  このドキュメントの前のセクションでサービス プリンシパルが作成されたときに保存したサービス プリンシパル アプリケーション シークレット を `<clientSecret>` に設定します。  

## <a name="example"></a>例

このセクションでは、Azure Stack で仮想ネットワークを作成するための Go コードのサンプルを示します。 Go SDK の詳細な例については、[Azure Go SDk サンプル リポジトリ](https://github.com/Azure-Samples/azure-sdk-for-go-samples)に関するページをご覧ください。 Azure Stack サンプルは、リポジトリのサービス ホルダー内にある hybrid/ パスで使用可能です。

> [!Note]  
> この例のコードを実行するには、使用されるサブスクリプションが、**ネットワーク** リソース プロバイダーの一覧に**登録済み**として示されていることを確認します。 これを確認するには、Azure Stack ポータルでサブスクリプションを検索し、**リソース プロバイダー**をクリックします。

1. コード内で必要なパッケージをインポートします。 ネットワーク モジュールをインポートするには、Azure Stack で使用可能な最新のプロファイルを使用する必要があります。 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. 使用する環境変数を定義します。 仮想ネットワークを作成するには、リソース グループを保持している必要があります。 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. 使用する環境変数を定義したので、**adal** パッケージを使用して、認証トークンを作成するためのメソッドを追加します。 詳細については、前のセクションの「認証」をご覧ください。
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. main メソッドを追加します。 main メソッドでは最初に、前の手順で定義されたメソッドを使用してトークンを取得します。 次に、プロファイルのネットワーク モジュールを使用して、クライアントを作成します。 最後に、仮想ネットワークを作成します。 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>次の手順
* [PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)
* [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)  
