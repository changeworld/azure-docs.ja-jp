---
title: Azure クイック スタート - Azure PowerShell を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、オブジェクト (BLOB) ストレージで Azure PowerShell を使用します。 その後、PowerShell を使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: b482379c05133dcf58e54bd01f38f0c3cee95e8d
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398595"
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>クイック スタート: Azure PowerShell を使用して BLOB をアップロード、ダウンロード、および一覧表示する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、PowerShell を使用してローカル ディスクと Azure Blob Storage の間でファイルを転送する方法について詳細に説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このクイック スタートには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンピューター上のファイルをフォルダーで整理するように、BLOB のグループを整理できます。

ファイルのパブリック アクセスを許可するには、コンテナー名を設定してから、[New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) を使用してコンテナーを作成し、アクセス許可を 'blob' に設定します。 この例でのコンテナー名は *quickstartblobs* です。

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 IaaS VM をバックアップするために使用される VHD ファイルはページ BLOB です。 追加 BLOB は、ファイルに書き込んでから詳細情報を追加し続ける場合などの、ログ記録に使用されます。 BLOB ストレージに格納されているほとんどのファイルはブロック BLOB です。 

ファイルをブロック BLOB にアップロードするには、コンテナー参照を取得してから、そのコンテナー内のブロック BLOB への参照を取得します。 BLOB 参照を取得したら、[Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) を使用して、それにデータをアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、既に存在する場合は上書きされます。

次の例では、ローカル ディスク上の D:\\_TestImages フォルダーの Image001.jpg と Image002.png を作成したコンテナーにアップロードします。

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

続行する前に、希望する数のファイルをアップロードします。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) を使用して、コンテナー内の BLOB の一覧を取得します。 この例では、アップロードされた BLOB の名前だけを示しています。

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>BLOB をダウンロードする

BLOB をローカル ディスクにダウンロードします。 ダウンロードされる BLOB ごとに、名前を設定し、[Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) を呼び出して BLOB をダウンロードします。

この例では、BLOB をローカル ディスク上の D:\\_TestImages\Downloads にダウンロードします。 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>AzCopy でのデータ転送

[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ユーティリティは、Azure Storage 用のスクリプト可能な高性能データ転送のためのもう 1 つのオプションです。 AzCopy を使用すると、BLOB、ファイル、およびテーブル ストレージとの間でデータを転送できます。

簡単な例として、PowerShell ウィンドウ内から、*myfile.txt* という名前のファイルを *mystoragecontainer* コンテナーにアップロードするための AzCopy コマンドを次に示します。

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成したすべての資産を削除します。 これを行うための最も簡単な方法は、リソース グループの削除です。 これにより、そのグループ内に含まれているすべてのリソースも削除されます。 この場合は、ストレージ アカウントとリソース グループ自体が削除されます。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、ローカル ディスクと Azure Blob Storage の間でファイルを転送する方法を学習しました。 PowerShell を使用した BLOB ストレージの操作については、Azure Storage で Azure PowerShell を使用する方法に関するページに進みます。

> [!div class="nextstepaction"]
> [Azure Storage での Azure PowerShell の使用](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell Storage コマンドレット リファレンス

* [Storage PowerShell コマンドレット](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラー

* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。