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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Azure Data Factory パイプラインでカスタム アクティビティを使用する
Azure のデータのファクトリなどのビルトイン アクティビティのサポート **コピー アクティビティ** と **HDInsight アクティビティ** に移動し、データを処理パイプラインで使用します。独自の変換または処理ロジックで .NET のカスタム アクティビティを作成、パイプラインで、アクティビティを使用することができます。いずれかを使用して実行するアクティビティを構成する、 **Azure HDInsight** クラスターまたは **Azure バッチ** サービスです。

この記事では、カスタム アクティビティを作成し、Azure Data Factory のパイプラインで使用する方法について説明します。カスタム アクティビティを作成して使用するための詳細なチュートリアルも提供します。このチュートリアルは、リンクされている HDInsight サービスを使用します。Azure のバッチを使用するにはサービスを代わりにリンク、リンクの種類のサービスを作成する **AzureBatchLinkedService** JSON のパイプラインのアクティビティのセクションで使用して (* * linkedServiceName * *)。参照してください、 [Azure バッチ リンク サービス](#AzureBatch) 詳細については、カスタム アクティビティでの Azure のバッチの使用に関するセクション。

## 前提条件
最新のダウンロード [NuGet パッケージを Azure のデータのファクトリの][nuget-package] してインストールします。手順については、 [チュートリアル](#SupportedSourcesAndSinks) この記事でします。

## カスタム アクティビティの作成

カスタム アクティビティを作成するには:
 
1.	作成、 **クラス ライブラリ** Visual Studio 2013 でのプロジェクトです。
3. クラス ライブラリ内のソース ファイル最上部にあるステートメントを使用して、以下を追加します。
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. 実装するクラスの更新、 **IDotNetActivity** インターフェイスです。
	<ol type='a'>
	<li>
		クラスを派生する <b>IDotNetActivity</b>です。
		<br/>
		例: <br/>
		パブリック クラス <b>MyDotNetActivity: IDotNetActivity</b>
	</li>

	<li>
		実装する、 <b>Execute</b> メソッドの <b>IDotNetActivity</b> インターフェイス
	</li>

</ol>
5. プロジェクトをコンパイルします。


## パイプラインのカスタム アクティビティの使用
パイプライン内でカスタム アクティビティを使用するには:

1.	**Zip 形式で圧縮** からのすべてのバイナリ ファイル、 **bin \debug** または **bin \release** 、プロジェクトのフォルダーを出力します。 
2.	**、Zip のアップロード** に blob としてファイル、 **Azure blob ストレージ**です。 
3.	更新プログラム、 **JSON のパイプライン** ファイルを zip ファイル、DLL のカスタム アクティビティ、アクティビティのクラス、および JSON のパイプラインでは、zip ファイルを含む blob を参照してください。JSON ファイル内では:
	<ol type ="a">
	<li><b>アクティビティ タイプ</b> に設定する必要があります <b>DotNetActivity</b>です。</li>
	<li><b>AssemblyName</b> 、出力の種類を Visual Studio プロジェクトから DLL の名前を指定します。</li>
	<li><b>EntryPoint</b> を示す、 <b>名前空間</b> と <b>名前</b> の <b>クラス</b> を実装する、 <b>IDotNetActivity</b> インターフェイスです。</li>
	<li><b>PackageLinkedService</b> 、zip ファイルを含む blob を参照するリンクのサービスです。</li>
	<li><b>パッケージファイル</b> 、Azure blob ストレージにアップロードされたいる zip ファイルの名前と場所を指定します。</li>
	<li><b>LinkedServiceName</b> 、HDInsight クラスターをリンクする、リンクされているサービスの名前を指定します (オンデマンドまたは独自) はデータのファクトリにします。カスタム アクティビティは指定された HDInsight クラスター上でマップ専用のジョブとして動作します。</li>
</ol>**部分的な JSON の例**

    		"Name": "MyDotNetActivity",
    		"Type": "DotNetActivity",
    		"Inputs": [{"Name": "EmpTableFromBlob"}],
    		"Outputs": [{"Name": "OutputTableForCustom"}],
			"LinkedServiceName": "myhdinsightcluster",
    		"Transformation":
    		{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## カスタム アクティビティを更新します。
カスタム アクティビティのコードを更新する場合をビルドし、blob ストレージへの新しいバイナリが含まれている zip ファイルをアップロードします。

## <a name="walkthrough" /> チュートリアル
このチュートリアルのカスタム アクティビティを作成して、Azure のデータのファクトリのパイプラインでのアクティビティの使用の詳細な手順について説明します。このチュートリアルは、」からチュートリアルを拡張、 [Azure のデータのファクトリを使い始める][adfgetstarted]です。カスタム アクティビティの動作について確認したい場合は、まず「Azure Data Factory を使ってみる」のチュートリアルを終えてから、このチュートリアルを始めてください。

**前提条件:**


- チュートリアルから [Azure のデータのファクトリを使い始める][adfgetstarted]です。このチュートリアルを実行する前に、この記事からチュートリアルを完了する必要があります。
- Visual Studio 2012 または 2013
- ダウンロードしてインストール [Azure .NET SDK][azure-developer-center]
- 最新のダウンロード [NuGet パッケージを Azure のデータのファクトリの][nuget-package] してインストールします。手順はこのチュートリアルにあります。
- Azure ストレージの NuGet パッケージをダウンロードしてインストールします。チュートリアルで方法を説明しているため、この手順は省略できます。

## 手順 1: カスタム アクティビティを作成します。

1.	.NET クラス ライブラリ プロジェクトを作成します。
	<ol type="a">
	<li>起動 <b>Visual Studio 2012</b> または <b>Visual Studio 2013</b>です。</li>
	<li>クリックして <b>ファイル</b>, 、順にポイント <b>新規</b>, 、] をクリック <b>プロジェクト</b>です。</li> 
	<li>展開 <b>テンプレート</b>, 、選択 <b>Visual c#</b>です。このチュートリアルでは C# を使用しますが、カスタム アクティビティの開発には、どの .NET 言語でも使用できます。</li> 
	<li>選択 <b>クラス ライブラリ</b> 、右側のプロジェクトの種類の一覧からです。</li>
	<li>入力 <b>MyDotNetActivity</b> の <b>名前</b>です。</li> 
	<li>選択 <b>C:\ADFGetStarted</b> の <b>場所</b>です。</li>
	<li><b>[OK]</b> をクリックしてプロジェクトを作成します。</li>
</ol>
2.  クリックして <b>ツール</b>, 、順にポイント <b>NuGet Package Manager</b>, 、] をクリック <b>パッケージ マネージャー コンソール</b>です。
3.	 <b>パッケージ マネージャー コンソール</b>, 、インポートするには、次のコマンドを実行する <b>Microsoft.Azure.Management.DataFactories</b>です。 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	 <b>パッケージ マネージャー コンソール</b>, 、インポートするには、次のコマンドを実行する <b>Microsoft.DataFactories.Runtime</b>です。ダウンロードした Data Factory NuGet パッケージがある場所にフォルダーを置き換えます。

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. Azure ストレージ NuGet パッケージをプロジェクトにインポートします。

		Install-Package Azure.Storage

5. 次の追加 **を使用して** ステートメントをプロジェクトでソース ファイルにします。

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. 名前を変更、 **名前空間** に **MyDotNetActivityNS**です。

		namespace MyDotNetActivityNS

7. クラスの名前を変更 **MyDotNetActivity** から派生し、 **IDotNetActivity** インターフェイスの次のようです。

		public class MyDotNetActivity : IDotNetActivity

8. 実装する (追加)、 **Execute** のメソッド、 **IDotNetActivity** へのインターフェイス、 **MyDotNetActivity** クラスおよびメソッドに次のサンプル コードをコピーします。

	 **InputTables** と **outputTables** パラメーターはその名が示すように、アクティビティの入力呼び出し力のテーブルを表します。使用してログに記録するメッセージが表示、 **logger** 、Azure ポータルまたはコマンドレットを使用してからダウンロード可能なログ ファイル内のオブジェクトです。 **ExtendedProperties** ディクショナリには、アクティビティとその値の JSON ファイルで指定した拡張プロパティの一覧が含まれています。

	次のサンプル コードは、入力 blob 内の行の数をカウントし、出力 blob では、次のコンテンツを生成します。 アクティビティが実行される、現在の日付と時刻、マシンは、blob 内の行の数は、blob へのパス。

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

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
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
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
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. 以下のヘルパー メソッドを追加します。 **Execute** メソッドはこれらのヘルパー メソッドを呼び出します。 **GetConnectionString** メソッドは、Azure のストレージ接続文字列を取得し、 **GetFolderPath** メソッドは、blob の場所を取得します。


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. プロジェクトをコンパイルします。クリックして **ビルド** ] をクリックし、メニューから **ソリューションのビルド**です。
11. 起動 **Windows エクスプ ローラー**, に移動して **bin \debug** または **bin \release** に応じての種類のビルド フォルダーです。
12. Zip ファイルを作成する **MyDotNetActivity.zip** 内のすべてのバイナリが含まれている、 <project folder>\bin\Debug フォルダーです。
13. アップロード **MyDotNetActivity.zip** 、blob コンテナーに blob として: **customactvitycontainer** 、Azure で blob ストレージを **MyBlobStore** サービスにリンクされている、 **ADFTutorialDataFactory** を使用します。Blob コンテナーを作成する **blobcustomactivitycontainer** が既に存在しない場合。 


## 手順 2: パイプラインでのカスタム アクティビティを使用します。
このステップでを実行する手順を次に示します。

1. リンクされたマップのみジョブとして、カスタム アクティビティを実行する HDInsight クラスターのサービスを作成します。 
2. このサンプルでは、パイプラインを生成する出力テーブルを作成します。
3. 作成し、手順 1. で作成したカスタム アクティビティを使用するパイプラインを実行します。 
 
### カスタム アクティビティを実行するために使用する HDInsight クラスター用のリンクのサービスを作成します。
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。

> [AZURE.NOTE]実行時に、.NET、アクティビティのインスタンスが、HDInsight クラスター内の 1 つのワーカー ノードでのみ実行します。拡大して複数のノード上で実行することはできません。.NET のアクティビティの複数のインスタンスは、HDInsight クラスターの別々 のノードで並列に実行できます。

拡張した場合、 [Azure のデータのファクトリを使い始める][adfgetstarted] から、このチュートリアルでは、チュートリアル [の Pig の使用と Azure のデータのファクトリでの Hive][hivewalkthrough], 、このリンクのサービスの作成を省略して、リンクに、ADFTutorialDataFactory に既にあるサービスを使用することができます。


#### オンデマンド HDInsight クラスターを使用するには

1.  **Azure ポータル**, 、] をクリックして **作成者および展開** データ工場出荷時のホーム ページです。
2. データ ファクトリ エディターで、クリックして **新しいコンピューティング** クリックし、コマンド バーから **オンデマンドでの HDInsight クラスター** ] メニューからです。
2. JSON スクリプトでは、次のように 
	1.  **ClusterSize** プロパティでは、HDInsight クラスターのサイズを指定します。
	2.  **JobsContainer** プロパティでは、クラスターのログが格納される既定のコンテナーの名前を指定します。このチュートリアルでは、次のように指定します。 **adfjobscontainer**です。
	3.  **TimeToLive** プロパティ、どのくらいの期間、顧客が削除される前にアイドル状態できるを指定します。 
	4.  **バージョン** プロパティを使用する場合、HDInsight のバージョンを指定します。このプロパティを除外する場合は、最新のバージョンが使用します。  
	5.  **LinkedServiceName**, 、指定 **StorageLinkedService** ことを Get で作成したチュートリアルを開始します。 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. クリックして **展開** リンクされているサービスをデプロイするには、コマンド バー。
   
#### 独自の HDInsight クラスターを使用するには 

1.  **Azure ポータル**, 、] をクリックして **作成者および展開** データ工場出荷時のホーム ページです。
2.  **データ工場出荷時のエディター**, 、] をクリックして **新しいコンピューティング** クリックし、コマンド バーから **HDInsight クラスター** ] メニューからです。
2. JSON スクリプトでは、次のように 
	1.  **ClusterUri** プロパティで、HDInsight の URL を入力します。例: https://<clustername>.azurehdinsight.net/     
	2.  **UserName** プロパティ、HDInsight クラスターへのアクセス権を持つユーザーの名前を入力します。
	3.  **パスワード** プロパティでは、ユーザーのパスワードを入力します。 
	4.  **LinkedServiceName** プロパティ、入力 **StorageLinkedService**です。これは、入門チュートリアルで作成したリンクのサービスです。 

2. クリックして **展開** リンクされているサービスをデプロイするには、コマンド バー。

### 出力テーブルを作成します。

1.  **データ工場出荷時のエディター**, 、] をクリックして **新しいデータセット**, 、順にクリック **Azure Blob ストレージ** コマンド バーからです。
2. 右側のウィンドウでは、JSON スクリプトを次の JSON スクリプトに置き換えます。

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}


 	出力場所が **adftutorial/customactivityoutput/YYYYMMDDHH/** YYYYMMDDHH が年、月、日、および開発されている、スライスの時間です。参照してください [開発者向けリファレンス][adf-developer-reference] の詳細。

2. クリックして **展開** 、テーブルを展開するには、コマンド バーにします。


### 作成して、カスタム アクティビティを使用するパイプラインの実行
   
1. データ ファクトリ エディターで、クリックして **新しいパイプライン** コマンド バーでします。クリックして、コマンドが表示されない場合は、 **しています.(省略記号)** 表示されます。 
2. 次の JSON スクリプトを右側のウィンドウで、JSON を置き換えます。独自のクラスターを使用して、作成する手順の後にかどうか、 **HDInsightLinkedService** サービスでは、リンクされている置換 **HDInsightOnDemandLinkedService** で **HDInsightLinkedService** で、次の JSON です。 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
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

	> [AZURE.NOTE]置換 **StartDateTime** 値と現在の日付の前に 3 日間は、および **EndDateTime** 、現在の日付を持つ値です。StartDateTime と EndDateTime の両方がである必要があります [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)です。例: 2014年-10-14T16:32:41Z です。出力テーブルは毎日生成されるようスケジュール設定されているので、3 つのスライスが生成されることになります。

	以下の点に注意してください。

	- ［ 操作 ］ セクションの 1 つのアクティビティが存在し、型では。 **DotNetActivity**です。
	- 同じ入力テーブルを使用して **EmpTableFromBlob** で、Get を使用したチュートリアルを開始します。
	- 新しい出力テーブルを使用して **OutputTableForCustom** 次の手順で作成します。
	- **AssemblyName** 、DLL の名前に設定されている: **MyActivities.dll**です。
	- **EntryPoint** に設定されている **MyDotNetActivityNS.MyDotNetActivity**です。
	- **PackageLinkedService** に設定されている **MyBlobStore** 」からチュートリアルの一部として作成された [Azure のデータのファクトリを使い始める][adfgetstarted]です。この BLOB ストアには、カスタム アクティビティの zip ファイルが含まれています。
	- **パッケージファイル** に設定されている **customactivitycontainer/MyDotNetActivity.zip**です。
     
4. クリックして **展開** 、パイプラインを展開するには、コマンド バーにします。
8. 出力ファイルは、blob ストレージ内に生成されていることを確認、 **adftutorial** コンテナーです。

	![カスタム アクティビティからの出力します。][image-data-factory-ouput-from-custom-activity]

9. 出力ファイルを開くと、以下のような出力結果が表示されるはずです。
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19時 23分: 28

	(blob の場所) (blob の名前)、(、blob 内の行の番号)、(ノードは、アクティビティが実行された)、(日付タイムスタンプ)

10.	使用して、 [Azure ポータル][azure-preview-portal] または Azure PowerShell のコマンドレットをデータの工場出荷時、パイプライン、およびデータ セットを監視します。メッセージが表示、 **ActivityLogger** 、ログ内で、カスタム アクティビティのコードで、ポータルまたはコマンドレットを使用してからダウンロードできます。

	![カスタム アクティビティからのログをダウンロードします。][image-data-factory-download-logs-from-custom-activity]
   
参照してください [Azure のデータのファクトリを使い始める][adfgetstarted] データセットおよびパイプラインを監視するための詳細な手順についてです。
    
## <a name="AzureBatch"></a> Azure のバッチを使用してサービスのリンク 
> [AZURE.NOTE]参照してください [Azure バッチの技術概要][batch-technical-overview] Azure バッチの概要については、サービスし、を参照してください [Getting Started with .NET 用 Azure バッチ ライブラリ][batch-get-started] 、バッチの Azure サービスをすばやく開始します。

次に、前のセクションで説明したチュートリアルでは、Azure のバッチのリンクされているサービスを使用するための大まかな手順を示します。

1. Azure 管理ポータルを使用してバッチの Azure アカウントを作成します。参照してください [Azure バッチの技術概要][batch-create-account] 手順についての記事です。バッチの Azure アカウント名とアカウント キーを注意してください。 

	使用することもできます [新規 AzureBatchAccount][new-azure-batch-account] コマンドレットをバッチの Azure アカウントを作成します。参照してください [Azure バッチ アカウントの管理を Azure の PowerShell を使用して][azure-batch-blog] の詳細についてはこのコマンドレットを使用します。 
2. Azure のバッチ プールを作成します。ダウンロードして使用することができます、 [バッチ エクスプ ローラーの Azure ツール][batch-explorer] (または) を使用して [Azure .NET 用のバッチのライブラリ][batch-net-library] Azure バッチ プールを作成します。参照してください [Azure バッチ エクスプ ローラーのサンプルのチュートリアル][batch-explorer-walkthrough] Azure バッチ エクスプ ローラーを使用するための手順の詳細についてです。
	
	使用することもできます [新規 AzureBatchPool][new-azure-batch-pool] コマンドレットを Azure のバッチのプールを作成します。

2. 次の JSON テンプレートを使用して Azure バッチ リンクされているサービスを作成します。データの工場出荷時のエディター最初にするのと同様、テンプレートを表示します。JSON のスニペットで、Azure のバッチのアカウント名、アカウントのキーとプールの名前を指定します。

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	参照してください [Azure バッチのリンクされているサービスの MSDN のトピック「](https://msdn.microsoft.com/library/mt163609.aspx) これらプロパティの説明。

2.  データ ファクトリ エディターで開くチュートリアルと置換で作成したパイプラインの JSON 定義 **HDInsightLinkedService** で **AzureBatchLinkedService**です。
3.  バッチの Azure サービスを使用したシナリオをテストするには、開始時刻と終了時刻、パイプラインを変更することがあります。 
4.  次の図に示すように、Azure のバッチのエクスプ ローラーでのスライスの処理に関連する Azure のバッチのタスクを確認できます。

	![Azure のバッチのタスク][image-data-factory-azure-batch-tasks]

## 関連項目

[Azure データ工場出荷時の更新プログラム: Azure のバッチを使用して、ADF カスタム .NET の実行のアクティビティ](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)です。

[batch-net-library]: ./batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ./batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ./batch/batch-technical-overview.md
[batch-get-started]: ./batch/batch-dotnet-get-started.md
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

<!---HONumber=GIT-SubDir-->