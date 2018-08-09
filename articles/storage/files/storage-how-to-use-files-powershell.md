---
title: Azure PowerShell での Azure ファイル共有の管理
description: Azure PowerShell を使用して Azure ファイル共有を管理する方法について説明します。
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: a69e8946c050cc69411f843b07e907a119c6edea
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522931"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Azure PowerShell での Azure ファイル共有の管理 
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows、Linux、macOS でマウントできます。 このガイドでは、PowerShell を使用して Azure ファイル共有を操作する方法の基本について説明します。 この記事では、次の方法について説明します。

> [!div class="checklist"]
> * リソース グループとストレージ アカウントを作成する
> * Azure ファイル共有を作成する 
> * ディレクトリを作成する
> * ファイルをアップロードする 
> * ファイルをダウンロードする
> * 共有スナップショットを作成して使用する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このガイドでは Azure PowerShell モジュール バージョン 5.1.1 以降が必要になります。 実行中の Azure PowerShell モジュールのバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドレットで新しいものを作成できます。 

次の例では、*myResourceGroup* という名前のリソース グループを米国東部リージョンに作成します。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) のデプロイに使用できるストレージの共有プールです。 ストレージ アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。

この例では、`mystorageacct<random number>` という名前のストレージ アカウントを作成し、このストレージ アカウントへの参照を変数 **$storageAcct** に設定します。 ストレージ アカウント名は一意である必要があります。そのため、`Get-Random` を使用し、名前に数を追加して一意にします。 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
これで、最初の Azure ファイル共有を作成できます。 ファイル共有は、[New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare) コマンドレットを使用して作成できます。 この例では、`myshare` という名前の共有を作成します。

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> 共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Azure ファイル共有の内容の操作
Azure ファイル共有を作成したところで、SMB でファイル共有を [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md)、または [macOS](storage-how-to-use-files-mac.md) にマウントできます。 別の方法として、Azure PowerShell モジュールで Azure ファイル共有を操作することもできます。 これは、SMB でファイル共有をマウントするよりも便利です。理由は、PowerShell で実行されるすべての要求はファイル REST API で処理されるため、以下でファイル共有のファイルとディレクトリを作成、変更、削除できるからです。

- (SMB でファイル共有をマウントできない) PowerShell Cloud Shell。
- SMB 共有をマウントできないクライアント (ポート 445 がブロック解除されていないオンプレミスのクライアントなど)。
- サーバーレス シナリオ ([Azure Functions](../../azure-functions/functions-overview.md) の場合など)。 


### <a name="create-directory"></a>ディレクトリを作成する
Azure ファイル共有のルートで *myDirectory* という名前の新しいディレクトリを作成するには、[New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory) コマンドレットを使用します。


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>ファイルをアップロードする
[Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent) コマンドレットを使用してファイルをアップロードする方法を行うには、最初に PowerShell Cloud Shell のスクラッチ ドライブ内に、アップロードするファイルを作成する必要があります。 

この例では、スクラッチ ドライブ上の新しいファイルに現在の日付と時間を挿入してから、そのファイルをファイル共有にアップロードします。

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell をローカルで実行している場合、`C:\Users\ContainerAdministrator\CloudDrive\` を実際のマシンに存在するパスに置き換える必要があります。

ファイルのアップロード後、[Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) コマンドレットを使用して、ファイルが Azure ファイル共有にアップロードされたことをチェックできます。 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>ファイルをダウンロードする
[Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) コマンドレットを使用して、アップロードしたばかりのファイルのコピーを Cloud Shell のスクラッチ ドライブにダウンロードできます。

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

ファイルのダウンロード後、`Get-ChildItem` を使用して、ファイルが PowerShell Cloud Shell のスクラッチ ドライブにダウンロードされたことを確認できます。

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>ファイルのコピー
よくあるタスクの 1 つとして、1 つのファイル共有から別のファイル共有へのファイルのコピー、または Azure BLOB ストレージ コンテナーとの間でのコピーがあります。 この機能を実行するために、新しい共有を作成し、[Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy) コマンドレットを使用して先ほどアップロードしたファイルをその新しい共有にコピーできます。 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

次に、新しい共有のファイルを一覧表示すると、コピーしたファイルが表示されます。

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

`Start-AzureStorageFileCopy` コマンドレットは、Azure ファイル共有と Azure BLOB ストレージ コンテナーの間でのアドホック ファイル移動に便利です。ただし、(移動するファイルの数やサイズの点で) 移動の規模が大きい場合は AzCopy の使用をお勧めします。 [Windows 用の AzCopy](../common/storage-use-azcopy.md) と [Linux 用の AzCopy](../common/storage-use-azcopy-linux.md) についてご確認ください。 AzCopy はローカルにインストールする必要があります。Cloud Shell では使用できません。 

## <a name="create-and-modify-share-snapshots"></a>共有スナップショットの作成と変更
さらに、Azure ファイル共有で実行できる便利なタスクの 1 つとして、共有スナップショットの作成があります。 スナップショットでは、特定の時点の Azure ファイル共有が保存されます。 共有スナップショットは、場合によっては既に使い慣れている、次のようなオペレーティング システム テクノロジに類似しています。
- NTFS や ReFS などの Windows ファイル システム用の[ボリューム シャドウ コピー サービス (VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal)
- Linux システム用の[論理ボリューム マネージャー (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) スナップショット
- macOS 用の [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) スナップショット。 

共有の共有スナップショットは、ファイル共有の PowerShell オブジェクトに `Snapshot` メソッドを使用して作成できます。ファイル共有は、[Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare) コマンドレットで取得します。 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>共有スナップショットの参照
スナップショットの参照 (`$snapshot`) を `Get-AzureStorageFile` コマンドレットの `-Share` パラメーターに渡すことで、共有スナップショットの内容を参照できます。

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示
次のコマンドで、共有に関して取得したスナップショットの一覧を表示できます。

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元
先ほど使った `Start-AzureStorageFileCopy` コマンドを使用して、ファイルを復元できます。 このクイック スタートでは、先ほどアップロードした `SampleUpload.txt` ファイルを最初に削除します。そうすることで、スナップショットからそれを復元できます。

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>共有スナップショットの削除
`$snapshot` 参照を `-Share` パラメーターに含めた変数で [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare) コマンドレットを使用すると、共有スナップショットを削除できます。

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
完了したら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使用して、リソース グループとすべての関連リソースを削除できます。 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

また、リソースを 1 つずつ削除することもできます。

- このクイック スタートで作成した Azure ファイル共有を削除する場合。
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- ストレージ アカウント自体を削除する場合 (この場合、作成した Azure ファイル共有が暗黙的に削除されます。また、Azure BLOB ストレージ コンテナーなど、作成されたその他のストレージ リソースも暗黙的に削除されます)。
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>次の手順
- [Azure Portal でのファイル共有の管理](storage-how-to-use-files-portal.md)
- [Azure CLI でのファイル共有の管理](storage-how-to-use-files-cli.md)
- [Storage Explorer でのファイル共有の管理](storage-how-to-use-files-storage-explorer.md)
- [Azure Files のデプロイの計画](storage-files-planning.md)
