<properties 
	pageTitle="Azure Data Factory - よく寄せられる質問" 
	description="Azure データ ファクトリについてよく寄せられる質問です。" 
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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - よく寄せられる質問

## 一般的な質問

### Q: Azure のデータのファクトリとは

Data Factory は、完全に管理されたサービスで、データの保存、移動、処理の各サービスを可用性が高くフォールト トレラントなデータ パイプラインとして構成します。Data Factory は内部設置型とクラウドの両方のデータ ストレージで動作します。パイプラインは、一連のデータ入力、処理アクティビティ、およびデータ出力のセットで、シンプルな JSON スクリプトで定義され、PowerShell コマンドを使用してアクティブ化されます。アクティブ化された Data Factory は、ユーザーに代わり、自動クラスター管理のオプションを使用して、パイプラインを HDInsight (Hadoop) 上で実行するための編成とスケジュール設定を行います。また、Data Factory では、Azure プレビュー ポータルによって管理と監視をビジュアルな環境で実行できます。運用とサービスの正常性に関する豊富な情報が表示される単一のダッシュボードですべてのパイプラインを監視できます。
 
### Q: どのようなお客様の課題は、データのファクトリを解決でしょうか。

Azure Data Factory は、従来のリレーショナル ストレージと非構造化データを対象とした多様なデータ保存、処理、および移動のサービスを機敏に活用できると共に、完全に管理されたサービスの制御と監視の機能を提供します。

### Q: データのファクトリの対象はでしょうか。


- Hadoop とその他のシステム間における統合サービスの構築を担当しているデータ開発者。
	- 絶えず変化と拡大を続けるデータ環境への対応と統合を行う必要がある。
	- 情報生成システムがカスタム コードの記述を必要とし、コストもかかり、保守が難しく、高可用性やフォールト トレランスを提供していない。

- 多様化するデータを IT インフラストラクチャに組み込む方法を探している IT プロフェッショナル。
	- 組織内のすべてのデータから豊富なビジネスの洞察を導き出す必要がある。
	- コンピューティングとストレージのリソースを管理し、内部設置型とクラウドの間でコストと規模のバランスをとる必要がある。
	- 新しいビジネス ニーズに対応するためにさまざまなソースと処理を短時間で追加しつつ、すべてのコンピューティング資産とストレージ資産の全体像を常に把握する必要がある。

###  料金を検索できる場所を q: Azure のデータのファクトリの詳細ですか。

参照してください [データ工場出荷時の料金の詳細ページ][adf-pricing-details] の Azure の [データ工場出荷時の料金の詳細。

### Q.方法は概要 Azure のデータのファクトリで

- Azure のデータのファクトリの概要については、次を参照してください。 [Azure のデータのファクトリの概要][adf-introduction]です。
- クイック チュートリアルについては、次を参照してください。 [Azure のデータのファクトリを使い始める][adfgetstarted]です。
- 包括的なドキュメントについては、次を参照してください。 [Azure のデータのファクトリ ドキュメント][adf-documentation-landingpage]です。

  
### Q: 顧客アクセスするにはデータ ファクトリでしょうか。

顧客からのデータのファクトリへのアクセスを取得できます、 [Azure プレビュー ポータル][azure-preview-portal]です。

### Q: データのファクトリの利用可能な地域とは

パブリック プレビューの時点では、Data Factory は米国西部でのみ提供されます。Data Factory で使用されるコンピューティング サービスとストレージ サービスは、その他の地域でも利用できます。
 
### Q: データ ファクトリ/パイプライン/アクティビティとデータセットの数の制限とは 


- サブスクリプション内のデータのファクトリの数: 50
- データのファクトリ内からパイプライン数: 100
- パイプライン内でのアクティビティの数: 10
- によるデータセットのデータのファクトリでの数: 100

### Q: Azure のデータのファクトリのサービスを使用して、作成または開発者エクスペリエンスとは

データ ファクトリを生成または作成する方法には、以下のものがあります。

- **Azure プレビュー ポータル**です。Azure プレビュー ポータルの Data Factory ブレードは、データ ファクトリ、およびリンクされたサービスを作成するための優れたユーザー インターフェイスです。 **データ工場出荷時のエディター**, 、これは、ポータルの一部でも、これらの成果物の JSON の定義を指定することによって、関連付けられているサービス、テーブル、データのセット、およびパイプラインを簡単に作成できます。参照してください [データ工場出荷時のエディター][data-factory-editor] 、エディターの概要についてと [データ ファクトリの][datafactory-getstarted] 、ポータルとエディターを使用して作成し、データのファクトリの展開の例についてはします。   
- **Azure PowerShell**PowerShell ユーザーがポータル UI ではなく PowerShell の使用を希望している場合は、Azure PowerShell の一部として出荷される Azure Data Factory コマンドレットを使用してデータ ファクトリを作成し、デプロイできます。参照してください [の作成と Azure PowerShell を使用して、Azure データ工場出荷時のモニター][create-data-factory-using-powershell] 簡単な例についてと [チュートリアル: 移動し、データのファクトリを使用してログ ファイルの処理を][adf-tutorial] cmdles ad を作成する PowerShell を使用しての高度な例については、データのファクトリを展開します。参照してください [データ工場出荷時のコマンドレット リファレンス][adf-powershell-reference] データ工場出荷時のコマンドレットの包括的なドキュメントの MSDN ライブラリでコンテンツ。  
- **.NET クラス ライブラリ**です。Data Factory .NET SDK を使用すると、プログラムでデータ ファクトリを作成できます。参照してください [作成、監視、および .NET SDK を使用してデータのファクトリを管理する][create-factory-using-dotnet-sdk] .NET SDK を使用してデータ ファクトリの作成のチュートリアルについてです。参照してください [データ ファクトリ クラス ライブラリ リファレンス][msdn-class-library-reference] データ工場出荷時の .NET SDK の包括的なドキュメントのです。  
- **REST API**です。Azure Data Factory サービスで公開されている REST API を使用して、データ ファクトリを作成およびデプロイすることもできます。参照してください [データ工場出荷時の REST API リファレンス][msdn-rest-api-reference] データ工場出荷時の REST API の包括的なドキュメントのです。 

## アクティビティ: よく寄せられる質問
### Q: サポートされているデータ ソースとアクティビティとは

- **サポートされているデータ ソース:** Azure ストレージ (Blob およびテーブル)、SQL Server、Azure SQL データベース、ファイル システム、Oracle データベース。
- **アクティビティのサポート:**: コピー アクティビティ (オンプレミスからクラウドおよびクラウドからオンプレミス)、HDInsight アクティビティ (Pig、Hive、MapReduce Hadoop ストリーミング変換)、Azure マシン ラーニング バッチ スコアリング アクティビティ、ストアド プロシージャのアクティビティ、および c# のカスタム アクティビティです。

### アクティビティはときに実行するのでしょうか。
 **可用性** 、出力データのテーブルの構成設定では、アクティビティを実行するとによって異なります。アクティビティは、入力データのすべての依存関係が満たされるかどうかを確認します。 (つまり、 **準備ができて** 状態) の実行を開始する前にします。

## 利用状況 - よく寄せられる質問をコピーします。
### Q: どのような領域は、コピーのアクティビティでサポートされてでしょうか。

次の地域へのデータ コピーがコピー アクティビティ サポートされています。 米国東部、米国東部 2、米国西部、米国、米国中北部、米国中南部、北ヨーロッパ、西ヨーロッパ、および東南アジア。

その他の地域へのデータ コピーもサポートされて、データのルーティング用の上、リージョンのいずれかを使用しています。コピー操作は、データがルーティングされた地域に基づいて課金されます。

コピー先の地域 | ルーティングに使用する領域
-------------------------- | -----------------------
東アジア | 東南アジア
東日本 | 米国西部
西日本 | 米国西部
ブラジル南部 | 米国東部 2

### 複数の出力テーブルにコピーする方法
次の例に示すように、パイプラインに複数の出力テーブルことができます。

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### 最適なパイプラインを複数のアクティビティまたはアクティビティごとに別々 のパイプラインが必要ですか。 
パイプラインは、関連アクティビティをバンドルすると想定されます。論理的には、それらを接続するテーブルが、パイプラインの外部の他のアクティビティによって消費されていない場合は 1 つのパイプラインで、アクティビティを保持できます。これにより、必要はありませんチェーンのパイプラインのアクティブな期間を互いに対応できるようにします。また、パイプラインに内部テーブルにデータの整合性より保持されます、パイプラインを更新する場合。パイプラインの更新が基本的に、パイプライン内のすべてのアクティビティを停止、削除され、およびもう一度これらを作成します。パースペクティブを作成から簡単に、パイプラインの 1 つの JSON ファイルには、関連のアクティビティ内のデータのフローを参照してください。 その必要もあります。

## HDInsight の利用状況 - よく寄せられる質問

### Q: どのような領域は、HDInsight ではサポートされているでしょうか。

次の記事では、地理的な可用性のセクションを参照してください。 または [HDInsight の料金詳細][hdinsight-supported-regions]です。

### Q: どのような領域は、オンデマンドでの HDInsight クラスターで使用ですか。

オンデマンドの HDInsight クラスターは、クラスターで使用するように指定したストレージが存在するのと同じ地域に作成されます。

### Q: HDInsight クラスターへの追加のストレージ アカウントを関連付ける方法でしょうか。

独自の HDInsight クラスター (BYOC - 独自クラスターを表示) を使用している場合は、次のトピックを参照してください。

- [HDInsight クラスターを使用して、代替のストレージ アカウントとメタストア][hdinsight-alternate-storage]
- [追加のストレージ アカウントを使用して、HDInsight Hive に][hdinsight-alternate-storage-2]

出荷時のデータ サービスによって作成された、オンデマンドでクラスターを使用している場合は、HDInsight 用の追加のストレージ アカウントにリンクされるので、データのファクトリのサービスが自動的に登録できます考慮すべきサービスを指定する必要があります。オンデマンドでリンクされているサービスの JSON の定義で次のように使用します。 **additionalLinkedServiceNames** プロパティを、次の JSON のスニペットに示すように、代替のストレージ アカウントを指定します。
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

上記の例では otherLinkedServiceName1 と otherLinkedServiceName2 定義を持つには、HDInsight クラスターの別のストレージ アカウントにアクセスする必要がある資格情報が含まれている、関連付けられているサービスを表します。

## ストアド プロシージャの利用状況 - よく寄せられる質問
### ストアド プロシージャ アクティビティではどのようなデータ ソースをサポートするでしょうか。
ストアド プロシージャ アクティビティは、この時点で Azure SQL データベースのみをサポートします。

## スライスのよく寄せられる質問

### スライスを再実行する方法
次の方法のいずれかのスライスを再実行できます。

- をクリックして **実行** 上にあるコマンド バーで、 **データ スライス** ブレードで、ポータルでスライスします。 
- 実行 **セット AzureDataFactorySliceStatus** コマンドレットの状態と設定 **PendingExecution** 、スライスします。   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

参照してください [セット AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] 詳細については、コマンドレットに関するします。

### スライスを処理するのにはどのくらいの時間がかかりますか。
1. クリックして **データセット** タイルで、 **データ ファクトリ** ブレードで、データのファクトリです。
2. 特定のデータセットをクリックして、 **データセット** ブレードします。
3. スライスから興味のあるを選択、 **最近使用したスライス** ボックスの一覧で、 **テーブル** ブレードします。
4. 実行するアクティビティをクリックして、 **アクティビティの実行** ボックスの一覧で、 **データ スライス** ブレードします。 
5. クリックして **プロパティ** タイルで、 **アクティビティの実行の詳細** ブレードします。 
6. 表示されます、 **期間** フィールドの値です。これは、スライスの処理にかかる時間です。   

### 実行中のスライスを停止する方法でしょうか。
使用することができます、パイプラインが実行を停止する必要がある場合 [中断 AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) コマンドレットです。現時点では、中断、パイプラインは、進行中のスライスの実行は停止しません。処理の実行中の実行が完了余分なスライスが取得されません。

実際には、すべての実行をすぐに停止する場合は、唯一の方法に、パイプラインを削除し、再作成になります。パイプラインを削除することを選択する場合は、テーブルと、パイプラインによって使用される関連付けられているサービスを削除する必要はありません。



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

<!---HONumber=GIT-SubDir--> 