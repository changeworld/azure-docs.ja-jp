---
title: Azure Data Explorer クラスターのマネージド ID の構成方法
description: Azure Data Explorer クラスターのマネージド ID を構成する方法について説明します。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529670"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターのマネージド ID の構成

[Azure Active Directory のマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview) を使用すると、クラスターは AAD で保護された他のリソース (Azure Key Vault など) に簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、シークレットをプロビジョニングまたはローテーションする必要はありません。 この記事では、Azure Data Explorer クラスターのマネージド ID を作成する方法について説明します。 マネージド ID の構成は、現在、[お使いのクラスターに対して、お客様が管理するキーを有効にする](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)場合にのみサポートされています。

> [!Note]
> Azure Data Explorer クラスターがサブスクリプションやテナント間で移行された場合、Azure Data Explorer のマネージド ID は想定されたとおりに動作しません。 アプリは、機能を[無効にして](#disable-a-system-assigned-identity)から[再度有効にする](#add-a-system-assigned-identity)ことで、新しい ID を取得する必要があります。 新しい ID を使用するには、ダウンストリーム リソースのアクセス ポリシーも更新する必要があります。

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する
                                                                                                    
クラスターに関連付けられており、クラスターが削除されると削除されるシステム割り当て ID を割り当てます。 クラスターは 1 つのシステム割り当て ID しか持つことはできません。 システム割り当て ID を持つクラスターを作成するには、クラスター上で追加のプロパティを設定する必要があります。 システム割り当て ID は、次に示すように、C#、ARM テンプレート、または Azure portal を使用して追加されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Azure portal を使用してシステム割り当て ID を追加する

1. [Azure portal](https://portal.azure.com/) にサインインします。

#### <a name="new-azure-data-explorer-cluster"></a>新しい Azure Data Explorer クラスター

1. [Azure Data Explorer クラスターを作成します](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. **[セキュリティ]** タブ > **[システム割り当て ID]** で、 **[オン]** を選択します。 システム割り当て ID を削除するには、 **[オフ]** を選択します。
2. **[次へ: タグ] >** または **[確認と作成]** を選択して、クラスターを作成します。

    ![システム割り当て ID を新しいクラスターに追加する](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>既存の Azure Data Explorer クラスター

1. 既存の Azure Data Explorer クラスターを開きます。
1. ポータルの左ペインで、 **[設定]**  >  **[ID]** を選択します。
1. **[ID]** ペイン > **[システム割り当て]** タブで、次のようにします。
   1. **[状態]** スライダーを **[オン]** に移動します。
   1. **[保存]** を選びます。
   1. ポップアップ ウィンドウで、 **[はい]** を選択します。

    ![システム割り当て ID を追加する](media/managed-identities/turn-system-assigned-identity-on.png)

1. 数分後、画面に以下が表示されます。 
  * **オブジェクト ID** - カスタマー マネージド キーに使用されます 
  * **ロールの割り当て** - リンクをクリックして、関連するロールを割り当てます

    ![システム割り当て ID がオン](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>C# を使用してシステム割り当て ID を追加する

#### <a name="prerequisites"></a>前提条件

Azure Data Explorer C# クライアントを使用してマネージド ID を設定するには、次のようにします。

* [Azure Data Explorer (Kusto) NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)をインストールします。
* 認証用に、[Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)をインストールします。
* リソースにアクセスできる [Azure AD アプリケーション](/azure/active-directory/develop/howto-create-service-principal-portal)とサービス プリンシパルを作成します。 サブスクリプションのスコープでロールの割り当てを追加し、必要な `Directory (tenant) ID`、`Application ID`、および `Client Secret` を取得します。

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

`PrincipalId` と `TenantId` は GUID で置き換えられます。 `TenantId` プロパティは、ID が属している AAD テナントを特定します。 `PrincipalId` は、クラスターの新しい ID の一意識別子です。 AAD でのサービス プリンシパルの名前は、App Service または Azure Functions のインスタンスに指定したものと同じです。

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm)

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

`<TENANTID>` と `<PRINCIPALID>` は GUID で置き換えられます。 `TenantId` プロパティは、ID が属している AAD テナントを特定します。 `PrincipalId` は、クラスターの新しい ID の一意識別子です。 AAD でのサービス プリンシパルの名前は、App Service または Azure Functions のインスタンスに指定したものと同じです。

---

## <a name="disable-a-system-assigned-identity"></a>システム割り当て ID を無効にする

システム割り当て ID を削除すると、AAD からも削除されます。 クラスター リソースが削除されると、システム割り当て ID も AAD から自動的に削除されます。 システム割り当て ID を削除するには、機能を無効にします。  システム割り当て ID は、次に示すように、C#、ARM テンプレート、または Azure portal を使用して削除されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Azure portal を使用してシステム割り当て ID を無効にする

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ポータルの左ペインで、 **[設定]**  >  **[ID]** を選択します。
1. **[ID]** ペイン > **[システム割り当て]** タブで、次のようにします。
    1. **[状態]** スライダーを **[オフ]** に移動します。
    1. **[保存]** を選びます。
    1. ポップアップ ウィンドウで **[はい]** を選択して、システム割り当て ID を無効にします。 **[ID]** ペインは、システム割り当て ID が追加される前と同じ状態に戻ります。

    ![システム割り当て ID がオフ](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>C# を使用してシステム割り当て ID を削除する

システム割り当て ID を削除するには、次を実行します。

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して、システム割り当て ID を削除する

システム割り当て ID を削除するには、次を実行します。

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>次のステップ

* [Azure で Azure Data Explorer クラスターをセキュリティで保護する](security.md)
* 保存時の暗号化を有効にすることで、[Azure Data Explorer のクラスターをセキュリティで保護する - Azure portal](manage-cluster-security.md)。
 * [C# を使用してカスタマー マネージド キーを構成する](customer-managed-keys-csharp.md)
 * [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](customer-managed-keys-resource-manager.md)
