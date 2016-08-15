<properties
	pageTitle="Azure Data Factory パイプラインでカスタム アクティビティを使用する"
	description="カスタム アクティビティを作成して Azure Data Factory パイプラインで使用する方法について説明します。"
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
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Azure Data Factory パイプラインでカスタム アクティビティを使用する
Azure Data Factory パイプラインでは、2 種類のアクティビティを使用できます。
 
- [サポートされるデータ ストア](data-factory-data-movement-activities.md#supported-data-stores)の間でデータを移動する[データ移動アクティビティ](data-factory-data-movement-activities.md)。
- Azure HDInsight、Azure Batch、Azure Machine Learning などのコンピューティングを使用してデータを変換/処理する[データ変換アクティビティ](data-factory-data-transformation-activities.md)。たとえば、HDInsight Hive および Machine Learning Batch の実行です。

Azure Data Factory でサポートされていないデータ ストアとの間でデータを移動する必要がある場合は、独自のデータ移動ロジックでカスタム .NET アクティビティを作成し、パイプラインでそのアクティビティを使用できます。

同じように、Data Factory でサポートされていない方法でデータを変換/処理する必要がある場合は、独自のデータ処理ロジックでカスタム アクティビティを作成し、パイプラインでそのアクティビティを使用できます。
 
**Azure Batch** サービスまたは **Azure HDInsight** クラスターを使用して実行するようにカスタム .NET アクティビティを構成できます。

以下のチュートリアルでは、カスタム .NET アクティビティを作成し、パイプラインでそのカスタム アクティビティを使用する手順について説明します。このチュートリアルでは、**Azure Batch** のリンクされたサービスを使用します。代わりに Azure HDInsight のリンクされたサービスを使用するには、**HDInsight** (独自の HDInsight クラスターを使用する場合) または **HDInsightOnDemand** (オンデマンドで Data Factory を使用して HDInsight クラスターを作成する場合) 型のリンクされたサービスを作成し、パイプライン JSON (**linkedServiceName**) のアクティビティ セクションで使用します。Azure HDInsight を使用してカスタム アクティビティを実行する方法の詳細については、「[Azure HDInsight のリンクされたサービスの使用](#use-azure-hdinsight-linked-services)」セクションを参照してください。


## チュートリアル 

### 前提条件

- Visual Studio 2012/2013/2015
- [Azure .NET SDK][azure-developer-center] をダウンロードし、インストールします。


### Azure Batch の前提条件
このチュートリアルでは、コンピューティング リソースとして Azure Batch を使用して、カスタム .NET アクティビティを実行します。Azure Batch サービスの概要については、「[Azure Batch の技術概要][batch-technical-overview]」をご覧ください。Azure Batch サービスの使用をスムーズに開始するには、「[.NET 向け Azure Batch ライブライの概要][batch-get-started]」を参照してください。

このチュートリアルでは、VM のプールと Azure Batch アカウントを作成する必要があります。手順は次のようになります。

1. [Azure ポータル](http://manage.windowsazure.com)を使用して、**Azure Batch アカウント**を作成します。手順については、「[Azure Batch アカウントの作成と管理][batch-create-account]」の記事を参照してください。Azure Batch のアカウント名とアカウント キーをメモしておきます。

	[New-AzureBatchAccount][new-azure-batch-account] コマンドレットを使用して、Azure Batch アカウントを作成することもできます。このコマンドレットの使用方法の詳細については、「[Using Azure PowerShell to Manage Azure Batch Account (Azure PowerShell を使用した Azure Batch アカウントの管理)][azure-batch-blog]」をご覧ください。
2. **Azure Batch プール**を作成します。
	1. [Azure ポータル](https://portal.azure.com)で、左側のメニューの **[参照]** をクリックしてから、**[Batch アカウント]** をクリックします。
	2. Azure Batch アカウントを選択して、**[Batch アカウント]** ブレードを開きます。
	3. **[プール]** タイルをクリックします。
	4. **[プール]** ブレードで、ツールバーの [追加] ボタンをクリックしてプールを追加します。
		1. プールの ID を入力します (**プール ID**)。**プールの ID** は、Data Factory ソリューションを作成するときに必要になります。
		2. オペレーティング システム ファミリ設定には、**Windows Server 2012 R2** を指定します。
		3. **ノード価格レベル**を選択します。
		3. **ターゲットの専用数**の設定値として、「**2**」と入力します。
		4. **ノードごとの最大タスク**の設定値として、「**2**」と入力します。
	5. **[OK]** をクリックすると、プールが作成されます。
 
	[New-AzureBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) コマンドレットを使用して、Azure Batch プールを作成することもできます。

### 手順の概要 
1.	Data Factory パイプラインを使用する**カスタム アクティビティを作成**します。このサンプルのカスタム アクティビティには、データ変換/処理ロジックが格納されます。
	1.	Visual Studio で .NET クラス ライブラリ プロジェクトを作成し、入力データを処理するコードを追加して、プロジェクトをコンパイルします。
	2.	出力フォルダーにあるすべてのバイナリ ファイルと PDB (省略可能) ファイルを zip 形式で圧縮します。
	3.	zip ファイルを Azure Blob Storage にアップロードします。詳細な手順については、「カスタム アクティビティの作成」セクションを参照してください。
2. **カスタム アクティビティを使用する Azure Data Factory を作成する**:
	1. Azure Data Factory を作成します。
	2. リンクされたサービスを作成します。
		1. AzureStorageLinkedService: BLOB にアクセスするためのストレージの資格情報を指定します。
		2. AzureBatchLinkedService: コンピューティングとして Azure Batch を指定します。
	3. データセットを作成します。
		1. InputDataset: 入力 BLOB のストレージ コンテナーとフォルダーを指定します。
		1. OuputDataset: 出力 BLOB のストレージ コンテナーとフォルダーを指定します。
	2. カスタム アクティビティを使用するパイプラインを作成します。
	3. パラメーターを実行してテストします。
	4. パイプラインをデバッグします。

## カスタム アクティビティの作成
.NET カスタム アクティビティを作成するには、その **IDotNetActivity** インターフェイスを実装したクラスを含む **.NET クラス ライブラリ** プロジェクトを作成します。このインターフェイスには、[Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) という 1 つのメソッドのみが含まれ、そのシグネチャは次のとおりです。

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        

このメソッドには次の 4 つのパラメーターがあります。

- **linkedServices**。入力/出力データ ソースを Data Factory にリンクする、リンクされたサービスの列挙可能な一覧です。このサンプルでは、入力と出力の両方に使用される Azure Storage 型のリンクされたサービスは 1 つのみです。
- **datasets**。データセットの列挙可能な一覧です。このパラメーターを使用すると、入力と出力のデータセットに定義されている場所とスキーマを取得できます。
- **activity**。このパラメーターは、現在のコンピューティング エンティティ (この場合は Azure Batch) を表します。
- **logger**。logger を使用すると、パイプラインの “User” ログとして表示されるデバッグ コメントが出力されます。

メソッドから、今後、カスタム アクティビティの連結に使用できるディクショナリが返されます。この機能はまだ実装されていないため、メソッドからは空のディクショナリが返されるだけです。

### 手順 
1.	**.NET クラス ライブラリ** プロジェクトを作成します。
	<ol type="a">
		<li><b>Visual Studio 2015</b>、<b>Visual Studio 2013</b>、または <b>Visual Studio 2012</b> を起動します。</li>
		<li><b>[ファイル]</b> をクリックし、<b>[新規作成]</b> をポイントして、<b>[プロジェクト]</b> をクリックします。</li>
		<li><b>[テンプレート]</b> を展開し、<b>[Visual C#]</b> を選択します。このチュートリアルでは C# を使用しますが、カスタム アクティビティの開発には、どの .NET 言語でも使用できます。</li>
		<li>右側にあるプロジェクトの種類の一覧から <b>[クラス ライブラリ]</b> を選択します。</li>
		<li><b>[プロジェクト名]</b> に「<b>MyDotNetActivity</b>」と入力します。</li>
		<li><b>[場所]</b> で <b>[C:\ADFGetStarted]</b> を選択します。</li>
		<li><b>[OK]</b> をクリックしてプロジェクトを作成します。</li>
	</ol>
2.  **[ツール]** をクリックし、**[NuGet パッケージ マネージャー]** をポイントして、**[パッケージ マネージャー コンソール]** をクリックします。
3.	パッケージ マネージャー コンソールで、次のコマンドを実行して **Microsoft.Azure.Management.DataFactories** をインポートします。

		Install-Package Microsoft.Azure.Management.DataFactories

4. **Azure Storage** NuGet パッケージをプロジェクトにインポートします。

		Install-Package Azure.Storage

5. 次の **using** ステートメントをプロジェクト内のソース ファイルに追加します。

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6. **namespace** の名前を **MyDotNetActivityNS** に変更します。

		namespace MyDotNetActivityNS

7. クラス名を **MyDotNetActivity** に変更し、次に示すように **IDotNetActivity** インターフェイスから派生させます。

		public class MyDotNetActivity : IDotNetActivity

8. **IDotNetActivity** インターフェイスの **Execute** メソッドを **MyDotNetActivity** クラスに実装 (追加) し、次のサンプル コードをメソッドにコピーします。

	次のサンプルでは、データ スライスに関連付けられている各 BLOB で検索語句 ("Microsoft") の出現回数をカウントします。

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
			// to get extended properties (for example: SliceStart)
			DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

			// to log all extended properties			
			IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
			logger.Write("Logging extended properties if any...");
			foreach (KeyValuePair<string, string> entry in extendedProperties)
			{
				logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
			}
		
            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; 

			// To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
       			// with the data slice. definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

			// The dictionary can be used to chain custom activities together in the future.
			// This feature is not implemented yet, so just return an empty dictionary.  

            return new Dictionary<string, string>();
        }

9. 以下のヘルパー メソッドを追加します。**Execute** メソッドがこれらのヘルパー メソッドを呼び出します。**GetConnectionString** メソッドは Azure Storage の接続文字列を取得し、**GetFolderPath** メソッドは BLOB の場所を取得します。最も重要な点は、**Calculate** メソッドは、各 BLOB で反復処理されるコードを分離することです。

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.
		/// </summary>

		private static string GetFolderPath(Dataset dataArtifact)
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

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
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

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

	GetFolderPath メソッドは、データセットが指すフォルダーのパスを返し、GetFileName メソッドは、データセットが指す BLOB/ファイルの名前を返します。{Year}、{Month}、{Day} などの変数を使用して folderPath を定義している場合、メソッドは、文字列を実行時の値で置き換えずにそのまま返します。SliceStart、SliceEnd などにアクセスする方法の詳細については、「[拡張プロパティへのアクセス](#access-extended-properties)」セクションを参照してください。
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "adftutorial/inputfolder/",
	
	Calculate メソッドは、入力ファイル (フォルダー内の BLOB) 内のキーワード Microsoft のインスタンス数を計算します。検索語句 ("Microsoft") は、コードにハード コーディングされています。

10. プロジェクトをコンパイルします。メニューの **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。
11. **Windows エクスプローラー**を起動し、ビルドの種類に応じて、**bin\\debug** フォルダーまたは **bin\\release** フォルダーに移動します。
12. <プロジェクト フォルダー>\\bin\\Debug フォルダー内のバイナリをすべて含む zip ファイル、**MyDotNetActivity.zip** を作成します。エラー発生時の問題の原因となったソース コードの行番号など、追加情報を取得するために、**MyDotNetActivity.pdb** ファイルを含めることもできます。カスタム アクティビティの zip ファイル内のファイルは、いずれもサブフォルダーがない**最上位レベル**に置く必要があります。

	![バイナリ出力ファイル](./media/data-factory-use-custom-activities/Binaries.png)
13. **MyDotNetActivity.zip** を BLOB として **customactivitycontainer** にアップロードします。この BLOB コンテナーは、**ADFTutorialDataFactory** 内の **AzureStorageLinkedService** リンク サービスが使用する Azure Blob Storage 内にあります。BLOB コンテナー **customactivitycontainer** が既に存在していなければ、作成します。

> [AZURE.NOTE] この .NET アクティビティ プロジェクトを Visual Studio で Data Factory プロジェクトを含むソリューションに追加し、Data Factory アプリケーション プロジェクトから .NET アクティビティ プロジェクトへの参照を追加する場合は、最後の 2 つの手順 (手動での zip ファイルの作成と Azure Blob Storage へのアップロード) を実行する必要はありません。Data Factory エンティティを Visual Studio を使用して発行すると、これらの手順は発行プロセスによって自動的に実行されます。Visual Studio を使用した Data Factory エンティティの作成と発行の詳細については、記事「[Visual Studio を使用した初めてのパイプラインの作成](data-factory-build-your-first-pipeline-using-vs.md)」と記事「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](data-factory-copy-activity-tutorial-using-visual-studio.md)」を参照してください。

### Execute メソッド

ここでは、**Execute** メソッドのコードの詳細と注意事項について説明します。
 
1. 入力コレクションを反復処理するメンバーは、[Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 名前空間にあります。BLOB コレクションの反復処理では、**BlobContinuationToken** クラスを使用する必要があります。基本的に、既存のループのメカニズムとして、トークンに do-while ループを使用する必要があります。詳細については、「[.NET から BLOB ストレージを使用する方法](../storage/storage-dotnet-how-to-use-blobs.md)」を参照してください。基本的なループを次に示します。

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{   
			// Get the list of input blobs from the input storage client object.
			BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    					              true,
    	                              BlobListingDetails.Metadata,
    	                              null,
    	                              continuationToken,
    	                              null,
    	                              null);
			// Return a string derived from parsing each blob.
    		output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
		} while (continuationToken != null);

	詳細については、[ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) メソッドのドキュメントを参照してください。

2.	論理的には、BLOB セットを操作するコードを do-while ループ内に配置します。**Execute** メソッドの場合、do-while ループは BLOB の一覧を **Calculate** というメソッドに渡します。Calculate メソッドは、セグメント内のすべての BLOB を反復処理した結果である **output** という文字列変数を返します。

	また、**Calculate** メソッドに渡された BLOB 内の検索語句 (**Microsoft**) の出現回数を返します。

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	**Calculate** メソッドの処理が完了すると、新しい BLOB に出力されます。そのため、処理対象の BLOB セットごとに、結果を新しい BLOB に出力できます。新しい BLOB に出力するには、まず出力データセットを検索します。

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	また、このコードは、**GetFolderPath** というヘルパー メソッドも呼び出して、フォルダー パス (ストレージ コンテナー名) を取得します。
 
			folderPath = GetFolderPath(outputDataset);

	**GetFolderPath** は、FolderPath というプロパティがある AzureBlobDataSet に DataSet オブジェクトをキャストします。
			
			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
			
			return blobDataset.FolderPath;

5.	コードで、ファイル名 (BLOB 名) を取得する **GetFileName** メソッドを呼び出します。このコードは、フォルダー パスを取得する上記のコードと似ています。

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	新しい URI オブジェクトを作成してファイル名が出力されます。URI コンストラクターは **BlobEndpoint** プロパティを使用して、コンテナー名を返します。フォルダー パスとファイル名が追加され、出力 BLOB URI が構築されます。

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	ファイル名が出力されるので、Calculate メソッドから出力文字列を新しい BLOB に出力できるようになります。

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## Azure ポータルを使用した Data Factory の作成

「**カスタム アクティビティの作成**」セクションでは、カスタム アクティビティを作成し、バイナリと PDB ファイルを含む zip ファイルを Azure Blob コンテナーにアップロードしました。ここでは、**カスタム アクティビティ**を使用した**パイプライン**で Azure **Data Factory** を作成します。
 
カスタム アクティビティの入力データセットは、Blob Storage の入力フォルダー (adftutorial\\inputfolder) 内にある BLOB (ファイル) を表します。アクティビティの出力データセットは、Blob Storage の出力フォルダー (adftutorial\\outputfolder) 内にある出力 BLOB を表します。

次の内容を含む **file.txt** という名前のファイルを作成し、**adftutorial\\inputfolder** にアップロードします (adftutorial は Azure BLOB コンテナーの名前、inputfolder はそのコンテナー内のフォルダーの名前です)。

	test custom activity Microsoft test custom activity Microsoft

入力フォルダーに複数のファイルが含まれている場合でも、入力フォルダーは Azure Data Factory のスライスに相当します。パイプラインで各スライスが処理されるときに、カスタム アクティビティでは、そのスライスの入力フォルダー内にあるすべての BLOB が反復処理されます。

adftutorial\\output フォルダーには、1 行または複数行 (入力フォルダーの BLOB 数と同じ数) が含まれる 1 つの出力ファイルが作成されます。
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


このセクションで実行する手順を次に示します。

1. **Data Factory** を作成します。
2. カスタム アクティビティが実行される VM の Azure Batch プールの**リンクされたサービス**と、入力/出力 BLOB を保持する Azure Storage を作成します。
2. カスタム アクティビティの入出力を示す入力と出力の**データセット**を作成します。
3. カスタム アクティビティを使用する**パイプライン**を作成します。
4. **データ ファクトリ**を作成します。これらのエンティティを、Azure に発行するときに作成します。

> [AZURE.NOTE] まだ行っていない場合は、**file.txt** を作成し、BLOB コンテナーにアップロードします。上記の手順を参照してください。

### 手順 1: Data Factory を作成する

1.	Azure ポータルにログインした後、次の操作を行います。
	1.	左側のメニューの **[新規]** をクリックします。
	2.	**[新規]** ブレードの **[データ + 分析]** をクリックします。
	3.	**[データ分析]** ブレードの **[Data Factory]** をクリックします。
2.	**[新しい Data Factory]** ブレードで、[名前] フィールドに「**CustomActivityFactory**」と入力します。Azure Data Factory の名前はグローバルに一意にする必要があります。**""CustomActivityFactory" という名前の Data Factory は使用できません"** というエラーが発生した場合は、Data Factory の名前を変更して (**yournameCustomActivityFactory** など) 作成し直してください。
3.	**[リソース グループ名]** をクリックし、既存のリソース グループを選択するか、新しいリソース グループを作成します。
4.	作成する Data Factory に適した**サブスクリプション**と**リージョン**を使用していることを確認します。
5.	**[新しい Data Factory]** ブレードで **[作成]** をクリックします。
6.	作成した Data Factory は、Azure ポータルの**ダッシュボード**に表示されます。
7.	Data Factory が正常に作成されると、Data Factory の内容を表示する [Data Factory] ブレードが表示されます。

### 手順 2. リンク サービスを作成する

リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。この手順では、Azure Storage アカウントと Azure Batch アカウントを Data Factory にリンクします。

#### Azure Storage のリンクされたサービスを作成する

1.	**[CustomActivityFactory]** の **[Data Factory]** ブレードで、**[作成およびデプロイ]** をクリックします。Data Factory エディタが起動します。
2.	コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure Storage]** を選択します。Azure Storage のリンクされたサービスを作成するための JSON スクリプトがエディターに表示されます。
3.	**accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。ストレージ アクセス キーを取得する方法については、「[ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。
4.	コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

#### Azure Batch のリンクされたサービスの作成

2. Data Factory エディターで、コマンド バーの **[新規計算]** をクリックし、メニューの **[Azure Batch]** を選択します。
3. JSON スクリプトで、以下の手順を実行します。
	1. **accountName** プロパティに Azure Batch アカウント名を指定します。**[Azure Batch アカウント]** ブレードの **URL** は、http://**accountname**.region.batch.azure.com という形式です。JSON の **batchUri** プロパティでは、この URL から **"accountname" を削除**し、**accountName** JSON プロパティの**accountname** を必要があります。
	2. **accessKey** プロパティに Azure Batch アカウント キーを指定します。
	3. **poolName** プロパティに、前提条件の一部として作成したプールの名前を指定します。プールの名前の代わりにプールの ID を指定することもできます。
	4. **batchUri** プロパティに Azure Batch の URI を指定します。**accountName** プロパティについては、上記を参照してください。例: https://westus.batch.azure.com.
	5. **linkedServiceName** プロパティに **AzureStorageLinkedService** を指定します。
		
			{
			  "name": "AzureBatchLinkedService",
			  "properties": {
			    "type": "AzureBatch",
			    "typeProperties": {
			      "accountName": "myazurebatchaccount",
				  "batchUri": "https://westus.batch.azure.com",
			      "accessKey": "<yourbatchaccountkey>",
			      "poolName": "myazurebatchpool",
			      "linkedServiceName": "AzureStorageLinkedService"
			    }
			  }
			}

		**poolName** プロパティでは、プール名の代わりにプール ID を指定することもできます。

	> [AZURE.NOTE] Data Factory サービスでは、HDInsight の場合と同様、Azure Batch のオンデマンド オプションはサポートされません。Azure データ ファクトリでは、独自の Azure Batch プールのみ使用できます。
	
### 手順 3: データセットを作成する
この手順では、入力データと出力データを示すデータセットを作成します。

#### 入力データセットの作成
1.	Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューから **[Azure BLOB ストレージ]** をクリックします。
2.	右側のウィンドウの JSON を、次の JSON スニペットに置き換えます。

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "AzureStorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	このチュートリアルで後半では、開始時刻 2015-11-16T00:00:00Z、終了時刻 2015-11-16T05:00:00Z のパイプラインを作成します。データを毎時生成するようにスケジュールしているので、5 個の入力/出力スライスがあります (**00**:00:00 -> **05**:00:00 間)。

	入力データセットの **frequency** と **interval** はそれぞれ **Hour** と **1** に設定されています。つまり、1 時間に 1 つの入力スライスを利用できます。このサンプルでは、intputfolder の同じファイル (file.txt) です。

	次に各スライスの開始時刻を示します。これは上記の JSON スニペットの SliceStart システム変数で表されます。

	
3.	ツール バーの **[デプロイ]** をクリックし、**InputDataset** を作成してデプロイします。エディターのタイトル バーに **"テーブルが正常に作成されました"** というメッセージが表示されていることを確認します。


#### 出力データセットの作成

1. **Data Factory エディター**で、**[新しいデータセット]** をクリックし、コマンド バーの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON スクリプトを、次の JSON スクリプトに置き換えます。

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
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

 	出力場所は **adftutorial/customactivityoutput/** で、出力ファイル名は yyyy-MM-dd-HH.txt です。この yyyy-MM-dd-HH は、スライスが生成された年、月、日、時間です。詳細については、[開発者用リファレンス][adf-developer-reference]のページをご覧ください。

	各入力スライスの出力 BLOB/ファイルが生成されます。次に、各スライスの出力ファイルの命名方法を示します。すべての出力ファイルは、**adftutorial\\customactivityoutput** という 1 つのフォルダーに生成されます。

	| スライス | 開始時刻 | 出力ファイル |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	入力フォルダー内のすべてのファイルは、前述の開始時刻であるスライスの一部です。このスライスを処理すると、カスタム アクティビティは各ファイルをスキャンし、出力ファイルに、検索語句 (“Microsoft”) の出現回数が記述された 1 行を生成します。inputfolder に 3 つのファイルがある場合、出力ファイルには 1 時間ごとのスライス (2015-11-16-00.txt、2015-11-16:01:00:00.txt など) の 3 行が生成されます。


2. コマンド バーの **[デプロイ]** をクリックして **OutputDataset** をデプロイします。


### カスタム アクティビティを使用するパイプラインの作成と実行

1. Data Factory エディターで、コマンド バーの **[新しいパイプライン]** をクリックします。このコマンドが表示されない場合は、**[...]\(省略記号)** をクリックすると表示されます。
2. 右側のウィンドウの JSON を、次の JSON スクリプトに置き換えます。

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
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
		          }
		        ],
		        "LinkedServiceName": "AzureBatchLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "AzureStorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 2,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
		    "isPaused": false
		  }
		}

	以下の点に注意してください。

	- **Concurrency** を **2** に設定し、Azure Batch プール内の 2 つの VM で 2 つのスライスが並列的に処理されるようにします。
	- activities セクションには、**DotNetActivity** 型のアクティビティが 1 つあります。
	- **AssemblyName** を DLL の名前 (**MyActivities.dll**) に設定します。
	- **EntryPoint** を **MyDotNetActivityNS.MyDotNetActivity** に設定します。
	- **PackageLinkedService** は **AzureStorageLinkedService** に設定されます。これは、カスタム アクティビティの zip ファイルを含む Blob Storage を示します。入力/出力ファイルとカスタム アクティビティ zip ファイルに別の Azure Storage アカウントを使用している場合、Azure Storage のリンクされたサービスを別に作成する必要があります。この記事では、同じ Azure Storage アカウントを使用している前提で説明します。
	- **PackageFile** を **customactivitycontainer/MyDotNetActivity.zip** に設定します。形式は containerforthezip/nameofthezip.zip です。
	- カスタム アクティビティは入力として **InputDataset**、出力として **OutputDataset** を使用します。
	- カスタム アクティビティの linkedServiceName プロパティは **HDInsightLinkedService** を示します。これによって、Azure HDInsight クラスターでカスタム アクティビティが実行する必要がある Azure Data Factory がわかります。
	- **isPaused** プロパティは、既定で **false** に設定されています。この例では、スライスが過去に開始されているので、パイプラインは即時に実行されます。このプロパティを true に設定すると、パイプラインを一時停止できます。また false に設定し直すと再開されます。
	- **start** 時刻と **end** 時刻は **5** 時間離れており、スライスは毎時生成されるので、パイプラインによって 5 個のスライスが生成されます。

4. コマンド バーの **[デプロイ]** をクリックして、パイプラインをデプロイします。

### パイプラインの監視
 
8. Azure ポータルの [Data Factory] ブレードで、**[ダイアグラム]** をクリックします。
	
	![[ダイアグラム] タイル](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. [ダイアグラム] ビューで、OutputDataset をクリックします。
 
	![Diagram view](./media/data-factory-use-custom-activities/diagram.png)

10. スライスが生成されると、準備完了状態の出力スライスが 5 個表示されます。

	![出力スライス](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. **adftutorial** コンテナー内の BLOB ストレージに出力ファイルが生成されていることを確認します。

	![カスタム アクティビティの出力][image-data-factory-ouput-from-custom-activity]

9. 出力ファイルを開くと、以下のような出力結果が表示されるはずです。

	ファイル inputfolder/2015-11-16-00/file.txt には、検索語句 "Microsoft" が 2 回出現しました。

10.	[Azure ポータル][azure-preview-portal]または Azure PowerShell コマンドレットを使用して、データ ファクトリ、パイプライン、データセットを監視します。カスタム アクティビティのコード内の **ActivityLogger** からのメッセージは、ポータルからダウンロードできるログ (user-0.log など) で確認することも、コマンドレットを使用して確認することもできます。

	![カスタム アクティビティのダウンロード ログ][image-data-factory-download-logs-from-custom-activity]


データセットやパイプラインを監視するための詳細な手順については、「[パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」を参照してください。

### Data Factory と Batch の統合
Data Factory サービスによって、Azure Batch に **adf-poolname:job-xxx** という名前のジョブが作成されます。

![Azure Data Factory - Batch ジョブ](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

スライスのアクティビティの実行ごとに、1 つのタスクが作成されます。10 個のスライスを処理する準備ができた場合、このジョブに 10 個のタスクが作成されています。プール内に複数のコンピューティング ノードがある場合、複数のスライスを並列して実行することができます。コンピューティング ノードごとの最大タスクが 1 より大きな値に設定されている場合、同じコンピューティング ノードで複数のスライスを実行することもできます。

![Azure Data Factory - Batch ジョブのタスク](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

次の図は、Azure Data Factory と Batch のタスク間の関係を示しています。

![Data Factory と Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)


## パイプラインのデバッグ
デバッグには、いくつかの基本的な技術があります。

1.	以下のエラー メッセージが表示された場合は、CS ファイル内のクラスの名前が、パイプラインの JSON の EntryPoint プロパティに指定した名前と一致していることを確認してください。前出のチュートリアルにおけるクラスの名前は MyDotNetActivity で、EntryPoint は MyDotNetActivityNS.**MyDotNetActivity** として指定されています。

			MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly  
2.	入力スライスが **[準備完了]** に設定されていない場合、入力フォルダー構造が正しく、入力フォルダーに **file.txt** が存在することを確認します。
2.	問題の解決に役立つ情報をログに記録するには、カスタム アクティビティの **Execute** メソッドで、**IActivityLogger** オブジェクトを使用します。ログに記録されたメッセージがユーザー ログ ファイル (user-0.log、user-1.log、user-2.log というぐあいに名前が付けられた 1 つ以上のファイル) に表示されます。

	**[OutputDataset]** ブレードで、スライスをクリックすると、そのスライスの **[データ スライス]** ブレードが表示されます。そのスライスの**アクティビティの実行**が表示されます。このスライスには 1 回のアクティビティの実行が表示されます。コマンド バーの [実行] をクリックする場合、同じスライスの別のアクティビティの実行を開始できます。

	アクティビティの実行をクリックすると、**[アクティビティの実行の詳細]** ブレードにログ ファイルの一覧が表示されます。user\_0.log ファイルにログに記録されたメッセージが表示されます。エラーが発生すると、パイプライン/アクティビティ JSON の再試行回数が 3 に設定されるので、3 つのアクティビティの実行が表示されます。アクティビティの実行をクリックすると、ログ ファイルが表示されます。このファイルを確認すると、エラーを解決することができます。

	ログ ファイルの一覧の **user-0.log** をクリックします。右側のパネルには、**IActivityLogger.Write** メソッドの使用結果が表示されます。一部のメッセージが表示されない場合は、user_1.log、user_2.log のような名前のログ ファイルがほかにもないか確認します。それ以外の場合、コードは、最後にログにメッセージが記録された後に、失敗した可能性があります。

	また、**system-0.log** では、システム エラー メッセージと例外を確認することもできます。

3.	zip ファイルに **PDB** を含めると、エラーの詳細にエラー発生時の**コール スタック**などの情報も記録されます。
4.	カスタム アクティビティの zip ファイル内のファイルは、いずれもサブフォルダーがない**最上位レベル**に置く必要があります。
5.	**assemblyName** (MyDotNetActivity.dll)、**entryPoint**(MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip)、**packageLinkedService** (zip ファイルを含む Azure Blob Storage を示す必要があります) が正しい値に設定されていることを確認します。
6.	エラーを修正し、スライスを再処理する場合は、**[OutputDataset]** ブレードのスライスを右クリックし、**[実行]** をクリックします。
7.	このカスタム アクティビティでは、パッケージ内の **app.config** ファイルは使用されません。そのため、この構成ファイルから接続文字列を読み取るようにコードを記述した場合、実行時にそのコードは機能しません。Azure Batch を使用するときは、すべてのシークレットを **Azure KeyVault** に格納し、証明書ベースのサービス プリンシパルを使用してその **Key Vault** を保護したうえで、Azure Batch プールに証明書を配布することをお勧めします。こうすることで .NET カスタム アクティビティが実行時に KeyVault 内のシークレットにアクセスすることができます。これは一般的な手法であり、接続文字列に限らず、あらゆる種類のシークレットに応用できます。

	最善の方法ではありませんが、同じことをもっと簡単に行うこともできます。接続文字列設定を使用して **Azure SQL のリンクされたサービス**を新しく作成し、そのリンクされたサービスを使用するデータセットを作成して、カスタム .NET アクティビティにダミーの入力データセットとして連結します。リンクされたサービスの接続文字列にカスタム アクティビティのコード内からアクセスすれば、実行時に適切に機能します。



## カスタム アクティビティの更新
カスタム アクティビティのコードを更新する場合は、カスタム アクティビティを構築し、新しいバイナリを含む zip ファイルを BLOB ストレージにアップロードします。

## データのコピーと移動 
コピー アクティビティは、**ソース** データ ストアから**シンク** データ ストアにデータをコピーします。コピー アクティビティのソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストア](data-factory-data-movement-activities.md#supported-data-stores)」を参照してください。

**コピー アクティビティ**でサポートされていないデータ ストアとの間でデータを移動する必要がある場合は、データのコピーと移動に独自のロジックを使用した、Data Factory の**カスタム アクティビティ**を使用できます。GitHub の [HTTP データ ダウンローダー サンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample)を参照してください。

## Appdomain の分離 
Azure Data Factory ランチャーによって使用されるアセンブリのバージョン (WindowsAzure.Storage v4.3.0、Newtonsoft.Json v6.0.x など) に制限されない Azure Data Factory 用のカスタム .NET アクティビティを作成する方法については、[クロス AppDomain のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample)を参照してください。

## 拡張プロパティへのアクセス
次に示すようにアクティビティ JSON の拡張プロパティを宣言できます。

	"typeProperties": {
	  "AssemblyName": "MyDotNetActivity.dll",
	  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	  "PackageLinkedService": "AzureStorageLinkedService",
	  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	  "extendedProperties": {
	    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
		"DataFactoryName": "CustomActivityFactory"
	  }
	},

上記の例では、2 つの拡張プロパティ (**SliceStart** と **DataFactoryName**) があります。SliceStart の値は、SliceStart システム変数に基づいています。サポートされているシステム変数の一覧については、[システム変数](data-factory-scheduling-and-execution.md#data-factory-system-variables)に関するページをご覧ください。DataFactoryName の値は、"CustomActivityFactory" にハードコーディングされます。

**Execute** メソッドでこれらの拡張プロパティにアクセスするには、次のようなコードを使用します。

	// to get extended properties (for example: SliceStart)
	DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
	string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

	// to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
    	logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
	}

## Azure Batch の自動スケール機能
**自動スケール**機能で、Azure Batch プールを作成することもできます。たとえば、専用 VM 数が 0 の Azure Batch プールと、保留中のタスクの数に基づく自動スケールの数式を作成できます。

保留中のタスクにつき一度に 1 つの VM (例: 保留中のタスクが 5 つ -> 5 つの VM):

	pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
	$TargetDedicated = max(pendingTaskSampleVector);

保留中のタスクの数に関係なく、最大で一度に 1 つの VM:

	pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
	$TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;

詳細については、「[Azure Batch プール内のコンピューティング ノードの自動スケール](../batch/batch-automatic-scaling.md)」をご覧ください。

プールで既定の [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx) を使用する場合、Batch サービスがカスタム アクティビティを実行する前に VM を準備するのに 15 ～ 30 分かかることがあります。プールが異なる 　autoScaleEvaluationInterval を使用する場合、Batch サービスは autoScaleEvaluationInterval + 10 分を要することがあります。

## Azure HDInsight のリンクされたサービスの使用
このチュートリアルでは、Azure Batch コンピューティングを使用して、カスタム アクティビティを実行しました。独自の HDInsight クラスターを使用するか、Data Factory によってオンデマンド HDInsight クラスターを作成してから、HDInsight クラスターでカスタム アクティビティを実行することもできます。ここでは、HDInsight クラスターを使用するための手順の概要を示します。

1. Azure HDInsight のリンクされたサービスを作成します。
2. パイプライン JSON 内の **AzureBatchLinkedService** の代わりに HDInsight のリンクされたサービスを使用します。

Azure HDInsight サービスを使用してシナリオをテストできるように、パイプラインの**開始**時刻と**終了**時刻を変更できます。

#### Azure HDInsight のリンクされたサービスを作成する 
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。

> [AZURE.NOTE] 実行時に、.NET アクティビティのインスタンスは HDInsight クラスターの 1 つの worker ノードでのみ実行されます。複数のノードで実行されるように拡張することはできません。.NET アクティビティの複数のインスタンスは、HDInsight クラスターの別々のノードで並列に実行できます。

##### オンデマンド HDInsight クラスターを使用するには

1. **Azure ポータル**の Data Factory のホーム ページで、**[作成とデプロイ]** をクリックします。
2. Data Factory エディターで、コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[オンデマンド HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。
	1. **clusterSize** プロパティには、HDInsight クラスターのサイズを指定します。
	3. **timeToLive** プロパティには、顧客が削除されるまでの間にアイドル状態でいられる時間を指定します。
	4. **version** プロパティには、使用する HDInsight のバージョンを指定します。このプロパティを除外した場合は、最新バージョンが使用されます。
	5. **linkedServiceName** には、入門チュートリアルで作成した **AzureStorageLinkedService** を指定します。

			{
			    "name": "HDInsightOnDemandLinkedService",
			    "properties": {
			        "type": "HDInsightOnDemand",
			        "typeProperties": {
			            "clusterSize": 4,
			            "timeToLive": "00:05:00",
			            "osType": "Windows",
			            "linkedServiceName": "AzureStorageLinkedService",
			        }
			    }
			}

2. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

##### 独自の HDInsight クラスターを使用するには

1. **Azure ポータル**の Data Factory のホーム ページで、**[作成とデプロイ]** をクリックします。
2. **Data Factory エディター**で、コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。
	1. **clusterUri** プロパティには、HDInsight の URL を入力します。例: https://<clustername>.azurehdinsight.net/
	2. **UserName** プロパティには、HDInsight クラスターにアクセスできるユーザー名を入力します。
	3. **Password** プロパティには、ユーザーのパスワードを入力します。
	4. **LinkedServiceName** プロパティには、「**AzureStorageLinkedService**」と入力します。これは、入門チュートリアルで作成したリンク サービスです。

2. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

詳細については、「[コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md)」を参照してください。

**パイプライン JSON** で、HDInsight (オンデマンドまたは独自) のリンクされたサービスを使用します。

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
	            "Name": "InputDataset"
	          }
	        ],
	        "Outputs": [
	          {
	            "Name": "OutputDataset"
	          }
	        ],
	        "LinkedServiceName": "HDInsightOnDemandLinkedService",
	        "typeProperties": {
	          "AssemblyName": "MyDotNetActivity.dll",
	          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	          "PackageLinkedService": "AzureStorageLinkedService",
	          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	          "extendedProperties": {
	            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
	          }
	        },
	        "Policy": {
	          "Concurrency": 2,
	          "ExecutionPriorityOrder": "OldestFirst",
	          "Retry": 3,
	          "Timeout": "00:30:00",
	          "Delay": "00:00:00"
	        }
	      }
	    ],
		"start": "2015-11-16T00:00:00Z",
		"end": "2015-11-16T05:00:00Z",
	    "isPaused": false
	  }
	}

## 例

サンプル | カスタム アクティビティの動作 
------ | ----------- 
[HTTP データ ダウンローダー](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | Data Factory のカスタム C# アクティビティを使用して、HTTP エンドポイントから Azure Blob Storage にデータをダウンロードします。
[Twitter センチメント分析のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) | Azure ML モデルを呼び出し、センチメント分析、スコア付け、予測などを行います。
[R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) | 既に R がインストールされている HDInsight クラスターで RScript.exe を実行し、R スクリプトを呼び出します。 
[クロス AppDomain .NET アクティビティ](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Data Factory 起動ツールによって使用されているアセンブリ バージョンとは別のバージョンを使用します。  
 

## 関連項目

[Azure Data Factory の更新: Azure Batch を使用した ADF カスタム .NET アクティビティの実行](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
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

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

<!---HONumber=AcomDC_0803_2016-->