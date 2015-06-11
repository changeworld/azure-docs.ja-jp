<properties 
	pageTitle="Data Factory - リリース ノート | Azure" 
	description="データの工場出荷時のリリース ノート" 
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

## データのファクトリの 04/10/2015年のリリース ノート
表示されます、 **スライスを最近更新された** と **最近スライスが失敗しました** 上に一覧表示、 **テーブル** ブレードのようになりました。これらのリストは、スライスの更新時間によって並べ替えられます。次の状況では、スライスの更新時間が変更されます。

-  スライスのステータスは、手動で更新するなどを使用して、 **セット AzureDataFactorySliceStatus** (または) をクリックして **実行** 上、 **スライス** スライスのブレードします。
-  スライスは、実行のための状態を変更 (など、実行の開始、実行を終了し、失敗した、実行が成功したが終了したなど)。

リストまたはのタイトルをクリックして **しています.(省略記号)** より大規模な一覧のスライスを参照してください。クリックして **フィルター** をスライス、フィルター処理するには、ツールバーのです。
 
クリックして、スライスの時間を並べ替えてスライスをでも表示できますが **(スライスの時刻) でのデータ スライス** 並べて表示します。これらのコレクションに、スライスは、スライスの時間で並べ替えられます。次の例では、1 時間ごとである場合、スライスになります - 4/4/2015 2015 4/4/4 の進行状況の午後 5 時の pm"成功"の 4/4/2015 3 pm 失敗。

しかし場合は、古いスライスが再実行され、それは表示されませんこの一覧の上部にある場合でも、ユーザーが興味はおそらく。

## データのファクトリの 3/31/2015年のリリース ノート
- 更新された **Data Management Gateway** インストール パッケージにポストした [Microsoft ダウンロード センター][adf-gateway-download]です。
- コピー **ファイルを Azure の blob のシステムの内部設置型** は現在サポートされています。詳細については、次のトピックを参照してください。
	-  [内部設置型サービスがファイル システムにリンクされています。](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [テーブル JSON の OnPremisesFileSystemLocation プロパティ](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [ソースおよびシンクのサポート](https://msdn.microsoft.com/library/dn894007.aspx)です。更新されたコピーは、マトリックスを参照してくださいと **FileSystemSource** プロパティです。 
-  コピー **内部設置型の Oracle データベースを Azure の blob に** は現在サポートされています。詳細については、次のトピックを参照してください。 
	-  [Oracle の内部設置型サービスのリンク](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [テーブル JSON の OnPremisesOracleTableLocation プロパティ](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [ソースおよびシンクのサポート](https://msdn.microsoft.com/library/dn894007.aspx)です。更新されたコピーは、マトリックスを参照してくださいと **OracleSource** プロパティです。
-  Azure Blob 内のテキスト ファイルのエンコードを指定することができます。新しい [encodingName プロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)です。 
- SQL Sink にコピーするときに、追加のパラメーターを使用してストアド プロシージャを呼び出すことができます。参照してください [SQL シンクのストアド プロシージャを呼び出す][adf-copy-advanced] の詳細。   

投稿の blob を参照してください: [Azure のデータ工場出荷時の更新プログラム データ ストアの新しい](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) の例を含む追加の情報です。

## データの工場出荷時の 2/27/2015年のリリース ノート

### 新機能
- **Azure データ工場出荷時のエディター**です。Azure プレビュー ポータルの一部である、データ、工場出荷時エディターでは、作成、編集、および関連付けられているサービス、データのセット、およびパイプラインを定義する JSON ファイルを配置することができます。エディターの主な目的では、高速で軽量ユーザー インターフェイス (UI) を PowerShell コマンドレットを使用することで Azure PowerShell とごとの傾斜増加をインストールすることがなく Azure のデータのファクトリのアイテムを作成します。参照してください、 [Azure データ工場出荷時のエディター - ライト重み Web エディター][adf-editor-blog] 簡単な概要と、ビデオの [データの工場出荷時のエディターのブログの投稿します。エディターの詳細については、次を参照してください。、 [データ工場出荷時のエディター][adf-editor] 記事です。          

### 変更点

## データのファクトリの 2015 26/1/リリース ノート ##

### 変更点
- 更新された **Data Management Gateway** インストール パッケージにポストした [Microsoft ダウンロード センター][adf-gateway-download]です。今回のリリース以降、このダウンロードの場所での Azure データのファクトリで使用する最新の Data Management Gateway を検索できます。このインストール パッケージは Office 365 サービスの Azure データ ファクトリと Power BI の両方が機能します。両方のサービスを使用している場合は、データのファクトリを Power BI のゲートウェイを別々 のコンピューターにインストールされているし、データ ファクトリまたは Power BI のドキュメントからのガイダンスに従って構成を変更する必要があることに注意してください。
-  **コピー アクティビティ** 内部設置型 SQL Server データベースと Azure SQL データベースとの間でデータのコピー サポートようになりました。参照してください [コピー アクティビティ][adf-copy-activity] の詳細と [GitHub][adf-github-samples] JSON のサンプルについてです。
- **SqlSink** 新しいプロパティをサポートしています。 **WriteBatchTimeout**です。このプロパティを使用すると、操作がタイムアウトになる前に完了するには、バッチ挿入操作の待機する期間を構成するには柔軟性です。ハイブリッド コピー (コピー操作を内部設置型のデータ ソースと、クラウドのデータ ソースが含まれます) のこのプロパティを使用して 1.4 以降のバージョンのゲートウェイが必要です。 
- **サービスが SQL Server にリンクされている** ようになりました **Windows 認証**です。 
	- SQL サーバーを作成するリンクされると、ポータルを使用してサービスを今すぐ Windows 認証を使用し、適切な資格情報の設定を選択できます。1.4 以降のバージョンのゲートウェイが必要です。 
	- SQL サーバーを作成する Azure PowerShell を使用してサービス リンクされると、プレーン テキストで接続情報を指定または更新を使用して接続情報を暗号化できます [新規 AzureDataFactoryEncryptValue コマンドレット][adf-encrypt-value-cmdlet] し、暗号化された文字列を使用して、リンクされているサービスの JSON ペイロードでは、接続文字列のプロパティのです。参照してください [関連付けられているサービス][adf-msdn-linked-services] JSON でリンクされているサービスを定義する方法についてです。暗号化機能は、新規 AzureDataFactoryEncryptValue コマンドレットではまだサポートされません。 

## データのファクトリの 12/11/2014年リリース ノート ##

### 新機能

- Azure Machine Learning 統合
	- このリリースの Azure のデータのファクトリのサービスには Azure のデータのファクトリを使用して Azure マシン ラーニング (ML) と統合することができます **AzureMLLinkedService** と **AzureMLBatchScoringActivity**です。参照してください [データ ファクトリ、および Azure の機械学習を使用した予測のパイプラインを作成する][adf-azure-ml] の詳細。 
- ゲートウェイ バージョンのステータスが提供される
	- 現在インストールされているゲートウェイよりも新しいバージョンのゲートウェイを使用できる場合は、"NewVersionAvailable" ステータスが Azure プレビュー ポータルおよび Get-AzureDataFactoryGateway コマンドレットの出力に表示されます。ポータルの指示に従って新しいインストール ファイル (.msi) をダウンロードし、これを実行して最新のゲートウェイをインストールできます。追加の構成は必要ありません。

### 変更点

- HdInsightOnDemandLinkedService 内の JobsContainer が削除されました。
	- HDInsightOnDemandLinkedService の JSON の定義では指定する必要はありません **jobsContainer** これ以上のプロパティです。オンデマンドのリンクされたサービスで指定したプロパティがある場合、そのプロパティは無視されます。リンクされたサービスの JSON 定義からそのプロパティを削除し、New-AzureDataFactoryLinkedService コマンドレットを使用してリンクされたサービスの定義を更新できます。
- HDInsightOnDemandLinkedService のオプションの構成パラメーター
	- このリリースでは、HDInsightOnDemandLinked (オンデマンドの HDInsight クラスター) 用の複数のオプションの構成パラメーターのサポートが導入されています。参照してください [ClusterCreateParameters プロパティ][on-demand-hdi-parameters] の詳細。
- ゲートウェイの場所が削除される
	- ポータルまたは PowerShell (New-AzureDataFactoryGateway) 経由で Azure Data Factory ゲートウェイを作成する場合は、ゲートウェイの場所を指定する必要がありません。Data Factory 領域は継承されます。同様に、JSON を使用して SQL Server のリンクされたサービスを構成する場合、"gatewayLocation" プロパティは必要ありません。Data Factory .NET SDK もこれらの変更点を反映して更新されます。
	- 古いバージョンの SDK および Azure PowerShell を使用している場合は、引き続き場所を設定する必要があります。
 
     

#### 重大な変更
	
- CustomActivity から DotNetActivity へ
	- **ICustomActivity** インターフェイスの名前に変更が **IDotNetActivity**です。Data Factory NuGet パッケージを更新し、カスタム アクティビティのソース コードで、ICustomActivity を IDotNetActivity に変更する必要があります。  
	- カスタム アクティビティの JSON 定義内でカスタム アクティビティの種類を変更する必要があります **CustomActivity** に **DotNetActivity**です。 
	-  **[Customactivity]** と **CustomActivityProperties** クラスが名前に変更されました **DotNetActivity** と **DotNetActivityProperties** 一連の同じプロパティを使用します。

		古いバージョンの SDK および Azure PowerShell を使用している場合は、DotNetActivity の代わりに、引き続き CustomActivity を使用できます。
    
  		参照してください [カスタム アクティビティを使用して、Azure のデータのファクトリのパイプラインで][adf-custom-activities] カスタム アクティビティを作成し、Azure のデータのファクトリのパイプラインで使用する方法のチュートリアルについてです。

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

<!---HONumber=GIT-SubDir--> 