<properties 
   pageTitle="Azure コマンド ライン インターフェイスを使用して Azure Data Lake Analytics を管理する | Azure" 
   description="Azure CLI を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ジョブ、およびユーザーを管理する方法について説明します。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Azure コマンド ライン インターフェイス (CLI) を使用して Azure Data Lake Analytics を管理する

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。他のツールを使用する管理のトピックを表示するには、上のタブ セレクターをクリックします。

**前提条件**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- **Azure CLI**。「[Azure CLI のインストールと構成](../xplat-cli-install.md)」をご覧ください。
	- このデモを完了するためには、**プレリリース版の ** [Azure CLI ツール](https://github.com/MicrosoftBigData/AzureDataLake/releases)をダウンロードしてインストールします。
- **認証**。次のコマンドを使用します。

		azure login
	職場か学校のアカウントを使用した認証の詳細については、「[Azure CLI から Azure サブスクリプションへの接続する](../xplat-cli-connect.md)」をご覧ください。
- **Azure リソース マネージャー モードへの切り替え**。次のコマンドを使用します。

		azure config mode arm

**Data Lake Store と Data Lake Analytics のコマンド一覧を表示するには:**

	azure datalake store
	azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## アカウントの管理

Data Lake Analytics ジョブを実行するには、Data Lake Analytics アカウントが必要です。Azure HDInsight とは異なり、ジョブを実行しなければ、Analytics アカウントには課金されません。ジョブの実行時にのみ課金されます。詳細については、「[Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

###アカウントの作成

  	azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


###アカウントの更新

次のコマンドは、既存の Data Lake Analytics アカウントのプロパティを更新します。
  	
	azure datalake analytics account set "<Data Lake Analytics Account Name>"


###アカウントの一覧表示

Data Lake Analytics アカウントを一覧表示します。

	azure datalake analytics account list

特定のリソース グループ内の Data Lake Analytics アカウントをリストします。

	azure datalake analytics account list -g "<Azure Resource Group Name>"

特定の Data Lake Analytics アカウントの詳細を取得します。

	azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

###Data Lake Analytics アカウントの削除

  	azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## アカウント データ ソースの管理

Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Azure Storage](../storage/storage-introduction.md)

Analytics アカウントを作成する際には、既定のストレージ アカウントとして Azure Data Lake Storage アカウントを指定する必要があります。既定の ADL ストレージ アカウントは、ジョブ メタデータとジョブ監査ログを保存するために使用されます。Analytics アカウントを作成したら、さらに Data Lake Storage アカウントや Azure Storage アカウントを追加することができます。

### 既定の ADL ストレージ アカウントの検索

	azure datalake analytics account show "<Data Lake Analytics Account Name>"

値は、properties:datalakeStoreAccount:name の下に表示されます。

### 他の Azure BLOB ストレージ アカウントの追加

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

>[AZURE.NOTE] BLOB ストレージの短い名のみがサポートされます。"myblob.blob.core.windows.net" などの FQDN 名は使用しないでください。

### 他の Data Lake Store アカウントの追加

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] は追加される Data Lake が既定の Data Lake アカウントかどうかを示すスイッチであり、省略可能です。

### 既存のデータ ソースの更新

既存の Data Lake Store アカウントを既定として設定するには:

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d
	  
既存の BLOB ストレージ アカウント キーを更新するには:

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### データ ソースの一覧表示:

	azure datalake analytics account show "<Data Lake Analytics Account Name>"
	
![データ ソースを一覧表示している Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### データ ソースの削除:

Data Lake Store アカウントを削除するには:

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

BLOB ストレージ アカウントを削除するには:

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## ジョブの管理

ジョブを作成するには、Data Lake Analytics アカウントが必要です。詳細については、「[Data Lake Analytics アカウントの管理](#manage-accounts)」を参照してください。

### ジョブのリスト

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![データ ソースを一覧表示している Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### ジョブの詳細の取得

  	azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"
	
### ジョブの送信

> [AZURE.NOTE] ジョブの既定の優先度は 1000 で、ジョブの並列処理の既定の次数は 1 です。

	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### ジョブの取り消し

list コマンドを使用してジョブ ID を検索した後、cancel を使用してそのジョブを取り消します。

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## カタログの管理

U-SQL カタログを使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。詳細については、「[U-SQL カタログの使用](data-lake-analytics-use-u-sql-catalog.md)」を参照してください。
 
###カタログ項目のリスト

	#List databases
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

	#List tables
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table
	
タイプには、データベース、スキーマ、アセンブリ、externaldatasource、テーブル、tablevaluedfunction、または tablestatistics が含まれます。

###カタログ シークレットの作成

	azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### カタログ シークレットの変更

  	azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

###カタログ シークレットの削除

	azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## ARM グループの使用

アプリケーションは通常、Web アプリケーション、データベース、データベース サーバー、ストレージ、サード パーティのサービスなどの、複数のコンポーネントで構成されます。Azure リソース マネージャー (ARM) を使用すると、アプリケーション内の複数のリソースを 1 つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。

Data Lake Analtyics サービスには、次のコンポーネントを含めることができます。

- Azure Data Lake Analytics アカウント
- 必要な既定の Azure Data Lake Storage アカウント
- 追加の Azure Data Lake Storage アカウント
- 追加の Azure Storage アカウント

管理しやすくするために 1 つの ARM グループの下にこれらすべてのコンポーネントを作成することができます。

![Azure Data Lake Analytics のアカウントとストレージ](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics アカウントと従属するストレージ アカウントは同じ Azure データ センターに配置する必要があります。ただし、ARM グループは別のデータ センターに配置できます。


##関連項目 

- [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
- [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)
- [Azure ポータルを使用する Azure Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0504_2016-->