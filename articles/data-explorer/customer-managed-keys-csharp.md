---
title: C# を使用してカスタマー マネージド キーを構成する
description: この記事では、Azure Data Explorer でデータに対するカスタマー マネージド キーの暗号化を構成する方法について説明します。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7987555dfd435aae1f580c73795a5617c192490a
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722120"
---
# <a name="configure-customer-managed-keys-using-c"></a>C# を使用してカスタマー マネージド キーを構成する

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Azure Resource Manager テンプレート](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>カスタマー マネージド キーによる暗号化を構成する

このセクションでは、Azure Data Explorer C# クライアントを使用して、カスタマー マネージド キーの暗号化を構成する方法について説明します。 

### <a name="prerequisites"></a>前提条件

* Visual Studio 2019 をインストールしていない場合は、**無料**の [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

### <a name="install-c-nuget"></a>C# Nuget をインストールする

* [Azure Data Explorer (Kusto) NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)をインストールします。

* 認証用に [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)をインストールします。

### <a name="authentication"></a>認証

この記事の例を実行するには、リソースにアクセスできる [Azure AD アプリケーション](/azure/active-directory/develop/howto-create-service-principal-portal)とサービス プリンシパルを作成する必要があります。 サブスクリプションのスコープでロールの割り当てを追加して、必要な `Directory (tenant) ID`、`Application ID`、および `Client Secret` を取得できます。

### <a name="configure-cluster"></a>クラスターの構成

既定では、Azure Data Explorer の暗号化では Microsoft のマネージド キーが使用されます。 カスタマー マネージド キーを使用するように Azure Data Explorer クラスターを構成し、そのクラスターに関連付けるキーを指定します。

1. 次のコードを使用してクラスターを更新します。

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. 次のコマンドを実行して、クラスターが正常に更新されたかどうかを確認します。

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    結果に値が `Succeeded` の `ProvisioningState` が含まれている場合、クラスターは正常に更新されています。

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、新しいバージョンを使用するようにクラスターを更新する必要があります。 まず `Get-AzKeyVaultKey` を呼び出し、キーの最新バージョンを取得します。 次に、「[クラスターの構成](#configure-cluster)」に示されているように、新しいバージョンのキーを使用するようにクラスターのキー コンテナーのプロパティを更新します。

## <a name="next-steps"></a>次のステップ

* [Azure で Azure Data Explorer クラスターをセキュリティで保護する](security.md)
* [Azure Data Explorer クラスターのマネージド ID を構成する](managed-identities.md)
* 保存時の暗号化を有効にすることで、[Azure Data Explorer のクラスターをセキュリティで保護する - Azure portal](manage-cluster-security.md)。
* [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](customer-managed-keys-resource-manager.md)


