<properties 
	pageTitle="Azure Storage Explorer を使用して Azure BLOB ストレージ間でデータを移動する | Microsoft Azure" 
	description="Azure Storage Explorer を使用して Azure BLOB ストレージ間でデータを移動する" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/31/2016"
	ms.author="bradsev" />

# Azure Storage Explorer を使用して Azure BLOB ストレージ間でデータを移動する

Azure ストレージ エクスプローラーは、Windows、MacOS、Linux で Azure Storage のデータを操作できる Microsoft の無料ツールです。このトピックでは、Azure ストレージ エクスプローラーを使用して Azure BLOB ストレージからデータをアップロードおよびダウンロードする方法について説明します。このツールは、[Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)のページからダウンロードできます。

以下のリンクから、Azure Blob ストレージとの間でデータを移動するために使用するテクノロジについてのガイダンスを参照してください。
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

 
> [AZURE.NOTE] [Azure のデータ サイエンス用仮想マシン](machine-learning-data-science-virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、Azure Storage Explorer は既に VM にインストールされています。
 
> [AZURE.NOTE] Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。

## 前提条件

このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

- Azure サブスクリプションを設定するには、[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
- ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。ストレージ アカウントのアクセス キーをメモしておきます。Azure ストレージ エクスプローラー ツールでアカウントに接続する際に、このキーが必要になります。
- Azure ストレージ エクスプローラー ツールは、[Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)のページからダウンロードできます。インストール時に既定値をそのまま使用します。


<a id="explorer"></a>
## Azure ストレージ エクスプローラーの使用 

次の手順では、Azure ストレージ エクスプローラーを使用してデータをアップロード/ダウンロードする方法について説明します。

1.  Microsoft Azure ストレージ エクスプローラーを起動します。
2.  **Sign in to your account... (アカウントにサインイン...)** ウィザードを起動するには、**[Azure アカウントの設定]** アイコンをクリックし、**[アカウントの追加]** をクリックして、資格情報を入力します。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3.  **Connect to Azure Storage (Azure Storage への接続)** ウィザードを起動するには、**[Connect to Azure storage (Azure Storage への接続)]** アイコンをクリックします。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. **Connect to Azure Storage (Azure Storage への接続)** ウィザードで Azure ストレージ アカウントのアクセス キーを入力し、**[次へ]** をクリックします。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. **[アカウント名]** ボックスにストレージ アカウント名を入力し、**[次へ]** をクリックします。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. 追加されたストレージ アカウントが表示されます。ストレージ アカウントに BLOB コンテナーを作成するには、そのアカウントの **[BLOB コンテナー]** ノードを右クリックして **[BLOB コンテナーの作成]** を選択し、名前を入力します。
7. コンテナーにデータをアップロードするには、対象のコンテナーを選択し、**[アップロード]** ボタンをクリックします。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. **[ファイル]** ボックスの右側の **[...]** をクリックし、アップロードする 1 つまたは複数のファイルをファイル システムから選択します。**[アップロード]** をクリックして、ファイルのアップロードを開始します。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
7. データをダウンロードするには、対応するコンテナー内の BLOB を選択し、**[ダウンロード]** をクリックします。![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

<!---HONumber=AcomDC_0914_2016-->