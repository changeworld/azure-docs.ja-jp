---
title: ".NET SDK: Azure Data Lake Store に対するファイルシステム操作 | Microsoft Docs"
description: "Azure Data Lake Store .NET SDK を使用して、Data Lake Store に対してフォルダーの作成などのファイルシステム操作を実行します。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 7f6319dcf1ae66a686dd1c2ea2810b3041183098
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>.NET SDK を使用した Azure Data Lake Store に対するファイルシステム操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

この記事では、.NET SDK を使用して Data Lake Store に対するファイルシステム操作を実行する方法について説明します。 ファイルシステム操作には、Data Lake Store アカウントでのフォルダーの作成、ファイルのアップロード、ファイルのダウンロードなどがあります。

.NET SDK を使用して Data Lake Store に対するアカウント管理操作を実行する方法については、[.NET SDK を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-net-sdk.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
* **Visual Studio 2013、2015、2017**。 以下の手順では、Visual Studio 2017 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Data Lake Store アカウント**。 アカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)

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
5. NuGet パッケージをプロジェクトに追加します。

   1. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[NuGet パッケージの管理]**をクリックします。
   2. **[NuGet パッケージ マネージャー]** タブで、**[パッケージ ソース]** が **nuget.org** に設定されており、**[プレリリースを含める]** チェック ボックスがオンになっていることを確認します。
   3. 以下の NuGet パッケージを検索してインストールします。

      * `Microsoft.Azure.Management.DataLake.Store` - このチュートリアルでは、v2.1.3-preview を使用します。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - このチュートリアルでは、v2.2.12 を使用します。

        ![NuGet ソースの追加](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "新しい Azure Data Lake アカウントの作成")
   4. **NuGet パッケージ マネージャー**を閉じます。
6. **Program.cs**を開き、既存のコードを削除し、次のステートメントに置き換えて、名前空間の参照を追加します。

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. 以下に示すように変数を宣言し、プレースホルダーの値を指定します。 さらに、ここに指定したローカル パスとファイル名がコンピューターに存在していることを確認します。

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

記事の以降のセクションでは、認証、ファイルのアップロードなどの操作を実行する .NET メソッドの使用方法について説明します。

## <a name="authentication"></a>認証

* アプリケーションのエンドユーザー認証については、「[End-user authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するエンドユーザー認証)](data-lake-store-end-user-authenticate-net-sdk.md)」を参照してください。
* アプリケーションのサービス間認証については、「[Service-to-service authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するサービス間認証)](data-lake-store-service-to-service-authenticate-net-sdk.md)」を参照してください。


## <a name="create-client-objects"></a>クライアント オブジェクトを作成する
次のスニペットは、Data Lake Store アカウントとファイルシステム クライアント オブジェクトを作成します。これらは、サービスに要求を発行するために使用されます。

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

## <a name="create-a-directory"></a>ディレクトリを作成する
次のメソッドをクラスに追加します。 このスニペットは、Data Lake Store アカウント内にディレクトリを作成するために使用できる `CreateDirectory()` メソッドを示しています。

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

次のスニペットを `Main()` メソッドに追加して、`CreateDirectory()` メソッドを呼び出します。

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>ファイルをアップロードする
次のメソッドをクラスに追加します。 このスニペットは、Data Lake Store アカウントにファイルをアップロードするために使用できる `UploadFile()` メソッドを示しています。 SDK では、ローカル ファイル パスと Data Lake Store ファイル パスの間の再帰的なアップロードとダウンロードがサポートされています。

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

次のスニペットを `Main()` メソッドに追加して、`UploadFile()` メソッドを呼び出します。

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>ファイルまたはディレクトリの情報を取得する
次のスニペットは、Data Lake Store で使用できるファイルまたはディレクトリに関する情報を取得するために使用できる `GetItemInfo()` メソッドの例です。

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

次のスニペットを `Main()` メソッドに追加して、`GetItemInfo()` メソッドを呼び出します。

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>ファイルまたはディレクトリを一覧表示する
次のスニペットは、Data Lake Store アカウントのファイルとディレクトリを一覧表示するために使用できる `ListItems()` メソッドの例です。

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

次のスニペットを `Main()` メソッドに追加して、`ListItems()` メソッドを呼び出します。

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>ファイルを連結する
次のスニペットは、ファイルの連結に使用する `ConcatenateFiles()` メソッドの例です。

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

次のスニペットを `Main()` メソッドに追加して、`ConcatenateFiles()` メソッドを呼び出します。 このスニペットでは、別のファイルが Data Lake Store アカウントにアップロードされていて、そのファイルのパスが文字列 *anotherRemoteFilePath* で指定されていることを前提としています。

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>ファイルに追加する
次のスニペットは、Data Lake Store アカウントに既に格納されているファイルに、データを追加するために使用する `AppendToFile()` メソッドの例です。

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

次のスニペットを `Main()` メソッドに追加して、`AppendToFile()` メソッドを呼び出します。

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>ファイルをダウンロードする
次のスニペットは、Data Lake Store アカウントからファイルをダウンロードするために使用する `DownloadFile()` メソッドの例です。

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

次のスニペットを `Main()` メソッドに追加して、`DownloadFile()` メソッドを呼び出します。

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>関連項目
* [.NET SDK を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>次のステップ
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
