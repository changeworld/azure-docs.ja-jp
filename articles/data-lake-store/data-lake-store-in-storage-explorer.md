---
title: Data Lake Storage Gen1 リソースを管理する - Azure Storage Explorer
description: Azure Data Lake Storage Gen1 のデータやリソースに Azure Storage Explorer からアクセスして管理する方法について説明します
author: jejiang
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/04/2021
ms.author: jejiang
ms.openlocfilehash: 9ef06e1b13141e3b5c342842a63e8e520e3e03e9
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2021
ms.locfileid: "111589897"
---
# <a name="manage-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Storage Explorer を使用して Data Lake Storage Gen1 リソースを管理する

[Azure Data Lake Storage Gen1](./data-lake-store-overview.md) は、テキストやバイナリ データなどの大量の非構造化データを格納するためのサービスです。 そのデータには、HTTP または HTTPS を介してどこからでもアクセスすることができます。 Azure Storage Explorer 上で Data Lake Storage Gen1 を使用すると、BLOB やキューなど、他の Azure エンティティと共に、Data Lake Storage Gen1 のデータおよびリソースにアクセスし、管理できます。 さまざまな Azure エンティティを、同じツールを使用して 1 か所で管理できるようになりました。

もう 1 つの利点は、Data Lake Storage Gen1 データを管理するためにサブスクリプションのアクセス許可が不要であることです。 Storage Explorer では、アクセス許可が付与されていれば、 **[ローカルで接続済み]** ノードに Data Lake Storage Gen1 パスをアタッチできます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の前提条件を満たす必要があります。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial)に関するページを参照してください。
* Data Lake Storage Gen1 アカウント。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](./data-lake-store-get-started-portal.md)に関する記事をご覧ください。

## <a name="install-storage-explorer"></a>Storage Explorer のインストール

Azure Storage Explorer の最新版は、[製品 Web ページ](https://azure.microsoft.com/features/storage-explorer/)からインストールできます。 インストールは、Windows、Linux、Mac の各バージョンに対応しています。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

1. Storage Explorer で、プラグイン アイコンを選択します。

   ![ユーザー インターフェイスのプラグイン アイコンの場所を示すスクリーンショット](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

   **[Azure Storage へ接続する]** ダイアログ ボックスが表示されます。
1. **[リソースの選択]** ページで、 **[サブスクリプション]** を選択します。
1. **[Azure 環境の選択]** ページで、サインインする Azure 環境を選択し、 **[次へ]** を選択します。
1. **[サインイン]** ダイアログ ボックスで、Azure の資格情報を入力し、 **[次へ]** を選択します。

1. Storage Explorer の **[アカウント管理]** ペインで、管理する Data Lake Storage Gen1 アカウントを含むサブスクリプションを選択し、 **[エクスプローラーを開く]** を選択します。
1. **[エクスプローラー]** ペインで、サブスクリプションを展開します。 ペインが更新され、選択したサブスクリプション内のアカウントが表示されます。 これには、すべての Data Lake Storage Gen1 アカウントが含まれます。次に例を示します。

     ![Data Lake Storage Gen1 ノード内のアカウントの例を示すスクリーンショット](./media/data-lake-store-in-storage-explorer/account-list.png)

## <a name="connect-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 への接続

ご利用のサブスクリプションに存在しないリソースであっても、そのリソースの URI を支給してもらえば、アクセスすることができます。 サインイン後、その URI を使って Data Lake Storage Gen1 に接続することができます。

1. Storage Explorer を開きます。
1. **[ローカルで接続済み]** を展開します。
1. **[Data Lake Storage Gen1 (プレビュー)]** を右クリックし、 **[Data Lake Storage Gen1 に接続]** を選択します。
1. URI を入力します。次に例を示します。

      ![[Connect to Data Lake Store]\(Data Lake Store に接続する\) ダイアログ ボックスと URI を入力するためのテキスト ボックスを示すスクリーンショット](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

   入力した URL の場所にツールが移動します。

      ![UI の [Data Lake Storage Gen1 (プレビュー)] ノードの下にある Data Lake Storage Gen1 アカウントを表示します](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-the-contents-of-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントの内容を表示する

Data Lake Storage Gen1 アカウントのリソースには、フォルダーとファイルが含まれます。 次の手順では、Storage Explorer 内で Data Lake Storage Gen1 アカウントの内容を表示する方法を示します。

1. Storage Explorer を開きます。
1. 表示する Data Lake Storage Gen1 アカウントが含まれているサブスクリプションを展開します。
1. **[Data Lake Storage Gen1 (プレビュー)]** を展開します。
1. 表示する Data Lake Storage Gen1 アカウントを選択します。

   メイン ウィンドウには、Data Lake Storage Gen1 アカウントの内容が表示されます。

   ![Data Lake Storage Gen1 アカウントを選択した状態のメイン ウィンドウと、アカウント内のフォルダーの一覧を表示します](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 のリソースを管理する

次の操作を実行することで、Data Lake Storage Gen1 リソースを管理できます。

* 複数の Data Lake Storage Gen1 アカウントを対象に Data Lake Storage Gen1 のリソースを閲覧する。  
* 接続文字列を使用して、直接 Data Lake Storage Gen1 に接続し、管理する。
* 他のユーザーが ACL を使用して共有した Data Lake Storage Gen1 リソースを **[ローカルで接続済み]** に表示する。
* ファイルとフォルダーの CRUD 操作を実行する (再帰的フォルダーとファイルの複数選択に対応)。
* フォルダーをドラッグ アンド ドロップして、クイック アクセスの最近使用した場所に追加する。 この操作は、デスクトップのエクスプローラーの操作とよく似ています。
* Storage Explorer から 1 回のクリックで、Data Lake Storage Gen1 のハイパーリンクをコピーして開く。
* 下のウィンドウに **アクティビティ** ログを表示し、アクティビティの状態を確認する。
* フォルダーの統計とファイルのプロパティを表示する。

## <a name="manage-resources-in-azure-storage-explorer"></a>Azure Storage Explorer でリソースを管理する

Data Lake Storage Gen1 アカウントを作成した後で、次の操作を実行できます。

* ローカル コンピューターでのフォルダーやファイルのアップロードとダウンロード、リソースの表示
* **[クイック アクセス]** のピン留め、新しいフォルダーの作成、URL のコピー、すべて選択
* コピー、貼り付け、名前の変更、削除、フォルダーの統計情報の取得、最新の情報への更新

次の項目では、Data Lake Storage Gen1 アカウント内のリソースを管理する方法を示します。 実行するタスクの手順に従ってください。

### <a name="upload-files"></a>ファイルをアップロードする

1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、 **[ファイルのアップロード]** を選択します。
1. **[Select files to upload]\(アップロードするファイルを選択\)** ダイアログ ボックスで、アップロードするファイルを選択します。
1. **[開く]** を選択して、アップロードを開始します。

> [!NOTE]
> ローカル コンピューター上のファイルは、アップロードを開始するために直接ドラッグすることもできます。

### <a name="upload-a-folder"></a>フォルダーをアップロードする

1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、 **[フォルダーのアップロード]** を選択します。
1. **[Select folder to upload]\(アップロードするフォルダーを選択\)** ダイアログ ボックスで、アップロードするフォルダーを選択します。
1. **[フォルダーの選択]** を選択して、アップロードを開始します。

> [!NOTE]
> ローカル コンピューター上のフォルダーは、アップロードを開始するために直接ドラッグすることもできます。

### <a name="download-folders-or-files-to-your-local-computer"></a>フォルダーまたはファイルをローカル コンピューターにダウンロードする

1. ダウンロードするフォルダーまたはファイルを選択します。
1. メイン ウィンドウのツール バーで、 **[ダウンロード]** を選択します。
1. **[Select a folder to save the downloaded files into]\(ダウンロードしたファイルを保存するフォルダーの選択\)** ダイアログ ボックスで、場所と名前を指定します。
1. **[保存]** を選択します。

### <a name="open-a-folder-or-file-from-your-local-computer"></a>ローカル コンピューターからフォルダーまたはファイルを開く

1. 開くフォルダーまたはファイルを選択します。
1. メイン ウィンドウのツール バーで、 **[開く]** を選択します。 または、選択したフォルダーまたはファイルを右クリックし、ショートカット メニューの **[開く]** を選択します。

ファイルがダウンロードされ、その基になるファイルの種類に関連付けられているアプリケーションで開かれます。 または、メイン ウィンドウでフォルダーが開きます。

### <a name="copy-folders-or-files-to-the-clipboard"></a>フォルダーまたはファイルをクリップボードにコピーする

Data Lake Storage Gen1 のフォルダーまたはファイルをコピーして、それらを別の Data Lake Storage Gen1 アカウントに貼り付けることはできます。 異なる種類のストレージ間でのコピーと貼り付けの操作はサポートされていません。 たとえば、Data Lake Storage Gen1 のフォルダーやファイルをコピーして、Azure Blob Storage に貼り付けることも、その逆の操作を行うこともできません。

1. コピーするフォルダーまたはファイルを選択します。
1. メイン ウィンドウのツール バーで、 **[コピー]** を選択します。 または、選択したフォルダーまたはファイルを右クリックし、ショートカット メニューの **[コピー]** を選択します。
1. ナビゲーション ウィンドウで別の Data Lake Storage Gen1 アカウントを参照し、それを選択してメイン ウィンドウに表示します。
1. メイン ウィンドウのツール バーで **[貼り付け]** を選択して、コピーを作成します。 または、コピー先のショートカット メニューで **[貼り付け]** を選択します。

> [!NOTE]
> コピー/貼り付け操作は、フォルダーまたはファイルをローカル コンピューターにダウンロードして、目的の場所にアップロードすることで動作します。 ツールがバックエンドでアクションを実行することはありません。 大きなファイルのコピー/貼り付け操作には時間がかかります。

### <a name="delete-folders-or-files"></a>フォルダーまたはファイルを削除する

1. 削除するフォルダーまたはファイルを選択します。
1. メイン ウィンドウのツール バーで、 **[削除]** を選択します。 または、選択したフォルダーまたはファイルを右クリックし、ショートカット メニューの **[削除]** を選択します。
1. 確認のダイアログ ボックスで **[はい]** を選択します。

### <a name="pin-to-quick-access"></a>クイック アクセスにピン留め

1. リソースに簡単にアクセスできるようにピン留めするフォルダーを選択します。
1. メイン ウィンドウのツール バーで **[クイック アクセスにピン留め]** を選択します。

   ナビゲーション ウィンドウで、選択したフォルダーが **[クイック アクセス]** ノードに追加されます。

   ![UI の [クイック アクセス] ノードの下に表示されるフォルダーを表示します](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

### <a name="use-deep-links"></a>ディープ リンクを使用する

URL がある場合は、エクスプローラーまたはブラウザーのアドレス パスに URL を入力してください。 Storage Explorer が自動的に開き、その URL の場所に移動します。

![エクスプローラー ウィンドウにコピーされる、Data Lake Storage Gen1 アカウント内のフォルダーの URL を表示します](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>次のステップ

* [最新の Storage Explorer のリリース ノートとビデオ](https://www.storageexplorer.com)を確認します。
* [Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Data Lake Storage Gen1 の使用を開始する](./data-lake-store-overview.md)
