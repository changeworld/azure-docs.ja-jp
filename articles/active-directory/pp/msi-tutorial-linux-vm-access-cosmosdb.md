---
title: "Linux VM 上でユーザー割り当て管理対象サービス ID (MSI) を使用して Azure Cosmos DB にアクセスする"
description: "Linux VM 上でユーザー割り当て管理対象サービス ID (MSI) を使用して Azure Cosmos DB にアクセスするプロセスについて説明するチュートリアルです。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2c0c3597999e80af86f079385653d94ddfcab245
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Linux VM 上でユーザー割り当て管理対象サービス ID (MSI) を使用して Azure Cosmos DB にアクセスする 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

このチュートリアルでは、ユーザー割り当て管理対象サービス ID (MSI) を作成して Linux 仮想マシンから使用し、その ID を使用して Azure Cosmos DB にアクセスする方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * ユーザー割り当て管理対象サービス ID (MSI) を作成する
> * ユーザー割り当て MSI を Linux 仮想マシンに割り当てる
> * Azure Cosmos DB インスタンスへの MSI のアクセスを許可する
> * ユーザー割り当て MSI の ID を使用してアクセス トークンを取得し、それ使用して Azure Cosmos DB にアクセスする

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある **[試してみる]** ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Linux 仮想マシンを作成する

このチュートリアルでは、新しい Linux VM を作成します。 既存の VM で MSI を有効にすることもできます。

1. Azure Portal の左上隅にある **[+ リソースの作成]** をクリックします。
2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS VM]** の順に選択します。
3. 仮想マシンの情報を入力します。 **[認証の種類]** で、**[SSH 公開キー]** または **[パスワード]** を選択します。 作成した資格情報を使用して VM にログインできます。

    ![MSI Linux VM](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. ドロップダウンで仮想マシンの**サブスクリプション**を選択します。
5. **[リソース グループ]** で、**[新規作成]** を選択し、この VM のリソース グループ名を入力します。 完了したら、**[OK]** をクリックします。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、[サポートされるディスクの種類] フィルターを変更します。 **[設定]** ブレードで、既定値のまま **[OK]** をクリックします。

## <a name="create-a-user-assigned-msi"></a>ユーザー割り当て MSI を作成する

1. Azure Cloud Shell セッションではなく CLI コンソールを使用している場合は、最初に Azure にサインインします。 新しい MSI を作成する Azure サブスクリプションに関連付けられているアカウントを使用します。

    ```azurecli
    az login
    ```

2. [az identity create](/cli/azure/identity#az_identity_create) を使用してユーザー割り当て MSI を作成します。 `-g` パラメーターは MSI が作成されるリソース グループを指定し、`-n` パラメーターはその名前を指定します。 `<RESOURCE GROUP>` と `<MSI NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    応答には、作成されたユーザー割り当ての MSI の詳細が含まれます (後の手順で使用するので、MSI の `clientId` と `id` の値に注意してください)。

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>ユーザー割り当て MSI を Linux VM に割り当てる

システム割り当て MSI とは異なり、ユーザー割り当て MSI は、複数の Azure リソース上のクライアントで使用できます。 このチュートリアルでは、ユーザー割り当て MSI を単一の VM に割り当てます。 ユーザー割り当て MSI は複数の VM に割り当てることもできます。

[az vm assign-identity](/cli/azure/vm#az_vm_assign_identity) を使用して、ユーザー割り当て MSI を Linux VM に割り当てます。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `--identities` パラメーターの値には、前の手順で返された `id` プロパティを使用します。

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する 

Cosmos DB アカウントがまだない場合は、この時点で作成します。 この手順をスキップし、必要に応じて既存の Cosmos DB アカウントを使用することもできます。 

1. Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2. **[データベース]**、**[Azure Cosmos DB]** の順にクリックします。[新しいアカウント] パネルが表示されます。
3. Cosmos DB アカウントの **[ID]** を入力します。この ID は後で使用されます。  
4. **[API]** は「SQL」に設定します。 このチュートリアルで説明されている方法は、他の利用可能な API の種類と併用できますが、このチュートリアルの手順は SQL API 向けです。
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。  Cosmos DB を使用できる **[場所]** を選択します。
6. **Create** をクリックしてください。

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Cosmos DB アカウントでコレクションを作成する

次に、後の手順でクエリを実行できるデータ コレクションを Cosmos DB アカウントに追加します。

1. 新しく作成した Cosmos DB アカウントに移動します。
2. **[概要]** タブで **[+ コレクションの追加]** ボタンをクリックします。[コレクションの追加] パネルが表示されます。
3. コレクションにデータベース ID、コレクション ID を指定し、ストレージ容量を選択し、パーティション キーを入力し、スループット値を入力し、**[OK]** をクリックします。  このチュートリアルでは、データベース ID とコレクション ID として "Test" を使用し、固定ストレージ容量と最低スループット (400 RU/s) を選択する設定で十分です。

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>ユーザー割り当て MSI アクセス権を Cosmos DB アカウントのアクセス キーに付与します。

Cosmos DB は、ネイティブでは Azure AD 認証をサポートしていません。  ただし、MSI を使用して Resource Manager から Cosmos DB アクセス キーを取得し、そのキーを使用して Cosmos DB にアクセスできます。  この手順では、ユーザー割り当て MSI アクセス権を Cosmos DB アカウントのキーに付与します。

まず、Azure CLI を使用して Azure Resource Manager の Cosmos DB アカウントに MSI ID アクセス権を付与します。 使用する環境に合わせて、`<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、および `<COSMOS DB ACCOUNT NAME>` の値を更新します。 `<MSI PRINCIPALID>` を、「[ユーザー割り当て MSI を作成する](#create-a-user-assigned-msi)」の `az identity create` コマンドによって返された `principalId` プロパティで置き換えます。  Cosmos DB は、アクセス キーを使用する場合、アカウントへの読み取り/書き込みアクセス権と、アカウントへの読み取り専用アクセス権という 2 レベルの粒度をサポートしています。  アカウントの読み取り/書き込みキーを取得する場合は `DocumentDB Account Contributor` ロールを割り当て、アカウントの読み取り専用キーを取得する場合は `Cosmos DB Account Reader Role` ロールを割り当てます。

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

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>ユーザー割り当て MSI を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/install_guide) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。 VM に接続する文字列をコピーします。 
2. SSH クライアントを使用して VM に接続します。  
3. 次に、**Linux VM** の作成時に追加した**パスワード**の入力を求められます。 パスワードを入力すると、正常にサインインできます。  
4. CURL を使用して Azure Resource Manager のアクセス トークンを取得します。  

    アクセス トークンの CURL 要求と応答を次に示します。  <CLIENT ID> をユーザー割り当て MSI の clientId 値で置き換えます。
    
    ```bash
    curl 'http://localhost:50342/oauth2/token?resource=https://management.azure.com/&client_id=<CLIENT ID>' -H "Metadata:true"
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

ここで、CURL を使用して、前のセクションで取得したアクセス トークンで Resource Manager を呼び出し、Cosmos DB アカウント アクセス キーを取得します。 アクセス キーを取得したら、Cosmos DB に対してクエリを実行できます。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、および `<COSMOS DB ACCOUNT NAME>` の各パラメーターの値は、必ず実際の値に置き換えてください。 `<ACCESS TOKEN>` の値は、以前に取得したアクセス トークンに置き換えます。  読み取り/書き込みキーを取得する場合は、キー操作の種類 `listKeys` を使用します。  読み取り専用キーを取得する場合は、キー操作の種類 `readonlykeys` を使用します。

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

- MSI の概要については、「[Azure リソースの管理対象サービス ID (MSI)](msi-overview.md)」を参照してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。