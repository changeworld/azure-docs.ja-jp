---
title: Azure Storage Explorer での Azure ファイル共有の管理
description: Azure Storage Explorer を使用して Azure Files を管理する方法について説明します。
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>Azure Storage Explorer での Azure ファイル共有の管理 
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 このガイドでは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して Azure ファイル共有を操作する方法の基本について説明します。 Azure Storage Explorer は、Azure ファイル共有やその他のストレージ リソースを管理するために Windows、macOS、Linux で使用できる、一般的なクライアント ツールです。

このクイックスタートでは、Azure Storage Explorer がインストールされていることを前提としています。 これをインストールする必要がある場合は、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) のページでダウンロードしてください。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * リソース グループとストレージ アカウントを作成する
> * Azure ファイル共有を作成する 
> * ディレクトリを作成する
> * ファイルをアップロードする
> * ファイルをダウンロードする
> * 共有スナップショットを作成して使用する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
Azure Storage Explorer には、新しいリソースを作成する機能はありません。そのため、このデモのために [Azure Portal](https://portal.azure.com/) でストレージ アカウントを作成します。 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>Azure Storage Explorer と Azure リソースの接続
初回の起動時には、**[Microsoft Azure Storage Explorer - 接続]** ウィンドウが表示されます。 Azure Storage Explorer では、いくつかの方法でストレージ アカウントに接続できます。 

- **Azure アカウントでログインする**: 所属する組織のユーザー資格情報または Microsoft アカウントでログインできます。 
- **接続文字列または SAS トークンを使用して、特定のストレージ アカウントに接続する**: 接続文字列は、ストレージ アカウント名とストレージ アカウント キー/SAS トークンが含まれた特別な文字列であり、(単に Azure アカウント内のすべてのストレージ アカウントを表示するのではなく) Azure Storage Explorer がストレージ アカウントに直接アクセスするのを可能にします。 接続文字列の詳細については、「[Azure Storage の接続文字列を構成する](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。
- **ストレージ アカウントの名前とキーを使用して、特定のストレージ アカウントに接続する**: ストレージ アカウントの名前とキーを使用して、Azure Storage に接続します。

このクイック スタートでは、Azure アカウントでログインします。 **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、**[サインイン]** をクリックします。 画面上のプロンプトに従って Azure アカウントにサインインします。

![[Microsoft Azure Storage Explorer - 接続] ウィンドウ](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>ファイル共有を作成する
最初の Azure ファイル共有を *storageacct<random number>* ストレージ アカウント内に作成するには:

1. 作成したストレージ アカウントを展開します。
2. **[ファイル共有]** を右クリックして、**[ファイル共有の作成]** を選択します。  
    ![実際のファイル共有フォルダーとコンテキスト メニューのスクリーンショット](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. ファイル共有に「*myshare*」と入力して、**Enter** キーを押します。

> [!Important]  
> 共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

ファイル共有が作成されると、ファイル共有のタブが右ウィンドウに表示されます。 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Azure ファイル共有の内容の操作
Azure ファイル共有を作成したところで、SMB でファイル共有を [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md)、または [macOS](storage-how-to-use-files-mac.md) にマウントできます。 別の方法として、Azure Portal で Azure ファイル共有を操作することもできます。 Azure Portal 経由で実行されるすべての要求はファイル REST API で処理されるため、SMB アクセスのないクライアント上のファイルとディレクトリを作成、変更、削除できます。

### <a name="create-a-directory"></a>ディレクトリを作成する
ディレクトリを追加すると、ファイル共有を管理するための階層構造が生じます。 複数のレベルを作成できます。ただし、サブディレクトリを作成する前に、すべての親ディレクトリが存在することを確認する必要があります。 たとえば、パス myDirectory/mySubDirectory では、最初にディレクトリ *myDirectory* を作成してから *mySubDirectory* を作成する必要があります。 

1. ファイル共有のタブにあるトップ メニューで、**+ [新しいフォルダー]** ボタンをクリックします。 **[新しいディレクトリの作成]** ページが開きます。
    ![実際の [新しいフォルダー] ボタンのスクリーンショット](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. 「*myDirectory*」を名前として入力し、**[OK]** をクリックします。 

*myDirectory* ディレクトリが *myshare* ファイル共有のタブに表示されます。

### <a name="upload-a-file"></a>ファイルをアップロードする 
ローカル コンピューターからファイル共有の新しいディレクトリにファイルをアップロードします。 フォルダー全体をアップロードしたり、単一のファイルだけをアップロードしたりできます。

1. 上部のメニューで、**[アップロード]** を選択します。 これにより、フォルダーまたはファイルをアップロードするオプションが表示されます。

2. **[ファイルのアップロード]** を選択して、ローカル コンピューターからアップロードするファイルを選択します。

3. **[Upload to a directory]\(ディレクトリにアップロードする\)** で「*myDirectory*」と入力し、**[アップロード]** をクリックします。 

完了したら、ファイルが **myDirectory** ページの一覧に表示されます。

### <a name="download-a-file"></a>ファイルをダウンロードする
ファイル共有内のファイルをクリックして **[ダウンロード]** を選択すると、ファイルのコピーをダウンロードできます。 ファイルの保存先にするローカル コンピューター上の場所を選択して、**[保存]** をクリックします。

ウィンドウの下部にある **[アクティビティ]** ウィンドウに、ダウンロードの進行状況が表示されます。

## <a name="create-and-modify-share-snapshots"></a>共有スナップショットの作成と変更
スナップショットでは、Azure ファイル共有の特定時点のコピーが保存されます。 ファイル共有スナップショットは、場合によっては既に使い慣れている、次のような他のテクノロジに類似しています。
- NTFS や ReFS などの Windows ファイル システム用の[ボリューム シャドウ コピー サービス (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)。
- Linux システム用の[論理ボリューム マネージャー (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) スナップショット
- macOS 用の [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) スナップショット。 

共有スナップショットを作成するには:

1. *myshare* ファイル共有のタブを開きます。
2. タブの上部にあるメニューで **[スナップショットの作成]** をクリックします (これは、Azure Storage Explorer のウィンドウのサイズによっては **[...詳細]** の後ろに隠れている場合があります)。  
    ![実際の [スナップショットの作成] ボタンのスクリーンショット](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>共有スナップショットの一覧表示と参照
スナップショットが作成されたら、**[View Snapshots for File Share]\(ファイル共有のスナップショットを参照する\)** (これは、Azure Storage Explorer のウィンドウのサイズによっては **[...詳細]** の後ろに隠れている場合があります) をクリックして、共有のスナップショットを一覧表示できます。 共有スナップショットをダブルクリックして参照します。

![スナップショットの参照画面のスクリーンショット](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元
共有スナップショットからのファイルの復元を行うには、最初に有効な Azure ファイル共有からファイルを削除する必要があります。 *myDirectory* フォルダーに移動し、アップロードしたファイルを右クリックしてから、**[削除]** をクリックします。 次に、共有スナップショットからそのファイルを復元します。

1. **[View Snapshots for File Share]\(ファイル共有のスナップショットを参照する\)** をクリックします (これは、Azure Storage Explorer のウィンドウのサイズによっては **[...詳細]** の後ろに隠れている場合があります)。
2. 一覧から共有スナップショットを選択し、ダブルクリックして移動します。
3. 削除したファイルが見つかるまでスナップショット内を探し、それを選択してから **[スナップショットの復元]** をクリックします (これは、Azure Storage Explorer のウィンドウのサイズによっては **[...詳細]** の後ろに隠れている場合があります)。 ファイルを復元するとファイル共有の内容が上書きされ、元に戻すことができないことを示す警告が表示されます。 **[OK]**を選択します。
4. これで、有効な Azure ファイル共有の元の場所にファイルが戻ります。

### <a name="delete-a-share-snapshot"></a>共有スナップショットの削除
共有スナップショットを削除するには、[共有スナップショットの一覧に移動します](#list-and-browse-share-snapshots)。 削除したい共有スナップショットを右クリックして、[削除] を選択します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
Azure Storage Explorer には、リソースを削除する機能がありません。[Azure Portal](https://portal.azure.com/) を使用して、このクイック スタートでクリーンアップできます。 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>次の手順
- [Azure Portal でのファイル共有の管理](storage-how-to-use-files-portal.md)
- [Azure PowerShell でのファイル共有の管理](storage-how-to-use-files-powershell.md)
- [Azure CLI でのファイル共有の管理](storage-how-to-use-files-cli.md)
- [Azure Files のデプロイの計画](storage-files-planning.md)