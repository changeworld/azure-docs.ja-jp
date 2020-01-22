---
title: Azure Data Explorer クラスターのマネージド ID の構成方法
description: Azure Data Explorer クラスターのマネージド ID を構成する方法について説明します。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3b0bb62de4a96a45d607e05b32a87feec692e4d4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722204"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターのマネージド ID の構成

[Azure Active Directory のマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview) を使用すると、クラスターは AAD で保護された他のリソース (Azure Key Vault など) に簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、シークレットをプロビジョニングまたはローテーションする必要はありません。 この記事では、Azure Data Explorer クラスターのマネージド ID を作成する方法について説明します。 

> [!Note]
> アプリがサブスクリプションやテナント間で移行された場合、Azure Data Explorer のマネージド ID は想定されたとおりに動作しません。 アプリは新しい ID を取得する必要があります。これは、[ID の削除](#remove-an-identity)を使用して機能を無効にしてから再度有効にすることで実行できます。 新しい ID を使用するには、ダウンストリーム リソースのアクセス ポリシーも更新する必要があります。

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する

クラスターに関連付けられている**システム割り当て ID** をクラスターに割り当てることができ、それはクラスターが削除されると削除されます。 クラスターは 1 つのシステム割り当て ID しか持つことはできません。 システム割り当て ID を持つクラスターを作成するには、クラスター上で追加のプロパティを設定する必要があります。

### <a name="add-a-system-assigned-identity-using-c"></a>C# を使用してシステム割り当て ID を追加する

Azure Data Explorer C# クライアントを使用してマネージド ID を設定するには、次の手順を実行します。

* [Azure Data Explorer (Kusto) NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)をインストールします。
* 認証用に、[Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)をインストールします。
* 次の例を実行するには、[Azure AD アプリケーションを作成し](/azure/active-directory/develop/howto-create-service-principal-portal)、リソースにアクセスできるサービス プリンシパルも作成します。 サブスクリプションのスコープでロールの割り当てを追加して、必要な `Directory (tenant) ID`、`Application ID`、および `Client Secret` を取得できます。

#### <a name="create-or-update-your-cluster"></a>クラスターを作成または更新します

1. `Identity` プロパティを使用してクラスターを作成または更新します。

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
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. 次のコマンドを実行して、クラスターが ID とともに正常に作成または更新されたかどうかを確認します。

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    結果の `ProvisioningState` の値が `Succeeded` であれば、クラスターが作成または更新されたので、次のプロパティを持つはずです。
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` と `TenantId` は GUID で置き換えられます。 `TenantId` プロパティは、ID が属している AAD テナントを特定します。 `PrincipalId` は、クラスターの新しい ID の一意の識別子です。 AAD でのサービス プリンシパルの名前は、App Service または Azure Functions のインスタンスに指定したものと同じです。

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して、システム割り当て ID を追加する

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 Azure Data Explorer へのデプロイの詳細については、「[Azure Resource Manager テンプレートを使用して Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-resource-manager.md)」を参照してください。

システム割り当てタイプを追加することで、Azure に対してクラスターの ID を作成して管理するように指示します。 種類が `Microsoft.Kusto/clusters` であるすべてのリソースは、リソース定義に次のプロパティを含めることにより、ID を使って作成できます。 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

次に例を示します。

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

クラスターが作成されると、次の追加プロパティを持ちます。

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` と `<PRINCIPALID>` は GUID で置き換えられます。 `TenantId` プロパティは、ID が属している AAD テナントを特定します。 `PrincipalId` は、クラスターの新しい ID の一意の識別子です。 AAD でのサービス プリンシパルの名前は、App Service または Azure Functions のインスタンスに指定したものと同じです。

## <a name="remove-an-identity"></a>ID を削除する

システム割り当て ID を削除すると、AAD からも削除されます。 クラスター リソースが削除されると、システム割り当て ID も AAD から自動的に削除されます。 システム割り当て ID を削除するには、機能を無効にします。

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>次のステップ

* [Azure で Azure Data Explorer クラスターをセキュリティで保護する](security.md)
* 保存時の暗号化を有効にすることで、[Azure Data Explorer のクラスターをセキュリティで保護する - Azure portal](manage-cluster-security.md)。
 * [C# を使用してカスタマー マネージド キーを構成する](customer-managed-keys-csharp.md)
 * [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](customer-managed-keys-resource-manager.md)
