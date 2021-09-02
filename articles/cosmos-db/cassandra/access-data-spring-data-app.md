---
title: Azure Cosmos DB で Spring Data Apache Cassandra API を使用する方法
description: Azure Cosmos DB で Spring Data Apache Cassandra API を使用する方法を説明します。
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: how-to
ms.date: 07/17/2021
ms.openlocfilehash: 92f9045891aa56589e13d887dfea6c3157292185
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780973"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Azure Cosmos DB で Spring Data Apache Cassandra API を使用する方法
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

この記事では、[Spring Data] を使用して、[Azure Cosmos DB Cassandra API](/azure/cosmos-db/cassandra-introduction) を使って情報を格納および取得するサンプル アプリケーションを作成する方法を説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次の前提条件を満たす必要があります。

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、または[無料の Azure アカウント]にサインアップできます。
* サポートされている Java Development Kit (JDK)。 Azure での開発時に使用可能な JDK の詳細については、「[Java の Azure および Azure Stack に関するサポート](/azure/developer/java/fundamentals/java-support-on-azure)」 を参照してください。
* [Apache Maven](http://maven.apache.org/) バージョン 3.0 以降。
* [Curl](https://curl.haxx.se/) または機能をテストするための類似の HTTP ユーティリティ。
* [Git](https://git-scm.com/downloads) クライアント。

> [!NOTE]
> 次で説明するサンプルでは、Azure Cosmos DB Cassandra API を使用する場合のエクスペリエンスを向上させるためのカスタム拡張機能を実装しています。 これには、カスタムの再試行ポリシーと負荷分散ポリシー、推奨される接続設定の実装が含まれます。 カスタム ポリシーの使用方法の詳細については、[バージョン 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample) および[バージョン 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample-v4) の Java サンプルを参照してください。 

## <a name="create-a-cosmos-db-cassandra-api-account"></a>Cosmos DB Cassandra API アカウントを作成する

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="configure-the-sample-application"></a>サンプル アプリケーションを構成する

次の手順に従って、テスト アプリケーションを構成します。

1. コマンド シェルを開き、次のいずれかの例を複製します。

   Java [バージョン 3 ドライバー](https://github.com/datastax/java-driver/tree/3.x)および対応する Spring バージョンの場合:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v3.git
   ```
   
   Java [バージョン 4 ドライバー](https://github.com/datastax/java-driver/tree/4.x)および対応する Spring バージョンの場合:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v4.git
   ```     

    > [!NOTE]    
    > 以下で説明する使用法は、上記の Java バージョン 3 とバージョン 4 の両方のサンプルで同じですが、カスタムの再試行ポリシーと負荷分散ポリシーを含めるためにそれらを実装する方法は異なります。 既存の Spring Java アプリケーションに変更を加える場合は、コードを確認してカスタム ポリシーを実装する方法について理解することをお勧めします。  

1. サンプル プロジェクトの *resources* ディレクトリ内で *application.properties* ファイルを探すか、まだ存在しない場合はファイルを作成します。

1. テキスト エディターで *application.properties* ファイルを開き、このファイルに次の行を追加するか構成して、サンプルの値を前半の該当する値に置き換えます。

   ```yaml
   spring.data.cassandra.contact-points=<Account Name>.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=<Account Name>
   spring.data.cassandra.password=********
   ```

   各値の説明:

   | パラメーター | 説明 |
   |---|---|
   | `spring.data.cassandra.contact-points` | この記事の前半の **コンタクト ポイント** を指定します。 |
   | `spring.data.cassandra.port` | この記事の前半の **ポート** を指定します。 |
   | `spring.data.cassandra.username` | この記事の前半の **ユーザー名** を指定します。 |
   | `spring.data.cassandra.password` | この記事の前半の **プライマリ パスワード** を指定します。 |

1. *application.properties* ファイルを保存して閉じます。

## <a name="package-and-test-the-sample-application"></a>サンプル アプリケーションをパッケージ化してテストする 

.pom ファイルを含むディレクトリを参照し、アプリケーションをビルドしてテストします。

1. サンプル アプリケーションを Maven でビルドします。次に例を示します。

   ```shell
   mvn clean package
   ```

1. サンプル アプリケーションを開始します。次に例を示します。

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. 次の例のように、コマンド プロンプトから `curl` を使用して新しいレコードを作成します。

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   アプリケーションから次のような値が返されます。

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. 次の例のように、コマンド プロンプトから `curl` を使用して既存のすべてのレコードを取得します。

   ```shell
   curl -s http://localhost:8080/pets
   ```

   アプリケーションから次のような値が返されます。

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

Spring および Azure の詳細については、Azure ドキュメント センターで引き続き Spring に関するドキュメントをご確認ください。

> [!div class="nextstepaction"]
> [Azure の Spring](../../index.yml)

### <a name="additional-resources"></a>その他のリソース

Java での Azure の使用の詳細については、「[Java 開発者向けの Azure]」および「[Azure DevOps と Java の操作]」を参照してください。

<!-- URL List -->

[Java 開発者向けの Azure]: ../index.yml
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps と Java の操作]: /azure/devops/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/access-data-spring-data-app/create-cosmos-db-01.png
[COSMOSDB02]: media/access-data-spring-data-app/create-cosmos-db-02.png
[COSMOSDB03]: media/access-data-spring-data-app/create-cosmos-db-03.png
[COSMOSDB04]: media/access-data-spring-data-app/create-cosmos-db-04.png
[COSMOSDB05]: media/access-data-spring-data-app/create-cosmos-db-05.png
[COSMOSDB05-1]: media/access-data-spring-data-app/create-cosmos-db-05-1.png
[COSMOSDB06]: media/access-data-spring-data-app/create-cosmos-db-06.png