---
title: "Azure .NET SDK を使用して Azure Data Lake Analytics を管理する | Microsoft Docs"
description: "Data Lake Analytics のジョブ、データ ソース、ユーザーの管理方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3ed1b4121e8e64b07abaeb1117f2b8a0cfd75406
ms.openlocfilehash: 7b2380e45c62684ed29fe819db7e254b968d55d0


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Azure .NET SDK を使用して Azure Data Lake Analytics を管理する
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure .NET SDK を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。 他のツールを使用する管理のトピックを表示する場合は、上のタブ セレクターをクリックします。

**前提条件**

このチュートリアルを開始する前に、以下の前提条件を満たしている必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

### <a name="create-an-azure-resource-group"></a>Azure リソース グループを作成する
Azure リソース グループをまだ作成していない場合は、Data Lake Analytics コンポーネントを作成する際に必要になります。 次のコードは、リソース グループの作成方法を示しています。

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

詳細については、「[Azure リソース グループと Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics)」を参照してください。


## <a name="connect-to-azure-data-lake"></a>Azure Data Lake に接続する
次の Nuget パッケージが必要です。

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


次のコード サンプルの Main メソッドでは、Azure に接続し、Analytics アカウントと Store アカウント用の Data Lake クライアント管理オブジェクトを初期化する方法を示します。

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Data Lake クライアント管理オブジェクト
Azure Data Lake SDK には、プログラミングの大部分を行うことができるクライアント管理オブジェクトのセットが含まれます。これらは、次の&2; つの名前空間に属します。
* Mirosoft.Azure.Management.DataLake.Analytics
* Microsot.Azure.Management.DataLake.Store

次の表に、この記事全体で例として使用されるオブジェクトと変数を示します。

| クライアント管理オブジェクト                  | コード変数         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 管理クライアント オブジェクト:
* DataLakeStoreAccountManagementClient - Data Lake Store の作成および管理に使用します。
* DataLakeFileSystemAccountManagementClient - フォルダーとファイルの作成、ファイルのアップロード、ファイルの一覧表示、ACL および資格情報へのアクセス、Azure Storage Blob へのリンクの追加などのファイル システムのタスクに使用します。

Data Lake から Azure Storage へのリンクを作成できますが、そのコンテンツにアクセスすることはできません。 そのためには、Azure Storage SDK API を使用する必要があります。 ただし、Azure Storage Blob で U-SQL スクリプトを実行することができます。

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 管理クライアント オブジェクト:
* DataLakeAnaylyticsAccountManagementClient - Data Lake Analytics アカウントの作成および管理に使用します。
* DataLakeAnalyticsCatalogManagementClient - スキーマの一覧表示など、SQL データベースの構成に使用します。
* DataLakeAnalyticsJobManagementClient - U-SQL ジョブの作成および管理に使用します。

## <a name="create-accounts"></a>アカウントの作成
Data Lake Analytics ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Azure HDInsight とは異なり、ジョブを実行しなければ、Analytics アカウントには課金されません。  ジョブの実行時にのみ課金されます。  詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

また、Data Lake Analytics アカウントには、少なくとも&1; つの Data Lake Store アカウントが必要です。
  
### <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する
次のコードは、Data Lake Store アカウントの作成方法を示しています。 Create メソッドを使用する前に、場所を指定することでパラメーターを定義する必要があります。

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
次のコードは、Data Lake Analytics アカウントの作成方法を示しています。 DataLakeAnalyticsAccountManagementClient オブジェクトの Create メソッドは、そのパラメーターのうちの&1; つについて Data Lake Store アカウントのコレクションを受け取ります。 このコレクションには、DataLakeStoreAccountInfo オブジェクトのインスタンスを入力する必要があります。 この例では、これらの DataLakeStoreAccountInfo オブジェクトはヘルパー メソッド (AdlaFromAdlsStoreAccounts) から取得されます。 さらに、サブスクリプション内のすべての Data Lake Store アカウントが必ずしも単一の Data Lake Analitics アカウント内にある必要はないため、このコードによって、承認されたリストに照らして名前が確認されます。

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>アカウントの管理

### <a name="list-data-lake-store-and-analytic-accounts"></a>Data Lake Store と Data Lake Analytic のアカウントを一覧表示する
次のコードは、サブスクリプション内の Data Lake Store アカウントを一覧表示します。 リスト操作では、オブジェクトのすべてのプロパティが提供されるわけではないため、場合によっては、オブジェクトで Get 操作を実行する必要があります。
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>アカウントを取得する
次のコードは、Data Lake Analytics アカウントが存在する場合に、DataLakeAnalyticsAccountManagementClient を使用してそのアカウントを返します。 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

同様に、同じ方法で DataLakeStoreAccountManagementClient (_adlsClient) を使用して Data Lake Store アカウントを取得することができます。        
### <a name="delete-an-account"></a>アカウントの削除
次のコードは、Data Lake Analytics アカウントが存在する場合にそのアカウントを削除します。 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

DataLakeStoreAccountManagementClient を使用して同じ方法で Data Lake Store アカウントを削除することもできます。

### <a name="get-the-default-data-lake-store-account"></a>既定の Data Lake Store アカウントを取得する
すべての Data Lake Analytics アカウントに、既定の Data Lake Store アカウントが必要です。 次のコードを使用して、Analytics アカウントの既定の Store アカウントを決定使用します。

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>データ ソースを管理する
Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/storage-introduction.md)

Analytics アカウントを作成する際には、既定のストレージ アカウントとして Azure Data Lake ストレージ アカウントを指定する必要があります。 既定の Data Lake Store アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。 Analytics アカウントを作成したら、さらに Data Lake Storage と Azure Storage アカウントへのリンクを追加することができます。 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>Data Lake に Azure Storage へのリンクを含める
Data Lake 環境内で Azure Storage Blob へのリンクを作成できます。 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>Data Lake データ ソースを一覧表示する
次のコードは、Data Lake Store アカウントと、指定した Data Lake Analytics アカウントの (Azure Storage の) Data Lake Storage アカウントを一覧表示します。

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>Data Lake Store アカウントにファイルをアップロードする
次のコードは、DataLakeStoreFileSystemManagementClient を使用してローカル ファイルを Data Lake Store アカウントにアップロードします。

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>Data Lake Store アカウントにファイルを作成する
ファイルをアップロードするだけでなく、分析用に、Data Lake Store アカウントにプログラムで簡単にファイルを作成できます。 次のコードは、100 個のランダムなバイト配列の最初の 4 つの値を .csv ファイルに書き込みます。

        MemoryStream azMem = new MemoryStream();
        StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

        for (int i = 0; i < 100; i++)
        {
            byte[] gA = Guid.NewGuid().ToByteArray();
            string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
            sw.WriteLine(dataLine);
        }
        sw.Flush();
        azMem.Position = 0;

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>Data Lake Store アカウントからファイルをコピーする
次のコードは、DataLakeFileSystemAccountManagementClient オブジェクトを使用したファイル システム操作を示します。 Samples/Data/AmbulanceData ディレクトリからコンピューター上のローカル ディレクトリにスプレッドシート (.csv) ファイルをコピーします。

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>Azure Storage コンテナーを一覧表示する
次のコードは、指定した Azure ストレージ アカウントのコンテナーを一覧表示します。

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Azure ストレージ アカウント パスを確認する
次のコードは、Data Lake Analytics アカウント (analyticsAccountName) 内に Azure ストレージ アカウント (storageAccntName) が存在するかどうかと、その Azure ストレージ アカウント内にコンテナー (containerName) が存在するかどうかを確認します。 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>カタログとジョブを管理する
DataLakeAnalyticsCatalogManagementClient オブジェクトは、各 Azure Data Lake Store に提供された SQL Database を管理するための方法を提供します。 DataLakeAnalyticsJobManagementClient は、U-SQL スクリプトを使用してデータベースで実行されるジョブを送信および管理する方法を提供します。

### <a name="list-databases-and-schemas"></a>データベースとスキーマを一覧表示する
一覧表示できる対象の中で、最も一般的なものはデータベースとスキーマです。 次のコードは、データベースのコレクションを取得し、各データベースのスキーマを列挙します。

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
        }
    }

既定の master データベースで実行すると、この例は次のように出力されます。

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>テーブルの列を一覧表示する
次のコードでは、Data Lake Analytics カタログ管理クライアントを使用してデータベースにアクセスして、指定したテーブルの列を一覧表示する方法を示します。

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>失敗したジョブを一覧表示する
次のコードは、失敗したジョブに関する情報を一覧表示します。

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>U-SQL スクリプトで Azure Storage を参照する
次のコードは、U-SQL スクリプトの先頭です。 このスクリプトは、Data Lake Store アカウント上のファイル "/Samples/Data/SearchLog.tsv" からデータを読み取るように指定しています。

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

リンクされた Azure ストレージ アカウントの BLOB からデータを読み取るには、次の形式で BLOB への完全な URL を使用する必要があります。

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

たとえば、ソース ファイル (SearchLog.tsv) が "contoso_33" ストレージ アカウントの "samples" という名前の BLOB コンテナーに格納されている場合、FROM ステートメントのパスは次のようになります。

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Azure リソース グループと Data Lake Analytics
アプリケーションは通常、Web アプリ、データベース、データベース サーバー、ストレージ、サードパーティのサービスなどの、複数のコンポーネントで構成されます。 Azure Resource Manager を使用すると、アプリケーション内の複数のリソースを&1; つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。 デプロイにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。 詳細については、「 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。 

Data Lake Analtyics サービスには、次のコンポーネントを含めることができます。

* Azure Data Lake Analytics アカウント
* 必要な既定の Azure Data Lake Storage アカウント
* 1 つ以上の Azure Data Lake Analytics アカウント
* 1 つ以上の Azure Data Lake Store アカウント。少なくとも&1; つ必要です。
* 追加のリンクされた Azure Data Lake Storage アカウント
* 追加の Azure Storage アカウント

管理しやすくするために&1; つの Resource Management グループの下にこれらすべてのコンポーネントを作成することができます。

![Azure Data Lake Analytics のアカウントとストレージ](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics アカウントと従属するストレージ アカウントは同じ Azure データ センターに配置する必要があります。
ただし、Resource Management グループは別のデータ センターに配置できます。  

## <a name="see-also"></a>関連項目
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [Azure ポータルを使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
* [Azure ポータルを使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Feb17_HO1-->


