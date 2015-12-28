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
	ms.date="12/15/2015"
	ms.author="spelluru"/>

# Azure Data Factory パイプラインでカスタム アクティビティを使用する
Azure Data Factory は、パイプラインで使用してデータの移動や処理を行う、**コピー アクティビティ**や **HDInsight アクティビティ**などの組み込みアクティビティをサポートしています。独自の変換/処理ロジックで .NET カスタム アクティビティを作成し、そのアクティビティをパイプラインで使用することもできます。**Azure HDInsight** クラスターまたは **Azure Batch** サービスを使用して実行するようにアクティビティを構成できます

この記事では、カスタム アクティビティを作成し、Azure Data Factory のパイプラインで使用する方法について説明します。カスタム アクティビティを作成して使用するための詳細なチュートリアルも提供します。チュートリアルでは、HDInsight リンク サービスを使用します。代わりに Azure Batch リンク サービスを使用するには、**AzureBatch** 型のリンク サービスを作成し、パイプライン JSON (**linkedServiceName**) のアクティビティ セクションで使用します。カスタム アクティビティでの Azure Batch の使用方法の詳細については、[Azure Batch リンク サービス](#AzureBatch)に関するセクションをご覧ください。


## <a name="walkthrough" />チュートリアル
このチュートリアルでは、カスタム アクティビティを作成し、そのアクティビティを Azure Data Factory パイプラインで使用する手順について説明します。このチュートリアルは、「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルをさらに進めたものです。カスタム アクティビティの動作について確認したい場合は、まず「Azure Data Factory を使ってみる」のチュートリアルを終えてから、このチュートリアルを始めてください。

### 前提条件


- 「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアル。本チュートリアルを実行する前に、この記事のチュートリアルを完了しておく必要があります。
- Visual Studio 2012 または 2013
- [Azure .NET SDK][azure-developer-center] をダウンロードし、インストールします。
- 最新の [Azure Data Factory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/)をダウンロードし、インストールします。手順はこのチュートリアルにあります。
- Azure Storage の NuGet パッケージをダウンロードしてインストールします。チュートリアルで方法を説明しているため、この手順は省略できます。

### 手順の概要 
1.	Data Factory ソリューションで使用する**カスタム アクティビティを作成**します。このカスタム アクティビティには、データ処理ロジックが含まれています。 
	1.	Visual Studio (または任意のコード エディター) で .NET Class Library プロジェクトを作成し、入力データを処理するコードを追加してプロジェクトをコンパイルします。	
	2.	出力フォルダーにあるすべてのバイナリ ファイルと PDB (省略可能) ファイルを zip 形式で圧縮します。	
	3.	zip ファイルを Azure Blob Storage にアップロードします。詳細な手順については、「カスタム アクティビティの作成」セクションを参照してください。 
2. **カスタム アクティビティを使用する Azure Data Factory を作成する**:
	1. Azure Data Factory を作成します。
	2. リンクされたサービスを作成します。
		1. StorageLinkedService: BLOB にアクセスするためのストレージの資格情報を指定します。
		2. HDInsightLinkedService: コンピューティングとして Azure HDInsight を指定します。
	3. データセットを作成します。
		1. InputDataset: 入力 BLOB のストレージ コンテナーとフォルダーを指定します。
		1. OuputDataset: 出力 BLOB のストレージ コンテナーとフォルダーを指定します。
	2. カスタム アクティビティを使用するパイプラインを作成します。
	3. パラメーターを実行してテストします。
	4. パイプラインをデバッグします。

## カスタム アクティビティの作成
Azure Data Factory パイプラインで使用できる .NET カスタム アクティビティを作成するには、その **IDotNetActivity** インターフェイスを実装したクラスを含む **.NET Class Library** プロジェクトを作成する必要があります。このインターフェイスには、Execute という 1 つのメソッドのみが含まれます。このメソッドのシグネチャを次に示します。

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
このメソッドには、理解しておく必要がある重要な要素がいくつかあります。

- このメソッドには次の 4 つのパラメーターがあります。
	- **linkedServices**。入力/出力データ ソースを Data Factory にリンクする、リンクされたサービスの列挙可能な一覧です。このサンプルでは、入力と出力の両方に使用される Azure Storage 型のリンクされたサービスは 1 つのみです。 
	- **datasets**。データセットの列挙可能な一覧です。このパラメーターを使用すると、入力と出力のデータセットに定義されている場所とスキーマを取得できます。
	- **activity**。このパラメーターは、現在のコンピューティング エンティティ (この場合は Azure HDInsight) を表します。
	- **logger**。logger を使用すると、パイプラインの “User” ログとして表示されるデバッグ コメントが出力されます。 

- メソッドから、カスタム アクティビティの連結に使用できるディクショナリが返されます。このサンプル ソリューションでは、この機能を使用しません。

### 手順: 
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


	次のサンプル コードでは、入力 BLOB 内の行数をカウントし、BLOB へのパス、BLOB 内の行数、アクティビティが実行されたコンピューター、現在の日時を出力 BLOB に生成します。

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

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
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
       			// with the data slice. 
        		// 
        	    // definition of the method is shown in the next step.
 
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

            // return a new Dictionary object (unused in this code).
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

	GetFolderPath メソッドは、データセットが指すフォルダーのパスを返し、GetFileName メソッドは、データセットが指す BLOB/ファイルの名前を返します。
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	Calculate メソッドは、入力ファイル (フォルダー内の BLOB) 内のキーワード Microsoft のインスタンス数を計算します。検索語句 ("Microsoft") は、コードにハード コーディングされています。

10. プロジェクトをコンパイルします。メニューの **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。
11. **Windows エクスプローラー**を起動し、ビルドの種類に応じて、**bin\\debug** フォルダーまたは **bin\\release** フォルダーに移動します。
12. <project folder>\\bin\\Debug フォルダー内のすべてのバイナリを含む zip ファイル、**MyDotNetActivity.zip** を作成します。エラー発生時の問題の原因となったソース コードの行番号など、追加情報を取得するために、**MyDotNetActivity.pdb** ファイルを含めることもできます。

	![バイナリ出力ファイル](./media/data-factory-use-custom-activities/Binaries.png)
13. **MyDotNetActivity.zip** を BLOB として **customactvitycontainer** にアップロードします。この BLOB コンテナーは、**ADFTutorialDataFactory** 内の **StorageLinkedService** リンク サービスが使用する Azure BLOB ストレージ内にあります。BLOB コンテナー **customactivitycontainer** が既に存在していなければ、作成します。

> [AZURE.NOTE]この .NET アクティビティ プロジェクトを Visual Studio で Data Factory プロジェクトを含むソリューションに追加する場合は、最後の 2 つの手順 (zip ファイルの作成と Azure BLOB ストレージへの手動でのアップロード) を実行する必要はありません。Data Factory エンティティを Visual Studio を使用して発行すると、これらの手順は発行プロセスによって自動的に実行されます。Visual Studio を使用した Data Factory エンティティの作成と発行の詳細については、記事「[Visual Studio を使用した初めてのパイプラインの作成](data-factory-build-your-first-pipeline-using-vs.md)」と記事「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](data-factory-get-started-using-vs.md)」を参照してください。

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

5.	コードで、ファイル名 (BLOB 名) を取得する **GetFileName** を呼び出します。このコードは、フォルダー パスを取得する上記のコードと似ています。

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	新しい URI オブジェクトを作成してファイル名が出力されます。URI コンストラクターは **BlobEndpoint** プロパティを使用してコンテナー名を返します。フォルダー パスとファイル名が追加され、出力 BLOB URI が構築されます。

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	ファイル名が出力されるので、Calculate メソッドから出力文字列を新しい BLOB に出力できるようになります。

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## Data Factory の作成

「**カスタム アクティビティの作成**」セクションでは、カスタム アクティビティを作成し、バイナリと PDB ファイルを含む zip ファイルを Azure Blob コンテナーにアップロードしました。ここでは、**カスタム アクティビティ**を使用した**パイプライン**で Azure **Data Factory** を作成します。
 
カスタム アクティビティの入力データセットは、Blob Storage の入力フォルダー (mycontainer\\inputfolder) 内にある BLOB (ファイル) を表します。アクティビティの出力データセットは、Blob Storage の出力フォルダー (mycontainer\\outputfolder) 内にある出力 BLOB を表します。

次の内容の file.txt というファイルを作成し、mycontainer\\inputfolder にアップロードします (mycontainer は Azure Blob コンテナーの名前、inputfolder はそのコンテナー内のフォルダー名です)。

	test custom activity Microsoft test custom activity Microsoft

入力フォルダーに複数のファイルが含まれている場合でも、入力フォルダーは Azure Data Factory のスライスに相当します。パイプラインで各スライスが処理されるときに、カスタム アクティビティでは、そのスライスの入力フォルダー内にあるすべての BLOB が反復処理されます。

mycontainer\\output フォルダーには、1 行または複数行 (入力フォルダーの BLOB 数と同じ数) が含まれる 1 つの出力ファイルが作成されます。
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


このセクションで実行する手順を次に示します。

1. **Data Factory** を作成します。
2. HDInsight クラスターの**リンクされたサービス**を作成します。HDInsight クラスター上で、カスタム アクティビティは、マップ専用のジョブと、入力/出力 BLOB を保持する Azure Storage として動作します。 
2. カスタム アクティビティの入力と出力を示す入力および出力の**データセット**を作成します。 
3. カスタム アクティビティを使用する**パイプライン**を作成して実行します。

### 手順 1: Data Factory を作成する

1.	Azure ポータルにログインした後、次の操作を行います。
	1.	左側のメニューの **[新規]** をクリックします。
	2.	**[新規]** ブレードの **[データ + 分析]** をクリックします。
	3.	**[データ分析]** ブレードの **[Data Factory]** をクリックします。
2.	**[新しい Data Factory]** ブレードで、[名前] フィールドに「**CustomActivityFactory**」と入力します。Azure Data Factory の名前はグローバルに一意にする必要があります。**""CustomActivityFactory" という名前の Data Factory は使用できません"** というエラーが発生した場合は、データ ファクトリの名前を変更して (**yournameCustomActivityFactory** など) 作成し直してください。
3.	**[リソース グループ名]** をクリックし、既存のリソース グループを選択するか、新しいリソース グループを作成します。 
4.	作成する Data Factory に適した**サブスクリプション**と**リージョン**を使用していることを確認します。 
5.	**[新しい Data Factory]** ブレードで **[作成]** をクリックします。
6.	作成した Data Factory は、Azure ポータルの**ダッシュボード**に表示されます。
7.	Data Factory が正常に作成されると、Data Factory の内容を表示する [Data Factory] ブレードが表示されます。

### 手順 2. リンク サービスを作成する

リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。この手順では、Azure Storage アカウントと Azure HDInsight クラスターを Data Factory にリンクします。

#### Azure Storage のリンクされたサービスを作成する

1.	**[CustomActivityFactory]** の **[Data Factory]** ブレードで、**[作成およびデプロイ]** をクリックします。Data Factory エディタが起動します。
2.	コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure Storage]** を選択します。Azure Storage のリンクされたサービスを作成するための JSON スクリプトがエディターに表示されます。
3.	**accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。ストレージ アクセス キーを取得する方法については、「[ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。
4.	コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。


#### Azure HDInsight のリンクされたサービスを作成する 
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。また、独自のクラスターを使って同じ処理を行うことも可能です。オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。

> [AZURE.NOTE]実行時に、.NET アクティビティのインスタンスは HDInsight クラスターの 1 つの worker ノードでのみ実行されます。複数のノードで実行されるように拡張することはできません。.NET アクティビティの複数のインスタンスは、HDInsight クラスターの別々のノードで並列に実行できます。

「[Azure Data Factory を使ってみる][adfgetstarted]」のチュートリアルに加えて「[Data Factory で Pig と Hive を使用する][hivewalkthrough]」のチュートリアルも終えている場合は、このリンク サービスの作成を省略し、既に ADFTutorialDataFactory 内にあるリンク サービスを使用できます。


##### オンデマンド HDInsight クラスターを使用するには

1. **Azure ポータル**の Data Factory のホーム ページで、**[作成とデプロイ]** をクリックします。
2. Data Factory エディターで、コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[オンデマンド HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterSize** プロパティには、HDInsight クラスターのサイズを指定します。
	3. **timeToLive** プロパティには、顧客が削除されるまでの間にアイドル状態でいられる時間を指定します。 
	4. **version** プロパティには、使用する HDInsight のバージョンを指定します。このプロパティを除外した場合は、最新バージョンが使用されます。  
	5. **linkedServiceName** には、入門チュートリアルで作成した **StorageLinkedService** を指定します。 

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. コマンド バーの **[デプロイ]** をクリックして、リンクされたサービスをデプロイします。

##### 独自の HDInsight クラスターを使用するには

1. **Azure ポータル**の Data Factory のホーム ページで、**[作成とデプロイ]** をクリックします。
2. **Data Factory エディター**で、コマンド バーの **[新しいコンピューティング]** をクリックし、メニューから **[HDInsight クラスター]** を選択します。
2. JSON スクリプトで、以下の手順を実行します。 
	1. **clusterUri** プロパティには、HDInsight の URL を入力します。たとえば、https://<clustername>.azurehdinsight.net/ などです。     
	2. **UserName** プロパティには、HDInsight クラスターにアクセスできるユーザー名を入力します。
	3. **Password** プロパティには、ユーザーのパスワードを入力します。 
	4. **LinkedServiceName** プロパティには、「**StorageLinkedService**」と入力します。これは、入門チュートリアルで作成したリンク サービスです。 

2. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

### 手順 3: データセットを作成する
この手順では、入力データと出力データを示すデータセットを作成します。

#### 入力データセットの作成
1.	Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューから **[Azure BLOB ストレージ]** をクリックします。
2.	右側のウィンドウの JSON を、次の JSON スニペットに置き換えます。

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "StorageLinkedService",
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


#### 出力テーブルの作成

1. **Data Factory エディター**で、**[新しいデータセット]** をクリックし、コマンド バーの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON スクリプトを、次の JSON スクリプトに置き換えます。

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
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

 	出力場所は **adftutorial/customactivityoutput/YYYYMMDDHH/** です。YYYYMMDDHH は、スライスが生成された年 (Year)、月 (Month)、日 (Day)、時間 (Hour) です。詳細については、[開発者用リファレンス][adf-developer-reference]のページをご覧ください。

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
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
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
	- **PackageLinkedService** は **StorageLinkedService** に設定されます。これは、カスタム アクティビティの zip ファイルを含む Blob Storage を示します。入力/出力ファイルとカスタム アクティビティ zip ファイルに別の Azure Storage アカウントを使用している場合、Azure Storage のリンクされたサービスを別に作成する必要があります。この記事では、同じ Azure Storage アカウントを使用している前提で説明します。
	- **PackageFile** を **customactivitycontainer/MyDotNetActivity.zip** に設定します。形式は <containerforthezip>/<nameofthezip.zip> です。
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

### パイプラインのデバッグ
デバッグには、いくつかの基本的な技術があります。

1.	入力スライスが **[準備完了]** に設定されていない場合、入力フォルダー構造が正しく、入力フォルダーに file.txt が存在することを確認します。
2.	問題の解決に役立つ情報をログに記録するには、カスタム アクティビティの **Execute** メソッドで、**IActivityLogger** オブジェクトを使用します。ログに記録されたメッセージは user\_0.log ファイルに表示されます。 

	**[OutputDataset]** ブレードで、スライスをクリックすると、そのスライスの **[データ スライス]** ブレードが表示されます。そのスライスの**アクティビティの実行**が表示されます。このスライスには 1 回のアクティビティの実行が表示されます。コマンド バーの [実行] をクリックする場合、同じスライスの別のアクティビティの実行を開始できます。

	アクティビティの実行をクリックすると、**[アクティビティの実行の詳細]** ブレードにログ ファイルの一覧が表示されます。user\_0.log ファイルにログに記録されたメッセージが表示されます。エラーが発生すると、パイプライン/アクティビティ JSON の再試行回数が 3 に設定されるので、3 つのアクティビティの実行が表示されます。アクティビティの実行をクリックすると、ログ ファイルが表示されます。このファイルを確認すると、エラーを解決することができます。

	ログ ファイルの一覧の **user-0.log** をクリックします。右側のパネルには、**IActivityLogger.Write** メソッドの使用結果が表示されます。

	また、**system-0.log** では、システム エラー メッセージと例外を確認することもできます。

3.	zip ファイルに **PDB** を含めると、エラーの詳細にエラー発生時の**コール スタック**などの情報も記録されます。
4.	カスタム アクティビティの zip ファイルのすべてのファイルは、サブフォルダーがない**最上位レベル**に置く必要があります。
5.	**assemblyName** (MyDotNetActivity.dll)、**entryPoint**(MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip)、**packageLinkedService** (zip ファイルを含む Azure Blob Storage を示す必要があります) が正しい値に設定されていることを確認します。 
6.	エラーを修正し、スライスを再処理する場合は、**[OutputDataset]** ブレードのスライスを右クリックし、**[実行]** をクリックします。 


## カスタム アクティビティの更新
カスタム アクティビティのコードを更新する場合は、カスタム アクティビティを構築し、新しいバイナリを含む zip ファイルを BLOB ストレージにアップロードします。

## <a name="AzureBatch"></a>Azure Batch のリンクされたサービスの使用
> [AZURE.NOTE]Azure Batch サービスの概要については、「[Azure Batch の技術概要][batch-technical-overview]」をご覧ください。Azure Batch サービスの使用をスムーズに開始するには、「[.NET 向け Azure Batch ライブライの概要][batch-get-started]」を参照してください。

コンピューティング リソースとして Azure Batch を使用するカスタム .NET アクティビティを実行できます。独自の Azure Batch のプールを作成し、他の構成と併せて VM の数を指定する必要があります。Azure Batch のプールは、顧客に以下の機能を提供します。

1. 作成するプールのコアは 1 個から数千個まで対応
2. 式に基づいて VM 数を自動スケール
3. あらゆるサイズの VM をサポート
4. VM あたりのタスク数を構成可能
5. 無制限の数のタスクをキューに配置


ここでは、前のセクションのチュートリアルで説明した、Azure Batch リンク サービスを使用するための手順の概要を示します。

1. [Azure ポータル](http://manage.windowsazure.com)を使用して、Azure Batch アカウントを作成します。手順については、「[Azure Batch アカウントの作成と管理][batch-create-account]」の記事を参照してください。Azure Batch のアカウント名とアカウント キーをメモしておきます。

	[New-AzureBatchAccount][new-azure-batch-account] コマンドレットを使用して、Azure Batch アカウントを作成することもできます。このコマンドレットの使用方法の詳細については、「[Using Azure PowerShell to Manage Azure Batch Account (Azure PowerShell を使用した Azure Batch アカウントの管理)][azure-batch-blog]」をご覧ください。 
2. Azure Batch プールの作成[Azure Batch Explorer ツール][batch-explorer]のソース コードをダウンロードし、コンパイルして使用することもできますが、[.NET 向け Azure Batch ライブラリ][batch-net-library]を使用して Azure Batch プールを作成することもできます。Azure Batch Explorer の使用手順については、[Azure Batch Explorer サンプル チュートリアル][batch-explorer-walkthrough]のページを参照してください。

	[New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) コマンドレットを使用して、Azure Batch プールを作成することもできます。

	また、複数のコンピューティング ノードを含む Azure Batch プールを作成して、それらのスライスが並列して処理されるようにすることもできます。Batch Explorer を使用している場合:

	- プールの ID を入力します (**プール ID**)。**プールの ID** は、Data Factory ソリューションを作成するときに必要になります。 
	- オペレーティング システム ファミリ設定には、**Windows Server 2012 R2** を指定します。
	- **コンピューティング ノードごとの最大タスク**の設定値には、**2** を指定します。
	- **ターゲットの専用数**の設定値には **2** を指定します。 

	Data Factory サービスによって、Azure Batch に adf-<pool name>:job-xxx という名前のジョブが作成されます。スライスのアクティビティの実行ごとに、1 つのタスクが作成されます。10 個のスライスを処理する準備ができた場合、このジョブに 10 個のタスクが作成されています。プール内に複数のコンピューティング ノードがある場合、複数のスライスを並列して実行することができます。コンピューティング ノードごとの最大タスクが 1 より大きな値に設定されている場合、同じコンピューティング ノードで複数のスライスを実行することもできます。
	
	![Batch Explorer のタスク](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

	![Data Factory と Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

2. 次の JSON テンプレートを使用して、Azure Batch リンク サービスを作成します。Data Factory エディターには、最初に同様のテンプレートが表示されます。JSON スニペットで、Azure Batch のアカウント名、アカウント キー、プール名を指定します。

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT]**[Azure Batch アカウント] ブレード**の **URL** は、accountname.region.batch.azure.com です。JSON の **batchUri** プロパティでは、この URL から **"accountname" を削除**し、**accountName** JSON プロパティの**accountname** を必要があります。
	  
	**poolName** プロパティでは、プール名の代わりにプール ID を指定することもできます。

	これらのプロパティについては、[Azure Batch リンク サービスに関する MSDN のトピック](https://msdn.microsoft.com/library/mt163609.aspx)をご覧ください。

2.  Data Factory エディターで、チュートリアルで作成したパイプラインの JSON 定義を開き、**HDInsightLinkedService** を **AzureBatchLinkedService** に置き換えます。
3.  Azure Batch サービスを使用してシナリオをテストできるように、パイプラインの開始時刻と終了時刻を変更できます。 
4.  次の図に示すように、Azure Batch Explorer でスライスの処理に関連する Azure Batch のタスクを確認できます。

	![Azure Batch のタスク][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]Data Factory サービスでは、HDInsight の場合と同様、Azure Batch のオンデマンド オプションはサポートされません。Azure データ ファクトリでは、独自の Azure Batch プールのみ使用できます。

## 関連項目

[Azure Data Factory の更新: Azure Batch を使用した ADF カスタム .NET アクティビティの実行](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
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

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=AcomDC_1217_2015-->