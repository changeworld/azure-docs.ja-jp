<properties
	pageTitle="Data Factory Editor を使用した初めての Azure Data Factory パイプラインの作成"
	description="このチュートリアルでは、Azure ポータルで Data Factory Editor を使用して、サンプルの Azure Data Factory パイプラインを作成します。"
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
	ms.date="10/15/2015"
	ms.author="spelluru"/>

# Data Factory Editor (Azure ポータル) を使用した初めての Azure Data Factory パイプラインの作成
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


この記事では、[Azure プレビュー ポータル](https://portal.azure.com/)を使用して最初のパイプラインを作成する方法について説明します。このチュートリアルの手順は次のとおりです。

1.	データ ファクトリを作成する
2.	リンクされたサービス (データ ストア、計算) とデータセットを作成する
3.	パイプラインを作成する

この記事では、Azure Data Factory サービスの概念については説明しません。サービスの詳細については、記事「[Azure Data Factory サービスの概要](data-factory-introduction.md)」を参照してください。

> [AZURE.IMPORTANT]「[チュートリアルの概要](data-factory-build-your-first-pipeline.md)」という記事を参照し、前提条件の手順を完了してから、このチュートリアルを実行してください。

## 手順 1: データ ファクトリを作成する

1.	[Azure プレビュー ポータル](http://portal.azure.com/)にログインした後、次の操作を行います。
	1.	左側のメニューの **[新規]** をクリックします。 
	2.	**[作成]** ブレードの **[データ分析]** をクリックします。
	3.	**[データ分析]** ブレードの **[Data Factory]** をクリックします。

		![[作成] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	**[新しいデータ ファクトリ]** ブレードで、[名前] に「**DataFactoryMyFirstPipeline**」と入力します。

	![[新しいデータ ファクトリ] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Azure Data Factor の名前はグローバルに一意にする必要があります。"**"DataFactoryMyFirstPipeline" という名前のデータ ファクトリは使用できません**" というエラーが発生した場合は、データ ファクトリの名前を変更して (yournameDataFactoryMyFirstPipeline など) 作成し直してください。Data Factory アーティファクトの名前付け規則については、[Data Factory - 名前付け規則](data-factory-naming-rules.md)に関するトピックを参照してください。
	>  
	> データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。

3.	リソース グループを作成していない場合は、リソース グループを作成する必要があります。これを行うには、次の手順を実行します。
	1.	**[リソース グループ名]** をクリックします。
	2.	**[リソース グループ]** ブレードで、**[新規リソース グループの作成]** を選択します。
	3.	**[リソース グループの作成]** ブレードで、**[名前]** に「**ADF**」と入力します。
	4.	**[OK]** をクリックします。
	
		![リソース グループの作成](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	リソース グループを選択した後、データ ファクトリを作成する正しいサブスクリプションを使用していることを確認します。
5.	**[新しいデータ ファクトリ]** ブレードで **[作成]** をクリックします。
6.	次のように、Azure プレビュー ポータルの**スタート画面**にデータ ファクトリを作成中であることが示されます。   

	![Data factory を作成中の状態](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. ご利用ありがとうございます。 これで、最初のデータ ファクトリが正常に作成されました。データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。 	

	![[Data Factory] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

以降の手順では、このチュートリアルで使用するリンクされたサービス、データセット、およびパイプラインを作成する方法を説明します。

## 手順 2: リンクされたサービスとデータセットを作成する
この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクした後、Hive 処理からの出力データを表すデータセットを作成します。

### Azure Storage のリンクされたサービスを作成する
1.	**DataFactoryFirstPipeline** 用の **[Data Factory]** ブレードで、**[作成とデプロイ]** をクリックします。Data Factory エディタが起動します。 
	 
	![[作成とデプロイ] タイル](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	**[新しいデータ ストア]** をクリックし、**[Azure ストレージ]** を選択します。
	
	![Azure Storage のリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Azure Storage のリンクされたサービスを作成するための JSON スクリプトがエディターに表示されます。 
4. **accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。ストレージ アクセス キーを取得する方法については、「[ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md/#view-copy-and-regenerate-storage-access-keys)」を参照してください。
5. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

	![デプロイ ボタン](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Azure HDInsight のリンクされたサービスを作成する
次に、Hive スクリプトの実行に使用するオンデマンド HDInsight クラスター用のリンクされたサービスを作成します。

1. **Data Factory エディター**で、コマンド バーの **[新しいコンピューティング]** をクリックし、**[オンデマンド HDInsight クラスター]** を選択します。

	![新しいコンピューティング](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。この JSON スニペットは、HDInsight クラスターをオンデマンドで作成するために使用されるプロパティを記述します。 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	次の表に、スニペットで使用される JSON プロパティの説明を示します。
	
	プロパティ | 説明
	-------- | -----------
	バージョン | バージョン 3.1 の HDInsight を作成することを指定します。 
	ClusterSize | 1 ノードの HDInsight クラスターを作成します。 
	TimeToLive | 削除されるまでの HDInsight クラスターのアイドル時間を指定します。
	JobsContainer | HDInsight によって生成されるログを保存するために作成されるジョブ コンテナーの名前を指定します。
	linkedServiceName | HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。
3. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。 
4. StorageLinkedService と HDInsightOnDemandLinkedService が両方とも左側のツリー ビューに表示されていることを確認します。

	![リンクされたサービスを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### 出力データセットを作成する
次に、Azure BLOB ストレージに格納されるデータを表す出力データセットを作成します。

1. **Data Factory エディター**で、コマンド バーの **[新しいデータセット]** をクリックし、**[Azure BLOB ストレージ]** を選択します。  

	![新しいデータセット](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。この JSON スニペットでは、**AzureBlobOutput** というデータセットを作成し、Hive スクリプトによって生成されるデータの構造を指定しています。さらに、結果を **data** という BLOB コンテナーおよび **partitioneddata** というフォルダーに格納することを指定します。**availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
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

3. コマンド バーの **[デプロイ]** をクリックして、新しく作成したデータセットをデプロイします。
4. データセットが正常に作成されたことを確認します。

	![リンクされたサービスを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## 手順 3: 最初のパイプラインを作成する
この手順では、最初のパイプラインを作成します。

1. **Data Factory エディター**で、省略記号 **[…]** をクリックし、**[新しいパイプライン]** をクリックします。
	
	![[新しいパイプライン] ボタン](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。

	> [AZURE.IMPORTANT]**storageaccountname** は、JSON でのストレージ アカウントの名前に置き換えます。

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}
 
	この JSON スニペットでは、Hive を使用して HDInsight クラスターのデータを処理する 1 つのアクティビティで構成されるパイプラインを作成します。
	
	Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定され、**StorageLinkedService** という名前) および **script** という名前のコンテナーに格納されます。

	**extendedProperties** セクションは、Hive 構成値 (例: ${hiveconf:PartitionedData}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

	パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

	アクティビティ JSON では、Hive スクリプトがリンクされたサービス **HDInsightOnDemandLinkedService** によって指定されたコンピューティングで実行することを指定します。
3. コマンド バーの **[デプロイ]** をクリックして、パイプラインをデプロイします。
4. ツリー ビューにパイプラインが表示されることを確認します。

	![パイプラインを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. これで、最初のパイプラインが正常に作成されました。
6. **[X]** をクリックして Data Factory エディターのブレードを閉じ、[Data Factory] ブレードに戻って **[ダイアグラム]** をクリックします。
  
	![[ダイアグラム] タイル](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. ダイアグラム ビューに、パイプラインの概要と、このチュートリアルで使用されるデータセットが表示されます。
	
	![[ダイアグラム] ビュー](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. ダイアグラム ビューで、**AzureBlobOutput** データセットをダブルクリックします。現在処理中のスライスが表示されます。

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. 処理が完了すると、スライスの状態に **[準備完了]** が表示されますオンデマンド HDInsight クラスターの作成には通常しばらく時間がかかることに注意してください。 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. スライスが**準備完了**状態になったら、BLOB ストレージの **data** コンテナーの **partitioneddata** フォルダーで出力データを調べます。  
 

 

## 次のステップ
この記事では、オンデマンド HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。コピー アクティビティを使用して Azure BLOB から Azure SQL にデータをコピーする方法については、「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](./data-factory-get-started.md)」を参照してください。
  

## フィードバックの送信
この記事に関するフィードバックをお待ちしています。少しのお時間をとって、[電子メール](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-editor.md)でフィードバックをお寄せください。

<!---HONumber=Oct15_HO4-->