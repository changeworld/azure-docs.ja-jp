<properties
	pageTitle="初めてのデータ ファクトリの作成 (Azure ポータル) | Microsoft Azure"
	description="このチュートリアルでは、Azure ポータルで Data Factory エディターを使用して、サンプルの Azure Data Factory パイプラインを作成します。"
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
	ms.date="09/14/2016"
	ms.author="spelluru"/>

# チュートリアル: Azure Portal を使用した初めての Azure データ ファクトリの作成
> [AZURE.SELECTOR]
- [概要と前提条件](data-factory-build-your-first-pipeline.md)
- [Azure ポータル](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

この記事では、[Azure ポータル](https://portal.azure.com/)を使用して最初の Azure データ ファクトリを作成する方法について説明します。

## 前提条件		
1. 「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」に目を通し、**前提条件**の手順を完了する必要があります。
2. この記事では、Azure Data Factory サービスの概念については説明しません。サービスの詳細については、[Azure Data Factory の概要](data-factory-introduction.md)に関するページを参照することをお勧めします。

## データ ファクトリの作成
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。パイプラインには、1 つまたは複数のアクティビティを含めることができます。たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行し、入力データを変換して出力データを生成する HDInsight Hive アクティビティなどを含めることができます。それでは、この手順でデータ ファクトリの作成から始めましょう。

1.	[Azure ポータル](https://portal.azure.com/)にログインします。
2.	左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。
		
	![[作成] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	**[新しい data factory]** ブレードで、[名前] フィールドに「**GetStartedDF**」と入力します。

	![[新しいデータ ファクトリ] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] 
	Azure データ ファクトリの名前は**グローバルに一意**にする必要があります。**"データ ファクトリ名 "GetStartedDF" は利用できません**" というエラーが発生した場合は、データ ファクトリの名前を変更して (たとえば、yournameGetStartedDF)、もう一度作成してみてください。Data Factory アーティファクトの名前付け規則については、「[Azure Data Factory - 名前付け規則](data-factory-naming-rules.md)」を参照してください。
	> 
	> データ ファクトリの名前は今後、**DNS** 名として登録される可能性があるため、一般ユーザーに表示される場合があります。

3.	データ ファクトリを作成する **Azure サブスクリプション**を選択します。
4.	既存の**リソース グループ**を選択するか、新しいリソース グループを作成します。このチュートリアルでは、**ADFGetStartedRG** という名前のリソース グループを作成します。
5.	**[新しい Data Factory]** ブレードで **[作成]** をクリックします。

	> [AZURE.IMPORTANT] Data Factory インスタンスを作成するには、サブスクリプション/リソース グループ レベルで [Data Factory の共同作業者](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor)ロールのメンバーである必要があります。
6.	次のように、Azure ポータルの**スタート画面**にデータ ファクトリを作成中であることが示されます。

	![Data factory を作成中の状態](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. ご利用ありがとうございます。 これで、最初のデータ ファクトリが正常に作成されました。データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。

	![[Data Factory] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

データ ファクトリのパイプラインを作成する前に、まず、Data Factory エンティティをいくつか作成する必要があります。最初に、データ ストアやコンピューティングをデータ ストアにリンクするリンクされたサービスを作成し、リンクされたデータ ストア内の入力/出力データを表す入力データセットと出力データセットを定義した後、これらのデータセットを使用するアクティビティを含むパイプラインを作成します。

## リンクされたサービスの作成
この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクします。Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。自分のシナリオで使用する[データ ストア](data-factory-data-movement-activities.md)や[コンピューティング サービス](data-factory-compute-linked-services.md)を特定し、リンクされたサービスを作成して、それらのサービスをデータ ファクトリにリンクします。

### Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。このチュートリアルでは、同じ Azure ストレージ アカウントを使用して、入力/出力データと HQL スクリプト ファイルを格納します。

1.	**GetStartedDF** の **[DATA FACTORY]** ブレードの **[作成およびデプロイ]** をクリックします。Data Factory エディターが起動します。
	 
	![[作成とデプロイ] タイル](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	**[新しいデータ ストア]** をクリックし、**[Azure Storage]** を選択します。

	![New data store - Azure Storage - menu](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

3.	Azure Storage のリンクされたサービスを作成するための JSON スクリプトがエディターに表示されます。
	
	![Azure Storage のリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
	 
4. **accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。ストレージ アクセス キーを取得する方法については、「[ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。
5. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

	![デプロイ ボタン](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   リンクされたサービスが正常にデプロイされると、**[Draft-1]** ウィンドウが消え、**AzureStorageLinkedService** が左側のツリー ビューに表示されます。 ![メニューでのストレージのリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)

 
### Azure HDInsight のリンクされたサービスを作成する
この手順では、オンデマンド HDInsight クラスターをデータ ファクトリにリンクします。HDInsight クラスターは、実行時に自動的に作成されます。また、処理が終わり、アイドル状態が一定時間続くと削除されます。

1. **Data Factory エディター**で、**[...] (その他)**、**[新規計算]** の順にクリックし、**[On-demand HDInsight cluster (オンデマンド HDInsight クラスター)]** を選択します。

	![New compute](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. 次のスニペットをコピーして、**[Draft-1]** ウィンドウに貼り付けます。この JSON スニペットは、HDInsight クラスターをオンデマンドで作成するために使用されるプロパティを記述します。

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService"
		    }
		  }
		}
	
	次の表に、このスニペットで使用される JSON プロパティの説明を示します。
	
	| プロパティ | 説明 |
	| :------- | :---------- |
	| バージョン | 作成された HDInsight のバージョンが 3.2 になるように指定します。 | 
	| ClusterSize | HDInsight クラスターのサイズを指定します。 | 
	| TimeToLive | 削除されるまでの HDInsight クラスターのアイドル時間を指定します。 |
	| linkedServiceName | HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。 |

	以下の点に注意してください。
	
	- Data Factory は、JSON で **Windows ベース**の HDInsight クラスターを自動的に作成します。**Linux ベース**の HDInsight クラスターを作成させることもできます。詳細については、[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)に関するセクションを参照してください。
	- オンデマンド HDInsight クラスターの代わりに、**独自の HDInsight クラスター**を使用できます。詳細については、[HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に関するセクションを参照してください。
	- HDInsight クラスターは、JSON (**linkedServiceName**) で指定した Blob Storage に**既定のコンテナー**を作成します。クラスターを削除しても、HDInsight はこのコンテナーを削除しません。この動作は仕様です。オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスが処理されるたびに HDInsight クラスターが作成されます。クラスターは、処理が終了すると自動的に削除されます。
	
		処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。これらのコンテナーの名前は、"adf**<データ ファクトリ名>**-**<リンクされたサービス名>**-<日時スタンプ>" というパターンに従います。Azure Blob Storage 内のコンテナーを削除するには、[Microsoft ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを使用します。

	詳細については、[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)に関するセクションを参照してください。
3. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

	![Deploy on-demand HDInsight linked service](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

4. **AzureStorageLinkedService** と **HDInsightOnDemandLinkedService** が両方とも左側のツリー ビューに表示されていることを確認します。

	![リンクされたサービスを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## データセットを作成する
この手順では、Hive 処理の入力データと出力データを表すデータセットを作成します。これらのデータセットは、このチュートリアルで前に作成した **AzureStorageLinkedService** を参照します。このリンクされたサービスは Azure ストレージ アカウントを指し、データセットは入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。

### 入力データセットの作成

1. **Data Factory エディター**のコマンド バーで **[...] (その他)** をクリックし、**[新しいデータセット]** をクリックし、**[Azure BLOB ストレージ]** を選択します。

	![新しいデータセット](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。この JSON スニペットでは、パイプラインのアクティビティの入力データを表す **AzureBlobInput** というデータセットを作成します。さらに、**adfgetstarted** という BLOB コンテナーと **inputdata** というフォルダーに入力データが配置されるように指定します。
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService",
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

	次の表に、このスニペットで使用される JSON プロパティの説明を示します。

	| プロパティ | Description |
	| :------- | :---------- |
	| type | データは Azure Blob Storage に存在するため、type プロパティを AzureBlob に設定しています。 |  
	| linkedServiceName | 前に作成した AzureStorageLinkedService を参照します。 |
	| fileName | このプロパティは省略可能です。このプロパティを省略した場合は、folderPath のすべてのファイルが取得されます。このチュートリアルでは、input.log のみが処理されます。 |
	| type | ログ ファイルはテキスト形式です。そのため、TextFormat を使用します。 | 
	| columnDelimiter | ログ ファイル内の列はコンマ (,) で区切られています。 |
	| frequency/interval | frequency を Month に設定し、interval を 1 に設定しています。そのため、入力スライスは 1 か月ごとになります。 | 
	| 外部 | Data Factory サービスによって入力データが生成されない場合は、このプロパティを true に設定します。 | 
	  	
3. コマンド バーの **[デプロイ]** をクリックして、新しく作成したデータセットをデプロイします。データセットが左側のツリー ビューに表示されます。


### 出力データセットの作成
次に、Azure BLOB ストレージに格納される出力データを表す出力データセットを作成します。

1. **Data Factory エディター**のコマンド バーで、**[...] (その他)** をクリックし、**[新しいデータセット]** をクリックし、**[Azure BLOB ストレージ]** を選択します。
2. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。この JSON スニペットでは、**AzureBlobOutput** というデータセットを作成し、Hive スクリプトによって生成されるデータの構造を指定しています。さらに、**adfgetstarted** という BLOB コンテナーと **partitioneddata** というフォルダーに結果が保存されるように指定します。**availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
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

	これらのプロパティの説明については、「**入力データセットを作成する**」セクションを参照してください。Data Factory サービスによってデータセットが生成されるため、出力データセットの external プロパティは設定しません。
3. コマンド バーの **[デプロイ]** をクリックして、新しく作成したデータセットをデプロイします。
4. データセットが正常に作成されたことを確認します。

	![リンクされたサービスを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## パイプラインの作成
この手順では、**HDInsightHive** アクティビティを含む最初のパイプラインを作成します。入力スライスは 1 か月ごと (frequency: Month、interval: 1) に使用可能であり、出力スライスは 1 か月ごとに生成されるため、アクティビティの scheduler プロパティも 1 か月ごとに設定します。出力データセットとアクティビティの scheduler の設定は一致している必要があります。現在、スケジュールは出力データセットによって開始されるため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。次の JSON で使用されているプロパティについては、このセクションの最後で説明します。

1. **Data Factory エディター**で、省略記号 **[…]** (その他のコマンド) をクリックし、**[新しいパイプライン]** をクリックします。
	
	![[新しいパイプライン] ボタン](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。

	> [AZURE.IMPORTANT] **storageaccountname** は、JSON でのストレージ アカウントの名前に置き換えます。
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService",
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
		        "start": "2016-04-01T00:00:00Z",
		        "end": "2016-04-02T00:00:00Z",
		        "isPaused": false
		    }
		}
 
	この JSON スニペットでは、Hive を使用して HDInsight クラスターのデータを処理する 1 つのアクティビティで構成されるパイプラインを作成します。
	
	Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定され、**AzureStorageLinkedService** という名前) および **adfgetstarted** コンテナーの **script** フォルダーに格納されます。

	**defines** セクションは、Hive 構成値 (例: ${hiveconf:inputtable}、${hiveconf:partitionedtable}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

	パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

	アクティビティ JSON では、**linkedServiceName** に指定されたコンピューティング **HDInsightOnDemandLinkedService** で Hive スクリプトが実行されるように指定します。

	> [AZURE.NOTE] 例で使用した JSON プロパティの詳細については、「[パイプラインのしくみ](data-factory-create-pipelines.md#anatomy-of-a-pipeline)」を参照してください。

3. 次のことを確認します。
	1. Azure BLOB ストレージの **adfgetstarted** コンテナーの **inputdata** フォルダーに **input.log** ファイルが存在すること。
	2. Azure BLOB ストレージの **adfgetstarted** コンテナーの **script** フォルダーに **partitionweblogs.hql** ファイルが存在すること。これらのファイルがない場合は、「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」で前提条件の手順を実行してください。
	3. パイプライン JSON の実際のストレージ アカウントの名前を **storageaccountname** に指定済みであること。
2. コマンド バーの **[デプロイ]** をクリックして、パイプラインをデプロイします。**start** と **end** が過去の日時に設定され、**isPaused** が false に設定されているため、パイプライン (パイプラインのアクティビティ) はデプロイするとすぐに実行されます。
4. ツリー ビューにパイプラインが表示されることを確認します。

	![パイプラインを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. これで、最初のパイプラインが正常に作成されました。

## パイプラインを監視する

### ダイアグラム ビューを使用してパイプラインを監視する

6. **[X]** をクリックして Data Factory エディターのブレードを閉じ、[Data Factory] ブレードに戻って **[ダイアグラム]** をクリックします。
  
	![[ダイアグラム] タイル](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. ダイアグラム ビューに、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。
	
	![ダイアグラム ビュー](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)
8. パイプラインのすべてのアクティビティを表示するために、ダイアグラム内のパイプラインを右クリックし、[パイプラインを開く] をクリックします。

	![パイプラインを開くメニュー](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. パイプラインの HDInsightHive アクティビティが表示されることを確認します。
  
	![Open pipeline view](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

	前のビューに戻るには、上部にある階層リンク メニューの **[データ ファクトリ]** をクリックします。
10. **ダイアグラム ビュー**で、**AzureBlobInput** データセットをダブルクリックします。スライスの状態が **[準備完了]** であることを確認します。スライスの状態が [準備完了] と表示されるまでに数分かかる場合があります。しばらく待っても [準備完了] と表示されない場合は、入力ファイル (input.log) が適切なコンテナー (adfgetstarted) とフォルダー (inputdata) に配置されていることを確認してください。

	![Input slice in ready state](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. **[X]** をクリックして、**[AzureBlobInput]** ブレードを閉じます。
12. **ダイアグラム ビュー**で、**AzureBlobOutput** データセットをダブルクリックします。現在処理中のスライスが表示されます。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. 処理が完了すると、スライスの状態に **[準備完了]** が表示されます。
	>[AZURE.IMPORTANT] オンデマンド HDInsight クラスターの作成には通常しばらく時間がかかります (約 20 分)。そのため、パイプラインによるスライスの処理に**約 30 分**かかると想定してください。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)
	
10. スライスが**準備完了**状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。
 
	![output data](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)
11. スライスをクリックすると、**[データ スライス]** ブレードに詳細が表示されます。

	![Data slice details](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)
12. **[アクティビティの実行]** 一覧でアクティビティの実行をクリックすると、**[アクティビティの実行の詳細]** ウィンドウにアクティビティの実行の詳細 (このシナリオでは Hive アクティビティ) が表示されます。

	![アクティビティ実行の詳細](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)
	
	ログ ファイルで、実行された Hive クエリとステータス情報を確認できます。これらのログは、すべての問題のトラブルシューティングに役立ちます。詳細については、[Azure ポータルのブレードを使用したパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)に関するページを参照してください。

> [AZURE.IMPORTANT] 入力ファイルは、スライスが正常に処理された時点で削除されます。そのためスライスを取得したり、このチュートリアルをもう一度行ったりする場合は、adfgetstarted コンテナーの inputdata フォルダーに入力ファイル (input.log) をアップロードしてください。

### 監視と管理アプリを使用してパイプラインを監視する
パイプラインは、監視と管理アプリを使用して監視することもできます。このアプリケーションの使い方の詳細については、[監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。

1. データ ファクトリのホーム ページの **[監視と管理]** タイルをクリックします。

	![Monitor & Manage tile](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)
2. **監視と管理アプリケーション**が表示されます。パイプラインの開始時刻 (2016 年 4 月 1 日午前 12:00) と終了時刻 (2016 年 4 月 2 日午前 12:00) と一致するように、**[開始時刻]** と **[終了時刻]** を変更し、**[適用]** をクリックします。

	![Monitor & Manage App](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)
3. **[アクティビティ ウィンドウ]** 一覧でアクティビティ ウィンドウを選択し、詳細を確認します。![Activity window details](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)


## 概要 
このチュートリアルでは、HDInsight Hadoop クラスター上で Hive スクリプトを実行してデータを処理するために、Azure データ ファクトリを作成しました。以下の手順を実行するために、Azure ポータルで Data Factory エディターを使用しました。

1.	Azure **データ ファクトリ**を作成しました。
2.	次の 2 つの**リンクされたサービス**を作成しました。
	1.	入出力ファイルを保持する Azure Blob Storage をデータ ファクトリにリンクするための **Azure Storage** のリンクされたサービス。
	2.	オンデマンド HDInsight Hadoop クラスターをデータ ファクトリにリンクするための **Azure HDInsight** オンデマンドのリンクされたサービス。Azure Data Factory は、入力データを処理し、出力データを生成するために、HDInsight Hadoop クラスターをジャストインタイムで作成します。
3.	パイプラインの HDInsight Hive アクティビティ向けの入出力データを記述する 2 つの**データセット**を作成しました。
4.	**HDInsight Hive** アクティビティを持つ**パイプライン**を作成しました。

## 次のステップ
この記事では、オンデマンド HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。コピー アクティビティを使用して Azure BLOB から Azure SQL にデータをコピーする方法については、「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)」を参照してください。

## 関連項目
| トピック | 説明 |
| :---- | :---- |
| [データ変換のアクティビティ](data-factory-data-transformation-activities.md) | この記事には、Azure Data Factory でサポートされているデータ変換のアクティビティ (このチュートリアルで使用した HDInsight Hive 変換など) の一覧を示します。 | 
| [スケジュールと実行](data-factory-scheduling-and-execution.md) | この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [パイプライン](data-factory-create-pipelines.md) | この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。 |
| [データセット](data-factory-create-datasets.md) | この記事では、Azure Data Factory のデータセットについて説明します。
| [監視アプリを使用したパイプラインの監視と管理に関する記事](data-factory-monitor-manage-app.md) | この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 

  

<!---HONumber=AcomDC_0921_2016-->