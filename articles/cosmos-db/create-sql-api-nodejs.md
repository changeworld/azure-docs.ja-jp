---
title: クイックスタート - Node.js を使用して Azure Cosmos DB の SQL API アカウントからクエリを実行する
description: Azure Cosmos DB の SQL API アカウントに接続してデータを照会するアプリを Node.js で作成する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: c36f31ef30b6386677c517b1d7e643f9eacea093
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303292"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>クイック スタート:Node.js を使用して Azure Cosmos DB の SQL API アカウントに接続してデータを照会する

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

このクイックスタートでは、Azure portal から Azure Cosmos DB のSQL API アカウントを作成して管理し、また GitHub から複製された Node.js アプリを使用します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能により、ドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。 または、`https://localhost:8081` の URI でキー `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` を使用して [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) を使用することもできます。
- [Node.js 6.0.0+](https://nodejs.org/)。
- [Git](https://www.git-scm.com/downloads).

## <a name="create-a-database"></a>データベースを作成する

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>コンテナーの追加

Azure portal でデータ エクスプローラー ツールを使用してデータベースとコンテナーを作成できるようになりました。 

1. **[データ エクスプローラー]**  >  **[新しいコンテナー]** の順に選択します。 
    
    **[コンテナーの追加]** 領域が右端に表示されます。表示するには、右へスクロールする必要がある場合があります。

    ![Azure portal の [データ エクスプローラー] の [コンテナーの追加] ウィンドウ](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. **[コンテナーの追加]** ページで、新しいコンテナーの設定を入力します。

    |設定|推奨値|説明
    |---|---|---|
    |**[データベース ID]**|処理手順|新しいデータベースの名前として*タスク*を入力します。 データベース名は 1 文字以上 255 文字以内にする必要があります。`/, \\, #, ?` は使えず、末尾にスペースを入れることもできません。 **[Provision database throughput]\(データベース スループットをプロビジョニングする\)** オプションをオンにすると、データベースにプロビジョニングされたスループットをデータベース内のすべてのコンテナーにわたって共有できます。 このオプションは、コストの削減にも役立ちます。 |
    |**スループット**|400|スループットを 400 要求ユニット/秒 (RU/秒) のままにします。 待ち時間を短縮する場合、後でスループットをスケールアップできます。| 
    |**コンテナー ID**|アイテム|新しいコンテナーの名前として「*アイテム*」と入力します。 コンテナー ID には、データベース名と同じ文字要件があります。|
    |**パーティション キー**| /category| この記事で説明するサンプルでは、 */category* をパーティション キーとして使用します。|
    
    上記の設定に加え、必要に応じて、このコンテナー用に**一意なキー**を追加できます。 この例では、このフィールドを空のままにしましょう。 一意なキーを使用すると、開発者はデータベースにデータ整合性のレイヤーを追加できます。 コンテナーの作成中に一意キー ポリシーを作成すると、パーティション キーごとに 1 つ以上の値の一意性が保証されます。 詳細については、記事「[Azure Cosmos DB における一意なキー](unique-keys.md)」を参照してください。
    
    **[OK]** を選択します。 新しいデータベースとコンテナーがデータ エクスプローラーに表示されます。

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>データのクエリ

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ここで、GitHub から Node.js アプリの複製を作成し、接続文字列を設定して実行します。

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内の Azure Cosmos データベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。

以前のバージョンの SQL JavaScript SDK に慣れている方は、"_コレクション_" や "_ドキュメント_" といった用語をよく目にしたかと思います。 Azure Cosmos DB は[複数の API モデル](introduction.md)をサポートしているため、[バージョン 2.0 以上の JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) では、コレクション、グラフ、テーブルを表す用語として "_コンテナー_" が、またコンテナーの内容を表す用語として "_項目_" が一般的に使用されます。

次のスニペットはすべて _app.js_ ファイルからのものです。

- `CosmosClient` オブジェクトが初期化されます。

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- "タスク" データベースを選択します。

  ```javascript
  const database = await client.databases(databaseId);
  ```

- "アイテム" コンテナーまたはコレクションを選択します。

  ```javascript
  const container = await client.databases(containerId);
  ```

- "アイテム" コンテナー内のすべての項目を選択します。

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: results } = await container.items
    .query(querySpec)
    .fetchAll();

  return results;
  ```

- 新しい項目を作成します

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- 項目を更新します

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: itemToUpdate } = await container
    .item(id, category)
    .replace(itemToUpdate);

  return result;
  ```

- 項目を削除する

  ```javascript
  const { resource: result } = await this.container.item(id, category).delete();
  ```

> [!NOTE]
> "更新" メソッドと "削除" メソッドの両方で、`container.item()` を呼び出してデータベースから項目を選択する必要があります。 渡される 2 つのパラメーターは、項目の ID と項目のパーティション キーです。 この場合、パーティション キーは "category" フィールドの値です。

## <a name="update-your-connection-string"></a>接続文字列を更新する

次に、Azure portal に戻って、Azure Cosmos アカウントの接続文字列情報を取得します。 アプリからデータベースに接続できるよう、接続文字列をアプリにコピーします。

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、左のナビゲーションの **[キー]** を選択してから **[読み取り/書き込みキー]** を選択します。 次の手順では、画面の右側にあるコピー ボタンを使用して、URI と主キーを _app.js_ ファイルにコピーします。

   ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-sql-api-dotnet/keys.png)

2. _config.js_ ファイルを開きます。

3. ポータルから (コピー ボタンを使用して) URI 値をコピーし、_config.js_ の endpoint キーの値に設定します。

   `endpoint: "<Your Azure Cosmos account URI>"`

4. 次に、ポータルから [プライマリ キー] 値をコピーし、_config.js_ 内の `config.key` の値に設定します。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>アプリを実行する

1. ターミナルで `npm install` を実行し、必要な npm モジュールをインストールします。

2. ターミナルで `node app.js` を実行し、node アプリケーションを起動します。

これで、データ エクスプローラーに戻って、この新しいデータに変更を加えたり操作したりすることができます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してコンテナーを作成し、Node.js アプリを実行する方法を説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)
