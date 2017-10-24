---
title: "エンドユーザー認証: Azure Active Directory を使用した .NET SDK と Data Lake Store | Microsoft Docs"
description: ".NET SDK と Azure Active Directory を使用した Data Lake Store でエンドユーザー認証を行う方法について説明します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: a2a9754b462e39713e6580baab84c064237306a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Data Lake Store での .NET SDK を使用したエンドユーザー認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK の使用](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-end-user-authenticate-python.md)
> * [REST API の使用](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

この記事では、.NET SDK を使用して、Azure Data Lake Store に対するエンドユーザー認証を行う方法について説明します。 .NET SDK を使用した Data Lake Store に対するサービス間認証については、「[Service-to-service authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するサービス間認証)](data-lake-store-service-to-service-authenticate-net-sdk.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
* **Visual Studio 2013、2015、2017**。 以下の手順では、Visual Studio 2017 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "ネイティブ" アプリケーションを作成します**。 [Azure Active Directory を使用した Data Lake Store に対するエンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)のステップを完了している必要があります。

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
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

## <a name="end-user-authentication"></a>エンドユーザー認証
このスニペットを .NET クライアント アプリケーションに追加します。 プレースホルダーの値を、Azure AD のネイティブ アプリケーション (前提条件として一覧表示) から取得した値で置き換えます。 このスニペットを使用すると、Data Lake Store でアプリケーションを**対話的**に認証できます。つまり、Azure 資格情報を入力するように求められます。

使いやすくするために、次のスニペットでは、クライアント ID とリダイレクト URI について、すべての Azure サブスクリプションで有効な既定値を使用しています。 次のスニペットでは、入力する必要があるのはテナント ID の値のみです。 テナント ID は、「[テナント ID を取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)」で説明されている指示に従って取得できます。
    
    private static void Main(string[] args)
    {
        // User login via interactive popup
        // Use the client ID of an existing AAD native application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
        var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
        var nativeClientApp_applicationId = "1950a258-227b-4e31-a9cf-717495945fc2";
        var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_applicationId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
        var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;
    }

上記のスニペットに関して、以下のいくつかの点に留意してください。

* できるだけ短時間でチュートリアルを終了できるよう、このスニペットでは、すべての Azure サブスクリプションで既定で使用できる Azure AD ドメインとクライアント ID を使用しています。 そのため、**このスニペットを実際のアプリケーションで使用するときは、現状のままで使用**してください。
* ただし、独自の Azure AD ドメインとアプリケーション クライアント ID を使う必要がある場合は、Azure AD ネイティブ アプリケーションを作成したうえで、作成したアプリケーションの Azure AD テナント ID、クライアント ID、およびリダイレクト URI を使用する必要があります。 手順については、[Data Lake Store でのエンド ユーザー認証のための Active Directory アプリケーションの作成](data-lake-store-end-user-authenticate-using-active-directory.md)に関するページを参照してください。

  
## <a name="next-steps"></a>次のステップ
この記事では、エンドユーザー認証を使って、.NET SDK を使用して Azure Data Lake Store で認証する方法を説明しました。 これで、.NET SDK を使用して Azure Data Lake Store を使用する方法について説明した次の記事に進めるようになりました。

* [.NET SDK を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-net-sdk.md)
* [.NET SDK を使用した Data Lake Store に対するデータ操作](data-lake-store-data-operations-net-sdk.md)

