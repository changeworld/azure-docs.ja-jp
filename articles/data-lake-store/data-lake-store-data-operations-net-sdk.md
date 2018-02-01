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
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 2b4c6128d9b50f200a40c8d96faede1e80fbee9e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
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
[GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) で入手できるコード サンプルは、ストアでのファイルの作成、ファイルの連結、ファイルのダウンロード、ストアでのファイルの削除のプロセスを示しています。 このセクションでは、コードの主要部分について説明します。

1. Visual Studio を開き、コンソール アプリケーションを作成します。
2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
3. **[新しいプロジェクト]**で、次の値を入力するか、選択します。

   | プロパティ | 値 |
   | --- | --- |
   | カテゴリ |テンプレート/Visual C#/Windows |
   | テンプレート |コンソール アプリケーション |
   | Name |CreateADLApplication |

4. **[OK]** をクリックしてプロジェクトを作成します。

5. NuGet パッケージをプロジェクトに追加します。

   1. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[NuGet パッケージの管理]**をクリックします。
   2. **[NuGet パッケージ マネージャー]** タブで、**[パッケージ ソース]** が **nuget.org** に設定されており、**[プレリリースを含める]** チェック ボックスがオンになっていることを確認します。
   3. 以下の NuGet パッケージを検索してインストールします。

      * `Microsoft.Azure.DataLake.Store` - このチュートリアルでは、v1.0.0 を使用します。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - このチュートリアルでは、v2.3.1 を使用します。
    
    **NuGet パッケージ マネージャー**を閉じます。

6. **Program.cs**を開き、既存のコードを削除し、次のステートメントに置き換えて、名前空間の参照を追加します。

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. 以下に示すように変数を宣言し、プレースホルダーの値を指定します。 さらに、ここに指定したローカル パスとファイル名がコンピューターに存在していることを確認します。

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net";        
            }
        }

記事の以降のセクションでは、認証、ファイルのアップロードなどの操作を実行する .NET メソッドの使用方法について説明します。

## <a name="authentication"></a>認証

* アプリケーションのエンドユーザー認証については、「[End-user authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するエンドユーザー認証)](data-lake-store-end-user-authenticate-net-sdk.md)」を参照してください。
* アプリケーションのサービス間認証については、「[Service-to-service authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するサービス間認証)](data-lake-store-service-to-service-authenticate-net-sdk.md)」を参照してください。


## <a name="create-client-object"></a>クライアント オブジェクトを作成する
次のスニペットを使用して、サービスに要求を発行するために使用される Data Lake Store ファイルシステム クライアント オブジェクトを作成します。

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>ファイルとディレクトリを作成する
次のスニペットをアプリケーションに追加します。 このスニペットを使用して、ファイルと、存在しない親ディレクトリがあればそのディレクトリを追加します。

    // Create a file - automatically creates any parent directories that don't exist
    
    string fileName = "/Test/testFilename1.txt";
    using (var streamWriter = new StreamWriter(client.CreateFile(fileName, IfExists.Overwrite)))
    {
        streamWriter.WriteLine("This is test data to write");
        streamWriter.WriteLine("This is line 2");
    }

## <a name="append-to-a-file"></a>ファイルに追加する
次のスニペットを使用して、Data Lake Store アカウントの既存のファイルにデータを追加します。

    // Append to existing file
    using (var streamWriter = new StreamWriter(client.GetAppendStream(fileName)))
    {
        streamWriter.WriteLine("This is the added line");
    }

## <a name="read-a-file"></a>ファイルを読み取る
次のスニペットを使用して、Data Lake Store のファイルの内容を読み取ります。

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>ファイルのプロパティを取得する
次のスニペットを使用して、ファイルまたはディレクトリに関連付けられているプロパティを返します。

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

`PrintDirectoryEntry` メソッドの定義は、[GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) でサンプルの一部として入手できます。 

## <a name="rename-a-file"></a>ファイル名を変更する
次のスニペットを使用して、Data Lake Store アカウントの既存のファイルの名前を変更します。

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>ディレクトリを列挙する
次のスニペットを使用して、Data Lake Store アカウントのディレクトリを列挙します

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

`PrintDirectoryEntry` メソッドの定義は、[GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) でサンプルの一部として入手できます。

## <a name="delete-directories-recursively"></a>ディレクトリを再帰的に削除する
次のスニペットを使用して、ディレクトリとそのすべてのサブディレクトリを再帰的に削除します。

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>サンプル
Data Lake Store ファイルシステム SDK の使用方法に関するいくつかのサンプルを次に示します。
* [GitHub の基本サンプル](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [GitHub の上級サンプル](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>関連項目
* [.NET SDK を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>次の手順
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
