---
title: ".NET SDK を使用して Azure Data Lake Store でアプリケーションを開発する | Microsoft Docs"
description: "Azure Data Lake Store .NET SDK を使用して、Data Lake Store アカウントを作成し、Data Lake Store で基本的な操作を実行します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/21/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 8ec76c597dfb59860b456e42a78239c67d289f13


---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>.NET SDK で Data Lake Store の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

[Azure Data Lake Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) を使用して、フォルダーの作成、データ ファイルのアップロードとダウンロードなどの基本操作を実行する方法について説明します。Data Lake の詳細については、[Azure Data Lake Store](data-lake-store-overview.md) に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
* **Visual Studio 2013 または 2015**。 以下の手順では、Visual Studio 2015 を使用します。
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 アカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* **Azure Active Directory アプリケーションを作成する**。 Azure AD アプリケーションを使用して、Azure AD で Data Lake Store アプリケーションを認証します。 Azure AD での認証方法には、**エンドユーザー認証**と**サービス間認証**があります。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="create-a-net-application"></a>.NET アプリケーションの作成
1. Visual Studio を開き、コンソール アプリケーションを作成します。
2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
3. **[新しいプロジェクト]**で、次の値を入力するか、選択します。
   
   | プロパティ | 値 |
   | --- | --- |
   | カテゴリ |テンプレート/Visual C#/Windows |
   | テンプレート |コンソール アプリケーション |
   | 名前 |CreateADLApplication |
4. **[OK]** をクリックしてプロジェクトを作成します。
5. Nuget パッケージをプロジェクトに追加します。
   
   1. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[NuGet パッケージの管理]**をクリックします。
   2. **[Nuget パッケージ マネージャー]** タブで、**[パッケージ ソース]** が **nuget.org** に設定されており、**[プレリリースを含める]** チェック ボックスがオンになっていることを確認します。
   3. 以下の NuGet パッケージを検索してインストールします。
      
      * `Microsoft.Azure.Management.DataLake.Store` - このチュートリアルでは、v0.12.5-preview を使用します。
      * `Microsoft.Azure.Management.DataLake.StoreUploader` - このチュートリアルでは、v0.10.6-preview を使用します。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - このチュートリアルでは、v2.2.8-preview を使用します。
        
        ![Nuget ソースの追加](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "新しい Azure Data Lake アカウントの作成")
   4. **NuGet パッケージ マネージャー**を閉じます。
6. **Program.cs**を開き、既存のコードを削除し、次のステートメントに置き換えて、名前空間の参照を追加します。
   
        using System;
        using System.Threading;
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
7. 以下のように変数を宣言し、既に存在する Data Lake Store 名とリソース グループ名の値を指定します。 また、ここで指定するローカル パスとファイル名は、コンピューターに存在している必要があります。 名前空間の宣言後に次のコード スニペットを追加します。
   
        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
   
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

記事の以降のセクションでは、認証、ファイルのアップロードなどの操作を実行する .NET メソッドの使用方法について説明します。

## <a name="authentication"></a>認証
### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>エンド ユーザー認証を使用している場合 (このチュートリアルではこちらを推奨)
既存の Azure AD "ネイティブ クライアント" アプリケーションと共に、次に記載されているスニペットを使用します。 このチュートリアルは、できるだけ短時間で終了できるよう、このアプローチの使用を推奨しています。

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

このスニペットに関して、以下の&2; 点に留意してください。

* できるだけ短時間でチュートリアルを終了できるよう、このスニペットでは、すべての Azure サブスクリプションから既定で利用できる Azure AD ドメインとクライアント ID を使用しています。 そのため、**このスニペットを実際のアプリケーションで使用するときは、現状のままで使用**してください。
* ただし、独自の Azure AD ドメインとアプリケーション クライアント ID を使う必要がある場合は、Azure AD ネイティブ アプリケーションを作成したうえで、Azure AD ドメイン、クライアント ID、リダイレクト URI を、作成したアプリケーションに使用する必要があります。 手順については、「 [Active Directory アプリケーションを作成する](data-lake-store-end-user-authenticate-using-active-directory.md) 」を参照してください。

> [!NOTE]
> 上記のリンクの手順は、Azure AD Web アプリケーションのためのものです。 しかし、ネイティブ クライアント アプリケーションを作成する場合でも、手順はまったく同じです。 
> 
> 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>クライアント シークレットによるサービス間認証を使用している場合
次のスニペットは、アプリケーション/サービス プリンシパルのクライアント シークレット/キーを使用して、アプリケーションを非対話的に認証するために使用できます。 これは、既存の [Azure AD "Web アプリ" アプリケーション](../azure-resource-manager/resource-group-create-service-principal-portal.md)と共に使用します。

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>証明書によるサービス間認証を使用している場合
3 つ目のオプションとして、次のスニペットは、アプリケーション/サービス プリンシパルの証明書を使用して、アプリケーションを非対話的に認証するためにも使用できます。 これは、既存の [Azure AD "Web アプリ" アプリケーション](../azure-resource-manager/resource-group-create-service-principal-portal.md)と共に使用します。

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>クライアント オブジェクトを作成する
次のスニペットは、Data Lake Store アカウントとファイルシステム クライアント オブジェクトを作成します。これらは、サービスに要求を発行するために使用されます。

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>サブスクリプション内のすべての Data Lake Store アカウントを一覧表示する
次のスニペットは、特定の Azure サブスクリプション内のすべての Data Lake Store アカウントを一覧表示します。

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>ディレクトリを作成する
次のスニペットは、Data Lake Store アカウント内にディレクトリを作成するために使用できる `CreateDirectory` メソッドの例です。

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>ファイルをアップロードする
次のスニペットは、Data Lake Store アカウントにファイルをアップロードするために使用できる `UploadFile` メソッドの例です。

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader` では、ローカル ファイル パスと Data Lake Store ファイル パスの間の再帰的なアップロードとダウンロードがサポートされています。    

## <a name="get-file-or-directory-info"></a>ファイルまたはディレクトリの情報を取得する
次のスニペットは、Data Lake Store で使用できるファイルまたはディレクトリに関する情報を取得するために使用できる `GetItemInfo` メソッドの例です。 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>ファイルまたはディレクトリを一覧表示する
次のスニペットは、Data Lake Store アカウントのファイルとディレクトリを一覧表示するために使用できる `ListItem` メソッドの例です。

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>ファイルを連結する
次のスニペットは、ファイルの連結に使用する `ConcatenateFiles` メソッドの例です。 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>ファイルに追加する
次のスニペットは、Data Lake Store アカウントに既に格納されているファイルに、データを追加するために使用する `AppendToFile` メソッドの例です。

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>ファイルをダウンロードする
次のスニペットは、Data Lake Store アカウントからファイルをダウンロードするために使用する `DownloadFile` メソッドの例です。

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);

        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>次のステップ
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store .NET SDK リファレンス](https://msdn.microsoft.com/library/mt581387.aspx)
* [Data Lake Store REST リファレンス](https://msdn.microsoft.com/library/mt693424.aspx)




<!--HONumber=Jan17_HO4-->


