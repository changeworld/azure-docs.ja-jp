---
title: サービス間認証 - Data Lake Storage Gen2 - Java SDK
description: Java から Azure Active Directory を使用して Azure Data Lake Storage Gen2 に対するサービス間認証を行う方法について説明します
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: normesta
ms.openlocfilehash: 8f36eaef8c84afd1010d6e4ab2714b0d538028b9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494551"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen2-using-java"></a>Azure Data Lake Storage Gen2 に対する Java を使用したサービス間認証

> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

この記事では、Java SDK を使用して、Azure Data Lake Storage Gen2 に対するサービス間認証を行う方法について説明します。 Java SDK を使った Azure Data Lake Storage Gen2 に対するエンド ユーザー認証はサポートされません。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "Web" アプリケーションを作成します**。 [Data Lake Storage Gen2 に対する Azure Active Directory を使用したサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)の手順を完了している必要があります。

* [Maven](https://maven.apache.org/install.html)。 このチュートリアルでは、ビルドとプロジェクトの依存関係に Maven を使用します。 Maven や Gradle などのビルド システムを使用しなくてもビルドすることはできますが、これらのシステムを使用すると、依存関係の管理が容易になります。

* (オプション) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) や [Eclipse](https://www.eclipse.org/downloads/) などの IDE。

## <a name="service-to-service-authentication"></a>サービス間認証

1. コマンド ラインで [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) を使用するか、IDE を使用して、Maven プロジェクトを作成します。 IntelliJ を使用して Java プロジェクトを作成する方法については、[こちら](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)をご覧ください。 Eclipse を使用してプロジェクトを作成する方法については、[こちら](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)をご覧ください。

2. Maven の **pom.xml** ファイルに次の依存関係を追加します。 **\</project>** タグの前に次のスニペットを追加します。

    ```xml
    <dependencies>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-storage-file-datalake</artifactId>
          <version>12.6.0</version>
      </dependency>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-identity</artifactId>
          <version>1.3.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    最初の依存関係は、Maven リポジトリから Data Lake Storage Gen2 SDK (`azure-storage-file-datalake`) を使用することです。 2 番目の依存関係は、このアプリで使用するログ記録フレームワーク (`slf4j-nop`) を指定することです。 Data Lake Storage Gen2 SDK では、[slf4j](https://www.slf4j.org/) ログ ファサードを使用します。それを使用すると、Log4j、Java ログ、logback などの多数の一般的なログ記録フレームの中から選択することも、ログを記録しないようにすることもできます。 この例ではログを無効にするため、**slf4j-nop** バインドを使用します。 アプリで他のログ オプションを使用するには、「[ログに対するプロジェクトの依存関係の宣言](https://www.slf4j.org/manual.html#projectDep)」を参照してください。

3. アプリケーションに次の import ステートメントを追加します。

    ```java
    import com.azure.identity.ClientSecretCredential;
    import com.azure.identity.ClientSecretCredentialBuilder;
    import com.azure.storage.file.datalake.DataLakeDirectoryClient;
    import com.azure.storage.file.datalake.DataLakeFileClient;
    import com.azure.storage.file.datalake.DataLakeServiceClient;
    import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
    import com.azure.storage.file.datalake.DataLakeFileSystemClient;
    import com.azure.storage.file.datalake.models.ListPathsOptions;
    import com.azure.storage.file.datalake.models.PathAccessControl;
    import com.azure.storage.file.datalake.models.PathPermissions;
    ```

4. Java アプリで次のスニペットを使用して、`StorageSharedKeyCredential` のいずれかのクラスを使用して (次の例では、`credential` を使用) 以前に作成した Active Directory Web アプリのトークンを取得します。 トークン プロバイダーは、トークンを取得するために使用した資格情報をメモリにキャッシュし、そのトークンの有効期限が切れそうになった場合に自動的に更新します。 トークンがカスタム コードで取得されるように、`StorageSharedKeyCredential` の独自のサブクラスを作成することもできます。 ここでは、SDK に用意されているものを使ってみましょう。

    **FILL-IN-HERE** を、Azure Active Directory Web アプリケーションの実際の値に置き換えます。

    ```java
    private static String clienttId = "FILL-IN-HERE";
    private static String tenantId = "FILL-IN-HERE";
    private static String clientSecret = "FILL-IN-HERE";
   
    ClientSecretCredential credential = new ClientSecretCredentialBuilder().clientId(clientId).tenantId(tenantId).clientSecret(clientSecret).build();
    ```

Data Lake Storage Gen2 SDK には、Data Lake Storage Gen2 アカウントとの対話に必要なセキュリティ トークンを管理できる便利な方法が用意されています。 ただし、使用する方法はこれらに限定されるわけではありません。 [Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/identity/azure-identity)や独自のカスタム コードの使用など、トークンを取得する他の任意の方法も使用できます。

## <a name="next-steps"></a>次の手順

この記事では、Data Lake Storage Gen2 に対し、Java SDK からエンドユーザー認証を使って認証を行う方法について説明しました。 これで、Java SDK を使用して Data Lake Storage Gen2 を使用する方法について説明した次の記事に進めるようになりました。

* [Java SDK を使用した Data Lake Storage Gen2 に対するデータ操作](data-lake-store-get-started-java-sdk.md)
