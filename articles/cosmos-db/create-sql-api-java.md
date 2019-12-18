---
title: クイックスタート - Java から Azure Cosmos DB を使用してドキュメント データベースを作成する
description: このクイックスタートでは、Azure Cosmos DB SQL API への接続とクエリに使用できる Java コード サンプルについて説明します
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: a4a8990b3da534acb39ff87c9f7665fb3b08ef06
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708178"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>クイック スタート:Azure Cosmos DB SQL API データを管理する Java アプリを作成する


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

このクイックスタートでは、Java アプリケーションを使用して、Azure Cosmos DB SQL API アカウントからドキュメント データベースを作成して管理する方法について説明します。 まず、Azure portal を使用して Azure Cosmos DB SQL API アカウントを作成し、SQL Java SDK を使用して Java アプリケーションを作成します。さらに Java アプリケーションを使用して Cosmos DB アカウントにリソースを追加します。 このクイックスタートの手順は、Java を実行できる任意のオペレーティング システムで使用できます。 このクイックスタートを完了すると、UI とプログラムのどちらか好きな方法で、Cosmos DB データベースとコンテナーの作成と変更ができるようになります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

加えて次の作業を行います。 

* [Java Development Kit (JDK) バージョン 8](https://aka.ms/azure-jdks)
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* [Maven](https://maven.apache.org/) バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-a-database-account"></a>データベース アカウントの作成

ドキュメント データベースを作成するには、Azure Cosmos DB を含んだ SQL API アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>コンテナーの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>データのクエリ

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次は、コードを使った作業に移りましょう。 GitHub から SQL API アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 それ以外の場合は、「[アプリの実行](#run-the-app)」に進んでください。 

* `CosmosClient` の初期化。 `CosmosClient` は、Azure Cosmos データベース サービスのクライアント側の論理表現を提供します。 このクライアントは、サービスに対する要求の構成と実行に使用されます。
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* CosmosDatabase の作成。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* CosmosContainer の作成。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* `createItem` メソッドを使用した項目の作成。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* `getItem` と `read` メソッドを使用してポイント読み取りを実行します。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* `queryItems` メソッドを使用して JSON に対する SQL クエリを実行します。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>アプリの実行

ここで Azure portal に戻って接続文字列情報を取得し、エンドポイント情報を使用してアプリを起動します。 これでアプリが、ホストされているデータベースと通信できます。


1. Git ターミナル ウィンドウで `cd` を使用して、サンプル コード フォルダーに移動します。

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. git ターミナル ウィンドウで、次のコマンドを実行して 必要な Java パッケージをインストールします。

    ```bash
    mvn package
    ```

3. Git ターミナル ウィンドウで、次のコマンドを使用して Java アプリケーションを起動します (YOUR_COSMOS_DB_HOSTNAME は引用符で囲んだポータルの URI 値に置き換え、YOUR_COSMOS_DB_MASTER_KEY は引用符で囲んだポータルのプライマリ キーに置き換えます)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    ターミナル ウィンドウに、FamilyDB データベースが作成されたという通知が表示されます。 
    
4. `AzureSampleFamilyDB` という名前のデータベースがアプリによって作成されます。
5. `FamilyContainer` という名前のコンテナーがアプリによって作成されます。
6. アプリは、オブジェクト ID とパーティション キーの値 (この例では lastName) を使用してポイント読み取りを実行します。 
7. アプリは項目を照会して、姓が IN 条件 ('Andersen', 'Wakefield', 'Johnson') に該当するすべての家族を取得します。

7. 作成したリソースは、アプリによって削除されません。 ポータルに戻り、[リソースをクリーンアップ](#clean-up-resources)します。  料金が発生しないように、アカウントから削除します。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、データ エクスプローラーを使って Azure Cosmos アカウント、ドキュメント データベース、コンテナーを作成する方法について説明しました。また、同じことをプログラムから行うアプリを実行する方法についても説明しました。 これで、Azure Cosmos コンテナーに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)
