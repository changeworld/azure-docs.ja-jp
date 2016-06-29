<properties
   pageTitle="Azure Storage Blob のデータを Data Lake Store にコピーする | Microsoft Azure"
   description="AdlCopy ツールを使用して Azure Storage Blob のデータを Data Lake Store にコピーします"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/13/2016"
   ms.author="nitinme"/>

# Azure Storage BLOB から Data Lake Store へのデータのコピー

Azure Data Lake Store には、[AdlCopy](http://aka.ms/downloadadlcopy) というコマンド ライン ツールがあります。このツールを使用すると、**Azure Storage BLOB のデータを Data Lake Store に**コピーできます。AdlCopy を使用して Data Lake Store のデータを Azure Storage BLOB にコピーすることはできません。

AdlCopy ツールには 2 つの使用方法があります。

* **スタンドアロン**。Data Lake Store リソースを使用してタスクを実行する方法です。
* **Data Lake Analytics アカウントの使用**。Data Lake Analytics アカウントに割り当てられているユニットを使用して、コピー操作を実行する方法です。予測可能な方法でコピー操作を実行したい場合は、このオプションを利用できます。

##前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- Data Lake Store のパブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。
- データが含まれる **Azure Storage Blobs** コンテナー。
- **Azure Data Lake Analytics アカウント (省略可能)** - Data Lake Store アカウントの作成手順については、「[チュートリアル: Azure ポータルで Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)」を参照してください。
- **AdlCopy ツール**。[http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy) の AdlCopy ツールをインストールします。

## AdlCopy ツールの構文

AdlCopy ツールを操作するには、次の構文を使用します。

	AdlCopy /Source <Blob source> /Dest <ADLS destination> /SourceKey <Key for Blob account> /Account <ADLA account> /Unit <Number of Analytics units>

構文のパラメーターを次に示します。

| オプション | 説明 |
|-----------|------------|
| Source | Azure Storage Blob のソース データの場所を指定します。ソースには BLOB コンテナーまたは BLOB を使用できます。 |
| Dest | Data Lake Store のコピー先を指定します。 |
| SourceKey | Azure Storage Blob ソースのストレージ アクセス キーを指定します。 |
| Account | **省略可能**。Azure Data Lake Analytics アカウントを使用してコピー ジョブを実行する場合に、このオプションを使用します。構文に /Account オプションを使用して、Data Lake Analytics アカウントを指定しない場合、AdlCopy は既定のアカウントを使用してジョブを実行します。また、このオプションを使用する場合、ソース (Azure Storage Blob) とコピー先 (Azure Data Lake Store) を Data Lake Analytics アカウントのデータ ソースとして追加する必要があります。 |
| Units | コピー ジョブに使用する Data Lake Analytics ユニット数を指定します。 **/Account** オプションを使用して Data Lake Analytics アカウントを指定する場合、このオプションは必須です。                                                                                                                                                                                                                                                                                                                                               



## スタンドアロンとして AdlCopy ツールを使用する

1. コマンド プロンプトを開き、AdlCopy がインストールされているディレクトリ (通常は `%HOMEPATH%\Documents\adlcopy`) に移動します。

2. 次のコマンドを実行して、ソース コンテナーの特定の BLOB を Data Lake Store にコピーします。

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	次に例を示します。

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	Data Lake Store アカウントがある Azure サブスクリプションの資格情報を入力するように求められます。次のような出力が表示されます。

		Initializing Copy.
		Copy Started.
		...............
		0.00% data copied.
		. . .
		. . .
		100% data copied.
		Finishing copy.
		....
		Copy Completed.

1. また、あるコンテナーのすべての BLOB を Data Lake Store アカウントにコピーするには、次のコマンドも使用できます。

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	次に例を示します。

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==



## Data Lake Analytics アカウントで AdlCopy を使用する

Data Lake Analytics アカウントを使用して、AdlCopy ジョブを実行し、Azure Storage Blob のデータを Data Lake Store にコピーすることもできます。通常、このオプションを使用するのは、移動対象のデータが GB および TB の範囲で、パフォーマンスのスループットを向上し、予測可能にする場合です。

Data Lake Analytics アカウントと AdlCopy を使用するには、ソース (Azure Storage Blob) と コピー先 (Azure Data Lake Store) の両方を Data Lake Analytics アカウントのデータ ソースとして追加する必要があります。データ ソースを Data Lake Analytics アカウントに追加する手順については、[Data Lake Analytics の「アカウント データ ソースの管理」](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources)を参照してください。

次のコマンドを実行します。

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

次に例を示します。

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeaccount /Units 2

## 課金

* AdlCopy ツールをスタンドアロンとして使用する場合、ソースの Azure Storage アカウントが Data Lake Store と同じリージョン内になければ、データの移動に関する送信コストがかかります。

* Data Lake Analytics アカウントで AdlCopy ツールを使用する場合、標準の [Data Lake Analytics 料金](https://azure.microsoft.com/pricing/details/data-lake-analytics/)が適用されます。

## AdlCopy の使用に関する注意点

* AdlCopy (バージョン 1.0.4) では、合計で数千個を超えるファイルとフォルダーがあるソースからのデータのコピーをサポートしています。ただし、大規模なデータセットをコピーする際に問題が発生する場合は、ファイルやフォルダーを異なるサブフォルダーに分散し、代わりにこれらのサブフォルダーへのパスをソースとして使用します。

## 次のステップ

- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
- [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0615_2016-->