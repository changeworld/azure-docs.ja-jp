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
	ms.date="09/08/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - よく寄せられる質問

## 一般的な質問

### Azure Data Factory とは何ですか。

Data Factory は、データの移動や変換を調整し自動化するクラウドベースのデータ統合サービスです。原材料を機械で加工して最終製品を作成する工場と同じように、Data Factory は生データを収集してすぐに使用できる情報に変換する既存のサービスを調整します。

Data Factory は、オンプレミスとクラウドのデータ ソースおよび SaaS で動作し、データの取り込み、準備、変換、分析、および発行を行います。Data Factory を使えば、各種のサービスを管理されたデータ フロー パイプラインにまとめ上げることによって、ビッグ データのコンピューティング ニーズに応える [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) や [Azure Batch](http://azure.microsoft.com/documentation/services/batch/) のほか、分析ソリューションを運用可能にするための [Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/) などを使ったデータの変換を実現できます。単なる表形式の監視ビューではなく、Data Factory のリッチな視覚化機能を使用してデータ パイプライン間の系列と依存関係をすばやく表示します。1 つの統一されたビューからすべてのデータ フロー パイプラインを監視し、問題を簡単に特定して監視アラートを設定します。

詳細については、「[概要と主要な概念](data-factory-introduction.md)」を参照してください。
 
### Data Factory では顧客のどのような課題が解決されますか。

Azure Data Factory は、従来のリレーショナル ストレージと非構造化データを対象とした多様なデータ保存、処理、および移動のサービスを機敏に活用できると共に、完全に管理されたサービスの制御と監視の機能を提供します。

### Data Factory の対象ユーザーはだれですか。


- Hadoop とその他のシステム間における統合サービスの構築を担当しているデータ開発者。
	- 絶えず変化と拡大を続けるデータ環境への対応と統合を行う必要がある。
	- 情報生成システムがカスタム コードの記述を必要とし、コストもかかり、保守が難しく、高可用性やフォールト トレランスを提供していない。

- 多様化するデータを IT インフラストラクチャに組み込む方法を探している IT プロフェッショナル。
	- 組織内のすべてのデータから豊富なビジネスの洞察を導き出す必要がある。
	- コンピューティングとストレージのリソースを管理し、オンプレミスとクラウドの間でコストと規模のバランスをとる必要がある。
	- 新しいビジネス ニーズに対応するためにさまざまなソースと処理を短時間で追加しつつ、すべてのコンピューティング資産とストレージ資産の全体像を常に把握する必要がある。

### Azure Data Factory の料金の詳細はどこで確認できますか。

Azure Data Factory の料金の詳細については、[Data Factory の料金詳細][adf-pricing-details]に関するページをご覧ください。

### Azure Data Factory の利用はどのように開始するのですか。

- Azure Data Factory の概要については、[Azure Data Factory サービスの概要][adf-introduction]に関するページをご覧ください。
- クイック チュートリアルについては、「[Azure Data Factory を使ってみる][adfgetstarted]」をご覧ください。
- 包括的なドキュメントについては、[Azure Data Factory のドキュメント][adf-documentation-landingpage]に関するページをご覧ください。

  
### 顧客は Data Factory にどのようにアクセスしますか。

お客様は [Azure プレビュー ポータル][azure-preview-portal]を通じて Data Factory にアクセスできます。

### Data Factory を利用可能な地域はどこですか。

Data Factory は米国西部と北ヨーロッパで使用できます。Data Factory で使用されるコンピューティング サービスとストレージ サービスは、その他の地域でも利用できます。
 
### Data Factory/パイプライン/アクティビティ/データセットの数の制限値はいくつですか。
 
詳細については、記事「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md#data-factory-limits)」の **Azure Data Factory の制限**に関するセクションを参照してください。


### Azure Data Factory サービスではどのような作成エクスペリエンスまたは開発者エクスペリエンスが提供されますか。

データ ファクトリを生成または作成する方法には、以下のものがあります。

- **Azure プレビュー ポータル**。Azure プレビュー ポータルの Data Factory ブレードは、データ ファクトリ、およびリンクされたサービスを作成するための優れたユーザー インターフェイスです。**Data Factory エディター**は、ポータルの一部でもあり、これらの成果物の JSON の定義を指定することによって、リンクされたサービス、テーブル、データ セット、パイプラインを簡単に作成できる機能です。エディターの概要については、[Data Factory エディター][data-factory-editor]に関するページを、データ ファクトリを作成してデプロイするためのポータルやエディターの使用例については、[Data Factory の使用][datafactory-getstarted]に関するページをご覧ください。   
- **Azure PowerShell**PowerShell ユーザーがポータル UI ではなく PowerShell の使用を希望している場合は、Azure PowerShell の一部として出荷される Azure Data Factory コマンドレットを使用してデータ ファクトリを作成し、デプロイできます。単純な例については、[Azure PowerShell を使用した Azure データ ファクトリの作成と監視][create-data-factory-using-powershell]に関するページをご覧ください。PowerShell コマンドレットを使用してデータ ファクトリを作成してデプロイする高度な例については、[Data Factory を使用したログ ファイルの移動と処理][adf-tutorial]に関するチュートリアルをご覧ください。Data Factory コマンドレットの包括的なドキュメントについては、[MSDN ライブラリの Data Factory コマンドレット リファレンス][adf-powershell-reference]に関するコンテンツをご覧ください。  
- **Visual Studio**。また、Visual Studio を使用して、プログラムによりデータ ファクトリを作成、監視、管理できます。詳細については、記事「[Data Factory .NET SDK を使用して Azure データ ファクトリを作成、監視、管理する](data-factory-create-data-factories-programmatically)」を参照してください。  
- **.NET クラス ライブラリ**。Data Factory .NET SDK を使用すると、プログラムでデータ ファクトリを作成できます。.NET SDK を使用したデータ ファクトリの作成のチュートリアルについては、[.NET SDK を使用したデータ ファクトリの作成、監視、管理][create-factory-using-dotnet-sdk]に関するページをご覧ください。Data Factory .NET SDK の包括的なドキュメントについては、[Data Factory クラス ライブラリ リファレンス][msdn-class-library-reference]に関するページをご覧ください。  
- **REST API**。Azure Data Factory サービスで公開されている REST API を使用して、データ ファクトリを作成およびデプロイすることもできます。Data Factory REST API の包括的なドキュメントについては、[Data Factory REST API リファレンス][msdn-rest-api-reference]に関するページをご覧ください。 

### Data Factory の名前を変更できますか。
いいえ。他の Azure のリソースと同様に、Azure Data Factory の名前は変更できません。

## アクティビティ - FAQ
### どのようなデータ ソースとアクティビティがサポートされますか。

サポートされるデータ ソースとアクティビティについては、[データ移動のアクティビティ](data-factory-data-movement-activities.md)に関する記事と、[データ変換のアクティビティ](data-factory-data-transformation-activities.md)に関する記事を参照してください。

### アクティビティはいつ実行されますか。
アクティビティが実行されるタイミングは、出力データ テーブルの **availability** 構成設定によって決定されます。アクティビティは、実行を開始する前に、入力データのすべての依存関係が満たされている (つまり、**準備完了**状態になっている) かどうかを確認します。

## コピー アクティビティ - FAQ
### 1 つのパイプラインに複数のアクティビティを設定する方法とアクティビティごとに別個のパイプラインを使用する方法ではどちらの方法がよいですか。 
パイプラインでは、関連する複数のアクティビティをまとめることが想定されています。論理的には、複数のアクティビティを接続するテーブルがパイプラインの外部の他のアクティビティによって使用されない場合は、1 つのパイプラインにそれらのアクティビティを保持できます。これにより、パイプラインのアクティブな期間を揃えるためにこれを連結する必要はありません。また、パイプライン内部のテーブルのデータの整合性が、パイプラインを更新するときによりうまく保持されるようになります。パイプラインを更新するとき、基本的にパイプライン内のすべてのアクティビティが停止、削除された後、もう一度作成されます。作成操作の観点では、パイプラインの 1 つの JSON ファイルで関連するアクティビティ内のデータのフローを簡単に見ることができるようになる可能性があります。

## HDInsight アクティビティ - FAQ

### HDInsight でサポートされているリージョンはどこですか。

次の記事の利用可能な地域に関するセクションをご覧ください。[HDInsight の料金詳細][hdinsight-supported-regions]

### オンデマンドの HDInsight クラスターはどの地域で使用されますか。

オンデマンドの HDInsight クラスターは、クラスターで使用するように指定したストレージが存在するのと同じ地域に作成されます。

### 追加のストレージ アカウントを HDInsight クラスターに関連付けるにはどうすればよいですか。

独自の HDInsight クラスター (BYOC - Bring Your Own Cluster: クラスターを自分で用意する) を使用している場合は、次のトピックをご覧ください。

- [代替のストレージ アカウントとメタストアでの HDInsight クラスターの使用][hdinsight-alternate-storage]
- [HDInsight ハイブでの追加のストレージ アカウントの使用][hdinsight-alternate-storage-2]

出荷時のデータ サービスによって作成された、オンデマンドでクラスターを使用している場合は、HDInsight 用の追加のストレージ アカウントにリンクされるので、データのファクトリのサービスが自動的に登録できます考慮すべきサービスを指定する必要があります。オンデマンドのリンクされたサービスの JSON 定義内で、次の JSON のスニペットに示すように、**additionalLinkedServiceNames** プロパティを使用して、代替のストレージ アカウントを指定します。
 
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

上記の例において、otherLinkedServiceName1 と otherLinkedServiceName2 は、HDInsight クラスターが代替ストレージ アカウントにアクセスするために必要な資格情報がその定義に含まれているリンクされたサービスを表します。

## スライス - FAQ

### スライスを再実行するにはどうすればよいですか。
スライスを再実行するには、次のどちらかの方法を使用します。

- ポータルのスライスの **[データ スライス]** ブレードで、コマンド バーの **[実行]** をクリックします。 
- **Set-AzureDataFactorySliceStatus** を実行して、スライスの状態を **PendingExecution** に設定します   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

コマンドレットの詳細については、[Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] に関するページをご覧ください。

### スライスの処理にかかった時間を調べるにはどうすればよいですか。
1. データ ファクトリの **[データ ファクトリ]** ブレードの **[データセット]** タイルをクリックします。
2. **[データセット]** ブレードの特定のデータセットをクリックします。
3. **[テーブル]** ブレードの **[最近使用したスライス]** ボックスの一覧で、目的のスライスを選択します。
4. **[データ スライス]** ブレードの **[アクティビティの実行]** ボックスの一覧で、[アクティビティの実行] をクリックします。 
5. **[アクティビティの実行の詳細]** ブレードで、**[プロパティ]** タイルをクリックします。 
6. **[期間]** フィールドに表示されている値を確認します。これが、スライスの処理にかかった時間です。   

### 実行中のスライスを停止するにはどうすればよいですか
パイプラインの実行を停止する必要がある場合は、[Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) コマンドレットを使用できます。現時点では、パイプラインを中断しても、進行中のスライスの実行は停止しません。進行中の実行が完了すると、追加のスライスは取得されません。

すべての実行をすぐに停止するには、パイプラインをいったん削除した後で再作成するしかありません。パイプラインを削除する場合は、パイプラインによって使用されているテーブルとリンクされたサービスを削除する必要はありません。



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
 

<!---HONumber=Nov15_HO3-->