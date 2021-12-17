---
title: クイック スタート:Azure Cosmos DB SQL API アカウントを使用して Python アプリを構築する
description: Azure Cosmos DB SQL API への接続とクエリに使用できる Python コード サンプルについて説明します
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 08/26/2021
ms.author: rosouz
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: 6cdfcff203f9694ab9c9e0b7a693b51070f73aac
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070966"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>クイック スタート:Azure Cosmos DB SQL API アカウントを使用して Python アプリケーションを構築する
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3 コネクタ](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

このクイックスタートでは、Azure portal を使用して、また GitHub からクローンした Python アプリを Visual Studio Code から使用して、Azure Cosmos DB SQL API アカウントの作成と管理を行います。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件

- Cosmos DB アカウント。 オプションは次のとおりです。
    * Azure アクティブ サブスクリプション内で:
        * [Azure 無料アカウントを作成する](https://azure.microsoft.com/free)か、既存のサブスクリプションを使用します 
        * [Visual Studio 月単位クレジット](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB の Free レベル](../optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Azure アクティブ サブスクリプションなしで:
        * 30 日間継続するテスト環境として、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)。
        * [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 または 3.6 以降](https://www.python.org/downloads/) (`python` 実行可能ファイルが `PATH` に設定されていること)。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Visual Studio Code 用の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)。
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="important-update-on-python-2x-support"></a>Python 2.x サポートに関する重要な更新

この SDK の新しいリリースでは、2022 年 1 月 1 日以降は Python 2.x はサポートされません。 詳細については、[変更ログ](./sql-api-sdk-python.md)に関する記事を確認してください。

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>コンテナーの追加

[!INCLUDE [cosmos-db-create-collection](../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>データのクエリ

[!INCLUDE [cosmos-db-create-sql-api-query-data](../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

では、GitHub から SQL API アプリを複製し、接続文字列を設定して実行しましょう。 このクイックスタートでは、[Python SDK](https://pypi.org/project/azure-cosmos/#history) のバージョン 4 を使用します。

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```cmd
    md git-samples
    ```

   bash プロンプトを使用している場合は、代わりに次のコマンドを使用する必要があります。

   ```bash
   mkdir "git-samples"
   ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリをクローンします。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、左側のナビゲーションから **[キー]** を選択します。 次の手順では、画面の右側のコピー ボタンを使用して、**URI** と **プライマリ キー** を *cosmos_get_started.py* ファイルにコピーします。

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Azure portal でキー設定のアクセス キーと URI を取得する":::

2. Visual Studio Code で、 *\git-samples\azure-cosmos-db-python-getting-started* の *cosmos_get_started.py* ファイルを開きます。

3. ポータルから (コピー ボタンを使用して) **URI** 値をコピーし、それを *cosmos_get_started.py* 内の **endpoint** 変数の値にします。 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. 次に、ポータルから **PRIMARY KEY** 値をコピーし、*cosmos_get_started.py* 内の **key** の値に設定します。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

    `key = 'FILLME'`

5. *cosmos_get_started.py* ファイルを保存します。

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コードで作成されたデータベース リソースについて確認するか、または省略して「[接続文字列を更新する](#update-your-connection-string)」に進んでください。

以下のスニペットは、いずれも *cosmos_get_started.py* ファイルからの引用です。

* CosmosClient が初期化されます。 「[接続文字列を更新する](#update-your-connection-string)」セクションの説明に従って "endpoint" と "key" の値を更新します。 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* 新しいデータベースが作成されます。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* 新しいコンテナーが、400 RU/秒の[プロビジョニング スループット](../request-units.md)で作成されます。 [パーティション キー](../partitioning-overview.md#choose-partitionkey)として `lastName` を選択します。こうすることで、このプロパティでフィルター処理を行う効率的なクエリを実行できます。 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* いくつかの項目がコンテナーに追加されます。 コンテナーは、さまざまなスキーマを持つことができる項目 (JSON ドキュメント) のコレクションです。 ヘルパー メソッド ```get_[name]_family_item``` は、JSON ドキュメントとして Azure Cosmos DB に格納されているファミリの表現を返します。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* ポイント読み取り (キー値の検索) は、`read_item` メソッドを使用して実行されます。 各操作の [RU 料金](../request-units.md)が出力されます。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* SQL クエリ構文を使用して、クエリが実行されます。 WHERE 句で ```lastName``` のパーティション キー値を使用しているため、Azure Cosmos DB は、このクエリを適切なパーティションに効率的にルーティングし、パフォーマンスを向上させます。

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>アプリを実行する

1. Visual Studio Code で、 **[表示]**  >  **[コマンド パレット]** の順に選択します。 

2. プロンプトで「**Python: Select Interpreter**」と入力し、使用する Python のバージョンを選択します。

    Visual Studio Code のフッターが、選択されているインタープリターを示すように更新されます。 

3. **[表示]**  >  **[統合ターミナル]** の順に選択して、Visual Studio Code 統合ターミナルを開きます。

4. 統合ターミナル ウィンドウで、*azure-cosmos-db-python-getting-started* フォルダー内にいることを確認します。 そうでない場合は、次のコマンドを実行して、サンプル フォルダーに切り替えます。 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. 次のコマンドを実行して、azure-cosmos パッケージをインストールします。 

    ```python
    pip install --pre azure-cosmos
    ```

    azure-cosmos をインストールしようとしたときにアクセスが拒否されたことを示すエラーが表示される場合は、[管理者として VS Code を実行する](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights)必要があります。

6. 次のコマンドを実行してサンプルを実行し、新しいドキュメントを作成して Azure Cosmos DB に保存します。

    ```python
    python cosmos_get_started.py
    ```

7. 新しい項目が作成および保存されたことを確認するには、Azure portal で **[データ エクスプローラー]**  >  **[AzureSampleFamilyDatabase]**  >  **[Items]\(項目\)** の順に選択します。 作成された項目を表示します。 例として、Andersen ファミリのサンプル JSON ドキュメントを次に示します。
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してコンテナーを作成し、Visual Studio Code で Python アプリを実行する方法を説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。 

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [SQL API 用に Azure Cosmos DB にデータをインポートする](../import-data.md)
