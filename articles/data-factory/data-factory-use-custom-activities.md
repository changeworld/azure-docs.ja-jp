<properties 
	pageTitle="Azure Data Factory パイプラインでカスタム アクティビティを使用する" 
	description="カスタム アクティビティを作成して Azure データ ファクトリ パイプラインで使用する方法について説明します。" 
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
	ms.date="07/16/2015" 
	ms.author="spelluru"/>

# Azure Data Factory パイプラインでカスタム アクティビティを使用する
Azure Data Factory は、パイプラインで使用してデータの移動や処理を行う、**コピー アクティビティ**や **HDInsight アクティビティ**などの組み込みアクティビティをサポートしています。独自の変換/処理ロジックで .NET カスタム アクティビティを作成し、そのアクティビティをパイプラインで使用することもできます。**Azure HDInsight** クラスターまたは **Azure Batch** サービスを使用して実行するようにアクティビティを構成できます

この記事では、カスタム アクティビティを作成し、Azure Data Factory のパイプラインで使用する方法について説明します。カスタム アクティビティを作成して使用するための詳細なチュートリアルも提供します。チュートリアルでは、HDInsight リンク サービスを使用します。代わりに Azure Batch リンク サービスを使用するには、**AzureBatch** 型のリンク サービスを作成し、パイプライン JSON (**linkedServiceName**) のアクティビティ セクションで使用します。カスタム アクティビティでの Azure Batch の使用方法の詳細については、[Azure Batch リンク サービス](#AzureBatch)に関するセクションをご覧ください。


## <a name="walkthrough" />チュートリアル
このチュートリアルでは、カスタム アクティビティを作成し、そのアクティビティを Azure Data Factory パイプラインで使用する手順について説明します。このチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルをさらに進めたものです。カスタム アクティビティの動作について確認したい場合は、まず「Azure Data Factory を使ってみる」のチュートリアルを終えてから、このチュートリアルを始めてください。

**前提条件:**


- 「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアル。本チュートリアルを実行する前に、この記事のチュートリアルを完了しておく必要があります。
- Visual Studio 2012 または 2013
- [Azure .NET SDK][azure-developer-center] をダウンロードし、インストールします。
- 最新の [Azure Data Factory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/)をダウンロードし、インストールします。手順はこのチュートリアルにあります。
- Azure ストレージの NuGet パッケージをダウンロードしてインストールします。チュートリアルで方法を説明しているため、この手順は省略できます。

## 手順 1. カスタム アクティビティを作成する

1.	.NET クラス ライブラリ プロジェクトを作成します。
	<ol type="a">
	<li><b>Visual Studio 2012</b> または <b>Visual Studio 2013</b> を起動します。</li>
	<li><b>[ファイル]</b> をクリックし、<b>[新規作成]</b> をポイントして、<b>[プロジェクト]</b> をクリックします。</li> 
	<li><b>[テンプレート]</b> を展開し、<b>[Visual C#]</b> を選択します。このチュートリアルでは C# を使用しますが、カスタム アクティビティの開発には、どの .NET 言語でも使用できます。</li> 
	<li>右側にあるプロジェクトの種類の一覧から <b>[クラス ライブラリ]</b> を選択します。</li>
	<li><b>[プロジェクト名]</b> に「<b>MyDotNetActivity</b>」と入力します。</li> 
	<li><b>[場所]</b> で <b>[C:\ADFGetStarted]</b> を選択します。</li>
	<li><b>[OK]</b> をクリックしてプロジェクトを作成します。</li>
</ol>
2.  <b>[ツール]</b> をクリックし、<b>[NuGet パッケージ マネージャー]</b> をポイントして、<b>[パッケージ マネージャー コンソール]</b> をクリックします。
3.	<b>[パッケージ マネージャー コンソール]</b> で、次のコマンドを実行して <b>Microsoft.Azure.Management.DataFactories</b> をインポートします。 

		Install-Package Microsoft.Azure.Management.DataFactories

4. Azure Storage NuGet パッケージをプロジェクトにインポートします。

		Install-Package Azure.Storage

5. 次の **using** ステートメントをプロジェクト内のソース ファイルに追加します。

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. **namespace** の名前を **MyDotNetActivityNS** に変更します。

		namespace MyDotNetActivityNS

7. クラス名を **MyDotNetActivity** に変更し、次に示すように **IDotNetActivity** インターフェイスから派生させます。

		public class MyDotNetActivity : IDotNetActivity

8. **IDotNetActivity** インターフェイスの **Execute** メソッドを **MyDotNetActivity** クラスに実装 (追加) し、次のサンプル コードをメソッドにコピーします。


	次のサンプル コードでは、入力 BLOB 内の行数をカウントし、BLOB へのパス、BLOB 内の行数、アクティビティが実行されたコンピューター、現在の日時を出力 BLOB に生成します。

        public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Table> tables, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Table inputTable = tables.Single(table => table.Name == activity.Inputs.Single().Name);
            inputLocation = inputTable.Properties.TypeProperties as CustomDataset;

			// using First method instead of Single since we are using the same 
			// Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;


            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputTable);

            logger.Write("Reading blob from: {0}", folderPath);

            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            BlobContinuationToken continuationToken = null;

            do
            {
                BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath,
                                            true,
                                            BlobListingDetails.Metadata,
                                            null,
                                            continuationToken,
                                            null,
                                            null);
                foreach (IListBlobItem listBlobItem in result.Results)
                {
                    CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                    int count = 0;
                    if (inputBlob != null)
                    {
                        using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                        {
                            while (!sr.EndOfStream)
                            {
                                string line = sr.ReadLine();
                                if (count == 0)
                                {
                                    logger.Write("First line: [{0}]", line);
                                }
                                count++;
                            }

                        }

                    }
                    output += string.Format(CultureInfo.InvariantCulture,
                                    "{0},{1},{2},{3},{4}\n",
                                    folderPath,
                                    inputBlob.Name,
                                    count,
                                    Environment.MachineName,
                                    DateTime.UtcNow);

                }
                continuationToken = result.ContinuationToken;

            } while (continuationToken != null);

            Table outputTable = tables.Single(table => table.Name == activity.Outputs.Single().Name);
            outputLocation = outputTable.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputTable);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. 以下のヘルパー メソッドを追加します。**Execute** メソッドがこれらのヘルパー メソッドを呼び出します。**GetConnectionString** メソッドは Azure Storage の接続文字列を取得し、**GetFolderPath** メソッドは BLOB の場所を取得します。


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

        
        private static string GetFolderPath(Table dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }
   

10. プロジェクトをコンパイルします。メニューの **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。
11. **Windows エクスプローラー**を起動し、ビルドの種類に応じて、**bin\\debug** フォルダーまたは **bin\\release** フォルダーに移動します。
12. <project folder>\\bin\\Debug フォルダー内のすべてのバイナリを含む zip ファイル、**MyDotNetActivity.zip** を作成します。エラー発生時の問題の原因となったソース コードの行番号など、追加情報を取得するために、MyDotNetActivity.pdb ファイルを含めることもできます。 
13. **MyDotNetActivity.zip** を BLOB として **customactvitycontainer** にアップロードします。この BLOB コンテナーは、**ADFTutorialDataFactory** 内の **StorageLinkedService** リンク サービスが使用する Azure BLOB ストレージ内にあります。BLOB コンテナー **customactivitycontainer** が既に存在していなければ、作成します。 


## 手順 2. パイプラインでカスタム アクティビティを使用する
ここでは、次の手順を実行します。

1. カスタム アクティビティがマップ専用のジョブとして実行される HDInsight クラスター用のリンク サービスを作成します。 
2. このサンプルのパイプラインが生成する出力テーブルを作成します。
3. 手順 1. で作成したカスタム アクティビティを使用するパイプラインを作成して実行します。 
 
### カスタム アクティビティの実行に使用する HDInsight クラスター用のリンク サービスの作成
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。

> [AZURE.NOTE]実行時に、.NET アクティビティのインスタンスは HDInsight クラスターの 1 つのワーカー ノードでのみ実行されます。複数のノードで実行されるように拡張することはできません。.NET アクティビティの複数のインスタンスは、HDInsight クラスターの別々のノードで並列に実行できます。

「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルに加えて「[Data Factory で Pig と Hive を使用する][hivewalkthrough]」のチュートリアルも終えている場合は、このリンク サービスの作成を省略し、既に ADFTutorialDataFactory 内にあるリンク サービスを使用できます。


#### オンデマンド HDInsight クラスターを使用するには

1. **Azure ポータル**の Data Factory のホーム ページで、**[作成とデプロイ]** をクリックします。
2. Data Factory エディターで、コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[オンデマンド HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterSize** プロパティには、HDInsight クラスターのサイズを指定します。
	2. **jobsContainer** プロパティには、クラスター ログを格納する既定のコンテナーの名前を指定します。このチュートリアルでは、「**adfjobscontainer**」と指定します。
	3. **timeToLive** プロパティには、顧客が削除されるまでの間にアイドル状態でいられる時間を指定します。 
	4. **version** プロパティには、使用する HDInsight のバージョンを指定します。このプロパティを除外した場合は、最新バージョンが使用されます。  
	5. **linkedServiceName** には、入門チュートリアルで作成した **StorageLinkedService** を指定します。 

		{ "name": "HDInsightOnDemandLinkedService", "properties": { "type": "HDInsightOnDemand", "typeProperties": { "clusterSize": "1", "timeToLive": "00:05:00", "version": "3.1", "linkedServiceName": "StorageLinkedService" } } }

2. コマンド バーの **[デプロイ]** をクリックして、リンクされたサービスをデプロイします。
   
#### 独自の HDInsight クラスターを使用するには 

1. **Azure ポータル**の Data Factory のホーム ページで、**[作成とデプロイ]** をクリックします。
2. **Data Factory エディター**で、コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterUri** プロパティには、HDInsight の URL を入力します。たとえば、https://<clustername>.azurehdinsight.net/ などです。     
	2. **UserName** プロパティには、HDInsight クラスターにアクセスできるユーザー名を入力します。
	3. **Password** プロパティには、ユーザーのパスワードを入力します。 
	4. **LinkedServiceName** プロパティには、「**StorageLinkedService**」と入力します。これは、入門チュートリアルで作成したリンク サービスです。 

2. コマンド バーの **[デプロイ]** をクリックして、リンクされたサービスをデプロイします。

### 出力テーブルの作成

1. **Data Factory エディター**で、**[新しいデータセット]** をクリックし、コマンド バーの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON スクリプトを、次の JSON スクリプトに置き換えます。

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
		          }
		        }
		      ]
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

 	出力場所は **adftutorial/customactivityoutput/YYYYMMDDHH/** です。YYYYMMDDHH は、スライスが生成された年 (Year)、月 (Month)、日 (Day)、時間 (Hour) です。詳細については、[開発者用リファレンス][adf-developer-reference]のページをご覧ください。

2. コマンド バーの **[デプロイ]** をクリックして、テーブルをデプロイします。


### カスタム アクティビティを使用するパイプラインの作成と実行
   
1. Data Factory エディターで、コマンド バーの **[新しいパイプライン]** をクリックします。このコマンドが表示されない場合は、**[...] (省略記号)** をクリックすると表示されます。 
2. 右側のウィンドウの JSON を、次の JSON スクリプトに置き換えます。**HDInsightLinkedService** リンク サービスの作成手順を実行した場合、独自のクラスターを使用するには、次の JSON で **HDInsightOnDemandLinkedService** を **HDInsightLinkedService** に置き換えます。 
		
		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	**StartDateTime** の値を現在の 3 日前の日付に置き換え、**EndDateTime** の値を現在の日付に置き換えます。StartDateTime と EndDateTime は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります (例: 2014-10-14T16:32:41Z)。出力テーブルは毎日生成されるようスケジュール設定されているので、3 つのスライスが生成されることになります。

	以下の点に注意してください。

	- activities セクションには、**DotNetActivity** 型のアクティビティが 1 つあります。
	- 入門チュートリアルで使用したものと同じ入力テーブル、**EmpTableFromBlob** を使用します。
	- 次の手順で作成する新しい出力テーブル、**OutputTableForCustom** を使用します。
	- **AssemblyName** を DLL の名前 (**MyActivities.dll**) に設定します。
	- **EntryPoint** を **MyDotNetActivityNS.MyDotNetActivity** に設定します。
	- **PackageLinkedService** を、「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルで作成された **MyBlobStore** に設定します。この BLOB ストアには、カスタム アクティビティの zip ファイルが含まれています。
	- **PackageFile** を **customactivitycontainer/MyDotNetActivity.zip** に設定します。
     
4. コマンド バーの **[デプロイ]** をクリックして、パイプラインをデプロイします。
8. **adftutorial** コンテナー内の BLOB ストレージに出力ファイルが生成されていることを確認します。

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. 出力ファイルを開くと、以下のような出力結果が表示されるはずです。
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(BLOB の場所), (BLOB の名前), (BLOB 内の行数), (アクティビティが実行されたノード), (日付タイムスタンプ)

10.	[Azure ポータル][azure-preview-portal]または Azure PowerShell コマンドレットを使用して、データ ファクトリ、パイプライン、データセットを監視します。カスタム アクティビティのコード内の **ActivityLogger** からのメッセージは、ポータルからダウンロードできるログ (user-0.log など) で確認することも、コマンドレットを使用して確認することもできます。

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
	
   
データセットやパイプラインを監視するための詳細な手順については、「[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」を参照してください。

## カスタム アクティビティの更新
カスタム アクティビティのコードを更新する場合は、カスタム アクティビティを構築し、新しいバイナリを含む zip ファイルを BLOB ストレージにアップロードします。
    
## <a name="AzureBatch"></a>Azure Batch リンク サービスの使用 
> [AZURE.NOTE]Azure Batch サービスの概要については、「[Azure Batch の技術概要][batch-technical-overview]」をご覧ください。Azure Batch サービスの使用をスムーズに開始するには、「[.NET 向け Azure Batch ライブライの概要][batch-get-started]」をご覧ください。

コンピューティング リソースとして Azure Batch を使用するカスタム .NET アクティビティを実行できます。独自の Azure Batch のプールを作成し、他の構成と併せて VM の数を指定する必要があります。Azure Batch のプールは、顧客に以下の機能を提供します。

1. 作成するプールのコアは 1 個から数千個まで対応
2. 式に基づいて VM 数を自動スケール
3. あらゆるサイズの VM をサポート
4. VM あたりのタスク数を構成可能
5. 無制限の数のタスクをキューに配置


ここでは、前のセクションのチュートリアルで説明した、Azure Batch リンク サービスを使用するための手順の概要を示します。

1. Microsoft Azure 管理ポータルを使用して、Azure Batch アカウントを作成します。手順については、「[Azure Batch の技術概要][batch-create-account]」をご覧ください。Azure Batch のアカウント名とアカウント キーをメモしておきます。 

	[New-AzureBatchAccount][new-azure-batch-account] コマンドレットを使用して、Azure Batch アカウントを作成することもできます。このコマンドレットの使用方法の詳細については、「[Using Azure PowerShell to Manage Azure Batch Account (Azure PowerShell を使用した Azure Batch アカウントの管理)][azure-batch-blog]」をご覧ください。 
2. Azure Batch プールの作成[Azure Batch Explorer ツール][batch-explorer]のソース コードをダウンロードし、コンパイルして使用することもできますが、[.NET 向け Azure Batch ライブラリ][batch-net-library]を使用して Azure Batch プールを作成することもできます。Azure Batch Explorer の使用手順については、[Azure Batch Explorer サンプル チュートリアル][batch-explorer-walkthrough]のページを参照してください。
	
	[New-AzureBatchPool][new-azure-batch-pool] コマンドレットを使用して、Azure Batch プールを作成することもできます。

2. 次の JSON テンプレートを使用して、Azure Batch リンク サービスを作成します。Data Factory エディターには、最初に同様のテンプレートが表示されます。JSON スニペットで、Azure Batch のアカウント名、アカウント キー、プール名を指定します。

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.NOTE]**accountName** プロパティのバッチ アカウントの名前に "**.<リージョン名**" を追加します。例: "mybatchaccount.eastus"。もう 1 つの選択肢は、下のように batchUri エンドポイントを指定することです。

		accountName: "adfteam",
		batchUri: "https://eastus.batch.azure.com",
 
	これらのプロパティについては、[Azure Batch リンク サービスに関する MSDN のトピック](https://msdn.microsoft.com/library/mt163609.aspx)をご覧ください。

2.  Data Factory エディターで、チュートリアルで作成したパイプラインの JSON 定義を開き、**HDInsightLinkedService** を **AzureBatchLinkedService** に置き換えます。
3.  Azure Batch サービスを使用してシナリオをテストできるように、パイプラインの開始時刻と終了時刻を変更できます。 
4.  次の図に示すように、Azure Batch Explorer でスライスの処理に関連する Azure Batch のタスクを確認できます。

	![Azure Batch tasks][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]Data Factory サービスでは、HDInsight の場合と同様、Azure Batch のオンデマンド オプションはサポートされません。Azure データ ファクトリでは、独自の Azure Batch プールのみ使用できます。

## 関連項目

[Azure Data Factory の更新: Azure Batch を使用した ADF カスタム .NET アクティビティの実行](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png
 

<!---HONumber=August15_HO7-->