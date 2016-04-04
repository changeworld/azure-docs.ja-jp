<properties 
	pageTitle="Azure Storage Explorer を使用して Azure BLOB ストレージ間でデータを移動する | Microsoft Azure" 
	description="Azure Storage Explorer を使用して Azure BLOB ストレージ間でデータを移動する" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="bradsev" />

# Azure Storage Explorer を使用して Azure BLOB ストレージ間でデータを移動する

## はじめに 

Azure ストレージ エクスプローラーは、Azure Storage アカウント内のデータを検査および変更するための無料の Windows ベースのツールです。このトピックでは、Azure ストレージ エクスプローラーを使用して Azure BLOB ストレージからデータをアップロードおよびダウンロードする方法について説明します。このツールは、[Azure ストレージ エクスプローラー](http://storageexplorer.com/)からダウンロードできます。

以下のリンクから、Azure Blob ストレージとの間でデータを移動するために使用するテクノロジについてのガイダンスを参照してください。
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
&nbsp;
 
> [AZURE.NOTE] [Azure のデータ サイエンス用仮想マシン](machine-learning-data-science-virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、Azure Storage Explorer は既に VM にインストールされています。
 
&nbsp;
 
> [AZURE.NOTE] Azure BLOB ストレージの完全な概要については、「[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)」と「[Azure BLOB Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)」を参照してください。

## 前提条件

このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

- Azure サブスクリプションを設定するには、「[1 か月間の無料評価版](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。
- ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。


<a id="explorer"></a>
## Azure ストレージ エクスプローラーの使用 

次の手順では、Azure ストレージ エクスプローラーを使用してデータをアップロード/ダウンロードする方法について説明します。

1.  Azure ストレージ エクスプローラーの起動 
2.  アクセスするストレージ アカウントが、Azure ストレージ エクスプローラーに追加されていない場合は、[アカウントの追加] ボタンをクリックしてアカウントを追加します。既に追加されている場合は、[--ストレージ アカウントの選択--] ドロップダウンからアカウントを選択します。![Create workspace][1] <br>
3. ストレージ アカウント名とストレージ アカウント キーを入力して、[ストレージ アカウントの追加] をクリックします。複数のストレージ アカウントを追加できます。その場合、各アカウントがタブに表示されます。このストレージ アカウント下のコンテナーは、左側のパネルに表示されます。コンテナーを選択すると、右側のパネルにコンテナー内の BLOB が表示されます。![Create workspace][2] <br> ![Create workspace][3] <br>
4. データをアップロードするには、[アップロード] ボタンをクリックします。アップロードする 1 つまたは複数のファイルをファイル システムから選択し、[開く] をクリックしてファイルのアップロードを開始します。
5. 対応するコンテナー内の BLOB を選択し、[ダウンロード] ボタンをクリックして、データをダウンロードします。

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=AcomDC_0309_2016-->