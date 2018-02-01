---
title: ".NET SDK: Azure Data Lake Store に対するアカウント管理操作 | Microsoft Docs"
description: "Azure Data Lake Store .NET SDK を使用して、Data Lake Store に対するアカウント管理操作を実行します"
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
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: cb44fb1cbc279f12f970237f1498a570a63544bd
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>.NET SDK を使用した Azure Data Lake Store に対するアカウント管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

この記事では、.NET SDK を使用して Data Lake Store に対するアカウント管理操作を実行する方法について説明します。 アカウント管理操作には、Data Lake Store アカウントの作成、Azure サブスクリプション内のアカウントの一覧表示、アカウントの削除などがあります。

.NET SDK を使用して Data Lake Store に対するデータ管理操作を実行する方法については、[.NET SDK を使用した Data Lake Store に対するファイルシステム操作](data-lake-store-data-operations-net-sdk.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
* **Visual Studio 2013、2015、2017**。 以下の手順では、Visual Studio 2017 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="create-a-net-application"></a>.NET アプリケーションの作成
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

7. 変数を宣言し、プレースホルダーの値を指定します。 さらに、指定したローカル パスとファイル名がコンピューターに存在していることを確認します。

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

記事の以降のセクションでは、認証、ファイルのアップロードなどの操作を実行する .NET メソッドの使用方法について説明します。

## <a name="authentication"></a>認証

* アプリケーションのエンドユーザー認証については、「[End-user authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するエンドユーザー認証)](data-lake-store-end-user-authenticate-net-sdk.md)」を参照してください。
* アプリケーションのサービス間認証については、「[Service-to-service authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するサービス間認証)](data-lake-store-service-to-service-authenticate-net-sdk.md)」を参照してください。

## <a name="create-client-object"></a>クライアント オブジェクトを作成する
次のスニペットを使用して、アカウントの作成やアカウントの削除など、アカウント管理要求をサービスに発行するために使用される Data Lake Store アカウント クライアント オブジェクトを作成します。

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する
次のスニペットを使用して、Data Lake Store アカウント クライアント オブジェクトの作成中に指定した Azure サブスクリプションに Data Lake Store アカウントを作成します。

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>サブスクリプション内のすべての Data Lake Store アカウントを一覧表示する
次のメソッドをクラス定義に追加します。 次のスニペットは、特定の Azure サブスクリプション内のすべての Data Lake Store アカウントを一覧表示します。

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>Data Lake Store アカウントを削除する
次のスニペットを使用して、以前に作成した Data Lake Store アカウントを削除します。

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>関連項目
* [.NET SDK を使用した Data Lake Store に対するファイルシステム操作](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Store .NET SDK リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>次の手順
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
