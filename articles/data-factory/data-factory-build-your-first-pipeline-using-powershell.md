<properties
	pageTitle="Azure PowerShell を使用した初めての Azure Data Factory パイプラインの作成"
	description="このチュートリアルでは、Azure PowerShell を使用して、サンプルの Azure Data Factory パイプラインを作成します。"
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
	ms.topic="hero-article"
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Azure PowerShell を使用した初めての Azure Data Factory パイプラインの作成
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


この記事では、Azure PowerShell を使用して最初の Azure データ ファクトリを作成する方法について説明します。


## 前提条件
チュートリアルの「概要」トピックに記載されている前提条件とは別に、次をインストールする必要があります。

- 先に進む前に、「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」に目を通し、前提条件の手順を完了する**必要があります**。
- **Azure PowerShell**「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。
- (省略可能) この記事では、すべての Data Factory コマンドレットを取り上げているわけではありません。Data Factory コマンドレットに関する包括的なドキュメントについては、「[Data Factory コマンドレット リファレンス](https://msdn.microsoft.com/library/dn820234.aspx)」を参照してください。 

Azure PowerShell の**バージョン 1.0 より前のバージョン**を使用する場合は、[ここ][cmdlet-reference]に記載されているコマンドレットを使用する必要があります。また、Data Factory コマンドレットを使用する前に、次のコマンドを実行する必要があります。
 
1. Azure PowerShell を起動し、次のコマンドを実行します。Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
	1. **Add-AzureAccount** を実行し、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。
	2. **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
	3. **Select-AzureSubscription** を実行して、使用するサブスクリプションを選択します。このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
4. Azure Data Factory コマンドレットは AzureResourceManager モードでのみ使用できるため、**Switch-AzureMode AzureResourceManager** を実行して、このモードに切り替えます。


## 手順 1: Data Factory を作成する

この手順では、Azure PowerShell を使用して、**FirstDataFactoryPSH** という名前の Azure データ ファクトリを作成します。データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。パイプラインには、1 つまたは複数のアクティビティを含めることができます。たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行し、入力データを変換して出力データを生成する HDInsight Hive アクティビティなどを含めることができます。それでは、この手順でデータ ファクトリの作成から始めましょう。

1. Azure PowerShell を起動し、次のコマンドを実行します。Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
	- **Login-AzureRmAccount** を実行し、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。  
	- **Get-AzureSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
	- **Select-AzureSubscription <Name of the subscription>** を実行して、使用するサブスクリプションを選択します。このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
3. 次のコマンドを実行して、**ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	このチュートリアルの一部の手順は、ADFTutorialResourceGroup という名前のリソース グループを使用することを前提としています。異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりにそのリソース グループを使用する必要があります。
4. **New-AzureRmDataFactory** コマンドレットを実行し、**FirstDataFactoryPSH** という名前のデータ ファクトリを作成します。  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT]Azure Data Factory の名前はグローバルに一意にする必要があります。"**Data factory 名 "FirstDataFactoryPSH" は利用できません**" というエラーが発生した場合は、名前を変更します (yournameFirstDataFactoryPSH など)。このチュートリアルの手順の実行中に、この名前を ADFTutorialFactoryPSH の代わりに使用します。Data Factory アーティファクトの名前付け規則については、[Data Factory - 名前付け規則](data-factory-naming-rules.md)に関するトピックを参照してください。
	> 
	> データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。

パイプラインを作成する前に、まず、Data Factory エンティティをいくつか作成する必要があります。最初に、データ ストアやコンピューティングをデータ ストアにリンクするリンクされたサービスを作成し、リンクされたデータ ストア内のデータを表す入力データセットと出力データセットを定義した後、これらのデータセットを使用するアクティビティを含むパイプラインを作成します。

## 手順 2. リンク サービスを作成する 
この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクします。Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。自分のシナリオで使用するデータ ストアやコンピューティング サービスを特定し、リンクされたサービスを作成して、それらのサービスをデータ ファクトリにリンクする必要があります。

### Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。このチュートリアルでは、同じ Azure ストレージ アカウントを使用して、入力/出力データと HQL スクリプト ファイルを格納します。

1. 次の内容で C:\\ADFGetStarted フォルダーに StorageLinkedService.json という名前の JSON ファイルを作成します。ADFGetStarted フォルダーがない場合は作成します。

		{
	    	"name": "StorageLinkedService",
	    	"properties": {
	        	"type": "AzureStorage",
	        	"description": "",
	        	"typeProperties": {
	            	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        	}
	    	}
		}

	**accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。ストレージ アクセス キーを取得する方法については、「[ストレージ アクセス キーの表示、コピーおよび再生成](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)」を参照してください。

2. Azure PowerShell で ADFGetStarted フォルダーに切り替えます。
3. **New-AzureRmDataFactoryLinkedService** コマンドレットを使用して、リンクされたサービスを作成できます。このコマンドレットと、このチュートリアルで使用する他の Data Factory コマンドレットでは、*ResourceGroupName* および *DataFactoryName* パラメーターの値を渡す必要があります。または、**Get-AzureRmDataFactory** を使用して **DataFactory** オブジェクトを取得すると、コマンドレットを実行するたびに *ResourceGroupName* と *DataFactoryName* を入力しなくてもオブジェクトを渡すことができます。**Get-AzureRmDataFactory** コマンドレットの出力を **$df** 変数に割り当てるには、次のコマンドを実行します。

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. これで、**New-AzureRmDataFactoryLinkedService** コマンドレットを実行して、リンクされた **StorageLinkedService** サービスを作成できます。

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	**Get-AzureRmDataFactory** コマンドレットを実行して出力を **$df** 変数に割り当てていない場合は、*ResourceGroupName* および *DataFactoryName* パラメーターの値を次のように指定する必要があります。

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	チュートリアルの途中で Azure PowerShell を閉じた場合、次に Azure PowerShell を起動したときに、**Get-AzureRmDataFactory** コマンドレットを実行してチュートリアルを完了する必要があります。

### Azure HDInsight のリンクされたサービスを作成する
この手順では、オンデマンド HDInsight クラスターをデータ ファクトリにリンクします。HDInsight クラスターは、実行時に自動的に作成されます。また、処理が終わり、アイドル状態が一定時間続くと削除されます。オンデマンド HDInsight クラスターの代わりに、独自の HDInsight クラスターを使用できます。詳細については、「[コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md)」を参照してください。

1. 次の内容で **C:\\ADFGetStarted** フォルダーに **HDInsightOnDemandLinkedService**.json という名前の JSON ファイルを作成します。

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	次の表に、このスニペットで使用される JSON プロパティの説明を示します。

	| プロパティ | 説明 |
	| :------- | :---------- |
	| バージョン | 作成された HDInsight のバージョンが 3.2 になるように指定します。 | 
	| ClusterSize | 1 ノードの HDInsight クラスターを作成します。 | 
	| TimeToLive | 削除されるまでの HDInsight クラスターのアイドル時間を指定します。 |
	| linkedServiceName | HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。 |

2. **New-AzureRmDataFactoryLinkedService** コマンドレットを実行して、HDInsightOnDemandLinkedService という名前のリンクされたサービスを作成します。

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## 手順 3: データセットを作成する
この手順では、Hive 処理の入力データと出力データを表すデータセットを作成します。これらのデータセットは、このチュートリアルで前に作成した **StorageLinkedService** を参照します。このリンクされたサービスは Azure ストレージ アカウントを指し、データセットは入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。

### 入力データセットを作成する
1. 以下の内容を記述した **InputTable.json** という名前の JSON ファイルを **C:\\ADFGetStarted** フォルダー内に作成します。

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	前の例では、**AzureBlobOutput** というデータセットを作成し、JSON スニペットを指定して、パイプラインのアクティビティの入力データを表す **AzureBlobInput** というデータセットを作成します。さらに、**adfgetstarted** という BLOB コンテナーと **inputdata** というフォルダーに入力データが配置されるように指定します。

	次の表に、このスニペットで使用される JSON プロパティの説明を示します。

	| プロパティ | 説明 |
	| :------- | :---------- |
	| type | データは Azure Blob Storage に存在するため、type プロパティを AzureBlob に設定しています。 |  
	| linkedServiceName | 前に作成した StorageLinkedService を参照します。 |
	| fileName | このプロパティは省略可能です。このプロパティを省略した場合は、folderPath のすべてのファイルが取得されます。このチュートリアルでは、input.log のみが処理されます。 |
	| type | ログ ファイルはテキスト形式です。そのため、TextFormat を使用します。 | 
	| columnDelimiter | ログ ファイル内の列はコンマ (,) で区切られています。 |
	| frequency/interval | frequency を Month に設定し、interval を 1 に設定しています。そのため、入力スライスは 1 か月ごとになります。 | 
	| 外部 | Data Factory サービスによって入力データが生成されない場合は、このプロパティを true に設定します。 | 

2. 次のコマンドを Azure PowerShell で実行して、Data Factory データセットを作成します。

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### 出力データセットを作成します。
次に、Azure Blob Storage に格納される出力データを表す出力データセットを作成します。

1. 以下の内容を記述した **OutputTable.json** という名前の JSON ファイルを **C:\\ADFGetStarted** フォルダー内に作成します。

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

	この JSON スニペットでは、**AzureBlobOutput** というデータセットを作成し、Hive スクリプトによって生成されるデータの構造を指定しています。さらに、**adfgetstarted** という BLOB コンテナーと **partitioneddata** というフォルダーに結果が保存されるように指定します。**availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。

2. 次のコマンドを Azure PowerShell で実行して、Data Factory データセットを作成します。

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## 手順 3: 最初のパイプラインを作成する
この手順では、**HDInsightHive** アクティビティを含む最初のパイプラインを作成します。入力スライスは 1 か月ごと (frequency: Month、interval: 1) であり、出力スライスは 1 か月ごとに生成されるため、アクティビティの scheduler プロパティも 1 か月ごとに設定します (下記参照)。出力データセットとアクティビティの scheduler の設定は一致している必要があります。出力データセットによってスケジュールが開始されるため、アクティビティが出力を生成しない場合でも、この時点で、出力データセットを作成する必要があります。アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。次の JSON で使用されているプロパティについては、このセクションの最後で説明します。


1. 以下の内容を記述した MyFirstPipelinePSH.json という名前の JSON ファイルを C:\\ADFGetStarted フォルダー内に作成します。

	> [AZURE.IMPORTANT]**storageaccountname** は、JSON でのストレージ アカウントの名前に置き換えます。
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	この JSON スニペットでは、Hive を使用して HDInsight クラスターのデータを処理する 1 つのアクティビティで構成されるパイプラインを作成します。
	
	Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定され、**StorageLinkedService** という名前) と **adfgetstarted** コンテナーの **script** フォルダーに格納されます。

	**defines** セクションは、Hive 構成値 (例: ${hiveconf:inputtable}、${hiveconf:partitionedtable}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

	パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

	アクティビティ JSON では、**linkedServiceName** に指定されたコンピューティング **HDInsightOnDemandLinkedService** で Hive スクリプトが実行されるように指定します。
2.  Azure Blob Storage の **adfgetstarted/inputdata** フォルダーに **input.log** ファイルがあることを確認し、次のコマンドを実行して、パイプラインをデプロイします。**start** と **end** が過去の日時に設定され、**isPaused** が false に設定されているため、パイプライン (パイプラインのアクティビティ) はデプロイするとすぐに実行されます。 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. これで、Azure PowerShell を使用して最初のパイプラインを作成できました。

### <a name="MonitorDataSetsAndPipeline"></a> データセットとパイプラインを監視する
このステップでは、Azure PowerShell を使用して、Azure Data Factory の状況を監視します。

1. **Get-AzureRmDataFactory** を実行して **$df** 変数に出力を割り当てます。

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. **Get-AzureRmDataFactorySlice** を実行し、**EmpSQLTable** のすべてのスライスの詳細を表示します。これは、パイプラインの出力テーブルです。

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	ここで指定した StartDateTime がパイプライン JSON で指定されている開始時刻と同じであることに注意してください。次のような出力が表示されます。

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. **Get-AzureRmDataFactoryRun** を実行して、特定のスライスに関するアクティビティの実行の詳細を取得します。

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	次のような出力が表示されます。
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	スライスが **Ready** 状態または **Failed** 状態になるまでこのコマンドレットを実行させたままにできます。スライスが Ready 状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。オンデマンド HDInsight クラスターの作成には通常しばらく時間がかかることに注意してください。

	![output data](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## 次のステップ
この記事では、オンデマンド Azure HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。コピー アクティビティを使用して Azure BLOB から Azure SQL にデータをコピーする方法については、「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](./data-factory-get-started.md)」を参照してください。

### 参照
| トピック | 説明 |
| :---- | :---- |
| [Data Factory コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn820234.aspx) | Data Factory コマンドレットに関する包括的なドキュメントです。 |
| [パイプライン](data-factory-create-pipelines.md) | この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。 |
| [データセット](data-factory-create-datasets.md) | この記事では、Azure Data Factory のデータセットについて説明します。
| [スケジュールと実行](data-factory-scheduling-and-execution.md) | この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) | この記事では、パイプラインを監視、管理、およびデバッグする方法について説明します。また、警告を作成して障害時に通知を受け取る方法についての情報も提供します。 |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_1223_2015-->