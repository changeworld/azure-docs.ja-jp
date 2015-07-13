<properties 
	pageTitle="Data Factory - リリース ノート | Azure" 
	description="Data Factory - リリース ノート" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Azure Data Factory リリース ノート

## Data Factory の 2015 年 04 月 10 日リリース ノート
**[テーブル]** ブレードに **[最近更新したスライス]** と **[最近失敗したスライス]** の一覧が表示されるようになりました。これらの一覧は、スライスの更新時間で並べ替えられます。次の状況では、スライスの更新時間が変更されます。

-  **Set-AzureDataFactorySliceStatus** を使用したり、スライスの **[スライス]** ブレードで **[実行]** をクリックしたりすることで、スライスの状態を手動で更新した場合。
-  スライスの実行 (実行の開始、実行の終了と失敗、実行の終了と成功など) により、スライスの状態が変わります。

一覧のタイトルをクリックするか、**[...] (省略記号)** をクリックすると、さらに多くのスライスが一覧表示されます。スライスをフィルター処理するには、ツールバーの **[フィルター]** をクリックします。
 
**[データ スライス (スライスの時刻別)]** タイルをクリックして、スライスを時間別に並べ替えて表示することもできます。これらのコレクションのスライスは、スライスの時間で並べ替えられます。たとえば、1 時間ごとのスケジュールである場合、スライスは次のようになります: 4/4/2015 5pm 処理中 - 4/4/2015 4pm 成功 - 4/4/2015 3pm 失敗

ただし、使用していないスライスを再実行した場合、ユーザーがそのスライスに最も関心がある場合でも、そのスライスは一覧の先頭に表示されません。

## Data Factory の 2015 年 3 月 31 日リリース ノート
- 更新された **Data Management Gateway** インストール パッケージが [Microsoft ダウンロード センター][adf-gateway-download]に投稿されました。
- **オンプレミスのファイル システムから Azure BLOB への**コピーがサポートされるようになりました。詳細については、次のトピックを参照してください。
	-  [オンプレミス ファイル システムのリンクされたサービス](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [JSON テーブルの OnPremisesFileSystemLocation プロパティ](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx)更新されたコピー マトリックスと **FileSystemSource** プロパティを参照してください。 
-  **オンプレミスの Oracle データベースから Azure BLOB への**コピーがサポートされるようになりました。詳細については、次のトピックを参照してください。 
	-  [オンプレミスの Oracle のリンクされたサービス](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [JSON テーブルの OnPremisesOracleTableLocation プロパティ](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx)更新されたコピー マトリックスと **OracleSource** プロパティを参照してください。
-  Azure BLOB 内のテキスト ファイルのエンコードを指定できます。新しい [encodingName プロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)を参照してください。 
- SQL シンクにコピーするときに、追加のパラメーターを使用してストアド プロシージャを呼び出すことができます。詳細については、「[SQL シンクのストアド プロシージャの呼び出し][adf-copy-advanced]」を参照してください。   

例を含む追加情報については、[Azure Data Factory の更新 - 新しいデータ ストア](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/)に関する BLOB の投稿を参照してください。

## Data Factory の 2015 年 2 月 27 日リリース ノート

### 新機能
- **Azure Data Factory エディター**Azure プレビュー ポータルの一部である Data Factory エディターでは、リンクされたサービス、データ セット、パイプラインを定義する JSON ファイルを作成、編集、デプロイできます。このエディターの主な目的は、高速かつ軽量のユーザー インターフェイス (UI) を使用して、Azure PowerShell をインストールしたり PowerShell コマンドレットを導入せずに Azure Data Factory アーティファクトを作成することです。Data Factory エディターの概要とビデオについては、[Azure Data Factory エディター - 軽量の Web エディター][adf-editor-blog]に関するブログの投稿を参照してください。Data Factory エディターの詳細については、記事「[Data Factory エディター][adf-editor]」を参照してください。          

### 変更点

## Data Factory の 2015 年 1 月 26 日リリース ノート ##

### 変更点
- 更新された **Data Management Gateway** インストール パッケージが [Microsoft ダウンロード センター][adf-gateway-download]に投稿されました。今回のリリース以降、このダウンロード元から Azure Data Factory で使用できる最新の Data Management Gateway を検索できるようになりました。このインストール パッケージは、Azure Data Factory と Power BI for Office 365 サービスの両方で機能します。両方のサービスを使用している場合は、Data Factory と Power BI のゲートウェイを別のコンピューターにインストールし、Data Factory または Power BI のドキュメントのガイダンスに従って個別に構成する必要があります。
- **コピー アクティビティ**で、オンプレミスの SQL Server データベースと Azure SQL Database の間でのデータのコピーがサポートされるようになりました。詳細については、[コピー アクティビティ][adf-copy-activity]に関するページを参照してください。JSON のサンプルについては [GitHub][adf-github-samples] を参照してください。
- **SqlSink** で新しいプロパティ **WriteBatchTimeout** がサポートされるようになりました。このプロパティを使用すると、操作がタイムアウトになる前にバッチ挿入操作が完了するまでの待機時間を柔軟に構成できます。ハイブリッド コピー (オンプレミスのデータ ソースとクラウドのデータ ソースが関連するコピー操作) の場合、このプロパティを使用するには 1.4 以上のバージョンのゲートウェイが必要です。 
- **SQL Server のリンクされたサービス** で、**Windows 認証**がサポートようになりました。 
	- ポータルを使用して SQL Server のリンクされたサービスを作成する場合、Windows 認証を使用して適切な資格情報を設定できるようになりました。この設定には 1.4 以上のバージョンのゲートウェイが必要です。 
	- Azure PowerShell を使用して SQL Server のリンクされたサービスを作成する場合、プレーン テキストで接続情報を指定するか、更新された [New-AzureDataFactoryEncryptValue コマンドレット][adf-encrypt-value-cmdlet]を使用して接続情報を暗号化したうえで、リンクされたサービスの JSON ペイロードの接続文字列プロパティに、暗号化された文字列を使用できます。JSON でリンクされたサービスを定義する方法については、「[リンクされたサービス][adf-msdn-linked-services]」を参照してください現時点では、暗号化機能は New-AzureDataFactoryEncryptValue コマンドレットでサポートされていません。 

## Data Factory の 2014 年 12 月 11 日リリース ノート ##

### 新機能

- Azure Machine Learning 統合
	- Azure Data Factory サービスのこのリリースでは、**AzureMLLinkedService** と **AzureMLBatchScoringActivity** を使用して、Azure Data Factory と Azure Machine Learning (ML) を統合できます。詳細については、「[Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する][adf-azure-ml]」を参照してください。 
- ゲートウェイ バージョンのステータスが提供される
	- 現在インストールされているゲートウェイよりも新しいバージョンのゲートウェイを使用できる場合は、"NewVersionAvailable" ステータスが Azure プレビュー ポータルおよび Get-AzureDataFactoryGateway コマンドレットの出力に表示されます。ポータルの指示に従って新しいインストール ファイル (.msi) をダウンロードし、これを実行して最新のゲートウェイをインストールできます。追加の構成は必要ありません。

### 変更点

- HdInsightOnDemandLinkedService 内の JobsContainer が削除されました。
	- HDInsightOnDemandLinkedService の JSON 定義で、**jobsContainer** プロパティを指定する必要がありません。オンデマンドのリンクされたサービスで指定したプロパティがある場合、そのプロパティは無視されます。リンクされたサービスの JSON 定義からそのプロパティを削除し、New-AzureDataFactoryLinkedService コマンドレットを使用してリンクされたサービスの定義を更新できます。
- HDInsightOnDemandLinkedService のオプションの構成パラメーター
	- このリリースでは、HDInsightOnDemandLinked (オンデマンドの HDInsight クラスター) 用の複数のオプションの構成パラメーターのサポートが導入されています。詳細については、「[ClusterCreateParameters プロパティ][on-demand-hdi-parameters]」を参照してください。
- ゲートウェイの場所が削除される
	- ポータルまたは PowerShell (New-AzureDataFactoryGateway) 経由で Azure Data Factory ゲートウェイを作成する場合は、ゲートウェイの場所を指定する必要がありません。Data Factory 領域は継承されます。同様に、JSON を使用して SQL Server のリンクされたサービスを構成する場合、"gatewayLocation" プロパティは必要ありません。Data Factory .NET SDK もこれらの変更点を反映して更新されます。
	- 古いバージョンの SDK および Azure PowerShell を使用している場合は、引き続き場所を設定する必要があります。
 
     

#### 重大な変更
	
- CustomActivity から DotNetActivity へ
	- **ICustomActivity** インターフェイスの名前が **IDotNetActivity** に変更されました。Data Factory NuGet パッケージを更新し、カスタム アクティビティのソース コードで、ICustomActivity を IDotNetActivity に変更する必要があります。  
	- カスタム アクティビティの JSON 定義におけるカスタム アクティビティの種類を、**CustomActivity** から **DotNetActivity** に変更する必要があります。 
	- **CustomActivity** と **CustomActivityProperties** クラスの名前は、それぞれ、**DotNetActivity** と **DotNetActivityProperties** に変更されました （プロパティ セットは変わりません)。

		古いバージョンの SDK および Azure PowerShell を使用している場合は、DotNetActivity の代わりに、引き続き CustomActivity を使用できます。
    
  		カスタム アクティビティを作成し、これを Azure Data Factory パイプラインで使用する方法のチュートリアルは、「[Azure Data Factory パイプラインでカスタム アクティビティを使用する][adf-custom-activities]」を参照してください。

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=62-->