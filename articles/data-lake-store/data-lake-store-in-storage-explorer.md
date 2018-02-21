---
title: "Azure Storage Explorer で Azure Data Lake Store リソースを管理する"
description: "ユーザーが Azure Storage Explorer で ADLS のデータおよびリソースにアクセスし、管理できるようにします。"
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Storage Explorer (プレビュー) を使用して Azure Data Lake Store リソースを管理する

[Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) は、テキストやバイナリ データなどの大量の非構造化データを格納するためのサービスです。 ユーザーは、HTTP または HTTPS を介してどこからでもデータにアクセスできます。 Azure Storage Explorer で ADLS を使用すると、ユーザーは BLOB やキューなど、他の Azure エンティティと共に、ADLS のデータおよびリソースにアクセスし、管理できます。 ユーザーは、さまざまな Azure エンティティを、同じツールを使用して 1 か所で管理できるようになりました。

もう 1 つの利点として、ユーザーは ADLS データを管理するためにサブスクリプションのアクセス許可を必要としません。 Storage Explorer では、他のユーザーからアクセス許可を付与される限り、"ローカルで接続済み" のノードに ADLS パスをアタッチできます。

## <a name="prerequisites"></a>前提条件
この記事の手順を完了するには、次の前提条件を満たす必要があります。

*   Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial)に関するページを参照してください。
*   Azure Data Lake Store アカウント。 これを作成する手順については、[Azure Data Lake Store の使用開始](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal)に関するページを参照してください。

## <a name="installation"></a>インストール

最新の Azure Storage Explorer を「[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)」ページからインストールします。 現在、Windows、Linux、Mac バージョンがサポートされています。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

1. **Azure Storage Explorer** をインストールしたら、次の図の左側にある **[プラグイン]** アイコンをクリックします。
       
   ![[プラグイン] アイコン](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、**[サインイン]** をクリックします。

   ![Azure サブスクリプションに接続する](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. **[Azure サインイン]** ダイアログ ボックスで、**[サインイン]** を選択し、Azure 資格情報を入力します。

    ![[サインイン]](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. 一覧からサブスクリプションを選択し、**[適用]** をクリックします。

    ![適用](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    エクスプローラー ウィンドウが更新され、選択したサブスクリプション内のアカウントが表示されます。

    ![アカウントの一覧](./media/data-lake-store-in-storage-explorer/account-list.png)

    **Azure Data Lake Store** から Azure サブスクリプションに正常に接続されています。

## <a name="connect-to-data-lake-store"></a>Data Lake Store に接続する
リソースにアクセスしたいと考えていますが、サブスクリプションに存在しません。 ただし、他のユーザーから、リソースの URI の取得が許可されています。 この場合は、サインインした後に URI を使用して Data Lake Store に接続できます。 次の手順を参照してください。
1. ストレージ エクスプローラー (プレビュー) を起動します。
2. 左側のウィンドウで **[Local and Attached]\(ローカルで接続済み\)** を展開します。
3. **[Data Lake Store]** を右クリックし、コンテキスト メニューから **[Connect to Data Lake Store]\(Data Lake Store に接続する\)** を選択します。

      ![Data Lake Store への接続のコンテキスト メニュー](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI を入力すると、ツールが、入力した URL の場所に移動します。

      ![Data Lake Store への接続のダイアログ](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Data Lake Store への接続の結果](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Azure Data Lake Store アカウントの内容を表示する
Azure Data Lake Store アカウントのリソースには、フォルダーとファイルが含まれます。

次の手順では、Storage Explorer (プレビュー) 内で ADLS アカウントの内容を表示する方法を説明します。

1. ストレージ エクスプローラー (プレビュー) を起動します。
2. 左側のウィンドウで、表示する Azure Data Lake Store アカウントが含まれているサブスクリプションを展開します。
3. **[Data Lake Store]** を展開します。
4. 表示する Azure Data Lake Store アカウントのノードを右クリックし、コンテキスト メニューの **[開く]** を選択します。 ADLS アカウントをダブルクリックして開くこともできます。 
5. メイン ウィンドウには、ADLS アカウントの内容が表示されます。

     ![メイン ウィンドウ](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Azure Data Lake Store リソースの管理

次の操作を実行することで、Azure Data Lake Store リソースを管理できます。
*   複数の ADL アカウントにわたって ADLS リソースを移動する。  
*   接続文字列を使用して、直接 ADLS に接続し、管理する。 
*   他のユーザーが ACL を使用して共有した ADLS リソースを [Local and Attached]\(ローカルで接続済み\) で表示する。
*   ファイルまたはフォルダーの CRUD 操作を実行する (再帰的フォルダーとファイルの複数選択に対応)。 
*   フォルダーをドラッグ アンド ドロップして、[クイック アクセス] および [最近使用した場所] に追加する。これにより、デスクトップのエクスプローラーのエクスペリエンスがミラーリングされます。 
*   Storage Explorer を使用して 1 回のクリックで、ADL ハイパーリンクをコピーして開く。 
*   右下のウィンドウにアクティビティ ログを表示し、アクティビティの状態を確認する。
*   フォルダーの統計とファイルのプロパティを表示する。

## <a name="manage-resources-in-azure-storage-explorer"></a>Azure Storage Explorer でリソースを管理する
Azure Data Lake Store アカウントを作成したら、ご利用のローカル コンピューターで、フォルダーとファイルをアップロードしたり、リソースをダウンロードして開いたりできます。 また、クイック アクセスへの​​ピン留め、フォルダーの新規作成、URL のコピー、すべて選択も可能になります。 さらに、コピー、貼り付け、名前の変更、削除、フォルダーの統計、更新を実行できます。

次の項目では、Azure Data Lake Store アカウント内でリソースを管理する方法を示します。 実行するタスクに応じて、以下の手順に従います。
   * **ファイルをアップロードする**

     1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[Upload Files]\(ファイルのアップロード\)** を選択します。

        ![Upload files menu](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. **[Select files to Upload]\(アップロードするファイルを選択\)** ダイアログで、アップロードするファイルを選択します。

        ![フォルダーのアップロードのダイアログ](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. **[開く]** を選択して、アップロードを開始します。
   * **フォルダーをアップロードする**

     1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[Upload Folder]\(フォルダーのアップロード\)** を選択します。

        ![Upload folder menu](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. **[Select folder to Upload]\(アップロードするフォルダーを選択\)** ダイアログで、アップロードするフォルダーを選択します。

        ![フォルダーのアップロードのダイアログ](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. **[フォルダーの選択]** を選択して、フォルダーのアップロードを開始します。

        ![フォルダーのアップロードのダイアログ](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > ローカル コンピューター内のフォルダーとファイルは、アップロードを実装するために直接ドラッグできます。 
       
   * **フォルダーまたはファイルをローカル コンピューターにダウンロードする**

     1. ダウンロードするフォルダーまたはファイルを選択します。
     2. メイン ウィンドウのツール バーで、 **[ダウンロード]**を選択します。
     3. **[Select a folder to save the downloaded files into]\(ダウンロードしたファイルを保存するフォルダーの選択\)** ダイアログで、フォルダーまたはファイルのダウンロード先の場所を指定します。 その後、それに付ける名前を指定します。
     4. **[保存]** を選択します。
   * **ローカル コンピューターからフォルダーまたはファイルを開く**

     1. 開くフォルダーまたはファイルを選択します。
     2. メイン ウィンドウのツール バーで **[開く]** を選択するか、選択したフォルダーまたはファイルを右クリックして、コンテキスト メニューの **[開く]** をクリックします。
     3. ファイルがダウンロードされ、その基になるファイルの種類に関連付けられているアプリケーションを使用して開かれます。 または、メイン ウィンドウでフォルダーが開きます。

        ![ファイルを開く](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **フォルダーまたはファイルをクリップボードにコピーする**

     1. コピーするフォルダーまたはファイルを選択します。
     2. メイン ウィンドウのツール バーで **[コピー]** を選択するか、選択したフォルダーまたはファイルを右クリックして、コンテキスト メニューの **[コピー]** をクリックします。
     3. 左側のウィンドウで別の ADLS アカウントに移動し、それをダブルクリックして、メイン ウィンドウに表示します。
     4. メイン ウィンドウのツール バーで **[貼り付け]** を選択して、コピーを作成します。 または、コピー先でコンテキスト メニューの **[貼り付け]** をクリックします。

        ![フォルダーまたはファイルのコピーと貼り付け](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + 異なる種類のストレージ間でのコピーと貼り付けはサポート**されていません**。 ADLS のフォルダーまたはファイルをコピーして、別の ADLS アカウントに貼り付けることができます。 ただし、ADLS のフォルダーまたはファイルをコピーして BLOB ストレージに貼り付けることも、その逆の操作を行うことも**できません**。 
          > + コピーと貼り付けは、フォルダーまたはファイルをローカルにダウンロードして、目的の場所にアップロードすることで動作します。 ツールがバックエンドでアクションを実行することは**ありません**。 大きなファイルのコピーと貼り付けには時間がかかります。 パフォーマンスの高いファイルのコピーと移動の最適化が行われています。
   * **フォルダーまたはファイルを削除する**

     1. 削除するフォルダーまたはファイルを選択します。
     2. メイン ウィンドウのツール バーで **[削除]** を選択するか、選択したフォルダーまたはファイルを右クリックして、コンテキスト メニューの **[削除]** をクリックします。
     3. 確認のダイアログで **[はい]** を選択します。

        ![フォルダーまたはファイルのコピーと貼り付け](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **クイック アクセスにピン留めする**

     1. ピン留めするフォルダーを選択します。
     2. メイン ウィンドウのツール バーで **[クイック アクセスにピン留め]** を選択します。
     3. 左側のウィンドウで、選択したフォルダーが **[クイック アクセス]** ノードに追加されていることがわかります。

        ![クイック アクセスにピン留め](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. クイック アクセスを作成すると、リソースに簡単にアクセスできます。
   * **ディープ リンク**
     1. URL がある場合は、URL を**エクスプローラー**またはブラウザーのアドレス パスに入力するだけです。
     2. その後、**Storage Explorer.exe** が自動的に起動し、入力した URL の場所に移動します。

        ![エクスプローラーのディープ リンク](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>次の手順
* [最新のストレージ エクスプローラー (プレビュー) のリリース ノートとビデオ](http://www.storageexplorer.com)を確認します。
* [Azure Storage Explorer で Azure Cosmos DB を管理する](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)方法を学習します。
* 「[ストレージ エクスプローラー (プレビュー) の概要](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)」で Storage Explorer の詳細を確認します。
* [Azure Data Lake Store の概要](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)に関する記事を参照して Azure Data Lake Store (ADLS) の使用を開始します。
* 次のビデオを見て、[Azure Storage Explorer を使用して Azure Cosmos DB を利用する方法](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)を確認します。
