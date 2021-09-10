---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/18/2021
ms.author: glenga
ms.openlocfilehash: 87f43ebd24cdc7b2cf4163c835e713eb39a52a85
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829712"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、3 つのリソースを作成する必要があります。

- [リソース グループ](../articles/azure-resource-manager/management/overview.md)。関連リソースの論理コンテナーです。
- [ストレージ アカウント](../articles/storage/common/storage-account-create.md)。関数についての情報 (状態など) を維持する目的で使用されます。
- 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

以下のコマンドを使用してこれらの項目を作成します。 Azure CLI と PowerShell の両方がサポートされます。

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az_login) コマンドで Azure アカウントにサインインします。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットで Azure アカウントにサインインします。

    ---

1. 選択したリージョンに `AzureFunctionsQuickstart-rg` という名前のリソース グループを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location <REGION>
    ```
 
    [az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 上記のコマンドで、[az account list-locations](/cli/azure/account#az_account_list_locations) コマンドから返された使用可能なリージョン コードを使用して、`<REGION>` を自分の近くのリージョンに置き換えます。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location <REGION>
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 リソース グループとリソースは通常、近くのリージョンに作成します。[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドレットから返される利用可能なリージョンを使用してください。

    ---

1. リソース グループとリージョン内に汎用ストレージ アカウントを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドでストレージ アカウントを作成します。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットでストレージ アカウントを作成します。

    ---

    前の例の `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](../articles/azure-functions/storage-considerations.md#storage-account-requirements)されています。
