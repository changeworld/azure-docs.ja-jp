<properties
	pageTitle="SSIS コネクタを使用して Azure BLOB ストレージとの間でデータを移動する | Microsoft Azure"
	description="SSIS コネクタを使用して Azure BLOB ストレージとの間でデータを移動します。"
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
	ms.date="09/14/2016"
	ms.author="bradsev" />

# SSIS コネクタを使用して Azure BLOB ストレージとの間でデータを移動する

[SQL Server Integration Services Feature Pack for Azure](https://msdn.microsoft.com/library/mt146770.aspx) には、Azure への接続、Azure とオンプレミスのデータ ソースとの間でのデータ転送、Azure に格納したデータの処理に必要なコンポーネントが用意されています。

以下のリンクから、Azure Blob ストレージとの間でデータを移動するために使用するテクノロジについてのガイダンスを参照してください。

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


ユーザーは、オンプレミスのデータをクラウドに移動すると、そのデータにすべての Azure サービスからアクセスできるようになり、一連の Azure テクノロジを最大限に活用できます。たとえば、Azure Machine Learning または HDInsight クラスター上で使用できます。

通常は、これが [SQL](machine-learning-data-science-process-sql-walkthrough.md) および [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) のチュートリアルで最初の手順になります。

ハイブリッド データ統合のシナリオに共通するビジネス ニーズを満たすために SSIS を使用する標準的なシナリオの詳細については、[SQL Server Integration Services Feature Pack for Azure をさらに活用する方法](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx)に関するブログを参照してください。

> [AZURE.NOTE] Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。

## 前提条件

この記事で説明するタスクを実行するには、Azure サブスクリプションと Azure ストレージ アカウントの設定が必要です。データをアップロードまたはダウンロードするために、Azure ストレージ アカウント名とアカウント キーを確認しておく必要があります。

- **Azure サブスクリプション**を設定するには、[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。

- **ストレージ アカウント**の作成と、アカウントとキー情報の取得についての手順については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。


**SSIS コネクタ**を使用するには、次のものをダウンロードする必要があります。

- **SQL Server 2014 または 2016 Standard 以降**: インストールには SQL Server Integration Services が含まれます。
- **Microsoft SQL Server 2014 または 2016 Integration Services Feature Pack for Azure**: これらは、[SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) および [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) のページからそれぞれダウンロードできます。

> [AZURE.NOTE] SSIS は SQL Server と共にインストールされますが、Express バージョンには含まれていません。SQL Server の各種エディションに含まれるアプリケーションについては、[SQL Server のエディション](http://www.microsoft.com/ja-JP/server-cloud/products/sql-server-editions/)に関するページを参照してください。

SSIS のトレーニング資料については、[SSIS の実践的トレーニング](http://www.microsoft.com/download/details.aspx?id=20766)に関するドキュメントを参照してください。

SISS を使用して稼働状態にし、簡単な ETL (抽出、変換、読み込み) パッケージを作成する方法については、「[SSIS チュートリアル: 簡単な ETL パッケージの作成](https://msdn.microsoft.com/library/ms169917.aspx)」を参照してください。

## NYC タクシー データセットをダウンロードする  
ここで説明する例では、一般公開されているデータセットである、[NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) データセットを使用します。このデータセットは、2013 年のニューヨーク市における約 1 億 7300 万件のタクシー乗車情報で構成されています。データには、乗車の詳細データと料金データの 2 種類があります。月ごとにファイルが用意されているため、全部で 24 ファイルになり、各ファイルは圧縮されていない状態で約 2 GB になります。


## Azure BLOB ストレージにデータをアップロードする
SSIS 機能パックを使用して、データをオンプレミスから Azure BLOB ストレージに移動するには、次に示す [**Azure BLOB アップロード タスク**](https://msdn.microsoft.com/library/mt146776.aspx)のインスタンスを使用します。

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


タスクで使用されるパラメーターについて、ここで説明します。


フィールド|Description|
----------------------|----------------|
**AzureStorageConnection**|既存の Azure Storage 接続マネージャーを指定するか、BLOB ファイルがホストされる場所を指す Azure ストレージ アカウントを参照する、新しい Azure Storage 接続マネージャーを作成します。|
**BlobContainer**|アップロードしたファイルを BLOB として保持する BLOB コンテナーの名前を指定します。|
**BlobDirectory**|アップロードしたファイルがブロック BLOB として格納される BLOB ディレクトリを指定します。BLOB ディレクトリは仮想階層構造です。BLOB が既に存在する場合は置き換えられます。|
**LocalDirectory**|アップロードするファイルが含まれているローカル ディレクトリを指定します。|
**FileName**|指定された名前のパターンを使用したファイルを選択するための名前フィルターを指定します。たとえば、MySheet*.xls* には、MySheet001.xls や MySheetABC.xlsx のようなファイルが含まれます。|
**TimeRangeFrom/TimeRangeTo**|時間範囲フィルターを指定します。*TimeRangeFrom* から *TimeRangeTo* までに変更されたファイルが含まれます。|


> [AZURE.NOTE] 転送を試す前に、**AzureStorageConnection** の資格情報が正しく、**BlobContainer** が存在することを確認する必要があります。

## Azure BLOB ストレージからデータをダウンロードする

SSIS を使用して Azure BLOB ストレージからオンプレミスのストレージにデータをダウンロードするには、[Azure BLOB アップロード タスク](https://msdn.microsoft.com/library/mt146779.aspx)のインスタンスを使用します。

##さらに高度な SSIS と Azure のシナリオ
SSIS 機能パックでは、複数のタスクを 1 つにパッケージ化することで、さらに複雑なフローを処理することができます。たとえば、BLOB データを HDInsight クラスターに直接フィードし、HDInsight クラスターの出力を BLOB にダウンロードしてから、さらにオンプレミス ストレージにダウンロードすることができます。SSIS は、追加の SSIS コネクタを使用することで、HDInsight クラスター上で Hive ジョブおよび Pig ジョブを実行できます。

- SSIS を使用して Azure HDInsight クラスター上で Hive スクリプトを実行するには、[Azure HDInsight Hive タスク](https://msdn.microsoft.com/library/mt146771.aspx)を使用します。
- SSIS を使用して Azure HDInsight クラスター上で Pig スクリプトを実行するには、[Azure HDInsight Pig タスク](https://msdn.microsoft.com/library/mt146781.aspx)を使用します。

<!---HONumber=AcomDC_0921_2016-->