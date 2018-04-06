---
title: Azure CLI を使用した Azure ファイル共有の管理
description: Azure CLI を使用して Azure Files を管理する方法を説明します。
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Azure CLI を使用した Azure ファイル共有の管理
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows、Linux、macOS でマウントできます。 このガイドでは、Azure CLI を使用して Azure ファイル共有を操作する方法の基本について説明します。 以下の項目について説明します。 

> [!div class="checklist"]
> * リソース グループとストレージ アカウントを作成する
> * Azure ファイル共有を作成する 
> * ディレクトリを作成する
> * ファイルをアップロードする 
> * ファイルをダウンロードする
> * 共有スナップショットを作成して使用する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、このガイドでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

既定では、Azure CLI コマンドは JSON (JavaScript Object Notation) を返します。これは、REST API からメッセージを送受信する実際の方法です。 JSON 応答を操作しやすくするために、このガイドのいくつかの例では、Azure CLI コマンドでクエリ パラメーターを使用します。 このパラメーターでは、JSON の解析のために [JMESPath クエリ言語](http://jmespath.org/)が使用されます。 Azure CLI コマンドの結果を操作する方法について詳しくは、「[JMESPath Tutorial (JMESPath チュートリアル)](http://jmespath.org/tutorial.html)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[az group create](/cli/azure/group#create) コマンドで新しいものを作成できます。 

次の例では、*myResourceGroup* という名前のリソース グループを "*米国東部*" の場所に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) をデプロイできるストレージの共有プールです。 ストレージ アカウントに含まれるファイル共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。

この例では、[az storage account create](/cli/azure/storage/account#create) コマンドを使用して `mystorageaccount<random number>` という名前のストレージ アカウントを作成します。次に、そのストレージ アカウントの名前を変数 `$STORAGEACCT` に設定します。 ストレージ アカウント名は一意である必要があります。`$RANDOM` を使用し、ストレージ アカウントの末尾に数字を追加して一意にします。 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>ストレージ アカウント キーを取得する
ストレージ アカウント キーは、ストレージ アカウント内のリソースへのアクセスを制御するために使用されます。 これらは、ストレージ アカウントの作成時に自動的に作成されます。 ストレージ アカウントのストレージ アカウント キーは、[az storage account keys list](/cli/azure/storage/account/keys#list) コマンドを使用して取得できます。 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
これで、最初の Azure ファイル共有を作成できます。 ファイル共有は、[az storage share create](/cli/azure/storage/share#create) コマンドを使用して作成できます。 この例では、`myshare` という名前の Azure ファイル共有を作成します。 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> 共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Azure ファイル共有の内容の操作
Azure ファイル共有を作成したところで、SMB でファイル共有を [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md)、または [macOS](storage-how-to-use-files-mac.md) にマウントできます。 別の方法として、Azure CLI で Azure ファイル共有を操作することもできます。 これは、SMB でファイル共有をマウントするよりも便利です。理由は、Azure CLI で実行されるすべての要求はファイル REST API で処理されるため、以下でファイル共有のファイルとディレクトリを作成、変更、削除できるからです。

- (SMB でファイル共有をマウントできない) Bash Cloud Shell。
- SMB 共有をマウントできないクライアント (ポート 445 がブロック解除されていないオンプレミスのクライアントなど)。
- サーバーレス シナリオ ([Azure Functions](../../azure-functions/functions-overview.md) の場合など)。 

### <a name="create-directory"></a>ディレクトリを作成する
Azure ファイル共有のルートで *myDirectory* という名前の新しいディレクトリを作成するには、[`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) コマンドを使用します。

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>ファイルをアップロードする
[`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) コマンドを使用してファイルをアップロードする方法を行うには、最初に Azure CLI Cloud Shell のスクラッチ ドライブ内に、アップロードするファイルを作成する必要があります。 次の例では、ファイルを作成してアップロードします。

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI をローカルで実行している場合、`~/clouddrive` を実際のマシンに存在するパスに置き換える必要があります。

ファイルのアップロード後、[`az storage file list`](/cli/azure/storage/file#az_storage_file_list) コマンドを使用して、ファイルが Azure ファイル共有にアップロードされたことをチェックできます。

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
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>ファイルのコピー
よくあるタスクの 1 つとして、1 つのファイル共有から別のファイル共有へのファイルのコピー、または Azure BLOB ストレージ コンテナーとの間でのコピーがあります。 この機能を実行するために、新しい共有を作成し、[az storage file copy](/cli/azure/storage/file/copy) コマンドを使用してアップロードしたファイルをその新しい共有にコピーできます。 

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

`az storage file copy start` コマンドは、Azure ファイル共有と Azure BLOB ストレージ コンテナーの間でのアドホック ファイル移動に便利です。ただし、(移動するファイルの数やサイズの点で) 移動の規模が大きい場合は AzCopy の使用をお勧めします。 [Linux 用の AzCopy](../common/storage-use-azcopy-linux.md) と [Windows 用の AzCopy](../common/storage-use-azcopy.md) についてご確認ください。 AzCopy はローカルにインストールする必要があります。Cloud Shell では使用できません 

## <a name="create-and-modify-share-snapshots"></a>共有スナップショットの作成と変更
さらに、Azure ファイル共有で実行できる便利なタスクの 1 つとして、共有スナップショットの作成があります。 スナップショットでは、特定の時点の Azure ファイル共有が保存されます。 共有スナップショットは、場合によっては既に使い慣れている、次のようなオペレーティング システム テクノロジに類似しています。
- Linux システム用の[論理ボリューム マネージャー (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) スナップショット
- macOS 用の [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) スナップショット。 
- NTFS や ReFS などの Windows ファイル システム用の[ボリューム シャドウ コピー サービス (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)

[`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot) コマンドを使用して、共有スナップショットを作成できます。

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>共有スナップショットの内容の参照
変数 `$SNAPSHOT` でキャプチャした共有スナップショットのタイムスタンプを `az storage file list` コマンドに渡すことで、共有スナップショットの内容を参照できます。

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示
次のコマンドで、共有に関して取得したスナップショットの一覧を表示できます。

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元
先ほど使った `az storage file copy start` コマンドを使用して、ファイルを復元できます。 最初に、アップロードした `SampleUpload.txt` ファイルを削除します。そうすることで、スナップショットからそれを復元できます。

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
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
`$SNAPSHOT` 参照を `--snapshot` パラメーターに含めた変数で [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) コマンドを使用すると、共有スナップショットを削除できます。

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

また、リソースを 1 つずつ削除することもできます。
- このクイック スタートで作成した Azure ファイル共有を削除する場合。

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

- ストレージ アカウント自体を削除する場合 (この場合、作成した Azure ファイル共有が暗黙的に削除されます。また、Azure BLOB ストレージ コンテナーなど、作成されたその他のストレージ リソースも暗黙的に削除されます)。

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>次の手順
- [Azure Portal でのファイル共有の管理](storage-how-to-use-files-portal.md)
- [Azure PowerShell でのファイル共有の管理](storage-how-to-use-files-powershell.md)
- [Storage Explorer でのファイル共有の管理](storage-how-to-use-files-storage-explorer.md)
- [Azure Files のデプロイの計画](storage-files-planning.md)