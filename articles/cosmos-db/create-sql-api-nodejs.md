---
title: クイック スタート:Node.js を使用して Azure Cosmos DB の SQL API アカウントからクエリを実行する
description: Azure Cosmos DB の SQL API アカウントに接続してデータを照会するアプリを Node.js で作成する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 8df78df27ffb7e8bb8fc88567bd0b3d37be20488
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719502"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>クイック スタート:Node.js を使用して Azure Cosmos DB の SQL API アカウントに接続してデータを照会する

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

このクイックスタートでは、Node.js アプリを使用して Azure Cosmos DB の [SQL API](sql-api-introduction.md) アカウントに接続する方法を紹介します。 その後、Azure Cosmos DB の SQL クエリを使用してデータを照会し、管理することができます。 この記事で作成する Node.js アプリには、[SQL JavaScript SDK](sql-api-sdk-node.md) が使用されています。 このクイック スタートではバージョン 2.0 の [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) を使用します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* さらに:
    * [Node.js](https://nodejs.org/en/) バージョン v6.0.0 以降
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>データベースを作成する 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>コンテナーの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>データのクエリ

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ここで、GitHub から Node.js アプリの複製を作成し、接続文字列を設定して実行します。

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内の Azure Cosmos データベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

以前のバージョンの JavaScript SDK に慣れている方は、"コレクション" や "ドキュメント" といった用語をよく目にしたかと思います。 Azure Cosmos DB は[複数の API モデル](https://docs.microsoft.com/azure/cosmos-db/introduction)をサポートしているため、バージョン 2.0 以上の JavaScript SDK では、コレクション、グラフ、テーブルを表す用語として "コンテナー" が、またコンテナーの内容を表す用語として "項目" が一般的に使用されます。

次のスニペットはすべて **app.js** ファイルからのものです。

* `CosmosClient` オブジェクトが初期化されます。

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* 新しい Azure Cosmos データベースを作成します。

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* データベース内に新しいコンテナー (コレクション) が作成されます。

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* 項目 (ドキュメント) が作成されます。

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* ファミリー データベースの JSON に対する SQL クエリが実行されます。 次のクエリでは、"Anderson" 家のすべての子が返されます。 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>接続文字列を更新する

次に、Azure portal に戻って、Azure Cosmos アカウントの接続文字列情報を取得します。 アプリからデータベースに接続できるよう、接続文字列をアプリにコピーします。

1. [Azure portal](https://portal.azure.com/) で、Azure Cosmos アカウントの左のナビゲーションの **[キー]** をクリックし、 **[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、URI とプライマリ キーを `config.js` ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-sql-api-dotnet/keys.png)

2. `config.js` ファイルを開きます。 

3. ポータルから (コピー ボタンを使用して) URI 値をコピーし、`config.js` の endpoint キーの値に設定します。 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. 次に、ポータルから PRIMARY KEY 値をコピーし、`config.js` 内の `config.key` の値に設定します。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>アプリを実行する

1. ターミナルで `npm install` を実行し、必要な npm モジュールをインストールします。

2. ターミナルで `node app.js` を実行し、node アプリケーションを起動します。

これで、データ エクスプローラーに戻って、この新しいデータに変更を加えたり操作したりすることができます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos アカウントを作成し、データ エクスプローラーを使用してコンテナーを作成し、アプリを実行する方法を説明しました。 これで、Azure Cosmos データベースに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)


