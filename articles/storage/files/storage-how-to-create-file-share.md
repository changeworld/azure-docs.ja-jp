---
title: Azure ファイル共有を作成する
titleSuffix: Azure Files
description: Azure portal、PowerShell、Azure CLI を使用して Azure ファイル共有を作成する方法。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452888"
---
# <a name="create-a-file-share"></a>ファイル共有を作成する
Azure Files での共有は、 [Azure portal](https://portal.azure.com/)、Azure Storage の PowerShell コマンドレット、Azure Storage のクライアント ライブラリ、または Azure Storage の REST API を使用して、作成することができます。 この記事では、次の方法について学習します。
- Azure portal を使用して Azure ファイル共有を作成する
- PowerShell を使用して Azure ファイル共有を作成する
- Azure CLI を使用して Azure ファイル共有を作成する

## <a name="prerequisites"></a>前提条件
Azure ファイル共有を作成するには、既存のストレージ アカウントを使用するか、[新しい Azure ストレージ アカウントを作成する](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)ことができます。 PowerShell によって Azure ファイル共有を作成するには、アカウント キーとストレージ アカウントの名前が必要になります。 PowerShell または CLI を使用する予定の場合は、ストレージ アカウント キーが必要になります。

> [!NOTE]
> 5 TiB を超えるファイル共有を作成する場合は、[大きなファイル共有の有効化](storage-files-how-to-create-large-file-share.md)に関する記事を参照してください。

## <a name="create-a-file-share-through-the-azure-portal"></a>Azure portal を使用したファイル共有の作成

1. Azure portal 上の **[ストレージ アカウント]** ペインに移動します。
    ![[ストレージ アカウント] ペイン](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. **[+ ファイル共有]** ボタンを選択します。
    ![[ファイル共有の追加] ボタン](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. **[名前]** と **[クォータ]** に情報を入力します。
    ![新しいファイル共有の名前とクォータ](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. 新しいファイル共有を表示します。
    ![UI の [新しいファイル共有]](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. ファイルをアップロードします。
    ![ナビゲーション バーにある [アップロード] ボタン](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. ファイル共有を参照して、ディレクトリとファイルを管理します。
    ![ファイル共有のフォルダー ビュー](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>PowerShell を使用したファイル共有の作成

Azure PowerShell コマンドレットをダウンロードしてインストールします。 インストール先とインストール方法については、 [Azure PowerShell のインストールおよび構成方法](https://docs.microsoft.com/powershell/azure/overview) に関するページを参照してください。

> [!Note]  
> 最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。

1. 新しいストレージ アカウントを作成します。
    ストレージ アカウントは、Azure ファイル共有および BLOB やキューなどのストレージ リソースをデプロイできるストレージの共有プールです。

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. 新しいファイル共有を作成します。        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> ファイル共有の名前はすべて小文字にする必要があります。 ファイル共有およびファイルへの名前付けの詳細については、「 [Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](https://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。

## <a name="create-a-file-share-through-the-cli"></a>CLI を使用したファイル共有の作成

1. Azure CLI をダウンロードしてインストールします。
    「 [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」と「[Azure CLI の概要](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)」を参照してください。

1. 共有の作成先となるストレージ アカウントへの接続文字列を作成します。
    次の例の  ```<storage-account>```  と  ```<resource_group>```  は、実際のストレージ アカウント名とリソース グループに置き換えてください。

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. ファイル共有を作成します。
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>次のステップ

* [Windows でのファイル共有の接続とマウント](storage-how-to-use-files-windows.md)
* [Linux でのファイル共有の接続とマウント](storage-how-to-use-files-linux.md)
* [macOS でのファイル共有の接続とマウント](storage-how-to-use-files-mac.md)

Azure Files の詳細については、次のリンクをご覧ください。

* [ストレージ ファイルに関する FAQ](storage-files-faq.md)
* [Azure Files のトラブルシューティング - Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Azure Files のトラブルシューティング - Linux](storage-troubleshoot-linux-file-connection-problems.md)
