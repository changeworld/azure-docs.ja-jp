---
title: 'エンドユーザー認証: Azure Active Directory を使用した Java と Data Lake Store | Microsoft Docs'
description: Java と Azure Active Directory を使用した Data Lake Store でエンドユーザー認証を行う方法について説明します
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: b638860dbdab7e3b5a747a4ddd82e7247f24845f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-java"></a>Data Lake Store での Java を使用したエンドユーザー認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK の使用](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-end-user-authenticate-python.md)
> * [REST API の使用](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

この記事では、Java SDK を使用して、Azure Data Lake Store に対するエンドユーザー認証を行う方法について説明します。 Java SDK を使用した Data Lake Store に対するサービス間認証については、「[Service-to-service authentication with Data Lake Store using Java](data-lake-store-service-to-service-authenticate-java.md)」(Java を使用した Data Lake Store に対するサービス間認証) を参照してください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "ネイティブ" アプリケーションを作成します**。 [Azure Active Directory を使用した Data Lake Store に対するエンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)のステップを完了している必要があります。

* [Maven](https://maven.apache.org/install.html)。 このチュートリアルでは、ビルドとプロジェクトの依存関係に Maven を使用します。 Maven や Gradle などのビルド システムを使用しなくてもビルドすることはできますが、これらのシステムを使用すると、依存関係の管理が容易になります。

* (オプション) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) や [Eclipse](https://www.eclipse.org/downloads/) などの IDE。

## <a name="end-user-authentication"></a>エンドユーザー認証
1. コマンド ラインで [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) を使用するか、IDE を使用して、Maven プロジェクトを作成します。 IntelliJ を使用して Java プロジェクトを作成する方法については、[こちら](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)をご覧ください。 Eclipse を使用してプロジェクトを作成する方法については、[こちら](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)をご覧ください。

2. Maven の **pom.xml** ファイルに次の依存関係を追加します。 **\</project>** タグの前に次のスニペットを追加します。
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    最初の依存関係では、maven リポジトリから Data Lake Store SDK (`azure-data-lake-store-sdk`) を使用します。 2 番目の依存関係では、このアプリケーションで使用するログ記録フレームワーク (`slf4j-nop`) を指定します。 Data Lake Store SDK では、[slf4j](http://www.slf4j.org/) ログ ファサードを使用します。slf4j を使用すると、log4j、Java ログ、logback などの多数の一般的なログ記録フレームの中から選択することも、ログを記録しないようにすることもできます。 この例ではログを無効にするため、**slf4j-nop** バインドを使用します。 アプリケーションで他のログ オプションを使用する場合は、[こちら](http://www.slf4j.org/manual.html#projectDep)をご覧ください。

3. アプリケーションに次の import ステートメントを追加します。

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Java アプリケーションから次のスニペットを使用して、`DeviceCodeTokenProvider` を使用して以前に作成した Active Directory ネイティブ アプリケーションのトークンを取得します。 **FILL-IN-HERE** を、Azure Active Directory ネイティブ アプリケーションの実際の値に置き換えます。

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Data Lake Store SDK には、Data Lake Store アカウントとの対話に必要なセキュリティ トークンを管理できる便利な方法が用意されています。 ただし、使用する方法はこれらに限定されるわけではありません。 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) や独自のカスタム コードの使用など、トークンを取得するその他の方法も使用できます。

## <a name="next-steps"></a>次の手順
この記事では、Azure Data Lake Store に対し、Java SDK からエンドユーザー認証を使って認証を行う方法について説明しました。 これで、Java SDK を使用して Azure Data Lake Store を使用する方法について説明した次の記事に進めるようになりました。

* [Java SDK を使用した Data Lake Store に対するデータ操作](data-lake-store-get-started-java-sdk.md)


