---
title: 仮想マシンのマネージド ID を使用して Cosmos DB にアクセスする | Microsoft Docs
description: Azure portal、CLI、PowerShell、Azure Resource Manager テンプレートを使用して、Windows VM でマネージド ID を使用する方法について説明します
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: msi
ms.workload: integration
ms.topic: tutorial
ms.date: 10/14/2021
ms.author: barclayn
ms.custom: ep-miar
ms.openlocfilehash: 7ff0ae1578a8b1d637f7baee50ed6d71a63e3996
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070111"
---
# <a name="how-to-use-managed-identities-to-connect-to-cosmos-db-from-an-azure-virtual-machine"></a>マネージド ID を使用して Azure 仮想マシンから Cosmos DB に接続する方法

この記事では、マネージド ID を使用して Cosmos に接続するよう仮想マシンを設定します。 [Azure Cosmos DB](../../cosmos-db/introduction.md) は、最新のアプリ開発に対応するフル マネージドの NoSQL データベースです。 [Azure リソースのマネージド ID](overview.md) を使用すると、Azure によって管理されている ID を使用して Azure AD 認証をサポートするサービスにアクセスするときに、アプリケーションを認証できます。

## <a name="prerequisites"></a>前提条件

- マネージド ID の基礎知識。 続行する前に Azure リソースのマネージド ID について詳しく学習する場合は、マネージド ID の[概要](overview.md)を確認してください。
- アクティブなサブスクリプションを含む Azure アカウントが必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。
- [PowerShell](/powershell/azure/new-azureps-module-az) または [CLI](/cli/azure/install-azure-cli) のいずれかが必要になる場合があります。
- [Visual Studio Community エディション](https://visualstudio.microsoft.com/vs/community/)、または任意のその他の開発環境。 

## <a name="create-a-resource-group"></a>リソース グループを作成する

**mi-test** という名前のリソース グループを作成します。 このチュートリアルで使用されるすべてのリソースについて、このリソース グループを使用します。

- [Azure portal を使用してリソース グループを作成する](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)
- [CLI を使用してリソース グループを作成する](../../azure-resource-manager/management/manage-resource-groups-cli.md#create-resource-groups)
- [PowerShell を使用してリソース グループを作成する](../../azure-resource-manager/management/manage-resource-groups-powershell.md#create-resource-groups)

## <a name="create-an-azure-vm-with-a-managed-identity"></a>マネージド ID を使用して Azure VM を作成する

このチュートリアルでは、Azure 仮想マシン (VM) が必要です。 システム割り当てマネージド ID を有効にして **mi-vm-01** という名前の仮想マシンを作成します。  また、以前作成したリソース グループ (**mi-test**) に、**mi-ua-01** という名前の[ユーザー割り当てマネージド ID を作成する](how-manage-user-assigned-managed-identities.md)こともできます。 ユーザー割り当てマネージド ID を使用する場合は、作成時に VM に割り当てることができます。

### <a name="create-a-vm-with-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用して VM を作成する

システム割り当てマネージド ID を有効にして Azure VM を作成するには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD の他のロールの割り当ては不要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

- **Azure portal** から、 **[仮想マシン]** を検索します。
- **[作成]** を選択します。
- [基本] タブで、必要な情報を指定します。
- **[Next: Disks>]\(次へ: ディスク>\)** を選択します。
- 必要に応じて情報の入力を続行し、 **[管理]** タブで **[ID]** セクションを見つけ、 **[システム割り当てマネージド ID]** の横のボックスをオンにします。

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/create-vm-system-assigned-managed-identities.png" alt-text="VM の作成時にシステム割り当てマネージド ID を有効にする方法を示す画像。":::

詳細については、Azure 仮想マシンのドキュメントを参照してください。

- [Linux](../../virtual-machines/linux/quick-create-portal.md)
- [Windows](../../virtual-machines/windows/quick-create-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AZVM](/powershell/module/az.compute/new-azvm) で、参照するリソースが作成されます (存在しない場合)。 システム割り当てマネージド ID を有効にして VM を作成するには、以下に示すようにパラメーター **-SystemAssignedIdentity** を渡します。 


```powershell

New-AzVm `
    -ResourceGroupName "My VM" `
    -Name "My resource group" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -SystemAssignedIdentity
    -OpenPorts 80,3389
```

- [クイック スタート:PowerShell を使用して Azure に Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md)
- [クイック スタート:PowerShell を使用して Azure に Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az vm create](/cli/azure/vm/#az_vm_create) を使用して VM を作成します。 次の例では、`--assign-identity` パラメーターの要求どおりに、システム割り当てマネージド ID を持つ *myVM* という名前の VM を作成します。 `--admin-username` および `--admin-password` パラメーターは、仮想マシンのサインイン用の管理ユーザー名とパスワードを指定します。 これらの値は、お使いの環境に合わせて更新してください。 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

- [システム割り当てマネージド ID を使用して Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-cli.md)
- [システム割り当てマネージド ID を使用して Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-cli.md)

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

システム割り当てマネージド ID を有効にするには、テンプレートをエディターに読み込み、`resources` セクション内で対象の `Microsoft.Compute/virtualMachines` リソースを探し、`"type": "Microsoft.Compute/virtualMachines"` プロパティと同じレベルに `"identity"` プロパティを追加します。 次の構文を使用します。

   ```json
   "identity": {
       "type": "SystemAssigned"
   },
   ```

完了すると、テンプレートの `resource` セクションに次のセクションが追加されます。 テンプレートは、次に示す例のようになります。

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('myVM')]",
            "location": "[myResourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

---


### <a name="create-a-vm-with-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を使用して VM を作成する

以下の手順では、ユーザー割り当てマネージド ID が構成された仮想マシンを作成する方法を示します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

現在 Azure portal では、VM 作成中のユーザー割り当てマネージド ID の割り当てはサポートされていません。 仮想マシンを作成した後で、ユーザー割り当てマネージド ID をそれに割り当てる必要があります。

[Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md#user-assigned-managed-identity)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ユーザー割り当てマネージド ID を指定して Windows 仮想マシンを作成する。

```powershell
New-AzVm `
    -ResourceGroupName "<Your resource group>" `
    -Name "<Your VM name>" `
    -Location "East US" `
    -VirtualNetworkName "<myVnet>" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 80,3389

```

ユーザー割り当てマネージド ID を指定して Linux 仮想マシンを作成する。

```powershell
New-AzVm `
    -Name "<Linux VM name>" `
    -image CentOS
    -ResourceGroupName "<Your resource group>" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -Linux `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 22


```

ユーザー割り当てマネージド ID は、その [resourceID](how-manage-user-assigned-managed-identities.md
) を使用して指定する必要があります。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az vm create --resource-group <MyResourceGroup> --name <myVM> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
```

[Azure CLI を使用して Azure VM で Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

API のバージョンに応じて、[異なる手順](qs-configure-template-windows-vm.md#user-assigned-managed-identity)を実行する必要があります。 apiVersion が 2018-06-01 の場合、ユーザー割り当てマネージド ID は userAssignedIdentities ディクショナリ形式で格納され、```<identityName>``` 値はテンプレートの variables セクションで定義する変数の名前です。 変数で、割り当てるユーザー割り当てマネージド ID を指定します。

```json
    "variables": {
     "identityName": "my-user-assigned" 
        
    },
```

ユーザー割り当てマネージド ID を VM に割り当てるには、resources 要素に次のエントリを追加します。 ```<identityName>``` は、作成したユーザー割り当てマネージド ID の名前に置き換えてください。

```json

"resources": [
     {
         //other resource provider properties...
         "apiVersion": "2018-06-01",
         "type": "Microsoft.Compute/virtualMachines",
         "name": "[variables('vmName')]",
         "location": "[resourceGroup().location]",
         "identity": {
             "type": "userAssigned",
             "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<identityName>'))]": {}
             }
         }
     }
 ]
```

---

## <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する

これで、ユーザー割り当てマネージド ID またはシステム割り当てマネージド ID を使用して VM が作成されたので、管理者権限を持つ Cosmos DB アカウントを使用できるようにする必要があります。 このチュートリアル用に Cosmos DB アカウントを作成する必要がある場合、その方法の詳細な手順については [Cosmos DB クイック スタート](../..//cosmos-db/sql/create-cosmosdb-resources-portal.md)を参照してください。

>[!NOTE]
> マネージド ID は、Azure Active Directory 認証をサポートするあらゆる Azure リソースにアクセスするために使用できます。 このチュートリアルでは、Cosmos DB アカウントが次に示すように構成されていることを前提とします。

 |設定|値|説明 |
   |---|---|---|
   |サブスクリプション|サブスクリプション名|この Azure Cosmos アカウントに使用する Azure サブスクリプションを選択します。 |
   |リソース グループ|リソース グループ名|**mi-test** を選択するか、 **[新規作成]** を選択し、新しいリソース グループの一意の名前を入力します。 |
   |アカウント名|一意の名前|自分の Azure Cosmos アカウントを識別するための名前を入力します。 指定した名前に *documents.azure.com* が付加されて URI が作成されるので、一意の名前を使用してください。<br><br>名前に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 長さは 3 文字から 44 文字でなければなりません。|
   |API|作成するアカウントの種類。|ドキュメント データベースを作成し、SQL 構文を使用してクエリを実行するには、 **[コア (SQL)]** を選択します。 <br><br>[SQL API について詳しくは、こちらをご覧ください](../../cosmos-db/introduction.md)。|
   |場所|ユーザーに最も近いリージョン|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできるよう、お客様のユーザーに最も近い場所を使用します。|

   > [!NOTE]
   > テストを行う場合は、Azure Cosmos DB の Free レベル割引を適用することもできます。 Azure Cosmos DB Free レベルのアカウントでは、最初の 1000 RU/s と 25 GB のストレージを無料でご利用いただけます。 [Free レベル](https://azure.microsoft.com/pricing/details/cosmos-db/)の詳細を確認してください。 ただし、このチュートリアルでは、この選択によって違いが生じることはありません。

## <a name="grant-access"></a>アクセス権の付与

この時点で、マネージド ID を使用して構成された仮想マシンと、Cosmos DB アカウントが両方存在するはずです。 続行する前に、マネージド ID にいくつかの異なるロールを付与する必要があります。

- 最初に、[Azure RBAC](../../cosmos-db/role-based-access-control.md) を使用して、Cosmos 管理プレーンへのアクセス権を付与します。 マネージド ID には、データベースとコンテナーを作成するために DocumentDB アカウント共同作成者ロールが割り当てられている必要があります。

- また、[Cosmos RBAC](../../cosmos-db/how-to-setup-rbac.md) を使用して、マネージド ID に共同作成者ロールを付与する必要もあります。 具体的な手順は下で確認できます。 

> [!NOTE] 
> **Cosmos DB 組み込みデータ共同作成者** ロールを使用します。 アクセス権を付与するには、ロール定義を ID に関連付ける必要があります。 この例では、マネージド ID は仮想マシンに関連付けられます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

**現時点では、Azure portal ではロールの割り当てオプションを利用できません**


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>" 
$contributorRoleDefinitionId = "00000000-0000-0000-0000-000000000002" # This is the ID of the Cosmos DB Built-in Data contributor role definition
$principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $contributorRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

ロールの割り当て手順が完了すると、次のような結果が表示されます。

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/results-role-assignment.png" alt-text="ロールの割り当ての結果が表示されます。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '00000000-0000-0000-0000-000000000002' # This is the ID of the Cosmos DB Built-in Data contributor role definition
principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId

```

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

```JSON
{
  "id": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleAssignments/00000000-0000-0000-0000-000000000002",
  "name": "myRoleAssignmentId",
  "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments",
  "properties": {
    "roleDefinitionId": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleDefinitions/00000000-0000-0000-0000-000000000002",
    "scope": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/dbs/purchases/colls/redmond-purchases",
    "principalId": "myPrincipalId"
  }
}

```

---

## <a name="access-data"></a>データにアクセスする

マネージド ID を使用して Cosmos にアクセスすることは、Azure.identity ライブラリを使用してアプリケーションで認証を有効にすることによって実現できます。 [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) を直接呼び出すか、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) を使用することができます。

ManagedIdentityCredential クラスでは、デプロイ環境に割り当てられたマネージド ID を使用して認証が試行されます。 [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme) では、様々な認証オプションが順番に実行されます。 DefaultAzureCredential が試行する 2 番目の認証オプションが、マネージド ID です。 

下の例では、データベース、コンテナー、コンテナー内の項目を作成し、仮想マシンのシステム割り当てマネージド ID を使用して、新しく作成された項目を読み戻します。 ユーザー割り当てマネージド ID を使用する場合は、マネージド ID のクライアント ID を指定して、ユーザー割り当てマネージド ID を指定する必要があります。 

```csharp
string userAssignedClientId = "<your managed identity client Id>";
var tokenCredential = new DefaultAzureCredential(new DefaultAzureCredentialOptions { ManagedIdentityClientId = userAssignedClientId });
```

下のサンプルを使用するには、次の NuGet パッケージが必要です。

- Azure.Identity
- Microsoft.Azure.Cosmos
- Microsoft.Azure.Management.CosmosDB

上記の NuGet パッケージに加えて、 **[プレリリースを含める]** を有効にし、**Azure.ResourceManager.CosmosDB** を追加する必要もあります。 

```csharp
using Azure.Identity;
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;
using Microsoft.Azure.Cosmos;
using System;
using System.Threading.Tasks;

namespace MITest
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var subscriptionId = "Your subscription ID";
            var resourceGroupName = "You resource group";
            var accountName = "Cosmos DB Account name";
            var databaseName = "mi-test";
            var containerName = "container01";

            var tokenCredential = new DefaultAzureCredential();

            // create the management clientSS
            var managementClient = new CosmosDBManagementClient(subscriptionId, tokenCredential);

            // create the data client
            var dataClient = new CosmosClient("https://[Account].documents.azure.com:443/", tokenCredential);

            // create a new database 
            var createDatabaseOperation = await managementClient.SqlResources.StartCreateUpdateSqlDatabaseAsync(resourceGroupName, accountName, databaseName,
                new SqlDatabaseCreateUpdateParameters(new SqlDatabaseResource(databaseName), new CreateUpdateOptions()));
            await createDatabaseOperation.WaitForCompletionAsync();

            // create a new container
            var createContainerOperation = await managementClient.SqlResources.StartCreateUpdateSqlContainerAsync(resourceGroupName, accountName, databaseName, containerName,
                new SqlContainerCreateUpdateParameters(new SqlContainerResource(containerName), new CreateUpdateOptions()));
            await createContainerOperation.WaitForCompletionAsync();


            // create a new item 
            var partitionKey = "pkey";
            var id = Guid.NewGuid().ToString();
            await dataClient.GetContainer(databaseName, containerName)
                .CreateItemAsync(new { id = id, _partitionKey = partitionKey }, new PartitionKey(partitionKey));


            // read back the item
            var pointReadResult = await dataClient.GetContainer(databaseName, containerName)
                .ReadItemAsync<dynamic>(id, new PartitionKey(partitionKey));


            // run a query
            await dataClient.GetContainer(databaseName, containerName)
                .GetItemQueryIterator<dynamic>("SELECT * FROM c")
                .ReadNextAsync();
        }
    }
}

```

ManagedIdentityCredential を使用した言語固有の例:

### <a name="net"></a>.NET

Cosmos DB クライアントを初期化します。

```csharp
CosmosClient client = new CosmosClient("<account-endpoint>", new ManagedIdentityCredential());
```

次に、[データの読み取りと書き込み](../../cosmos-db/sql/sql-api-dotnet-v3sdk-samples.md)を行います。

### <a name="java"></a>Java

Cosmos DB クライアントを初期化します。

```java
CosmosAsyncClient Client = new CosmosClientBuilder().endpoint("<account-endpoint>") .credential(new ManagedIdentityCredential()) .build();
```

次に、[これらのサンプル](../../cosmos-db/sql/sql-api-java-sdk-samples.md)で説明されているように、データの読み取りと書き込みを行います。

### <a name="javascript"></a>JavaScript

Cosmos DB クライアントを初期化します。

```javascript
const client = new CosmosClient({ "<account-endpoint>", aadCredentials: new ManagedIdentityCredential() });
```

次に、[これらのサンプル](../../cosmos-db/sql/sql-api-nodejs-samples.md)で説明されているように、データの読み取りと書き込みを行います。

## <a name="clean-up-steps"></a>クリーンアップの手順

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [ポータル](https://portal.azure.com)で、削除するリソースを選択します。

1. **[削除]** を選択します。

1. メッセージが表示されたら、削除を確定します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

セクションは意図的に空のままになっています

---

## <a name="next-steps"></a>次のステップ

詳細については、Azure リソースのマネージド ID について学びます。

- [Azure リソースのマネージド ID とは](overview.md)
- [Azure リソース マネージャーのテンプレート](https://github.com/Azure/azure-quickstart-templates)

Azure Cosmos について学習します

- [Azure Cosmos DB リソース モデル](../../cosmos-db/account-databases-containers-items.md)
- [チュートリアル:.NET コンソール アプリをビルドして Azure Cosmos DB SQL API アカウントのデータを管理する](../../cosmos-db/sql/sql-api-get-started.md)
