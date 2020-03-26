---
title: 'クイック スタート: Node.js での Table API の使用 - Azure Cosmos DB'
description: このクイックスタートでは、Azure Cosmos DB Table API を使用して Azure Portal と Node.js でアプリケーションを作成する方法を示します
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/06/2019
ms.author: sngun
ms.openlocfilehash: f317b7b5f3ab60f466054f2043027b13e8396abc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "77212824"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>クイックスタート: Node.js と Azure Cosmos DB での Table API アプリの構築

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

このクイックスタートでは、Azure Cosmos DB Table API アカウントを作成し、データ エクスプローラーと GitHub からクローンした Node.js アプリを使用してテーブルとエンティティを作成します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。 または、[ の URI でキー ](https://aka.ms/cosmosdb-emulator) を使用して `https://localhost:8081`Azure Cosmos DB Emulator`C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` を使用することもできます。
- [Node.js 0.10.29 以上](https://nodejs.org/)。
- [Git](https://git-scm.com/downloads)。

## <a name="create-a-database-account"></a>データベース アカウントの作成

> [!IMPORTANT] 
> 新しいテーブル API アカウントを作成して一般公開のテーブル API SDK を操作する必要があります。 プレビュー期間中に作成されたテーブル API アカウントは、一般公開の SDK ではサポートされません。
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>テーブルの追加

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

GitHub で Table アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

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
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 これでアプリが、ホストされているデータベースと通信できます。 

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、 **[接続文字列]** を選択します。 

    ![[接続文字列] ウィンドウに表示されている必要な接続文字列情報をコピーします。](./media/create-table-nodejs/connection-string.png)

2. 右側にあるコピー ボタンを使って、プライマリ接続文字列をコピーします。

3. *app.config* ファイルを開き、3 行目の connectionString に値を貼り付けます。 

    > [!IMPORTANT]
    > エンドポイントで documents.azure.com を使用している場合は、プレビュー アカウントを持っていることになるため、[新しいテーブル API](#create-a-database-account) アカウントを作成して、一般公開のテーブル API SDK を操作する必要があります。
    >

3. *app.config* ファイルを保存します。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="run-the-app"></a>アプリを実行する

1. git ターミナル ウィンドウで、storage-table-java-getting-started フォルダーに `cd` で移動します。

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. 次のコマンドを実行して、[azure]、[node-uuid]、[nconf]、[async] の各モジュールをローカルにインストールし、これらのモジュールのエントリを *package.json* ファイルに保存します。

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. git ターミナル ウィンドウで、次のコマンドを実行して Node.js アプリケーションを実行します。

    ```
    node ./tableSample.js 
    ```

    コンソール ウィンドウに、Azure Cosmos DB の新しいテーブル データベースに追加されるテーブル データが表示されます。

    これで、データ エクスプローラーに戻って、この新しいデータの表示、クエリ、変更、操作を行うことができます。 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してテーブルを作成し、Node.js アプリを実行してテーブル データを追加する方法を説明しました。  これで、Table API を使用して、データをクエリできます。  

> [!div class="nextstepaction"]
> [テーブル データを Table API にインポートする](table-import.md)
