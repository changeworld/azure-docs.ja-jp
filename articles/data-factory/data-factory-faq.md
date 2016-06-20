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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - よく寄せられる質問

## 一般的な質問

### Azure Data Factory とは何ですか。

Data Factory は、**データの移動や変換を自動化する**クラウドベースのデータ統合サービスです。原材料を機械で加工して最終製品を作成する工場と同じように、Data Factory は生データを収集してすぐに使用できる情報に変換する既存のサービスを調整します。
 
Data Factory サービスを使用すると、データ ドリブンのワークフローを作成して、オンプレミスとクラウド データ ストア間でデータを移動したり、Azure HDInsight や Azure Data Lake Analytics などのコンピューティング サービスを使用してデータを処理/変換したりすることができます。必要なアクションを実行するパイプラインを作成した後で、それを定期的に実行するようにスケジュールすることができます (毎時、毎日、毎週など)。

詳細については、「[概要と主要な概念](data-factory-introduction.md)」を参照してください。

### Azure Data Factory の料金の詳細はどこで確認できますか。

Azure Data Factory の料金の詳細については、[Data Factory の料金詳細][adf-pricing-details]に関するページをご覧ください。

### Azure Data Factory の利用はどのように開始するのですか。

- Azure Data Factory の概要については、[Azure Data Factory サービスの概要](data-factory-introduction.md)に関するページをご覧ください。
- コピー アクティビティを使用する**データのコピー/移動**方法に関するチュートリアルについては、「[チュートリアル: Azure BLOB Storage から Azure SQL Database にデータをコピーする](data-factory-get-started.md)」を参照してください。
- HDInsight Hive アクティビティを使用する**データの変換**方法に関するチュートリアルについては、「[チュートリアル: 初めての Data Factory の作成 (概要)](data-factory-build-your-first-pipeline.md)」を参照してください。 
  
### Data Factory を利用可能なリージョンはどこですか。
Data Factory は、**米国西部**と**北ヨーロッパ**で使用できます。Data Factory で使用されるコンピューティング サービスとストレージ サービスは、その他のリージョンでも利用できます。「[サポートされているリージョン](data-factory-introduction.md#supported-regions)」を参照してください。
 
### Data Factory/パイプライン/アクティビティ/データセットの数の制限値はいくつですか。
 
詳細については、記事「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md#data-factory-limits)」の **Azure Data Factory の制限**に関するセクションを参照してください。

### Azure Data Factory サービスではどのような作成エクスペリエンスまたは開発者エクスペリエンスが提供されますか。

データ ファクトリを生成または作成する方法には、以下のものがあります。

- **Azure ポータル**: Azure ポータルの Data Factory ブレードは、Data Factory およびリンクされたサービスを作成するための優れたユーザー インターフェイスです。**Data Factory エディター**は、ポータルの一部でもあり、これらの成果物の JSON の定義を指定することによって、リンクされたサービス、テーブル、データ セット、パイプラインを簡単に作成できる機能です。ポータルまたはエディターを使用した Data Factory の作成とデプロイの例については、「[Azure ポータルまたは Data Factory Editor を使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-editor.md)」を参照してください。

- **Visual Studio**: Visual Studio を使用して、Azure Data Factory を作成することができます。詳細については、「[Visual Studio を使用した初めての Azure Data Factory パイプラインの作成](data-factory-build-your-first-pipeline-using-vs.md)」を参照してください。

- **Azure PowerShell**: PowerShell を使用する Data Factory の作成に関するチュートリアルについては、「[Azure PowerShell を使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-powershell.md)」を参照してください。Data Factory コマンドレットの包括的なドキュメントについては、[MSDN ライブラリの Data Factory コマンドレット リファレンス][adf-powershell-reference]に関するコンテンツをご覧ください。
   
- **.NET クラス ライブラリ**: Data Factory .NET SDK を使用すると、プログラムでデータ ファクトリを作成できます。.NET SDK を使用したデータ ファクトリの作成のチュートリアルについては、[.NET SDK を使用したデータ ファクトリの作成、監視、管理](data-factory-create-data-factories-programmatically.md)に関するページをご覧ください。Data Factory .NET SDK の包括的なドキュメントについては、[Data Factory クラス ライブラリ リファレンス][msdn-class-library-reference]に関するページをご覧ください。

- **REST API**: Azure Data Factory サービスで公開されている REST API を使用して、データ ファクトリを作成およびデプロイすることもできます。Data Factory REST API の包括的なドキュメントについては、[Data Factory REST API リファレンス][msdn-rest-api-reference]に関するページをご覧ください。
 
- **Azure Resource Manager テンプレート**: 詳細については、「[チュートリアル: Azure Resource Manager テンプレートを使用した初めての Azure Data Factory の作成](data-factory-build-your-first-pipeline-using-arm.md)」を参照してください。

### Data Factory の名前を変更できますか。
いいえ。他の Azure のリソースと同様に、Azure Data Factory の名前は変更できません。

### Azure サブスクリプション間でデータ ファクトリを移動できますか。 
はい。次に示すように、データ ファクトリ ブレードの **[移動]** ボタンを使用します。

![データ ファクトリの移動](media/data-factory-faq/move-data-factory.png)

## アクティビティ - FAQ
### Data Factory パイプラインで使用できるアクティビティには、どのような種類がありますか。 

- [データ移動アクティビティ](data-factory-data-movement-activities.md)で、データを移動します。
- [データ変換アクティビティ](data-factory-data-transformation-activities.md)で、データを処理/変換します。 

### アクティビティはいつ実行されますか。
アクティビティが実行されるタイミングは、出力データ テーブルの **availability** 構成設定によって決定されます。入力データセットが指定される場合、アクティビティは実行を開始する前に、入力データのすべての依存関係が満たされている (つまり、**[準備完了]** 状態になっている) かどうかを確認します。

## コピー アクティビティ - FAQ
### 1 つのパイプラインに複数のアクティビティを設定する方法とアクティビティごとに別個のパイプラインを使用する方法ではどちらの方法がよいですか。 
パイプラインでは、関連する複数のアクティビティをまとめることが想定されています。論理的には、複数のアクティビティを接続するテーブルがパイプラインの外部の他のアクティビティによって使用されない場合は、1 つのパイプラインにそれらのアクティビティを保持できます。これにより、パイプラインのアクティブな期間を揃えるためにこれを連結する必要はありません。また、パイプライン内部のテーブルのデータの整合性が、パイプラインを更新するときによりうまく保持されるようになります。パイプラインを更新するとき、基本的にパイプライン内のすべてのアクティビティが停止、削除された後、もう一度作成されます。作成操作の観点では、パイプラインの 1 つの JSON ファイルで関連するアクティビティ内のデータのフローを簡単に見ることができるようになる可能性があります。

## HDInsight アクティビティ - FAQ

### HDInsight でサポートされているリージョンはどこですか。

次の記事の利用可能な地域に関するセクションをご覧ください。[HDInsight の料金詳細][hdinsight-supported-regions]

### オンデマンドの HDInsight クラスターはどのリージョンで使用されますか。

オンデマンドの HDInsight クラスターは、クラスターで使用するように指定したストレージが存在するのと同じリージョンに作成されます。

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
			"typeProperties": {
	        	"clusterSize": 1,
		        "timeToLive": "00:01:00",
		        "linkedServiceName": "LinkedService-SampleData",
		        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
			}
	    }
	} 

上記の例において、otherLinkedServiceName1 と otherLinkedServiceName2 は、HDInsight クラスターが代替ストレージ アカウントにアクセスするために必要な資格情報がその定義に含まれているリンクされたサービスを表します。

## スライス - FAQ

### 入力スライスが準備完了状態にならない 
一般的なミスとして、入力データがデータ ファクトリの外部に存在する (データ ファクトリによって生成されたものでない) ときに、入力データセットの **external** プロパティが **true** に設定されていないことが挙げられます。

次の例では、**dataset1** のみ、**external** を true に設定する必要があります。

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

別のデータ ファクトリに dataset4 (データ ファクトリ 1 のパイプライン 2 で生成) を受け取るパイプラインがある場合、dataset4 は、外部のデータセットとして指定する必要があります。dataset4 は異なるデータ ファクトリ (DataFactory2 ではなく DataFactory1) によって生成されたものであるためです。

**DataFactory2** Pipeline 1: dataset4->activity4->dataset5

external プロパティが適切に設定されている場合は、入力データセットの定義で指定された場所に入力データが存在しているかどうかを確認してください。

### スライスを毎日生成している場合、午前 0 時以外の時刻にスライスを実行するにはどうすればよいですか。
**offset** プロパティを使用して、スライスを生成する時刻を指定します。このプロパティの詳細については、「[データセットの可用性](data-factory-create-datasets.md#Availability)」セクションをご覧ください。以下に簡単な例を示します。

	"availability":
	{
	    "frequency": "Day",
	    "interval": 1,
	    "offset": "06:00:00"
	}

既定の設定である真夜中ではなく、毎日**午前 6 時**にスライスを開始する例です。

### スライスを再実行するにはどうすればよいですか。
スライスを再実行するには、次のどちらかの方法を使用します。

- 監視と管理アプリを使用して、アクティビティ ウィンドウまたはスライスを再実行します。「[選択したアクティビティ ウィンドウを再実行する](data-factory-monitor-manage-app.md#re-run-selected-activity-windows)」をご覧ください。   
- ポータルのスライスの **[データ スライス]** ブレードで、コマンド バーの **[実行]** をクリックします。
- スライスの状態を **Waiting** に設定して、**Set-AzureRmDataFactorySliceStatus** コマンドレットを実行します。   
	
		Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

コマンドレットの詳細については、[Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] に関するページをご覧ください。

### スライスの処理にかかった時間を調べるにはどうすればよいですか。
データ スライスの処理に要した時間は、監視と管理アプリのアクティビティ ウィンドウ エクスプローラーを使用して調べます。詳細については、[アクティビティ ウィンドウ エクスプローラー](data-factory-monitor-manage-app.md#activity-window-explorer)に関するページをご覧ください。

同じことは、Azure ポータルから次の手順で行うこともできます。

1. データ ファクトリの **[データ ファクトリ]** ブレードの **[データセット]** タイルをクリックします。
2. **[データセット]** ブレードの特定のデータセットをクリックします。
3. **[テーブル]** ブレードの **[最近使用したスライス]** ボックスの一覧で、目的のスライスを選択します。
4. **[データ スライス]** ブレードの **[アクティビティの実行]** ボックスの一覧で、[アクティビティの実行] をクリックします。 
5. **[アクティビティの実行の詳細]** ブレードで、**[プロパティ]** タイルをクリックします。 
6. **[期間]** フィールドに表示されている値を確認します。これが、スライスの処理にかかった時間です。   

### 実行中のスライスを停止するにはどうすればよいですか
パイプラインの実行を停止する必要がある場合は、[Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) コマンドレットを使用できます。現時点では、パイプラインを中断しても、進行中のスライスの実行は停止しません。進行中の実行が完了すると、追加のスライスは取得されません。

すべての実行をすぐに停止するには、パイプラインをいったん削除した後で再作成するしかありません。パイプラインを削除する場合は、パイプラインによって使用されているテーブルとリンクされたサービスを削除する必要はありません。


[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=AcomDC_0608_2016-->