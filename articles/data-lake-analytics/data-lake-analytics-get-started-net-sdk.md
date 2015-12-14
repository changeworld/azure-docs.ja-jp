<properties 
   pageTitle="チュートリアル: .NET SDK で Azure Data Lake Analytics の使用を開始する | Azure" 
   description=".NET SDK を使用して、Data Lake Store アカウントを作成し、Data Lake Analytics ジョブを作成してから、U-SQL で記述されたジョブを送信する方法について説明します。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/21/2015"
   ms.author="edmaca"/>

# チュートリアル: .NET SDK で Azure Data Lake Analytics の使用を開始する

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Azure .NET SDK を使用して、Azure Data Lake Analytics アカウントを作成し、[U-SQL](data-lake-analytics-u-sql-get-started.md) で Data Lake Analytics ジョブを定義して、Data Lake Analtyic アカウントにジョブを送信する方法について説明します。Data Lake Analytics の詳細については、「[Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

このチュートリアルでは、U-SQL スクリプトを含む C# コンソール アプリケーションを開発します。このスクリプトでは、タブ区切り値 (TSV) ファイルを読み取り、それをコンマ区切り値 (CSV) ファイルに変換します。サポートされている他のツールを使用する同じチュートリアルを読み進めるには、このセクションの上部にあるタブをクリックします。

**基本的な Data Lake Analytics のプロセス:**

![Azure Data Lake Analytics プロセスのフロー図](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Data Lake Analytics アカウントを作成します。
2. ソース データを準備します。Data Lake Analytic ジョブでは、Azure Data Lake Store アカウントまたは Azure BLOB ストレージ アカウントからデータを読み取ることができます。   
3. U-SQL スクリプトを開発します。
4. ジョブ (U-SQL スクリプト) を Data Lake Analytics アカウントに送信します。ジョブはソース データから読み取り、U-SQL スクリプトで指示されたとおりにデータを処理して、Data Lake Store アカウントまたは BLOB ストレージ アカウントに出力を保存します。

##前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Visual Studio 2015、Visual Studio 2013 update 4、または Visual Studio 2012 (Visual C++ インストール済み)**。
- **Microsoft Azure SDK for .NET バージョン 2.5 以上**。[Web Platform Installer を使用してインストールします](http://www.microsoft.com/web/downloads/platform.aspx)。
- **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**。 
- **Data Lake Analytics アカウント**。[Azure Data Lake Analytics アカウントの作成](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)に関する記述を参照してください。

	Data Lake Tools では、Data Lake Analytics アカウントの作成はサポートされません。そのため、Azure ポータル、Azure PowerShell、.NET SDK、または Azure CLI を使用して作成する必要があります。

##コンソール アプリケーションの作成

このチュートリアルでは、いくつかの検索ログを処理します。検索ログは、Data Lake Store または Azure BLOB ストレージに格納できます。

サンプルの検索ログは、パブリック Azure Blob コンテナーにコピーされました。アプリケーションでは、ファイルをワークステーションにダウンロードしてから、そのファイルを既定の Data Lake Store アカウントにアップロードします。

**アプリケーションを作成するには**

1. Visual Studio を開きます。
2. C# コンソール アプリケーションを作成します。
3. NuGet パッケージ管理コンソールを開き、次のコマンドを実行します。

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsCatalog -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsJob -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreFileSystem -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package WindowsAzure.Storage

4. **SampleUSQLScript.txt** というプロジェクトに新しいファイルを追加してから、以下の U-SQL スクリプトを貼り付けます。Data Lake Analtyics ジョブは U-SQL 言語で記述されます。U-SQL の詳細については、[U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)と [U-SQL 言語リファレンス](http://go.microsoft.com/fwlink/?LinkId=691348)に関する記述を参照してください。

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。
    
    C# プログラムでは、**/Samples/Data/SearchLog.tsv** ファイル、および **/Output/** フォルダーを準備する必要があります。
	
	既定の Data Lake アカウントに格納されたファイルの相対パスを使用する方が簡単です。絶対パスを使用することもできます。たとえば、次のように入力します。
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    リンクされた Storage アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。リンクされた Azure Storage アカウントに格納されているファイルの構文は以下のとおりです。
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]パブリック BLOB またはパブリック コンテナーのアクセス許可を持つ Azure BLOB コンテナーは、現在サポートされていません。
       
       
5. Program.cs では、次のコードを貼り付けます。

        using System;
        using System.Security;
        using System.IO;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Common.Authentication.Factories;
        
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob.Models;
        
        using Microsoft.WindowsAzure.Storage.Blob;
        
        namespace data_lake_analytics_get_started
        {
            class Program
            {
        
                private const string AzureSubscriptionID = "<Your Azure Subscription ID>";
        
                private const string ResourceGroupName = "<Existing Azure Resource Group Name>"; //See the Get started using portal article
                private const string Location = "<Azure Data Center>";  //For example, EAST US 2
                private const string DataLakeStoreAccountName = "<Data Lake Store Account Name>"; // The application will create this account.
                private const string DataLakeAnalyticsAccountName = "<Data Lake Analytics Account Name>"; //The application will create this account.
        
                private const string LocalFolder = @"C:\tutorials\downloads";  //local folder with write permission for file transferring.
        
                private static DataLakeStoreManagementClient _dataLakeStoreClient;
                private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
        
                private static DataLakeAnalyticsManagementClient _dataLakeAnalyticsClient;
                private static DataLakeAnalyticsJobManagementClient _dataLakeAnalyticsJobClient;
        
                static void Main(string[] args)
                {
                    var subscriptionId = new Guid(AzureSubscriptionID);
                    var _credentials = GetAccessToken();
        
                    _credentials = GetCloudCredentials(_credentials, subscriptionId);
                    _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
                    _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
                    _dataLakeAnalyticsClient = new DataLakeAnalyticsManagementClient(_credentials);
                    _dataLakeAnalyticsJobClient = new DataLakeAnalyticsJobManagementClient(_credentials);
        
                    //=========================
                    Console.WriteLine("Creating the Azure Data Lake Store account ...");
                    var storeAccountParameters = new DataLakeStoreAccountCreateOrUpdateParameters();
                    storeAccountParameters.DataLakeStoreAccount = new Microsoft.Azure.Management.DataLake.Store.Models.DataLakeStoreAccount
                    {
                        Name = DataLakeStoreAccountName,
                        Location = Location
                    };
                    _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, storeAccountParameters);
        
                    //=========================
                    Console.WriteLine("Preparing the source data file ...");
        
                    // Download the source file from a public Azure Blob container.
                    CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                    blob.DownloadToFile(LocalFolder + "SearchLog.tsv", System.IO.FileMode.Create);
        
                    // Create a folder in the default Data Lake Store account.
                    _dataLakeStoreFileSystemClient.FileSystem.Mkdirs("/Samples/Data/", DataLakeStoreAccountName, "777");
        
                    // Upload the source file to the default Data Lake Store account
                    var uploadParameters = new UploadParameters(LocalFolder + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv", DataLakeStoreAccountName, isOverwrite: true);
                    var uploader = new DataLakeStoreUploader(uploadParameters, new DataLakeStoreFrontEndAdapter(DataLakeStoreAccountName, _dataLakeStoreFileSystemClient));
                    uploader.Execute();
        
                    //=========================
                    Console.WriteLine("Creating the Data Lake Analytics account ...");
                    var analyticsAccountParameters = new DataLakeAnalyticsAccountCreateOrUpdateParameters();
                    analyticsAccountParameters.DataLakeAnalyticsAccount = new DataLakeAnalyticsAccount
                    {
                        Name = DataLakeAnalyticsAccountName,
                        Location = Location,
                        Properties = new DataLakeAnalyticsAccountProperties()
                    };
        
                    //create a DataLakeStoreAccount object, add it to the analytics client and then set it as the default ADL Store account
                    Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount storeAccountObject = new Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount();
                    storeAccountObject.Name = DataLakeStoreAccountName;
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DataLakeStoreAccounts.Add(storeAccountObject);
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DefaultDataLakeStoreAccount = DataLakeStoreAccountName;
        
                    _dataLakeAnalyticsClient.DataLakeAnalyticsAccount.Create(ResourceGroupName, analyticsAccountParameters);
        
                    //=========================
                    Console.WriteLine("Submitting the job ...");
        
                    USqlProperties uSQLProperties = new USqlProperties
                    {
                        Type = JobType.USql,
                        Script = File.ReadAllText("SampleUSQLScript.txt")
                    };
        
                    JobInformation jobParameters = new JobInformation
                    {
                        JobId = Guid.NewGuid(),
                        Name = "myfirstdatalakeanalyticsjob",
                        Properties = uSQLProperties,
                        Type = JobType.USql,
                        DegreeOfParallelism = 1,
                        Priority = 1
                    };
        
                    _dataLakeAnalyticsJobClient.Jobs.Create(ResourceGroupName, DataLakeAnalyticsAccountName, new JobInfoBuildOrCreateParameters { Job = jobParameters });
        
                    Console.WriteLine(" Downloading results ...");
                    FileCreateOpenAndAppendResponse beginOpenResponse = _dataLakeStoreFileSystemClient.FileSystem.BeginOpen("/Output/SearchLog-from-Data-Lake.csv", DataLakeStoreAccountName, new FileOpenParameters());
                    FileOpenResponse openResponse = _dataLakeStoreFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
                    System.IO.File.WriteAllBytes(LocalFolder + "SearchLog-from-Data-Lake.csv", openResponse.FileContents);
        
                    Console.WriteLine("Done");
                }
        
                public static SubscriptionCloudCredentials GetAccessToken(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    return new TokenCloudCredentials(authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken);
                }
        
                public static SubscriptionCloudCredentials GetCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

7. **F5** キーを押してアプリケーションを実行します。

## 関連項目

- 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
- より複雑なクエリを表示する場合は、「[Azure Data Lake Analytics を使用する Web サイト ログの分析](data-lake-analytics-analyze-weblogs.md)」を参照してください。
- U-SQL アプリケーションの開発を開始する場合は、「[Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。
- U-SQL の詳細については、「[Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
- 管理タスクについては、「[Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
- Data Lake Analytics の概要については、「[Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

<!---HONumber=AcomDC_1203_2015-->