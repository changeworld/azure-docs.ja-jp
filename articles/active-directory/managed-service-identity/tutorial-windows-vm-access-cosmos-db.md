---
title: Windows VM マネージド サービス ID を使用して Azure Cosmos DB にアクセスする
description: このチュートリアルでは、Windows VM 上でシステム割り当てのマネージド サービス ID を使用して Azure Cosmos DB にアクセスするプロセスについて説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: 05b31dffbe51dcbcd76c13a17f6ecc640b63569b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248970"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-cosmos-db"></a>チュートリアル: Windows VM マネージド サービス ID を使用して Azure Cosmos DB にアクセスする

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows VM のマネージド サービス ID を作成し、それを使用して Cosmos DB にアクセスする方法を示します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * マネージド サービス ID が有効な Windows VM を作成する 
> * Cosmos DB アカウントを作成する
> * Windows VM のマネージド サービス ID のアクセス権を Cosmos DB アカウントのアクセス キーに付与する
> * Windows VM のマネージド サービス ID を使用して、Azure Resource Manager を呼び出すためのアクセス トークンを取得する
> * Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Windows 仮想マシンを作成する

このチュートリアルでは、新しい Windows VM を作成します。  既存の VM でマネージド サービス ID を有効にすることもできます。

1. Azure Portal の左上隅にある **[リソースの作成]** ボタンをクリックします。
2. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 
3. 仮想マシンの情報を入力します。 ここで作成した**ユーザー名**と**パスワード**は、仮想マシンへのログインに使用する資格情報になります。
4. ドロップダウンで仮想マシンの適切な**サブスクリプション**を選択します。
5. 仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 設定ページで、既定値のまま **[OK]** をクリックします。

   ![イメージ テキスト](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>VM でマネージド サービス ID を有効にする 

仮想マシンのマネージド サービス ID を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 Azure portal で仮想マシンのマネージド サービス ID を有効にすると、内部では VM が Azure AD に登録されてマネージド ID が作成され、VM で ID が構成されます。

1. マネージド サービス ID を有効にする**仮想マシン**を選択します。  
2. 左側のナビゲーション バーで、**[構成]** をクリックします。 
3. **管理対象のサービス ID** が表示されます。 マネージド サービス ID を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。 
4. **[保存]** をクリックして構成を保存します。  
   ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する 

Cosmos DB アカウントがまだない場合は作成します。 この手順をスキップし、既存の Cosmos DB アカウントを使用することもできます。 

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

## <a name="grant-windows-vm-managed-service-identity-access-to-the-cosmos-db-account-access-keys"></a>Windows VM のマネージド サービス ID のアクセス権を Cosmos DB アカウントのアクセス キーに付与する

Cosmos DB は、ネイティブでは Azure AD 認証をサポートしていません。 ただし、マネージド サービス ID を使用して Resource Manager から Cosmos DB のアクセス キーを取得し、そのキーを使用して Cosmos DB にアクセスできます。 この手順では、マネージド サービス ID のアクセス権を Cosmos DB アカウントのキーに付与します。

Azure Resource Manager で PowerShell を使用してマネージド サービス ID の ID のアクセス権を Cosmos DB アカウントに付与するには、`<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<COSMOS DB ACCOUNT NAME>` の値を環境に合わせて更新します。 `<MSI PRINCIPALID>` は、「[Retrieve the principalID of the Linux VM's MSI](#retrieve-the-principalID-of-the-linux-VM's-MSI)」(Linux VM の MSI の principalID を取得する) の `az resource show` コマンドによって返された `principalId` プロパティに置き換えます。  Cosmos DB は、アクセス キーを使用する場合、アカウントへの読み取り/書き込みアクセス権と、アカウントへの読み取り専用アクセス権という 2 レベルの粒度をサポートしています。  アカウントの読み取り/書き込みキーを取得する場合は `DocumentDB Account Contributor` ロールを割り当て、アカウントの読み取り専用キーを取得する場合は `Cosmos DB Account Reader Role` ロールを割り当てます。

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-managed-service-identity-to-call-azure-resource-manager"></a>Windows VM のマネージド サービス ID を使用して、Azure Resource Manager を呼び出すためのアクセス トークンを取得する

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。 

ここでは、Azure Resource Manager PowerShell コマンドレットを使用する必要があります。  インストールしていない場合は、先に進む前に、[最新バージョンをダウンロード](https://docs.microsoft.com/powershell/azure/overview)してください。

最新バージョンの [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) を Windows VM にインストールする必要もあります。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。 
2. Windows VM を作成したときに追加した**ユーザー名**と**パスワード**を入力します。 
3. これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで PowerShell を開きます。
4. PowerShell の Invoke-WebRequest を使用して、ローカルのマネージド サービス ID エンドポイントに対して Azure Resource Manager のアクセス トークンを取得するよう要求します。

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > "resource"パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    
    次に、$response オブジェクト内で JavaScript Object Notation (JSON) 形式の文字列として格納されている "Content" 要素を抽出します。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    次に、アクセス トークンを応答から抽出します。
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する

ここで、PowerShell を使用して、前のセクションで取得したアクセス トークンで Resource Manager を呼び出し、Cosmos DB アカウント アクセス キーを取得します。 アクセス キーを取得したら、Cosmos DB に対してクエリを実行できます。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、および `<COSMOS DB ACCOUNT NAME>` の各パラメーターの値は、必ず実際の値に置き換えてください。 `<ACCESS TOKEN>` の値は、以前に取得したアクセス トークンに置き換えます。  読み取り/書き込みキーを取得する場合は、キー操作の種類 `listKeys` を使用します。  読み取り専用キーを取得する場合は、キー操作の種類 `readonlykeys` を使用します。

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
応答では、キーのリストが返されます。  たとえば、読み取り専用キーを取得する場合は次のようになります。

```powershell
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

このチュートリアルでは、Cosmos DB にアクセスするための Windows マネージド サービス ID の作成方法について説明します。  Cosmos DB の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Cosmos DB の概要 ](/azure/cosmos-db/introduction)


