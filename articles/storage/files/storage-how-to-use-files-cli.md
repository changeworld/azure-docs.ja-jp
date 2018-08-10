---
title: Azure CLI を使用した Azure ファイル共有の管理
description: Azure CLI を使用して Azure Files を管理する方法を説明します。
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: ebf8605a0f4686a69f89adf0c36d072f12d3c750
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525258"
---
# <a name="manage-azure-file-shares-using-azure-cli"></a>Azure CLI を使用した Azure ファイル共有の管理
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows、Linux、macOS でマウントできます。 この記事では、Azure CLI を使用して Azure ファイル共有を操作する方法の基本について説明します。 以下の項目について説明します。 

> [!div class="checklist"]
> * リソース グループとストレージ アカウントを作成する
> * Azure ファイル共有を作成する 
> * ディレクトリを作成する
> * ファイルをアップロードする 
> * ファイルをダウンロードする
> * 共有スナップショットを作成して使用する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、この記事の手順を実行するには Azure CLI バージョン 2.0.4 以降を実行している必要があります。 Azure CLI のバージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

既定では、Azure CLI コマンドは JavaScript Object Notation (JSON) を返します。 JSON は、REST API との間でメッセージを送受信するための標準的な方法です。 JSON 応答を操作しやすくするために、この記事のいくつかの例では、Azure CLI コマンドで *query* パラメーターを使用しています。 このパラメーターでは、JSON を解析するために [JMESPath クエリ言語](http://jmespath.org/)が使用されます。 JMESPath クエリ言語に従って Azure CLI コマンドの結果を使用する方法の詳細については、[JMESPath のチュートリアル](http://jmespath.org/tutorial.html)を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[az group create](/cli/azure/group#create) コマンドを使用して作成できます。 

次の例では、*myResourceGroup* という名前のリソース グループを "*米国東部*" の場所に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) をデプロイできるストレージの共有プールです。 1 つのストレージ アカウントに格納できるファイル共有の数に制限はありません。 1 つの共有に格納できるファイルの数に制限はなく、ストレージ アカウントの容量の上限までファイルを格納できます。

次の例では、[az storage account create](/cli/azure/storage/account#create) コマンドを使用して *mystorageaccount\<ランダムな数字\>* という名前のストレージ アカウントを作成し、そのストレージ アカウントの名前を `$STORAGEACCT` 変数に設定します。 ストレージ アカウント名は一意である必要があります。 `$RANDOM` を使用することで、ストレージ アカウント名に数字を追加して一意な名前にすることができます。 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>ストレージ アカウント キーを取得する
ストレージ アカウント キーは、ストレージ アカウント内のリソースへのアクセスを制御するために使用されます。 キーは、ストレージ アカウントの作成時に自動的に作成されます。 ストレージ アカウントのストレージ アカウント キーは、[az storage account keys list](/cli/azure/storage/account/keys#list) コマンドを使用して取得できます。 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
これで、最初の Azure ファイル共有を作成できます。 [az storage share create](/cli/azure/storage/share#create) コマンドを使用してファイル共有を作成します。 この例では、*myshare* という名前の Azure ファイル共有を作成します。 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!IMPORTANT]  
> 共有名には、小文字の英字、数字、単一ハイフンのみを使用することができます (ただし、名前をハイフンで始めることはできません)。 ファイル共有とファイルの名前付けの詳細については、「[Naming and referencing shares, directories, files, and metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Azure ファイル共有の内容の操作
Azure ファイル共有を作成したところで、SMB を使用してファイル共有を [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md)、または [macOS](storage-how-to-use-files-mac.md) にマウントできます。 別の方法として、Azure CLI を使用して Azure ファイル共有を操作することもできます。 SMB を使用してファイル共有をマウントする代わりに Azure CLI を使用する利点として、Azure CLI を使用して実行されるすべての要求がファイル REST API を使用して処理されることが挙げられます。 ファイル REST API を使用して、次の場所からファイル共有内のファイルとディレクトリを作成、変更、および削除できます。

- (SMB でファイル共有をマウントできない) Bash Azure Cloud Shell
- SMB 共有をマウントできないクライアント (ポート 445 がブロック解除されていないオンプレミスのクライアントなど)
- サーバーレス シナリオ ([Azure Functions](../../azure-functions/functions-overview.md) の場合など)

### <a name="create-a-directory"></a>ディレクトリを作成する
Azure ファイル共有のルートに *myDirectory* という名前の新しいディレクトリを作成するには、[`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) コマンドを使用します。

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>ファイルをアップロードする
[`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) コマンドを使用してファイルをアップロードする方法を示すために、最初に Cloud Shell のスクラッチ ドライブ上に、アップロードするファイルを作成します。 次の例では、ファイルを作成してアップロードします。

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI をローカルで実行している場合は、`~/clouddrive` を実際のマシンに存在するパスに置き換えてください。

ファイルのアップロード後、[`az storage file list`](/cli/azure/storage/file#az_storage_file_list) コマンドを使用して、ファイルが Azure ファイル共有にアップロードされたことを確認できます。

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>ファイルをダウンロードする
[`az storage file download`](/cli/azure/storage/file#az_storage_file_download) コマンドを使用して、アップロードしたファイルのコピーを Cloud Shell のスクラッチ ドライブにダウンロードできます。

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>ファイルのコピー
よくあるタスクとして、1 つのファイル共有から別のファイル共有へのファイルのコピー、または Azure Blob Storage コンテナーとの間でのコピーがあります。 この機能を示すために、新しい共有を作成します。 [az storage file copy](/cli/azure/storage/file/copy) コマンドを使用して、アップロードしたファイルをこの新しい共有にコピーします。 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

次に、新しい共有のファイルを一覧表示すると、コピーしたファイルが表示されます。

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

`az storage file copy start` コマンドは、Azure ファイル共有と Azure Blob Storage コンテナーの間でのファイル移動に便利です。ただし、移動の規模が大きい場合は AzCopy の使用をお勧めします  (移動するファイルの数やサイズの点で規模が大きい場合)。[Linux 用の AzCopy](../common/storage-use-azcopy-linux.md) と [Windows 用の AzCopy](../common/storage-use-azcopy.md) についてご確認ください。 AzCopy はローカルにインストールする必要があります。 AzCopy は Cloud Shell では使用できません。 

## <a name="create-and-modify-share-snapshots"></a>共有スナップショットの作成と変更
Azure ファイル共有で実行できるもう 1 つの便利なタスクとして、共有スナップショットの作成があります。 スナップショットでは、Azure ファイル共有の特定時点のコピーが保存されます。 共有スナップショットは、場合によっては既に使い慣れている、次のようなオペレーティング システム テクノロジに類似しています。
- Linux システム用の[論理ボリューム マネージャー (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) スナップショット
- macOS 用の [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) スナップショット
- NTFS や ReFS などの Windows ファイル システム用の[ボリューム シャドウ コピー サービス (VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal)

[`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot) コマンドを使用して、共有スナップショットを作成できます。

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>共有スナップショットの内容の参照
変数 `$SNAPSHOT` でキャプチャした共有スナップショットのタイム スタンプを `az storage file list` コマンドに渡すことで、共有スナップショットの内容を参照できます。

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示
共有に関して取得したスナップショットの一覧を表示するには、次のコマンドを使用します。

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元
前に使用した `az storage file copy start` コマンドを使用してファイルを復元できます。 最初に、スナップショットからファイルを復元するために、アップロードした SampleUpload.txt ファイルを削除します。

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>共有スナップショットの削除
[`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) コマンドを使用して、共有スナップショットを削除できます。 `--snapshot` パラメーターへの `$SNAPSHOT` 参照を含む変数を使用します。

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
完了したら、[`az group delete`](/cli/azure/group#delete) コマンドを使用して、リソース グループとすべての関連リソースを削除できます。 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

また、リソースを個別に削除することもできます。
- この記事用に作成した Azure ファイル共有を削除するには、次のコマンドを使用します。

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- ストレージ アカウント自体を削除するには、次のコマンドを使用します  (この場合、作成した Azure ファイル共有と、Azure Blob Storage コンテナーなど、作成したその他のストレージ リソースが暗黙的に削除されます)。

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>次の手順
- [Azure Portal を使用したファイル共有の管理](storage-how-to-use-files-portal.md)
- [Azure PowerShell を使用したファイル共有の管理](storage-how-to-use-files-powershell.md)
- [Storage Explorer を使用したファイル共有の管理](storage-how-to-use-files-storage-explorer.md)
- [Azure Files のデプロイの計画](storage-files-planning.md)