<properties title="Use custom activities in an Azure Data Factory pipeline" pageTitle="Azure Data Factory パイプラインでカスタム アクティビティを使用する" description="Learn how to create custom activities and use them in an Azure Data Factory pipeline." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure Data Factory パイプラインでカスタム アクティビティを使用する
Azure Data Factory は、パイプライン内で使用され、データの移動や処理を行う**コピー アクティビティ**や **HDInsight アクティビティ**といった組み込みのアクティビティをサポートしています。独自の変換/処理ロジックでカスタム アクティビティを作成し、それをパイプライン内で使用することも可能です。カスタム アクティビティは、HDInsight クラスター上でマップ専用のジョブとして動作するため、パイプライン内でカスタム アクティビティ用に HDInsight クラスターをリンクすることが必要になります。
 
この記事では、カスタム アクティビティを作成し、Azure Data Factory のパイプラインで使用する方法について説明します。カスタム アクティビティを作成して使用するための詳細なチュートリアルも提供します。

## カスタム アクティビティの作成

カスタム アクティビティを作成するには:
 
1.	Visual Studio 2013 で**クラス ライブラリ** プロジェクトを作成します。
2.	[NuGet package for Azure Data Factory][ nuget パッケージ]をダウンロードし、インストールします。インストールの手順は、チュートリアル内にあります。
3. クラス ライブラリ内のソース ファイル最上部にあるステートメントを使用して、以下を追加します。
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. クラスを更新して **ICustomActivity** インターフェイスを実装します。
	<ol type='a'>
		<li>
			 <b>ICustomActivity</b>. からクラスを派生させる
			<br/>
			例: <br/>
			public class <b>MyCustomActivity : ICustomActivity</b>
		</li>

		<li>
			<b>ICustomActivity</b> インターフェイスの  <b>Execute</b>  メソッドを実装
		</li>

	</ol>
5. プロジェクトをコンパイルします。


## パイプライン内でカスタム アクティビティを使用する
パイプライン内でカスタム アクティビティを使用するには:

1.	**プロジェクトの ****bin\debug** または **bin\release** 出力フォルダーから、すべてのバイナリ ファイルを zip ファイルにまとめます。 
2.	**この zip ファイル** を BLOB として **Azure BLOB ストレージ**にアップロードします。 
3.	**パイプライン JSON** ファイルを更新し、パイプライン JSON 内で zip ファイル、カスタム アクティビティ DLL、アクティビティ クラス、および zip ファイルを含む BLOB を参照します。JSON ファイル内では:
	<ol type ="a">
		<li><b>アクティビティの種類</b>は <b>CustomActivity</b> に設定する必要があります。</li>
		<li><b>AssemblyName</b> は Visual Studio プロジェクトからの出力 DLL の名前です。</li>
		<li><b>EntryPoint</b> は <b>ICustomActivity</b> インターフェイスを実装する<b>クラス</b>の<b>名前</b>と<b>名前空間</b>を指定します。</li>
		<li><b>PackageLinkedService</b> はリンクされたサービスで、zip ファイルを含む BLOB を参照します。 </li>
		<li><b>PackageFile</b> は Azure BLOB ストレージにアップロードされた zip ファイルの場所と名前を指定します。</li>
		<li><b>LinkedServiceName</b> は (オンデマンドまたは独自の) HDInsight クラスターをデータ ファクトリにリンクする、リンクされたサービスの名前です。カスタム アクティビティは指定された HDInsight クラスター上でマップ専用のジョブとして動作します。</li>
	</ol>

	

	**JSON の部分例**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## カスタム アクティビティを更新するには
カスタム アクティビティのコードを更新し、ビルドして、新しいバイナリを含む zip ファイルを BLOB ストレージにアップロードする場合は、**New-AzureDataFactoryPipeline** を実行してカスタム アクティビティを使用する既存のパイプラインをオーバーライドする必要があります。カスタム アクティビティを使用するパイプラインを作成すると、指定した BLOB ストレージから、HDInsight クラスターに接続されている BLOB ストレージ上の Azure Data Factory コンテナーに zip ファイルがコピーされます。これは、パイプラインの作成時にのみ行われます。そのため、カスタム アクティビティを更新する場合は、パイプラインを再度作成する必要があります。 

以下のチュートリアルでは、カスタム アクティビティを作成し、それを Azure Data Factory で使用する手順について説明します。このチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルをさらに進めたものです。カスタム アクティビティの動作について確認したい場合は、まず「Azure Data Factory を使ってみる」のチュートリアルを終えてから、このチュートリアルを始めてください。 

## チュートリアル
**前提条件:**


- 「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアル。本チュートリアルを進める前に、この記事のチュートリアルを完了する必要があります。
- Visual Studio 2012 または 2013
- [Windows Azure .NET SDK][azure-developer-center] をダウンロードしてインストールします。
- [NuGet packages for Azure Data Factory][nuget-package] をダウンロードします。
- Azure ストレージの NuGet パッケージをダウンロードしてインストールします。チュートリアルで方法を説明しているため、この手順は省略できます。

### 手順 1.カスタム アクティビティの作成

1.	.NET クラス ライブラリ プロジェクトを作成します。
	<ol type="a">
		<li><b>Visual Studio 2012</b> または <b>Visual Studio 2013</b> を起動します。</li>
		<li><b>[ファイル]</b> をクリックし、<b>[新規作成]</b> にカーソルを合わせ、<b>[プロジェクト]</b> をクリックします。</li> 
		<li><b>[テンプレート]</b> を展開し、<b>[Visual C#]</b> を選択します。このチュートリアルでは C# を使用しますが、カスタム アクティビティの開発には、どの .NET 言語でも使用できます。</li> 
		<li>右側にあるプロジェクトの種類の一覧から <b>[クラス ライブラリ]</b> を選択します。</li>
		<li><b>[プロジェクト名]</b> に「<b>MyCustomActivity</b>」と入力します。</li> 
		<li><b>[場所]</b> は <b>[C:\ADFGetStarted]</b> を選択します。</li>
		<li><b>[OK]</b> をクリックして、プロジェクトを作成します。</li>
	</ol>
2.  <b>[ツール]</b> をクリックし、<b>[NuGet パッケージ マネージャー]</b> にカーソルを合わせ、<b>[パッケージ マネージャー コンソール]</b> をクリックします。
3.	<b>[パッケージ マネージャー コンソール]</b> で次のコマンドを実行し、前もってダウンロードした <b>Microsoft.Azure.Management.DataFactories</b> をインポートします。ダウンロードした Data Factory NuGet パッケージがある場所にフォルダーを置き換えます。

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	<b>[パッケージ マネージャー コンソール]</b> で次のコマンドを実行し、<b>Microsoft.DataFactories.Runtime</b> をインポートします。ダウンロードした Data Factory NuGet パッケージがある場所にフォルダーを置き換えます。

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Windows Azure ストレージ NuGet パッケージをプロジェクトにインポートします。

		Install-Package WindowsAzure.Storage

5. 次の **using** ステートメントをプロジェクト内のソース ファイルに追加します。

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. **名前空間**の名前を **MyCustomActivityNS** に変更します。

		namespace MyCustomActivityNS

7. クラス名を **MyCustomActivity** に変更し、それを以下のように **ICustomActivity** インターフェイスから派生させます。

		public class MyCustomActivity : ICustomActivity

8. **ICustomActivity** インターフェイスの **Execute** メソッドを **MyCustomActivity** クラスに実装 (追加) し、次のサンプル コードをメソッドにコピーします。 

	**inputTables** および **outputTables** パラメーターは、その名前が示すとおりアクティビティの入出力テーブルを表しています。記録するメッセージは、Azure ポータルからダウンロードできるログ ファイル内の **logger** オブジェクト、またはコマンドレットを使用して見ることができます。**extendedProperties** ディクショナリには、アクティビティとその値について JSON ファイル内で指定する拡張プロパティの一覧が含まれています。 

	以下のサンプル コードでは、入力 BLOB 内の行数をカウントし、出力 BLOB に次の内容を生成します: BLOB へのパス、BLOB 内の行数、アクティビティを実行しているコンピューター、現在の日時。

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
										"{0},{1},{2},{3}\n", 
										folderPath, 
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

9. 以下のヘルパー メソッドを追加します。**Execute** メソッドがこれらのヘルパー メソッドを呼び出します。**GetConnectionString** メソッドは Azure ストレージの接続文字列を検索し、**GetFolderPath** メソッドは BLOB の場所を検索します。 


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
   


10. プロジェクトをコンパイルします。[メニュー] から **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。
11. **Windows エクスプローラー**を起動し、ビルドの種類によって **bin\debug** または **bin\release** フォルダーに移動します。
12. <project folder>\bin\Debug フォルダー内のバイナリをすべて含む zip ファイル、**MyCustomActivity.zip** を作成します。
	![zip output binaries][image-data-factory-zip-output-binaries]
13. **MyCustomActivity.zip** を BLOB として BLOB コンテナーにアップロードします。BLOB コンテナー **customactvitycontainer** は、**ADFTutorialDataFactory** 内のリンクされたサービス、**MyBlobStore** が使用する Azure BLOB ストレージ内にあります。  **blobcustomactivitycontainer** が既に存在していなければ、作成します。 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### カスタム アクティビティの実行に使用する HDInsight クラスター用の  リンクされたサービスを作成する
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。試しに、オンデマンド クラスターを使用してみましょう。 

> [WACOM.NOTE] 「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルに加えて「[Azure Data Factory で Pig と Hive を使用する][hivewalkthrough]」のチュートリアルも終えている場合、このリンクされたサービスの作成は省略し、既に ADFTutorialDataFactory 内にあるリンクされたサービスを使用することができます。 

#### オンデマンド HDInsight クラスターを使用するには

1. 以下の内容を記述した **HDInsightOnDemandCluster.json** という名前の JSON ファイルを作成して **C:\ADFGetStarted\Custom** フォルダーに保存します。


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell** を起動し、以下のコマンドを実行して **AzureResourceManager** モードに切り替えます。**AzureResourceManager** モードでは Azure Data Factory コマンドレットが使用できます。

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Custom** フォルダーに移動します。
4. 以下のコマンドを実行して、オンデマンド HDInsight クラスター用のリンクされたサービスを作成します。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### 独自の HDInsight クラスターを使用するには 

1. 以下の内容を記述した **MyHDInsightCluster.json** という名前の JSON ファイルを作成して **C:\ADFGetStarted\Custom** フォルダーに保存します。JSON ファイルを保存する前にクラスター名、ユーザー名、およびパスワードを適切な値に置き換えます。  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell** を起動し、以下のコマンドを実行して **AzureResourceManager** モードに切り替えます。**AzureResourceManager** モードでは Azure Data Factory コマンドレットが使用できます。

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Custom** フォルダーに移動します。
4. 以下のコマンドを実行して、オンデマンド HDInsight クラスター用のリンクされたサービスを作成します。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### 手順 2.パイプライン内でカスタム アクティビティを使用する
「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルをさらに前進させ、他のパイプラインを作成してこのカスタム アクティビティをテストしましょう。

#### カスタム アクティビティの実行に使用する HDInsight クラスター用のリンクされたサービスを作成します。


1.	以下の例に示すようにパイプライン用の JSON を作成し、**ADFTutorialPipelineCustom.json** として **C:\ADFGetStarted\Custom** フォルダーに保存します。**LinkedServiceName** の名前を HDInsight cluster の名前 (**HDInsightOnDemandCluster** または **MyHDInsightCluster**) に変更します。


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
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
				]
			}
		}

	以下の点に注意してください。 

	- アクティビティ セクションには 1 つのアクティビティがあり、その種類は **CustomActivity** です。
	- 「Azure Data Factory を使ってみる」のチュートリアルで使用したものと同じ入力テーブル、**EmpTableFromBlob** を使用します。
	- 次の手順で作成する新しい出力テーブル、**OutputTableForCustom** を使用します。
	- **AssemblyName** は DLL、**MyActivities.dll** の名前に設定します。
	- **EntryPoint** は **MyCustomActivityNS.MyCustomActivity** に設定します。
	- **PackageLinkedService** は、「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルの一部として作成された、**MyBlobStore** に設定します。この BLOB ストアには、カスタム アクティビティの zip ファイルが含まれています。
	- **PackageFile** は **customactivitycontainer/MyCustomActivity.zip** に設定します。
     

4. 出力テーブル (パイプライン JSON によって参照される **OutputTableForCustom**) 用の JSON ファイルを作成し、C:\ADFGetStarted\Custom\OutputTableForCustom.json として保存します。

		

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

 	出力場所は **adftutorial/customactivityoutput/YYYYMMDDHH/** で、YYYYMMDDHH は生成されたスライスの年 (Year)、月 (Month)、日 (Day)、時 (Hour)です。詳細については、「[Developer Reference (開発者向けリファレンス)][adf-developer-reference]」を参照してください。 

5. 次のコマンドを実行して、**ADFTutorialDataFactory** 内に**出力テーブルを作成**します。
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. 次のコマンドを実行して**パイプラインを作成**します。以前の手順で、パイプラインの JSON ファイルは作成済みです。

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. 次の PowerShell コマンドを実行して、作成したパイプラインに**有効期間**を設定します。

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] **StartDateTime** の値を現在の日付に置き換え、**EndDateTime** の値を翌日の日付に置き換えます。StartDateTime と EndDateTime はいずれも UTC 時間で、[ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります。次に例を示します。2014-10-14T16:32:41Z。**EndDateTime** は任意ですが、このチュートリアルでは使用します。 
	> **EndDateTime** を指定しない場合は、"**StartDateTime + 48 時間**" として計算されます。パイプラインを無期限に実行する場合は、**9/9/9999** を **EndDateTime** として指定します。



8. 出力ファイルが **adftutorial** コンテナー内の BLOB ストレージに生成されることを確認します。

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. 出力ファイルを開くと、以下のような出力結果が表示されるはずです。
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(blob location), (number of lines in the blob), (node on which the activity ran), (date time stamp)

10.	[Azure ポータル][azure-preview-portal]または Azure PowerShell コマンドレットを使用してデータ ファクトリ、パイプライン、およびデータ セットを監視します。ポータルから、またはコマンドレットを使用してダウンロードできるログの中のカスタム アクティビティ用コードで、**ActivityLogger** からのメッセージが確認できます。

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
データセットとパイプラインを監視する手順の詳細については、「[Azure Data Factory を使ってみる][adfgetstarted]」を参照してください。      
    
## 関連項目

記事 | 説明
------ | ---------------
[パイプラインが内部設置型のデータを扱えるようにする][use-onpremises-datasources] | この記事には、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーする方法を説明したチュートリアルが記載されています。
[Data Factory で Pig と Hive を使用する][use-pig-and-hive-with-data-factory] | この記事のチュートリアルでは、HDInsight Activity を使用して Hive/Pig スクリプトを実行し、入力データを処理して出力データを生成する方法が学べます。 
[チュートリアル: Data Factory を使用してログ ファイルの移動と処理を行う][adf-tutorial] | この記事には、Azure Data Factory を使用してログ ファイルのデータを洞察へと変換する現実に近いシナリオの実行方法について、詳細なチュートリアルが記載されています。
[Azure Data Factory パイプラインでカスタム アクティビティを使用する][use-custom-activities] | この記事には、カスタム アクティビティを作成してパイプラインで使用する詳細な手順のチュートリアルが記載されています。 
[Azure PowerShell を使用した Azure Data Factory の監視と管理][monitor-manage-using-powershell] | この記事では、Azure PowerShell コマンドレットを使用して Azure Data Factory を監視する方法について説明しています。記事に含まれている例を ADFTutorialDataFactory で試すことができます。
[Data Factory のトラブルシューティング][troubleshoot] | この記事では、Azure Data Factory の問題のトラブルシューティングを行う方法について説明しています。エラーを発生させて (Azure SQL データベースのテーブルを削除する)、ADFTutorialDataFactory でこの記事のチュートリアルを試すことができます。 
[Azure Data Factory Developer Reference (Azure Data Factory 開発者向けリファレンス)][developer-reference] | この開発者向けリファレンスには、コマンドレット、JSON スクリプト、関数などを対象とした包括的なリファレンスが記載されています。 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/ja-jp/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
