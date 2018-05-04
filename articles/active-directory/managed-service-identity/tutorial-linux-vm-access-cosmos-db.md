---
title: Linux VM の MSI を使用した Azure Cosmos DB へのアクセス
description: Linux VM 上でシステム割り当てマネージド サービス ID (MSI) を使用して Azure Cosmos DB にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: 692bc5eb401ccda36ef42006de509144170f7757
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="use-a-linux-vm-msi-to-access-azure-cosmos-db"></a>Linux VM の MSI を使用した Azure Cosmos DB へのアクセス 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


このチュートリアルでは、Linux VM の MSI を作成して使用する方法を示します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * MSI を有効にして Linux VM を作成する
> * Cosmos DB アカウントを作成する
> * Cosmos DB アカウントでコレクションを作成する
> * Azure Cosmos DB インスタンスへの MSI のアクセスを許可する
> * Linux VM の MSI の `principalID` を取得する
> * アクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す
> * Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する

## <a name="prerequisites"></a>前提条件

まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com)してから先に進んでください。

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある **[試してみる]** ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Linux 仮想マシンを作成する

このチュートリアルでは、新しい MSI を有効にした Linux VM を作成します。

MSI 対応 VM を作成するには

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az_login) を使用して Azure にサインインします。 次のように、VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az_group_create) を使用して、VM とその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vm create](/cli/azure/vm/#az_vm_create) を使用して VM を作成します。 次の例では、`--assign-identity` パラメーターからの要求により、MSI を使用して *myVM* という VM を作成します。 `--admin-username` および `--admin-password` パラメーターは、仮想マシンのサインイン用の管理ユーザー名とパスワードを指定します。 これらの値は、お使いの環境に合わせて更新してください。 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12

## Create a Cosmos DB account 

If you don't already have one, create a Cosmos DB account. You can skip this step and use an existing Cosmos DB account. 

1. Click the **+/Create new service** button found on the upper left-hand corner of the Azure portal.
2. Click **Databases**, then **Azure Cosmos DB**, and a new "New account" panel  displays.
3. Enter an **ID** for the Cosmos DB account, which you use later.  
4. **API** should be set to "SQL." The approach described in this tutorial can be used with the other available API types, but the steps in this tutorial are for the SQL API.
5. Ensure the **Subscription** and **Resource Group** match the ones you specified when you created your VM in the previous step.  Select a **Location** where Cosmos DB is available.
6. Click **Create**.

## Create a collection in the Cosmos DB account

Next, add a data collection in the Cosmos DB account that you can query in later steps.

1. Navigate to your newly created Cosmos DB account.
2. On the **Overview** tab click the **+/Add Collection** button, and an "Add Collection" panel slides out.
3. Give the collection a database ID, collection ID, select a storage capacity, enter a partition key, enter a throughput value, then click **OK**.  For this tutorial, it is sufficient to use "Test" as the database ID and collection ID, select a fixed storage capacity and lowest throughput (400 RU/s).  

## Retrieve the `principalID` of the Linux VM's MSI

To gain access to the Cosmos DB account access keys from the Resource Manager in the following section, you need to retrieve the `principalID` of the Linux VM's MSI.  Be sure to replace the `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (resource group in which you VM resides), and `<VM NAME>` parameter values with your own values.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
応答には、システム割り当て MSI の詳細が含まれています (次のセクションで使用するので、principalID をメモしてください)。

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Linux VM の MSI のアクセス権を Cosmos DB アカウントのアクセス キーに付与する

Cosmos DB は、ネイティブでは Azure AD 認証をサポートしていません。 ただし、MSI を使用して Resource Manager から Cosmos DB アクセス キーを取得し、そのキーを使用して Cosmos DB にアクセスできます。 この手順では、MSI のアクセス権を Cosmos DB アカウントのキーに付与します。

Azure Resource Manager で Azure CLI を使用して MSI ID のアクセス権を Cosmos DB アカウントに付与するには、`<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<COSMOS DB ACCOUNT NAME>` の値を環境に合わせて更新します。 `<MSI PRINCIPALID>` は、「[Retrieve the principalID of the Linux VM's MSI](#retrieve-the-principalID-of-the-linux-VM's-MSI)」(Linux VM の MSI の principalID を取得する) の `az resource show` コマンドによって返された `principalId` プロパティに置き換えます。  Cosmos DB は、アクセス キーを使用する場合、アカウントへの読み取り/書き込みアクセス権と、アカウントへの読み取り専用アクセス権という 2 レベルの粒度をサポートしています。  アカウントの読み取り/書き込みキーを取得する場合は `DocumentDB Account Contributor` ロールを割り当て、アカウントの読み取り専用キーを取得する場合は `Cosmos DB Account Reader Role` ロールを割り当てます。

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

応答には、作成されたロール割り当ての詳細が含まれています。

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-msi-and-use-it-to-call-azure-resource-manager"></a>Linux VM の MSI を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/install_guide) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。 VM に接続する文字列をコピーします。 
2. SSH クライアントを使用して VM に接続します。  
3. 次に、**Linux VM** の作成時に追加した**パスワード**の入力を求められます。 パスワードを入力すると、正常にサインインできます。  
4. CURL を使用して Azure Resource Manager のアクセス トークンを取得します。 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > 前述の要求では、"resource" パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    > 次の応答では、簡潔にするため access_token 要素が短縮されています。
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する  

ここで、CURL を使用して、前のセクションで取得したアクセス トークンを使って Resource Manager を呼び出し、Cosmos DB アカウント アクセス キーを取得します。 アクセス キーを取得したら、Cosmos DB に対してクエリを実行できます。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、および `<COSMOS DB ACCOUNT NAME>` の各パラメーターの値は、必ず実際の値に置き換えてください。 `<ACCESS TOKEN>` の値は、以前に取得したアクセス トークンに置き換えます。  読み取り/書き込みキーを取得する場合は、キー操作の種類 `listKeys` を使用します。  読み取り専用キーを取得する場合は、キー操作の種類 `readonlykeys` を使用します。

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> 前述の URL のテキストでは大文字小文字が区別されるので、リソース グループに使用されている大文字小文字が正しく反映されていることを確認してください。 また、これは GET 要求ではなく POST 要求であることを認識し、-d (NULL を指定可能) を指定して長さの制限を取得するための値を渡すことが重要です。  

CURL 応答では、キーのリストが返されます。  たとえば、読み取り専用キーを取得する場合は次のようになります。  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

これで Cosmos DB アカウントのアクセス キーが取得できました。これを Cosmos DB SDK に渡して、アカウントにアクセスするための呼び出しを行うことができます。  簡単な例として、アクセス キーを Azure CLI に渡す場合があります。  Azure Portal の Cosmos DB アカウント ブレードの **[概要]** タブから <COSMOS DB CONNECTION URL> を取得できます。  <ACCESS KEY> を前述の手順で取得した値に置き換えます。

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

この CLI コマンドは、コレクションに関する詳細を返します。

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>次の手順

- MSI の概要については、「[Azure リソースの管理対象サービス ID (MSI)](overview.md)」を参照してください。

