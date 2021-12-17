---
title: Azure ファイル共有を管理するためのクイック スタート
description: Azure portal、Azure CLI、または Azure PowerShell モジュールを使用して Azure ファイル共有を作成および管理する方法を参照してください。 ストレージ アカウントを作成し、Azure ファイル共有を作成して、Azure ファイル共有を使用します。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 09/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 24a84fb0bc64c4c44d056098e8c429660b88e0e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603526"
---
# <a name="quickstart-create-and-manage-azure-file-shares"></a>クイック スタート: Azure ファイル共有の作成および管理
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows、Linux、macOS でマウントできます。 このガイドでは、Azure portal、Azure CLI、または Azure PowerShell モジュールのいずれかを使用して、Azure ファイル共有を操作する方法の基本について説明します。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="pre-requisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このガイドでは Azure PowerShell モジュール Az バージョン 0.7 以降が必要になります。 実行中の Azure PowerShell モジュールのバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合は、`Login-AzAccount` を実行して Azure アカウントにログインする必要もあります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- 既定では、Azure CLI コマンドは JavaScript Object Notation (JSON) を返します。 JSON は、REST API との間でメッセージを送受信するための標準的な方法です。 JSON 応答を操作しやすくするために、この記事のいくつかの例では、Azure CLI コマンドで *query* パラメーターを使用しています。 このパラメーターでは、JSON を解析するために [JMESPath クエリ言語](http://jmespath.org/)が使用されます。 JMESPath クエリ言語に従って Azure CLI コマンドの結果を使用する方法の詳細については、[JMESPath のチュートリアル](http://jmespath.org/tutorial.html)を参照してください。

---

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

# <a name="portal"></a>[ポータル](#tab/azure-portal)
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)


### <a name="powershell---create-a-resource-group"></a>PowerShell - リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して新しいものを作成できます。 ストレージ アカウントを作成するにはリソース グループが必要です。

次の例では、*myResourceGroup* という名前のリソース グループを米国西部 2 リージョンに作成します。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

### <a name="powershell---create-a-storage-account"></a>PowerShell - ストレージ アカウンの作成

ストレージ アカウントは、Azure ファイル共有のデプロイに使用できるストレージの共有プールです。

この例では、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットを使用してストレージ アカウントを作成します。 ストレージ アカウントの名前は *mystorageaccount\<random number>* で、そのストレージ アカウントへの参照は変数 **$storageAcct** に格納されます。 ストレージ アカウント名は一意である必要があります。そのため、`Get-Random` を使用し、名前に数を追加して一意にします。 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cli---create-a-resource-group"></a>CLI - リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[az group create](/cli/azure/group) コマンドを使用して作成できます。 ストレージ アカウントを作成するにはリソース グループが必要です。

次の例では、*myResourceGroup* という名前のリソース グループを *米国西部 2* に作成します。

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

### <a name="cli---create-a-storage-account"></a>CLI - ストレージ アカウンの作成
ストレージ アカウントは、Azure ファイル共有のデプロイに使用できるストレージの共有プールです。

次の例では、[az storage account create](/cli/azure/storage/account) コマンドを使用してストレージ アカウントを作成します。 ストレージ アカウント名は一意である必要があります。そのため、`$RANDOM` を使用し、名前に数を追加して一意にします。

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```


---


## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する


# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure ファイル共有を作成するには:

1. ダッシュボードからストレージ アカウントを選択します。
1. ストレージ アカウント ページの **[サービス]** セクションで、**[ファイル]** を選択します。
    
    ![ストレージ アカウントの [データ ストレージ] セクションのスクリーンショット。[ファイル共有] を選択します。](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. **[File サービス]** ページの上部にあるメニューで、**[ファイル共有]** をクリックします。 **[新しいファイル共有]** ページが表示されます。
1. **[名前]** に「*myshare*」と入力し、クォータを入力し、 **[階層]** では、 **[トランザクションが最適化されました]** を選択したままにします。
1. **[作成]** を選択して、Azure ファイル共有を作成します。

共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ストレージ アカウントを作成したので、最初の Azure ファイル共有を作成できます。 ファイル共有は、[New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) コマンドレットを使用して作成します。 この例では、**myshare** という名前の共有を作成します。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウントを作成したので、最初の Azure ファイル共有を作成できます。 [az storage share-rm create](/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=false#az_storage_share_rm_create) コマンドを使用してファイル共有を作成します。 この例では、**myshare** という名前の Azure ファイル共有を作成します。 

```azurecli-interactive
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

---

#### <a name="create-a-directory"></a>ディレクトリを作成する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure ファイル共有のルートで *myDirectory* という名前の新しいディレクトリを作成するには:

1. **[File サービス]** ページで、**myshare** ファイル共有を選択します。 ファイル共有の名前ページが開きます。
1. ページ上部のメニューで、**[+ ディレクトリの追加]** を選択します。 **[新しいディレクトリ]** ページが表示されます。
1. 「*myDirectory*」と入力し、**[OK]** をクリックします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure ファイル共有のルートで **myDirectory** という名前の新しいディレクトリを作成するには、[New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) コマンドレットを使用します。

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure ファイル共有のルートに **myDirectory** という名前の新しいディレクトリを作成するには、[`az storage directory create`](/cli/azure/storage/directory) コマンドを使用します。

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

---


#### <a name="upload-a-file"></a>ファイルをアップロードする 
# <a name="portal"></a>[ポータル](#tab/azure-portal)


ファイルのアップロードを行うには、最初にアップロードされるファイルを作成または選択する必要があります。 これは、必要に応じた方法で実行できます。 アップロードしたいファイルを選択したら:

1. **myDirectory** ディレクトリを選択します。 **myDirectory** パネルが開きます。
1. 上部のメニューで、**[アップロード]** を選択します。 **[ファイルのアップロード]** パネルが開きます。  
    
    ![[ファイルのアップロード] パネルのスクリーンショット](media/storage-how-to-use-files-portal/upload-file-1.png)

1. フォルダー アイコンを選択して、ローカル ファイルを参照するためのウィンドウを開きます。 
1. ファイルを選択して、 **[開く]** を選択します。 
1. **[ファイルのアップロード]** ページで、ファイル名を確認して **[アップロード]** を選択します。
1. 完了したら、ファイルが **myDirectory** ページの一覧に表示されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) コマンドレットを使用してファイルをアップロードする方法を示すには、最初に PowerShell Cloud Shell のスクラッチ ドライブ内に、アップロードするファイルを作成する必要があります。 

この例では、スクラッチ ドライブ上の新しいファイルに現在の日付と時間を挿入してから、そのファイルをファイル共有にアップロードします。

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell をローカルで実行している場合、`~/CloudDrive/` を実際のマシンに存在するパスに置き換えます。

ファイルのアップロード後、[Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) コマンドレットを使用して、ファイルが Azure ファイル共有にアップロードされたことを確認できます。 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[`az storage file upload`](/cli/azure/storage/file) コマンドを使用してファイルをアップロードする方法を示すために、最初に Cloud Shell のスクラッチ ドライブ上に、アップロードするファイルを作成します。 次の例では、ファイルを作成してアップロードします。

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI をローカルで実行している場合は、`~/clouddrive` を実際のマシンに存在するパスに置き換えてください。

ファイルのアップロード後、[`az storage file list`](/cli/azure/storage/file) コマンドを使用して、ファイルが Azure ファイル共有にアップロードされたことを確認できます。

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```


---

#### <a name="download-a-file"></a>ファイルをダウンロードする
# <a name="portal"></a>[ポータル](#tab/azure-portal)

ファイルを右クリックすると、アップロードしたファイルのコピーをダウンロードできます。 ダウンロード ボタンを選択した後の正確なエクスペリエンスは、使用しているオペレーティング システムとブラウザーによって異なります。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) コマンドレットを使用して、アップロードしたファイルのコピーを Cloud Shell のスクラッチ ドライブにダウンロードできます。

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

ファイルのダウンロード後、`Get-ChildItem` を使用して、ファイルが PowerShell Cloud Shell のスクラッチ ドライブにダウンロードされたことを確認できます。

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[`az storage file download`](/cli/azure/storage/file) コマンドを使用して、アップロードしたファイルのコピーを Cloud Shell のスクラッチ ドライブにダウンロードできます。

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする
# <a name="portal"></a>[ポータル](#tab/azure-portal)

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

完了したら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループと、リソース グループに含まれているすべてのリソースを削除できます。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

完了したら、[`az group delete`](/cli/azure/group) コマンドを使用して、リソース グループと、リソース グループに含まれているすべてのリソースを削除できます。 

```azurecli-interactive 
az group delete --name $resourceGroupName
```


---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Files とは何ですか。](storage-files-introduction.md)